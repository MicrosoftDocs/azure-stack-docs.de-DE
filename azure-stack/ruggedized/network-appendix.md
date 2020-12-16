---
title: Anhang – Azure Stack Hub Ruggedized-Netzwerke
description: Anhänge für Azure Stack Hub Ruggedized-Netzwerke
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: conceptual
ms.date: 10/14/2020
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: 2a5acdf50a310fe2bda89e4472c38af2c75f9487
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96941099"
---
# <a name="azure-stack-hub-ruggedized-network-appendix"></a>Anhang – Azure Stack Hub Ruggedized und Netzwerke

Der Anhang enthält Informationen zu Geräteparametern und -identitäten für Azure Stack Hub Ruggedized-Hardware.

## <a name="technical-device-parameters"></a>Parameter für technische Geräte

### <a name="dell-s5048f-on"></a>Dell S5048F-ON

Ports: 

- 48 SFP28-Anschlüsse mit 25-Gigabit-Ethernet 
- 6 QSFP28-Anschlüsse mit 100-Gigabit-Ethernet 
- 1 RJ45-Konsolen-/Verwaltungsanschluss mit RS232-Signalisierung
- 1 optionaler Micro-USB-Konsolenanschluss Typ B 
- 1 10/100/1000Base-T-Ethernet-Anschluss, der als Verwaltungsanschluss verwendet wird 
- 1 USB-Anschluss Typ A für den externen Massenspeicher 

Spezifikationen:

- Größe: 1 HE – 4,4 cm (H) x 43,4 cm (B) x 45,7 cm (T) (1,72"(H) x 17,1" (B) x 18"(T)) 
- Gewichtung: 9,98 kg (22 lbs) 
- A-bewerteter Schalldruckpegel (ISO 7779): 59,6 dBA bei 23 °C (73,4 °F)  
- Stromversorgung: 100–240 V AC, 50/60 Hz 
- Maximale Wärmeleistung: 1.956 BTU/h 
- Maximale Stromaufnahme pro System: 
  - 5,73 A/4,8 A bei 100/120 V AC 
  - 2,87 A/2,4 A bei 200/240 V AC 
- Maximaler Stromverbrauch: 573 Watt (AC) 
- Typischer Stromverbrauch: 288 Watt (AC) bei Nutzung sämtlicher Glasfaseranschlüsse  
- Maximalwerte im Betrieb: 
  - Betriebstemperatur: 0–45 °C (32–113 °F) 
  - Luftfeuchtigkeit im Betrieb: 10–90 % (relative Luftfeuchtigkeit), nicht kondensierend. 
  - Bis 45 °C mit Dell Fresh Air kompatibel. 
- Maximalwerte außerhalb des Betriebs: 
  - Lagertemperatur: –40 °C bis 70 °C (–40 °F bis 158 °F) 
  - Luftfeuchtigkeit bei der Lagerung: 5–95 % (relative Luftfeuchtigkeit), nicht kondensierend

### <a name="dell-3048-on"></a>Dell 3048-ON

Ports:

- 48 1000Base-T-Anschlüsse  
- 4 SFP+-Anschlüsse mit 10 GbE 
- 1 RJ45-Konsolen-/Verwaltungsanschluss mit RS232-Signalisierung  

Spezifikationen:

- Größe: 1 HE – 4,4 cm (H) x 43,4 cm (B) x 32,0 cm (T) (1,71"(H) x 17,09" (B) x 12,6"(T))  
- Gewichtung: 5,84 kg (12,8 lbs) mit einem Netzteil, 6,74 kg (14,8 lbs) mit zwei Netzteilen 
- A-bewerteter Schalldruckpegel (ISO 7779): \< 36 dBA bei 26 °C (78,8 °F) 
- Stromversorgung: 90–264 V Wechselstrom, 50/60 Hz. 
  1) Netzteil, Luftstromrichtung nach vorn.  
  2) Netzteil, umgekehrte Luftstromrichtung. 
- Maximale Wärmeleistung: 290 BTU/h 
- Maximale Stromaufnahme pro System:  
  - \<1 A bei 100/120 V AC 
  - \<0,5 A bei 200/240 V AC  
- Maximaler Stromverbrauch: 87 W 
- Typischer Stromverbrauch: 65 W 
- Maximalwerte im Betrieb:  
  - Betriebstemperatur: 0–45 °C (32–113 °F)  
  - Luftfeuchtigkeit im Betrieb: 5–85 % (relative Luftfeuchtigkeit), nicht kondensierend    
  - Betriebshöhe: 0 bis 3.000 m über dem Meeresspiegel  
- Maximalwerte außerhalb des Betriebs: 
  - Lagertemperatur: –40 °C bis 70 °C (–40 °F bis 158 °F) 
  - Luftfeuchtigkeit bei der Lagerung: 5–95 % (relative Luftfeuchtigkeit), nicht kondensierend   

## <a name="identity"></a>Identity

Alle Netzwerkgeräte sind für die Arbeit mit TACACS (Terminal Access Controller Access-Control System) konzipiert. TACACS bietet eine zentralisierte Identitätsverwaltung. Der TACACS-Server muss im Rahmen der Erstkonfiguration bereitgestellt werden.

Beachten Sie, dass die Geräte auf die unten aufgeführten lokalen Identitäten zurückgreifen, wenn der TACACS-Server nicht verfügbar ist. Während des normalen Betriebs sind diese Konten deaktiviert.

Die folgenden Benutzerkonten sind pro Gerät verfügbar:

| Sicherungsmedium | Username | Standardkennwort     |
|--------|----------|----------------------|
| ToR 1  | Root     | Vom Kunden zugewiesen |
| ToR 2  | Root     | Vom Kunden zugewiesen |
| BMC    | Root     | Vom Kunden zugewiesen |
