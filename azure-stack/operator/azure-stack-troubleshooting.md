---
title: Problembehandlung in Azure Stack Hub
titleSuffix: Azure Stack
description: Erfahren Sie, wie Sie Probleme in Azure Stack Hub beheben, z. B. mit VMs, Speicher und App Service.
author: myoungerman
ms.topic: article
ms.date: 07/21/2020
ms.author: v-myoung
ms.reviewer: prchint
ms.lastreviewed: 07/21/2020
ms.openlocfilehash: 403335a81a5ac02f2e32432f54df584baf7c2a02
ms.sourcegitcommit: 868887e4b13b1572f15004a9db2c334e60d8add2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2020
ms.locfileid: "91778150"
---
# <a name="troubleshoot-issues-in-azure-stack-hub"></a>Behandeln von Problemen in Azure Stack Hub

Dieses Dokument enthält Informationen zur Problembehandlung für integrierte Azure Stack Hub-Umgebungen. Hilfe zum Azure Stack Development Kit erhalten Sie unter [Problembehandlung beim ASDK](../asdk/asdk-troubleshooting.md) oder von den Experten im [MSDN-Forum für Azure Stack Hub](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack).

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

Diese Abschnitte enthalten Links zu Dokumentationen, in denen Sie Antworten auf häufige Fragen finden, die an den Microsoft-Support gesendet werden.

### <a name="purchase-considerations"></a>Überlegungen zum Kauf

* [Informationen zum Erwerb](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)
* [Übersicht über Azure Stack Hub](azure-stack-overview.md)

### <a name="updates-and-diagnostics"></a>Updates und Diagnose

* [Verwenden von Diagnosetools in Azure Stack Hub](./azure-stack-diagnostic-log-collection-overview.md)
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

* [Tutorial: Erstellen einer benutzerdefinierten Rolle für Azure-Ressourcen mithilfe von Azure PowerShell](/azure/role-based-access-control/tutorial-custom-role-powershell)

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

### <a name="reset-linux-vm-password"></a>Zurücksetzen des Kennworts eines virtuellen Linux-Computers

Wenn Sie das Kennwort für eine Linux-VM vergessen haben und die Option **Kennwort zurücksetzen** aufgrund von Problemen mit der VMAccess-Erweiterung nicht funktioniert, können Sie mit folgenden Schritten eine Rücksetzung ausführen:

1. Wählen Sie eine Linux-VM aus, die als Wiederherstellungs-VM genutzt werden soll.

1. Melden Sie sich beim Benutzerportal an:
   1. Notieren Sie sich VM-Größe, NIC, öffentliche IP-Adresse, Netzwerksicherheitsgruppe und Datenträger.
   1. Beenden Sie die betroffene VM.
   1. Entfernen Sie die betroffene VM.
   1. Fügen Sie den Datenträger der betroffenen VM als Datenträger an die Wiederherstellungs-VM an (möglicherweise dauert es einige Minuten, bis der Datenträger verfügbar ist).

1. Melden Sie sich bei der Wiederherstellungs-VM an, und führen Sie den folgenden Befehl aus:

   ```
   sudo su –
   mkdir /tempmount
   fdisk -l
   mount /dev/sdc2 /tempmount /*adjust /dev/sdc2 as necessary*/
   chroot /tempmount/
   passwd root /*substitute root with the user whose password you want to reset*/
   rm -f /.autorelabel /*Remove the .autorelabel file to prevent a time consuming SELinux relabel of the disk*/
   exit /*to exit the chroot environment*/
   umount /tempmount
   ```

1. Melden Sie sich beim Benutzerportal an:

   1. Trennen Sie den Datenträger von der Wiederherstellungs-VM.
   1. Erstellen Sie die VM auf Grundlage des Datenträgers neu.
   1. Stellen Sie sicher, dass Sie die öffentliche IP-Adresse von der vorherigen VM übertragen, die Datenträger anfügen usw.


Sie können auch eine Momentaufnahme des ursprünglichen Datenträgers anlegen und daraus einen neuen Datenträger erstellen, anstatt die Änderungen direkt auf dem ursprünglichen Datenträger auszuführen. Weitere Informationen finden Sie in den folgenden Themen:

- [Zurücksetzen des Kennworts](/azure/virtual-machines/troubleshooting/reset-password)
- [Erstellen eines Datenträgers aus einer Momentaufnahme](/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-portal-linux#create-a-disk-from-the-snapshot)
- [Erstellen und Zurücksetzen des Stammkennworts](https://access.redhat.com/documentation/red_hat_enterprise_linux/7/html/system_administrators_guide/sec-terminal_menu_editing_during_boot#sec-Changing_and_Resetting_the_Root_Password)


### <a name="license-activation-fails-for-windows-server-2012-r2-during-provisioning"></a>Fehler bei der Lizenzaktivierung Windows Server 2012 R2 während der Bereitstellung

In diesem Fall wird Windows nicht aktiviert, und in der unteren rechten Ecke des Bildschirms wird ein Wasserzeichen angezeigt. Die WaSetup.xml-Protokolle unter „C:\Windows\Panther“ enthalten das folgende Ereignis:

```xml
<Event time="2019-05-16T21:32:58.660Z" category="ERROR" source="Unattend">
    <UnhandledError>
        <Message>InstrumentProcedure: Failed to execute 'Call ConfigureLicensing()'. Will raise error to caller</Message>
        <Number>-2147221500</Number>
        <Description>Could not find the VOLUME_KMSCLIENT product</Description>
        <Source>Licensing.wsf</Source>
    </UnhandledError>
</Event>
```


Zum Aktivieren der Lizenz kopieren Sie den AVMA-Schlüssel (Automatic Virtual Machine Activation) für die SKU, die Sie aktivieren möchten.

|Edition|AVMA-Schlüssel|
|-|-|
|Datacenter|Y4TGP-NPTV9-HTC2H-7MGQ3-DV4TW|
|Standard|DBGBW-NPF86-BJVTX-K3WKJ-MTB6V|
|Essentials|K2XGM-NMBT3-2R6Q8-WF2FK-P36R2|

Führen Sie auf der VM den folgenden Befehl aus:

```powershell
slmgr /ipk <AVMA_key>
```

Ausführliche Informationen finden Sie unter [VM-Aktivierung](/windows-server/get-started-19/vm-activation-19).

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

Wenn Sie ein integriertes System in einem nicht verbundenen Szenario verwenden, wird die Verwendung einer Unternehmenszertifizierungsstelle empfohlen. Exportieren Sie das Stammzertifikat im Base64-Format, und importieren Sie es in Azure Storage-Explorer. Entfernen Sie unbedingt den nachgestellten Schrägstrich (`/`) vom Resource Manager-Endpunkt. Weitere Informationen finden Sie unter [Vorbereiten für die Verbindung mit Azure Stack Hub](../user/azure-stack-storage-connect-se.md).

## <a name="troubleshoot-app-service"></a>Problembehandlung von App Service

### <a name="create-aadidentityappps1-script-fails"></a>Fehler für das Skript „Create-AADIdentityApp.ps1“

Wenn beim Skript „Create-AADIdentityApp.ps1“, das für App Service benötigt wird, ein Fehler auftritt, müssen Sie den erforderlichen Parameter `-AzureStackAdminCredential` einfügen. Weitere Informationen finden Sie unter [Voraussetzungen für das Bereitstellen von App Service in Azure Stack Hub](azure-stack-app-service-before-you-get-started.md#create-an-azure-ad-app).

## <a name="troubleshoot-azure-stack-hub-updates"></a>Problembehandlung von Azure Stack Hub-Updates

Der Azure Stack Hub-Patch und -Updatevorgang ermöglicht den Betreibern das konsistente und optimierte Anwenden von Updatepaketen. Probleme bei Patch- und Updatevorgängen sind zwar selten, können jedoch auftreten. Die folgenden Schritte werden empfohlen, wenn während des Patch- und Updatevorgangs ein Problem auftritt:

0. **Voraussetzungen:** Stellen Sie sicher, dass Sie die [Checkliste für Updateaktivitäten](release-notes-checklist.md) befolgt und die [proaktive Protokollsammlung konfiguriert](./azure-stack-diagnostic-log-collection-overview.md#send-logs-proactively) haben.

1. Führen Sie die Behebungsschritte in der Fehlerwarnung aus, die beim Fehlschlagen des Updates erstellt wurde.

2. Wenn Sie das Problem nicht beheben konnten, erstellen Sie ein [Azure Stack Hub-Supportticket](./azure-stack-help-and-support-overview.md?view=azs-2002). Stellen Sie sicher, dass für die Zeitspanne, in der das Problem aufgetreten ist, [Protokolle gesammelt](./azure-stack-diagnostic-log-collection-overview.md#send-logs-now) wurden.

## <a name="common-azure-stack-hub-patch-and-update-issues"></a>Häufige Probleme bei Azure Stack Hub-Patches und -Updates

*Anwendungsbereich: Integrierte Azure Stack Hub-Systeme*

### <a name="preparationfailed"></a>PreparationFailed

**Geltungsbereich**: Dieses Problem gilt für alle unterstützten Versionen.

**Ursache:** Wenn Sie versuchen, das Azure Stack Hub-Update zu installieren, kommt es für das Update möglicherweise zu einem Statusfehler, und der Status wird in `PreparationFailed` geändert. Bei Systemen mit Internetverbindung weist dies in der Regel darauf hin, dass das Updatepaket aufgrund einer schlechten Internetverbindung nicht richtig heruntergeladen werden kann. 

**Abhilfe**: Sie können dieses Problem umgehen, indem Sie erneut auf **Jetzt installieren** klicken. Falls das Problem weiterhin besteht, empfehlen wir Ihnen das manuelle Hochladen des Updatepakets gemäß der Anleitung im Abschnitt [Installieren von Updates](azure-stack-apply-updates.md?#install-updates-and-monitor-progress).

**Häufigkeit**: Allgemein

::: moniker range="azs-2002"
### <a name="2002-update-failed"></a>Fehler bei Update 2002

**Geltungsbereich**: Dieses Problem gilt nur für Release 2002.

**Ursache:** Beim Versuch, das Update 2002 zu aktualisieren, könnte ein Fehler beim Update auftreten und diese Nachricht bereitgestellt werden: `The private network parameter is missing from cloud parameters. Please use set-azsprivatenetwork cmdlet to set private networkTrace`.

**Abhilfe**: [Richten Sie ein privates internes Netzwerk ein](https://docs.microsoft.com/azure-stack/operator/azure-stack-network?view=azs-2002#private-network).
::: moniker-end