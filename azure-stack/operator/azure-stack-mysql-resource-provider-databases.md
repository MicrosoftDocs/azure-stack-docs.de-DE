---
title: Erstellen von MySQL-Datenbanken in Azure Stack Hub
description: Erfahren Sie, wie Sie MySQL-Datenbanken, die mit dem Ressourcenanbieter „MySQL Adapter“ bereitgestellt wurden, in Azure Stack Hub erstellen und verwalten.
author: bryanla
ms.topic: article
ms.date: 1/22/2020
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 10/16/2019
ms.openlocfilehash: 2da344a5699357d008fcc4494992cfcc67047971
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "77698877"
---
# <a name="create-mysql-databases-in-azure-stack-hub"></a>Erstellen von MySQL-Datenbanken in Azure Stack Hub
Ein Azure Stack Hub-Benutzer, der ein Angebot abonniert hat, das den MySQL-Datenbankdienst enthält, kann MySQL-Self-Service-Datenbanken im Benutzerportal erstellen und verwalten.

## <a name="create-a-mysql-database"></a>Erstellen einer MySQL-Datenbank

1. Melden Sie sich beim Azure Stack Hub-Benutzerportal an.
2. Wählen Sie **+Ressource erstellen** > **Daten und Speicher** > **MySQL-Datenbank** > **Hinzufügen** aus.
3. Geben Sie unter **MySQL-Datenbank erstellen** den Datenbanknamen ein, und konfigurieren Sie die anderen Einstellungen für Ihre Umgebung.

    ![Erstellen einer MySQL-Testdatenbank](./media/azure-stack-mysql-rp-deploy/mysql-create-db-a.png)

4. Klicken Sie unter **Datenbank erstellen** auf **SKU**. Wählen Sie unter **Select a MySQL SKU** (MySQL-SKU auswählen) die SKU für Ihre Datenbank aus.

    ![Auswählen einer MySQL-SKU](./media/azure-stack-mysql-rp-deploy/mysql-select-sku.png)

    >[!Note]
    >Wenn Hostserver zu Azure Stack Hub hinzugefügt werden, wird ihnen eine SKU zugewiesen. Datenbanken werden im Pool von Hostservern in einer SKU erstellt.

5. Klicken Sie unter **Anmeldung** auf ***Erforderliche Einstellungen konfigurieren***.
6. Unter **Anmeldung auswählen** können Sie eine bereits vorhandene Anmeldung auswählen oder auf **+ Neue Anmeldung erstellen** klicken, um eine neue Anmeldung einzurichten.  Geben Sie einen Namen für **Datenbankanmeldung** sowie ein **Kennwort** ein, und klicken Sie anschließend auf **OK**.

    ![Erstellen einer neuen Datenbankanmeldung](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    >[!NOTE]
    >Der Name für die Datenbankanmeldung darf in MySQL 5.7 maximal 32 Zeichen lang sein. In früheren Versionen durften sie 16 Zeichen nicht überschreiten.

7. Klicken Sie auf **Erstellen**, um die Einrichtung der Datenbank abzuschließen.

Notieren Sie sich nach der Bereitstellung der Datenbank die **Verbindungszeichenfolge** aus der **Zusammenfassung**. Diese Zeichenfolge kann in jeder Anwendung verwendet werden, die auf die MySQL-Datenbank zugreifen muss.

![Abrufen der Verbindungszeichenfolge für die MySQL-Datenbank](./media/azure-stack-mysql-rp-deploy/mysql-db-created-a.png)

## <a name="update-the-administrative-password"></a>Aktualisieren des Administratorkennworts

Das Kennwort kann in der MySQL Server-Instanz geändert werden.

1. Wählen Sie **VERWALTUNGSRESSOURCEN** > **MySQL-Hostserver** aus. Wählen Sie den Hostserver aus.
2. Wählen Sie unter **Einstellungen** die Option **Kennwort** aus.
3. Geben Sie unter **Kennwort** das neue Kennwort ein, und klicken Sie anschließend auf **Speichern**.

![Aktualisieren des Administratorkennworts](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über das [Anbieten von hochverfügbaren MySQL-Datenbanken](azure-stack-tutorial-mysql.md).
