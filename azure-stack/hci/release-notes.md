---
title: 'Azure Stack HCI: Versionshinweise'
description: Versionshinweise für Azure Stack HCI (Version 20H2)
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 11/10/2020
ms.openlocfilehash: eaa9417abd41828495d3cd685ecc1f56c42c92e1
ms.sourcegitcommit: 96bc36a203954622be411fdb038d601e49f97d4e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94441173"
---
# <a name="release-notes-for-azure-stack-hci-public-preview"></a>Versionshinweise für die Public Preview von Azure Stack HCI

> Gilt für: Azure Stack HCI, Version 20H2

In diesem Artikel werden die Inhalte der Updatepakete für die Public Preview von Azure Stack HCI beschrieben.

## <a name="november-10-2020-security-update-kb4586811"></a>10. November 2020, Sicherheitsupdate (KB4586811)

Dieses Update enthält Verbesserungen und Fehlerbehebungen für das neueste Release von Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Verbesserungen und Fehlerbehebungen
Dieses Update enthält verschiedene Sicherheitsverbesserungen für interne Betriebssystemfunktionen. Für dieses Release wurden keine weiteren Probleme dokumentiert.

Weitere Informationen zu den beseitigten Sicherheitsrisiken finden Sie im [Leitfaden für Sicherheitsupdates](https://portal.msrc.microsoft.com/security-guidance).

### <a name="known-issues-in-this-update"></a>Bekannte Probleme in diesem Update
Microsoft sind aktuell keine Probleme mit diesem Update bekannt.

### <a name="how-to-get-this-update"></a>Beziehen dieses Updates
Das Sicherheitsupdate vom 10. November 2020 (KB4586811) für [Azure Stack HCI (Vorschauversion)](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) wird über Windows Update bereitgestellt. Informationen zur Installation in Ihrem Azure Stack HCI-Cluster finden Sie unter [Aktualisieren von Azure Stack HCI-Clustern](manage/update-cluster.md).

### <a name="file-information"></a>Dateiinformationen
Eine Liste der im Rahmen dieses Updates (Betriebssystembuild 17784.1345) bereitgestellten Dateien erhalten Sie durch Herunterladen der [Dateiinformationen für das kumulative Update 4586811](https://download.microsoft.com/download/8/f/2/8f2ce4bb-e113-4abc-b3ff-f0f4c4c71403/4586811.csv).

   > [!NOTE]
   > Einige Dateien sind in der Dateiversionsspalte der CSV-Datei fälschlicherweise mit „Not applicable“ (Nicht zutreffend) gekennzeichnet. Dies kann bei der Überprüfung des Builds durch ein Drittanbietertool zu falsch positiven oder falsch negativen Ergebnissen führen.

## <a name="november-10-2020-servicing-stack-update-kb4590242"></a>10. November 2020, Wartungsstapelaktualisierung (KB4590242)

Dieses Update bietet Qualitätsverbesserungen für das neueste Release von Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Verbesserungen und Fehlerbehebungen
Dieses Update verbessert die Qualität des Wartungsstapels, der Komponente zum Installieren von Updates. Wartungsstapelaktualisierungen sorgen für einen stabilen und zuverlässigen Wartungsstapel, damit Ihre Geräte Microsoft-Updates empfangen und installieren können.

### <a name="how-to-get-this-update"></a>Beziehen dieses Updates
Das Sicherheitsupdate von November 2020 (KB4590242) für [Azure Stack HCI (Vorschauversion)](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) wird über Windows Update bereitgestellt. Informationen zur Installation in Ihrem Azure Stack HCI-Cluster finden Sie unter [Aktualisieren von Azure Stack HCI-Clustern](manage/update-cluster.md).

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

Dieses Update enthält Verbesserungen und Fehlerbehebungen für das neueste Release von Azure Stack HCI.

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
Das Sicherheitsupdate vom 20. Oktober 2020 (KB4580388) für [Azure Stack HCI (Vorschauversion)](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) wird über Windows Update bereitgestellt. Informationen zur Installation in Ihrem Azure Stack HCI-Cluster finden Sie unter [Aktualisieren von Azure Stack HCI-Clustern](manage/update-cluster.md).

### <a name="file-information"></a>Dateiinformationen
Eine Liste der im Rahmen dieses Updates (Betriebssystembuild 17784.1321) bereitgestellten Dateien erhalten Sie durch Herunterladen der [Dateiinformationen für das kumulative Update 4580388](https://download.microsoft.com/download/2/f/b/2fb766d3-c4c8-4279-8718-8efbd0b6f211/4580388.csv).

   > [!NOTE]
   > Einige Dateien sind in der Dateiversionsspalte der CSV-Datei fälschlicherweise mit „Not applicable“ (Nicht zutreffend) gekennzeichnet. Dies kann bei der Überprüfung des Builds durch ein Drittanbietertool zu falsch positiven oder falsch negativen Ergebnissen führen.

## <a name="october-13-2020-security-update-kb4580363"></a>13. Oktober 2020 Sicherheitsupdate (KB4580363)

Dieses Update enthält Verbesserungen und Fehlerbehebungen für das neueste Release von Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Verbesserungen und Fehlerbehebungen
Dieses Update enthält verschiedene Sicherheitsverbesserungen für interne Betriebssystemfunktionen. Für dieses Release wurden keine weiteren Probleme dokumentiert.

Weitere Informationen zu den beseitigten Sicherheitsrisiken finden Sie im [Leitfaden für Sicherheitsupdates](https://portal.msrc.microsoft.com/security-guidance).

### <a name="known-issues-in-this-update"></a>Bekannte Probleme in diesem Update
Microsoft sind aktuell keine Probleme mit diesem Update bekannt.

### <a name="how-to-get-this-update"></a>Beziehen dieses Updates
Das Sicherheitsupdate vom 13. Oktober 2020 (KB4580363) für [Azure Stack HCI (Vorschauversion)](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) wird über Windows Update bereitgestellt. Informationen zur Installation in Ihrem Azure Stack HCI-Cluster finden Sie unter [Aktualisieren von Azure Stack HCI-Clustern](manage/update-cluster.md).

### <a name="file-information"></a>Dateiinformationen
Eine Liste der im Rahmen dieses Updates (Betriebssystembuild 17784.1288) bereitgestellten Dateien erhalten Sie durch Herunterladen der [Dateiinformationen für das kumulative Update 4580363](https://download.microsoft.com/download/f/7/8/f78801f0-e7e5-4a3d-a971-f642ccd24ee4/4580363.csv).

   > [!NOTE]
   > Einige Dateien sind in der Dateiversionsspalte der CSV-Datei fälschlicherweise mit „Not applicable“ (Nicht zutreffend) gekennzeichnet. Dies kann bei der Überprüfung des Builds durch ein Drittanbietertool zu falsch positiven oder falsch negativen Ergebnissen führen.

## <a name="october-13-2020-servicing-stack-update-kb4583287"></a>13. Oktober 2020, Wartungsstapelaktualisierung (KB4583287)

Dieses Update bietet Qualitätsverbesserungen für das neueste Release von Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Verbesserungen und Fehlerbehebungen
Dieses Update verbessert die Qualität des Wartungsstapels, der Komponente zum Installieren von Updates. Wartungsstapelaktualisierungen sorgen für einen stabilen und zuverlässigen Wartungsstapel, damit Ihre Geräte Microsoft-Updates empfangen und installieren können.

### <a name="how-to-get-this-update"></a>Beziehen dieses Updates
Das Sicherheitsupdate vom 13. Oktober 2020 (KB4583287) für [Azure Stack HCI (Vorschauversion)](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) wird über Windows Update bereitgestellt. Informationen zur Installation in Ihrem Azure Stack HCI-Cluster finden Sie unter [Aktualisieren von Azure Stack HCI-Clustern](manage/update-cluster.md).

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

Dieses Update enthält Verbesserungen und Fehlerbehebungen für das neueste Release von Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Verbesserungen und Fehlerbehebungen
Dieses Nicht-Sicherheitsupdate umfasst Qualitätsverbesserungen. Zu den wichtigsten Änderungen gehören:
- Es wurde ein Problem behandelt, bei dem der Datenverkehr des Softwarelastenausgleichs, der den Multiplexer durchläuft, möglicherweise auf einen anderen Host umgeleitet wird, was zu einem Verbindungsfehler der Anwendung führen kann.

### <a name="known-issues-in-this-update"></a>Bekannte Probleme in diesem Update
Microsoft sind aktuell keine Probleme mit diesem Update bekannt.

### <a name="how-to-get-this-update"></a>Beziehen dieses Updates
Das Sicherheitsupdate vom 17. September 2020 (KB4577629) für [Azure Stack HCI (Vorschauversion)](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) wird über Windows Update bereitgestellt. Informationen zur Installation in Ihrem Azure Stack HCI-Cluster finden Sie unter [Aktualisieren von Azure Stack HCI-Clustern](manage/update-cluster.md).

### <a name="file-information"></a>Dateiinformationen
Eine Liste der im Rahmen dieses Updates (Betriebssystembuild 17784.1259) bereitgestellten Dateien erhalten Sie durch Herunterladen der [Dateiinformationen für das kumulative Update 4577629](https://download.microsoft.com/download/9/1/a/91addcbb-2b36-408c-ab88-736de42edb98/4577629.csv).

   > [!NOTE]
   > Einige Dateien sind in der Dateiversionsspalte der CSV-Datei fälschlicherweise mit „Not applicable“ (Nicht zutreffend) gekennzeichnet. Dies kann bei der Überprüfung des Builds durch ein Drittanbietertool zu falsch positiven oder falsch negativen Ergebnissen führen.

## <a name="september-8-2020-security-update-kb4577470"></a>8\. September 2020, Sicherheitsupdate (KB4577470)

Dieses Update enthält Verbesserungen und Fehlerbehebungen für das neueste Release von Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Verbesserungen und Fehlerbehebungen
Dieses Update enthält verschiedene Sicherheitsverbesserungen für interne Betriebssystemfunktionen. Für dieses Release wurden keine weiteren Probleme dokumentiert.

Weitere Informationen zu den beseitigten Sicherheitsrisiken finden Sie im [Leitfaden für Sicherheitsupdates](https://portal.msrc.microsoft.com/security-guidance).

### <a name="known-issues-in-this-update"></a>Bekannte Probleme in diesem Update
Microsoft sind aktuell keine Probleme mit diesem Update bekannt.

### <a name="how-to-get-this-update"></a>Beziehen dieses Updates
Das Sicherheitsupdate vom 8. September 2020 (KB4577470) für [Azure Stack HCI (Vorschauversion)](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) wird über Windows Update bereitgestellt. Informationen zur Installation in Ihrem Azure Stack HCI-Cluster finden Sie unter [Aktualisieren von Azure Stack HCI-Clustern](manage/update-cluster.md).

### <a name="file-information"></a>Dateiinformationen
Eine Liste der im Rahmen dieses Updates (Betriebssystembuild 17784.1226) bereitgestellten Dateien erhalten Sie durch Herunterladen der [Dateiinformationen für das kumulative Update 4577470](https://download.microsoft.com/download/3/c/4/3c468525-5867-4cc3-8d34-dba88989adab/4577470.csv).

   > [!NOTE]
   > Einige Dateien sind in der Dateiversionsspalte der CSV-Datei fälschlicherweise mit „Not applicable“ (Nicht zutreffend) gekennzeichnet. Dies kann bei der Überprüfung des Builds durch ein Drittanbietertool zu falsch positiven oder falsch negativen Ergebnissen führen.

## <a name="september-8-2020-servicing-stack-update-kb4577558"></a>8\. September 2020, Wartungsstapelaktualisierung (KB4577558)

Dieses Update bietet Qualitätsverbesserungen für das neueste Release von Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Verbesserungen und Fehlerbehebungen
Dieses Update verbessert die Qualität des Wartungsstapels, der Komponente zum Installieren von Updates. Wartungsstapelaktualisierungen sorgen für einen stabilen und zuverlässigen Wartungsstapel, damit Ihre Geräte Microsoft-Updates empfangen und installieren können.

### <a name="how-to-get-this-update"></a>Beziehen dieses Updates
Das Sicherheitsupdate vom 8. September 2020 (KB4577558) für [Azure Stack HCI (Vorschauversion)](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) wird über Windows Update bereitgestellt. Informationen zur Installation in Ihrem Azure Stack HCI-Cluster finden Sie unter [Aktualisieren von Azure Stack HCI-Clustern](manage/update-cluster.md).

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

Dieses Update enthält Verbesserungen und Fehlerbehebungen für das neueste Release von Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Verbesserungen und Fehlerbehebungen
Dieses Update enthält verschiedene Sicherheitsverbesserungen für interne Betriebssystemfunktionen. Für dieses Release wurden keine weiteren Probleme dokumentiert.

Weitere Informationen zu den beseitigten Sicherheitsrisiken finden Sie im [Leitfaden für Sicherheitsupdates](https://portal.msrc.microsoft.com/security-guidance).

### <a name="known-issues-in-this-update"></a>Bekannte Probleme in diesem Update
Microsoft sind aktuell keine Probleme mit diesem Update bekannt.

### <a name="how-to-get-this-update"></a>Beziehen dieses Updates
Das Sicherheitsupdate vom 11. August 2020 (KB4574585) für [Azure Stack HCI (Vorschauversion)](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) wird über Windows Update bereitgestellt. Informationen zur Installation in Ihrem Azure Stack HCI-Cluster finden Sie unter [Aktualisieren von Azure Stack HCI-Clustern](manage/update-cluster.md).

### <a name="file-information"></a>Dateiinformationen
Eine Liste der im Rahmen dieses Updates (Betriebssystembuild 17784.1162) bereitgestellten Dateien erhalten Sie durch Herunterladen der [Dateiinformationen für das kumulative Update 4574585](https://download.microsoft.com/download/7/f/4/7f451def-76c5-4cc0-9929-0c5efeb27d2f/4574585.csv).

   > [!NOTE]
   > Einige Dateien sind in der Dateiversionsspalte der CSV-Datei fälschlicherweise mit „Not applicable“ (Nicht zutreffend) gekennzeichnet. Dies kann bei der Überprüfung des Builds durch ein Drittanbietertool zu falsch positiven oder falsch negativen Ergebnissen führen.