---
title: Indikatoren und Codes
description: Enthält Informationen zu Anzeigen und Codes.
author: justinha
ms.topic: how-to
ms.date: 01/28/2021
ms.author: justinha
ms.reviewer: thoroet
ms.lastreviewed: ''
ms.openlocfilehash: af4b9e3cbbff2c9d2c06f3c3966a2f67b1b7c037
ms.sourcegitcommit: 5ea0e915f24c8bcddbcaf8268e3c963aa8877c9d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100488070"
---
# <a name="indicators-and-codes"></a>Indikatoren und Codes

Die in diesem Abschnitt beschriebenen Features sind für die Hardwarelebenszyklus-Host- und Skalierungseinheitknoten-Server identisch.

## <a name="system-information-label"></a>Etikett mit Systeminformationen

Die folgende Abbildung zeigt das Etikett mit den Systeminformationen, das sich auf der Vorderseite befindet.

![Das Etikett mit Systeminformationen für das LED-Verhalten](media/image-75.png)

## <a name="idrac-direct-led-indicator-codes"></a>iDRAC Direct-LED-Anzeigecodes

Die iDRAC Direct-LED-Anzeige blinkt, wenn der Anschluss verbunden ist und im Rahmen eines iDRAC-Subsystems verwendet wird.

Sie können den iDRAC Direct mithilfe eines USB-zu-Micro-USB-Kabels (Typ AB) konfigurieren, das Sie mit Ihrem Laptop oder Tablet verbinden können. In der folgenden Tabelle werden die iDRAC Direct-Aktivität bei aktiviertem iDRAC Direct-Anschluss und die iDRAC Direct-LED-Anzeigecodes erläutert.

Tabelle 4: LED-Anzeigecodes

| **Anzeigecodes**                                         | **Condition**                                                |
|-------------------------------------------------------------|--------------------------------------------------------------|
| Leuchtet durchgängig grün für zwei Sekunden                                 | Der Laptop oder das Tablet ist verbunden.            |
| Blinkt grün (zwei Sekunden ein, zwei Sekunden aus) | Der verbundene Gerät (Laptop oder Tablet) wird erkannt. |
| Leuchtet nicht                                             | Der Laptop oder das Tablet ist ausgesteckt.            |

## <a name="left-control-panel"></a>Linkes Kontrollfeld

In der folgenden Abbildung werden die Status- und Integritäts-LED-Anzeigen des linken Bedienfelds angezeigt.



Abbildung 11. Linkes Kontrollfeld

![Abbildung mit den Status- und Integritäts-LED-Anzeigen des linken Bedienfelds](media/image-76.png)

In der folgenden Tabelle werden die Features im linken Bedienfeld beschrieben.


|    <br>Element       |    <br>LED-Anzeige oder Knopf                       |    <br>Symbol                                |    <br>BESCHREIBUNG                                                                                                  |
|-------------------|--------------------------------------------------|--------------------------------------------|---------------------------------------------------------------------------------------------------------------------|
|    <br>1          |    <br>Status-LED-Anzeigen                     |    <br>Nicht zutreffend                                 |    <br>Zeigt den Status des Systems an.     |
|    <br>2 und 3    |    <br>Systemintegritäts- und System-ID-LED-Anzeigen    | ![Symbol für die Systemintegrität](media/image-77.png) |    <br>Zeigt die Systemintegrität an.                                                                                 |

In der folgenden Tabelle sind die Status-LED-Anzeigen aufgelistet.

> [!NOTE]
> Die Status-LED-Anzeigen sind immer ausgeschaltet. Dies ändert sich nur bei Auftreten eines Fehlers, wenn die LED durchgängig gelborange leuchtet.



| <br>Symbol | <br>Beschreibung           | <br>Bedingung                                                                                                                                                                       | <br>Korrekturmaßnahme                                                                                                                                                                                                                                                                                                    |
|----------|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|          | <br>Laufwerkanzeige       | <br>Wenn ein Laufwerkfehler vorliegt, leuchtet die LED-Anzeige durchgängig gelborange.                                                                                                                      | 1. Überprüfen Sie im Systemereignisprotokoll, ob ein Laufwerkfehler vorliegt.<br>2. Führen Sie den entsprechenden Onlinediagnosetest durch.<br>3. Starten Sie das System neu, und führen Sie eine integrierte Diagnose (Embedded Diagnostics, ePSA) aus.<br>4. Wenn die Laufwerke in einem RAID-Array konfiguriert sind, starten Sie das System neu, und geben Sie das Hilfsprogramm für die Hostadapterkonfiguration ein. |
|          | <br>Temperatur-LED-Anzeige | <br>Die LED-Anzeige wechselt zu einem durchgängigen gelborange, wenn im System ein thermischer Fehler auftritt (z. B. liegt die Umgebungstemperatur außerhalb des zulässigen Bereichs, oder ein Lüfterfehler ist aufgetreten).                 | Stellen Sie sicher, dass keine der folgenden Bedingungen zutrifft:<br>* Ein Lüfter wurde entfernt oder hat einen Fehler verursacht. <br>* Die Abdeckung des Systems, die Luftschutzhaube, die Blindblende für das Speichermodul oder die Rückzwischenhalterung wurde entfernt.<br>* Die Umgebungstemperatur ist zu hoch.<br>* Der externe Luftstrom ist blockiert.                                                 |
|          | <br>Strom-LED-Anzeige  | <br>Die LED-Anzeige wechselt zu einem durchgängigen gelborange, wenn im System ein elektrischer Fehler (z. B. Stromspannung außerhalb des zulässigen Bereichs), eine fehlerhafte Stromversorgungseinheit (Power Supply Unit, PSU) oder ein fehlerhafter Spannungsregler vorliegt. | 1. Überprüfen Sie das Systemereignisprotokoll oder die Systemmeldungen auf das jeweilige Problem.<br>2. Wenn das Protokoll auf ein Problem mit der PSU hinweist, überprüfen Sie die LED der PSU.<br>3. Setzen Sie die PSU wieder ein.                                                                                                                                              |
|          | <br>Speicher-LED-Anzeige      | <br>Wenn ein Speicherfehler vorliegt, leuchtet die LED-Anzeige durchgängig gelborange.                                                                                                                       | 1. Überprüfen Sie das Systemereignisprotokoll oder die Systemmeldungen auf die Position des fehlerhaften Speichers.<br>2. Setzen Sie das Speichermodul wieder ein.                                                                                                                                                                                                  |
|          | <br>PCIe-LED-Anzeige      | <br>Wenn ein Fehler mit einer PCIe-Karte auftritt, leuchtet die LED-Anzeige durchgängig gelborange.                                                                                                          | 1. Starten Sie das System neu.<br>2. Aktualisieren Sie alle erforderlichen Treiber für die PCIe-Karte.<br>3. Installieren Sie die Karte neu.                                                                                                                                                                                                                    |

## <a name="power-supply-unit-indicator-codes"></a>Anzeigecodes für Stromversorgungeinheiten

AC-PSUs verfügen über einen leuchtenden Griff, das als LED-Anzeige dient. DC-PSUs verfügen über eine LED, die als Anzeige fungiert. Die Anzeige gibt an, ob Strom ließt oder ob ein Fehler aufgetreten ist.

In der folgenden Abbildung wird die AC-PSU gezeigt.

Abbildung 12: AC-PSU

![Abbildung, in der die AC-PSU angezeigt wird und die LED gekennzeichnet ist](media/image-83.png)

In der folgenden Tabelle sind die LED-Anzeigecodes definiert.

Tabelle 7. Anzeigecodes von AC-PSUs

| Stromanzeigecodes         | Bedingung                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|-------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Grün                         | Eine zulässige Stromquelle ist mit der PSU verbunden, und die PSU ist funktionstüchtig.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Blinkt gelb                | Es besteht ein Problem mit der PSU.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| Leuchtet nicht               | Die PSU ist nicht an eine Stromquelle angeschlossen.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| Blinkt grün                | Wenn die Firmware der PSU aktualisiert wird, blinkt der Griff der PSU grün. <br>Beim Aktualisieren der Firmware dürfen weder das Stromkabel, noch die PSU ausgesteckt werden. Wird das Firmwareupdate unterbrochen, funktionieren die PSUs nicht mehr.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| Blinkt grün und schaltet sich aus  | Wird eine PSU per Hot Plugging ausgetauscht, blinkt der Griff der PSU fünfmal grün mit einer Rate von 4 Hz und schaltet sich anschließend aus. Dies weist auf einen PSU-Konflikt in Bezug auf die Effizienz, die Features, den Integritätsstatus oder die unterstützte Spannung hin. <br><br>Wenn zwei PSUs installiert sind, müssen beide PSUs dieselbe Bezeichnung aufweisen, z. B. die Bezeichnung „Extended Power Performance (EPP)“. Eine Vermischung mit PSUs früherer Generationen von Servern wird nicht unterstützt. Dies gilt auch, wenn die PSUs über die gleiche Energiebewertung verfügen. Dies führt zu einer nicht übereinstimmenden PSU-Bedingung oder einem Fehler beim Einschalten des Systems. <br>Wenn Sie einen PSU-Konflikt beheben, muss nur die PSU mit der blinkenden Anzeige ausgetauscht werden. Das Austauschen einer PSU zum Herstellen einer Übereinstimmung zwischen beiden Einheiten kann zu einer Fehlerbedingung sowie <br>zu einem unerwarteten Herunterfahren des Systems führen. Wenn Sie von einer Konfiguration mit hoher Ausgabe zu einer Konfiguration mit geringer Ausgabe oder umgekehrt wechseln möchten, müssen Sie das System ausschalten. <br>AC-PSUs unterstützen Eingabespannungen von 240 V und 120 V. Die Ausnahme stellen Titan-PSUs dar, die nur 240 V unterstützen. Wenn zwei identische PSUs unterschiedliche Eingabespannungen erhalten, können sie unterschiedliche Wattzahlen ausgeben und einen Konflikt auslösen. <br>Wenn zwei PSUs verwendet werden, müssen diese denselben Typ und die gleiche maximale Ausgabeleistung aufweisen. <br>Das Kombinieren von AC- und DC-PSUs wird nicht unterstützt und löst einen Konflikt aus. |
