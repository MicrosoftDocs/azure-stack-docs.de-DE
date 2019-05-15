---
title: Speicherkonten in Azure Stack | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie ein Azure Stack-Speicherkonto erstellen.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: mabrigg
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: ec3865a8956dcf0cce700ed309e0770527141afe
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "64308772"
---
# <a name="storage-accounts-in-azure-stack"></a>Speicherkonten in Azure Stack
Speicherkonten enthalten Blob-und Tabellenspeicherdienste und den eindeutigen Namespace für Ihre Speicherdatenobjekte. Standardmäßig sind die Daten in Ihrem Konto nur für Sie als Speicherkontobesitzer verfügbar.

1. Melden Sie sich auf dem Azure Stack-PoC-Computer unter `https://adminportal.local.azurestack.external` als [Administrator](../asdk/asdk-connect.md) an, und klicken Sie auf **+ Eine Ressource erstellen** > **Daten und Speicher** > **Speicherkonto**.

   ![](media/azure-stack-provision-storage-account/image01.png)
2. Geben Sie auf dem Blatt **Speicherkonto erstellen** einen Namen für das Speicherkonto ein. Erstellen Sie eine neue **Ressourcengruppe**, oder wählen Sie eine vorhandene aus, und klicken Sie auf **Erstellen**, um das Speicherkonto zu erstellen.

   ![](media/azure-stack-provision-storage-account/image02.png)
3. Klicken sie zum Anzeigen des neuen Speicherkontos auf **Alle Ressourcen**, suchen Sie dann nach dem Speicherkonto, und klicken Sie auf seinen Namen.

    ![](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>Nächste Schritte
[Verwenden von Azure Resource Manager-Vorlagen](../user/azure-stack-arm-templates.md)

[Weitere Informationen zu Azure Storage-Konten]((/azure/storage/common/storage-create-storage-account)

[Herunterladen des Azure Stack-Überprüfungshandbuchs für mit Azure konsistentem Speicher](https://aka.ms/azurestacktp1doc)
