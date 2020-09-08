---
title: 'Azure Stack Hub-MySQL-Ressourcenanbieter 1.1.47.0: Versionshinweise'
description: Lesen Sie die Versionshinweise, um zu erfahren, welche Neuerungen im Update zum MySQL-Ressourcenanbieter 1.1.47.0 von Azure Stack Hub enthalten sind.
author: justinha
ms.topic: article
ms.date: 11/26/2019
ms.author: justinha
ms.reviewer: xiaofmao
ms.lastreviewed: 11/26/2019
ms.openlocfilehash: 5bc03134a859182909e62c60159049d639eb8ffe
ms.sourcegitcommit: 08a421ab5792ab19cc06b849763be22f051e6d78
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/02/2020
ms.locfileid: "89364676"
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

Diese Version des Azure Stack Hub-MySQL-Ressourcenanbieters ist ein Hotfixrelease, mit dem der Ressourcenanbieter mit einigen der neuesten Portaländerungen im Update 1910 kompatibel gemacht werden soll. Die Version enthält keine neuen Features.

Darüber hinaus unterstützt sie das aktuelle Azure Stack Hub-API-Versionsprofil „2019-03-01-hybrid“ sowie das Azure Stack Hub PowerShell-Modul 1.8.0. Daher müssen während der Bereitstellung und Aktualisierung keine bestimmten Verlaufsversionen von Modulen installiert werden.

Es wird empfohlen, nach dem Upgrade von Azure Stack Hub auf das Release 1910 den Hotfix 1.1.47.0 für den MySQL-Ressourcenanbieter anzuwenden.

## <a name="known-issues"></a>Bekannte Probleme

Wenn eine [Zertifikatrotation](azure-stack-mysql-resource-provider-maintain.md#secrets-rotation) für integrierte Azure Stack Hub-Systeme erfolgt, ist das KeyVaultPfxPassword-Argument obligatorisch, auch wenn nicht beabsichtigt ist, das Kennwort für das Key Vault-Zertifikat zu aktualisieren.

## <a name="next-steps"></a>Nächste Schritte

- [Informieren Sie sich über den MySQL-Ressourcenanbieter](azure-stack-mysql-resource-provider.md).
- [Bereiten Sie sich auf die Bereitstellung des MySQL-Ressourcenanbieters vor](azure-stack-mysql-resource-provider-deploy.md#prerequisites).
- [Führen Sie für den MySQL-Ressourcenanbieter ein Upgrade von einer früheren Version durch](azure-stack-mysql-resource-provider-update.md).
