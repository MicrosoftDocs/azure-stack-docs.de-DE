---
title: 'Azure Stack HCI-Versionshinweise: Archiv'
description: Enthält die archivierten Versionshinweise für Azure Stack HCI-Version 20H2 (Public Preview-Versionen).
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 12/21/2020
ms.openlocfilehash: 5125e9b27fbb9cc17e9381a9617fefce6409418f
ms.sourcegitcommit: a745662c7a5a18f135accf3f70d8508b57e83e2b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/22/2020
ms.locfileid: "97737945"
---
# <a name="archived-release-notes-for-azure-stack-hci-version-20h2-public-preview"></a>Archivierte Versionshinweise für Azure Stack HCI-Version 20H2 (Public Preview)

> Gilt für: Azure Stack HCI, Version 20H2

Dieser Artikel enthält eine Beschreibung des Inhalts von Updatepaketen für Azure Stack HCI-Version 20H2 (Public Preview-Versionen), die vor der Version vom Typ „Allgemeine Verfügbarkeit“ am 10. Dezember 2020 veröffentlicht wurde. Sie können auch auf die aktuellen [Versionshinweise für Azure Stack HCI](https://support.microsoft.com/help/4595086/) zugreifen.

## <a name="december-8-2020-security-update-kb4592441"></a>8\. Dezember 2020, Sicherheitsupdate (KB4592441)

Dieses Update enthält Verbesserungen und Fehlerbehebungen für die Public Preview-Version von Azure Stack HCI. 

### <a name="improvements-and-fixes"></a>Verbesserungen und Fehlerbehebungen
Dieses Update enthält verschiedene Sicherheitsverbesserungen für interne Betriebssystemfunktionen. Es behebt ein Sicherheitsrisiko, indem es verhindert, dass Anwendungen, die unter dem SYSTEM-Konto ausgeführt werden, Ausgaben an „FILE:“-Ports vornehmen. Um dieses Problem in Zukunft zu beheben, stellen Sie sicher, dass Ihre Anwendungen oder Dienste unter einem bestimmten Benutzer- oder Dienstkonto ausgeführt werden.

Für dieses Release wurden keine weiteren Probleme dokumentiert.

Weitere Informationen zu den beseitigten Sicherheitsrisiken finden Sie im [Leitfaden für Sicherheitsupdates](https://portal.msrc.microsoft.com/security-guidance).

### <a name="known-issues-in-this-update"></a>Bekannte Probleme in diesem Update
Microsoft sind aktuell keine Probleme mit diesem Update bekannt.

### <a name="how-to-get-this-update"></a>Beziehen dieses Updates
Das Sicherheitsupdate vom 8. Dezember 2020 (KB4592441) für [Azure Stack HCI (Vorschauversion)](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) wird über Windows Update bereitgestellt. Informationen zur Installation in Ihrem Azure Stack HCI-Cluster finden Sie unter [Aktualisieren von Azure Stack HCI-Clustern](manage/update-cluster.md).

### <a name="file-information"></a>Dateiinformationen
Eine Liste der im Rahmen dieses Updates (Betriebssystembuild 17784.1408) bereitgestellten Dateien erhalten Sie durch Herunterladen der [Dateiinformationen für das kumulative Update 4592441](https://download.microsoft.com/download/2/7/2/272ea75f-1657-43ce-a7a4-a17d51463a94/4592441.csv).

   > [!NOTE]
   > Einige Dateien sind in der Dateiversionsspalte der CSV-Datei fälschlicherweise mit „Not applicable“ (Nicht zutreffend) gekennzeichnet. Dies kann bei der Überprüfung des Builds durch ein Drittanbietertool zu falsch positiven oder falsch negativen Ergebnissen führen.

## <a name="november-23-2020-preview-update-kb4586852"></a>23. November, 2020 Update der Vorschauversion (KB4586852)

Dieses Update enthält Verbesserungen und Fehlerbehebungen für die Public Preview-Version von Azure Stack HCI. 

   > [!IMPORTANT]
   > Wenn Sie Azure Stack HCI-Cluster konfiguriert und mit dem Public Preview-Image registriert haben, müssen Sie Ihre Azure-Registrierung reparieren, nachdem das KB4586852-Update installiert wurde, um die neuen Funktionen zu verwenden, die das Update bietet. Führen Sie nach der Installation des Updates die folgenden Schritte für jeden Cluster aus:
   >
   > 1. Stellen Sie sicher, dass alle Server im Cluster auf KB4586852 aktualisiert wurden. Wenn dies nicht der Fall ist, schlägt die Reparatur fehl und zeigt die Knoten an, die aktualisiert werden müssen.
   >
   > 2. Stellen Sie entweder lokal oder mithilfe von `Enter-PSSession <server-name>` eine Verbindung mit einem der Clusterknoten her.
   >
   > 3. Laden Sie das Registrierungsmodul „AzStackHCI v0.4.1“ (oder höher) aus dem PowerShell-Katalog herunter. Führen Sie `Install-Module -Name Az.StackHCI` aus, um das aktuellste Modul zu erhalten.
   >
   > 4. Führen Sie den folgenden Befehl aus, um Ihre Registrierung zu reparieren. Verwenden Sie die Abonnement-ID, die ursprünglich zum Registrieren des Clusters verwendet wurde. Mit `Get-AzureStackHCI` wird der aktuelle Azure Resource Manager-URI angezeigt, unter dem die Abonnementinformationen zu finden sind.
   >
   >   ```PowerShell
   >   Register-AzStackHCI -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -RepairRegistration
   >   ```
   > 

### <a name="improvements-and-fixes"></a>Verbesserungen und Fehlerbehebungen
Dieses Nicht-Sicherheitsupdate umfasst Qualitätsverbesserungen. Zu den wichtigsten Änderungen gehören: 

- Mit diesem Update können Azure Stack HCI-Kunden mit gültigen Lizenzen für Windows Server 2019 Datacenter Edition diese verwenden, um in Azure Stack HCI gehostete virtuelle Computer (VMs) ganz einfach zu aktivieren, ohne die Product Keys für die einzelnen virtuellen Computer verwalten zu müssen. Insbesondere können Sie Windows Admin Center oder PowerShell verwenden, um einen nicht verwendeten Aktivierungsschlüssel von Windows Server 2019 Datacenter Edition direkt in den Azure Stack HCI-Host einzugeben, um die automatische VM-Aktivierung (AVMA) zu aktivieren. Virtuelle Computer, auf denen Windows Server 2019 oder niedriger ausgeführt wird, können dann die Aktivierung vom Host erben. Der eingegebene Schlüssel kann ein Mehrfachaktivierungsschlüssel (Multiple Activation Key, MAK) sein, der aus dem Volume Licensing Center stammt, der Schlüssel, der auf dem Aufkleber mit dem Echtheitszertifikat (Certificate of Authenticity, COA) steht, das an einem OEM-Server angebracht ist, oder der Schlüssel einer Einzelhandelskopie von Windows Server 2019 Datacenter Edition in einer Schachtel. In dieser Version werden generische Volumenlizenzschlüssel (GVLK) nicht unterstützt.

- Azure Stack HCI sammelt nun erforderliche Diagnosedaten, wobei es sich um die Daten handelt, die mindestens erforderlich sind, um das Gerät sicher und auf dem neuesten Stand zu halten sowie seine erwartungsgemäße Funktion sicherzustellen. Mit den erforderlichen Diagnosedaten wird eine begrenzte Menge von Daten gesammelt, die für das Verständnis des Geräts und seiner Konfiguration wichtig sind. Diese Daten helfen bei der Identifizierung von Problemen, die bei einer bestimmten Hardware- oder Softwarekonfiguration auftreten können.  

### <a name="known-issues-in-this-update"></a>Bekannte Probleme in diesem Update
Microsoft sind aktuell keine Probleme mit diesem Update bekannt.

### <a name="how-to-get-this-update"></a>Beziehen dieses Updates 
Das Sicherheitsupdate vom 23. November 2020 (KB4586852) für [Azure Stack HCI (Vorschauversion)](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) wird über Windows Update bereitgestellt. Informationen zur Installation in Ihrem Azure Stack HCI-Cluster finden Sie unter [Aktualisieren von Azure Stack HCI-Clustern](manage/update-cluster.md).

### <a name="file-information"></a>Dateiinformationen
Eine Liste der im Rahmen dieses Updates (Betriebssystembuild 17784.1381) bereitgestellten Dateien erhalten Sie durch Herunterladen der  [Dateiinformationen für das kumulative Update 4586852](https://download.microsoft.com/download/5/c/6/5c6f8c37-3e0b-4239-a6d9-9c709e18e869/4586852.csv).

   > [!NOTE]
   > Einige Dateien sind in der Dateiversionsspalte der CSV-Datei fälschlicherweise mit „Not applicable“ (Nicht zutreffend) gekennzeichnet. Dies kann bei der Überprüfung des Builds durch ein Drittanbietertool zu falsch positiven oder falsch negativen Ergebnissen führen.

## <a name="november-10-2020-security-update-kb4586811"></a>10. November 2020, Sicherheitsupdate (KB4586811)

Dieses Update enthält Verbesserungen und Fehlerbehebungen für die Public Preview-Version von Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Verbesserungen und Fehlerbehebungen
Dieses Update enthält verschiedene Sicherheitsverbesserungen für interne Betriebssystemfunktionen. Es behebt ein Sicherheitsrisiko, indem es verhindert, dass Anwendungen, die unter dem SYSTEM-Konto ausgeführt werden, auf lokale Ports drucken, die auf eine Datei verweisen. Druckaufträge, die fehlschlagen, protokollieren den Fehler 50, „Die Anforderung wird nicht unterstützt“, mit der Ereignis-ID 372 im Ereignisprotokoll „PrintService\Admin“. Um dieses Problem in Zukunft zu beheben, stellen Sie sicher, dass Ihre Anwendungen oder Dienste unter einem bestimmten Benutzer- oder Dienstkonto ausgeführt werden.

Für dieses Release wurden keine weiteren Probleme dokumentiert.

Weitere Informationen zu den beseitigten Sicherheitsrisiken finden Sie im [Leitfaden für Sicherheitsupdates](https://portal.msrc.microsoft.com/security-guidance).

### <a name="known-issues-in-this-update"></a>Bekannte Probleme in diesem Update
Microsoft sind aktuell keine Probleme mit diesem Update bekannt.

### <a name="how-to-get-this-update"></a>Beziehen dieses Updates
Das Sicherheitsupdate vom 10. November 2020 (KB4586811) für [Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) wird über Windows Update bereitgestellt. Informationen zur Installation in Ihrem Azure Stack HCI-Cluster finden Sie unter [Aktualisieren von Azure Stack HCI-Clustern](manage/update-cluster.md).

### <a name="file-information"></a>Dateiinformationen
Eine Liste der im Rahmen dieses Updates (Betriebssystembuild 17784.1345) bereitgestellten Dateien erhalten Sie durch Herunterladen der [Dateiinformationen für das kumulative Update 4586811](https://download.microsoft.com/download/8/f/2/8f2ce4bb-e113-4abc-b3ff-f0f4c4c71403/4586811.csv).

   > [!NOTE]
   > Einige Dateien sind in der Dateiversionsspalte der CSV-Datei fälschlicherweise mit „Not applicable“ (Nicht zutreffend) gekennzeichnet. Dies kann bei der Überprüfung des Builds durch ein Drittanbietertool zu falsch positiven oder falsch negativen Ergebnissen führen.

## <a name="november-10-2020-servicing-stack-update-kb4590242"></a>10. November 2020, Wartungsstapelaktualisierung (KB4590242)

Dieses Update enthält Qualitätsverbesserungen für die Public Preview-Version von Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Verbesserungen und Fehlerbehebungen
Dieses Update verbessert die Qualität des Wartungsstapels, der Komponente zum Installieren von Updates. Wartungsstapelaktualisierungen sorgen für einen stabilen und zuverlässigen Wartungsstapel, damit Ihre Geräte Microsoft-Updates empfangen und installieren können.

### <a name="how-to-get-this-update"></a>Beziehen dieses Updates
Die Wartungsstapelaktualisierung von November 2020 (KB4590242) für [Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) wird über Windows Update bereitgestellt. Informationen zur Installation in Ihrem Azure Stack HCI-Cluster finden Sie unter [Aktualisieren von Azure Stack HCI-Clustern](manage/update-cluster.md).

### <a name="restart-information"></a>Informationen zum Neustart
Sie müssen den Computer nach Anwenden dieses Updates nicht neu starten.

### <a name="removal-information"></a>Informationen zum Entfernen
Wartungsstapelaktualisierungen nehmen Änderungen an der Installation von Updates vor und können nicht vom Gerät deinstalliert werden.

### <a name="file-information"></a>Dateiinformationen
Eine Liste der im Rahmen dieses Updates (Betriebssystembuild 17784.1342) bereitgestellten Dateien erhalten Sie durch Herunterladen der [Dateiinformationen für das kumulative Update 4590242](https://download.microsoft.com/download/b/b/4/bb4fb4f5-c0ba-4e55-bada-d72310857982/4590242.csv).

### <a name="references"></a>References

Informationen zu Wartungsstapelaktualisierungen finden Sie in den folgenden Artikeln:

- [Wartungsstapelaktualisierungen](/windows/deployment/update/servicing-stack-updates)
- [Wartungsstapelaktualisierungen: häufig gestellte Fragen](https://support.microsoft.com/help/4535697)

Erfahren Sie mehr über die [Terminologie](https://support.microsoft.com/help/824684), die Microsoft zum Beschreiben von Softwareupdates verwendet.

## <a name="october-20-2020-preview-update-kb4580388"></a>Update der Vorschauversion vom 20. Oktober 2020 (KB4580388)

Dieses Update enthält Verbesserungen und Fehlerbehebungen für die Public Preview-Version von Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Verbesserungen und Fehlerbehebungen
Dieses Nicht-Sicherheitsupdate umfasst Qualitätsverbesserungen. Zu den wichtigsten Änderungen gehören:

- Mit diesem Update können Azure Stack HCI-Kunden mit gültigen Lizenzen für Windows Server 2019 Datacenter Edition diese verwenden, um in Azure Stack HCI gehostete virtuelle Computer (VMs) ganz einfach zu aktivieren, ohne die Product Keys für die einzelnen virtuellen Computer verwalten zu müssen.

### <a name="known-issues-in-this-update"></a>Bekannte Probleme in diesem Update

Microsoft ist ein Problem bei diesem Update bekannt.

#### <a name="symptom"></a>Symptom
Wenn Sie die Livemigration zum Verschieben einer VM zwischen Windows Server und Azure Stack HCI-Betriebssystemen verwenden, wird möglicherweise der folgende Fehler angezeigt: „Ein Migrationsvorgang für den virtuellen Computer <vmname> wurde blockiert, da die VM-Migration zwischen unterschiedlichen Windows-Editionen nicht unterstützt wird (ID des virtuellen Computers).“

Dies kann auch zu einem Fehler bei einer clusterfähigen Aktualisierung (Cluster Aware Update, CAU) führen, wenn einer der virtuellen Computer während der clusterfähigen Aktualisierung eine Livemigration ausführen soll.

#### <a name="workaround"></a>Problemumgehung

Verwenden Sie anstelle der Livemigration die Schnellmigration. Wenn Sie CAU verwenden, ändern Sie das Standardverhalten zeitweilig, damit beim CAU die Schnellmigration verwendet wird.

Beispiel:

```powershell
Get-ClusterResourceType "Virtual Machine" | Set-ClusterParameter MoveTypeThreshold 3001
```

Es wird empfohlen, nach einem erfolgreichen Abschluss des CAU wieder den vorherigen `MoveTypeThreshold`-Wert festzulegen.

Weitere Informationen finden Sie unter [Configuring How VMs Are Moved when a Node is Drained](https://techcommunity.microsoft.com/t5/failover-clustering/configuring-how-vms-are-moved-when-a-node-is-drained/ba-p/371848) (Konfigurieren der Verschiebung von VMs nach dem Entfernen eines Knotens).

### <a name="how-to-get-this-update"></a>Beziehen dieses Updates
Das Sicherheitsupdate vom 20. Oktober 2020 (KB4580388) für [Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) wird über Windows Update bereitgestellt. Informationen zur Installation in Ihrem Azure Stack HCI-Cluster finden Sie unter [Aktualisieren von Azure Stack HCI-Clustern](manage/update-cluster.md).

### <a name="file-information"></a>Dateiinformationen
Eine Liste der im Rahmen dieses Updates (Betriebssystembuild 17784.1321) bereitgestellten Dateien erhalten Sie durch Herunterladen der [Dateiinformationen für das kumulative Update 4580388](https://download.microsoft.com/download/2/f/b/2fb766d3-c4c8-4279-8718-8efbd0b6f211/4580388.csv).

   > [!NOTE]
   > Einige Dateien sind in der Dateiversionsspalte der CSV-Datei fälschlicherweise mit „Not applicable“ (Nicht zutreffend) gekennzeichnet. Dies kann bei der Überprüfung des Builds durch ein Drittanbietertool zu falsch positiven oder falsch negativen Ergebnissen führen.

## <a name="october-13-2020-security-update-kb4580363"></a>13. Oktober 2020 Sicherheitsupdate (KB4580363)

Dieses Update enthält Verbesserungen und Fehlerbehebungen für die Public Preview-Version von Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Verbesserungen und Fehlerbehebungen
Dieses Update enthält verschiedene Sicherheitsverbesserungen für interne Betriebssystemfunktionen. Für dieses Release wurden keine weiteren Probleme dokumentiert.

Weitere Informationen zu den beseitigten Sicherheitsrisiken finden Sie im [Leitfaden für Sicherheitsupdates](https://portal.msrc.microsoft.com/security-guidance).

### <a name="known-issues-in-this-update"></a>Bekannte Probleme in diesem Update
Microsoft sind aktuell keine Probleme mit diesem Update bekannt.

### <a name="how-to-get-this-update"></a>Beziehen dieses Updates
Das Sicherheitsupdate vom 13. Oktober 2020 (KB4580363) für [Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) wird über Windows Update bereitgestellt. Informationen zur Installation in Ihrem Azure Stack HCI-Cluster finden Sie unter [Aktualisieren von Azure Stack HCI-Clustern](manage/update-cluster.md).

### <a name="file-information"></a>Dateiinformationen
Eine Liste der im Rahmen dieses Updates (Betriebssystembuild 17784.1288) bereitgestellten Dateien erhalten Sie durch Herunterladen der [Dateiinformationen für das kumulative Update 4580363](https://download.microsoft.com/download/f/7/8/f78801f0-e7e5-4a3d-a971-f642ccd24ee4/4580363.csv).

   > [!NOTE]
   > Einige Dateien sind in der Dateiversionsspalte der CSV-Datei fälschlicherweise mit „Not applicable“ (Nicht zutreffend) gekennzeichnet. Dies kann bei der Überprüfung des Builds durch ein Drittanbietertool zu falsch positiven oder falsch negativen Ergebnissen führen.

## <a name="october-13-2020-servicing-stack-update-kb4583287"></a>13. Oktober 2020, Wartungsstapelaktualisierung (KB4583287)

Dieses Update enthält Qualitätsverbesserungen für die Public Preview-Version von Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Verbesserungen und Fehlerbehebungen
Dieses Update verbessert die Qualität des Wartungsstapels, der Komponente zum Installieren von Updates. Wartungsstapelaktualisierungen sorgen für einen stabilen und zuverlässigen Wartungsstapel, damit Ihre Geräte Microsoft-Updates empfangen und installieren können.

### <a name="how-to-get-this-update"></a>Beziehen dieses Updates
Die Wartungsstapelaktualisierung vom 13. Oktober 2020 (KB4583287) für [Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) wird über Windows Update bereitgestellt. Informationen zur Installation in Ihrem Azure Stack HCI-Cluster finden Sie unter [Aktualisieren von Azure Stack HCI-Clustern](manage/update-cluster.md).

### <a name="restart-information"></a>Informationen zum Neustart
Sie müssen den Computer nach Anwenden dieses Updates nicht neu starten.

### <a name="removal-information"></a>Informationen zum Entfernen
Wartungsstapelaktualisierungen nehmen Änderungen an der Installation von Updates vor und können nicht vom Gerät deinstalliert werden.

### <a name="file-information"></a>Dateiinformationen
Eine Liste der im Rahmen dieses Updates (Betriebssystembuild 17784.1287) bereitgestellten Dateien erhalten Sie durch Herunterladen der [Dateiinformationen für das kumulative Update 4583287](https://download.microsoft.com/download/b/8/5/b85160fb-85d9-49f9-b9d5-7dbc0158a944/4583287.csv).

### <a name="references"></a>References

Informationen zu Wartungsstapelaktualisierungen finden Sie in den folgenden Artikeln:

- [Wartungsstapelaktualisierungen](/windows/deployment/update/servicing-stack-updates)
- [Wartungsstapelaktualisierungen: häufig gestellte Fragen](https://support.microsoft.com/help/4535697)

Erfahren Sie mehr über die [Terminologie](https://support.microsoft.com/help/824684), die Microsoft zum Beschreiben von Softwareupdates verwendet.

## <a name="september-17-2020-preview-update-kb4577629"></a>17. September 2020 Preview-Update (KB4577629)

Dieses Update enthält Verbesserungen und Fehlerbehebungen für die Public Preview-Version von Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Verbesserungen und Fehlerbehebungen
Dieses Nicht-Sicherheitsupdate umfasst Qualitätsverbesserungen. Zu den wichtigsten Änderungen gehören:
- Es wurde ein Problem behandelt, bei dem der Datenverkehr des Softwarelastenausgleichs, der den Multiplexer durchläuft, möglicherweise auf einen anderen Host umgeleitet wird, was zu einem Verbindungsfehler der Anwendung führen kann.

### <a name="known-issues-in-this-update"></a>Bekannte Probleme in diesem Update
Microsoft sind aktuell keine Probleme mit diesem Update bekannt.

### <a name="how-to-get-this-update"></a>Beziehen dieses Updates
Das Sicherheitsupdate vom 17. September 2020 (KB4577629) für [Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) wird über Windows Update bereitgestellt. Informationen zur Installation in Ihrem Azure Stack HCI-Cluster finden Sie unter [Aktualisieren von Azure Stack HCI-Clustern](manage/update-cluster.md).

### <a name="file-information"></a>Dateiinformationen
Eine Liste der im Rahmen dieses Updates (Betriebssystembuild 17784.1259) bereitgestellten Dateien erhalten Sie durch Herunterladen der [Dateiinformationen für das kumulative Update 4577629](https://download.microsoft.com/download/9/1/a/91addcbb-2b36-408c-ab88-736de42edb98/4577629.csv).

   > [!NOTE]
   > Einige Dateien sind in der Dateiversionsspalte der CSV-Datei fälschlicherweise mit „Not applicable“ (Nicht zutreffend) gekennzeichnet. Dies kann bei der Überprüfung des Builds durch ein Drittanbietertool zu falsch positiven oder falsch negativen Ergebnissen führen.

## <a name="september-8-2020-security-update-kb4577470"></a>8\. September 2020, Sicherheitsupdate (KB4577470)

Dieses Update enthält Verbesserungen und Fehlerbehebungen für die Public Preview-Version von Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Verbesserungen und Fehlerbehebungen
Dieses Update enthält verschiedene Sicherheitsverbesserungen für interne Betriebssystemfunktionen. Für dieses Release wurden keine weiteren Probleme dokumentiert.

Weitere Informationen zu den beseitigten Sicherheitsrisiken finden Sie im [Leitfaden für Sicherheitsupdates](https://portal.msrc.microsoft.com/security-guidance).

### <a name="known-issues-in-this-update"></a>Bekannte Probleme in diesem Update
Microsoft sind aktuell keine Probleme mit diesem Update bekannt.

### <a name="how-to-get-this-update"></a>Beziehen dieses Updates
Das Sicherheitsupdate vom 8. September 2020 (KB4577470) für [Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) wird über Windows Update bereitgestellt. Informationen zur Installation in Ihrem Azure Stack HCI-Cluster finden Sie unter [Aktualisieren von Azure Stack HCI-Clustern](manage/update-cluster.md).

### <a name="file-information"></a>Dateiinformationen
Eine Liste der im Rahmen dieses Updates (Betriebssystembuild 17784.1226) bereitgestellten Dateien erhalten Sie durch Herunterladen der [Dateiinformationen für das kumulative Update 4577470](https://download.microsoft.com/download/3/c/4/3c468525-5867-4cc3-8d34-dba88989adab/4577470.csv).

   > [!NOTE]
   > Einige Dateien sind in der Dateiversionsspalte der CSV-Datei fälschlicherweise mit „Not applicable“ (Nicht zutreffend) gekennzeichnet. Dies kann bei der Überprüfung des Builds durch ein Drittanbietertool zu falsch positiven oder falsch negativen Ergebnissen führen.

## <a name="september-8-2020-servicing-stack-update-kb4577558"></a>8\. September 2020, Wartungsstapelaktualisierung (KB4577558)

Dieses Update enthält Qualitätsverbesserungen für die Public Preview-Version von Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Verbesserungen und Fehlerbehebungen
Dieses Update verbessert die Qualität des Wartungsstapels, der Komponente zum Installieren von Updates. Wartungsstapelaktualisierungen sorgen für einen stabilen und zuverlässigen Wartungsstapel, damit Ihre Geräte Microsoft-Updates empfangen und installieren können.

### <a name="how-to-get-this-update"></a>Beziehen dieses Updates
Die Wartungsstapelaktualisierung vom 8. September 2020 (KB4577558) für [Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) wird über Windows Update bereitgestellt. Informationen zur Installation in Ihrem Azure Stack HCI-Cluster finden Sie unter [Aktualisieren von Azure Stack HCI-Clustern](manage/update-cluster.md).

### <a name="restart-information"></a>Informationen zum Neustart 
Sie müssen den Computer nach Anwenden dieses Updates nicht neu starten.

### <a name="removal-information"></a>Informationen zum Entfernen
Wartungsstapelaktualisierungen nehmen Änderungen an der Installation von Updates vor und können nicht vom Gerät deinstalliert werden.

### <a name="file-information"></a>Dateiinformationen
Eine Liste der im Rahmen dieses Updates (Betriebssystembuild 17784.1220) bereitgestellten Dateien erhalten Sie durch Herunterladen der [Dateiinformationen für das kumulative Update 4577558](https://download.microsoft.com/download/8/f/6/8f612a9b-cb4e-4832-9397-156760848592/4577558.csv).

### <a name="references"></a>References

Informationen zu Wartungsstapelaktualisierungen finden Sie in den folgenden Artikeln:

- [Wartungsstapelaktualisierungen](/windows/deployment/update/servicing-stack-updates)
- [Wartungsstapelaktualisierungen: häufig gestellte Fragen](https://support.microsoft.com/help/4535697)

Erfahren Sie mehr über die [Terminologie](https://support.microsoft.com/help/824684), die Microsoft zum Beschreiben von Softwareupdates verwendet.

## <a name="august-11-2020-security-update-kb4574585"></a>Sicherheitsupdate vom 11. August 2020 (KB4574585)

Dieses Update enthält Verbesserungen und Fehlerbehebungen für die Public Preview-Version von Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Verbesserungen und Fehlerbehebungen
Dieses Update enthält verschiedene Sicherheitsverbesserungen für interne Betriebssystemfunktionen. Für dieses Release wurden keine weiteren Probleme dokumentiert.

Weitere Informationen zu den beseitigten Sicherheitsrisiken finden Sie im [Leitfaden für Sicherheitsupdates](https://portal.msrc.microsoft.com/security-guidance).

### <a name="known-issues-in-this-update"></a>Bekannte Probleme in diesem Update
Microsoft sind aktuell keine Probleme mit diesem Update bekannt.

### <a name="how-to-get-this-update"></a>Beziehen dieses Updates
Das Sicherheitsupdate vom 11. August 2020 (KB4574585) für [Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) wird über Windows Update bereitgestellt. Informationen zur Installation in Ihrem Azure Stack HCI-Cluster finden Sie unter [Aktualisieren von Azure Stack HCI-Clustern](manage/update-cluster.md).

### <a name="file-information"></a>Dateiinformationen
Eine Liste der im Rahmen dieses Updates (Betriebssystembuild 17784.1162) bereitgestellten Dateien erhalten Sie durch Herunterladen der [Dateiinformationen für das kumulative Update 4574585](https://download.microsoft.com/download/7/f/4/7f451def-76c5-4cc0-9929-0c5efeb27d2f/4574585.csv).

   > [!NOTE]
   > Einige Dateien sind in der Dateiversionsspalte der CSV-Datei fälschlicherweise mit „Not applicable“ (Nicht zutreffend) gekennzeichnet. Dies kann bei der Überprüfung des Builds durch ein Drittanbietertool zu falsch positiven oder falsch negativen Ergebnissen führen.
