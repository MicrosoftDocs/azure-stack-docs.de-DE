---
title: Muster für das Training eines Machine Learning-Modells (ML) im Edge-Bereich mit Azure und Azure Stack Hub.
description: Erfahren Sie, wie Sie Azure- und Azure Stack Hub-Dienste für das Training eines ML-Modells im Edge-Bereich einsetzen.
author: BryanLa
ms.service: azure-stack
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: f845aee01e4c87bc877eb77fb793b32472f9f222
ms.sourcegitcommit: 5c92a669007ab4aaffe4484f1d8836a40340dde1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73640467"
---
# <a name="train-machine-learning-ml-model-at-the-edge-pattern"></a>Muster zum Trainieren eines Machine Learning-Modells (ML) im Edge-Bereich

Generieren Sie portierbare ML-Modelle anhand von Daten, die nur lokal vorhanden sind.

## <a name="context-and-problem"></a>Kontext und Problem

Viele Organisationen möchten mithilfe von Tools, die ihre Datenanalysten verstehen, Erkenntnisse aus ihren lokalen oder älteren Datenbeständen gewinnen. [Azure Machine Learning Service](/azure/machine-learning/) bietet native Cloudtools zum Trainieren, Optimieren und Bereitstellen von ML- und Deep Learning-Modellen.  

Einige Datenmengen sind jedoch zu groß, um sie in die Cloud zu senden, oder können aus rechtlichen Gründen nicht in die Cloud übertragen werden. Anhand dieses Musters können Datenanalysten mit Azure Machine Learning Modelle trainieren, die lokale Daten und Computeressourcen nutzen. 

## <a name="solution"></a>Lösung

Für das Muster zum Training im Edge-Bereich wird ein in Azure Stack Hub ausgeführter virtueller Computer (VM) verwendet. Die VM ist als Computeziel in Azure ML registriert, sodass sie auf Daten zugreifen kann, die nur lokal verfügbar sind. In diesem Fall werden die Daten im Blob Storage von Azure Stack Hub gespeichert. 

Nach dem Training des Modells wird es bei Azure ML registriert, in Containern organisiert und zur Bereitstellung einer Azure Container Registry-Instanz hinzugefügt. Für diese Iteration des Musters muss die Azure Stack Hub-Trainings-VM über das öffentliche Internet erreichbar sein. 

[![Architektur für das Trainieren von ML-Modellen im Edge-Bereich](media/pattern-train-ml-model-at-edge/solution-architecture.png)](media/pattern-train-ml-model-at-edge/solution-architecture.png)

Die Lösung funktioniert so: 

1. Die Azure Stack Hub-VM wird als Computeziel bei Azure ML bereitgestellt und registriert.
2. In Azure ML wird ein Experiment erstellt, das die Azure Stack Hub-VM als Computeziel verwendet.
3. Sobald das Modell trainiert ist, wird es registriert und in Containern organisiert.
4. Das Modell kann nun an Speicherorten bereitgestellt werden, die sich entweder lokal oder in der Cloud befinden.

## <a name="components"></a>Komponenten

Diese Lösung verwendet die folgenden Komponenten:

| Ebene | Komponente | BESCHREIBUNG |
|----------|-----------|-------------|
| Azure | Azure Machine Learning | [Azure Machine Learning](/azure/machine-learning/) orchestriert das Training des ML-Modells. |
| | Azure Container Registry | Azure ML packt das Modell in einen Container und speichert es für die Bereitstellung in einer [Azure Container Registry](/azure/container-registry/)-Instanz.|
| Azure Stack Hub | App Service | [Azure Stack Hub mit App Service](/azure-stack/operator/azure-stack-app-service-overview) stellt die Basis für die Komponenten im Edge-Bereich bereit. |
| | Compute | Eine Azure Stack Hub-VM, auf der Ubuntu mit Docker ausgeführt wird, wird zum Trainieren des ML-Modells eingesetzt. |
| | Storage | Private Daten können im Blob Storage von Azure Stack Hub gehostet werden. |

## <a name="issues-and-considerations"></a>Probleme und Überlegungen

Beachten Sie die folgenden Punkte bei der Entscheidung, wie diese Lösung implementiert werden soll:

### <a name="scalability"></a>Skalierbarkeit 

Damit diese Lösung skalierbar ist, müssen Sie in Azure Stack Hub einen entsprechend großen virtuellen Computer für das Training erstellen.

### <a name="availability"></a>Verfügbarkeit

Stellen Sie sicher, dass die Trainingsskripts und die Azure Stack Hub-VM Zugriff auf die für das Training verwendeten lokalen Daten haben.

### <a name="manageability"></a>Verwaltbarkeit

Sorgen Sie dafür, dass Modelle und Experimente ordnungsgemäß registriert sowie mit Versionsangaben und mit Tags versehen sind, um Verwechslungen bei der Modellbereitstellung zu vermeiden. 

### <a name="security"></a>Sicherheit

Dieses Muster ermöglicht Azure ML den Zugriff auf lokale möglicherweise sensible Daten. Vergewissern Sie sich, dass das Konto, das für die SSH-Verbindung mit der Azure Stack Hub-VM verwendet wird, über ein sicheres Kennwort verfügt und dass Trainingsskripts keine Daten speichern oder in die Cloud hochladen. 

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den in diesem Artikel behandelten Themen:
- Eine Übersicht über ML und verwandte Themen finden Sie in der [Azure Machine Learning-Dokumentation](/azure/machine-learning).
- Unter [Azure Container Registry](/azure/container-registry/) erfahren Sie, wie Sie Images für Containerbereitstellungen erstellen, speichern und verwalten.
- Weitere Informationen zum Ressourcenanbieter und zur Bereitstellung finden Sie unter [App Service in Azure Stack Hub](/azure-stack/operator/azure-stack-app-service-overview).
- Unter [Überlegungen zum Entwurf von Hybridanwendungen](overview-app-design-considerations.md) erfahren Sie mehr zu bewährten Methoden und erhalten Antworten auf weitere Fragen.
- Siehe die [Azure Stack-Familie mit Produkten und Lösungen](/azure-stack), um mehr über das gesamte Portfolio von Produkten und Lösungen zu erfahren.

Wenn Sie bereit sind, das Lösungsbeispiel zu testen, fahren Sie mit dem [Bereitstellungsleitfaden zum Trainieren eines ML-Modells im Edge-Bereich](https://aka.ms/edgetrainingdeploy) fort. In diesem Bereitstellungsleitfaden finden Sie detaillierte Anweisungen zum Bereitstellen und Testen der zugehörigen Komponenten.