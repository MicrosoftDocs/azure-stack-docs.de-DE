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
ms.date: 11/05/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 28e067abef24fbc60cff629e5112ffacdf14b1ec
ms.sourcegitcommit: 7817d61fa34ac4f6410ce6f8ac11d292e1ad807c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689940"
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

* Cloud Solution Provider
* Partner Shared Services-Abonnement

### <a name="get-scale-unit-metrics"></a>Abrufen der Metriken von Skalierungseinheiten

Sie können PowerShell verwenden, um ohne CSS-Hilfe Informationen zur Stempelauslastung zu erhalten. Rufen Sie die Stempelauslastung wie folgt ab: 

1. Erstellen Sie eine PEP-Sitzung.
2. Führen Sie „test-azurestack“ aus.
3. Beenden Sie die PEP-Sitzung.
4. Führen Sie das SeedRing-Element „get-azurestacklog -filterbyrole“ aus, indem Sie einen „invoke-command“-Aufruf verwenden.
5. Extrahieren Sie die SeedRing-ZIP-Datei, um im ERCS-Ordner, in dem Sie „test-azurestack“ ausgeführt haben, auf den Bericht der Überprüfung zuzugreifen.

Weitere Informationen finden Sie unter [Azure Stack-Diagnose](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs).

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

### <a name="azure-storage-explorer-not-working-with-azure-stack"></a>Azure Storage-Explorer funktioniert nicht mit Azure Stack. 
 
Wenn Sie ein integriertes System in einem nicht verbundenen Szenario verwenden, wird die Verwendung einer Unternehmenszertifizierungsstelle empfohlen. Exportieren Sie das Stammzertifikat im Base64-Format, und importieren Sie es in Azure Storage-Explorer. Entfernen Sie unbedingt den nachstehenden Schrägstrich (/) vom ARM-Endpunkt. Weitere Informationen finden Sie unter [Herstellen einer Verbindung von Storage-Explorer mit einem Azure Stack-Abonnement oder -Speicherkonto](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-connect-se#prepare-for-connecting-to-azure-stack).
 

## <a name="troubleshooting-app-service"></a>Problembehandlung für App Service
### <a name="create-aadidentityappps1-script-fails"></a>Fehler für das Skript „Create-AADIdentityApp.ps1“

Wenn für das Skript „Create-AADIdentityApp.ps1“, das für App Service benötigt wird, ein Fehler auftritt, sollten Sie beim Ausführen des Skripts den erforderlichen Parameter „-AzureStackAdminCredential“ einfügen. Weitere Informationen finden Sie unter [Voraussetzungen für das Bereitstellen von App Service unter Azure Stack](azure-stack-app-service-before-you-get-started.md#create-an-azure-active-directory-app).

