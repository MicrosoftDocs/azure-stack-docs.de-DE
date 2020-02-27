---
title: Überwachen von Tests im Azure Stack Hub-Validierungsportal
titleSuffix: Azure Stack Hub
description: Hier erfahren Sie, wie Sie Tests im Azure Stack Hub-Validierungsportal verwalten.
author: mattbriggs
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 697aabcb1b52bc52083b635e67058f1b08451a38
ms.sourcegitcommit: 4e1c948ae4a498bd730543b0704bbc2b0d88e1ec
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77625430"
---
# <a name="manage-tests-in-the-azure-stack-hub-validation-portal"></a>Überwachen von Tests im Azure Stack Hub-Validierungsportal

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Nach dem Planen von Tests für Ihre Azure Stack Hub-Lösung beginnt VaaS (Validation-as-a-Service) mit dem Melden des Testausführungsstatus. Diese Informationen sind im Azure Stack Hub-Validierungsportal zusammen mit Aktionen (etwa Neuplanen und Abbrechen von Tests) verfügbar.

## <a name="navigate-to-the-workflow-tests-summary-page"></a>Navigieren zur Zusammenfassungsseite für Workflowtests

1. Wählen Sie auf dem Lösungsdashboard eine vorhandene Lösung mit mindestens einem Workflow aus.

    ![Workflowkacheln auf dem Lösungsdashboard](media/tile_all-workflows.png)

1. Wählen Sie auf der Workflowkachel die Option **Verwalten** aus. Auf der nächsten Seite sind die für die ausgewählte Lösung erstellten Workflows aufgeführt.

1. Wählen Sie den Namen eines Workflows aus, um die zugehörige Testzusammenfassung zu öffnen.

## <a name="change-workflow-parameters"></a>Ändern von Workflowparametern

Sie können für jeden Workflowtyp die bei der Workflowerstellung angegebenen [Testparameter](azure-stack-vaas-parameters.md#test-parameters) bearbeiten.

1. Wählen Sie auf der Testzusammenfassungsseite **Bearbeiten** aus.

1. Geben Sie neue Werte gemäß den Informationen unter [Allgemeine Workflowparameter für Validation-as-a-Service in Azure Stack Hub](azure-stack-vaas-parameters.md) ein.

1. Wählen Sie **Übermitteln** aus, um die Werte zu speichern.

> [!NOTE]
> Im Workflow **Testdurchlauf** müssen Sie die Testauswahl fertig stellen und die Seite „Überprüfen“ aufrufen, damit Sie die neuen Parameterwerte speichern können.

### <a name="add-tests-test-pass-only"></a>Hinzufügen von Tests (nur „Testdurchlauf“)

In Workflows vom Typ **Testdurchlauf** können Sie über die Schaltflächen **Tests hinzufügen** und **Bearbeiten** neue Tests im Workflow planen.

> [!TIP]
> Wählen Sie **Tests hinzufügen** aus, wenn Sie neue Tests planen möchten, aber keine Parameter für einen Workflow vom Typ **Testdurchlauf** bearbeiten müssen.

## <a name="manage-test-instances"></a>Verwalten von Testinstanzen

Bei inoffiziellen Ausführungen (beim Workflow **Testdurchlauf**) werden auf der Testzusammenfassungsseite die für die Azure Stack Hub-Lösung geplanten Tests aufgeführt.

Bei offiziellen Ausführungen (bei Workflows vom Typ **Validierung**) werden auf der Testzusammenfassungsseite die Tests aufgeführt, die zum Abschließen der Überprüfung der Azure Stack Hub-Lösung erforderlich sind. Auf dieser Seite werden die Validierungstests geplant.

In jeder geplanten Testinstanz werden die folgenden Informationen angezeigt:

| Column | BESCHREIBUNG |
| --- | --- |
| Testname | Name und Version des Tests |
| Category | Der Zweck des Tests |
| Erstellt | Der Zeitpunkt, zu dem der Test geplant wurde |
| Gestartet | Der Zeitpunkt, zu dem die Ausführung des Tests gestartet wurde |
| Duration | Die Ausführungsdauer des Tests |
| Status | Der Status oder das Ergebnis des Tests. Vor oder während der Ausführung lautet der Status `Pending` oder `Running`. Nach der Ausführung lautet der Status `Cancelled`, `Failed`, `Aborted` oder `Succeeded`. |
| Agent-Name | Der Name des Agents, der den Test ausgeführt hat |
| Vorgänge gesamt | Die Gesamtanzahl von Vorgängen, die im Test versucht wurden |
| Passed operations (Bestandene Vorgänge) | Die Anzahl erfolgreicher Vorgänge im Test |
|  Fehlerhafte Vorgänge | Die Anzahl fehlerhafter Vorgänge im Test |

### <a name="actions"></a>Aktionen

Für jede Testinstanz werden ausführbare Aktionen aufgelistet, wenn Sie in der Tabelle der Testinstanzen das jeweilige Kontextmenü **[...]** auswählen.

#### <a name="view-information-about-the-test-definition"></a>Anzeigen von Information zur Testdefinition

Wählen Sie im Kontextmenü **Information anzeigen** aus, um allgemeine Informationen zur Testdefinition anzuzeigen. Diese Informationen sind für alle Testinstanzen mit dem gleichen Namen und der gleichen Version identisch.

| Testeigenschaft | BESCHREIBUNG |
| -- | -- |
| Testname | Der Name des Tests. |
| Testversion | Die Version des Tests |
| Herausgeber | Der Herausgeber des Tests |
| Category |  Der Zweck des Tests |
| Target services (Zieldienste) | Die Azure Stack Hub-Dienste, die getestet werden |
| BESCHREIBUNG | Die Beschreibung des Tests |
| Estimated duration (minutes) (Geschätzte Dauer (Minuten)) | Die erwartete Ausführungsdauer des Tests |
| Links | Alle relevanten Informationen zum Test oder zu Ansprechpartnern |

#### <a name="view-test-instance-parameters"></a>Anzeigen der Testinstanzparameter

Wählen Sie im Kontextmenü die Option **View parameters** (Parameter anzeigen), um die Parameter anzuzeigen, die der Testinstanz zum geplanten Zeitpunkt zur Verfügung gestellt werden. Vertrauliche Zeichenfolgen wie Kennwörter werden nicht angezeigt. Diese Aktion ist nur für geplante Tests verfügbar.

Dieses Fenster enthält die folgenden Metadaten für alle Testinstanzen:

| Testinstanzeigenschaft | BESCHREIBUNG |
| -- | -- |
| Testname | Der Name des Tests. |
| Testversion | Die Version des Tests |
| Test instance ID (Testinstanz-ID) | Eine GUID, die die spezifische Instanz des Tests angibt |

#### <a name="view-test-instance-operations"></a>Anzeigen der Testinstanzvorgänge

Wählen Sie im Kontextmenü die Option **View operations** (Vorgänge anzeigen) aus, um den ausführlichen Status von Vorgängen anzuzeigen, die während des Tests ausgeführt wurden. Diese Aktion ist nur für geplante Tests verfügbar.

![View operations (Vorgänge anzeigen)](media/manage-test_context-menu-operations.png)

#### <a name="download-logs-for-a-completed-test-instance"></a>Herunterladen von Protokollen für eine abgeschlossene Testinstanz

Wählen Sie im Kontextmenü die Option **Protokolle herunterladen** aus, um eine Datei vom Typ `.zip` der Protokolle herunterzuladen, die während der Testausführung ausgegeben wurden. Diese Aktion ist nur für abgeschlossene Tests verfügbar, d. h. Tests mit dem Status `Cancelled`, `Failed`, `Aborted` oder `Succeeded`.

#### <a name="reschedule-a-test-instance-or-schedule-a-test"></a>Neuplanen einer Testinstanz oder Planen eines Tests

Die Planung von Tests über die Verwaltungsseite hängt vom Workflowtyp ab, in dem der Test ausgeführt wird.

##### <a name="test-pass-workflow"></a>Workflow „Testdurchlauf“

Im Workflow „Testdurchlauf“ werden beim **Neuplanen** einer Testinstanz die gleichen Parameter wie bei der ursprünglichen Testinstanz verwendet. Das ursprüngliche Ergebnis (einschließlich der Protokolle) wird *ersetzt*. Bei der Neuplanung müssen vertrauliche Zeichenfolgen wie Kennwörter erneut eingegeben werden.

1. Wählen Sie im Kontextmenü die Option **Reschedule** (Neu planen) aus, um eine Eingabeaufforderung zum Neuplanen der Testinstanz zu öffnen.

1. Geben Sie alle erforderlichen Parametern ein.

1. Wählen Sie **Senden** aus, um die Testinstanz neu zu planen und die vorhandene Instanz zu ersetzen.

##### <a name="validation-workflows"></a>Validierungsworkflows

[!INCLUDE [azure-stack-vaas-workflow-validation-section_schedule](includes/azure-stack-vaas-workflow-validation-section_schedule.md)]

#### <a name="cancel-a-test-instance"></a>Abbrechen einer Testinstanz

Ein geplanter Test kann abgebrochen werden, wenn sein Status `Pending` oder `Running` lautet.  

1. Wählen Sie im Kontextmenü die Option **Abbrechen** aus, um eine Eingabeaufforderung zum Abbrechen der Testinstanz zu öffnen.

1. Wählen Sie **Senden** aus, um die Testinstanz abzubrechen.

## <a name="next-steps"></a>Nächste Schritte

- [Problembehandlung für Validation-as-a-Service](azure-stack-vaas-troubleshoot.md)
