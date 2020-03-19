---
title: Problembehandlung in Azure Stack Hub
titleSuffix: Azure Stack
description: Erfahren Sie, wie Sie Probleme in Azure Stack Hub beheben, z. B. mit VMs, Speicher und App Service.
author: justinha
ms.topic: article
ms.date: 11/05/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: fec8ac1797ef3fb6ce17b7173d813aff74ba3712
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/18/2020
ms.locfileid: "79512331"
---
# <a name="troubleshoot-issues-in-azure-stack-hub"></a>Behandeln von Problemen in Azure Stack Hub

Dieses Dokument enthält Informationen zur Problembehandlung für integrierte Azure Stack Hub-Umgebungen. Hilfe zum Azure Stack Development Kit erhalten Sie unter [Problembehandlung beim ASDK](../asdk/asdk-troubleshooting.md) oder von den Experten im [MSDN-Forum für Azure Stack Hub](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack).

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

Diese Abschnitte enthalten Links zu Dokumentationen, in denen Sie Antworten auf häufige Fragen finden, die an den Microsoft-Kundensupport gesendet werden.

### <a name="purchase-considerations"></a>Überlegungen zum Kauf

* [Informationen zum Erwerb](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)
* [Übersicht über Azure Stack Hub](azure-stack-overview.md)

### <a name="updates-and-diagnostics"></a>Updates und Diagnose

* [Verwenden von Diagnosetools in Azure Stack Hub](azure-stack-diagnostics.md)
* [Überprüfen des Azure Stack Hub-Systemstatus](azure-stack-diagnostic-test.md)
* [Veröffentlichungsintervall für Updatepakete](azure-stack-servicing-policy.md#update-package-release-cadence)

### <a name="supported-operating-systems-and-sizes-for-guest-vms"></a>Unterstützte Betriebssysteme und Größen für Gast-VMS

* [In Azure Stack Hub unterstützte Gastbetriebssysteme](azure-stack-supported-os.md)
* [In Azure Stack Hub unterstützte VM-Größen](../user/azure-stack-vm-sizes.md)

### <a name="azure-marketplace"></a>Azure Marketplace

* [Für Azure Stack Hub verfügbare Azure Marketplace-Elemente](azure-stack-marketplace-azure-items.md)

### <a name="manage-capacity"></a>Verwalten der Kapazität

#### <a name="memory"></a>Arbeitsspeicher

Sie können zusätzlichen Arbeitsspeicher hinzufügen, um die verfügbare Arbeitsspeicherkapazität für Azure Stack Hub zu erhöhen. In Azure Stack Hub wird Ihr physischer Server auch als Knoten einer Skalierungseinheit bezeichnet. Alle Skalierungseinheitknoten, die Mitglieder einer einzelnen Skalierungseinheit sind, müssen über die [gleiche Speichermenge](azure-stack-manage-storage-physical-memory-capacity.md) verfügen.

#### <a name="retention-period"></a>Beibehaltungsdauer

Mit dieser Option kann ein Cloudoperator einen Zeitraums in Tagen (zwischen 0 und 9999 Tage) festlegen, in dem gelöschte Konten ggf. wiederhergestellt werden können. Der Standardaufbewahrungszeitraum ist auf **0** Tage festgelegt. Mit dem Wert **0** wird festgelegt, dass alle gelöschten Konten sofort aus der Aufbewahrung entfernt und für die regelmäßige automatische Speicherbereinigung markiert werden.

* [Festlegen des Aufbewahrungszeitraums](azure-stack-manage-storage-accounts.md#set-the-retention-period)

### <a name="security-compliance-and-identity"></a>Sicherheit, Compliance und Identität  

#### <a name="manage-rbac"></a>Verwalten der RBAC

Ein Benutzer in Azure Stack Hub kann ein Leser, ein Besitzer oder ein Mitwirkender jeder Instanz eines Abonnements, einer Ressourcengruppe oder eines Diensts sein.

* [Verwalten der rollenbasierten Zugriffssteuerung in Azure Stack Hub](azure-stack-manage-permissions.md)

Wenn die integrierten Rollen für Azure-Ressourcen die Ansprüche Ihrer Organisation nicht erfüllen, können Sie Ihre eigenen benutzerdefinierten Rollen erstellen. In diesem Tutorial erstellen Sie mithilfe von Azure PowerShell eine benutzerdefinierte Rolle namens „Reader Support Tickets“.

* [Tutorial: Erstellen einer benutzerdefinierten Rolle für Azure-Ressourcen mithilfe von Azure PowerShell](https://docs.microsoft.com/azure/role-based-access-control/tutorial-custom-role-powershell)

### <a name="manage-usage-and-billing-as-a-csp"></a>Verwalten der Nutzung und Abrechnung als CSP

* [Verwalten der Nutzung und Abrechnung als CSP](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)
* [Erstellen eines CSP- oder APSS-Abonnements](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)

Wählen Sie den Typ des Kontos für gemeinsame Dienste aus, das Sie für Azure Stack Hub verwenden. Folgende Abonnementtypen können für die Registrierung einer mehrinstanzenfähigen Azure Stack Hub-Instanz verwendet werden:

* Cloud Solution Provider
* Partner Shared Services-Abonnement

### <a name="get-scale-unit-metrics"></a>Abrufen der Metriken von Skalierungseinheiten

Sie können PowerShell verwenden, um ohne CSS-Hilfe Informationen zur Stempelauslastung zu erhalten. Rufen Sie die Stempelauslastung wie folgt ab:

1. Erstellen Sie eine PEP-Sitzung.
2. Führen Sie `test-azurestack` aus.
3. Beenden Sie die PEP-Sitzung.
4. Führen Sie `get-azurestacklog -filterbyrole seedring` mithilfe eines invoke-command-Aufrufs aus.
5. Extrahieren Sie das Seedring-ZIP-Archiv. Sie können in dem ERCS-Ordner, in dem Sie `test-azurestack` ausgeführt haben, auf den Bericht der Überprüfung zugreifen.

Weitere Informationen finden Sie unter [Azure Stack Hub-Diagnose](azure-stack-get-azurestacklog.md).

## <a name="troubleshoot-virtual-machines-vms"></a>Problembehandlung bei virtuellen Computern

### <a name="default-image-and-gallery-item"></a>Standardimage und Katalogelement

Vor der Bereitstellung virtueller Computer in Azure Stack Hub müssen Sie zuerst ein Windows Server-Image und ein Katalogelement hinzufügen.

### <a name="ive-deleted-some-vms-but-still-see-the-vhd-files-on-disk"></a>Ich habe einige VMs gelöscht, aber die VHD-Dateien werden auf dem Datenträger weiterhin angezeigt

Dieses Verhalten ist beabsichtigt:

* Wenn Sie einen virtuellen Computer löschen, werden die VHDs nicht gelöscht. Datenträger sind separate Ressourcen in der Ressourcengruppe.
* Wenn ein Speicherkonto gelöscht wird, ist dieser Löschvorgang sofort in Azure Resource Manager sichtbar. Die enthaltenen Datenträger werden jedoch noch so lange im Speicher beibehalten, bis die Garbage Collection ausgeführt wurde.

Wenn „verwaiste“ VHD-Dateien angezeigt werden, müssen Sie wissen, ob diese zu einem Ordner für ein gelöschtes Speicherkonto gehören. Wenn das Speicherkonto nicht gelöscht wurde, ist es normal, dass die Dateien weiterhin bestehen.

Weitere Informationen zum Konfigurieren des Schwellenwerts für die Aufbewahrung und zur bedarfsgesteuerten Anforderung der Freigabe von Speicherplatz finden Sie unter [Manage Storage Accounts](azure-stack-manage-storage-accounts.md) (Verwalten von Speicherkonten).

## <a name="troubleshoot-storage"></a>Beheben von Problemen mit dem Speicher

### <a name="storage-reclamation"></a>Freigabe von Speicherplatz

Es kann bis zu 14 Stunden dauern, bis freigegebene Kapazität im Portal angezeigt wird. Die Freigabe von Speicherplatz hängt von verschiedenen Faktoren ab, einschließlich der prozentualen Auslastung von internen Containerdateien im Blockblobspeicher. Je nach gelöschter Datenmenge besteht daher keine Garantie für die Menge des Speicherplatzes, die durch die Ausführung des Garbage Collectors freigegeben werden kann.

### <a name="azure-storage-explorer-not-working-with-azure-stack-hub"></a>Azure Storage-Explorer funktioniert nicht mit Azure Stack Hub.

Wenn Sie ein integriertes System in einem nicht verbundenen Szenario verwenden, wird die Verwendung einer Unternehmenszertifizierungsstelle empfohlen. Exportieren Sie das Stammzertifikat im Base64-Format, und importieren Sie es in Azure Storage-Explorer. Entfernen Sie unbedingt den nachgestellten Schrägstrich (`/`) vom Resource Manager-Endpunkt. Weitere Informationen finden Sie unter [Vorbereiten für die Verbindung mit Azure Stack Hub](/azure-stack/user/azure-stack-storage-connect-se).

## <a name="troubleshooting-app-service"></a>Problembehandlung für App Service

### <a name="create-aadidentityappps1-script-fails"></a>Fehler für das Skript „Create-AADIdentityApp.ps1“

Wenn beim Skript „Create-AADIdentityApp.ps1“, das für App Service benötigt wird, ein Fehler auftritt, müssen Sie den erforderlichen Parameter `-AzureStackAdminCredential` einfügen. Weitere Informationen finden Sie unter [Voraussetzungen für das Bereitstellen von App Service in Azure Stack Hub](azure-stack-app-service-before-you-get-started.md#create-an-azure-active-directory-app).
