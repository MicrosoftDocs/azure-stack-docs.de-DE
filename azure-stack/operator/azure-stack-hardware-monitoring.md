---
title: Überwachen der Azure Stack Hub-Hardwareintegrität
description: Hier erfahren Sie, wie Sie die Integrität von Azure Stack Hub-Hardwarekomponenten überwachen.
author: sethmanheim
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: sethm
ms.reviewer: thoroet
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: 4fe942e6a4230d9807780507de8b658a6e0ed4c3
ms.sourcegitcommit: c653efe7cd5177bf61c1a321ba7f38c0cdf90346
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77575126"
---
# <a name="monitor-azure-stack-hub-hardware-components"></a>Überwachen von Azure Stack Hub-Hardwarekomponenten

Das Integritäts- und Überwachungssystem von Azure Stack Hub überwacht den Status des Speichersubsystems und löst bei Bedarf Warnungen aus. Das Integritäts- und Überwachungssystem kann auch Warnungen für folgende Hardwarekomponenten auslösen:

- Systemlüfter
- Systemtemperatur
- Stromversorgung
- CPUs
- Arbeitsspeicher
- Startlaufwerke

> [!NOTE]
> Informieren Sie sich vor dem Aktivieren dieses Features unbedingt bei Ihrem Hardwarepartner, ob die Komponenten bereit sind. Von Ihrem Hardwarepartner erhalten Sie auch ausführliche Informationen zu den Schritten, die zum Aktivieren dieses Features im BMC ausgeführt werden müssen.

## <a name="snmp-listener-scenario"></a>SNMP-Listenerszenario

Ein Listener vom Typ „SNMP v3“ wird in allen drei ERCS-Instanzen am TCP-Port 162 ausgeführt. Der Baseboard-Verwaltungscontroller (Baseboard Management Controller, BMC) muss so konfiguriert sein, dass er SNMP-Traps an den Azure Stack Hub-Listener sendet. Die drei PEP-IP-Adressen finden Sie im Administratorportal in der Ansicht mit den Regionseigenschaften.

Beim Senden von Traps an den Listener ist eine Authentifizierung erforderlich, und es müssen die gleichen Anmeldeinformationen wie beim Zugriff auf den Basis-BMC verwendet werden.

Wenn ein SNMP-Trap bei einer der drei ERCS-Instanzen am TCP-Port 162 eingeht, wird die OID intern abgeglichen und eine Warnung ausgelöst. Das Integritäts- und Überwachungssystem von Azure Stack Hub akzeptiert nur vom Hardwarepartner definierte OIDs. Ist Azure Stack Hub eine OID nicht bekannt, erfolgt kein Abgleich mit einer Warnung.

Nach dem Austausch einer fehlerhaften Komponente wird vom BMC ein Ereignis an den SNMP-Listener gesendet, um die Zustandsänderung anzugeben, und die Warnung wird in Azure Stack Hub automatisch geschlossen.

> [!NOTE]
> Wird der gesamte Knoten oder die gesamte Hauptplatine ausgetauscht, werden vorhandene Warnungen nicht automatisch geschlossen. Gleiches gilt, wenn die Konfiguration des BMC verloren geht (beispielsweise durch Zurücksetzen auf die Werkseinstellungen).

## <a name="next-steps"></a>Nächste Schritte

[Firewallintegration](azure-stack-firewall.md)
