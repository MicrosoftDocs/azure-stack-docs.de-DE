---
title: 'Azure Stack Hub-MySQL-Ressourcenanbieter 1.1.93.0: Versionshinweise'
description: Lesen Sie die Versionshinweise, um zu erfahren, welche Neuerungen im Update 1.1.93.0 zum Azure Stack Hub-MySQL-Ressourcenanbieter enthalten sind.
author: caoyang
ms.topic: article
ms.date: 09/22/2020
ms.author: caoyang
ms.reviewer: xiaofmao
ms.lastreviewed: 09/22/2020
ms.openlocfilehash: f298e2e9e55d8057a63264fb347069ef07204a16
ms.sourcegitcommit: 69cfff119ab425d0fbb71e38d1480d051fc91216
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/30/2020
ms.locfileid: "91585972"
---
# <a name="mysql-resource-provider-11930-release-notes"></a>Versionshinweise zum MySQL-Ressourcenanbieter 1.1.93.0

In diesen Versionshinweisen werden die Verbesserungen und bekannten Probleme in Version 1.1.93.0 des MySQL-Ressourcenanbieters beschrieben.

## <a name="build-reference"></a>Buildreferenz
Laden Sie die Binärdatei des MySQL-Ressourcenanbieters herunter, und führen Sie dann den Self-Extractor aus, um den Inhalt in ein temporäres Verzeichnis zu extrahieren. Der Ressourcenanbieter verfügt über einen entsprechenden Mindestbuild für Azure Stack Hub. Hier ist angegeben, welche Azure Stack Hub-Releaseversion für die Installation dieser Version des MySQL-Ressourcenanbieters erforderlich ist:

> |Unterstützte Azure Stack Hub-Version|Version des MySQL-Ressourcenanbieters|
> |-----|-----|
> |Version 2005|[MySQL-RP-Version 1.1.93.0](https://aka.ms/azshmysqlrp11930)|  
> |     |     |

> [!IMPORTANT]
> Wenden Sie das mindestens erforderliche Azure Stack Hub-Update auf Ihr integriertes Azure Stack Hub-System an, bevor Sie die neueste Version des MySQL-Ressourcenanbieters bereitstellen.

## <a name="new-features-and-fixes"></a>Neue Features und Fehlerbehebungen

Diese Version des Azure Stack Hub-MySQL-Ressourcenanbieters umfasst die folgenden Verbesserungen und Fehlerbehebungen:

- **Aktualisieren der Basis-VM auf eine spezialisierte Windows Server-Instanz**: Diese Windows Server-Version wurde speziell für die Azure Stack Hub-Add-On-RP-Infrastruktur konzipiert und wird auf dem Mandanten-Marketplace nicht angezeigt. Laden Sie das Image **Microsoft AzureStack Add-On RP Windows Server INTERNAL ONLY** herunter, bevor Sie diese Version des MySQL-Ressourcenanbieters bereitstellen oder ein Upgrade auf diese Version durchführen.
- **Unterstützung für das Entfernen von verwaisten Metadaten der Datenbank und des Hostservers**: Wenn für einen Hostserver keine Verbindung mehr hergestellt werden kann, erhält der Mandant die Option, die verwaisten Metadaten der Datenbank aus dem Portal zu entfernen. Wenn keine verwaisten Datenbankmetadaten mehr mit dem Hostserver verknüpft sind, kann der Operator die verwaisten Hostservermetadaten aus dem Administratorportal entfernen.
- **Definieren von KeyVaultPfxPassword als optionales Argument beim Rotieren von Geheimnissen**: Ausführliche Informationen dazu finden Sie in [diesem Dokument](azure-stack-sql-resource-provider-maintain.md#secrets-rotation).
- **Weitere Fehlerbehebungen**

Es wird empfohlen, nach dem Upgrade von Azure Stack Hub auf das Release 2005 den MySQL-Ressourcenanbieter in Version 1.1.93.0 anzuwenden.

## <a name="known-issues"></a>Bekannte Probleme
Keine.

## <a name="next-steps"></a>Nächste Schritte

- [Informieren Sie sich über den MySQL-Ressourcenanbieter](azure-stack-mysql-resource-provider.md).
- [Bereiten Sie sich auf die Bereitstellung des MySQL-Ressourcenanbieters vor](azure-stack-mysql-resource-provider-deploy.md#prerequisites).
- [Führen Sie für den MySQL-Ressourcenanbieter ein Upgrade von einer früheren Version durch](azure-stack-mysql-resource-provider-update.md).
