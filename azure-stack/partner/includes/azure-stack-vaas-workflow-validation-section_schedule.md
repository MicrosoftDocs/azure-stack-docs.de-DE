---
author: mattbriggs
ms.service: azure-stack
ms.topic: include
ms.date: 03/04/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ms.openlocfilehash: e9d59fadb9cbfeb7463f799bdab2e6da49208fe9
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "63447778"
---
In den Validierungsworkflows werden beim **Planen** eines Tests die allgemeinen Parameter auf der Workflowebene verwendet, die Sie bei der Workflowerstellung angegeben haben (siehe [Allgemeine Workflowparameter für Validation-as-a-Service in Azure Stack](../azure-stack-vaas-parameters.md)). Sollt einer der Testparameterwerte ungültig werden, muss er gemäß den Anweisungen unter [Ändern von Workflowparametern](../azure-stack-vaas-monitor-test.md#change-workflow-parameters) erneut angegeben werden.

> [!NOTE]
> Wenn Sie einen Validierungstest für eine bereits vorhandene Instanz planen, wird die alte Instanz im Portal durch eine neu erstellte Instanz ersetzt. Die Protokolle für die alte Instanz bleiben erhalten, können aber nicht über das Portal verwendet werden.  
Nach erfolgreicher Ausführung eines Tests wird die **Planungsoption** deaktiviert.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](azure-stack-vaas-workflow-step_select-agent.md)]

1. Klicken Sie im Kontextmenü auf **Planen**, um eine Eingabeaufforderung für die Planung der Testinstanz zu öffnen.

1. Überprüfen Sie die Testparameter, und klicken Sie anschließend auf **Absenden**, um die Testausführung zu planen.