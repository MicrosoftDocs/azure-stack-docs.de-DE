---
title: Bereitstellen einer Vorlage über das Portal in Azure Stack Hub
description: Erfahren Sie, wie Sie mit dem Azure Stack Hub-Portal eine Vorlage bereitstellen.
author: mattbriggs
ms.topic: article
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: ffcc181f0d5693b1c525f871ff5ba92f0b508b04
ms.sourcegitcommit: cad40ae88212cc72f40c84a1c88143ea0abb65ef
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84112129"
---
# <a name="deploy-a-template-using-the-portal-in-azure-stack-hub"></a>Bereitstellen einer Vorlage über das Portal in Azure Stack Hub

Sie können das Portal zum Bereitstellen von Azure Resource Manager-Vorlagen (ARM) für Azure Stack Hub verwenden.

## <a name="to-deploy-a-template"></a>So stellen Sie eine Vorlage bereit

1. Melden Sie sich am Portal an, und wählen Sie **+Ressource erstellen** und dann **Benutzerdefiniert** aus.

   ![Erstellen einer Ressource im Azure Stack Hub-Portal](media/azure-stack-deploy-template-portal/template-deploy1.png)

1. Wählen Sie **Vorlagenbereitstellung** aus.

   ![Bereitstellen der Vorlage im Azure Stack Hub-Portal](media/azure-stack-deploy-template-portal/template-deploy2.png)

1. Wählen Sie **Vorlage bearbeiten**, und fügen Sie Ihren JSON-Vorlagencode in das Codefenster ein. Wählen Sie **Speichern** aus.

   ![Bearbeiten der Vorlage im Azure Stack Hub-Portal](media/azure-stack-deploy-template-portal/template-deploy3.png)

1. Wählen Sie **Parameter bearbeiten**, geben Sie Werte für die angezeigten Parameter an, und wählen Sie dann **OK**.

   ![Bearbeiten von Parametern im Azure Stack Hub-Portal](media/azure-stack-deploy-template-portal/template-deploy4.png)

1. Wählen Sie **Abonnement** aus. Wählen Sie das Abonnement, das Sie verwenden möchten, und wählen Sie dann **OK**.

   ![Auswählen des Abonnements im Azure Stack Hub-Portal](media/azure-stack-deploy-template-portal/template-deploy5.png)

1. Wählen Sie **Ressourcengruppe** aus. Wählen Sie eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine neue Ressourcengruppe. Wählen Sie dann **OK**.

   ![Auswählen der Ressourcengruppe im Azure Stack Hub-Portal](media/azure-stack-deploy-template-portal/template-deploy6.png)

1. Klicken Sie auf **Erstellen**. Eine neue Kachel auf dem Dashboard verfolgt den Fortschritt Ihrer Vorlagenbereitstellung.

   ![Erstellen einer Vorlage im Azure Stack Hub-Portal](media/azure-stack-deploy-template-portal/template-deploy7.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Bereitstellen von Vorlagen finden Sie im folgenden Artikel:

- [Bereitstellen von Vorlagen mit PowerShell](azure-stack-deploy-template-powershell.md)
