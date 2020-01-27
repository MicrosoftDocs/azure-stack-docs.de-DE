---
title: 'Azure Stack Hub-MySQL-Ressourcenanbieter 1.1.30.0: Versionshinweise | Microsoft-Dokumentation'
description: Lesen Sie die Versionshinweise, um zu erfahren, welche Neuerungen im Update zum MySQL-Ressourcenanbieter 1.1.30.0 von Azure Stack Hub enthalten sind.
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
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: jiahan
ms.lastreviewed: 12/10/2018
ms.openlocfilehash: 59a998fbbd209402f03a532f3ce421ed3df53408
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75882435"
---
# <a name="mysql-resource-provider-11300-release-notes"></a>Versionsanmerkungen zum MySQL-Ressourcenanbieter 1.1.30.0

In diesen Versionshinweisen werden die Verbesserungen und bekannten Probleme in Version 1.1.30.0 des MySQL-Ressourcenanbieters beschrieben.

## <a name="build-reference"></a>Buildreferenz
Laden Sie die Binärdatei des MySQL-Ressourcenanbieters herunter, und führen Sie dann den Self-Extractor aus, um den Inhalt in ein temporäres Verzeichnis zu extrahieren. Der Ressourcenanbieter verfügt über einen entsprechenden Mindestbuild für Azure Stack Hub. Hier ist angegeben, welche Azure Stack Hub-Releaseversion für die Installation dieser Version des MySQL-Ressourcenanbieters erforderlich ist:

> |Azure Stack Hub-Mindestversion|Version des MySQL-Ressourcenanbieters|
> |-----|-----|
> |Azure Stack Hub-Update 1808 (1.1808.0.97)|[1.1.30.0](https://aka.ms/azurestackmysqlrp11300)|
> |     |     |

> [!IMPORTANT]
> Wenden Sie das mindestens erforderliche Azure Stack Hub-Update auf Ihr integriertes Azure Stack Hub-System an, oder stellen Sie das aktuelle Azure Stack Development Kit (ASDK) bereit, bevor Sie die neueste Version des MySQL-Ressourcenanbieters bereitstellen.

## <a name="new-features-and-fixes"></a>Neue Features und Fehlerbehebungen
Diese Version des Azure Stack Hub-MySQL-Ressourcenanbieters umfasst die folgenden Verbesserungen und Fehlerbehebungen:

- **Aktivierte Telemetriedaten für Bereitstellungen des MySQL-Ressourcenanbieters**. Die Erfassung von Telemetriedaten wurde für Bereitstellungen des MySQL-Ressourcenanbieters aktiviert. Zu den erfassten Telemetriedaten gehören beispielsweise die Ressourcenanbieterbereitstellung, Start- und Stoppzeiten, Beendigungsstatus, Beendigungsmeldungen und Fehlerdetails (falls zutreffend).

- **Update der TLS 1.2-Verschlüsselung**. Die ausschließliche Unterstützung von TLS 1.2 für die Ressourcenanbieterkommunikation mit internen Azure Stack Hub-Komponenten wurde aktiviert. 

### <a name="fixes"></a>Fehlerbehebungen

- **Azure Stack Hub-PowerShell-Kompatibilität für MySQL-Ressourcenanbieter**. Der MySQL-Ressourcenanbieter wurde aktualisiert, damit er mit dem PowerShell-Profil „Azure Stack Hub 2018-03-01-hybrid“ funktioniert und Kompatibilität mit AzureRM 1.3.0 und höher gewährleistet wird.

- **Blatt zur Änderung des MySQL-Anmeldekennworts**. Es wurde ein Problem behoben, bei dem das Kennwort auf dem entsprechenden Blatt nicht geändert werden kann. Links wurden aus Benachrichtigungen zur Kennwortänderung entfernt.

## <a name="known-issues"></a>Bekannte Probleme

- **Dauer von bis zu einer Stunde bis zur Anzeige von MySQL SKUs im Portal**. Es kann bis zu einer Stunde dauern, bis die neu erstellten SKUs beim Erstellen neuer MySQL-Datenbanken angezeigt werden.

    **Problemumgehung**: Keine.

- **Wiederverwendung von MySQL-Anmeldungen**. Der Versuch einer neuen MySQL-Anmeldung mit dem gleichen Benutzernamen wie eine vorhandene Anmeldung unter demselben Abonnement führt dazu, dass die gleiche Anmeldung und das vorhandene Kennwort wiederverwendet werden.

    **Problemumgehung**: Verwenden Sie unterschiedliche Benutzernamen, wenn Sie neue Anmeldungen unter demselben Abonnement oder Anmeldungen mit demselben Benutzernamen unter unterschiedlichen Abonnements erstellen.

- **TLS 1.2-Supportanforderung**. Wenn Sie versuchen, den MySQL-Ressourcenanbieter über einen Computer, bei dem TLS 1.2 nicht aktiviert ist, bereitzustellen oder zu aktualisieren, schlägt der Vorgang möglicherweise fehl. Führen Sie den folgenden PowerShell-Befehl auf dem Computer aus, der zum Bereitstellen oder Aktualisieren des Ressourcenanbieters verwendet wird, um sicherzustellen, dass TLS 1.2 als unterstützt zurückgegeben wird:

  ```powershell
  [System.Net.ServicePointManager]::SecurityProtocol
  ```

  Wenn **Tls12** in der Ausgabe des Befehls nicht enthalten ist, ist TLS 1.2 auf dem Computer nicht aktiviert.

    **Problemumgehung**: Führen Sie den folgenden PowerShell-Befehl aus, um TLS 1.2 zu aktivieren. Starten Sie dann die Bereitstellung des Ressourcenanbieters, oder aktualisieren Sie das Skript aus derselben PowerShell-Sitzung:

    ```powershell
    [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
    ```
 
### <a name="known-issues-for-cloud-admins-operating-azure-stack-hub"></a>Bekannte Probleme von Cloudadministratoren, die Azure Stack Hub betreiben
Lesen Sie die Dokumentation in den [Versionshinweisen zu Azure Stack Hub](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Nächste Schritte
[Informieren Sie sich über den MySQL-Ressourcenanbieter](azure-stack-mysql-resource-provider.md).

[Bereiten Sie sich auf die Bereitstellung des MySQL-Ressourcenanbieters vor](azure-stack-mysql-resource-provider-deploy.md#prerequisites).

[Führen Sie für den MySQL-Ressourcenanbieter ein Upgrade von einer früheren Version durch](azure-stack-mysql-resource-provider-update.md). 