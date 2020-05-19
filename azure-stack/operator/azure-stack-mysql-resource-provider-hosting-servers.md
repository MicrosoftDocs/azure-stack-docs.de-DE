---
title: Hinzufügen für MySQL-Hostserver in Azure Stack Hub
description: Erfahren Sie, wie Sie MySQL-Hostserver für die Bereitstellung über den MySQL-Adapterressourcenanbieter hinzufügen.
author: bryanla
ms.topic: article
ms.date: 11/06/2019
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 11/06/2019
ms.openlocfilehash: 90b20ddcc129b8077cf28fa1a1a758054795de60
ms.sourcegitcommit: 4a8d7203fd06aeb2c3026d31ffec9d4fbd403613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83202498"
---
# <a name="add-mysql-hosting-servers-in-azure-stack-hub"></a>Hinzufügen für MySQL-Hostserver in Azure Stack Hub

Sie können eine MySQL-Hostserverinstanz auf einem virtuellen Computer (VM) in [Azure Stack Hub](azure-stack-overview.md) oder auf einer VM außerhalb Ihrer Azure Stack Hub-Umgebung hosten, solange sich der MySQL-Ressourcenanbieter mit der Instanz verbinden kann.

> [!NOTE]
> Der MySQL-Ressourcenanbieter sollte im Standardanbieterabonnement erstellt werden, während MySQL-Hostingserver in einem abrechenbaren Benutzerabonnement erstellt werden sollten. Der Server des Ressourcenanbieters sollte nicht zum Hosten von Benutzerdatenbanken verwendet werden.

Die MySQL-Versionen 5.6, 5.7 und 8.0 können für Ihre Hostingserver verwendet werden. Der MySQL RP unterstützt keine caching_sha2_password-Authentifizierung. MySQL-8.0-Server müssen für die Verwendung von mysql_native_password konfiguriert werden.

## <a name="prepare-a-mysql-hosting-server"></a>Vorbereiten eines MySQL-Hostservers

### <a name="create-a-network-security-group-rule"></a>Erstellen einer Netzwerksicherheitsgruppen-Regel

Standardmäßig ist für MySQL kein öffentlicher Zugriff auf den virtuellen Hostcomputer konfiguriert. Damit der Azure Stack Hub-MySQL-Ressourcenanbieter eine Verbindung mit dem MySQL-Server herstellen und diesen verwalten kann, muss eine Eingangsregel für die Netzwerksicherheitsgruppe erstellt werden.

1. Navigieren Sie im Administratorportal zu der Ressourcengruppe, die beim Bereitstellen des MySQL-Servers erstellt wurde, und wählen Sie die Netzwerksicherheitsgruppe (**default-subnet-sg**) aus:

   ![Auswählen einer Netzwerksicherheitsgruppe im Azure Stack Hub-Administratorportal](media/azure-stack-tutorial-mysqlrp/img6.png)

2. Wählen Sie **Eingangssicherheitsregeln** und dann **Hinzufügen** aus.

    Geben Sie **3306** als **Zielportbereich** ein, und geben Sie optional eine Beschreibung in die Felder **Name** und **Beschreibung** ein.

   ![öffnen](media/azure-stack-tutorial-mysqlrp/img7.png)

3. Wählen Sie **Hinzufügen** aus, um das Dialogfeld für die Eingangssicherheitsregel zu schließen.

### <a name="configure-external-access-to-the-mysql-hosting-server"></a>Konfigurieren des externen Zugriffs auf den MySQL-Hostserver

Bevor der MySQL-Server als Azure Stack Hub-MySQL Server-Host hinzugefügt werden kann, muss der externe Zugriff aktiviert werden. Verwenden Sie Bitnami MySQL (verfügbar über den Azure Stack Hub-Marketplace) als Beispiel. Sie können die folgenden Schritte ausführen, um den externen Zugriff zu konfigurieren.

1. Melden Sie sich über einen SSH-Client (in diesem Beispiel wird [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) verwendet) von einem Computer, der auf die öffentliche IP-Adresse zugreifen kann, beim MySQL-Server an.

    Melden Sie sich über die öffentliche IP-Adresse mit dem Benutzernamen und dem zuvor erstellten Anwendungskennwort (ohne Sonderzeichen) bei der VM an.

   ![Linux-Anmeldung](media/azure-stack-tutorial-mysqlrp/bitnami1.png)

2. Verwenden Sie im Fenster des SSH-Clients den folgenden Befehl, um sicherzustellen, dass der Bitnami-Dienst aktiv ist und ausgeführt wird. Geben Sie das Kennwort für „bitnami“ erneut ein, wenn Sie dazu aufgefordert werden.

   `sudo service bitnami status`

   ![Überprüfen des Bitnami-Diensts](media/azure-stack-tutorial-mysqlrp/bitnami2.png)

3. Wenn der MySQL-Hostserver mindestens Version 8.0 hat, müssen Sie die Authentifizierungsmethode in **mysql_native_password** ändern. Ist die MySQL-Version niedriger als 8.0, kann dieser Schritt übersprungen werden.

   Verwenden Sie Bitnami MySQL als Beispiel. Die Konfigurationsdatei befindet sich unter **/opt/bitnami/mysql/conf/my.cnf**. Legen Sie die Eigenschaft **default_authentication_plugin** mit dem Wert **mysql_native_password** fest.
   ```
   [mysqld]
   default_authentication_plugin=mysql_native_password
   ```
   Starten Sie den Bitnami-Dienst neu, und stellen Sie sicher, dass er ordnungsgemäß ausgeführt wird.
   ```console
   sudo service bitnami restart
   sudo service bitnami status
   ```

4. Erstellen Sie ein Benutzerkonto mit Remotezugriff, das vom Azure Stack Hub-MySQL-Hostserver zum Herstellen der Verbindung mit MySQL verwendet werden soll.

    Führen Sie die folgenden Befehle aus, um sich mit dem Stammkennwort unter *~/bitnami_credentials* als Root-Benutzer bei MySQL anzumelden. Erstellen Sie einen neuen Administratorbenutzer, und ersetzen Sie *\<username\>* und *\<password\>* durch die für Ihre Umgebung erforderlichen Werte. In diesem Beispiel heißt der erstellte Benutzer **sqlsa**, und es wird ein sicheres Kennwort verwendet:

   ```sql
   mysql -u root -p
   create user <username>@'%' identified by '<password>';
   grant all privileges on *.* to <username>@'%' with grant option;
   flush privileges;
   ```

   ![Erstellen des Administratorbenutzers](media/azure-stack-tutorial-mysqlrp/bitnami3.png)

5. Stellen Sie sicher, dass **mysql_native_password** das Plug-In des erstellten SQL-Benutzers **sqlsa** ist, und beenden Sie dann den SSH-Client.
   
   ```sql
   SELECT user,host,plugin from mysql.user;
   ```
6. Notieren Sie sich die Informationen zum neuen MySQL-Benutzer.

   Dieser Benutzername und das Kennwort werden verwendet, während der Azure Stack Hub-Operator mit diesem MySQL-Server einen MySQL-Hostserver erstellt.


## <a name="connect-to-a-mysql-hosting-server"></a>Mit einem MySQL-Hostserver verbinden

Stellen Sie sicher, dass Sie über die Anmeldeinformationen für ein Konto mit Systemadministratorrechten verfügen.

> [!NOTE]
> Für MySQL 8.0 und höhere Versionen ist der Remotezugriff standardmäßig nicht aktiviert. Sie müssen ein neues Benutzerkonto erstellen und die Berechtigung für den Remotezugriff auf dieses Benutzerkonto gewähren, bevor Sie es als Hostserver hinzufügen.

Gehen Sie folgendermaßen vor, um einen Hostserver hinzuzufügen:

1. Melden Sie sich beim Azure Stack Hub-Administratorportal als Dienstadministrator an.
2. Wählen Sie **Alle Dienste** aus.
3. Wählen Sie unter der Kategorie **VERWALTUNGSRESSOURCEN** die Option **MySQL-Hostserver** >  **+Hinzufügen** aus. Es wird das Dialogfeld **Einen MySQL-Hostserver hinzufügen** geöffnet, wie in der folgenden Bildschirmaufnahme dargestellt.

   ![Konfigurieren eines MySQL-Hostservers](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

4. Geben Sie die Verbindungsdetails für Ihre MySQL Server-Instanz an.

   * Geben Sie für **MySQL-Hostservername** den vollqualifizierten Domänennamen (FQDN) oder eine gültige IPv4-Adresse an. Verwenden Sie nicht den Kurznamen des virtuellen Computers.
   * Der **Benutzername** des Standardadministrators für die verfügbaren Bitnami-MySQL-Images im Azure Stack Hub-Marketplace lautet *root*.
   * Sollte Ihnen das root-**Kennwort** nicht bekannt sein, erfahren Sie in der [Bitnami-Dokumentation](https://docs.bitnami.com/azure/faq/#how-to-find-application-credentials), wie Sie es erhalten.
   * Es ist keine MySQL-Standardinstanz angegeben, daher müssen Sie die **Größe des Hostservers in GB** eingeben. Geben Sie eine Größe ein, die in etwa der physischen Kapazität des Datenbankservers entspricht.
   * Behalten Sie die Standardeinstellung für **Abonnement** bei.
   * Für **Ressourcengruppe** können Sie eine neue Gruppe erstellen oder eine vorhandene Gruppe verwenden.

   > [!NOTE]
   > Wenn die MySQL-Instanz für den Azure Resource Manager-Mandanten und -Administrator zugänglich ist, kann sie vom Ressourcenanbieter gesteuert werden. Die MySQL-Instanz **muss** jedoch ausschließlich dem Ressourcenanbieter zugeordnet sein.

5. Wählen Sie **SKUs** aus, um das Dialogfeld **SKU erstellen** zu öffnen.

   ![Erstellen einer MySQL-SKU](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)

   Der **SKU-Name** sollte die Eigenschaften der SKU widerspiegeln, sodass Benutzer ihre Datenbanken für die entsprechende SKU bereitstellen können.

6. Klicken Sie auf **OK**, um die SKU zu erstellen.
   > [!NOTE]
   > Es kann bis zu einer Stunde dauern, bis SKUs im Portal angezeigt werden. Sie können erst dann eine Datenbank erstellen, wenn die SKU bereitgestellt wurde und ausgeführt wird.

7. Wählen Sie unter **MySQL-Hostserver hinzufügen** die Option **Erstellen** aus.

Wenn Sie Server hinzufügen, müssen Sie diese einer neuen oder vorhandenen SKU hinzufügen, um Dienstangebote zu unterscheiden. Beispielsweise können Sie eine MySQL Enterprise-Instanz haben, die erweiterte Datenbank- und automatische Sicherungen bietet. Sie können diesen leistungsfähigen Server für verschiedene Abteilungen in Ihrem Unternehmen reservieren.

## <a name="security-considerations-for-mysql"></a>Sicherheitsüberlegungen zu MySQL

Die folgenden Informationen gelten für den RP und MySQL-Hostserver:

* Stellen Sie sicher, dass alle Hostserver für die Kommunikation mit TLS 1.1 konfiguriert sind. Weitere Informationen finden Sie unter [Konfigurieren von MySQL für die Verwendung verschlüsselter Verbindungen](https://dev.mysql.com/doc/refman/5.7/en/using-encrypted-connections.html).
* Stellen Sie [Transparent Data Encryption](https://dev.mysql.com/doc/mysql-secure-deployment-guide/5.7/en/secure-deployment-data-encryption.html) bereit.
* Der MySQL RP unterstützt keine caching_sha2_password-Authentifizierung.

## <a name="increase-backend-database-capacity"></a>Erhöhen der Back-End-Datenbankkapazität

Sie können die Kapazität der Back-End-Datenbank erhöhen, indem Sie mehr MySQL-Server im Azure Stack Hub-Portal bereitstellen. Fügen Sie diese Server zu einer neuen oder bereits vorhandenen SKU hinzu. Wenn Sie einen Server zu einer bestehenden SKU hinzufügen, stellen Sie sicher, dass die Servereigenschaften mit denen der anderen Server in der SKU übereinstimmen.

## <a name="sku-notes"></a>Hinweise zu SKUs
Verwenden Sie einen SKU-Namen, der die Funktionen der Server in der SKU beschreibt, z.B. Kapazität und Leistung. Der Name soll Benutzer dabei unterstützen, ihre Datenbanken für die entsprechende SKU bereitzustellen. Beispielsweise können Sie SKU-Namen verwenden, um Dienstangebote anhand der folgenden Merkmale zu unterscheiden:
  
* Hohe Kapazität
* Hohe Leistung
* Hochverfügbarkeit

Als Best Practice sollten alle Hostserver in einer SKU die gleichen Ressourcen- und Leistungsmerkmale aufweisen.

SKUs können nicht bestimmten Benutzern oder Gruppen zugeordnet werden.

Um eine SKU zu bearbeiten, navigieren Sie zu **Alle Dienste** > **MySQL-Adapter** > **SKUs**. Wählen Sie die zu ändernde SKU aus, nehmen Sie alle notwendigen Änderungen vor, und klicken Sie dann auf **Speichern**, um die Änderungen zu speichern. 

Um eine nicht mehr benötigte SKU zu löschen, navigieren Sie zu **Alle Dienste** > **MySQL-Adapter** > **SKUs**. Klicken Sie mit der rechten Maustaste auf den Namen der SKU, und wählen Sie dann **Löschen** aus, um sie zu löschen.

> [!IMPORTANT]
> Es kann bis zu einer Stunde dauern, bis neue SKUs im Benutzerportal verfügbar sind.

## <a name="make-mysql-database-servers-available-to-your-users"></a>Freigeben der MySQL-Datenbankserver für Ihre Benutzer

Erstellen Sie Pläne und Angebote, um MySQL-Datenbankserver für Benutzer verfügbar zu machen. Fügen Sie dem Plan den Dienst Microsoft.MySqlAdapter hinzu. Außerdem müssen Sie ein neues Kontingent erstellen. MySQL ermöglicht nicht, die Größe von Datenbanken einzuschränken.

> [!IMPORTANT]
> Es kann bis zu zwei Stunden dauern, bis neue Kontingente im Benutzerportal verfügbar sind oder ein geändertes Kontingent erzwungen wird.

## <a name="next-steps"></a>Nächste Schritte

[Erstellen einer MySQL-Datenbank](azure-stack-mysql-resource-provider-databases.md)
