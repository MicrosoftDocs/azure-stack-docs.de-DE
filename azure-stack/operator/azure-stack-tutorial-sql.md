---
title: Anbieten hoch verfügbarer SQL-Datenbanken in Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit Azure Stack einen Hostcomputer für einen SQL Server-Ressourcenanbieter sowie hoch verfügbare SQL-Always On-Datenbanken erstellen.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: justinha
ms.reviewer: quying
ms.lastreviewed: 10/23/2018
ms.openlocfilehash: e21819fc6475161e63fcdb99a723847c5d7fbbad
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66268411"
---
# <a name="tutorial-offer-highly-available-sql-databases"></a>Tutorial: Anbieten von hoch verfügbaren SQL-Datenbanken

Als Azure Stack-Bediener können Sie virtuelle Servercomputer zum Hosten von SQL Server-Datenbanken konfigurieren. Wenn ein SQL-Hostserver erstellt wurde und über Azure Stack verwaltet wird, können Benutzer, die SQL-Dienste abonniert haben, ganz einfach SQL-Datenbanken erstellen.

Dieses Tutorial zeigt, wie Sie eine Azure Stack-Schnellstartvorlage verwenden, um eine [SQL Server-Always On-Verfügbarkeitsgruppe](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017) zu erstellen, diese als Azure Stack-SQL-Hostserver hinzufügen und dann eine hoch verfügbare SQL-Datenbank erstellen.

Sie lernen Folgendes:

> [!div class="checklist"]
> * Erstellen einer SQL Server-Always On-Verfügbarkeitsgruppe aus einer Vorlage
> * Erstellen eines Azure Stack-SQL-Hostservers
> * Erstellen einer hoch verfügbaren SQL-Datenbank

In diesem Tutorial wird mithilfe von verfügbaren Elementen vom Azure Stack-Marketplace eine SQL Server-Always On-Verfügbarkeitsgruppe mit zwei virtuellen Computern erstellt. 

Vergewissern Sie sich zunächst, dass der [SQL Server-Ressourcenanbieter](azure-stack-sql-resource-provider-deploy.md) erfolgreich installiert wurde und dass die folgenden Elemente im Azure Stack-Marketplace verfügbar sind, bevor Sie mit den Schritten dieses Tutorials beginnen:

> [!IMPORTANT]
> Alle folgenden Elemente sind erforderlich, um die Azure Stack-Schnellstartvorlage zu verwenden.

- Marketplace-Image von [Windows Server 2016 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WindowsServer).
- SQL Server 2016 SP1 oder SP2 (Enterprise, Standard oder Developer) im Windows Server 2016-Serverimage. Dieses Tutorial verwendet das Marketplace-Image von [SQL Server 2016 SP2 Enterprise unter Windows Server 2016](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoftsqlserver.sql2016sp2-ws2016).
- [SQL Server IaaS Extension](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) Version 1.2.30 oder höher. SQL Server IaaS Extension installiert Komponenten, die von den SQL Server-Elementen im Marketplace für alle Windows-Versionen benötigt werden. Diese Erweiterung ermöglicht die Konfiguration von SQL-spezifischen Einstellungen auf virtuellen SQL-Computern. Wenn die Erweiterung nicht im lokalen Marketplace installiert ist, kann SQL nicht bereitgestellt werden.
- [Custom Script Extension für Windows](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.CustomScriptExtension) Version 1.9.1 oder höher. Custom Script Extension ist ein Tool, das zum automatischen Starten von VM-Anpassungstasks nach der Bereitstellung verwendet werden kann.
- [PowerShell Desired State Configuration (DSC)](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.DSC-arm) Version 2.76.0.0 oder höher. DSC ist eine Verwaltungsplattform in Windows PowerShell, die die Bereitstellung und Verwaltung von Konfigurationsdaten für Softwaredienste sowie die Verwaltung der Umgebung ermöglicht, in der diese Dienste ausgeführt werden.

Weitere Informationen über das Hinzufügen von Elementen zum Azure Stack-Marketplace finden Sie unter [Der Azure Stack-Marketplace – Übersicht](azure-stack-marketplace.md).

## <a name="create-a-sql-server-alwayson-availability-group"></a>Erstellen einer SQL Server-Always On-Verfügbarkeitsgruppe
Mit den Schritten in diesem Abschnitt stellen Sie mithilfe der [Azure Stack-Schnellstartvorlage „sql-2016-alwayson“](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2016-alwayson) eine SQL Server-Always On-Verfügbarkeitsgruppe bereit. Diese Vorlage stellt zwei SQL Server Enterprise-Instanzen – Standard oder Developer – in einer Always On-Verfügbarkeitsgruppe bereit. Er erstellt die folgenden Ressourcen:

- Eine Netzwerksicherheitsgruppe.
- Ein virtuelles Netzwerk
- Vier Speicherkonten (eins für Active Directory, eins für SQL, eins für den Dateifreigabenzeugen und eins für die VM-Diagnose).
- Vier öffentliche IP-Adressen (eine für AD, zwei für jede SQL-VM und eine für das öffentliche Lastenausgleichsmodul, das an den SQL Always On-Listener gebunden ist).
- Ein externes Lastenausgleichsmodul für SQL-VMs mit einer öffentlichen IP-Adresse, die an den SQL Always On-Listener gebunden ist.
- Eine VM (Windows Server 2016), die als Domänencontroller für eine neue Gesamtstruktur mit einer einzelnen Domäne konfiguriert ist.
- Zwei VMs (Windows Server 2016), die mit SQL Server 2016 SP1 oder SP2 Enterprise, Standard oder Developer Edition konfiguriert und gruppiert sind. Hierbei muss es sich um Marketplace-Images handeln.
- Eine VM (Windows Server 2016), die als Dateifreigabenzeuge für den Cluster konfiguriert ist.
- Eine Verfügbarkeitsgruppe, die die VMs für SQL und den Dateifreigabenzeugen enthält.  

1. 
   [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. Klicken Sie auf **\+** **Ressource erstellen** > **Benutzerdefiniert** und dann auf **Vorlagenbereitstellung**.

   ![Benutzerdefinierte Vorlagenbereitstellung](media/azure-stack-tutorial-sqlrp/1.png)


3. Klicken Sie auf dem Blatt **Benutzerdefinierte Bereitstellung** auf **Vorlage bearbeiten** > **Schnellstartvorlage**, und wählen Sie dann aus der Dropdownliste der verfügbaren benutzerdefinierten Vorlagen die Vorlage **sql-2016-alwayson** aus. Klicken Sie auf **OK** und dann auf **Speichern**.

   [![](media/azure-stack-tutorial-sqlrp/2-sm.PNG "Auswählen der Schnellstartvorlage")](media/azure-stack-tutorial-sqlrp/2-lg.PNG#lightbox)

4. Klicken Sie auf dem Blatt **Benutzerdefinierte Bereitstellung** auf **Parameter bearbeiten**, und sehen Sie sich die Standardwerte an. Ändern Sie die Werte nach Bedarf, um alle erforderlichen Parameterinformationen anzugeben, und klicken Sie dann auf **OK**.<br><br> Mindestangaben:

    - Geben Sie komplexe Kennwörter für die Parameter ADMINPASSWORD, SQLSERVERSERVICEACCOUNTPASSWORD und SQLAUTHPASSWORD an.
    - Geben Sie das DNS-Suffix für Reverse-Lookups für den Parameter DNSSUFFIX in Kleinbuchstaben ein (**azurestack.external** bei ASDK-Installationen).
    
   [![](media/azure-stack-tutorial-sqlrp/3-sm.PNG "Bearbeiten von benutzerdefinierten Bereitstellungsparametern")](media/azure-stack-tutorial-sqlrp/3-lg.PNG#lightbox)

5. Wählen Sie auf dem Blatt **Benutzerdefinierte Bereitstellung** das gewünschte Abonnement aus. Erstellen Sie dann eine neue Ressourcengruppe für die benutzerdefinierte Bereitstellung, oder wählen Sie eine vorhandene Ressourcengruppe aus.<br><br> Wählen Sie als Nächstes den Standort der Ressourcengruppe (**lokal** bei ASDK-Installationen), und klicken Sie auf **Erstellen**. Die Einstellungen für die benutzerdefinierte Bereitstellung werden überprüft, und dann wird die Bereitstellung gestartet.

    [![](media/azure-stack-tutorial-sqlrp/4-sm.PNG "Erstellen einer benutzerdefinierten Bereitstellung")](media/azure-stack-tutorial-sqlrp/4-lg.PNG#lightbox)


6. Klicken Sie im Verwaltungsportal auf **Ressourcengruppen** und dann auf den Namen der Ressourcengruppe, die Sie für die benutzerdefinierte Bereitstellung erstellt haben (in diesem Beispiel: **resource-group**). Überprüfen Sie den Status der Bereitstellung, um sicherzustellen, dass alle Bereitstellungen erfolgreich abgeschlossen wurden.<br><br>Überprüfen Sie danach die Ressourcengruppenelemente, und wählen Sie das Element **SQLPIPsql\<Ressourcengruppenname\>** für die öffentliche IP-Adresse aus. Notieren Sie sich die öffentliche IP-Adresse und den vollqualifizierten Domänennamen (FQDN) der öffentlichen IP-Adresse des Lastenausgleichsmoduls. Diese Informationen müssen Sie an einen Azure Stack-Bediener weitergeben, damit dieser einen SQL-Hostserver mit dieser SQL-Always On-Verfügbarkeitsgruppe erstellen kann.

   > [!NOTE]
   > Die Vorlagenbereitstellung dauert mehrere Stunden.

   ![Benutzerdefinierte Bereitstellung abgeschlossen](./media/azure-stack-tutorial-sqlrp/5.png)

### <a name="enable-automatic-seeding"></a>Aktivieren des automatischen Seedings
Nachdem die Vorlage erfolgreich bereitgestellt und die SQL-Always On-Verfügbarkeitsgruppe damit erstellt wurde, müssen Sie in jeder SQL Server-Instanz in der Verfügbarkeitsgruppe das [automatische Seeding](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) aktivieren. 

Wenn Sie eine Verfügbarkeitsgruppe mit automatischem Seeding erstellen, erstellt SQL Server automatisch und ohne jegliches manuelles Eingreifen die sekundären Replikate für jede Datenbank in der Gruppe, um die Hochverfügbarkeit der Always On-Datenbanken sicherzustellen.

Verwenden Sie diese SQL-Befehle, um das automatische Seeding für die Always On-Verfügbarkeitsgruppe zu konfigurieren. Ersetzen Sie je nach Bedarf \<InstanceName\> durch den Namen der primären SQL Server-Instanz und „<availability_group_name>“ durch den Namen der Always On-Verfügbarkeitsgruppe. 

Auf der primären SQL Server-Instanz:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<InstanceName>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

>  ![Skript für die primäre SQL Server-Instanz](./media/azure-stack-tutorial-sqlrp/sql1.png)

Auf sekundären SQL Server-Instanzen:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```
>  ![Skript für die sekundäre SQL Server-Instanz](./media/azure-stack-tutorial-sqlrp/sql2.png)

### <a name="configure-contained-database-authentication"></a>Konfigurieren der Authentifizierung der eigenständigen Datenbank
Stellen Sie vor dem Hinzufügen einer eigenständigen Datenbank zu einer Verfügbarkeitsgruppe sicher, dass die Serveroption für die Authentifizierung der eigenständigen Datenbank für jede Serverinstanz auf 1 festgelegt ist, die ein Verfügbarkeitsreplikat für die Verfügbarkeitsgruppe hostet. Weitere Informationen finden Sie unter [Contained Database Authentication (Serverkonfigurationsoption)](https://docs.microsoft.com/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017).

Verwenden Sie diese Befehle, um die Serveroption „contained database authentication“ zur Authentifizierung der eigenständigen Datenbank für jede SQL Server-Instanz in der Verfügbarkeitsgruppe festzulegen:

  ```sql
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```
>  ![Festlegen von „contained database authentication“](./media/azure-stack-tutorial-sqlrp/sql3.png)

## <a name="create-an-azure-stack-sql-hosting-server"></a>Erstellen eines Azure Stack-SQL-Hostservers
Nachdem die SQL Server-Always On-Verfügbarkeitsgruppe erstellt und ordnungsgemäß konfiguriert wurde, muss ein Azure Stack-Bediener einen Azure Stack-SQL-Hostserver erstellen, um Benutzern die zusätzliche Kapazität zur Verfügung zu stellen, damit diese Datenbanken erstellen können. 

Verwenden Sie die öffentliche IP-Adresse oder den vollständigen vollqualifizierten Domänennamen für die öffentliche IP-Adresse des Lastenausgleichsmoduls, die bzw. den Sie beim Erstellen der Ressourcengruppe (**SQLPIPsql\<Ressourcengruppenname\>** ) für die SQL AlwaysOn-Verfügbarkeitsgruppe erfasst haben. Darüber hinaus benötigen Sie die Anmeldeinformationen für die SQL Server-Authentifizierung, die für den Zugriff auf die SQL Server-Instanzen in der AlwaysOn-Verfügbarkeitsgruppe verwendet werden.

> [!NOTE]
> Dieser Schritt muss im Azure Stack-Verwaltungsportal von einem Azure Stack-Bediener ausgeführt werden.

Mit der öffentlichen IP-Adresse des Listeners für das Lastenausgleichsmodul der SQL-AlwaysOn-Verfügbarkeitsgruppe und den Anmeldeinformationen für die SQL-Authentifizierung kann ein Azure Stack-Bediener nun [unter Verwendung der SQL AlwaysOn-Verfügbarkeitsgruppe einen SQL-Hostserver erstellen](azure-stack-sql-resource-provider-hosting-servers.md#provide-high-availability-using-sql-always-on-availability-groups). 

Darüber hinaus müssen Sie Pläne und Angebote erstellt haben, um Benutzern die Erstellung von SQL AlwaysOn-Datenbanken zu ermöglichen. Der Bediener muss den **Microsoft.SqlAdapter**-Dienst zu einem Plan hinzufügen und ein neues Kontingent speziell für hoch verfügbare Datenbanken erstellen. Weitere Informationen zum Erstellen von Plänen finden Sie unter [Übersicht über Pläne, Angebote, Kontingente und Abonnements](azure-stack-plan-offer-quota-overview.md).

> [!TIP]
> Der **Microsoft.SqlAdapter**-Dienst kann erst dann zu Plänen hinzugefügt werden, wenn der [SQL Server-Ressourcenanbieter bereitgestellt wurde](azure-stack-sql-resource-provider-deploy.md).

## <a name="create-a-highly-available-sql-database"></a>Erstellen einer hoch verfügbaren SQL-Datenbank
Nachdem die SQL-Always On-Verfügbarkeitsgruppe durch einen Azure Stack-Bediener erstellt, konfiguriert und als Azure Stack-SQL-Hostserver hinzugefügt wurde, können Mandantenbenutzer mit einem Abonnement, das SQL Server-Datenbankfunktionen enthält, SQL-Datenbanken mit Always On-Funktionalität erstellen. Führen Sie dazu die Schritte in diesem Abschnitt aus. 

> [!NOTE]
> Führen Sie diese Schritte als Mandantenbenutzer im Azure Stack-Benutzerportal mit einem Abonnement aus, das SQL Server-Funktionen (Microsoft.SQLAdapter-Dienst) bereitstellt.

1. 
   [!INCLUDE [azs-user-portal](../includes/azs-user-portal.md)]

2. Klicken Sie auf **\+** **Ressource erstellen** > **Daten \+ Speicher** und dann auf **SQL-Datenbank**.<br><br>Geben Sie die erforderlichen Datenbankeigenschaften – Name, Sortierung, maximale Größe – sowie das Abonnement, die Ressourcengruppe und den Standort für die Bereitstellung an. 

   ![Erstellen einer SQL-Datenbank](./media/azure-stack-tutorial-sqlrp/createdb1.png)

3. Klicken Sie auf **SKU**, und wählen Sie eine geeignete SKU für den SQL-Hostserver aus. In diesem Beispiel hat der Azure Stack-Bediener die SKU **Enterprise-HA** erstellt, um die Hochverfügbarkeit für SQL-Always On-Verfügbarkeitsgruppen zu unterstützen.

   ![Auswählen der SKU](./media/azure-stack-tutorial-sqlrp/createdb2.png)

4. Klicken Sie auf **Anmelden** > **Neue Anmeldung erstellen**, und geben Sie die Anmeldeinformationen für die SQL-Authentifizierung ein, die für die neue Datenbank verwendet werden sollen. Wenn Sie fertig sind, klicken Sie auf **OK** und dann auf **Erstellen**, um den Prozess der Datenbankbereitstellung zu starten.

   ![Erstellen der Anmeldung](./media/azure-stack-tutorial-sqlrp/createdb3.png)

5. Wenn die Bereitstellung der SQL-Datenbank erfolgreich abgeschlossen wurde, überprüfen Sie die Datenbankeigenschaften, um die Verbindungszeichenfolge zu ermitteln, die zum Herstellen der Verbindung mit der neuen hoch verfügbaren Datenbank verwendet werden soll. 

   ![Anzeigen der Verbindungszeichenfolge](./media/azure-stack-tutorial-sqlrp/createdb4.png)




## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen einer SQL Server-Always On-Verfügbarkeitsgruppe aus einer Vorlage
> * Erstellen eines Azure Stack-SQL-Hostservers
> * Erstellen einer hoch verfügbaren SQL-Datenbank

Im nächsten Tutorial lernen Sie Folgendes:
> [!div class="nextstepaction"]
> [Erstellen von hoch verfügbaren MySQL-Datenbanken](azure-stack-tutorial-mysql.md)