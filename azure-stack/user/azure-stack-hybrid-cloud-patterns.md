---
title: Erstellen von Lösungen für Hybrid Cloud-Entwurfsmuster für Azure Stack | Microsoft-Dokumentation
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
ms.date: 07/16/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/20/2019
ms.openlocfilehash: 538a626b2e89d15aa4b816674dbb8c374ec4a262
ms.sourcegitcommit: 2a4cb9a21a6e0583aa8ade330dd849304df6ccb5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/17/2019
ms.locfileid: "68286807"
---
#  <a name="build-solutions-hybrid-cloud-design-patterns-for-azure-stack"></a>Erstellen von Lösungen für Hybrid Cloud-Entwurfsmuster für Azure Stack

Microsoft Azure Stack ist eine Erweiterung von Azure. Mit Azure Stack können Sie die Agilität des Cloud Computing für Ihre lokale Umgebung und den Edge über eine Hybrid Cloud-Infrastruktur nutzen. Sie können Hybrid Cloud-Apps in Azure erstellen und sie anschließend in Ihrem verbundenen oder nicht verbundenen Rechenzentrum bereitstellen, egal wo sich dieses befindet.

Microsoft Azure ist eine konsistente Hybrid Cloud-Plattform. Mit Azure können Sie Code wiederverwenden, der in Azure geschrieben wurde, und Ihre App in unabhängigen Azure-Clouds sowie in Azure Stack bereitstellen. Anwendungen, die mehrere Clouds nutzen, werden auch als *Hybridanwendungen* bezeichnet.

Hybridszenarios unterscheiden sich stark je nach den für die Bereitstellung verfügbaren Ressourcen und bringen Überlegungen zur Geografie, Sicherheit, Internetzugriff usw. mit sich. Auch wenn diese Szenarios Ihren speziellen Anforderungen nicht entsprechen, bringen sie doch einige wichtige Leitlinien und Beispiele mit sich, mit denen Sie sich in das Implementieren von Hybridlösungen einarbeiten können.

## <a name="hybrid-cloud-patterns"></a>Hybrid Cloud-Muster

- [Cloudübergreifende Skalierungsmuster](azure-stack-edge-pattern-cross-cloud-scaling.md)
- [Geografisch verteilte Muster](azure-stack-edge-pattern-geo-distribution.md)
- [DevOps-Muster](azure-stack-edge-pattern-hybrid-ci-cd.md)

## <a name="step-by-step-tutorials"></a>Schritt-für-Schritt-Tutorials

- [Deploy apps across both Azure and Azure Stack (Bereitstellen von Apps in Azure und Azure Stack)](azure-stack-solution-pipeline.md)
- [Deploy apps to Azure Stack and Azure](azure-stack-solution-hybrid-identity.md) (Bereitstellen von Apps in Azure Stack und Azure)
- [Tutorial: Konfigurieren der Hybrid Cloud-Konnektivität mit Azure und Azure Stack](azure-stack-solution-hybrid-connectivity.md)
- [Konfigurieren der Hybrid Cloud-Konnektivität mit Azure und Azure Stack](azure-stack-solution-staged-data-analytics.md)
- [Erstellen einer gestaffelten Lösung für die Datenanalyse mit Azure und Azure Stack](azure-stack-solution-staged-data.md)
- [Erstellen von cloudübergreifenden Skalierungslösungen mit Azure](azure-stack-solution-cloud-burst.md)
- [Erstellen einer geografisch verteilten App-Lösung mit Azure und Azure Stack](azure-stack-solution-geo-distributed.md)
- [Bereitstellen einer Hybrid Cloud-Lösung mit Azure und Azure Stack](azure-stack-solution-hybrid-cloud.md)
- [Bereitstellen von MongoDB in Azure und Azure Stack](azure-stack-solution-mongodb-ha.md)
- [Bereitstellen von SQL Server 2016 in Azure und Azure Stack](azure-stack-solution-sql-ha.md)


## <a name="next-steps"></a>Nächste Schritte

- Im Artikel zu [Design Considerations for Hybrid Applications (Entwurfsüberlegungen für Hybridanwendungen)](azure-stack-edge-pattern-overview.md) werden die wichtigen Aspekte in Bezug auf die Softwarequalität beschrieben, die für das Entwerfen, Bereitstellen und Betreiben von Hybridanwendungen erforderlich sind.
- [Richten Sie eine Entwicklungsumgebung in Azure Stack ein](azure-stack-dev-start.md), und [stellen Sie Ihre erste App in Azure Stack bereit](azure-stack-dev-start-deploy-app.md).
