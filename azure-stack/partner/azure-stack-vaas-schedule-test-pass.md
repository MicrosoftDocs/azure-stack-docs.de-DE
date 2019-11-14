---
title: Planen des ersten Tests mithilfe des Azure Stack-Validation-as-a-Service-Portals | Microsoft-Dokumentation
description: Planen des ersten Tests mithilfe des Azure Stack-Validation-as-a-Service-Portals
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 8843b6791876b4e0362d7aee3aa738453a8c3b88
ms.sourcegitcommit: 102ef41963b5d2d91336c84f2d6af3fdf2ce11c4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73955928"
---
# <a name="scheduling-a-test"></a>Planen eines Tests

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Planen Sie einen Test im Validation-as-a-Service-Portal (VaaS) für Ihre Azure Stack-Lösung. Eine VaaS-Lösung stellt eine Azure Stack-Lösung mit einer bestimmten Hardwareliste dar. Sie können einen Test planen, um zu überprüfen, ob Azure Stack auf Ihrer Hardware ausgeführt werden kann.

Um Ihre Lösung zu überprüfen, erstellen Sie den Workflow für einen Test. Ein VaaS-Workflow arbeitet im Kontext einer VaaS-Lösung. Es stellt einen Satz von Testsuites dar, die die Funktionalität einer Azure Stack-Bereitstellung auf Ihrer Hardware testen. Fügen Sie die Umgebungsparameter Ihrer Lösung hinzu, und wählen Sie Tests aus, die in Ihrer Lösung ausgeführt werden sollen.

Der Workflow „Testdurchlauf“ kann zum Ausführen beliebiger Tests von VaaS verwendet werden, einschließlich Tests von den Validierungsworkflows, die Ergebnisse des Workflows „Testdurchlauf“ gelten jedoch nicht als *offiziell*. Informationen zu offiziellen Validierungsworkflows finden Sie unter [Workflows](azure-stack-vaas-key-concepts.md#workflows).

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie diesem Schnellstart folgen, sollten Sie folgende Elemente abgeschlossen haben:

- [Einrichten Ihrer Validation-as-a-Service-Ressourcen](azure-stack-vaas-set-up-resources.md)
- [Bereitstellen des lokalen Agents](azure-stack-vaas-local-agent.md) (erforderlich)
- [Wichtige Begriffe zu Validation-as-a-Service](azure-stack-vaas-key-concepts.md) (erforderlich)

## <a name="start-a-workflow"></a>Starten eines Workflows

![Anmelden beim VaaS-Portal](media/vaas_portalsignin.png)

Melden Sie sich beim Portal an, wählen Sie eine Lösung aus, oder erstellen Sie eine, und wählen Sie dann die Lösung aus.

1. Melden Sie sich beim [VaaS-Portal](https://azurestackvalidation.com) an.
2. Geben Sie den Namen einer vorhandenen Lösung ein, oder wählen Sie **Neue Lösung** aus, um eine neue Lösung zu erstellen. Anweisungen hierzu finden Sie unter [Create a solution in the VaaS portal](azure-stack-vaas-key-concepts.md#create-a-solution-in-the-vaas-portal) (Erstellen einer Lösung im VaaS-Portal).
3. Wählen Sie **Starten** auf der Kachel **Test Passes** (Testdurchläufe) aus.

## <a name="specify-parameters"></a>Angeben von Parametern

![Alternativer Text](media/vaas_test_pass_parameters.png)

Geben Sie Parameter an, die für alle Tests innerhalb des Workflows gelten.

1. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]
2. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]
3. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]
4. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
5. Wählen Sie **Weiter** aus, um Tests für die Planung auszuwählen.

## <a name="select-tests-to-run"></a>Auswählen von auszuführenden Tests

Die ausgewählten Tests werden geplant, sobald der Workflow erstellt wurde.

1. Wählen Sie die Tests aus, die Sie in Ihrem Workflow ausführen möchten.

    Wenn Sie die allgemeinen Parameter (d.h. die im vorherigen Abschnitt angegebenen Parameter) für einen Test außer Kraft setzen möchten, wählen Sie den Link **Bearbeiten** aus, um neue Werte anzugeben.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](includes/azure-stack-vaas-workflow-step_select-agent.md)]

1. Wählen Sie **Weiter** aus, um den Workflow zu überprüfen.

## <a name="review-and-submit"></a>Überprüfen und Annehmen

Schließen Sie die Workflowerstellung ab.

1. Sehen Sie sich die angezeigten Informationen an.

    Der Dienst erstellt den Workflow mit den angegebenen Informationen, und die ausgewählten Tests werden geplant.

    Wenn etwas falsch erscheint, verwenden Sie die Schaltflächen**Zurück**, um zu einem früheren Abschnitt zu wechseln.

1. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]

## <a name="next-steps"></a>Nächste Schritte

- [Überwachen und Verwalten von Tests im VaaS-Portal](azure-stack-vaas-monitor-test.md)
