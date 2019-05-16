---
title: Verwenden von bewährten SQL Server-Methoden zur Leistungssteigerung in virtuellen Azure Stack-Computern | Microsoft-Dokumentation
description: Dieser Artikel enthält bewährte Methoden für SQL Server, um die Leistung zu steigern und SQL-Server in Azure Stack-VMs zu optimieren.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 77e7e994e382e5e359ba133ccc9e5a9b35a74153
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985580"
---
# <a name="sql-server-best-practices-to-optimize-performance-in-azure-stack"></a>Bewährte SQL Server-Methoden zum Optimieren der Leistung in Azure Stack

Dieser Artikel enthält bewährte SQL Server-Methoden zur Optimierung von SQL Server und zur Leistungsverbesserung in virtuellen Microsoft Azure Stack-Computern. Verwenden Sie beim Ausführen von SQL Server auf virtuellen Azure Stack-Computern die gleichen Optionen zur Optimierung der Datenbankleistung, die für SQL Server in einer lokalen Serverumgebung gelten. Die Leistung einer relationalen Datenbank in einer Azure Stack-Cloud hängt von vielen Faktoren ab. Dazu gehören die Größe der Familie eines virtuellen Computers und die Konfiguration der Datenträger.

Wenn Sie SQL Server-Images erstellen, [empfiehlt sich die Bereitstellung der virtuellen Computer im Azure Stack-Portal](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). Laden Sie im Azure Stack-Administratorportal die SQL-IaaS-Erweiterung unter „Marketplace Management“ (Marketplace-Verwaltung) und Ihre Auswahl an virtuellen Festplatten (VHDs) für virtuelle SQL-Computer herunter. Dazu gehören SQL2014SP2, SQL2016SP1 und SQL2017.

> [!NOTE]  
> Während im Artikel die Bereitstellung eines virtuellen SQL Server-Computers mit dem globalen Azure-Portal beschrieben wird, gelten die Anweisungen auch für Azure Stack mit folgenden Unterschieden: SSD ist nicht verfügbar für den Betriebssystemdatenträger, verwaltete Datenträger sind nicht verfügbar, und es gibt kleinere Unterschiede bei der Speicherkonfiguration.

Schwerpunkt dieses Artikels ist die *bestmögliche* Leistung für SQL Server auf virtuellen Azure Stack-Computern. Wenn Ihre Workload weniger anspruchsvoll ist, sind möglicherweise nicht alle empfohlenen Optimierungen erforderlich. Berücksichtigen Sie bei der Evaluierung dieser Empfehlungen Ihre Leistungsanforderungen und Workloadmuster.

> [!NOTE]  
> Einen Leitfaden zur Leistung für SQL Server auf virtuellen Azure-Computern finden Sie in [diesem Artikel](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).

## <a name="checklist-for-sql-server-best-practices"></a>Checkliste für bewährte SQL Server-Methoden

Hier eine Prüfliste für die optimale Leistung von SQL Server auf virtuellen Azure Stack-Computern:


|Bereich|Optimierungen|
|-----|-----|
|Größe des virtuellen Computers |[DS3](azure-stack-vm-sizes.md) oder höher für SQL Server Enterprise Edition<br><br>[DS2](azure-stack-vm-sizes.md) oder höher für SQL Server Standard Edition und Web Edition|
|Storage |Verwenden Sie eine Familie virtueller Computer, die [Storage Premium](azure-stack-acs-differences.md) unterstützt.|
|Datenträger |Verwenden Sie mindestens zwei Datenträger (einen für Protokolldateien und einen für Datendateien und TempDB), und wählen Sie die Größe der Datenträger entsprechend Ihren Kapazitätsanforderungen aus. Legen Sie die standardmäßigen Datendateispeicherorte für diese Datenträger während der Installation von SQL Server fest.<br><br>Vermeiden Sie die Verwendung von Betriebssystem- oder temporären Datenträgern für die Datenbankspeicherung oder Protokollierung.<br>Erstellen Sie unter Verwendung von Speicherplätzen ein Stripeset mehrerer Azure-Datenträger, um einen höheren E/A-Durchsatz zu erzielen.<br><br>Formatieren Sie mit dokumentierten Zuordnungsgrößen.|
|E/A|Aktivieren Sie die sofortige Dateiinitialisierung für Datendateien.<br><br>Begrenzen Sie die automatische Vergrößerung für die Datenbanken in angemessen kleinen festen Schritten (64 MB bis 256 MB).<br><br>Deaktivieren Sie die automatische Verkleinerung der Datenbank.<br><br>Richten Sie die standardmäßigen Speicherorte für Sicherungsdateien und Datenbankdateien auf Datenträgern und nicht auf dem Betriebssystemdatenträger ein.<br><br>Aktivieren Sie gesperrte Seiten.<br><br>Wenden Sie SQL Server Service Packs und kumulative Updates an.|
|Featurespezifisch|Führen Sie Sicherungen direkt in Blob Storage durch (sofern dies von der verwendeten SQL Server-Version unterstützt wird).|
|||

Weitere Informationen dazu, *wie* und *warum* diese Optimierungen vorgenommen werden, finden Sie in den Details und den Leitfäden in den folgenden Abschnitten.

## <a name="virtual-machine-size-guidance"></a>Leitfaden zur VM-Größe

Für leistungsabhängige Anwendungen empfiehlt sich die Verwendung der folgenden [Größen für virtuelle Computer](azure-stack-vm-sizes.md):

- **SQL Server Enterprise Edition**: DS3 oder höher

- **SQL Server Standard Edition und Web Edition:** DS2 oder höher

In Azure Stack besteht kein Unterschied bei der Leistung zwischen den Familien der virtuellen Computer der Serien DS und DS_v2.

## <a name="storage-guidance"></a>Leitfaden für Speicher

Virtuelle Computer der Serie DS (und der Serie DSv2) in Azure Stack bieten den maximalen Durchsatz (IOPS) für Betriebssystemdatenträger und Datenträger für Daten. Ein virtueller Computer der Serie DS oder DSv2 bietet bis zu 1.000 IOPS für den Betriebssystemdatenträger und bis zu 2.300 IOPS pro Datenträger, unabhängig vom Typ oder der Größe des ausgewählten Datenträgers.

Der Durchsatz der Datenträger wird ausschließlich basierend auf der Serie der Familie der virtuellen Computer bestimmt. Informationen zum Ermitteln des Durchsatzes der Datenträger pro Serie der Familie der virtuellen Computer [finden Sie in diesem Artikel](azure-stack-vm-sizes.md).

> [!NOTE]  
> Wählen Sie für Produktionsworkloads einen virtuellen Computer der Serie DS oder DSv2 aus, um die maximal möglichen IOPS auf dem Betriebssystemdatenträger und den Datenträgern für Daten bereitzustellen.

Wenn Sie ein Speicherkonto in Azure Stack erstellen, hat die Option für die Georeplikation keine Auswirkungen, da diese Funktion in Azure Stack nicht verfügbar ist.

## <a name="disks-guidance"></a>Leitfaden für Datenträger

Es gibt drei Haupttypen von Datenträgern auf einem virtuellen Azure Stack-Computer:

- **Betriebssystemdatenträger:** Wenn Sie einen virtuellen Azure Stack-Computer erstellen, fügt die Plattform mindestens einen Datenträger (mit der Bezeichnung Laufwerk **C:**) als Betriebssystem-Datenträger zum virtuellen Computer hinzu. Bei diesem Datenträger handelt es sich um eine VHD, die als Seitenblob gespeichert wird.

- **Temporärer Datenträger:** Virtuelle Azure Stack-Computer enthalten einen weiteren Datenträger, der als temporärer Datenträger bezeichnet wird (Laufwerk **D:**). Dies ist ein Datenträger für temporären Speicherbereich auf dem Knoten.

- **Datenträger für Daten:** Sie können dem virtuellen Computer weitere Datenträger für Daten hinzufügen. Diese werden als Seitenblobs im Speicher gespeichert.

In den folgenden Abschnitten werden Empfehlungen zur Verwendung dieser unterschiedlichen Datenträger erläutert.

### <a name="operating-system-disk"></a>Betriebssystem-Datenträger

Ein Betriebssystem-Datenträger ist eine virtuelle Festplatte, die Sie als aktive Version eines Betriebssystems starten und einbinden können, und die als Laufwerk **C** bezeichnet ist.

### <a name="temporary-disk"></a>Temporärer Datenträger

Das als Laufwerk **D:** bezeichnete temporäre Speicherlaufwerk wird nicht beibehalten. Speichern Sie auf Laufwerk **D:** keine Daten, die nicht verloren gehen sollen, z.B. die Datenbankdateien oder Transaktionsprotokolldateien für Ihre Benutzer.

Es empfiehlt sich, TempDB auf einem Datenträger für Daten zu speichern, da jeder Datenträger maximal bis zu 2.300 IOPS pro Datenträger bereitstellt.

### <a name="data-disks"></a>Datenträger

- **Verwenden von Datenträgern für Daten- und Protokolldateien:** Wenn Sie kein Datenträgerstriping verwenden, sollten Sie zwei Datenträger eines virtuellen Computers verwenden, der Storage Premium unterstützt, wobei sich auf einem Datenträger die Protokolldateien und auf dem anderen die Daten- und TempDB-Dateien befinden. Jeder Datenträger bietet abhängig von der Familie der virtuellen Computer bestimmte IOPS- und Bandbreitenwerte (MBit/s), wie unter [In Azure Stack unterstützte VM-Größen](azure-stack-vm-sizes.md) beschrieben. Wenn Sie eine Datenträgerstriping-Technik (beispielsweise Speicherplätze) verwenden, platzieren Sie alle Daten- und Protokolldateien auf demselben Laufwerk (einschließlich TempDB). Diese Konfiguration ermöglicht die für SQL Server maximal verfügbare Anzahl von IOPS, unabhängig davon, welche Datei diese zu einem bestimmten Zeitpunkt nutzt.

> [!NOTE]  
> Beim Bereitstellen eines virtuellen SQL Server-Computers im Portal haben Sie die Möglichkeit, Ihre Speicherkonfiguration zu bearbeiten. Je nach Konfiguration werden in Azure Stack ein oder mehrere Datenträger konfiguriert. Mehrere Datenträger werden in einem einzelnen Speicherpool zusammengefasst. Daten- und Protokolldateien befinden sich in dieser Konfiguration zusammen.

- **Datenträgerstriping:** Für einen höheren Durchsatz können Sie zusätzliche Datenträger für Daten hinzufügen und Datenträgerstriping verwenden. Um die Anzahl der Datenträger zu ermitteln, müssen Sie die Anzahl der IOPS und die Bandbreite analysieren, die für die Protokolldateien und für Ihre Daten und die TempDB-Dateien erforderlich sind. Beachten Sie, dass IOPS-Limits pro Datenträger basierend auf der Serie der Familie der virtuellen Computer und nicht basierend auf der Größe der virtuellen Computer gelten. Limits für die Netzwerkbandbreite basieren dagegen auf der Größe der virtuellen Computer. Weitere Details finden Sie in den Tabellen unter [In Azure Stack unterstützte VM-Größen](azure-stack-vm-sizes.md). Verwenden Sie die folgenden Richtlinien:

  - Verwenden Sie [Speicherplätze](https://technet.microsoft.com/library/hh831739.aspx) für Windows 2012 und höher entsprechend den folgenden Richtlinien:

    1. Legen Sie die Überlappung (Stripesetgröße) für OLTP-Workloads (Online Transaction Processing) auf 64 KB (65.536 Bytes) und für Data Warehousing-Workloads auf 256 KB (262.144 Bytes) fest, um Leistungseinbußen durch falsche Partitionsausrichtung zu vermeiden. Dies muss mit PowerShell festgelegt werden.

    2. Festgelegte Anzahl der Spalten = Anzahl der physischen Datenträger. Verwenden Sie PowerShell, wenn Sie mehr als acht Datenträger (keine Server-Manager-Benutzeroberfläche) konfigurieren.

       PowerShell erstellt z.B. wie folgt einen neuen Speicherpool mit der auf 64 KB festgelegten Überlappungsgröße und der auf 2 festgelegten Anzahl der Spalten:

       ```powershell  
       $PoolCount = Get-PhysicalDisk -CanPool $True
       $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

       New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple -UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false
       ```

- Bestimmen Sie auf Basis der erwarteten Auslastung die Anzahl der jedem Speicherpool zugeordneten Datenträger. Bedenken Sie, dass verschiedene VM-Größen unterschiedlich viele angefügte Datenträger für Daten unterstützen. Weitere Informationen finden Sie unter [In Azure Stack unterstützte VM-Größen](azure-stack-vm-sizes.md).
- Um die maximal möglichen IOPS für Datenträger zu erzielen, wird empfohlen, die maximale Anzahl von Datenträgern hinzuzufügen, die durch die [VM-Größe](azure-stack-vm-sizes.md) unterstützt werden, und Datenträgerstriping zu verwenden.
- **Größe der NTFS-Zuordnungseinheiten:** Beim Formatieren des Datenträgers für Daten empfiehlt es sich, als Größe der Zuordnungseinheiten für Daten- und Protokolldateien sowie für TempDB einen Wert von 64 KB zu verwenden.
- **Methoden für die Datenträgerverwaltung:** Beenden Sie den SQL Server-Dienst, wenn Sie einen Datenträger für Daten entfernen. Ändern Sie zudem nicht die Cacheeinstellungen auf den Datenträgern, da dadurch keine Leistungssteigerungen erzielt werden.

> [!WARNING]  
> Fehler beim Beenden des SQL Server-Diensts während dieser Vorgänge können zur Beschädigung der Datenbank führen.

## <a name="io-guidance"></a>E/A-Leitfaden

- Erwägen Sie die Aktivierung der sofortigen Dateiinitialisierung, um die für die anfängliche Dateizuordnung erforderliche Zeit zu verringern. Um die sofortige Dateiinitialisierung nutzen zu können, gewähren Sie dem SQL Server-Dienstkonto (MSSQLSERVER) die Berechtigung **SE_MANAGE_VOLUME_NAME**, und fügen Sie sie der Sicherheitsrichtlinie **Durchführen von Volumewartungsaufgaben** hinzu. Wenn Sie ein SQL Server-Plattformimage für Azure verwenden, wird das Standarddienstkonto (**NT Service\MSSQLSERVER**) nicht der Sicherheitsrichtlinie **Durchführen von Volumewartungsaufgaben** hinzugefügt. Anders ausgedrückt: Die sofortige Dateiinitialisierung wird in einem SQL Server Azure-Plattformimages nicht aktiviert. Starten Sie den SQL Server-Dienst neu, nachdem Sie das SQL Server-Dienstkonto der Sicherheitsrichtlinie **Durchführen von Volumewartungsaufgaben** hinzugefügt haben. Möglicherweise sind bei Verwendung dieses Features bestimmte Sicherheitsaspekte zu berücksichtigen. Weitere Informationen finden Sie unter [Datenbankdatei-Initialisierung](https://msdn.microsoft.com/library/ms175935.aspx).
- **Automatische Vergrößerung** ist eine Behelfslösung für unerwartetes Wachstum. Führen Sie die tägliche Verwaltung Ihres Daten- und Protokolldateiwachstums nicht durch automatische Vergrößerung. Wenn die automatische Vergrößerung verwendet wird, vergrößern Sie die Datei vorab mit dem Switch **Größe**.
- Stellen Sie sicher, dass **Automatische Verkleinerung** deaktiviert ist, um unnötigen Mehraufwand zu vermeiden, der sich negativ auf die Leistung auswirken kann.
- Richten Sie standardmäßige Dateispeicherorte für Sicherungen und Datenbanken ein. Verwenden Sie die Empfehlungen in diesem Artikel, und nehmen Sie die Änderungen im Fenster mit den Servereigenschaften vor. Eine Anleitung finden Sie unter [Anzeigen oder Ändern der Standardspeicherorte für Daten- und Protokolldateien (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). Im folgenden Screenshot ist dargestellt, wo diese Änderungen vorgenommen werden:

    > ![Anzeigen oder Ändern der Standardspeicherorte](./media/sql-server-vm-considerations/image1.png)

- Aktivieren Sie gesperrte Seiten, um E/A- und Auslagerungsaktivitäten zu verringern. Weitere Informationen finden Sie unter [Aktivieren der Option zum Sperren von Seiten im Speicher (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

- Es kann auch sinnvoll sein, alle Datendateien (einschließlich Sicherungen) beim Übertragen in bzw. aus Azure Stack zu komprimieren.

## <a name="feature-specific-guidance"></a>Featurespezifische Anleitungen

Für manche Bereitstellungen können durch Verwendung erweiterter Konfigurationsmethoden zusätzliche Leistungsvorteile erzielt werden. In der folgenden Liste sind einige SQL Server-Features zusammengestellt, mit denen Sie eine bessere Leistung erzielen können:

- **Sicherung in Azure** **Storage:** Wenn Sie Sicherungen für SQL Server durchführen, der auf virtuellen Azure Stack-Computern durchgeführt wird, können Sie die SQL Server-Sicherung über URLs verwenden. Dieses Feature steht ab SQL Server 2012 SP1 CU2 zur Verfügung und wird für Sicherungen auf die angefügten Datenträgern für Daten empfohlen.

    Befolgen Sie bei Sicherungen oder Wiederherstellungen mithilfe von Azure Storage die Empfehlungen unter [SQL Server-URL-Sicherung – bewährte Methoden und Problembehandlung](https://msdn.microsoft.com/library/jj919149.aspx) und [Wiederherstellen von in Microsoft Azure gespeicherten Sicherungen](https://docs.microsoft.com/sql/relational-databases/backup-restore/restoring-from-backups-stored-in-microsoft-azure?view=sql-server-2017). Darüber hinaus können Sie diese Sicherungen mit [automatisierten Sicherungen für SQL Server auf virtuellen Azure-Computern](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup)automatisieren.

-   **Sicherung im Azure Stack-Speicher:** Sie können Sicherungen im Azure Stack-Speicher auf ähnliche Weise durchführen wie Sicherungen in Azure Storage. Wenn Sie eine Sicherung in SQL Server Management Studio (SSMS) erstellen, müssen Sie die Konfigurationsinformationen manuell eingeben. SSMS können Sie nicht verwenden, um den Speichercontainer oder die Shared Access Signature zu erstellen. Mit SSMS kann nur eine Verbindung mit Azure-Abonnements, jedoch nicht mit Azure Stack-Abonnements hergestellt werden. Stattdessen müssen Sie das Speicherkonto, den Container und die Shared Access Signature im Azure Stack-Portal oder über PowerShell erstellen.

    Angeben der Informationen im Dialogfeld für die SQL Server-Sicherung:

    ![SQL Server-Sicherung](./media/sql-server-vm-considerations/image3.png)

    > [!NOTE]  
    > Die Shared Access Signature ist das SAS-Token aus dem Azure Stack-Portal ohne das führende „?“ in der Zeichenfolge. Wenn Sie die Kopierfunktion über das Portal verwenden, müssen Sie das führende „?“ löschen, damit das Token in SQL Server funktioniert.

    Nachdem Sie das Sicherungsziel in SQL Server eingerichtet und konfiguriert haben, können Sie Sicherungen im Azure Stack-Blobspeicher durchführen.

## <a name="next-steps"></a>Nächste Schritte

[Verwenden von Diensten oder Erstellen von Apps für Azure Stack](azure-stack-considerations.md)