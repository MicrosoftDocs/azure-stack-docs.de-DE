---
title: 'Azure Stack Hub-MySQL-Ressourcenanbieter 1.1.47.0: Versionshinweise'
description: Lesen Sie die Versionshinweise, um zu erfahren, welche Neuerungen im Update zum MySQL-Ressourcenanbieter 1.1.47.0 von Azure Stack Hub enthalten sind.
author: justinha
ms.topic: article
ms.date: 11/26/2019
ms.author: justinha
ms.reviewer: xiaofmao
ms.lastreviewed: 11/26/2019
ms.openlocfilehash: 4984f36b4632f2bcb204c93ef409ea82a7f299f5
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77698826"
---
# <a name="mysql-resource-provider-11470-release-notes"></a>Versionshinweise zum MySQL-Ressourcenanbieter 1.1.47.0

In diesen Versionshinweisen werden die Verbesserungen und bekannten Probleme in Version 1.1.47.0 des MySQL-Ressourcenanbieters beschrieben.

## <a name="build-reference"></a>Buildreferenz
Laden Sie die Binärdatei des MySQL-Ressourcenanbieters herunter, und führen Sie dann den Self-Extractor aus, um den Inhalt in ein temporäres Verzeichnis zu extrahieren. Der Ressourcenanbieter verfügt über einen entsprechenden Mindestbuild für Azure Stack Hub. Hier ist angegeben, welche Azure Stack Hub-Releaseversion für die Installation dieser Version des MySQL-Ressourcenanbieters erforderlich ist:

> |Azure Stack Hub-Mindestversion|Version des MySQL-Ressourcenanbieters|
> |-----|-----|
> |Version 1910 (1.1910.0.58)|[MySQL RP-Version 1.1.47.0](https://aka.ms/azurestackmysqlrp11470)|  
> |     |     |

> [!IMPORTANT]
> Wenden Sie das mindestens erforderliche Azure Stack Hub-Update auf Ihr integriertes Azure Stack Hub-System an, oder stellen Sie das aktuelle Azure Stack Development Kit (ASDK) bereit, bevor Sie die neueste Version des MySQL-Ressourcenanbieters bereitstellen.

## <a name="new-features-and-fixes"></a>Neue Features und Fehlerbehebungen

Diese Version des Azure Stack Hub MySQL-Ressourcenanbieters ist ein Hotfixrelease, mit dem der Ressourcenanbieter mit einigen der neuesten Portaländerungen im Update 1910 ohne neue Features kompatibel gemacht werden soll.

Darüber hinaus unterstützt sie das aktuelle Azure Stack Hub-API-Versionsprofil „2019-03-01-hybrid“ sowie das Azure Stack Hub PowerShell-Modul 1.8.0. Daher müssen während der Bereitstellung und Aktualisierung keine bestimmten Verlaufsversionen von Modulen installiert werden.

Es wird empfohlen, nach dem Upgrade auf das Release 1910 den Hotfix für den MySQL-Ressourcenanbieter 1.1.47.0 anzuwenden.

## <a name="known-issues"></a>Bekannte Probleme

Keine.

## <a name="next-steps"></a>Nächste Schritte
[Informieren Sie sich über den MySQL-Ressourcenanbieter](azure-stack-mysql-resource-provider.md).

[Bereiten Sie sich auf die Bereitstellung des MySQL-Ressourcenanbieters vor](azure-stack-mysql-resource-provider-deploy.md#prerequisites).

[Führen Sie für den MySQL-Ressourcenanbieter ein Upgrade von einer früheren Version durch](azure-stack-mysql-resource-provider-update.md). 
