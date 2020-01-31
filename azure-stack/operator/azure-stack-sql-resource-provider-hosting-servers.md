---
title: Hinzufügen von Hostservern für den SQL-Ressourcenanbieter
titleSuffix: Azure Stack Hub
description: Hier erfahren Sie, wie Sie Hostserver für die Bereitstellung über den SQL-Ressourcenanbieteradapter hinzufügen.
author: mattbriggs
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: c7b3d1ef979e69a72904e03b49cb5ee4b75c182e
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76882812"
---
# <a name="add-hosting-servers-for-the-sql-resource-provider"></a>Hinzufügen von Hostservern für den SQL-Ressourcenanbieter

Sie können SQL Server-Datenbankhostserver auf einer VM in [Azure Stack Hub](azure-stack-overview.md) oder auf einer VM außerhalb Ihrer Azure Stack Hub-Umgebung erstellen, sofern der SQL-Ressourcenanbieter eine Verbindung mit der Instanz herstellen kann.

> [!NOTE]
> Der SQL-Ressourcenanbieter sollte im Standardanbieterabonnement erstellt werden, während SQL-Hostingserver in einem abrechenbaren Benutzerabonnement erstellt werden sollten. Der Server des Ressourcenanbieters sollte nicht zum Hosten von Benutzerdatenbanken verwendet werden.

## <a name="overview"></a>Übersicht

Überprüfen Sie vor dem Hinzufügen eines SQL-Hostservers die folgenden obligatorischen und allgemeinen Anforderungen.

### <a name="mandatory-requirements"></a>Obligatorische Anforderungen

* Aktivieren Sie die SQL-Authentifizierung auf der SQL Server-Instanz. Da der virtuelle Computer eines SQL-Ressourcenanbieters nicht in eine Domäne eingebunden ist, kann er nur unter Verwendung der SQL-Authentifizierung eine Verbindung mit einem Hostserver herstellen.
* Konfigurieren Sie die IP-Adressen für die SQL-Instanzen als „Öffentlich“, wenn sie in Azure Stack Hub installiert werden. Der Ressourcenanbieter und die Benutzer (z. B. Web-Apps) kommunizieren über das Benutzernetzwerk, sodass in diesem Netzwerk Konnektivität mit der SQL-Instanz erforderlich ist.

### <a name="general-requirements"></a>Allgemeine Anforderungen

* Weisen Sie die SQL-Instanz für die Verwendung durch den Ressourcenanbieter und durch Benutzerworkloads zu. Sie können keine SQL-Instanz verwenden, die durch andere Consumer verwendet wird. Diese Einschränkung gilt auch für App Services.
* Konfigurieren Sie ein Konto mit den entsprechenden Berechtigungsstufen für den Ressourcenanbieter (Beschreibung siehe unten).
* Sie sind für die Verwaltung der SQL-Instanzen und deren Hosts verantwortlich. Beispielsweise wendet der Ressourcenanbieter keine Updates an, führt keine Sicherungen aus oder rotiert Anmeldeinformationen.

### <a name="sql-server-vm-images"></a>Images für SQL Server-VMs

Images für SQL-IaaS-VMs sind über das Marketplace-Verwaltungsfeature verfügbar. Diese Images sind mit den in Azure verfügbaren SQL-VMs identisch.

Laden Sie vor dem Bereitstellen eines virtuellen SQL-Computers mit einem Marketplace-Element unbedingt immer die neueste Version der **SQL-IaaS-Erweiterung** herunter. Die IaaS-Erweiterung und die zugehörigen Portalerweiterungen stellen zusätzliche Features wie das automatische Patchen und Sicherungen bereit. Weitere Informationen zu dieser Erweiterung finden Sie unter [Automatisieren von Verwaltungsaufgaben auf virtuellen Azure-Computern mit der SQL Server-IaaS-Agent-Erweiterung](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension).

> [!NOTE]
> Die SQL-IaaS-Erweiterung ist für alle Images vom Typ „SQL unter Windows“ im Marketplace _erforderlich_. Der virtuelle Computer wird nicht bereitgestellt, wenn Sie die Erweiterung nicht heruntergeladen haben. Bei Linux-basierten SQL-VM-Images wird sie nicht verwendet.

Es gibt noch weitere Optionen für die Bereitstellung von SQL-VMs, beispielsweise Vorlagen im [Schnellstartkatalog für Azure Stack Hub](https://github.com/Azure/AzureStack-QuickStart-Templates).

> [!NOTE]
> Alle Hostserver, die in einer Azure Stack Hub-Instanz mit mehreren Knoten installiert werden, müssen mit einem Benutzerabonnement erstellt werden, nicht mit dem Standardanbieterabonnement. Sie müssen über das Benutzerportal oder eine PowerShell-Sitzung mit einer entsprechenden Anmeldung erstellt werden. Alle Hostserver sind abrechenbare VMs, die entsprechende SQL-Lizenzen benötigen. Der Dienstadministrator _kann_ der Besitzer dieses Abonnements sein.

### <a name="required-privileges"></a>Erforderliche Berechtigungen

Sie können einen Administratorbenutzer erstellen, der über weniger Berechtigungen verfügt als ein SQL-Systemadministrator. Der Benutzer benötigt lediglich Rechte für die folgenden Vorgänge:

* Datenbank: erstellen, ändern, mit Kapselung (nur AlwaysOn), ablegen, sichern
* Verfügbarkeitsgruppe: ändern, verknüpfen, Datenbank hinzufügen/entfernen
* Anmeldung: erstellen, auswählen, ändern, ablegen, widerrufen
* Select-Vorgänge: \[master\].\[sys\].\[availability_group_listeners\] (Always On), sys.availability_replicas (Always On), sys.databases, \[master\].\[sys\].\[dm_os_sys_memory\], SERVERPROPERTY, \[master\].\[sys\].\[availability_groups\] (Always On), sys.master_files

### <a name="additional-security-information"></a>Weitere Sicherheitsinformationen

Die folgenden Informationen stellen zusätzliche Sicherheitshinweise dar:

* Der gesamte Azure Stack Hub-Speicher wird mit BitLocker verschlüsselt, sodass jede SQL-Instanz in Azure Stack Hub verschlüsselten Blobspeicher verwendet.
* Der SQL-Ressourcenanbieter bietet vollständige Unterstützung für TLS 1.2. Stellen Sie sicher, dass alle SQL Server-Instanzen, die über den SQL-Ressourcenanbieter verwaltet werden, _nur_ für TLS 1.2 konfiguriert sind und dass der Ressourcenanbieter standardmäßig dieses Protokoll verwendet. TLS 1.2 wird von allen unterstützten SQL Server-Versionen unterstützt. Weitere Informationen finden Sie unter [TLS 1.2-Unterstützung für Microsoft SQL Server](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server).
* Verwenden Sie den SQL Server-Konfigurations-Manager zum Festlegen der Option **ForceEncryption**, um sicherzustellen, dass die gesamte Kommunikation mit dem Computer mit SQL Server immer verschlüsselt wird. Weitere Informationen finden Sie unter [Konfigurieren des Servers zum Erzwingen verschlüsselter Verbindungen](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine?view=sql-server-2017#to-configure-the-server-to-force-encrypted-connections).
* Stellen Sie sicher, dass alle Client-Apps ebenfalls über eine verschlüsselte Verbindung kommunizieren.
* Der Ressourcenanbieter ist so konfiguriert, dass er den von der SQL Server-Instanz verwendeten Zertifikaten vertraut.

## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>Bereitstellen von Kapazität durch Herstellen einer Verbindung mit einem eigenständigen SQL-Hostserver

Sie können eigenständige SQL Server-Instanzen (ohne hohe Verfügbarkeit) mit einer beliebigen Edition von SQL Server 2014 oder SQL Server 2016 verwenden. Stellen Sie sicher, dass Sie über die Anmeldeinformationen für ein Konto mit Systemadministratorrechten verfügen.

Führen Sie die folgenden Schritte aus, um einen eigenständigen Hostserver hinzuzufügen, der bereits eingerichtet wurde:

1. Melden Sie sich beim Azure Stack Hub-Administratorportal als Dienstadministrator an.

2. Wählen Sie **Alle Dienste** &gt; **VERWALTUNGSRESSOURCEN** &gt; **SQL-Hostserver** aus.

   ![SQL-Hostserver im Azure Stack Hub-Administratorportal](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

   Unter **SQL-Hostserver** können Sie den SQL-Ressourcenanbieter mit SQL Server-Instanzen verbinden, die als Back-End des Ressourcenanbieters dienen.

   ![Dashboard des SQL-Adapters im Azure Stack Hub-Administratorportal](./media/azure-stack-sql-rp-deploy/sqlrp-hostingserver.png)

3. Klicken Sie auf **Hinzufügen**, und geben Sie dann auf dem Blatt **SQL-Hostserver hinzufügen** die Verbindungsdetails für Ihre SQL Server-Instanz an.

   ![Hinzufügen eines SQL-Hostservers im Azure Stack Hub-Administratorportal](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

    Geben Sie optional einen Instanznamen an, und geben Sie eine Portnummer an, wenn die Instanz nicht dem Standardport 1433 zugewiesen ist.

   > [!NOTE]
   > Sofern die SQL-Instanz für den Azure Resource Manager-Benutzer und -Administrator zugänglich ist, kann sie vom Ressourcenanbieter gesteuert werden. Die SQL-Instanz __muss__ ausschließlich dem Ressourcenanbieter zugeordnet sein.

4. Beim Hinzufügen von Servern müssen Sie den Servern eine vorhandene SKU zuweisen oder eine neue SKU erstellen. Wählen Sie unter **SQL-Hostserver hinzufügen** die Option **SKUs** aus.

   * Wenn Sie eine vorhandene SKU verwenden möchten, wählen Sie eine verfügbare SKU aus, und wählen Sie dann **Erstellen** aus.
   * Wenn Sie eine SKU erstellen möchten, wählen Sie **+ Neue SKU erstellen** aus. Geben Sie unter **SKU erstellen** die erforderlichen Informationen ein, und wählen Sie dann **OK** aus.

     ![Erstellen einer SKU im Azure Stack Hub-Administratorportal](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

## <a name="provide-high-availability-using-sql-always-on-availability-groups"></a>Bereitstellen von Hochverfügbarkeit mithilfe von SQL-AlwaysOn-Verfügbarkeitsgruppen

Für das Konfigurieren von SQL-AlwaysOn-Instanzen sind zusätzliche Schritte und drei virtuelle (oder physische) Computer erforderlich. In diesem Artikel wird davon ausgegangen, dass Sie bereits über solide Kenntnisse zu AlwaysOn-Verfügbarkeitsgruppen verfügen. Weitere Informationen finden Sie in den folgenden Artikeln:

* [Einführung in SQL Server Always On-Verfügbarkeitsgruppen auf virtuellen Azure-Computern](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
* [Always On-Verfügbarkeitsgruppen (SQL Server)](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2017)

> [!NOTE]
> Der SQL-Adapterressourcenanbieter unterstützt _ausschließlich_ SQL 2016 SP1 Enterprise oder spätere Instanzen für AlwaysOn-Verfügbarkeitsgruppen. Diese Adapterkonfiguration erfordert neue SQL-Features wie z.B. automatisches Seeding.

### <a name="automatic-seeding"></a>Automatisches Seeding

Sie müssen das [automatische Seeding](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) für jede Verfügbarkeitsgruppe für jede Instanz von SQL Server aktivieren.

Um das automatische Seeding für alle Instanzen zu aktivieren, bearbeiten Sie den folgenden SQL-Befehl für jede Instanz und führen ihn dann auf dem primären Replikat für jede sekundäre Instanz aus:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<secondary_node>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

Die Verfügbarkeitsgruppe muss in eckige Klammern eingeschlossen sein.

Führen Sie auf den sekundären Knoten den folgenden SQL-Befehl aus:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```

### <a name="configure-contained-database-authentication"></a>Konfigurieren der Authentifizierung der eigenständigen Datenbank

Stellen Sie vor dem Hinzufügen einer eigenständigen Datenbank zu einer Verfügbarkeitsgruppe sicher, dass die Serveroption für die Authentifizierung der eigenständigen Datenbank für jede Serverinstanz auf 1 festgelegt ist, die ein Verfügbarkeitsreplikat für die Verfügbarkeitsgruppe hostet. Weitere Informationen finden Sie unter [Serverkonfigurationsoption für die Authentifizierung der eigenständigen Datenbank](https://docs.microsoft.com/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017).

Verwenden Sie diese Befehle, um die Serveroption für die Authentifizierung der eigenständigen Datenbank für jede Instanz festzulegen:

  ```sql
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```

### <a name="to-add-sql-always-on-hosting-servers"></a>Hinzufügen von SQL-AlwaysOn-Hostservern

1. Melden Sie sich beim Azure Stack Hub-Administratorportal als Dienstadministrator an.

2. Wählen Sie **Durchsuchen** &gt; **VERWALTUNGSRESSOURCEN** &gt; **SQL-Hostserver** &gt; **+ Hinzufügen** aus.

   Unter **SQL-Hostserver** können Sie den SQL Server-Ressourcenanbieter mit tatsächlichen SQL Server-Instanzen verbinden, die als Back-End des Ressourcenanbieters dienen.

3. Füllen Sie das Formular mit den Verbindungsdetails für Ihre SQL Server-Instanz aus. Stellen Sie sicher, dass Sie die FQDN-Adresse des AlwaysOn-Listeners (und optional die Portnummer und den Instanznamen) verwenden. Geben Sie die Informationen für das Konto an, das Sie mit Systemadministratorrechten konfiguriert haben.

4. Aktivieren Sie das Kontrollkästchen „AlwaysOn-Verfügbarkeitsgruppe“, um Instanzen von SQL-AlwaysOn-Verfügbarkeitsgruppen zu unterstützen.

   ![Aktivieren einer Always On-Verfügbarkeitsgruppe im Azure Stack Hub-Administratorportal](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. Fügen Sie die SQL-Always On-Instanz einer SKU hinzu.

   > [!IMPORTANT]
   > Sie können in derselben SKU nicht eigenständige Server mit AlwaysOn-Instanzen kombinieren. Der Versuch, nach dem Hinzufügen des ersten Hostservers Typen zu kombinieren, führt zu einem Fehler.

## <a name="sku-notes"></a>Hinweise zu SKUs
Verwenden Sie einen SKU-Namen, der die Funktionen der Server in der SKU beschreibt, z.B. Kapazität und Leistung. Der Name soll Benutzer dabei unterstützen, ihre Datenbanken für die entsprechende SKU bereitzustellen. Beispielsweise können Sie SKU-Namen verwenden, um Dienstangebote anhand der folgenden Merkmale zu unterscheiden:
  
* Hohe Kapazität
* Hohe Leistung
* Hochverfügbarkeit

Als Best Practice sollten alle Hostserver in einer SKU die gleichen Ressourcen- und Leistungsmerkmale aufweisen.

SKUs können nicht bestimmten Benutzern oder Gruppen zugeordnet werden.

Es kann bis zu einer Stunde dauern, bis SKUs im Portal angezeigt werden. Benutzer können erst eine Datenbank erstellen, wenn die SKU vollständig erstellt wurde.

Um eine SKU zu bearbeiten, navigieren Sie zu **Alle Dienste** > **SQL-Adapter** > **SKUs**. Wählen Sie die zu ändernde SKU aus, nehmen Sie alle notwendigen Änderungen vor, und klicken Sie dann auf **Speichern**, um die Änderungen zu speichern. 

Wenn Sie eine nicht mehr benötigte SKU löschen möchten, navigieren Sie zu **Alle Dienste** > **SQL-Adapter** > **SKUs**. Klicken Sie mit der rechten Maustaste auf den Namen der SKU, und wählen Sie dann **Löschen** aus, um sie zu löschen.

> [!IMPORTANT]
> Es kann bis zu einer Stunde dauern, bis neue SKUs im Benutzerportal verfügbar sind.

## <a name="make-sql-databases-available-to-users"></a>Verfügbarmachen von SQL-Datenbanken für Benutzer

Erstellen Sie Pläne und Angebote, um SQL-Datenbanken für Benutzer zur Verfügung zu stellen. Fügen Sie dem Plan den Dienst **Microsoft.SqlAdapter** hinzu. Außerdem müssen Sie ein neues Kontingent erstellen.

> [!IMPORTANT]
> Es kann bis zu zwei Stunden dauern, bis neue Kontingente im Benutzerportal verfügbar sind oder ein geändertes Kontingent erzwungen wird.

## <a name="next-steps"></a>Nächste Schritte

[Hinzufügen von Datenbanken](azure-stack-sql-resource-provider-databases.md)
