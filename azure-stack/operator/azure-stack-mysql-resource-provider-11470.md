---
title: 'Azure Stack-MySQL-Ressourcenanbieter 1.1.47.0: Versionshinweise | Microsoft-Dokumentation'
description: Lesen Sie die Versionshinweise, um zu erfahren, welche Neuerungen im Update zum MySQL-Ressourcenanbieter 1.1.47.0 von Azure Stack enthalten sind.
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
ms.openlocfilehash: 979aa822c981b4d34cb430e69dc15f80661818fe
ms.sourcegitcommit: 11e0c2d9abbc0a2506f992976b3c9f8ca4e746b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74823522"
---
# <a name="mysql-resource-provider-11470-release-notes"></a>Versionshinweise zum MySQL-Ressourcenanbieter 1.1.47.0

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

In diesen Versionshinweisen werden die Verbesserungen und bekannten Probleme in Version 1.1.47.0 des MySQL-Ressourcenanbieters beschrieben.

## <a name="build-reference"></a>Buildreferenz
Laden Sie die Binärdatei des MySQL-Ressourcenanbieters herunter, und führen Sie dann den Self-Extractor aus, um den Inhalt in ein temporäres Verzeichnis zu extrahieren. Der Ressourcenanbieter verfügt über einen entsprechenden Mindestbuild für Azure Stack. Hier ist angegeben, welche Azure Stack-Releaseversion für die Installation dieser Version des MySQL-Ressourcenanbieters erforderlich ist:

> |Azure Stack-Mindestversion|Version des MySQL-Ressourcenanbieters|
> |-----|-----|
> |Version 1910 (1.1910.0.58)|[MySQL RP-Version 1.1.47.0](https://aka.ms/azurestackmysqlrp11470)|  
> |     |     |

> [!IMPORTANT]
> Wenden Sie das mindestens erforderliche Azure Stack-Update auf Ihr integriertes Azure Stack-System an, oder stellen Sie das aktuelle Azure Stack Development Kit (ASDK) bereit, bevor Sie die neueste Version des MySQL-Ressourcenanbieters bereitstellen.

## <a name="new-features-and-fixes"></a>Neue Features und Fehlerbehebungen

Diese Version des Azure Stack MySQL-Ressourcenanbieters ist ein Hotfixrelease, mit dem der Ressourcenanbieter mit einigen der neuesten Portaländerungen im Update 1910 ohne neue Features kompatibel gemacht werden soll.

Darüber hinaus unterstützt sie das aktuelle Azure Stack-API-Versionsprofil „2019-03-01-hybrid“ sowie das Azure Stack PowerShell-Modul 1.8.0. Daher müssen während der Bereitstellung und Aktualisierung keine bestimmten Verlaufsversionen von Modulen installiert werden.

Es wird empfohlen, nach dem Upgrade auf das Release 1910 den Hotfix für den MySQL-Ressourcenanbieter 1.1.47.0 anzuwenden.

## <a name="known-issues"></a>Bekannte Probleme

None (Keine):

## <a name="next-steps"></a>Nächste Schritte
[Informieren Sie sich über den MySQL-Ressourcenanbieter](azure-stack-mysql-resource-provider.md).

[Bereiten Sie sich auf die Bereitstellung des MySQL-Ressourcenanbieters vor](azure-stack-mysql-resource-provider-deploy.md#prerequisites).

[Führen Sie für den MySQL-Ressourcenanbieter ein Upgrade von einer früheren Version durch](azure-stack-mysql-resource-provider-update.md). 
