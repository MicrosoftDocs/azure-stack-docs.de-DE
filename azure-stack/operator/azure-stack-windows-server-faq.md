---
title: Häufig gestellte Fragen zu Azure Stack Hub für Windows Server
description: Liste von häufig gestellten Fragen zum Azure Stack Hub-Marketplace für Windows Server
author: sethmanheim
ms.topic: article
ms.date: 12/27/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 08/29/2019
ms.openlocfilehash: 5a0c02737610a663448286b16afbf1504102a8a6
ms.sourcegitcommit: 959513ec9cbf9d41e757d6ab706939415bd10c38
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2020
ms.locfileid: "76890135"
---
# <a name="windows-server-in-azure-stack-hub-marketplace-faq"></a>Häufig gestellte Fragen zu Windows Server im Azure Stack Hub-Marketplace

In diesem Artikel werden einige häufig gestellte Fragen zu Windows Server-Images im [Azure Stack Hub-Marketplace](azure-stack-marketplace.md) beantwortet.

## <a name="marketplace-items"></a>Marketplace-Elemente

### <a name="how-do-i-update-to-a-newer-windows-image"></a>Wie führe ich eine Aktualisierung auf ein neueres Windows-Image durch?

Finden Sie zunächst heraus, ob Azure Resource Manager-Vorlagen auf bestimmte Versionen verweisen. Wenn dies der Fall ist, aktualisieren Sie diese Vorlagen, oder behalten Sie die älteren Imageversionen bei. Empfohlen wird, die **Version mit der Bezeichnung „latest“** zu verwenden.

Falls VM-Skalierungsgruppen auf eine bestimmte Version verweisen, sollten Sie dann überlegen, ob diese später skaliert werden, und entscheiden, ob Sie ältere Versionen beibehalten möchten. Löschen Sie vor dem Herunterladen der neueren Images die älteren Images im Marketplace, wenn keine dieser Bedingungen zutrifft. Verwenden Sie zum Löschen die Marketplace-Verwaltung, falls das ursprüngliche Image auf diese Weise heruntergeladen wurde. Laden Sie dann die neuere Version herunter.

### <a name="what-are-the-licensing-options-for-windows-server-marketplace-images-on-azure-stack-hub"></a>Welche Lizenzierungsoptionen sind für Windows Server-Marketplace-Images in Azure Stack Hub verfügbar?

Microsoft bietet über den Azure Stack Hub-Marketplace zwei Versionen von Windows Server-Images an. In einer Azure Stack Hub-Umgebung kann nur eine Imageversion verwendet werden.  

- **Nutzungsbasierte Bezahlung**: Diese Images führen die Windows-Verbrauchseinheiten zum vollen Preis aus.
   Zielgruppe: Enterprise Agreement-Kunden (EA), die das *Nutzungsabrechnungsmodell* verwenden, sowie CSPs, die nicht die SPLA-Lizenzierung verwenden möchten.
- **BYOL (Bring-Your-Own-License, Verwendung Ihrer eigenen Lizenz)** : Diese Images führen grundlegende Verbrauchseinheiten aus.
   Zielgruppe: EA-Kunden mit einer Windows Server-Lizenz, und CSPs, die die SPLA-Lizenzierung verwenden.

Der Azure-Vorteil bei Hybridnutzung (Azure Hybrid Use Benefit, AHUB) wird in Azure Stack Hub nicht unterstützt. Kunden, die ihre Lizenz über das „Kapazitätsmodell“ bezogen haben, müssen das BYOL-Image verwenden. Wenn Sie das Azure Stack Development Kit (ASDK) testen, können Sie eine dieser Optionen verwenden.

### <a name="what-if-i-downloaded-the-wrong-version-to-offer-my-tenantsusers"></a>Was geschieht, wenn ich die falsche Version für meine Mandanten/Benutzer heruntergeladen habe?

Löschen Sie zuerst die falsche Version über die Marketplace-Verwaltung. Warten Sie, bis der Vorgang abgeschlossen ist (achten Sie auf die Benachrichtigungen über den Abschluss, nicht auf das Blatt **Marketplace-Verwaltung**). Laden Sie dann die richtige Version herunter.

Wenn Sie beide Versionen des Images herunterladen, ist nur die neueste Version für Endkunden im Marketplace-Katalog sichtbar.

### <a name="what-if-my-user-incorrectly-checked-the-i-have-a-license-box-in-previous-windows-builds-and-they-dont-have-a-license"></a>Was geschieht, wenn mein Benutzer fälschlicherweise das Kontrollkästchen „Ich besitze eine Lizenz“ in den vorherigen Windows-Builds aktiviert hat, obwohl er keine Lizenz besitzt?

Durch Ausführen des folgenden Skripts können Sie das Lizenzmodellattribut ändern, um vom BYOL-Modell (Bring Your Own License) zum Modell mit nutzungsbasierter Bezahlung (Pay As You Go) zu wechseln:

```powershell
$vm= Get-Azurermvm -ResourceGroup "<your RG>" -Name "<your VM>"
$vm.LicenseType = "None"
Update-AzureRmVM -ResourceGroupName "<your RG>" -VM $vm
```

Sie können den Lizenztyp Ihrer VM überprüfen, indem Sie die folgenden Befehle ausführen. Wenn das Lizenzmodell den Eintrag **Windows_Server** enthält, wird Ihnen der BYOL-Preis berechnet. Andernfalls werden Ihnen die Windows-Verbrauchseinheiten über das Modell „Nutzungsbasierte Zahlung“ berechnet:

```powershell
$vm | ft Name, VmId,LicenseType,ProvisioningState
```

### <a name="what-if-i-have-an-older-image-and-my-user-forgot-to-check-the-i-have-a-license-box-or-we-use-our-own-images-and-we-do-have-enterprise-agreement-entitlement"></a>Was geschieht, wenn wir ein älteres Image besitzen und mein Benutzer vergessen hat, das Kontrollkästchen „Ich besitze eine Lizenz“ zu aktivieren, oder wir unsere eigenen Images verwenden und über Enterprise Agreement-Berechtigung verfügen?

Durch Ausführen der folgenden Befehle können Sie das Lizenzmodellattribut ändern, um auf das BYOL-Modell (Bring Your Own License) umzustellen:

```powershell
$vm= Get-Azurermvm -ResourceGroup "<your RG>" -Name "<your VM>"
$vm.LicenseType = "Windows_Server"
Update-AzureRmVM -ResourceGroupName "<your RG>" -VM $vm
```

### <a name="what-about-other-vms-that-use-windows-server-such-as-sql-or-machine-learning-server"></a>Was passiert mit anderen VMs, die Windows Server verwenden, z.B. SQL Server oder Machine Learning Server?

Diese Images wenden den **licenseType**-Parameter an, weshalb das nutzungsbasierte Modell angewendet wird. Sie können diesen Parameter festlegen (siehe hierzu die Antwort zur vorherigen häufig gestellten Frage). Dies gilt nur für die Windows Server-Software, nicht für sich überlagernde Produkte wie SQL, für das Bring Your Own License erforderlich ist. Die benutzerdefinierte Lizenzierung gilt nicht für sich überlagernde Softwareprodukte.

Beachten Sie, dass Sie die **licenseType**-Eigenschaft für SQL Server-Images aus dem Marketplace nur ändern können, wenn die Version XX.X.20190410 oder höher lautet. Wenn Sie eine ältere Version der SQL Server-Images aus dem Marketplace ausführen, können Sie das **licenseType**-Attribut nicht ändern, und Sie müssen die Bereitstellung mit den neuesten SQL Server-Images aus dem Marketplace erneut ausführen.

### <a name="i-have-an-enterprise-agreement-ea-and-will-be-using-my-ea-windows-server-license-how-do-i-make-sure-images-are-billed-correctly"></a>Ich besitze ein Enterprise Agreement (EA) und verwende meine Windows Server-EA-Lizenz. Wie stelle ich sicher, dass die Images ordnungsgemäß abgerechnet werden?

Sie können **licenseType: Windows_Server** in einer Azure Resource Manager-Vorlage hinzufügen. Diese Einstellung muss jedem Ressourcenblock eines virtuellen Computers hinzugefügt werden.

## <a name="activation"></a>Aktivierung

Um einen virtuellen Windows Server-Computer in Azure Stack Hub zu aktivieren, müssen die folgenden Bedingungen erfüllt sein:

- Der OEM hat den entsprechenden BIOS-Marker in jedem Hostsystem in Azure Stack Hub festgelegt.
- Windows Server 2012 R2 und Windows Server 2016 müssen die [automatische Aktivierung virtueller Computer](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) verwenden. Der Schlüsselverwaltungsdienst (Key Management Service, KMS) und andere Aktivierungsdienste werden in Azure Stack Hub nicht unterstützt.

### <a name="how-can-i-verify-that-my-virtual-machine-is-activated"></a>Wie kann ich sicherstellen, dass mein virtueller Computer aktiviert ist?

Führen Sie den folgenden Befehl in einer Eingabeaufforderung mit erhöhten Rechten aus:

```shell
slmgr /dlv
```

Wenn er richtig aktiviert ist, wird dies klar und deutlich mit dem Hostnamen in der `slmgr`-Ausgabe angezeigt. Verlassen Sie sich nicht auf Wasserzeichen in der Anzeige, da sie möglicherweise nicht auf dem neuesten Stand sind oder von einem anderen virtuellen Computer angezeigt werden, der sich hinter dem Ihren befindet.

### <a name="my-vm-is-not-set-up-to-use-avma-how-can-i-fix-it"></a>Meine VM ist nicht für die Verwendung von AVMA konfiguriert. Wie kann ich dies beheben?

Führen Sie den folgenden Befehl in einer Eingabeaufforderung mit erhöhten Rechten aus:

```shell
slmgr /ipk <AVMA key>
```

Die für das Image zu verwendenden Schlüssel werden im Artikel [Automatische Aktivierung virtueller Computer](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) aufgeführt.

### <a name="i-create-my-own-windows-server-images-how-can-i-make-sure-they-use-avma"></a>Ich erstelle meine eigenen Windows Server-Images. Wie kann ich sicherstellen, dass diese AVMA verwenden?

Es wird empfohlen, die Befehlszeile `slmgr /ipk` mit dem entsprechenden Schlüssel auszuführen, bevor Sie den Befehl `sysprep` ausführen. Alternativ können Sie den AVMA-Schlüssel in einer Setupdatei namens „Unattend.exe“ einschließen.

### <a name="i-am-trying-to-use-my-windows-server-2016-image-created-on-azure-and-it-is-not-activating-or-using-kms-activation"></a>Ich möchte mein in Azure erstelltes Windows Server 2016-Image verwenden, doch es kann nicht aktiviert werden oder die KMS-Aktivierung funktioniert nicht.

Führen Sie den Befehl `slmgr /ipk` aus. Für Azure-Images wird das Fallback auf AVMA möglicherweise nicht richtig ausgeführt, aber wenn eine Verbindung mit dem Azure KMS-System hergestellt werden kann, werden sie aktiviert. Es wird empfohlen, sicherzustellen, dass diese VMs für die Verwendung von AVMA konfiguriert sind.

### <a name="i-have-performed-all-of-these-steps-but-my-virtual-machines-are-still-not-activating"></a>Ich habe all diese Schritte ausgeführt, aber meine virtuellen Computer sind noch nicht aktiviert.

Wenden Sie sich an Ihren Hardwareanbieter, um sicherzustellen, dass die richtigen BIOS-Marker installiert wurden.

### <a name="what-about-earlier-versions-of-windows-server"></a>Was ist mit früheren Versionen von Windows Server?

[Automatische Aktivierung virtueller Computer](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) wird nicht älteren Versionen von Windows Server unterstützt. Sie müssen die virtuellen Computer manuell aktivieren.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Der Azure Stack Hub-Marketplace – Übersicht](azure-stack-marketplace.md)
- [Herunterladen von Marketplace-Elementen von Azure in Azure Stack Hub](azure-stack-download-azure-marketplace-item.md)
