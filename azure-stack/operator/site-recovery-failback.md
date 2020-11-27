---
title: Benutzerhandbuch für das Azure Site Recovery-Failbacktool
description: Erfahren Sie, wie Sie das Azure Site Recovery-Failbacktool zum Schützen von VMs verwenden.
author: sethmanheim
ms.author: sethm
ms.date: 11/19/2020
ms.topic: how-to
ms.reviewer: rtiberiu
ms.lastreviewed: 11/19/2020
ms.openlocfilehash: 0cb3bccab11d337a8a8804578233edb95ac02dc6
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95517224"
---
# <a name="azure-site-recovery-failback-tool"></a>Azure Site Recovery-Failbacktool

In einer verbundenen Umgebung können Sie Azure Site Recovery zum Schutz von VMs verwenden, die auf Azure Stack Hub ausgeführt werden. In [diesem Artikel](/azure/site-recovery/azure-stack-site-recovery) wird beschrieben, wie Sie die Umgebung einrichten und wie Site Recovery zur allgemeinen Business Continuity & Disaster Recovery-Strategie für diese Workloads beiträgt.

Bei einem Ausfall führt der Azure Stack Hub-Operator ein *Failover* aus. Sobald Azure Stack Hub wieder ausgeführt wird, wird ein *Failbackverfahren* durchlaufen. Das Failoververfahren wird in [diesem Site Recovery-Artikel](/azure/site-recovery/azure-stack-site-recovery) beschrieben, das Failbackverfahren erfordert allerdings einige manuelle Schritte:

1. Beenden der in Azure ausgeführten VM
2. Herunterladen der VHDs
3. Hochladen der VHDs auf Azure Stack Hub
4. Neuerstellen der VMs
5. Starten der auf Azure Stack Hub ausgeführten VM 

Da dieser Prozess fehleranfällig und zeitaufwendig sein kann, haben wir Skripts erstellt, die beim Beschleunigen und Automatisieren helfen.

> [!Note]  
> Das Azure Site Recovery-Tool erfordert die Az-Module von Azure Stack Hub. Wenn Sie die AzureRM-Module von Azure Stack Hub ausführen, müssen Sie ein Upgrade Ihrer Arbeitsstation ausführen oder das Azure Site Recovery-Failbacktool in einer isolierten Umgebung mit den Az-Modulen verwenden. Weitere Informationen finden Sie unter [Installieren des Az-Moduls von PowerShell für Azure Stack Hub](powershell-install-az-module.md).

## <a name="failback-procedure"></a>Failbackverfahren

Das automatisierte Failbackverfahren besteht aus drei Hauptteilen:

- **Copy-AzSiteRecoveryVmVHD**:
  - Die Azure-VM wird heruntergefahren.
  - Der Datenträgerexport wird vorbereitet.
  - Der Datenträger wird über „AzCopy“ oder „StorageBlobCopy“ kopiert.
  - Der Datenträger wird in ein Azure Stack Hub-Speicherkonto hochgeladen.

- Nachdem der Datenträger kopiert wurde, sind zwei Szenarien möglich. Be beiden wird **Prepare-AzSiteRecoveryVMFailBack** verwendet:
  - Die ursprüngliche Azure Stack Hub-Instanz wurde wiederhergestellt. Die ursprüngliche VM ist noch vorhanden, und Sie müssen nur die VHDs ändern.
  - Wenn die ursprünglichen VMs durch einen schwerwiegenden Vorfall verloren gegangen sind, müssen Sie die VMs vollständig neu erstellen.

  Dieses Verfahren deckt beide Szenarien ab, da sowohl die Vorlage als auch die erforderliche Parameterdatei erstellt werden.

- Die eigentliche Bereitstellung der Azure Resource Manager-Vorlage mithilfe der Parameterdatei, sowie die Bereitstellung/Erstellung der VM auf Azure Stack Hub.

### <a name="prerequisites"></a>Voraussetzungen

Das Failbackverfahren setzt voraus, dass folgende Schritte ausgeführt werden:

- Kopieren Sie das [Azure Site Recovery-Failbacktool](https://aka.ms/azshasr).

- Importieren Sie das Modul „FailbackTool.psm1“ in PowerShell.

- Führen Sie das Verfahren aus, das in [diesem Artikel zum Installieren des Az-Moduls für Azure Stack Hub](powershell-install-az-module.md) beschrieben wird.

- (optional) [Laden Sie AzCopy, Version 10](/azure/storage/common/storage-use-azcopy-v10) herunter.

  - Das Kopieren des Blobs mithilfe von **AzCopy** ist zwar schneller, erfordert aber zusätzlichen Speicherplatz auf dem Datenträger zum temporären Speichern der Blobdatei.
  - Wenn **AzCopy** nicht verwendet wird, dient **AzStorageBlobCopy** zum Erstellen der VHD-Kopie. Das bedeutet, dass kein lokaler Speicher erforderlich ist, der Prozess aber länger dauert.

- Zugriff auf die Ressourcen im Azure-Portal sowie Zugriff zum Erstellen dieser Ressourcen auf Azure Stack Hub.

## <a name="step-1-copy-blob-from-azure-to-azure-stack-hub"></a>Schritt 1: Kopieren des Blobs aus Azure nach Azure Stack Hub

Rufen Sie das PowerShell-Cmdlet **Copy-AzSiteRecoveryVmVHD** auf, um die Azure-VM zu beenden und die VHDs von Azure herunter- und auf Azure Stack Hub hochzuladen. Beispiel:

```powershell
$uris = Copy-AzSiteRecoveryVmVHD `
        -SourceVM $vmOnAzure `
        -TargetStorageAccountName "targetaccountName" `
        -TargetStorageEndpoint "redmond.ext-v.masd.stbtest.microsoft.com" `
        -TargetStorageAccountKey $accountKey `
        -AzCopyPath "C:\azcopy_v10\azcopy.exe" `
        -VhdLocalFolder "C:\tempfolder"
```

Beachten Sie die folgenden Überlegungen:

- Dieses Beispiel verwendet `$uris`, um den in Schritt 2 verwendeten Wert `SourceDiskVhdUris` zu speichern.

- Der Parameter `-SourceVM` ist ein VM-Objekt, das von `Get-AzVM` abgerufen wird.
  - Dies ist die geschützte VM von Azure Stack Hub, für die ein Failover in Azure ausgeführt wurde.
  - Es spielt keine Rolle, ob die VM ausgeführt wird, da das Skript die VM herunterfährt. Es wird jedoch empfohlen, die Dienste auf der VM explizit herunterzufahren und zu beenden.

- Sie können entweder (mit `TargetStorageAccountKey`) einen Kontoschlüssel oder (mit `TargetStorageAccountSasToken`) das SAS-Token des Speicherkontos auf Azure Stack Hub angeben. Das SAS-Token muss auf Speicherkontoebene erstellt werden und mindestens die folgenden Berechtigungen besitzen:

   :::image type="content" source="media/site-recovery-failback/sasperms.png" alt-text="Berechtigungen des SAS-Tokens":::

- Sie können den Speicherendpunkt angeben, der die Region und den vollqualifizierten Domänenname umfasst, z. B. `regionname.azurestack.microsoft.com`. Sie können auch den Umgebungsnamen der Azure Stack Hub-Instanz angeben, wie etwa `AzureStackTenant`. Wenn der Umgebungsname verwendet wird, sollte er mithilfe von **Get-AzEnvironment** aufgelistet werden können.

- Sie können **AzCopy** oder **AzStorageBlobCopy** verwenden, um die VHD von Azure nach Azure Stack Hub zu kopieren. **AzCopy** ist schneller, muss die VHD-Dateien aber zunächst in einen lokalen Ordner herunterladen:
  - Wenn Sie **AzCopy** verwenden, geben Sie die Parameter `-AzCopyPath` und `-VhdLocalFolder` an (den Pfad, in den die VHDs kopiert werden).
  - Wenn im lokalen System nicht genügend Speicherplatz vorhanden ist, können Sie die VHDs auch ohne **AzCopy** direkt kopieren, indem Sie die Parameter `-AzCopyPath` und `-VhdLocalFolder` weglassen. Standardmäßig verwendet dieser Befehl **AzStorageBlobCopy**, um Dateien direkt in das Azure Stack Hub-Speicherkonto zu kopieren.

## <a name="step-2-generate-resource-manager-templates"></a>Schritt 2: Generieren von Azure Resource Manager-Vorlagen

Nachdem der Datenträger kopiert wurde, verwenden Sie das Cmdlet **Prepare-AzSiteRecoveryVMFailBack**, um die `$templateFile` und die `$parameterFile` zu erstellen, die zum Bereitstellen der VM auf Azure Stack Hub erforderlich sind:

```powershell
$templateFile, $parameterFile = Prepare-AzSiteRecoveryVMFailBack `
                                -SourceContextName "PublicAzure" `
                                -SourceVM $vmOnAzure `
                                -SourceDiskVhdUris $uris `
                                -TargetResourceLocation "redmond" `
                                -ArmTemplateDestinationPath "C:\ARMtemplates" `
                                -TargetVM $vmOnHub `
                                -TargetContextName "AzureStack"

```

Beachten Sie die folgenden Überlegungen:

- Dieses Beispiel verwendet `-SourceDiskVhdUris` als Rückgabewert aus Schritt 1 (mit `$uris`).

- Dieses Cmdlet unterstützt zwei Szenarien:
  - Wenn Sie `-TargetVM` angeben, setzen Sie voraus, dass die VM auf Azure Stack Hub aktiv ist, und möchten die Datenträger durch die neuesten Versionen ersetzen, die von Azure kopiert wurden.
  - Das Skript generiert eine Resource Manager-Vorlage zum Bereitstellen dieser VM und löscht die vorhandene VM aus Azure Stack Hub.
  
  > [!NOTE]
  > Durch das Löschen der Azure Stack Hub-VM werden keine anderen Objekte entfernt (wie z. B. das VNET, die Ressourcengruppe oder Netzwerksicherheitsgruppen). Es wird nur die VM-Ressource selbst entfernt. Dann wird die Vorlage mit dem Parameter `-incremental` bereitgestellt.

  - Wenn Sie den Parameter `-TargetVM` nicht angeben, geht das Skript davon aus, dass die VM auf Azure Stack Hub nicht mehr vorhanden ist. Das Skript erstellt eine Resource Manager-Vorlage, um eine vollständig neue VM bereitzustellen.

- Die generierten Resource Manager-Vorlagendateien werden unter `-ArmTemplateDestinationPath` abgelegt, und der vollständige Pfad der Vorlagen- oder Parameterdatei wird zurückgegeben.

- Wenn der Parameter `-TargetVM` angegeben wird, löscht das Cmdlet die VM, sodass Sie mit den folgenden Schritten fortfahren können.

## <a name="step-3-deploy-the-resource-manager-template"></a>Schritt 3: Bereitstellen der Resource Manager-Vorlage

An diesem Punkt wird die VHD auf Azure Stack Hub hochgeladen, und die Resource Manager-Vorlage und die entsprechenden Parameterdateien werden erstellt. Nun muss nur noch die VM auf Azure Stack Hub bereitgestellt werden.

In einigen Szenarien werden Sie diese Vorlage vermutlich bearbeiten und Namen oder Ressourcen hinzufügen, entfernen oder ändern wollen. Dies ist zulässig: Sie können die Vorlage ganz nach Bedarf bearbeiten und anpassen.

Wenn Sie fertig sind und die Ressourcen wie gewünscht in der Resource Manager-Vorlage festgelegt wurden, rufen Sie das Cmdlet **New-AzResourceGroupDeployment** auf, um die Ressourcen bereitzustellen. Beispiel:

```powershell
New-AzResourceGroupDeployment `
  -Name "Failback" `
  -ResourceGroupName "failbackrg" `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile `
  -Mode Incremental
```

Beachten Sie die folgenden Überlegungen:

- Der Parameter `-ResourceGroupName` sollte eine vorhandene Ressourcengruppe bezeichnen.
- Die Parameter `-TemplateFile` und `-TemplateParameterFile` stammen aus den Rückgabewerten in Schritt 2.

## <a name="next-steps"></a>Nächste Schritte

- [Features von Azure Stack Hub-VMs](../user/azure-stack-vm-considerations.md)
- [Hinzufügen eines benutzerdefinierten VM-Images zu Azure Stack Hub und dessen Entfernen](azure-stack-add-vm-image.md)
- [Schnellstart: Erstellen eines virtuellen Windows Server-Computers in Azure Stack Hub mit PowerShell](../user/azure-stack-quick-create-vm-windows-powershell.md)