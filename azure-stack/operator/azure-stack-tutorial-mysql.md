---
title: Erstellen von hoch verfügbaren MySQL-Datenbanken
titleSuffix: Azure Stack Hub
description: Erfahren Sie, wie Sie mit Azure Stack Hub einen Hostcomputer für einen MySQL Server-Ressourcenanbieter sowie hoch verfügbare MySQL-Datenbanken erstellen.
author: BryanLa
ms.topic: article
ms.date: 10/07/2019
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 10/23/2019
ms.openlocfilehash: 327b63e2b21ea545fd6e0d175d531862ceeac0d6
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77696922"
---
# <a name="create-highly-available-mysql-databases"></a>Erstellen von hoch verfügbaren MySQL-Datenbanken

Als Azure Stack Hub-Betreiber können Sie virtuelle Servercomputer zum Hosten von MySQL Server-Datenbanken konfigurieren. Wenn ein MySQL-Cluster erstellt wurde und über Azure Stack Hub verwaltet wird, können Benutzer, die MySQL-Dienste abonniert haben, ganz einfach hochverfügbare MySQL-Datenbanken erstellen.

Dieser Artikel zeigt, wie Sie Elemente aus dem Azure Stack-Marketplace verwenden, um einen [Cluster vom Typ „MySQL with Replication“](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster) zu erstellen. Diese Lösung verwendet mehrere virtuelle Computer, um die Datenbanken aus dem Masterknoten in eine konfigurierbare Anzahl von Replikaten zu replizieren. Nach der Erstellung kann der Cluster als Azure Stack Hub-MySQL-Hostserver hinzugefügt werden und Benutzer können hochverfügbare MySQL-Datenbanken erstellen.

> [!IMPORTANT]
> Das Azure Stack-Marketplace-Element **MySQL with Replication** ist möglicherweise nicht für alle Azure-Cloudabonnementumgebungen verfügbar. Vergewissern Sie sich, dass das Marketplace-Element in Ihrem Abonnement verfügbar ist, bevor Sie mit dem Tutorial fortfahren.

Sie lernen Folgendes:

> [!div class="checklist"]
> * Erstellen eines MySQL Server-Clusters aus Marketplace-Elementen
> * Erstellen eines Azure Stack Hub-MySQL-Hostservers
> * Erstellen einer hochverfügbaren MySQL-Datenbank

Mithilfe von verfügbaren Elementen aus dem Azure Stack-Marketplace wird ein MySQL Server-Cluster mit drei virtuellen Computern erstellt.

Vergewissern Sie sich, dass der [MySQL Server-Ressourcenanbieter](azure-stack-mysql-resource-provider-deploy.md) erfolgreich installiert wurde und dass die folgenden Elemente im Azure Stack-Marketplace verfügbar sind, bevor Sie beginnen:

> [!IMPORTANT]
> Alle folgenden Elemente sind erforderlich, um den MySQL-Cluster zu erstellen.

- [MySQL with Replication:](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster) Dies ist die Bitnami-Lösungsvorlage, die für die Bereitstellung des MySQL-Clusters verwendet wird.
- [Debian 8 „Jessie“:](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.Debian) Debian 8 „Jessie“ mit Backportkernel für Microsoft Azure, bereitgestellt durch credativ. Debian GNU/Linux ist eine der am häufigsten verwendeten Linux-Distributionen.
- [Custom Script for Linux 2.0:](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft.custom-script-linux?tab=Overview) Custom Script Extension ist ein Tool, mit dem Sie nach der Bereitstellung Ihrer VMs Anpassungstasks für die VMs ausführen. Wenn diese Erweiterung zu einem virtuellen Computer hinzugefügt wird, kann sie Skripts aus Azure Storage herunterladen und auf dem virtuellen Computer ausführen. Custom Script Extension-Tasks können auch mithilfe von Azure PowerShell-Cmdlets und Azure Cross-Platform Command-Line Interface (xPlat CLI) automatisiert werden.
- VM Access for Linux Extension 1.4.7: Die VM Access-Erweiterung ermöglicht es Ihnen, das Kennwort, den SSH-Schlüssel oder die SSH-Konfigurationen zurückzusetzen, sodass Sie wieder auf Ihren virtuellen Computer zugreifen können. Sie können mit dieser Erweiterung auch einen neuen Benutzer mit Kennwort oder SSH-Schlüssel hinzufügen oder einen Benutzer löschen. Diese Erweiterung gilt für Linux-VMs.

Weitere Informationen zum Hinzufügen von Elementen zum Azure Stack-Marketplace finden Sie unter [Der Azure Stack-Marketplace – Übersicht](azure-stack-marketplace.md).

Darüber hinaus benötigen Sie einen SSH-Client wie [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html), um sich bei den virtuellen Linux-Computern anzumelden, nachdem diese bereitgestellt wurden.

## <a name="create-a-mysql-server-cluster"></a>Erstellen eines MySQL Server-Clusters

Führen Sie die Schritte in diesem Abschnitt aus, um den MySQL Server-Cluster mithilfe des Marketplace-Elements [MySQL with Replication](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster) bereitzustellen. Diese Vorlage stellt drei MySQL Server-Instanzen bereit, die in einem hoch verfügbaren MySQL-Cluster konfiguriert sind. Standardmäßig werden folgende Ressourcen erstellt:

- Ein virtuelles Netzwerk
- Eine Netzwerksicherheitsgruppe.
- Ein Speicherkonto
- Eine Verfügbarkeitsgruppe
- Drei Netzwerkschnittstellen (eine für jede der Standard-VMs)
- Eine öffentliche IP-Adresse (für die primäre MySQL-Cluster-VM)
- Drei Linux-VMs zum Hosten des MySQL-Clusters

1. 
   [!INCLUDE [azs-admin-portal](../includes/azs-user-portal.md)]

2. Wählen Sie im Dashboard die Option **Abonnement erwerben** aus, falls noch keine Abonnements zugewiesen wurden. Geben Sie auf dem Blatt einen Namen für das Abonnement ein, und wählen Sie anschließend ein Angebot aus. Wir empfehlen Ihnen, die MySQL-Clusterbereitstellung unter einem eigenen Abonnement anzuordnen, um das versehentliche Entfernen zu verhindern.

3. Wählen Sie **\+** **Ressource erstellen** > **Compute** und dann **MySQL with Replication** aus.

   ![Bereitstellung einer benutzerdefinierten Vorlage in Azure Stack Hub](media/azure-stack-tutorial-mysqlrp/img1.png)

4. Geben Sie auf der Seite **Grundlagen** grundlegende Informationen zur Bereitstellung an. Überprüfen Sie die Standardwerte, ändern Sie diese nach Bedarf, und wählen Sie **OK** aus.

    Mindestangaben:

   - Name der Bereitstellung (Standardwert: mymysql)
   - Kennwort des Root-Benutzers für die Anwendung. Geben Sie ein aus zwölf Zeichen bestehendes alphanumerisches Kennwort **ohne Sonderzeichen** ein.
   - Name der Anwendungsdatenbank (Standardwert: bitnami)
   - Anzahl der zu erstellenden MySQL-Datenbankreplikat-VMs (Standardwert: 2)
   - Wählen Sie das zu verwendende Abonnement aus.
   - Wählen Sie die zu verwendende Ressourcengruppe aus, oder erstellen Sie eine neue.
   - Wählen Sie den Standort aus (Standardwert: „lokal“ für ASDK).

     ![Grundlagen der Bereitstellung – Erstellen einer MySQL-Instanz mit Replikation](media/azure-stack-tutorial-mysqlrp/img2.png)

5. Geben Sie auf der Seite **Umgebungskonfiguration** die folgenden Informationen an, und wählen Sie anschließend **OK** aus.

   - Kennwort oder öffentlicher SSH-Schlüssel für die SSH-Authentifizierung (Secure Shell). Wenn Sie ein Kennwort verwenden, muss dieses Buchstaben und Ziffern enthalten. Sonderzeichen **können** verwendet werden.
   - VM-Größe (Standardwert: D1 v2 Standard-VMs)
   - Datenträgergröße in GB

     ![Umgebungskonfiguration – Erstellen einer MySQL-Instanz mit Replikation](media/azure-stack-tutorial-mysqlrp/img3.png)

6. Überprüfen Sie die **Zusammenfassung** der Bereitstellung. Optional können Sie die benutzerdefinierten Vorlagen und Parameter herunterladen. Wählen Sie dann **OK** aus.

   ![Zusammenfassung – Erstellen einer MySQL-Instanz mit Replikation](media/azure-stack-tutorial-mysqlrp/img4.png)

7. Wählen Sie auf der Seite **Kaufen** die Option **Erstellen** aus, um die Bereitstellung zu starten.

   ![Seite „Kaufen“ – Erstellen einer MySQL-Instanz mit Replikation](media/azure-stack-tutorial-mysqlrp/img5.png)

    > [!NOTE]
    > Die Bereitstellung dauert etwa eine Stunde. Stellen Sie sicher, dass die Bereitstellung abgeschlossen ist und der MySQL-Cluster vollständig konfiguriert wurde, bevor Sie fortfahren.

8. Nachdem alle Bereitstellungen erfolgreich abgeschlossen sind, überprüfen Sie die Elemente der Ressourcengruppe, und wählen Sie das **mysqlip**-Element für die öffentliche IP-Adresse aus. Notieren Sie sich die öffentliche IP-Adresse und den vollqualifizierten Domänennamen (FQDN) der öffentlichen IP-Adresse für den Cluster.

    Diese IP-Adresse müssen Sie an einen Azure Stack Hub-Betreiber weitergeben, damit dieser einen MySQL-Hostserver mit diesem MySQL-Cluster erstellen kann.

### <a name="create-a-network-security-group-rule"></a>Erstellen einer Netzwerksicherheitsgruppen-Regel

Standardmäßig ist für MySQL kein öffentlicher Zugriff auf den virtuellen Hostcomputer konfiguriert. Damit der Azure Stack Hub-MySQL-Ressourcenanbieter eine Verbindung mit dem MySQL-Cluster herstellen und diesen verwalten kann, muss eine Eingangsregel für die Netzwerksicherheitsgruppe erstellt werden.

1. Navigieren Sie im Administratorportal zu der Ressourcengruppe, die beim Bereitstellen des MySQL-Clusters erstellt wurde, und wählen Sie die Netzwerksicherheitsgruppe (**default-subnet-sg**) aus:

   ![Auswählen einer Netzwerksicherheitsgruppe im Azure Stack Hub-Administratorportal](media/azure-stack-tutorial-mysqlrp/img6.png)

2. Wählen Sie **Eingangssicherheitsregeln** und dann **Hinzufügen** aus.

    Geben Sie **3306** als **Zielportbereich** ein, und geben Sie optional eine Beschreibung in die Felder **Name** und **Beschreibung** ein.

   ![öffnen](media/azure-stack-tutorial-mysqlrp/img7.png)

3. Wählen Sie **Hinzufügen** aus, um das Dialogfeld für die Eingangssicherheitsregel zu schließen.

### <a name="configure-external-access-to-the-mysql-cluster"></a>Konfigurieren des externen Zugriffs auf den MySQL-Cluster

Bevor der MySQL-Cluster als Azure Stack Hub-MySQL Server-Host hinzugefügt werden kann, muss der externe Zugriff aktiviert werden.

1. Melden Sie sich über einen SSH-Client (in diesem Beispiel wird [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) verwendet) von einem Computer, der auf die öffentliche IP-Adresse zugreifen kann, beim primären MySQL-Computer an. Der Name der primären MySQL-VM endet in der Regel auf **0**, und der VM ist eine öffentliche IP-Adresse zugewiesen.

    Melden Sie sich über die öffentliche IP-Adresse mit dem Benutzernamen **bitnami** und dem zuvor erstellten Anwendungskennwort (ohne Sonderzeichen) bei der VM an.

   ![Linux-Anmeldung](media/azure-stack-tutorial-mysqlrp/bitnami1.png)

2. Verwenden Sie im Fenster des SSH-Clients den folgenden Befehl, um sicherzustellen, dass der Bitnami-Dienst aktiv ist und ausgeführt wird. Geben Sie das Kennwort für „bitnami“ erneut ein, wenn Sie dazu aufgefordert werden.

   `sudo service bitnami status`

   ![Überprüfen des Bitnami-Diensts](media/azure-stack-tutorial-mysqlrp/bitnami2.png)

3. Erstellen Sie ein Benutzerkonto mit Remotezugriff, das vom Azure Stack Hub-MySQL-Hostserver zum Herstellen der Verbindung mit MySQL verwendet werden soll, und beenden Sie dann den SSH-Client.

    Führen Sie die folgenden Befehle aus, um sich mit dem zuvor erstellten Stammkennwort als Root-Benutzer bei MySQL anzumelden. Erstellen Sie einen neuen Administratorbenutzer, und ersetzen Sie *\<username\>* und *\<password\>* durch die für Ihre Umgebung erforderlichen Werte. In diesem Beispiel heißt der erstellte Benutzer **sqlsa**, und es wird ein sicheres Kennwort verwendet:

   ```mysql
   mysql -u root -p
   create user <username>@'%' identified by '<password>';
   grant all privileges on *.* to <username>@'%' with grant option;
   flush privileges;
   ```

   ![Erstellen des Administratorbenutzers](media/azure-stack-tutorial-mysqlrp/bitnami3.png)

4. Notieren Sie sich die Informationen zum neuen MySQL-Benutzer.

    Sie müssen diesen Benutzernamen und das Kennwort sowie die öffentliche IP-Adresse oder den vollqualifizierten Domänennamen der öffentlichen IP-Adresse für den Cluster einem Azure Stack Hub-Betreiber mitteilen, damit dieser mit diesem MySQL-Cluster einen MySQL-Hostserver erstellen kann.

## <a name="create-an-azure-stack-hub-mysql-hosting-server"></a>Erstellen eines Azure Stack Hub-MySQL-Hostservers

Nachdem der MySQL Server-Cluster erstellt und ordnungsgemäß konfiguriert wurde, muss ein Azure Stack Hub-Betreiber einen Azure Stack Hub-MySQL-Hostserver erstellen, um Benutzern die zusätzliche Kapazität zur Verfügung zu stellen, damit diese Datenbanken erstellen können.

Verwenden Sie die öffentliche IP-Adresse oder den vollständigen vollqualifizierten Domänennamen für die öffentliche IP-Adresse der primären MySQL-Cluster-VM, die bzw. der zuvor beim Erstellen der Ressourcengruppe (**mysqlip**) für den MySQL-Cluster erfasst wurde. Darüber hinaus muss der Betreiber die Anmeldeinformationen für die MySQL Server-Authentifizierung kennen, die Sie für den Remotezugriff auf die MySQL-Clusterdatenbank erstellt haben.

> [!NOTE]
> Dieser Schritt muss im Azure Stack Hub-Administratorportal von einem Azure Stack Hub-Betreiber ausgeführt werden.

Mit der öffentlichen IP-Adresse des MySQL-Clusters und den Anmeldeinformationen für die MySQL-Authentifizierung kann ein Azure Stack Hub-Betreiber nun [unter Verwendung des neuen MySQL-Clusters einen MySQL-Hostserver erstellen](azure-stack-mysql-resource-provider-hosting-servers.md#connect-to-a-mysql-hosting-server).

Darüber hinaus müssen Sie Pläne und Angebote erstellt haben, um Benutzern die Erstellung von MySQL-Datenbanken zu ermöglichen. Ein Bediener muss einem Plan den **Microsoft.MySqlAdapter**-Dienst hinzufügen und ein neues Kontingent speziell für hoch verfügbare Datenbanken erstellen. Weitere Informationen zum Erstellen von Plänen finden Sie unter [Übersicht über Dienste, Pläne, Angebote und Abonnements](service-plan-offer-subscription-overview.md).

> [!TIP]
> Der Dienst **Microsoft.MySqlAdapter** kann erst dann zu Plänen hinzugefügt werden, wenn der [MySQL Server-Ressourcenanbieter bereitgestellt wurde](azure-stack-mysql-resource-provider-deploy.md).

## <a name="create-a-highly-available-mysql-database"></a>Erstellen einer hoch verfügbaren MySQL-Datenbank

Nachdem der MySQL-Cluster durch einen Azure Stack Hub-Betreiber erstellt, konfiguriert und als Azure Stack Hub-MySQL-Hostserver hinzugefügt wurde, können Mandantenbenutzer mit einem Abonnement, das MySQL Server-Datenbankfunktionen enthält, hochverfügbare MySQL-Datenbanken erstellen. Dazu müssen die Schritte in diesem Abschnitt ausgeführt werden.

> [!NOTE]
> Führen Sie diese Schritte als Mandantenbenutzer im Azure Stack Hub-Benutzerportal mit einem Abonnement aus, das MySQL Server-Funktionen (Microsoft.MySQLAdapter-Dienst) bereitstellt.

1. 
   [!INCLUDE [azs-user-portal](../includes/azs-user-portal.md)]

2. Wählen Sie **\+** **Ressource erstellen** > **Daten \+ Speicher** und dann **MySQL-Datenbank** aus.

    Geben Sie die erforderlichen Datenbankeigenschaften – Name, Sortierung – sowie das Abonnement und den Standort für die Bereitstellung an.

    ![Erstellen einer MySQL-Datenbank im Azure Stack Hub-Benutzerportal](./media/azure-stack-tutorial-mysqlrp/createdb1.png)

3. Klicken Sie auf **SKU**, und wählen Sie eine geeignete SKU für den MySQL-Hostserver aus. In diesem Beispiel hat der Azure Stack Hub-Betreiber die SKU **MySQL-HA** erstellt, um die Hochverfügbarkeit für MySQL-Clusterdatenbanken zu unterstützen.

   ![Auswählen einer SKU im Azure Stack Hub-Benutzerportal](./media/azure-stack-tutorial-mysqlrp/createdb2.png)

4. Klicken Sie auf **Anmelden** > **Neue Anmeldung erstellen**, und geben Sie die Anmeldeinformationen für die MySQL-Authentifizierung ein, die für die neue Datenbank verwendet werden sollen. Wenn Sie fertig sind, wählen Sie **OK** und dann **Erstellen** aus, um den Prozess der Datenbankbereitstellung zu starten.

   ![Hinzufügen der Anmeldung im Azure Stack Hub-Benutzerportal](./media/azure-stack-tutorial-mysqlrp/createdb3.png)

5. Wenn die Bereitstellung der MySQL-Datenbank erfolgreich abgeschlossen wurde, überprüfen Sie die Datenbankeigenschaften, um die Verbindungszeichenfolge zu ermitteln, die zum Herstellen der Verbindung mit der neuen hoch verfügbaren Datenbank verwendet werden soll.

   ![Anzeigen der Verbindungszeichenfolge im Azure Stack Hub-Benutzerportal](./media/azure-stack-tutorial-mysqlrp/createdb4.png)

## <a name="next-steps"></a>Nächste Schritte

[Aktualisieren des MySQL-Ressourcenanbieters](azure-stack-mysql-resource-provider-update.md)
