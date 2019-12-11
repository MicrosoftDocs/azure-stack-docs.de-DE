---
title: 'Azure Stack-SQL-Ressourcenanbieter 1.1.47.0: Versionshinweise | Microsoft-Dokumentation'
description: Es wird beschrieben, was im neuesten Update für den Azure Stack-SQL-Ressourcenanbieter enthalten ist, z.B. bekannte Probleme und der Downloadort.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2019
ms.author: justinha
ms.reviewer: xiaofmao
ms.lastreviewed: 11/26/2019
ms.openlocfilehash: 703edc0cc2c5229c3301db0b68a400507fe4c872
ms.sourcegitcommit: 11e0c2d9abbc0a2506f992976b3c9f8ca4e746b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74823528"
---
# <a name="sql-resource-provider-11470-release-notes"></a>Versionshinweise zum SQL-Ressourcenanbieter 1.1.47.0

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

In diesen Versionshinweisen werden die Verbesserungen und bekannten Probleme in Version 1.1.47.0 des SQL-Ressourcenanbieters beschrieben.

## <a name="build-reference"></a>Buildreferenz
Laden Sie die Binärdatei des SQL-Ressourcenanbieters herunter, und führen Sie dann den Self-Extractor aus, um den Inhalt in ein temporäres Verzeichnis zu extrahieren. Der Ressourcenanbieter verfügt über einen entsprechenden Mindestbuild für Azure Stack. Hier ist angegeben, welche Azure Stack-Releaseversion für die Installation dieser Version des SQL-Ressourcenanbieters erforderlich ist:

> |Azure Stack-Mindestversion|Version des SQL-Ressourcenanbieters|
> |-----|-----|
> |Version 1910 (1.1910.0.58)|[SQL RP Version 1.1.47.0](https://aka.ms/azurestacksqlrp11470)|  
> |     |     |

> [!IMPORTANT]
> Wenden Sie das mindestens erforderliche Azure Stack-Update auf Ihr integriertes Azure Stack-System an, oder stellen Sie das aktuelle Azure Stack Development Kit (ASDK) bereit, bevor Sie die neueste Version des SQL-Ressourcenanbieters bereitstellen.

## <a name="new-features-and-fixes"></a>Neue Features und Fehlerbehebungen

Diese Version des Azure Stack SQL-Ressourcenanbieters ist ein Hotfixrelease, mit dem der Ressourcenanbieter mit einigen der neuesten Portaländerungen im Update 1910 ohne neue Features kompatibel gemacht werden soll.

Darüber hinaus unterstützt sie das aktuelle Azure Stack-API-Versionsprofil „2019-03-01-hybrid“ sowie das Azure Stack PowerShell-Modul 1.8.0. Daher müssen während der Bereitstellung und Aktualisierung keine bestimmten Verlaufsversionen von Modulen installiert werden.

Führen Sie im Anschluss an das Azure Stack-Upgrade auf das Update 1910 die Schritte des Ressourcenanbieter-Updateprozesses aus, um den SQL-Ressourcenanbieter-Hotfix 1.1.47.0 anzuwenden. Er hilft bei der Behebung eines bekannten Problems im Administratorportal, aufgrund dessen die Kapazitätsüberwachung im SQL-Ressourcenanbieter weiterhin geladen wird.

## <a name="known-issues"></a>Bekannte Probleme

None (Keine):

## <a name="next-steps"></a>Nächste Schritte
[Informieren Sie sich über den SQL-Ressourcenanbieter](azure-stack-sql-resource-provider.md).

[Bereiten Sie sich auf die Bereitstellung des SQL-Ressourcenanbieters vor](azure-stack-sql-resource-provider-deploy.md#prerequisites).

[Führen Sie für den SQL-Ressourcenanbieter ein Upgrade von einer früheren Version durch](azure-stack-sql-resource-provider-update.md). 
