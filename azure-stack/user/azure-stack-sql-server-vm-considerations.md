---
title: Bewährte SQL Server-Methoden zum Optimieren der Leistung in Azure Stack Hub | Microsoft-Dokumentation
description: Dieser Artikel enthält bewährte Methoden für SQL Server, um die Leistung zu steigern und SQL-Server in Azure Stack Hub-VMs zu optimieren.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 63fbc121aea2fe83b2ffda860f505fe069a4a5f1
ms.sourcegitcommit: d62400454b583249ba5074a5fc375ace0999c412
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/15/2020
ms.locfileid: "76023063"
---
# <a name="sql-server-best-practices-to-optimize-performance-in-azure-stack-hub"></a>Bewährte SQL Server-Methoden zum Optimieren der Leistung in Azure Stack Hub

Dieser Artikel enthält bewährte SQL Server-Methoden zur Optimierung von SQL Server und zur Leistungsverbesserung in virtuellen Microsoft Azure Stack Hub-Computern (VMs). Verwenden Sie beim Ausführen von SQL Server auf Azure Stack Hub-VMs die gleichen Optionen zur Optimierung der Datenbankleistung, die für SQL Server in einer lokalen Serverumgebung gelten. Die Leistung einer relationalen Datenbank in einer Azure Stack Hub-Cloud hängt von vielen Faktoren ab, z. B. der Größe der Familie einer VM und der Konfiguration der Datenträger.

Wenn Sie SQL Server-Images erstellen, [empfiehlt sich die Bereitstellung der virtuellen Computer im Azure Stack Hub-Portal](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). Laden Sie im Azure Stack Hub-Administratorportal die SQL-IaaS-Erweiterung unter „Marketplace Management“ (Marketplace-Verwaltung) sowie die gewünschten SQL Server-VM-Images herunter. Zur Auswahl stehen SQL Server 2016 SP1, SQL Server 2016 SP2 und SQL Server 2017.

> [!NOTE]  
> Im Artikel wird zwar die Bereitstellung einer SQL Server-VM mit dem globalen Azure-Portal beschrieben, aber die Anweisungen gelten auch für Azure Stack Hub. Es sollten nur die folgenden Unterschiede beachtet werden: Für den Betriebssystem-Datenträger ist keine SSD verfügbar, und es gibt kleinere Unterschiede bei der Speicherkonfiguration.

In den VM-Images können Sie für SQL Server nur BYOL (Bring-Your-Own-License, Verwendung einer eigenen Lizenz) verwenden. Für Windows Server ist das Standardlizenzmodell PAYG (Pay-as-you-go, nutzungsbasierte Bezahlung). Ausführliche Informationen zum Windows Server-Lizenzmodell in VM finden Sie im Artikel [Windows Server in Azure Stack Hub-Marketplace-FAQ](https://docs.microsoft.com/azure-stack/operator/azure-stack-windows-server-faq#what-about-other-vms-that-use-windows-server-such-as-sql-or-machine-learning-server).  

Schwerpunkt dieses Artikels ist die *bestmögliche* Leistung für SQL Server auf Azure Stack Hub-VMs. Wenn Ihre Workload weniger anspruchsvoll ist, sind möglicherweise nicht alle empfohlenen Optimierungen erforderlich. Berücksichtigen Sie bei der Evaluierung dieser Empfehlungen Ihre Leistungsanforderungen und Workloadmuster.

> [!NOTE]  
> Einen Leitfaden zur Leistung für SQL Server auf Azure-VMs finden Sie in [diesem Artikel](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).

## <a name="checklist-for-sql-server-best-practices"></a>Checkliste für bewährte SQL Server-Methoden

Hier ist eine Prüfliste für die optimale Leistung von SQL Server auf Azure Stack Hub-VMs angegeben:


|Bereich|Optimierungen|
|-----|-----|
|Größe des virtuellen Computers |[DS3](azure-stack-vm-sizes.md) oder höher für SQL Server Enterprise Edition<br><br>[DS2](azure-stack-vm-sizes.md) oder höher für SQL Server Standard Edition und Web Edition|
|Storage |Verwenden Sie eine VM-Familie, die [Storage Premium](azure-stack-acs-differences.md) unterstützt.|
|Datenträger |Verwenden Sie mindestens zwei Datenträger (einen für Protokolldateien und einen für Datendateien und TempDB), und wählen Sie die Größe der Datenträger entsprechend Ihren Kapazitätsanforderungen aus. Legen Sie die standardmäßigen Datendateispeicherorte für diese Datenträger während der Installation von SQL Server fest.<br><br>Vermeiden Sie die Verwendung von Betriebssystem- oder temporären Datenträgern für die Datenbankspeicherung oder Protokollierung.<br>Erstellen Sie unter Verwendung von Speicherplätzen ein Stripeset mehrerer Azure-Datenträger, um einen höheren E/A-Durchsatz zu erzielen.<br><br>Formatieren Sie mit dokumentierten Zuordnungsgrößen.|
|E/A|Aktivieren Sie die sofortige Dateiinitialisierung für Datendateien.<br><br>Begrenzen Sie die automatische Vergrößerung für die Datenbanken in angemessen kleinen festen Schritten (64 MB bis 256 MB).<br><br>Deaktivieren Sie die automatische Verkleinerung der Datenbank.<br><br>Richten Sie die standardmäßigen Speicherorte für Sicherungsdateien und Datenbankdateien auf Datenträgern und nicht auf dem Betriebssystemdatenträger ein.<br><br>Aktivieren Sie gesperrte Seiten.<br><br>Wenden Sie SQL Server Service Packs und kumulative Updates an.|
|Featurespezifisch|Führen Sie Sicherungen direkt in Blob Storage durch (sofern dies von der verwendeten SQL Server-Version unterstützt wird).|
|||

Weitere Informationen dazu, *wie* und *warum* diese Optimierungen vorgenommen werden, finden Sie in den Details und Leitfäden in den folgenden Abschnitten.

## <a name="vm-size-guidance"></a>Leitfaden für die Größe virtueller Computer

Für leistungsabhängige Anwendungen empfiehlt sich die Verwendung der folgenden [VM-Größen](azure-stack-vm-sizes.md):

- **SQL Server Enterprise Edition**: DS3 oder höher

- **SQL Server Standard Edition und Web Edition:** DS2 oder höher

In Azure Stack Hub besteht bei der Leistung kein Unterschied zwischen den VM-Familien DS und DS_v2.

## <a name="storage-guidance"></a>Leitfaden für Speicher

VMs der Serie DS (und der Serie DSv2) in Azure Stack Hub bieten den maximalen Durchsatz (IOPS) für Betriebssystem-Datenträger und Datenträger für Daten. Eine VM der Serie DS oder DSv2 bietet bis zu 1.000 IOPS für den Betriebssystem-Datenträger und bis zu 2.300 IOPS pro Datenträger für Daten – unabhängig vom Typ oder der Größe des ausgewählten Datenträgers.

Der Durchsatz der Datenträger wird ausschließlich anhand der Serie der VM-Familie bestimmt. Informationen zum Ermitteln des Durchsatzes der Datenträger pro Serie der VM-Familie finden Sie in [diesem Artikel](azure-stack-vm-sizes.md).

> [!NOTE]  
> Wählen Sie für Produktionsworkloads eine VM der Serie DS oder DSv2 aus, um die maximal möglichen IOPS auf dem Betriebssystem-Datenträger und den Datenträgern für Daten bereitzustellen.

Wenn Sie ein Speicherkonto in Azure Stack Hub erstellen, hat die Option für die Georeplikation keine Auswirkungen, da diese Funktion in Azure Stack Hub nicht verfügbar ist.

## <a name="disks-guidance"></a>Leitfaden für Datenträger

Es gibt drei Haupttypen von Datenträgern auf einer Azure Stack Hub-VM:

- **Betriebssystemdatenträger:** Wenn Sie eine Azure Stack Hub-VM erstellen, fügt die Plattform der VM mindestens einen Datenträger (mit der Bezeichnung Laufwerk **C:** ) für das Betriebssystem hinzu. Bei diesem Datenträger handelt es sich um eine VHD, die als Seitenblob gespeichert wird.

- **Temporärer Datenträger:** Azure Stack Hub-VMs enthalten einen weiteren Datenträger, der als temporärer Datenträger bezeichnet wird (Laufwerk **D:** ). Dies ist ein Datenträger für temporären Speicherbereich auf dem Knoten.

- **Datenträger für Daten:** Sie können der VM weitere Datenträger für Daten hinzufügen. Diese werden als Seitenblobs im Speicher gespeichert.

In den folgenden Abschnitten werden Empfehlungen zur Verwendung dieser unterschiedlichen Datenträger erläutert.

### <a name="operating-system-disk"></a>Betriebssystem-Datenträger

Ein Betriebssystem-Datenträger ist eine virtuelle Festplatte, die Sie als aktive Version eines Betriebssystems starten und einbinden können, und die als Laufwerk **C** bezeichnet ist.

### <a name="temporary-disk"></a>Temporärer Datenträger

Das temporäre Speicherlaufwerk **D** ist nicht persistent. Speichern Sie auf Laufwerk **D** keine Daten, die nicht verloren gehen dürfen. Dazu zählen die Datenbankdateien und Transaktionsprotokolldateien für Ihre Benutzer.

Es empfiehlt sich, TempDB auf einem Datenträger für Daten zu speichern, da jeder Datenträger maximal bis zu 2.300 IOPS pro Datenträger bereitstellt.

### <a name="data-disks"></a>Datenträger

- **Verwenden von Datenträgern für Daten- und Protokolldateien:** Wenn Sie kein Datenträgerstriping nutzen, sollten Sie zwei Datenträger einer VM verwenden, die Storage Premium unterstützt. Dabei enthält ein Datenträger die Protokolldateien und der andere die Daten- und TempDB-Dateien. Jeder Datenträger bietet abhängig von der VM-Familie bestimmte IOPS-Werte, wie unter [In Azure Stack Hub unterstützte VM-Größen](azure-stack-vm-sizes.md) beschrieben. Wenn Sie eine Datenträgerstriping-Technik (beispielsweise Speicherplätze) verwenden, speichern Sie alle Daten- und Protokolldateien auf demselben Laufwerk (einschließlich TempDB). Diese Konfiguration ermöglicht die für SQL Server maximal verfügbare Anzahl von IOPS, unabhängig davon, welche Datei diese zu einem bestimmten Zeitpunkt nutzt.

> [!NOTE]  
> Beim Bereitstellen einer SQL Server-VM im Portal haben Sie die Möglichkeit, Ihre Speicherkonfiguration zu bearbeiten. Je nach Konfiguration werden in Azure Stack Hub ein oder mehrere Datenträger konfiguriert. Mehrere Datenträger werden in einem einzelnen Speicherpool zusammengefasst. Daten- und Protokolldateien befinden sich in dieser Konfiguration zusammen.

- **Datenträgerstriping:** Für einen höheren Durchsatz können Sie zusätzliche Datenträger für Daten hinzufügen und Datenträgerstriping verwenden. Um die Anzahl der benötigten Datenträger zu ermitteln, müssen Sie die Anzahl der IOPS analysieren, die für die Protokolldateien, Ihre Daten und die TempDB-Dateien erforderlich sind. Beachten Sie, dass IOPS-Grenzwerte pro Datenträger basierend auf der Serie der VM-Familie gelten – nicht basierend auf der VM-Größe. Limits für die Netzwerkbandbreite basieren dagegen auf der VM-Größe. Weitere Details finden Sie in den Tabellen unter [In Azure Stack Hub unterstützte VM-Größen](azure-stack-vm-sizes.md). Verwenden Sie die folgenden Richtlinien:

  - Verwenden Sie [Speicherplätze](https://technet.microsoft.com/library/hh831739.aspx) für Windows 2012 und höher entsprechend den folgenden Richtlinien:

    1. Legen Sie die Überlappung (Stripesetgröße) für OLTP-Workloads (Online Transaction Processing) auf 64 KB (65.536 Bytes) und für Data Warehousing-Workloads auf 256 KB (262.144 Bytes) fest, um Leistungseinbußen durch falsche Partitionsausrichtung zu vermeiden. Dies muss mit PowerShell festgelegt werden.

    2. Festgelegte Anzahl der Spalten = Anzahl der physischen Datenträger. Verwenden Sie PowerShell, wenn Sie mehr als acht Datenträger (keine Server-Manager-Benutzeroberfläche) konfigurieren.

       PowerShell erstellt z.B. wie folgt einen neuen Speicherpool mit der auf 64 KB festgelegten Überlappungsgröße und der auf 2 festgelegten Anzahl der Spalten:

       ```powershell  
       $PoolCount = Get-PhysicalDisk -CanPool $True
       $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

       New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple -UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false
       ```

- Bestimmen Sie auf Basis der erwarteten Auslastung die Anzahl der jedem Speicherpool zugeordneten Datenträger. Bedenken Sie, dass verschiedene VM-Größen unterschiedlich viele angefügte Datenträger für Daten unterstützen. Weitere Informationen finden Sie unter [In Azure Stack Hub unterstützte VM-Größen](azure-stack-vm-sizes.md).
- Um die maximal möglichen IOPS für Datenträger zu erzielen, wird empfohlen, die von der [VM-Größe](azure-stack-vm-sizes.md) unterstützte maximale Anzahl von Datenträgern hinzuzufügen und Datenträgerstriping zu verwenden.
- **Größe der NTFS-Zuordnungseinheiten:** Beim Formatieren des Datenträgers empfehlen wir, als Größe der Zuordnungseinheiten für Daten- und Protokolldateien sowie für TempDB-Dateien einen Wert von 64 KB zu verwenden.
- **Methoden für die Datenträgerverwaltung:** Beenden Sie den SQL Server-Dienst, wenn Sie einen Datenträger für Daten entfernen. Ändern Sie zudem nicht die Cacheeinstellungen auf den Datenträgern, da dadurch keine Leistungssteigerungen erzielt werden.

> [!WARNING]  
> Fehler beim Beenden des SQL Server-Diensts während dieser Vorgänge können zur Beschädigung der Datenbank führen.

## <a name="io-guidance"></a>E/A-Leitfaden

- Erwägen Sie die Aktivierung der sofortigen Dateiinitialisierung, um die für die anfängliche Dateizuordnung erforderliche Zeit zu verringern. Um die sofortige Dateiinitialisierung nutzen zu können, gewähren Sie dem SQL Server-Dienstkonto (MSSQLSERVER) die Berechtigung **SE_MANAGE_VOLUME_NAME**, und fügen Sie sie der Sicherheitsrichtlinie **Durchführen von Volumewartungsaufgaben** hinzu. Wenn Sie ein SQL Server-Plattformimage für Azure verwenden, wird das Standarddienstkonto (**NT Service\MSSQLSERVER**) nicht der Sicherheitsrichtlinie **Durchführen von Volumewartungsaufgaben** hinzugefügt. Anders ausgedrückt: Die sofortige Dateiinitialisierung wird in einem SQL Server-Azure-Plattformimage nicht aktiviert. Starten Sie den SQL Server-Dienst neu, nachdem Sie das SQL Server-Dienstkonto der Sicherheitsrichtlinie **Durchführen von Volumewartungsaufgaben** hinzugefügt haben. Möglicherweise sind bei Verwendung dieses Features bestimmte Sicherheitsaspekte zu berücksichtigen. Weitere Informationen finden Sie unter [Datenbankdatei-Initialisierung](https://msdn.microsoft.com/library/ms175935.aspx).
- **Automatische Vergrößerung** ist eine Behelfslösung für unerwartetes Wachstum. Führen Sie die tägliche Verwaltung Ihres Daten- und Protokolldateiwachstums nicht mit automatischer Vergrößerung durch. Wenn die automatische Vergrößerung verwendet wird, vergrößern Sie die Datei vorab mit dem Switch **Größe**.
- Stellen Sie sicher, dass **Automatische Verkleinerung** deaktiviert ist, um unnötigen Mehraufwand zu vermeiden, der sich negativ auf die Leistung auswirken kann.
- Richten Sie standardmäßige Dateispeicherorte für Sicherungen und Datenbanken ein. Verwenden Sie die Empfehlungen in diesem Artikel, und nehmen Sie die Änderungen im Fenster mit den Servereigenschaften vor. Eine Anleitung finden Sie unter [Anzeigen oder Ändern der Standardspeicherorte für Daten- und Protokolldateien (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). Der folgende Screenshot zeigt, wo diese Änderungen vorgenommen werden:

    > ![Anzeigen oder Ändern der Standardspeicherorte](./media/sql-server-vm-considerations/image1.png)

- Aktivieren Sie gesperrte Seiten, um E/A- und Auslagerungsaktivitäten zu verringern. Weitere Informationen finden Sie unter [Aktivieren der Option zum Sperren von Seiten im Speicher (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

- Es kann auch sinnvoll sein, alle Datendateien (einschließlich Sicherungen) beim Übertragen in bzw. aus Azure Stack Hub zu komprimieren.

## <a name="feature-specific-guidance"></a>Featurespezifische Anleitungen

Für manche Bereitstellungen können durch Verwendung erweiterter Konfigurationsmethoden zusätzliche Leistungsvorteile erzielt werden. In der folgenden Liste sind einige SQL Server-Features zusammengestellt, mit denen Sie eine bessere Leistung erzielen können:

- **Sicherung im Azure Stack-Speicher** **.** Wenn Sie Sicherungen für eine auf Azure Stack Hub-VMs ausgeführte SQL Server-Instanz erstellen, können Sie die SQL Server-Sicherung über URLs verwenden. Dieses Feature steht ab SQL Server 2012 SP1 CU2 zur Verfügung und wird für Sicherungen auf die angefügten Datenträgern für Daten empfohlen.

    Befolgen Sie beim Sichern oder Wiederherstellen mithilfe von Azure Storage die Empfehlungen unter [SQL Server-URL-Sicherung – bewährte Methoden und Problembehandlung](https://msdn.microsoft.com/library/jj919149.aspx) und [Wiederherstellen von in Microsoft Azure gespeicherten Sicherungen](https://docs.microsoft.com/sql/relational-databases/backup-restore/restoring-from-backups-stored-in-microsoft-azure?view=sql-server-2017). Darüber hinaus können Sie diese Sicherungen automatisieren, indem Sie die Informationen unter [Automatisierte Sicherung für SQL Server 2014-VMs (Resource Manager)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup) verwenden.

-   **Sicherung im Azure Stack Hub-Speicher.** Sie können Sicherungen im Azure Stack Hub-Speicher auf ähnliche Weise durchführen wie Sicherungen in Azure Storage. Wenn Sie eine Sicherung in SQL Server Management Studio (SSMS) erstellen, müssen Sie die Konfigurationsinformationen manuell eingeben. SSMS kann nicht verwendet werden, um den Speichercontainer oder die Shared Access Signature zu erstellen. Mit SSMS kann nur eine Verbindung mit Azure-Abonnements, jedoch nicht mit Azure Stack Hub-Abonnements hergestellt werden. Stattdessen müssen Sie das Speicherkonto, den Container und die Shared Access Signature im Azure Stack Hub-Portal oder über PowerShell erstellen.


    ![SQL Server-Sicherung](./media/sql-server-vm-considerations/image3.png)

    > [!NOTE]  
    > Die Shared Access Signature ist das SAS-Token aus dem Azure Stack Hub-Portal ohne das führende „?“ in der Zeichenfolge. Wenn Sie die Kopierfunktion über das Portal verwenden, müssen Sie das führende „?“ löschen, damit das Token in SQL Server funktioniert.

    Nachdem Sie das Sicherungsziel in SQL Server eingerichtet und konfiguriert haben, können Sie Sicherungen im Azure Stack Hub-Blobspeicher durchführen.

## <a name="next-steps"></a>Nächste Schritte

[Verwenden von Diensten oder Erstellen von Apps für Azure Stack Hub](azure-stack-considerations.md)
