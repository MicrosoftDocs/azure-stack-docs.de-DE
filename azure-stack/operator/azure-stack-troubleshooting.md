---
title: Problembehandlung in Azure Stack Hub
titleSuffix: Azure Stack
description: Erfahren Sie, wie Sie Probleme in Azure Stack Hub beheben, z. B. mit VMs, Speicher und App Service.
author: justinha
ms.topic: article
ms.date: 05/13/2020
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 15/13/2020
ms.openlocfilehash: de19e65866413ec4e498c9a21848c1f43af6d65a
ms.sourcegitcommit: 5f4f0ee043ff994efaad44129ce49be43c64d5dc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/16/2020
ms.locfileid: "84819516"
---
# <a name="troubleshoot-issues-in-azure-stack-hub"></a>Behandeln von Problemen in Azure Stack Hub

Dieses Dokument enthält Informationen zur Problembehandlung für integrierte Azure Stack Hub-Umgebungen. Hilfe zum Azure Stack Development Kit erhalten Sie unter [Problembehandlung beim ASDK](../asdk/asdk-troubleshooting.md) oder von den Experten im [MSDN-Forum für Azure Stack Hub](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack).

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

Diese Abschnitte enthalten Links zu Dokumentationen, in denen Sie Antworten auf häufige Fragen finden, die an den Microsoft-Support gesendet werden.

### <a name="purchase-considerations"></a>Überlegungen zum Kauf

* [Informationen zum Erwerb](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)
* [Übersicht über Azure Stack Hub](azure-stack-overview.md)

### <a name="updates-and-diagnostics"></a>Updates und Diagnose

* [Verwenden von Diagnosetools in Azure Stack Hub](azure-stack-diagnostics.md)
* [Überprüfen des Azure Stack Hub-Systemstatus](azure-stack-diagnostic-test.md)
* [Veröffentlichungsintervall für Updatepakete](azure-stack-servicing-policy.md#update-package-release-cadence)
* [Überprüfen und Behandeln von Problemen mit dem Knotenstatus](azure-stack-node-actions.md)

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

Sie können PowerShell verwenden, um ohne Hilfe seitens des Microsoft-Supports Informationen zur Stempelauslastung zu erhalten. Rufen Sie die Stempelauslastung wie folgt ab:

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

## <a name="troubleshoot-app-service"></a>Problembehandlung von App Service

### <a name="create-aadidentityappps1-script-fails"></a>Fehler für das Skript „Create-AADIdentityApp.ps1“

Wenn beim Skript „Create-AADIdentityApp.ps1“, das für App Service benötigt wird, ein Fehler auftritt, müssen Sie den erforderlichen Parameter `-AzureStackAdminCredential` einfügen. Weitere Informationen finden Sie unter [Voraussetzungen für das Bereitstellen von App Service in Azure Stack Hub](azure-stack-app-service-before-you-get-started.md#create-an-azure-ad-app).

## <a name="troubleshoot-azure-stack-hub-updates"></a>Problembehandlung von Azure Stack Hub-Updates

Der Azure Stack Hub-Patch und -Updatevorgang ermöglicht den Betreibern das konsistente und optimierte Anwenden von Updatepaketen. Probleme bei Patch- und Updatevorgängen sind zwar selten, können jedoch auftreten. Die folgenden Schritte werden empfohlen, wenn während des Patch- und Updatevorgangs ein Problem auftritt:

0. **Voraussetzungen:** Stellen Sie sicher, dass Sie die [Checkliste für Updateaktivitäten](release-notes-checklist.md) befolgt und die [proaktive Protokollsammlung konfiguriert](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md) haben.

1. Führen Sie die Behebungsschritte in der Fehlerwarnung aus, die beim Fehlschlagen des Updates erstellt wurde.

2. Wenn Sie das Problem nicht beheben konnten, erstellen Sie ein [Azure Stack Hub-Supportticket](azure-stack-help-and-support-overview-tzl.md). Stellen Sie sicher, dass für die Zeitspanne, in der das Problem aufgetreten ist, [Protokolle gesammelt](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md) wurden.

## <a name="common-azure-stack-hub-patch-and-update-issues"></a>Häufige Probleme bei Azure Stack Hub-Patches und -Updates

*Anwendungsbereich: Integrierte Azure Stack Hub-Systeme*

### <a name="preparationfailed"></a>PreparationFailed

**Geltungsbereich**: Dieses Problem gilt für alle unterstützten Versionen.

**Ursache:** Wenn Sie versuchen, das Azure Stack Hub-Update zu installieren, kommt es für das Update möglicherweise zu einem Statusfehler, und der Status wird in `PreparationFailed` geändert. Bei Systemen mit Internetverbindung weist dies in der Regel darauf hin, dass das Updatepaket aufgrund einer schlechten Internetverbindung nicht richtig heruntergeladen werden kann. 

**Abhilfe**: Sie können dieses Problem umgehen, indem Sie erneut auf **Jetzt installieren** klicken. Falls das Problem weiterhin besteht, empfehlen wir Ihnen das manuelle Hochladen des Updatepakets gemäß der Anleitung im Abschnitt [Installieren von Updates](azure-stack-apply-updates.md?#install-updates-and-monitor-progress).

**Häufigkeit**: Allgemein
