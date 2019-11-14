---
title: Überprüfen einer neuen Azure Stack-Lösung | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie eine neue Azure Stack-Lösung mit Validation-as-a-Service überprüfen.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a29b003dca06ed1b886229f031e90c50fbc8dccb
ms.sourcegitcommit: 102ef41963b5d2d91336c84f2d6af3fdf2ce11c4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73955793"
---
# <a name="validate-a-new-azure-stack-solution"></a>Überprüfen einer neuen Azure Stack-Lösung

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Hier erfahren Sie, wie Sie den Workflow **Lösungsvalidierung** verwenden, um neue Azure Stack-Lösungen zu zertifizieren.

Eine Azure Stack-Lösung ist eine Hardwareliste, auf die sich Microsoft und der Partner nach Erfüllung der Anforderungen für die Windows Server-Logo-Zertifizierung geeinigt haben. Nach einer Änderung der Hardwareliste muss die Lösung erneut zertifiziert werden. Sollten Sie weitere Fragen zur erneuten Zertifizierung von Lösungen haben, wenden Sie sich unter [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com) an das zuständige Team.

Führen Sie den Workflow „Lösungsvalidierung“ zweimal aus, um Ihre Lösung zu zertifizieren. Führen Sie ihn einmal für die unterstützte *minimale* Konfiguration aus. Führen Sie ihn ein zweites Mal für die *maximale* unterstützte Konfiguration aus. Microsoft zertifiziert die Lösung, wenn beide Konfigurationen alle Tests bestehen.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="create-a-solution-validation-workflow"></a>Erstellen eines Lösungsvalidierungsworkflows

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. Klicken Sie auf der Kachel **Solution Validations** (Lösungsvalidierungen) auf **Starten**.

    ![Kachel des Lösungsvalidierungsworkflows](media/tile_validation-solution.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. Wählen Sie die **Lösungskonfiguration** aus.
    - **Minimum**: Die Lösung wird mit der unterstützten Mindestanzahl von Knoten konfiguriert.
    - **Maximum**: Die Lösung wird mit der maximal unterstützten Anzahl von Knoten konfiguriert.
6. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

    ![Informationen zur Lösungsvalidierung](media/workflow_validation-solution_info.png)

7. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > Umgebungsparameter können nach der Workflowerstellung nicht mehr geändert werden.

8. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
9. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    Sie werden auf die Seite mit der Testzusammenfassung umgeleitet.

## <a name="run-solution-validation-tests"></a>Ausführen von Lösungsvalidierungstests

Auf der Zusammenfassungsseite der **Lösungsvalidierungstests** wird eine Liste mit den erforderlichen Tests für die Überprüfung angezeigt.

In den Validierungsworkflows werden beim **Planen** eines Tests die allgemeinen Parameter auf der Workflowebene verwendet, die Sie bei der Workflowerstellung angegeben haben (siehe [Allgemeine Workflowparameter für Validation-as-a-Service in Azure Stack](azure-stack-vaas-parameters.md)). Sollt einer der Testparameterwerte ungültig werden, muss er gemäß den Anweisungen unter [Ändern von Workflowparametern](azure-stack-vaas-monitor-test.md#change-workflow-parameters) erneut angegeben werden.

> [!NOTE]
> Wenn Sie einen Validierungstest für eine bereits vorhandene Instanz planen, wird die alte Instanz im Portal durch eine neu erstellte Instanz ersetzt. Die Protokolle für die alte Instanz bleiben erhalten, können aber nicht über das Portal verwendet werden.  
Nach erfolgreicher Ausführung eines Tests wird die **Planungsoption** deaktiviert.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](includes/azure-stack-vaas-workflow-step_select-agent.md)]

2. Wählen Sie die folgenden Tests aus:
    - Cloud Simulation Engine
    - Compute SDK Operational Suite
    - Disk Identification Test
    - KeyVault Extension SDK Operational Suite
    - KeyVault SDK Operational Suite
    - Network SDK Operational Suite
    - Storage Account SDK Operational Suite

3. Klicken Sie im Kontextmenü auf **Planen**, um eine Eingabeaufforderung für die Planung der Testinstanz zu öffnen.

4. Überprüfen Sie die Testparameter, und klicken Sie anschließend auf **Absenden**, um die Testausführung zu planen.

![Planen eines Lösungsvalidierungstests](media/workflow_validation-solution_schedule-test.png)

## <a name="next-steps"></a>Nächste Schritte

- [Überwachen und Verwalten von Tests im VaaS-Portal](azure-stack-vaas-monitor-test.md)