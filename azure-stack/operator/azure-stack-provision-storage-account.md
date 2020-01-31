---
title: Erstellen von Speicherkonten in Azure Stack Hub
titleSuffix: Azure Stack Hub
description: Hier erfahren Sie, wie Sie Speicherkonten in Azure Stack Hub erstellen.
author: mattbriggs
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: mabrigg
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: d130b47d9c5c51d2d0cfa75f778a1facf829ab3b
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76881501"
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
