---
title: Aktualisieren der DNS-Weiterleitung in Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die DNS-Weiterleitung in Azure Stack aktualisieren.
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
ms.openlocfilehash: 22e49f28dee6b4aa97b9e84cf52950dd678450e4
ms.sourcegitcommit: cefba8d6a93efaedff303d3c605b02bd28996c5d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74308243"
---
# <a name="update-the-dns-forwarder-in-azure-stack"></a>Aktualisieren der DNS-Weiterleitung in Azure Stack

Mindestens eine erreichbare DNS-Weiterleitung ist erforderlich, damit die Azure Stack-Infrastruktur externe Namen auflösen kann. Für die Bereitstellung von Azure Stack muss eine DNS-Weiterleitung verfügbar gemacht werden. Diese Eingabe wird als Weiterleitung für die internen Azure Stack-DNS-Server verwendet und ermöglicht die externe Namensauflösung für Dienste wie Authentifizierung, Marketplace-Verwaltung oder Nutzung.

DNS ist ein kritischer Dienst der Rechenzentrumsinfrastruktur, der sich ändern kann. Wenn dies der Fall ist, muss Azure Stack aktualisiert werden.

In diesem Artikel wird beschrieben, wie Sie den privilegierten Endpunkt (PEP) zum Aktualisieren der DNS-Weiterleitung in Azure Stack verwenden. Es wird empfohlen, zwei zuverlässige IP-Adressen für die DNS-Weiterleitung zu verwenden.

1. Stellen Sie eine Verbindung mit dem [privilegierten Endpunkt](azure-stack-privileged-endpoint.md) her. Hinweis: Es ist nicht erforderlich, den privilegierten Endpunkt durch Erstellen eines Supporttickets zu entsperren.

2. Führen Sie den folgenden Befehl aus, um die aktuelle konfigurierte DNS-Weiterleitung zu überprüfen. Als Alternative können Sie auch die Regionseigenschaften des Verwaltungsportals verwenden:

   ```powershell
   Get-AzsDnsForwarder
   ```

3. Führen Sie den folgenden Befehl aus, um Azure Stack zu aktualisieren und die neue DNS-Weiterleitung zu verwenden:

   ```powershell
    Set-AzsDnsForwarder -IPAddress "IPAddress 1","IPAddress 2"
   ```

4. Überprüfen Sie die Ausgabe des Befehls auf Fehler.

## <a name="next-steps"></a>Nächste Schritte

[Firewallintegration](azure-stack-firewall.md)
