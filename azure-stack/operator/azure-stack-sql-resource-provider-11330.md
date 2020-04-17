---
title: 'Azure Stack Hub-SQL-Ressourcenanbieter 1.1.33.0: Versionshinweise'
titleSuffix: Azure Stack Hub
description: Sehen Sie sich hier die Versionshinweise für das Update 1.1.33.0 des Azure Stack Hub-SQL-Ressourcenanbieters an.
author: bryanla
ms.topic: article
ms.date: 1/22/2020
ms.author: bryanla
ms.reviewer: jiahan
ms.lastreviewed: 01/09/2020
ms.openlocfilehash: 4aefe4e01950c7891bedb43eb1245f10608d1709
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "77697432"
---
# <a name="sql-resource-provider-11330-release-notes"></a>Versionshinweise zum SQL-Ressourcenanbieter 1.1.33.0

In diesen Versionshinweisen werden die Verbesserungen und bekannten Probleme in Version 1.1.33.0 des SQL-Ressourcenanbieters beschrieben.

## <a name="build-reference"></a>Buildreferenz
Laden Sie die Binärdatei des SQL-Ressourcenanbieters herunter, und führen Sie dann den Self-Extractor aus, um den Inhalt in ein temporäres Verzeichnis zu extrahieren. Der Ressourcenanbieter verfügt über einen entsprechenden Minimalbuild für Azure Stack Hub. Im Folgenden finden Sie die Azure Stack Hub-Mindestversion, die für die Installation dieser Version des SQL-Ressourcenanbieters erforderlich ist:

> |Azure Stack Hub-Mindestversion|Version des SQL-Ressourcenanbieters|
> |-----|-----|
> |Version 1808 (1.1808.0.97)|[SQL RP Version 1.1.33.0](https://aka.ms/azurestacksqlrp11330)|  
> |     |     |

> [!IMPORTANT]
> Wenden Sie das mindestens erforderliche Azure Stack Hub-Update auf Ihr integriertes Azure Stack Hub-System an, bevor Sie die neueste Version des SQL-Ressourcenanbieters bereitstellen.

## <a name="new-features-and-fixes"></a>Neue Features und Fehlerbehebungen
Diese Version des Azure Stack Hub-SQL-Ressourcenanbieters enthält die folgenden Verbesserungen und Fehlerbehebungen:

### <a name="fixes"></a>Fehlerbehebungen

- **SQL-Ressourcenanbieter-Portalerweiterung wählt eventuell das falsche Abonnement aus.** Der SQL-Ressourcenanbieter ermittelt anhand von Azure Resource Manager-Aufrufen, welches Dienstadministratorabonnement zuerst verwendet werden soll. Es kann sein, dass dies nicht das *Standardanbieterabonnement* ist. In diesem Fall funktioniert der SQL-Ressourcenanbieter nicht normal.

- **SQL-Hostserver listet keine gehosteten Datenbanken auf.** Vom Benutzer erstellte Datenbanken werden unter Umständen nicht aufgeführt, wenn für SQL-Hostserver Mandantenressourcen angezeigt werden.

- **Bei der Bereitstellung des vorherigen SQL-Ressourcenanbieters (1.1.30.0) tritt ggf. ein Fehler auf, wenn TLS 1.2 nicht aktiviert ist.** SQL-Ressourcenanbieter 1.1.33.0 wurde aktualisiert, um TLS 1.2 zu ermöglichen, wenn der Ressourcenanbieter bereitgestellt oder aktualisiert wird oder Geheimnisse rotiert werden.

- **Fehler bei Rotation von Geheimnissen für SQL-Ressourcenanbieter.** Problem behoben, das beim Rotieren von Geheimnissen zu folgendem Fehler geführt hat: `New-AzureRmResourceGroupDeployment - Error: Code=InvalidDeploymentParameterValue; Message=The value of deployment parameter 'StorageAccountBlobUri' is null.`

## <a name="known-issues"></a>Bekannte Probleme

- **Dauer von bis zu einer Stunde bis zur Anzeige von SQL SKUs im Portal**: Es kann bis zu einer Stunde dauern, bis die neu erstellten SKUs beim Erstellen neuer SQL-Datenbanken angezeigt werden.

    **Problemumgehung**: Keine.

- **Wiederverwendung von SQL-Anmeldungen**: Der Versuch einer neuen SQL-Anmeldung mit dem gleichen Benutzernamen wie eine vorhandene Anmeldung unter demselben Abonnement führt dazu, dass die gleiche Anmeldung und das vorhandene Kennwort wiederverwendet werden.

    **Problemumgehung**: Verwenden Sie unterschiedliche Benutzernamen, wenn Sie neue Anmeldungen unter demselben Abonnement oder Anmeldungen mit demselben Benutzernamen unter unterschiedlichen Abonnements erstellen.

- **Dateninkonsistenz aufgrund von gemeinsam genutzten SQL-Anmeldungen**: Wenn eine SQL-Anmeldung für mehrere SQL-Datenbanken unter demselben Abonnement gemeinsam genutzt wird, führt eine Änderung des Anmeldekennworts zu Dateninkonsistenz.

    **Problemumgehung**: Verwenden Sie für unterschiedliche Datenbanken unter demselben Abonnement immer unterschiedliche Anmeldungen.

- **SQL-Ressourcenanbieter kann SQL Server Always On-Listener nicht hinzufügen.** Bei Verwendung der Listener-IP-Adresse des SQL Server Always On-Listeners kann die SQL-Ressourcenanbieter-VM den Hostnamen des Listeners nicht auflösen.

    **Problemumgehung**: Stellen Sie sicher, dass DNS ordnungsgemäß funktioniert, um die Listener-IP-Adresse in den Hostnamen des Listeners aufzulösen.

### <a name="known-issues-for-cloud-admins-operating-azure-stack-hub"></a>Bekannte Probleme für Cloudadministratoren, die Azure Stack Hub betreiben
Lesen Sie die Dokumentation in den [Versionshinweisen zu Azure Stack Hub](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Nächste Schritte
[Informieren Sie sich über den SQL-Ressourcenanbieter](azure-stack-sql-resource-provider.md).

[Bereiten Sie sich auf die Bereitstellung des SQL-Ressourcenanbieters vor](azure-stack-sql-resource-provider-deploy.md#prerequisites).

[Führen Sie für den SQL-Ressourcenanbieter ein Upgrade von einer früheren Version durch](azure-stack-sql-resource-provider-update.md).