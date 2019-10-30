---
title: Hinzufügen des MySQL-Hostservers in Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie MySQL-Hostserver für die Bereitstellung über den MySQL-Adapterressourcenanbieter hinzufügen.
services: azure-stack
documentationCenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 02/28/2019
ms.openlocfilehash: 632528519bbca9286ab72fb806ee756ccdb6a166
ms.sourcegitcommit: a23b80b57668615c341c370b70d0a106a37a02da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/21/2019
ms.locfileid: "72682210"
---
# <a name="add-mysql-hosting-servers-in-azure-stack"></a>Hinzufügen für MySQL-Hostserver in Azure Stack

Sie können eine MySQL-Hostserverinstanz auf einem virtuellen Computer (VM) in [Azure Stack](azure-stack-overview.md) oder auf einer VM außerhalb Ihrer Azure Stack-Umgebung hosten, solange sich der MySQL-Ressourcenanbieter mit der Instanz verbinden kann.

> [!NOTE]
> Der MySQL-Ressourcenanbieter sollte im Standardanbieterabonnement erstellt werden, während MySQL-Hostingserver in einem abrechenbaren Benutzerabonnement erstellt werden sollten. Der Server des Ressourcenanbieters sollte nicht zum Hosten von Benutzerdatenbanken verwendet werden.

Die MySQL-Versionen 5.6, 5.7 und 8.0 können für Ihre Hostingserver verwendet werden. Der MySQL RP unterstützt keine caching_sha2_password-Authentifizierung. Diese Unterstützung wird im nächsten Release hinzugefügt. MySQL-8.0-Server müssen für die Verwendung von mysql_native_password konfiguriert werden. MariaDB wird ebenfalls unterstützt.

## <a name="connect-to-a-mysql-hosting-server"></a>Mit einem MySQL-Hostserver verbinden

Stellen Sie sicher, dass Sie über die Anmeldeinformationen für ein Konto mit Systemadministratorrechten verfügen. Gehen Sie folgendermaßen vor, um einen Hostserver hinzuzufügen:

1. Anmelden beim Azure Stack-Administratorportal als Dienstadministrator
2. Wählen Sie **Alle Dienste** aus.
3. Wählen Sie unter der Kategorie **VERWALTUNGSRESSOURCEN** die Option **MySQL-Hostserver** >  **+Hinzufügen** aus. Es wird das Dialogfeld **Einen MySQL-Hostserver hinzufügen** geöffnet, wie in der folgenden Bildschirmaufnahme dargestellt.

   ![Konfigurieren eines MySQL-Hostservers](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

4. Geben Sie die Verbindungsdetails für Ihre MySQL Server-Instanz an.

   * Geben Sie für **MySQL-Hostservername** den vollqualifizierten Domänennamen (FQDN) oder eine gültige IPv4-Adresse an. Verwenden Sie nicht den Kurznamen des virtuellen Computers.
   * Der **Benutzername** des Standardadministrators für die verfügbaren Bitnami-MySQL-Images im Azure Stack-Marketplace lautet *root*.
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

* Stellen Sie sicher, dass alle Hostserver für die Kommunikation mit TLS 1.2 konfiguriert sind. Weitere Informationen finden Sie unter [Konfigurieren von MySQL für die Verwendung verschlüsselter Verbindungen](https://dev.mysql.com/doc/refman/5.7/en/using-encrypted-connections.html).
* Stellen Sie [Transparent Data Encryption](https://dev.mysql.com/doc/mysql-secure-deployment-guide/5.7/en/secure-deployment-data-encryption.html) bereit.
* Der MySQL RP unterstützt keine caching_sha2_password-Authentifizierung.

## <a name="increase-backend-database-capacity"></a>Erhöhen der Back-End-Datenbankkapazität

Sie können die Kapazität der Back-End-Datenbank erhöhen, indem Sie mehr MySQL-Server im Azure Stack-Portal bereitstellen. Fügen Sie diese Server zu einer neuen oder bereits vorhandenen SKU hinzu. Wenn Sie einen Server zu einer bestehenden SKU hinzufügen, stellen Sie sicher, dass die Servereigenschaften mit denen der anderen Server in der SKU übereinstimmen.

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
