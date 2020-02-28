---
title: Planen eines Tests im Azure Stack Hub-Validierungsportal
titleSuffix: Azure Stack Hub
description: Hier erfahren Sie, wie Sie einen Test im Azure Stack Hub-Validierungsportal planen.
author: mattbriggs
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: ccaae2e6d7625687f7739cba4fa77eda3ab91520
ms.sourcegitcommit: 4e1c948ae4a498bd730543b0704bbc2b0d88e1ec
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77625457"
---
# <a name="schedule-a-test-in-azure-stack-validation-portal"></a>Planen eines Tests im Azure Stack-Validierungsportal

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Planen Sie einen Test im Microsoft Azure Stack-Validierungsportal für Ihre Azure Stack Hub-Lösung. Eine VaaS-Lösung (Validation-as-a-Service) stellt eine Azure Stack Hub-Lösung mit einer bestimmten Hardwareliste dar. Sie können einen Test planen, um zu überprüfen, ob Azure Stack Hub auf Ihrer Hardware ausgeführt werden kann.

Um Ihre Lösung zu überprüfen, erstellen Sie den Workflow für einen Test. Ein VaaS-Workflow arbeitet im Kontext einer VaaS-Lösung. Dieser stellt eine Reihe von Testsammlungen dar, die die Funktionalität einer Azure Stack Hub-Bereitstellung auf Ihrer Hardware testen. Fügen Sie die Umgebungsparameter Ihrer Lösung hinzu, und wählen Sie Tests aus, die in Ihrer Lösung ausgeführt werden sollen.

Der Workflow „Testdurchlauf“ kann zum Ausführen beliebiger Tests von VaaS verwendet werden, einschließlich Tests von den Validierungsworkflows, die Ergebnisse des Workflows „Testdurchlauf“ gelten jedoch nicht als *offiziell*. Informationen zu offiziellen Validierungsworkflows finden Sie unter [Workflows](azure-stack-vaas-key-concepts.md#workflows).

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie vor dieser Schnellstartanleitung die folgenden Aufgaben aus:

- [Einrichten Ihrer Validation-as-a-Service-Ressourcen](azure-stack-vaas-set-up-resources.md)
- [Bereitstellen des lokalen Agents](azure-stack-vaas-local-agent.md) (erforderlich)
- [Wichtige Begriffe zu Validation-as-a-Service](azure-stack-vaas-key-concepts.md) (erforderlich)

## <a name="start-a-workflow"></a>Starten eines Workflows

![Anmelden beim VaaS-Portal](media/vaas_portalsignin.png)

Melden Sie sich beim Portal an, wählen Sie eine Lösung aus, oder erstellen Sie eine, und wählen Sie dann die Lösung aus.

1. Melden Sie sich beim [VaaS-Portal](https://azurestackvalidation.com) an.
2. Geben Sie den Namen einer vorhandenen Lösung ein, oder wählen Sie **Neue Lösung** aus, um eine neue Lösung zu erstellen. Anweisungen hierzu finden Sie unter [Create a solution in the VaaS portal](azure-stack-vaas-key-concepts.md#create-a-solution-in-the-azure-stack-hub-validation-portal) (Erstellen einer Lösung im VaaS-Portal).
3. Wählen Sie **Starten** auf der Kachel **Test Passes** (Testdurchläufe) aus.

## <a name="specify-parameters"></a>Angeben von Parametern

![Angeben von Parametern im VaaS-Portal](media/vaas_test_pass_parameters.png)

Geben Sie Parameter an, die für alle Tests innerhalb des Workflows gelten.

1. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]
2. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]
3. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]
4. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
5. Wählen Sie **Weiter** aus, um Tests für die Planung auszuwählen.

## <a name="select-tests-to-run"></a>Auswählen von auszuführenden Tests

Die ausgewählten Tests werden geplant, sobald der Workflow erstellt wurde.

1. Wählen Sie die Tests aus, die Sie in Ihrem Workflow ausführen möchten.

    Wenn Sie die allgemeinen Parameter (d. h. die im vorherigen Abschnitt angegebenen Parameter) für einen Test außer Kraft setzen möchten, wählen Sie den Link **Bearbeiten** aus, um neue Werte anzugeben.

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
