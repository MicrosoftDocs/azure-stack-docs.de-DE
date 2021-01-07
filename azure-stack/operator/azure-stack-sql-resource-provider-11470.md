---
title: 'Azure Stack Hub-SQL-Ressourcenanbieter 1.1.47.0: Versionshinweise'
description: Hier werden die Neuerungen im aktuellen Update für den Azure Stack Hub-SQL-Ressourcenanbieter beschrieben, z. B. neue Features, Hotfixes und bekannte Probleme.
author: PatAltimore
ms.topic: article
ms.date: 11/26/2019
ms.author: patricka
ms.reviewer: xiaofmao
ms.lastreviewed: 11/26/2019
ms.openlocfilehash: d95be92f4cab37c41e1802fdcb18969e3276f459
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97869218"
---
# <a name="sql-resource-provider-11470-release-notes"></a>Versionshinweise zum SQL-Ressourcenanbieter 1.1.47.0

In diesen Versionshinweisen werden die Verbesserungen und bekannten Probleme in Version 1.1.47.0 des SQL-Ressourcenanbieters beschrieben.

## <a name="build-reference"></a>Buildreferenz

Laden Sie die Binärdatei des SQL-Ressourcenanbieters herunter, und führen Sie dann den Self-Extractor aus, um den Inhalt in ein temporäres Verzeichnis zu extrahieren. Der Ressourcenanbieter verfügt über einen entsprechenden Minimalbuild für Azure Stack Hub. Im Folgenden finden Sie die Azure Stack Hub-Mindestversion, die für die Installation dieser Version des SQL-Ressourcenanbieters erforderlich ist:

> |Azure Stack Hub-Mindestversion|Version des SQL-Ressourcenanbieters|
> |-----|-----|
> |Version 1910 (1.1910.0.58)|[SQL RP Version 1.1.47.0](https://aka.ms/azurestacksqlrp11470)|  
> |     |     |

> [!IMPORTANT]
> Wenden Sie das mindestens erforderliche Azure Stack Hub-Update auf Ihr integriertes Azure Stack Hub-System an, bevor Sie die neueste Version des SQL-Ressourcenanbieters bereitstellen.

## <a name="new-features-and-fixes"></a>Neue Features und Fehlerbehebungen

Diese Version des Azure Stack Hub-SQL-Ressourcenanbieters ist ein Hotfixrelease, mit dem der Ressourcenanbieter mit den neuesten Portaländerungen im Update 1910 kompatibel gemacht werden soll. Die Version enthält keine neuen Features.

Darüber hinaus unterstützt sie das aktuelle Azure Stack Hub-API-Versionsprofil „2019-03-01-hybrid“ sowie das Azure Stack Hub PowerShell-Modul 1.8.0. Daher müssen während der Bereitstellung und Aktualisierung keine bestimmten Verlaufsversionen von Modulen installiert werden.

Führen Sie im Anschluss an das Azure Stack Hub-Upgrade auf das Update 1910 die Schritte des Ressourcenanbieter-Updateprozesses aus, um den SQL-Ressourcenanbieter-Hotfix 1.1.47.0 anzuwenden. Er hilft bei der Behebung eines bekannten Problems im Administratorportal, aufgrund dessen die Kapazitätsüberwachung im SQL-Ressourcenanbieter weiterhin geladen wird.

## <a name="known-issues"></a>Bekannte Probleme

Wenn eine [Zertifikatrotation](azure-stack-mysql-resource-provider-maintain.md#secrets-rotation) für integrierte Azure Stack Hub-Systeme erfolgt, ist das KeyVaultPfxPassword-Argument obligatorisch, auch wenn nicht beabsichtigt ist, das Kennwort für das Key Vault-Zertifikat zu aktualisieren.

## <a name="next-steps"></a>Nächste Schritte

- [Informieren Sie sich über den SQL-Ressourcenanbieter](azure-stack-sql-resource-provider.md).
- [Bereiten Sie sich auf die Bereitstellung des SQL-Ressourcenanbieters vor](azure-stack-sql-resource-provider-deploy.md#prerequisites).
- [Führen Sie für den SQL-Ressourcenanbieter ein Upgrade von einer früheren Version durch](azure-stack-sql-resource-provider-update.md).
