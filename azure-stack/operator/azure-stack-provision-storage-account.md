---
title: Erstellen von Speicherkonten in Azure Stack | Microsoft-Dokumentation
titleSuffix: Azure Stack
description: Hier erfahren Sie, wie Sie Speicherkonten in Azure Stack erstellen.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/2/2019
ms.author: mabrigg
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: 449d9e39b650e6f7ccd91f4703709ea033e7a5dc
ms.sourcegitcommit: ca358ea5c91a0441e1d33f540f6dbb5b4d3c92c5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73802335"
---
# <a name="create-storage-accounts-in-azure-stack"></a>Erstellen von Speicherkonten in Azure Stack

Speicherkonten in Azure Stack enthalten Blob- und Tabellenspeicherdienste sowie den eindeutigen Namespace für Ihre Speicherdatenobjekte. Standardmäßig sind die Daten in Ihrem Konto nur für Sie als Speicherkontobesitzer verfügbar.

1. Melden Sie sich auf dem Azure Stack-PoC-Computer unter `https://adminportal.local.azurestack.external` als [Administrator](../asdk/asdk-connect.md) an, und klicken Sie auf **+ Eine Ressource erstellen** > **Daten und Speicher** > **Speicherkonto**.

   ![Erstellen eines Speicherkontos im Azure Stack-Administratorportal](media/azure-stack-provision-storage-account/image01.png)

2. Geben Sie auf dem Blatt **Speicherkonto erstellen** einen Namen für das Speicherkonto ein. Erstellen Sie eine neue **Ressourcengruppe**, oder wählen Sie eine vorhandene aus, und klicken Sie auf **Erstellen**, um das Speicherkonto zu erstellen.

   ![Überprüfen Ihres Speicherkontos im Azure Stack-Administratorportal](media/azure-stack-provision-storage-account/image02.png)

3. Klicken sie zum Anzeigen des neuen Speicherkontos auf **Alle Ressourcen**, suchen Sie dann nach dem Speicherkonto, und klicken Sie auf seinen Namen.

    ![Der Name Ihres Speicherkontos im Azure Stack-Administratorportal](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>Nächste Schritte

- [Verwenden von Azure-Ressourcen-Manager-Vorlagen](../user/azure-stack-arm-templates.md)
- [Informationen zu Azure-Speicherkonten](/azure/storage/common/storage-create-storage-account)
- [Herunterladen des Azure Stack-Überprüfungshandbuchs für mit Azure konsistentem Speicher](https://aka.ms/azurestacktp1doc)
