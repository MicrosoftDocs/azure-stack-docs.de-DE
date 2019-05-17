---
title: Erstellen von Hybrid Cloud-Apps mit Azure und Azure Stack | Microsoft-Dokumentation
description: Informationen zum Erstellen von Hybrid Cloud-Apps mit Azure und Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 05/06/2019
ms.openlocfilehash: a1e13b471c9eaed9dcac79c4002ceca6b3b8e7d2
ms.sourcegitcommit: a78c0d143eadcab65a601746b9ea24be28091ad2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65212670"
---
# <a name="create-hybrid-cloud-apps-with-azure-and-azure-stack"></a>Erstellen von Hybrid Cloud-Apps mit Azure und Azure Stack

Microsoft Azure Stack ist eine Erweiterung von Azure. Mit Azure Stack können Sie die Agilität des Cloud Computing für Ihre lokale Umgebung und den Edge über eine Hybrid Cloud-Infrastruktur nutzen. Sie können Hybrid Cloud-Apps in Azure erstellen und sie anschließend in Ihrem verbundenen oder nicht verbundenen Rechenzentrum bereitstellen, egal wo sich dieses befindet.

Microsoft Azure ist eine konsistente Hybrid Cloud-Plattform. Mit Azure können Sie Code wiederverwenden, der in Azure geschrieben wurde, und Ihre App in unabhängigen Azure-Clouds sowie in Azure Stack bereitstellen. Anwendungen, die mehrere Clouds nutzen, werden auch als *Hybridanwendungen* bezeichnet.

Hybridszenarios unterscheiden sich stark je nach den für die Bereitstellung verfügbaren Ressourcen und bringen Überlegungen zur Geografie, Sicherheit, Internetzugriff usw. mit sich. Auch wenn diese Szenarios Ihren speziellen Anforderungen nicht entsprechen, bringen sie doch einige wichtige Leitlinien und Beispiele mit sich, mit denen Sie sich in das Implementieren von Hybridlösungen einarbeiten können.

Sie können Hybrid Cloud-Ansätze für Folgendes nutzen:
- Mehrstufige Beispieldaten
- SQL Server in Azure und Azure Stack
- End-to-End-Bereitstellung für die Mongo-Datenbank in Azure und Azure Stack
- „void“-Erkennung bei KI-Rückschlüssen in Microsoft Edge

## <a name="step-by-step-tutorials"></a>Schritt-für-Schritt-Tutorials

- [Deploy apps across both Azure and Azure Stack (Bereitstellen von Apps in Azure und Azure Stack)](azure-stack-solution-pipeline.md)
- [Deploy apps to Azure Stack and Azure](azure-stack-solution-hybrid-identity.md) (Bereitstellen von Apps in Azure Stack und Azure)
- [Tutorial: Konfigurieren der Hybrid Cloud-Konnektivität mit Azure und Azure Stack](azure-stack-solution-hybrid-connectivity.md)
- [Konfigurieren der Hybrid Cloud-Konnektivität mit Azure und Azure Stack](azure-stack-solution-staged-data-analytics.md)
- [Erstellen einer gestaffelten Lösung für die Datenanalyse mit Azure und Azure Stack](azure-stack-solution-cloud-burst.md)
- [Erstellen von cloudübergreifenden Skalierungslösungen mit Azure](azure-stack-solution-cloud-burst.md)
- [Erstellen einer geografisch verteilten App-Lösung mit Azure und Azure Stack](azure-stack-solution-geo-distributed.md)
- [Bereitstellen einer Hybrid Cloud-Lösung mit Azure und Azure Stack](azure-stack-solution-hybrid-cloud.md)

## <a name="next-steps"></a>Nächste Schritte

- Im Whitepaper [Design Considerations for Hybrid Applications (Entwurfsüberlegungen für Hybridanwendungen)](https://aka.ms/hybrid-cloud-applications-pillars) werden die wichtigen Aspekte in Bezug auf die Softwarequalität beschrieben, die für das Entwerfen, Bereitstellen und Betreiben von Hybridanwendungen erforderlich sind.
- [Richten Sie eine Entwicklungsumgebung in Azure Stack ein](azure-stack-dev-start.md), und [stellen Sie Ihre erste App in Azure Stack bereit](azure-stack-dev-start-deploy-app.md).
