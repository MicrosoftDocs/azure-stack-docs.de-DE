---
title: 'Azure Stack Hub-MySQL-Ressourcenanbieter 1.1.33.0: Versionshinweise | Microsoft-Dokumentation'
description: Lesen Sie die Versionshinweise, um zu erfahren, welche Neuerungen im Update zum MySQL-Ressourcenanbieter 1.1.33.0 von Azure Stack Hub enthalten sind.
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
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: jiahan
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 942d3b70650b335f1717083e4f9ba14d03e97132
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76534973"
---
# <a name="mysql-resource-provider-11330--release-notes"></a>Versionshinweise zum MySQL-Ressourcenanbieter 1.1.33.0

In diesen Versionshinweisen werden die Verbesserungen und bekannten Probleme in Version 1.1.33.0 des MySQL-Ressourcenanbieters beschrieben.

## <a name="build-reference"></a>Buildreferenz
Laden Sie die Binärdatei des MySQL-Ressourcenanbieters herunter, und führen Sie dann den Self-Extractor aus, um den Inhalt in ein temporäres Verzeichnis zu extrahieren. Der Ressourcenanbieter verfügt über einen entsprechenden Mindestbuild für Azure Stack Hub. Hier ist angegeben, welche Azure Stack Hub-Releaseversion für die Installation dieser Version des MySQL-Ressourcenanbieters erforderlich ist:

> |Azure Stack Hub-Mindestversion|Version des MySQL-Ressourcenanbieters|
> |-----|-----|
> |Version 1808 (1.1808.0.97)|[MySQL RP-Version 1.1.33.0](https://aka.ms/azurestackmysqlrp11330)|  
> |     |     |

> [!IMPORTANT]
> Wenden Sie das mindestens erforderliche Azure Stack Hub-Update auf Ihr integriertes Azure Stack Hub-System an, oder stellen Sie das aktuelle Azure Stack Development Kit (ASDK) bereit, bevor Sie die neueste Version des MySQL-Ressourcenanbieters bereitstellen.

## <a name="new-features-and-fixes"></a>Neue Features und Fehlerbehebungen
Diese Version des Azure Stack Hub-MySQL-Ressourcenanbieters umfasst die folgenden Verbesserungen und Fehlerbehebungen:

### <a name="fixes"></a>Fehlerbehebungen

- **MySQL-Ressourcenanbieter-Portalerweiterung wählt ggf. das falsche Abonnement:** Der MySQL-Ressourcenanbieter ermittelt anhand von Azure Resource Manager-Aufrufen, welches Dienstadministratorabonnement verwendet werden soll. Es kann sein, dass dies nicht das *Standardanbieterabonnement* ist. In diesem Fall funktioniert der MySQL-Ressourcenanbieter nicht normal.

- **MySQL-Hostserver listet keine gehosteten Datenbanken auf.** Vom Benutzer erstellte Datenbanken werden unter Umständen nicht aufgeführt, wenn für MySQL-Hostserver Mandantenressourcen angezeigt werden.

- **Bei der Bereitstellung des vorherigen MySQL-Ressourcenanbieters (1.1.30.0) tritt ggf. ein Fehler auf, wenn TLS 1.2 nicht aktiviert ist:** MySQL-Ressourcenanbieter 1.1.33.0 wurde aktualisiert, um TLS 1.2 zu ermöglichen, wenn der Ressourcenanbieter bereitgestellt oder aktualisiert wird oder Geheimnisse rotiert werden.

- **Fehler bei Rotation von Geheimnissen für MySQL-Ressourcenanbieter:** Problem behoben, das beim Rotieren von Geheimnissen zu folgendem Fehler geführt hat: `New-AzureRmResourceGroupDeployment - Error: Code=InvalidDeploymentParameterValue; Message=The value of deployment parameter 'StorageAccountBlobUri' is null.`

## <a name="known-issues"></a>Bekannte Probleme

- **Dauer von bis zu einer Stunde bis zur Anzeige von MySQL SKUs im Portal**. Es kann bis zu einer Stunde dauern, bis die neu erstellten SKUs beim Erstellen neuer MySQL-Datenbanken angezeigt werden. 

    **Problemumgehung**: Keine.

- **Wiederverwendung von MySQL-Anmeldungen**. Der Versuch einer neuen MySQL-Anmeldung mit dem gleichen Benutzernamen wie eine vorhandene Anmeldung unter demselben Abonnement führt dazu, dass die gleiche Anmeldung und das vorhandene Kennwort wiederverwendet werden.

    **Problemumgehung**: Verwenden Sie unterschiedliche Benutzernamen, wenn Sie neue Anmeldungen unter demselben Abonnement oder Anmeldungen mit demselben Benutzernamen unter unterschiedlichen Abonnements erstellen.

- **Dateninkonsistenz aufgrund von gemeinsam genutzten MySQL-Anmeldungen:** Wenn eine MySQL-Anmeldung für mehrere MySQL-Datenbanken unter demselben Abonnement gemeinsam genutzt wird, führt eine Änderung des Anmeldekennworts zu Dateninkonsistenz.

    **Problemumgehung**: Verwenden Sie für unterschiedliche Datenbanken unter demselben Abonnement immer unterschiedliche Anmeldungen.


### <a name="known-issues-for-cloud-admins-operating-azure-stack-hub"></a>Bekannte Probleme für Cloudadministratoren, die Azure Stack Hub betreiben
Lesen Sie die Dokumentation in den [Versionshinweisen zu Azure Stack Hub](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Nächste Schritte
[Informieren Sie sich über den MySQL-Ressourcenanbieter](azure-stack-mysql-resource-provider.md).

[Bereiten Sie sich auf die Bereitstellung des MySQL-Ressourcenanbieters vor](azure-stack-mysql-resource-provider-deploy.md#prerequisites).

[Führen Sie für den MySQL-Ressourcenanbieter ein Upgrade von einer früheren Version durch](azure-stack-mysql-resource-provider-update.md). 