---
title: Erstellen von Speicherkonten in Azure Stack Hub
titleSuffix: Azure Stack Hub
description: Hier erfahren Sie, wie Sie Speicherkonten in Azure Stack Hub erstellen.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: inhenkel
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: bc638be874518bec2e7b8a6db9935f93aa33e30f
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/20/2020
ms.locfileid: "77509686"
---
# <a name="create-storage-accounts-in-azure-stack-hub"></a>Erstellen von Speicherkonten in Azure Stack Hub

Speicherkonten in Azure Stack Hub enthalten Blob- und Tabellenspeicherdienste sowie den eindeutigen Namespace für Ihre Speicherdatenobjekte. Standardmäßig sind die Daten in Ihrem Konto nur für Sie als Speicherkontobesitzer verfügbar.

1. Melden Sie sich auf dem Azure Stack Hub-PoC-Computer unter `https://adminportal.local.azurestack.external` als [Administrator](../asdk/asdk-connect.md) an, und klicken Sie auf **+ Eine Ressource erstellen** > **Daten und Speicher** > **Speicherkonto**.

   ![Erstellen eines Speicherkontos im Azure Stack Hub-Administratorportal](media/azure-stack-provision-storage-account/image01.png)

2. Geben Sie auf dem Blatt **Speicherkonto erstellen** einen Namen für das Speicherkonto ein. Erstellen Sie eine neue **Ressourcengruppe**, oder wählen Sie eine vorhandene aus, und klicken Sie auf **Erstellen**, um das Speicherkonto zu erstellen.

   ![Überprüfen eines Speicherkontos im Azure Stack Hub-Administratorportal](media/azure-stack-provision-storage-account/image02.png)

3. Klicken sie zum Anzeigen des neuen Speicherkontos auf **Alle Ressourcen**, suchen Sie dann nach dem Speicherkonto, und klicken Sie auf seinen Namen.

    ![Der Name Ihres Speicherkontos im Azure Stack Hub-Administratorportal](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>Nächste Schritte

- [Verwenden von Azure Resource Manager-Vorlagen](../user/azure-stack-arm-templates.md)
- [Informationen zu Azure-Speicherkonten](/azure/storage/common/storage-create-storage-account)
- [Herunterladen des Azure Stack Hub-Überprüfungshandbuchs für mit Azure konsistentem Speicher](https://aka.ms/azurestacktp1doc)
