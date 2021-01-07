---
title: 'Azure Stack Hub-SQL-Ressourcenanbieter 1.1.93.x: Versionshinweise'
description: Lesen Sie die Versionshinweise, um zu erfahren, welche Neuerungen im Update 1.1.93.x zum Azure Stack Hub-SQL-Ressourcenanbieter enthalten sind.
author: caoyang
ms.topic: article
ms.date: 09/22/2020
ms.author: caoyang
ms.reviewer: xiaofmao
ms.lastreviewed: 09/22/2020
ms.openlocfilehash: 80caef7369a3cf5ebe79338bc907fa9b31d1a759
ms.sourcegitcommit: a745662c7a5a18f135accf3f70d8508b57e83e2b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/22/2020
ms.locfileid: "97737911"
---
# <a name="sql-resource-provider-1193x-release-notes"></a>Versionshinweise zum SQL-Ressourcenanbieter 1.1.93.x

In diesen Versionshinweisen werden die Verbesserungen und bekannten Probleme in Version 1.1.93.x des SQL-Ressourcenanbieters beschrieben.

## <a name="build-reference"></a>Buildreferenz
Laden Sie die Binärdatei des SQL-Ressourcenanbieters herunter, und führen Sie dann den Self-Extractor aus, um den Inhalt in ein temporäres Verzeichnis zu extrahieren. Der Ressourcenanbieter verfügt über einen entsprechenden Minimalbuild für Azure Stack Hub. Im Folgenden finden Sie die Azure Stack Hub-Mindestversion, die für die Installation dieser Version des SQL-Ressourcenanbieters erforderlich ist:

> |Unterstützte Azure Stack Hub-Version|Version des SQL-Ressourcenanbieters|
> |-----|-----|
> |Version 2008, 2005|[SQL-Ressourcenanbieterversion 1.1.93.1](https://aka.ms/azshsqlrp11931)|  
> |     |     |

> [!IMPORTANT]
> Wenden Sie das mindestens erforderliche Azure Stack Hub-Update auf Ihr integriertes Azure Stack Hub-System an, bevor Sie die neueste Version des MySQL-Ressourcenanbieters bereitstellen.

## <a name="new-features-and-fixes"></a>Neue Features und Fehlerbehebungen

Diese Version des Azure Stack Hub-SQL-Ressourcenanbieters enthält die folgenden Verbesserungen und Fehlerbehebungen:

- **Aktualisieren der Basis-VM auf eine spezialisierte Windows Server-Instanz**: Diese Windows Server-Version wurde speziell für die Azure Stack Hub-Add-On-RP-Infrastruktur konzipiert und wird auf dem Mandanten-Marketplace nicht angezeigt. Laden Sie das Image **Microsoft AzureStack Add-On RP Windows Server** herunter, bevor Sie diese Version des SQL-Ressourcenanbieters bereitstellen oder ein Upgrade auf diese Version durchführen.
- **Unterstützung für das Entfernen von verwaisten Metadaten der Datenbank und des Hostservers**: Wenn für einen Hostserver keine Verbindung mehr hergestellt werden kann, erhält der Mandant die Option, die verwaisten Metadaten der Datenbank aus dem Portal zu entfernen. Wenn keine verwaisten Datenbankmetadaten mehr mit dem Hostserver verknüpft sind, kann der Operator die verwaisten Hostservermetadaten aus dem Administratorportal entfernen.
- **Definieren von KeyVaultPfxPassword als optionales Argument beim Rotieren von Geheimnissen**: Ausführliche Informationen dazu finden Sie in [diesem Dokument](azure-stack-sql-resource-provider-maintain.md#secrets-rotation).
- **Weitere Fehlerbehebungen**

Wir empfehlen Ihnen, nach dem Upgrade von Azure Stack Hub auf das Release 2005 die Version 1.1.93.1 des SQL-Ressourcenanbieters anzuwenden.

## <a name="known-issue"></a>Bekanntes Problem
Bei der Bereitstellung der Version 1.1.93.0 tritt ggf. ein Fehler auf, wenn das falsche AzureRmContext-Element verwendet wird. Wir empfehlen Ihnen, das direkte Upgrade auf Version 1.1.93.1 durchzuführen. Falls Sie das Upgrade auf Version 1.1.93.0 bereits erfolgreich durchgeführt haben, können Sie Version 1.1.93.1 problemlos überspringen.

## <a name="next-steps"></a>Nächste Schritte

- [Informieren Sie sich über den SQL-Ressourcenanbieter](azure-stack-sql-resource-provider.md).
- [Bereiten Sie sich auf die Bereitstellung des SQL-Ressourcenanbieters vor](azure-stack-sql-resource-provider-deploy.md#prerequisites).
- [Führen Sie für den SQL-Ressourcenanbieter ein Upgrade von einer früheren Version durch](azure-stack-sql-resource-provider-update.md).

