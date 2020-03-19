---
title: Wichtige Begriffe zu Validation-as-a-Service
titleSuffix: Azure Stack Hub
description: Hier werden die wichtigsten Konzepte von Validation-as-a-Service in Azure Stack Hub beschrieben.
author: mattbriggs
ms.topic: article
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 5199a9f3b8dcf9a3ac43d9563688cba8da949ce3
ms.sourcegitcommit: 20d10ace7844170ccf7570db52e30f0424f20164
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79295161"
---
# <a name="validation-as-a-service-key-concepts"></a>Wichtige Begriffe zu Validation-as-a-Service

Dieser Artikel beschreibt wichtige Konzepte von Validation-as-a-Service (VaaS).

## <a name="solutions"></a>Lösungen

Eine VaaS-Lösung stellt eine Azure Stack Hub-Lösung mit einer bestimmten Hardwareliste dar. Die VaaS-Lösung fungiert als Container für die Workflows, die für die Azure Stack Hub-Lösung ausgeführt werden.

### <a name="create-a-solution-in-the-azure-stack-hub-validation-portal"></a>Erstellen einer Lösung im Azure Stack Hub-Validierungsportal

1. Melden Sie sich beim [Azure Stack Hub-Validierungsportal](https://azurestackvalidation.com) an.
2. Wählen Sie auf dem Lösungsdashboard die Option **Neue Lösung** aus.
3. Geben Sie einen Namen für Lösung ein. Benennungsvorschläge finden Sie unter [Benennungskonventionen für VaaS-Lösungen](azure-stack-vaas-best-practice.md#naming-convention-for-vaas-solutions).
4. Wählen Sie **Speichern** aus, um die Lösung zu erstellen.

## <a name="workflows"></a>Workflows

Ein VaaS-Workflow arbeitet im Kontext einer VaaS-Lösung. Er stellt eine Reihe von Testsammlungen dar, die die Funktionalität einer Azure Stack Hub-Bereitstellung testen. Für jede Bereitstellung oder Softwareaktualisierung einer Azure Stack Hub-Lösung sollte ein Workflow erstellt werden.

Workflows werden nach dem Testszenariotyp kategorisiert. Bei inoffiziellen Tests können Sie im Workflow **Testdurchlauf** Tests aus allen verfügbaren VaaS-Begleitmaterialien auswählen. Bei offiziellen Tests werden im Workflow **Validierung** bestimmte von Microsoft ausgewählte Testszenarien angezielt.

![VaaS-Workflowkacheln](media/tile_all-workflows.png)

> [!NOTE]
> Der Workflow für die **Paketvalidierung** unterstützt derzeit zwei Szenarien: [Überprüfen der OEM-Pakete](azure-stack-vaas-validate-oem-package.md) und [Überprüfen der Softwareupdates von Microsoft](azure-stack-vaas-validate-microsoft-updates.md).

Weitere Informationen zu Workflowtypen finden Sie unter [Was ist Validation-as-a-Service für Azure Stack Hub?](azure-stack-vaas-overview.md).

### <a name="getting-started-with-vaas-workflows"></a>Erste Schritte mit VaaS-Workflows

1. Erstellen Sie auf dem Lösungsdashboard eine neue Lösung, oder wählen Sie eine vorhandene aus. Dadurch werden die Workflowkacheln aktualisiert und aktiviert.
2. Wählen Sie zum Erstellen eines neuen Workflows auf einer beliebigen Kachel die Option **Starten** aus. Spezifische Informationen zu den einzelnen Workflows finden Sie in den folgenden Artikeln:
    - Testdurchlauf: [Schnellstart: Planen des ersten Tests mithilfe des Azure Stack Hub-Validierungsportals](azure-stack-vaas-schedule-test-pass.md)
    - Lösungsvalidierung: [Überprüfen einer neuen Azure Stack Hub-Lösung](azure-stack-vaas-validate-solution-new.md)
    - Paketvalidierung (monatliches Update): [Überprüfen der Softwareupdates von Microsoft](azure-stack-vaas-validate-microsoft-updates.md)
    - Paketvalidierung (Paketsignierung): [Überprüfen von OEM-Paketen](azure-stack-vaas-validate-oem-package.md)

3. Wählen Sie zum Verwalten oder Überwachen eines vorhandenen Workflows auf der Workflow-Kachel die Option **Verwalten** aus. Wählen Sie den Namen des Workflows aus, und zeigen Sie mithilfe der Schaltfläche **Bearbeiten** die Eigenschaften an, oder ändern Sie allgemeine Testparameter.

Weitere Informationen zu Eigenschaften und Parametern von Workflows finden Sie unter [Allgemeine Workflowparameter für Validation-as-a-Service in Azure Stack Hub](azure-stack-vaas-parameters.md).

## <a name="tests"></a>Tests

Ein Test in VaaS umfasst eine Sammlung von Vorgängen, die für eine Azure Stack Hub-Lösung ausgeführt werden. Tests haben unterschiedliche beabsichtigte Zwecke, die durch eine Kategorie (etwa Funktion oder Zuverlässigkeit) angegeben werden. Sie sind außerdem auf einen oder mehrere Dienste von Azure Stack Hub ausgelegt. Jeder Test definiert einen eigenen Satz von Parametern, von denen einige durch gemeinsame Parameter des sie enthaltenden Workflows angegeben werden.

Weitere Informationen zum Verwalten und Überwachen von Tests finden Sie unter [Überwachen und Verwalten von Tests im Azure Stack Hub-Validierungsportal](azure-stack-vaas-monitor-test.md).

Weitere Informationen zu Testparametern finden Sie unter [Allgemeine Workflowparameter für Validation-as-a-Service in Azure Stack Hub](azure-stack-vaas-parameters.md).

## <a name="agents"></a>Agents

Ein VaaS-Agent steuert die Testausführung. Zwei Typen von Agents führen VaaS-Tests aus:

- Der **Cloud-Agent**. Dies ist der in VaaS verfügbare Standard-Agent. Es ist kein Setup erforderlich, aber dies erfordert eingehende Verbindungen für Ihre Umgebung und dass Azure Stack Hub-Endpunkte über das Internet aufgelöst werden können. Einige Tests sind nicht mit dem Cloud-Agent kompatibel.
- Ein **lokaler Agent**. Dadurch können Sie eine Validierung in Szenarien ausführen, in denen keine eingehenden Verbindungen für Ihre Umgebung möglich sind. Für einige Tests ist die Ausführung über den lokalen Agent erforderlich.

Lokale Agents sind nicht an eine bestimmte Azure Stack Hub- oder VaaS-Lösung gebunden. Als bewährte Methode sollten sie außerhalb einer Azure Stack Hub-Umgebung ausgeführt werden.

Anweisungen zum Hinzufügen eines lokalen Agents finden Sie unter [Bereitstellen des lokalen Agents](azure-stack-vaas-local-agent.md).

## <a name="next-steps"></a>Nächste Schritte

- [Bewährte Methoden für Validation-as-a-Service](azure-stack-vaas-best-practice.md)