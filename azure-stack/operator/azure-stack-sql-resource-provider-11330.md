---
title: Azure Stack-SQL-Ressourcenanbieter 1.1.30.0 – Versionshinweise | Microsoft-Dokumentation
description: Es wird beschrieben, was im neuesten Update für den Azure Stack-SQL-Ressourcenanbieter enthalten ist, z.B. bekannte Probleme und der Downloadort.
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
ms.date: 05/06/2019
ms.author: mabrigg
ms.reviewer: jiahan
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 5e5e44ed9eca3cde0844c0eafbc4188bd1449da2
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618151"
---
# <a name="sql-resource-provider-11330-release-notes"></a>Versionshinweise zum SQL-Ressourcenanbieter 1.1.33.0

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

In diesen Versionshinweisen werden die Verbesserungen und bekannten Probleme in Version 1.1.33.0 des SQL-Ressourcenanbieters beschrieben.

## <a name="build-reference"></a>Buildreferenz
Laden Sie die Binärdatei des SQL-Ressourcenanbieters herunter, und führen Sie dann den Self-Extractor aus, um den Inhalt in ein temporäres Verzeichnis zu extrahieren. Der Ressourcenanbieter verfügt über einen entsprechenden Mindestbuild für Azure Stack. Hier ist angegeben, welche Azure Stack-Releaseversion für die Installation dieser Version des SQL-Ressourcenanbieters erforderlich ist:

> |Azure Stack-Mindestversion|Version des SQL-Ressourcenanbieters|
> |-----|-----|
> |Version 1808 (1.1808.0.97)|[SQL RP Version 1.1.33.0](https://aka.ms/azurestacksqlrp11330)|  
> |     |     |

> [!IMPORTANT]
> Wenden Sie das mindestens erforderliche Azure Stack-Update auf Ihr integriertes Azure Stack-System an, oder stellen Sie das aktuelle Azure Stack Development Kit (ASDK) bereit, bevor Sie die neueste Version des SQL-Ressourcenanbieters bereitstellen.

## <a name="new-features-and-fixes"></a>Neue Features und Fehlerbehebungen
Diese Version des Azure Stack-SQL-Ressourcenanbieters enthält die folgenden Verbesserungen und Fehlerbehebungen:

### <a name="fixes"></a>Fehlerbehebungen
- **SQL-Ressourcenanbieter-Portalerweiterung wählt eventuell das falsche Abonnement aus.** Der SQL-Ressourcenanbieter ermittelt anhand von Azure Resource Manager-Aufrufen, welches Dienstadministratorabonnement zuerst verwendet werden soll. Es kann sein, dass dies nicht das *Standardanbieterabonnement* ist. In diesem Fall funktioniert der SQL-Ressourcenanbieter nicht normal. 

- **SQL-Hostserver listet keine gehosteten Datenbanken auf.** Vom Benutzer erstellte Datenbanken werden unter Umständen nicht aufgeführt, wenn für SQL-Hostserver Mandantenressourcen angezeigt werden.

- **Bei der Bereitstellung des vorherigen SQL-Ressourcenanbieters (1.1.30.0) tritt eventuell ein Fehler auf, wenn TLS 1.2 nicht aktiviert ist.** SQL-Ressourcenanbieter 1.1.33.0 wurde aktualisiert, um TLS 1.2 zu ermöglichen, wenn der Ressourcenanbieter bereitgestellt oder aktualisiert wird oder Geheimnisse rotiert werden. 

- **Fehler bei Rotation von Geheimnissen für SQL-Ressourcenanbieter.** Problem behoben, das beim Rotieren von Geheimnissen zu folgendem Fehler geführt hat: `New-AzureRmResourceGroupDeployment - Error: Code=InvalidDeploymentParameterValue; Message=The value of deployment parameter 'StorageAccountBlobUri' is null.`

## <a name="known-issues"></a>Bekannte Probleme 

- **Dauer von bis zu einer Stunde bis zur Anzeige von SQL SKUs im Portal**: Es kann bis zu einer Stunde dauern, bis die neu erstellten SKUs beim Erstellen neuer SQL-Datenbanken angezeigt werden. 

    **Problemumgehung**: None (Keine):

- **Wiederverwendung von SQL-Anmeldungen**: Der Versuch einer neuen SQL-Anmeldung mit dem gleichen Benutzernamen wie eine vorhandene Anmeldung unter demselben Abonnement führt dazu, dass die gleiche Anmeldung und das vorhandene Kennwort wiederverwendet werden. 

    **Problemumgehung**: Verwenden Sie unterschiedliche Benutzernamen, wenn Sie neue Anmeldungen unter demselben Abonnement oder Anmeldungen mit demselben Benutzernamen unter unterschiedlichen Abonnements erstellen.

- **Dateninkonsistenz aufgrund von gemeinsam genutzten SQL-Anmeldungen**: Wenn eine SQL-Anmeldung für mehrere SQL-Datenbanken unter demselben Abonnement gemeinsam genutzt wird, führt eine Änderung des Anmeldekennworts zu Dateninkonsistenz.

    **Problemumgehung**: Verwenden Sie für unterschiedliche Datenbanken unter demselben Abonnement immer unterschiedliche Anmeldungen.

- **SQL-Ressourcenanbieter kann SQL Server Always On-Listener nicht hinzufügen.** Bei Verwendung der Listener-IP-Adresse des SQL Server Always On-Listeners kann die SQL-Ressourcenanbieter-VM den Hostnamen des Listeners nicht auflösen.

    **Problemumgehung**: Stellen Sie sicher, dass DNS ordnungsgemäß funktioniert, um die Listener-IP-Adresse in den Hostnamen des Listeners aufzulösen.

### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Bekannte Probleme von Cloudadministratoren, die Azure Stack betreiben
Lesen Sie die Dokumentation in den [Versionshinweisen zu Azure Stack](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Nächste Schritte
[Informieren Sie sich über den SQL-Ressourcenanbieter](azure-stack-sql-resource-provider.md).

[Bereiten Sie sich auf die Bereitstellung des SQL-Ressourcenanbieters vor](azure-stack-sql-resource-provider-deploy.md#prerequisites).

[Führen Sie für den SQL-Ressourcenanbieter ein Upgrade von einer früheren Version durch](azure-stack-sql-resource-provider-update.md). 