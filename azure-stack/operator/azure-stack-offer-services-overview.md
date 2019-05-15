---
title: Anbieten von Diensten in Azure Stack | Microsoft-Dokumentation
description: Als Cloudbetreiber können Sie Dienste für Ihre Benutzer anbieten.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: mabrigg
ms.reviewer: unknown
ms.lastreviewed: 09/17/2018
ms.openlocfilehash: 3282464bf1df7fcbbdb532735c8d4b1dabfa0401
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985041"
---
# <a name="overview-of-offering-services-in-azure-stack"></a>Übersicht über das Anbieten von Diensten in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

[Microsoft Azure Stack](azure-stack-overview.md) ist eine Hybrid-Cloudplattform, die das Bereitstellen von Diensten aus Ihrem Datencenter ermöglicht. Als Dienstanbieter können Sie Dienste für Ihre Mandanten anbieten. Innerhalb eines Unternehmens oder einer Behörde ist es möglich, den Mitarbeitern lokale Dienste anzubieten. 

Sie können [IaaS-Dienste](https://azure.microsoft.com/overview/what-is-iaas/) (Infrastructure-as-a-Service) anbieten, mit denen Benutzer bedarfsgesteuert eine Computinginfrastruktur erstellen können, die über das Azure Stack-Benutzerportal bereitgestellt und verwaltet wird.

Sie können auch [PaaS-Dienste](https://azure.microsoft.com/overview/what-is-paas/) (Platform-as-a-Service) von Microsoft und anderen Drittanbietern für Azure Stack bereitstellen. Die Dienste, die Sie bereitstellen können, umfassen u.a.:

- [Hinzufügen eines App Service-Ressourcenanbieters zu Azure Stack](azure-stack-app-service-overview.md)

- [Hinzufügen eines SQL Server-Ressourcenanbieters zu Azure Stack](azure-stack-sql-resource-provider-deploy.md)

- [Hinzufügen eines MySQL Server-Ressourcenanbieters zu Azure Stack](azure-stack-mysql-resource-provider-deploy.md)


Sie können auch Dienste kombinieren, um komplexe Lösungen für verschiedene Benutzer zu integrieren und zu erstellen.

Sie müssen zuerst [Pläne, Angebote und Kontingente](azure-stack-plan-offer-quota-overview.md) erstellen, um diese Dienste für Ihre Benutzer bereitzustellen. Ihre Benutzer können Ihre Angebote dann abonnieren, um die Dienste zu nutzen.

## <a name="plan-your-service-offers"></a>Planen Ihrer Dienstangebote

Berücksichtigen Sie beim Planen Ihrer Angebote Folgendes:

**Testangebote**: Testangebote sind praktisch, um neue Benutzer zu gewinnen, die dann ein Upgrade für zusätzliche Dienste durchführen können. Erstellen Sie für ein Testangebot einen kleinen [Basisplan](azure-stack-plan-offer-quota-overview.md#base-plan) mit einem optionalen größeren Add-On-Plan.

**Kapazitätsplanung**: Möglicherweise haben Sie Bedenken, dass Benutzer große Mengen an Ressourcen abrufen und das System für alle Benutzer lahmlegen. Sie können [Ihre Pläne mit Kontingenten konfigurieren](azure-stack-plan-offer-quota-overview.md#plans), um eine Obergrenze für die Nutzung festzulegen und so die Leistung zu verbessern.

**Delegierte Anbieter**: Bieten Sie anderen die Möglichkeit, Angebote in Ihrer Umgebung zu erstellen. Wenn Sie z.B. ein Dienstanbieter sind, können Sie diese Möglichkeit an Ihre Wiederverkäufer [delegieren](azure-stack-delegated-provider.md). Oder Unternehmen nutzen diese Möglichkeit, um solche Optionen an bestimmte Abteilungen oder Niederlassungen zu delegieren.

## <a name="next-steps"></a>Nächste Schritte

[Erstellen von Angeboten in Azure Stack](azure-stack-create-offer.md)
