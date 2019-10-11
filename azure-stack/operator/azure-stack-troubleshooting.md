---
title: Problembehandlung für Microsoft Azure Stack | Microsoft-Dokumentation
description: Problembehandlung für Azure Stack
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 09/30/2019
ms.openlocfilehash: 0fb46cd1b92c1b811ba1c72a91188201a7d2af96
ms.sourcegitcommit: 79ead51be63c372b23b7fca6ffeaf95fd44de786
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/30/2019
ms.locfileid: "71687968"
---
# <a name="microsoft-azure-stack-troubleshooting"></a>Problembehandlung für Microsoft Azure Stack

Dieses Dokument enthält Informationen zur Problembehandlung für integrierte Azure Stack-Umgebungen. Hilfe zum Azure Stack Development Kit erhalten Sie unter [Problembehandlung beim ASDK](../asdk/asdk-troubleshooting.md) oder von den Experten im [MSDN-Forum für Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

Diese Abschnitte enthalten Links zu Dokumentationen, in denen Sie Antworten auf häufige Fragen finden, die an den Microsoft-Kundensupport gesendet werden.

### <a name="purchase-considerations"></a>Überlegungen zum Kauf

* [Informationen zum Erwerb](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)
* [Übersicht über Azure Stack](azure-stack-overview.md)

### <a name="updates-and-diagnostics"></a>Updates und Diagnose

* [Verwenden von Diagnosetools in Azure Stack](azure-stack-diagnostics.md)
* [Überprüfen des Azure Stack-Systemstatus](azure-stack-diagnostic-test.md)
* [Veröffentlichungsintervall für Updatepakete](azure-stack-servicing-policy.md#update-package-release-cadence)

### <a name="supported-operating-systems-and-sizes-for-guest-vms"></a>Unterstützte Betriebssysteme und Größen für Gast-VMS

* [Für Azure Stack unterstützte Gastbetriebssysteme](azure-stack-supported-os.md)
* [In Azure Stack unterstützte VM-Größen](../user/azure-stack-vm-sizes.md)

### <a name="azure-marketplace"></a>Azure Marketplace

* [Für Azure Stack verfügbare Azure Marketplace-Elemente](azure-stack-marketplace-azure-items.md)

### <a name="manage-capacity"></a>Verwalten der Kapazität

#### <a name="memory"></a>Arbeitsspeicher

Sie können zusätzlichen Speicher hinzufügen, um die verfügbare Gesamtspeicherkapazität für Azure Stack zu erhöhen. In Azure Stack wird Ihr physischer Server auch als Skalierungseinheitknoten bezeichnet. Alle Skalierungseinheitknoten, die Mitglieder einer einzelnen Skalierungseinheit sind, müssen über die [gleiche Speichermenge](azure-stack-manage-storage-physical-memory-capacity.md) verfügen.

#### <a name="retention-period"></a>Aufbewahrungszeitraum

Die Einstellung für den Aufbewahrungszeitraum ermöglicht einem Cloudbetreiber das Festlegen eines Zeitraums in Tagen (zwischen 0 und 9999 Tage), in dem gelöschte Konten ggf. wiederhergestellt werden können. Der Standardaufbewahrungszeitraum ist auf **0** Tage festgelegt. Mit dem Wert **0** wird festgelegt, dass alle gelöschten Konten sofort aus der Aufbewahrung entfernt und für die regelmäßige automatische Speicherbereinigung markiert werden.

* [Festlegen des Aufbewahrungszeitraums](azure-stack-manage-storage-accounts.md#set-the-retention-period)

### <a name="security-compliance-and-identity"></a>Sicherheit, Compliance und Identität  

#### <a name="manage-rbac"></a>Verwalten der RBAC

Ein Benutzer in Azure Stack kann ein Leser, ein Besitzer oder ein Mitwirkender an jeder Instanz eines Abonnements, einer Ressourcengruppe oder eines Diensts sein.

* [Verwalten der rollenbasierten Zugriffssteuerung](azure-stack-manage-permissions.md)

Wenn die integrierten Rollen für Azure-Ressourcen die Ansprüche Ihrer Organisation nicht erfüllen, können Sie Ihre eigenen benutzerdefinierten Rollen erstellen. In diesem Tutorial erstellen Sie mithilfe von Azure PowerShell eine benutzerdefinierte Rolle namens „Reader Support Tickets“.

* [Tutorial: Erstellen einer benutzerdefinierten Rolle für Azure-Ressourcen mithilfe von Azure PowerShell](https://docs.microsoft.com/azure/role-based-access-control/tutorial-custom-role-powershell)

### <a name="manage-usage-and-billing-as-a-csp"></a>Verwalten der Nutzung und Abrechnung als CSP

* [Verwalten der Nutzung und Abrechnung als CSP](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)
* [Erstellen eines CSP- oder APSS-Abonnements](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)

Wählen Sie den Typ des Kontos für gemeinsame Dienste aus, das Sie für Azure Stack verwenden. Folgende Abonnementtypen können für die Registrierung einer mehrinstanzenfähigen Azure Stack-Instanz verwendet werden:

* Cloud-Dienstanbieter
* Partner Shared Services-Abonnement


## <a name="troubleshoot-deployment"></a>Problembehandlung bei der Bereitstellung 
### <a name="general-deployment-failure"></a>Allgemeiner Bereitstellungsfehler
Wenn während der Installation ein Fehler auftritt, können Sie mithilfe der Option „-rerun“ des Bereitstellungsskripts die Bereitstellung ab dem Schritt neu starten, in dem der Fehler aufgetreten ist.  

### <a name="template-validation-error-parameter-osprofile-is-not-allowed"></a>Fehlerparameter „osProfile“ für die Vorlagenüberprüfung ist nicht zulässig

Wenn Sie bei der Vorlagenüberprüfung eine Fehlermeldung mit dem Hinweis erhalten, dass der Parameter „osProfile“ nicht zulässig ist, sollten Sie sicherstellen, dass Sie die richtigen Versionen der APIs für diese Komponenten verwenden:

- [Compute](https://docs.microsoft.com/azure-stack/user/azure-stack-profiles-azure-resource-manager-versions#microsoftcompute)
- [Netzwerk](https://docs.microsoft.com/azure-stack/user/azure-stack-profiles-azure-resource-manager-versions#microsoftnetwork)

Verwenden Sie [AzCopy 7.3.0](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#download-and-install-azcopy), um eine VHD von Azure nach Azure Stack zu kopieren. Wenden Sie sich an Ihren Anbieter, um Probleme zu beheben, die das Image selbst betreffen. Weitere Informationen zu den WALinuxAgent-Anforderungen für Azure Stack finden Sie unter [Azure LinuX-Agent](azure-stack-linux.md#azure-linux-agent).

### <a name="deployment-fails-due-to-lack-of-external-access"></a>Bereitstellungsfehler aufgrund von fehlendem externen Zugriff
Wenn es bei der Bereitstellung in Phasen, in denen ein externer Zugriff erforderlich ist, zu Fehlern kommt, wird eine Ausnahme wie im folgenden Beispiel zurückgegeben:

```
An error occurred while trying to test identity provider endpoints: System.Net.WebException: The operation has timed out.
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.GetResponse(WebRequest request)
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.ProcessRecord()at, <No file>: line 48 - 8/12/2018 2:40:08 AM
```
Falls dieser Fehler auftritt, sollten Sie überprüfen, ob die Mindestanforderungen für den Netzwerkbetrieb erfüllt sind. Überprüfen Sie hierzu die [Informationen zum Netzwerkdatenverkehr bei der Bereitstellung](deployment-networking.md). Für Partner steht im Rahmen des Toolkits für Partner außerdem ein Tool für die Netzwerküberprüfung zur Verfügung.

Andere Bereitstellungsfehler sind normalerweise auf Probleme bei der Verbindungsherstellung mit Ressourcen im Internet zurückzuführen.

Sie können die folgenden Schritte ausführen, um die Konnektivität mit Ressourcen im Internet zu überprüfen:

1. Öffnen Sie PowerShell.
2. Starten Sie über „Enter-PSSession“ eine Sitzung auf WAS01 oder einer beliebigen ERC-VM.
3. Führen Sie das folgende Cmdlet aus: 
   ```powershell
   Test-NetConnection login.windows.net -port 443
   ```

Wenn dieser Befehl nicht erfolgreich ist, überprüfen Sie, ob der TOR-Switch und alle weiteren Netzwerkgeräte so konfiguriert sind, dass sie [Netzwerkverkehr zulassen](azure-stack-network.md).

## <a name="troubleshoot-virtual-machines"></a>Problembehandlung bei virtuellen Computern
### <a name="default-image-and-gallery-item"></a>Standardimage und Katalogelement
Vor der Bereitstellung virtueller Computer in Azure Stack müssen Sie zuerst ein Windows Server-Image und ein Katalogelement hinzufügen.


### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk"></a>Ich habe einige virtuelle Computer gelöscht, die VHD-Dateien werden mir auf dem Datenträger aber weiterhin angezeigt
Dieses Verhalten ist beabsichtigt:

* Wenn Sie einen virtuellen Computer löschen, werden die VHDs nicht gelöscht. Datenträger sind separate Ressourcen in der Ressourcengruppe.
* Wenn ein Speicherkonto gelöscht wird, wird der Löschvorgang über Azure Resource Manager sofort angezeigt, die darin enthaltenen Datenträger bleiben jedoch möglicherweise im Speicher erhalten, bis die Garbage Collection ausgeführt wird.

Wenn „verwaiste“ VHDs angezeigt werden, müssen Sie wissen, ob diese zu einem Ordner für ein gelöschtes Speicherkonto gehören. Wenn das Speicherkonto nicht gelöscht wurde, ist es normal, dass die VHDs noch angezeigt werden.

Weitere Informationen zum Konfigurieren des Schwellenwerts für die Aufbewahrung und zur bedarfsgesteuerten Anforderung der Freigabe von Speicherplatz finden Sie unter [Manage Storage Accounts](azure-stack-manage-storage-accounts.md) (Verwalten von Speicherkonten).

## <a name="troubleshoot-storage"></a>Beheben von Problemen mit dem Speicher
### <a name="storage-reclamation"></a>Freigabe von Speicherplatz
Es kann bis zu 14 Stunden dauern, bis freigegebene Kapazität im Portal angezeigt wird. Die Freigabe von Speicherplatz hängt von verschiedenen Faktoren ab, einschließlich der prozentualen Auslastung von internen Containerdateien im Blockblobspeicher. Je nach gelöschter Datenmenge besteht daher keine Garantie für die Menge des Speicherplatzes, der freigegeben werden kann, wenn der Garbage Collector ausgeführt wird.

## <a name="troubleshooting-app-service"></a>Problembehandlung für App Service
### <a name="create-aadidentityappps1-script-fails"></a>Fehler für das Skript „Create-AADIdentityApp.ps1“

Wenn für das Skript „Create-AADIdentityApp.ps1“, das für App Service benötigt wird, ein Fehler auftritt, sollten Sie beim Ausführen des Skripts den erforderlichen Parameter „-AzureStackAdminCredential“ einfügen. Weitere Informationen finden Sie unter [Voraussetzungen für das Bereitstellen von App Service unter Azure Stack](azure-stack-app-service-before-you-get-started.md#create-an-azure-active-directory-app).

