---
title: Überwachen der Integrität der Azure Stack-Hardware | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie die Integrität von Azure Stack-Hardwarekomponenten überwachen.
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
ms.openlocfilehash: b41a60aaf45d4b4fbbbf00945a4048dbf9dfb13a
ms.sourcegitcommit: cefba8d6a93efaedff303d3c605b02bd28996c5d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74308255"
---
# <a name="monitor-azure-stack-hardware-components"></a>Überwachen von Azure Stack-Hardwarekomponenten

Das Integritäts- und Überwachungssystem von Azure Stack überwacht bereits den Status des Speichersubsystems und löst bei Bedarf Warnungen aus. Mit dem Release 1910 von Azure Stack kann das Integritäts- und Überwachungssystem nun auch Warnungen für folgende Hardwarekomponenten auslösen:

- Systemlüfter
- Systemtemperatur
- Stromversorgung
- CPUs
- Arbeitsspeicher
- Startlaufwerke

> [!NOTE]
> Informieren Sie sich vor dem Aktivieren dieses Features unbedingt bei Ihrem Hardwarepartner, ob die Komponenten bereit sind. Von Ihrem Hardwarepartner erhalten Sie auch ausführliche Informationen zu den Schritten, die zum Aktivieren dieses Features im BMC ausgeführt werden müssen.

## <a name="snmp-listener-scenario"></a>SNMP-Listenerszenario

Ein Listener vom Typ „SNMP v3“ wird in allen drei ERCS-Instanzen am TCP-Port 162 ausgeführt. Der Baseboard-Verwaltungscontroller (Baseboard Management Controller, BMC) muss so konfiguriert sein, dass er SNMP-Traps an den Azure Stack-Listener sendet. Die drei PEP-IP-Adressen finden Sie im Administratorportal in der Ansicht mit den Regionseigenschaften.

Beim Senden von Traps an den Listener ist eine Authentifizierung erforderlich, und es müssen die gleichen Anmeldeinformationen wie beim Zugriff auf den Basis-BMC verwendet werden.

Wenn ein SNMP-Trap bei einer der drei ERCS-Instanzen am TCP-Port 162 eingeht, wird die OID intern abgeglichen und eine Warnung ausgelöst. Das Integritäts- und Überwachungssystem von Azure Stack akzeptiert nur vom Hardwarepartner definierte OIDs. Ist Azure Stack eine OID nicht bekannt, erfolgt kein Abgleich mit einer Warnung.

Nach dem Austausch einer fehlerhaften Komponente wird vom BMC ein Ereignis an den SNMP-Listener gesendet, um die Zustandsänderung anzugeben, und die Warnung wird in Azure Stack automatisch geschlossen.

> [!NOTE]
> Wird der gesamte Knoten oder die gesamte Hauptplatine ausgetauscht, werden vorhandene Warnungen nicht automatisch geschlossen. Gleiches gilt, wenn die Konfiguration des BMC verloren geht (beispielsweise durch Zurücksetzen auf die Werkseinstellungen).

## <a name="next-steps"></a>Nächste Schritte

[Firewallintegration](azure-stack-firewall.md)
