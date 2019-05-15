---
title: Azure Stack-MySQL-Ressourcenanbieter 1.1.30.0 – Versionshinweise | Microsoft-Dokumentation
description: Erfahren Sie, was im neuesten Update für den Azure Stack-MySQL-Ressourcenanbieter enthalten ist, welche bekannten Probleme vorliegen und wo Sie den Download finden.
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
ms.date: 01/09/2019
ms.author: mabrigg
ms.reviewer: jiahan
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: b5e649768f075f4e448520c648a77aa64351842a
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618093"
---
# <a name="mysql-resource-provider-11330--release-notes"></a>Versionshinweise zum MySQL-Ressourcenanbieter 1.1.33.0

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

In diesen Versionshinweisen werden die Verbesserungen und bekannten Probleme in Version 1.1.33.0 des MySQL-Ressourcenanbieters beschrieben.

## <a name="build-reference"></a>Buildreferenz
Laden Sie die Binärdatei des MySQL-Ressourcenanbieters herunter, und führen Sie dann den Self-Extractor aus, um den Inhalt in ein temporäres Verzeichnis zu extrahieren. Der Ressourcenanbieter verfügt über einen entsprechenden Mindestbuild für Azure Stack. Hier ist angegeben, welche Azure Stack-Releaseversion für die Installation dieser Version des MySQL-Ressourcenanbieters erforderlich ist:

> |Azure Stack-Mindestversion|Version des MySQL-Ressourcenanbieters|
> |-----|-----|
> |Version 1808 (1.1808.0.97)|[MySQL RP-Version 1.1.33.0](https://aka.ms/azurestackmysqlrp11330)|  
> |     |     |

> [!IMPORTANT]
> Wenden Sie das mindestens erforderliche Azure Stack-Update auf Ihr integriertes Azure Stack-System an, oder stellen Sie das aktuelle Azure Stack Development Kit (ASDK) bereit, bevor Sie die neueste Version des MySQL-Ressourcenanbieters bereitstellen.

## <a name="new-features-and-fixes"></a>Neue Features und Fehlerbehebungen
Diese Version des Azure Stack-MySQL-Ressourcenanbieters enthält die folgenden Verbesserungen und Fehlerbehebungen:

### <a name="fixes"></a>Fehlerbehebungen
- **MySQL-Ressourcenanbieter-Portalerweiterung wählt ggf. das falsche Abonnement:** Der MySQL-Ressourcenanbieter ermittelt anhand von Azure Resource Manager-Aufrufen, welches Dienstadministratorabonnement verwendet werden soll. Es kann sein, dass dies nicht das *Standardanbieterabonnement* ist. In diesem Fall funktioniert der MySQL-Ressourcenanbieter nicht normal. 

- **MySQL-Hostserver listet keine gehosteten Datenbanken auf:** Vom Benutzer erstellte Datenbanken werden unter Umständen nicht aufgeführt, wenn für MySQL-Hostserver Mandantenressourcen angezeigt werden.

- **Bei der Bereitstellung des vorherigen MySQL-Ressourcenanbieters (1.1.30.0) tritt ggf. ein Fehler auf, wenn TLS 1.2 nicht aktiviert ist:** MySQL-Ressourcenanbieter 1.1.33.0 wurde aktualisiert, um TLS 1.2 zu ermöglichen, wenn der Ressourcenanbieter bereitgestellt oder aktualisiert wird oder Geheimnisse rotiert werden. 

- **Fehler bei Rotation von Geheimnissen für MySQL-Ressourcenanbieter:** Problem behoben, das beim Rotieren von Geheimnissen zu folgendem Fehler geführt hat: `New-AzureRmResourceGroupDeployment - Error: Code=InvalidDeploymentParameterValue; Message=The value of deployment parameter 'StorageAccountBlobUri' is null.`

## <a name="known-issues"></a>Bekannte Probleme 

- **Dauer von bis zu einer Stunde bis zur Anzeige von MySQL SKUs im Portal**. Es kann bis zu einer Stunde dauern, bis die neu erstellten SKUs beim Erstellen neuer MySQL-Datenbanken angezeigt werden. 

    **Problemumgehung**: None (Keine):

- **Wiederverwendung von MySQL-Anmeldungen**. Der Versuch einer neuen MySQL-Anmeldung mit dem gleichen Benutzernamen wie eine vorhandene Anmeldung unter demselben Abonnement führt dazu, dass die gleiche Anmeldung und das vorhandene Kennwort wiederverwendet werden. 

    **Problemumgehung**: Verwenden Sie unterschiedliche Benutzernamen, wenn Sie neue Anmeldungen unter demselben Abonnement oder Anmeldungen mit demselben Benutzernamen unter unterschiedlichen Abonnements erstellen.

- **Dateninkonsistenz aufgrund von gemeinsam genutzten MySQL-Anmeldungen:** Wenn eine MySQL-Anmeldung für mehrere MySQL-Datenbanken unter demselben Abonnement gemeinsam genutzt wird, führt eine Änderung des Anmeldekennworts zu Dateninkonsistenz.

    **Problemumgehung**: Verwenden Sie für unterschiedliche Datenbanken unter demselben Abonnement immer unterschiedliche Anmeldungen.


### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Bekannte Probleme von Cloudadministratoren, die Azure Stack betreiben
Lesen Sie die Dokumentation in den [Versionshinweisen zu Azure Stack](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Nächste Schritte
[Informieren Sie sich über den MySQL-Ressourcenanbieter](azure-stack-mysql-resource-provider.md).

[Bereiten Sie sich auf die Bereitstellung des MySQL-Ressourcenanbieters vor](azure-stack-mysql-resource-provider-deploy.md#prerequisites).

[Führen Sie für den MySQL-Ressourcenanbieter ein Upgrade von einer früheren Version durch](azure-stack-mysql-resource-provider-update.md). 