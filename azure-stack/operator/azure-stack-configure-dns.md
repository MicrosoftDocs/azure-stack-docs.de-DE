---
title: Aktualisieren der DNS-Weiterleitung in Azure Stack Hub | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die DNS-Weiterleitung in Azure Stack Hub aktualisieren.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: sethm
ms.reviewer: thoroet
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: b16eade221c51664205e865d1680e7f048fbfc7a
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75817583"
---
# <a name="update-the-dns-forwarder-in-azure-stack-hub"></a>Aktualisieren der DNS-Weiterleitung in Azure Stack Hub

Mindestens eine erreichbare DNS-Weiterleitung ist erforderlich, damit die Azure Stack Hub-Infrastruktur externe Namen auflösen kann. Für die Bereitstellung von Azure Stack Hub muss eine DNS-Weiterleitung verfügbar gemacht werden. Diese Eingabe wird als Weiterleitung für die internen Azure Stack Hub-DNS-Server verwendet und ermöglicht die externe Namensauflösung für Dienste wie Authentifizierung, Marketplace-Verwaltung oder Nutzung.

DNS ist ein kritischer Dienst der Rechenzentrumsinfrastruktur, der sich ändern kann. Wenn dies der Fall ist, muss Azure Stack Hub aktualisiert werden.

In diesem Artikel wird beschrieben, wie Sie den privilegierten Endpunkt (PEP) zum Aktualisieren der DNS-Weiterleitung in Azure Stack Hub verwenden. Es wird empfohlen, zwei zuverlässige IP-Adressen für die DNS-Weiterleitung zu verwenden.

1. Stellen Sie eine Verbindung mit dem [privilegierten Endpunkt](azure-stack-privileged-endpoint.md) her. Hinweis: Es ist nicht erforderlich, den privilegierten Endpunkt durch Erstellen eines Supporttickets zu entsperren.

2. Führen Sie den folgenden Befehl aus, um die aktuelle konfigurierte DNS-Weiterleitung zu überprüfen. Als Alternative können Sie auch die Regionseigenschaften des Verwaltungsportals verwenden:

   ```powershell
   Get-AzsDnsForwarder
   ```

3. Führen Sie den folgenden Befehl aus, um Azure Stack Hub zu aktualisieren und die neue DNS-Weiterleitung zu verwenden:

   ```powershell
    Set-AzsDnsForwarder -IPAddress "IPAddress 1","IPAddress 2"
   ```

4. Überprüfen Sie die Ausgabe des Befehls auf Fehler.

## <a name="next-steps"></a>Nächste Schritte

[Firewallintegration](azure-stack-firewall.md)
