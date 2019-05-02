---
title: Bereitstellen von Vorlagen über das Portal in Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit dem Azure Stack-Portal Vorlagen bereitstellen.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 01/05/2019
ms.date: 02/18/2019
ms.author: v-jay
ms.reviewer: unknown
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: b9adac3f2f56093c3559570aab4e905eb047ccd2
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "64310392"
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>Bereitstellen von Vorlagen mit dem Azure Stack-Portal

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Sie können das Portal zum Bereitstellen von Azure Resource Manager-Vorlagen (ARM) für Azure Stack verwenden.

## <a name="to-deploy-a-template"></a>So stellen Sie eine Vorlage bereit

1. Melden Sie sich am Portal an, und wählen Sie **+Ressource erstellen** und dann **Benutzerdefiniert** aus.
2. Wählen Sie **Vorlagenbereitstellung** aus.
3. Wählen Sie **Vorlage bearbeiten**, und fügen Sie Ihren JSON-Vorlagencode in das Codefenster ein. Wählen Sie **Speichern** aus.
4. Wählen Sie **Parameter bearbeiten**, geben Sie Werte für die angezeigten Parameter an, und wählen Sie dann **OK**.
5. Wählen Sie **Abonnement** aus. Wählen Sie das Abonnement, das Sie verwenden möchten, und wählen Sie dann **OK**.
6. Wählen Sie **Ressourcengruppe** aus. Wählen Sie eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine neue Ressourcengruppe. Wählen Sie dann **OK**.
7. Klicken Sie auf **Erstellen**. Eine neue Kachel auf dem Dashboard verfolgt den Fortschritt Ihrer Vorlagenbereitstellung.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Bereitstellen von Vorlagen finden Sie im folgenden Artikel:

- [Bereitstellen von Vorlagen mit PowerShell](azure-stack-deploy-template-powershell.md)
