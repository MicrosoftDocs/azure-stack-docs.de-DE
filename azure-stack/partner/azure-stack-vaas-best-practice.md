---
title: Bewährte Methoden für die Azure Stack-Validierung
description: Hier erfahren Sie mehr über bewährte Methoden für Validation-as-a-Service.
author: mattbriggs
ms.topic: article
ms.date: 08/24/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 176f944b449ca2dabb6424965f78693d00e7cdce
ms.sourcegitcommit: 4922a14fdbc8a3b67df065336e8a21a42f224867
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2020
ms.locfileid: "88764380"
---
# <a name="azure-stack-validation-best-practices"></a>Bewährte Methoden für die Azure Stack-Validierung

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Dieser Artikel behandelt bewährte Methoden zum Verwalten von Ressourcen in Validation-as-a-Service (VaaS). Eine Übersicht über VaaS-Ressourcen finden Sie unter [Wichtige Begriffe zu Validation-as-a-Service](azure-stack-vaas-key-concepts.md).

## <a name="solution-management"></a>Lösungsverwaltung

### <a name="naming-convention-for-vaas-solutions"></a>Benennungskonvention für VaaS-Lösungen

Verwenden Sie eine konsistente Benennungskonvention für alle Lösungen, die in VaaS registriert sind. Der Lösungsname kann beispielsweise wie folgt aus den Hardwareeigenschaften erstellt werden:

|Produktname | Eindeutiges Hardwareelement 1 | Eindeutiges Hardwareelement 2 | Projektmappenname
|---|---|---|---|
Meine Lösung XYZ |  Nur Flash | Mein Switch X01 | MeineKösungXYZ_NurFlash_MeinSwitchX01

### <a name="when-to-create-a-new-vaas-solution"></a>Zeitpunkte für das Erstellen einer neuen VaaS-Lösung

Verwenden Sie dieselbe VaaS-Lösung, wenn Sie Workflows für dieselbe Hardware-SKU ausführen. Eine neue VaaS-Lösung sollte nur dann erstellt werden, wenn eine Änderung an der Hardware-SKU vorgenommen wurde.

## <a name="workflow-management"></a>Workflowverwaltung

### <a name="naming-convention-for-vaas-workflows"></a>Benennungskonvention für VaaS-Workflows

Verwenden Sie eine konsistente Benennungskonvention für alle VaaS Workflowdurchläufe. Ein Workflowname kann beispielsweise wie folgt auf der Grundlage der Buildeigenschaften konstruiert werden:

|Buildnummer (Hauptnummer) | Date | Lösungsgröße | Workflowname
|---|---|---| ---|
1808 | 081518 | 4NODE | 1808_081518_4NODE

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Wichtige Begriffe zu Validation-as-a-Service](azure-stack-vaas-key-concepts.md)
