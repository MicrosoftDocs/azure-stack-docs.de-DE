---
title: Aktualisieren der DNS-Weiterleitung in Azure Stack Hub
description: Erfahren Sie, wie Sie die DNS-Weiterleitung in Azure Stack Hub aktualisieren.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: inhenkel
ms.reviewer: thoroet
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: ede13e98893fcf30c0e1640d65271554410f659a
ms.sourcegitcommit: 70c344b3c9c63f8c12867b2cdfdd1794fcc518dc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836086"
---
# <a name="update-the-dns-forwarder-in-azure-stack-hub"></a>Aktualisieren der DNS-Weiterleitung in Azure Stack Hub

Mindestens eine erreichbare DNS-Weiterleitung ist erforderlich, damit die Azure Stack Hub-Infrastruktur externe Namen auflösen kann. Für die Bereitstellung von Azure Stack Hub muss eine DNS-Weiterleitung verfügbar gemacht werden. Diese Eingabe wird als Weiterleitung für die internen Azure Stack Hub-DNS-Server verwendet und ermöglicht die externe Namensauflösung für Dienste wie Authentifizierung, Marketplace-Verwaltung oder Nutzung.

DNS ist ein wichtiger Rechenzentrumsinfrastruktur-Dienst, der sich ändern kann. Wenn dies der Fall ist, muss Azure Stack Hub aktualisiert werden.

In diesem Artikel wird beschrieben, wie Sie den privilegierten Endpunkt (PEP) zum Aktualisieren der DNS-Weiterleitung in Azure Stack Hub verwenden. Es wird empfohlen, zwei zuverlässige IP-Adressen für die DNS-Weiterleitung zu verwenden.

## <a name="steps-to-update-the-dns-forwarder"></a>Schritte zum Aktualisieren der DNS-Weiterleitung

1. Stellen Sie eine Verbindung mit dem [privilegierten Endpunkt](azure-stack-privileged-endpoint.md) her. Es ist nicht erforderlich, den privilegierten Endpunkt zu entsperren, indem Sie ein Supportticket erstellen.

2. Führen Sie den folgenden Befehl aus, um die aktuelle konfigurierte DNS-Weiterleitung zu überprüfen. Als Alternative können Sie auch die Regionseigenschaften des Administratorportals verwenden:

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
