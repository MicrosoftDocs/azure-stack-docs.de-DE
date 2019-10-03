---
title: Bereitstellen einer Vorlage über das Portal in Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit dem Azure Stack-Portal eine Vorlage bereitstellen.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/13/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: bbbbbc5548397f82752a43c7a1aaca7b62151b75
ms.sourcegitcommit: e8aa26b078a9bab09c8fafd888a96785cc7abb4d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71708994"
---
# <a name="deploy-a-template-using-the-portal-in-azure-stack"></a>Bereitstellen einer Vorlage über das Portal in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Sie können das Portal zum Bereitstellen von Azure Resource Manager-Vorlagen (ARM) für Azure Stack verwenden.

## <a name="to-deploy-a-template"></a>So stellen Sie eine Vorlage bereit

1. Melden Sie sich am Portal an, und wählen Sie **+Ressource erstellen** und dann **Benutzerdefiniert** aus.

   ![Erstellen einer Ressource im Azure Stack-Portal](media/azure-stack-deploy-template-portal/template-deploy1.png)

1. Wählen Sie **Vorlagenbereitstellung** aus.

   ![Bereitstellen der Vorlage im Azure Stack-Portal](media/azure-stack-deploy-template-portal/template-deploy2.png)

1. Wählen Sie **Vorlage bearbeiten**, und fügen Sie Ihren JSON-Vorlagencode in das Codefenster ein. Wählen Sie **Speichern** aus.

   ![Bearbeiten der Vorlage im Azure Stack-Portal](media/azure-stack-deploy-template-portal/template-deploy3.png)

1. Wählen Sie **Parameter bearbeiten**, geben Sie Werte für die angezeigten Parameter an, und wählen Sie dann **OK**.

   ![Bearbeiten von Parametern im Azure Stack-Portal](media/azure-stack-deploy-template-portal/template-deploy4.png)

1. Wählen Sie **Abonnement** aus. Wählen Sie das Abonnement, das Sie verwenden möchten, und wählen Sie dann **OK**.

   ![Auswählen des Abonnements im Azure Stack-Portal](media/azure-stack-deploy-template-portal/template-deploy5.png)

1. Wählen Sie **Ressourcengruppe** aus. Wählen Sie eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine neue Ressourcengruppe. Wählen Sie dann **OK**.

   ![Auswählen der Ressourcengruppe im Azure Stack-Portal](media/azure-stack-deploy-template-portal/template-deploy6.png)

1. Klicken Sie auf **Erstellen**. Eine neue Kachel auf dem Dashboard verfolgt den Fortschritt Ihrer Vorlagenbereitstellung.

   ![Erstellen einer Vorlage im Azure Stack-Portal](media/azure-stack-deploy-template-portal/template-deploy7.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Bereitstellen von Vorlagen finden Sie im folgenden Artikel:

- [Bereitstellen von Vorlagen mit PowerShell](azure-stack-deploy-template-powershell.md)
