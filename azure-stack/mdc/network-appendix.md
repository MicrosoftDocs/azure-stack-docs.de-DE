---
title: 'MDC-Netzwerke (Modular Data Center): Anhang'
description: Anhänge zu MDC-Netzwerken
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: conceptual
ms.date: 12/30/2019
ms.lastreviewed: 12/30/2019
ms.openlocfilehash: b18f962ce2348a6a0f90a2ad48c1ea84e01fe921
ms.sourcegitcommit: 5fbc60b65d27c916ded7a95ba4102328d550c7e5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97598639"
---
# <a name="mdc-network-appendix"></a>Anhang zu MDC-Netzwerken

Der Anhang enthält Informationen zu Geräteparametern und -identitäten für MDC-Hardware.

## <a name="technical-device-parameters"></a>Parameter für technische Geräte

### <a name="cisco-93360yc-fx2--cisco-9348gc-fxp"></a>Cisco 93360YC-FX2 und Cisco 9348GC-FXP

| **Feature** | **Cisco Nexus 93360YC-FX2** | **Cisco Nexus 9348GC-FXP** |
|---|---|---|
| Ports | 96 Anschlüsse mit 1/10/25 GBit/s und 12 QSFP28-Anschlüsse mit 40/100 GBit/s | 48 × 1GBASE-T-Anschlüsse, 4 SFP28-Anschlüsse mit 1/10/25 GBit/s und 2 QSFP28-Anschlüsse mit 40/100 GBit/s |
| Unterstützte Geschwindigkeiten | 1/10/25 GBit/s bei Downlinks, 40/100 GBit/s bei Uplinks, Anschlüsse mit Unterstützung für Abzweigungen, 97–108: 4 × 10/25 G | Geschwindigkeiten von 100 MBit/s und 1 GBit/s |
| CPU | Vier Kerne | Vier Kerne |
| Systemspeicher | Bis zu 24 GB | 24 GB |
| SSD-Laufwerk | 128 GB | 128 GB |
| Systempuffer | 40MB | 40MB |
| Verwaltungsanschlüsse | Zwei Anschlüsse: 1 × RJ-45 und 1 × SFP+ | Zwei Anschlüsse: 1 × RJ-45 und 1 × SFP+ |
| USB-Anschlüsse | Eine | Eine |
| Serielle RS-232-Anschlüsse | Eine | Eine |
| Stromversorgung (bis zu zwei) | 1\.200 W Wechselstrom (AC), 1.200 W HVAC/HVDC | 350 W Wechselstrom (AC), 440 W Gleichstrom (DC) |
| Typische Stromversorgung (AC) | 404 W | 178 W |
| Maximale Stromversorgung (AC) | 900 W | 287 W |
| Eingangsspannung (AC) | 100 V bis 240 V | 100 V bis 240 V |
| Eingangsspannung (Hochspannung, Wechselstrom (HVAC)) | 100 V bis 277 V | 90 V bis 305 V |
| Eingangsspannung (DC) | -40 V bis -72 V | \-36 V bis -72 V |
| Eingangsspannung (Hochspannung, Gleichstrom (HVDC)) | -240 V bis -380 V | 192 V bis 400 V |
| Frequenz (AC) | 50 bis 60 Hz | 50 bis 60 Hz |
| Lüfter | Drei Lüfterschächte | drei |
| Luftströmung | Anschlussseitige Zu- und Abluft | Anschlussseitige Zu- und Abluft |
| Hardwareabmessungen | 3,38 × 17,41 × 24,14 Zoll (8,59 × 44,23 × 61,31 cm) | 1,72 × 17,3 × 19,7 Zoll |
| (H × B × T) | | (4,4 × 43,9 × 49,9 cm) |
| Akustik | 76,7 dBA bei 40 % Lüftergeschwindigkeit, 88,7 dBA bei 70 % Lüftergeschwindigkeit und 97,4 dBA bei 100 % Lüftergeschwindigkeit | 67,5 dBA bei 50 % Lüftergeschwindigkeit, 73,2 dBA bei 70 % Lüftergeschwindigkeit und 81,6 dBA bei 100 % Lüftergeschwindigkeit |
| RoHS-Konformität | Ja | Ja |
| MTBF | 320.040 Stunden | 257.860 Stunden |
| ACI-Mindestimage | ACI-N9KDK9-14.1.2 | ACI-N9KDK9-13.0 |
| NX-OS-Mindestimage | NXOS-9.3(1) | NXOS-703I7.1 |

### <a name="juniper-mx204"></a>Juniper MX204

:::row:::
    :::column:::
        **Layout**
    :::column-end:::
    :::column:::
        Systemkapazität

        Einschubfachausrichtung

        Montage
    :::column-end:::
    :::column span="2":::
        3 TBit/s

        Nicht verfügbar

        Vorderseite oder Mitte
    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
        **Physische Spezifikation**
    :::column-end:::
    :::column:::
        Abmessungen (B × H × T)

        Gewicht bei vollständiger Beladung

        Gewicht unbeladen
    :::column-end:::
    :::column span="2":::
        44,3 × 22,1 × 62,2 cm (17,45" × 8,71" × 24,5")

        59 kg (130 lb)

        23,6 kg (52 lbs)
    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
        **Routing-Engine**
    :::column-end:::
    :::column:::
        Standardspeicher

        Anzahl von Kernen
    :::column-end:::
    :::column span="2":::
        2 × 16 MB NOR-Flashspeicher; 64 GB DDR4-RAM; 2 × 50 GB SSD

        6 Kerne
    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
        **Redundanz**
    :::column-end:::
    :::column:::
        Komponenten
    :::column-end:::
    :::column span="2":::
        Stromversorgung, REs, Lüfter
    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
        **Umwelt**
    :::column-end:::
    :::column:::
        Luftzirkulation

        Betriebstemperatur

        Luftfeuchtigkeit im Betrieb

        Betriebshöhe
    :::column-end:::
    :::column span="2":::
        Seite zu Seite

        0 °C bis 46 °C (32 °F bis 115 °F) auf Normalnull

        5 % bis 90 %

        3\.048 m (10.000 ft)
    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
        **Zertifizierungen**
    :::column-end:::
    :::column:::
        NEBS
    :::column-end:::
    :::column span="2":::
        • GR-1089-Core EMC und elektrische Sicherheit

        • Common Bonding Network (CBN)

        • National Electrical Code (NEC)

        • Physischer Schutz nach GR-63-Core
    :::column-end:::
:::row-end:::

### <a name="dell-emc-s4148f-on"></a>Dell EMC S4148F-ON

Features:

- 48 Ethernet-SFP+-Anschlüsse mit 10 Gigabit
- 6 Ethernet-QSFP+-Anschlüsse mit 40 Gigabit
- 1 RJ45-Konsolen-/Verwaltungsanschluss mit RS232-Signalisierung 
- 1 RJ45-Mikro-USB-B-Konsolenanschluss 
- 1 RJ45-10/100/1000BASE-T-Ethernet-Verwaltungsanschluss 

Spezifikationen:

- Größe: 
  - 1 HE, 4,4 cm (H) × 43,1 cm (B) × 45,7 cm (T) (1,75" (H) × 17" (B) × 18"(T)) 
  - S4112: 4,125 cm (H) × 20,9 cm (B) × 45 cm (T) (1,7" (H) × 8,28" (B) × 18" (T)) 
- Stromversorgung: 100–240 V Wechselstrom, 50/60 Hz
- Stromversorgung Gleichstrom (DC) für S4412: Nennspannung von -40 V (DC) bis -72 V (DC)
- Maximale Stromaufnahme pro System: 6 A/5 A bei 100/120 V Wechselstrom; 3 A/2,5 A bei 200/240 V Wechselstrom
- S4112: 2 A/1,7 A bei 100/120 V Wechselstrom; 1 A/0,8 A bei 200/240 V Wechselstrom 
- S4112 (Gleichstrom, DC): -40 V/5 A, -48 V/4,2 A, -72 V/2,8 A 

Maximalwerte im Betrieb:
- Betriebstemperatur: 5 °C bis 40 °C (41 °F bis 104 °F)
- Luftfeuchtigkeit im Betrieb: 5–85 % (relative Luftfeuchtigkeit), nicht kondensierend 

Maximalwerte außerhalb des Betriebs:
- Lagertemperatur: -40 °C bis 65 °C (-40 °F bis 149 °F) 
- Luftfeuchtigkeit bei der Lagerung: 5–95 % (relative Luftfeuchtigkeit), nicht kondensierend


## <a name="identity"></a>Identity

Alle Netzwerkgeräte sind für die Arbeit mit TACACS (Terminal Access Controller Access-Control System) konzipiert. TACACS bietet eine zentralisierte Identitätsverwaltung. Der TACACS-Server muss im Rahmen der Erstkonfiguration bereitgestellt werden.

Beachten Sie, dass die Geräte auf die unten aufgeführten lokalen Identitäten zurückgreifen, wenn der TACACS-Server nicht verfügbar ist. Während des normalen Betriebs sind diese Konten deaktiviert.


Die folgenden Benutzerkonten sind pro Gerät verfügbar:

| Sicherungsmedium | Username | Standardkennwort |
|---------|--------------|----------------------|
| Edge 1 | root | Vom Kunden zugewiesen |
| Edge 2 | Root | Vom Kunden zugewiesen |
| ToR 1 | Root | Vom Kunden zugewiesen |
| ToR 2 | Root | Vom Kunden zugewiesen |
| BMC | Root | Vom Kunden zugewiesen |
| PDU 1 | root | Vom Kunden zugewiesen |
| PDU 2 | root | Vom Kunden zugewiesen |
| Seriell | Root | Vom Kunden zugewiesen |
| Avocent | „Root“ und „Admin“ | Vom Kunden zugewiesen |
