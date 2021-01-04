---
title: Übersicht über den EMC PowerEdge Tactical R640-Server
description: Hier erfahren Sie mehr über den EMC PowerEdge Tactical R640-Server.
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: d1f0e141c4ca4966aab9735064287121718dee87
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97392768"
---
# <a name="dell-emc-poweredge-tactical-r640-server-overview"></a>Übersicht über den Dell EMC PowerEdge Tactical R640-Server

Dieser Abschnitt enthält eine Übersicht über die Komponenten des Dell EMC PowerEdge Tactical R640-Servers.

## <a name="chassis-front-view"></a>Frontansicht des Gehäuses

Die folgende Abbildung zeigt den PowerEdge Tactical R640-Server (2U-Server).

Abbildung 3. Frontansicht des Gehäuses: PowerEdge Tactical R640-Hardwarelebenszyklushost und -Skalierungseinheit

![](media/image-60.png)

In der folgenden Tabelle werden die Eigenschaften des Frontbedienfelds des PowerEdge Tactical R640-Servers beschrieben.

Tabelle 1. Eigenschaften des Frontbedienfelds des PowerEdge Tactical R640-Servers

| Anschlüsse, Panels und Steckplätze  | BESCHREIBUNG                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Linkes Kontrollfeld        | Enthält die LED-Anzeigen für die Systemintegrität, die System-ID, die Status und iDRAC Quick Sync 2 (drahtlos). <br>**Hinweis:** Die iDRAC Quick Sync 2-LED-Anzeige ist nur in bestimmten Konfigurationen verfügbar. <br>Status-LEDs: Für die Ermittlung von fehlerhaften Hardwarekomponenten. Es gibt bis zu fünf Status-LEDs und eine LED-Leiste für die Gesamtintegrität des Systems (Gehäuseintegritäts- und -system ID). Weitere Informationen finden Sie unter „PowerEdge-Status-LED-Anzeigen“ auf Seite 61. <br>Quick Sync 2 (drahtlos): Zeigt ein Quick Sync-fähiges System an. Das Quick Sync-Feature ist optional. Dieses Feature ermöglicht die Systemverwaltung mithilfe mobiler Geräte. Dieses Feature aggregiert Hardware- oder Firmwareinventar und verschiedene Diagnose- und Fehlerinformationen auf Systemebene, die bei der Problembehandlung des Systems eingesetzt werden können.  |
| VGA-Anschluss                  | Für die Verbindung eines Anzeigegeräts mit dem System.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| Rechtes Bedienfeld       | Enthält den Netzschalter, den USB-Anschluss, den iDRAC Direct-Microanschluss und die iDRAC Direct-Status-LED                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |

## <a name="chassis-back-view"></a>Rückansicht des Gehäuses

Die folgende Abbildung zeigt die Komponenten der Gehäuserückseite des PowerEdge Tactical R640-Servers.

Abbildung 4. Rückansicht des Gehäuses: PowerEdge Tactical R640-Hardwarelebenszyklushost

![](media/image-61.png)

In der folgenden Tabelle werden die Eigenschaften des Bedienfelds auf der Rückseite des PowerEdge Tactical R640-Servers beschrieben.

Tabelle 2: Eigenschaften des Bedienfelds auf der Rückseite des PowerEdge Tactical R640-Servers

| Anschlüsse, Panels und Steckplätze       | Symbol                      | BESCHREIBUNG                                                                                                                                                                                                                                                                 |
|-------------------------------|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Festplatten                   | Nicht zutreffend                       | Betriebssystem und Datenspeicher für die Serverknoten „Hardwarelebenszyklushost“ und „Skalierungseinheit“                                                                                                                                                                                                                        |
| BOSS-Modul                   | Nicht zutreffend                       | Boot Optimized Storage Solution (BOSS), für die Installation des Betriebssystems oder Hypervisors                                                                                                                                                                                 |
| Stromversorgungseinheiten (2)        | Nicht zutreffend                       | Diese versorgen den Server mit Strom und werden aus Redundanzgründen doppelt bereitgestellt.                                                                                                                                                                                                 |
| USB 3.0-Anschlüsse                 |  ![](media/image-62.png)   | Die USB-Anschlüsse weisen 9 Pins auf und sind 3.0-kompatibel. Dank dieser Anschlüsse können Sie USB-Geräte mit dem System verbinden.                                                                                                                                                                     |
| VGA-Anschluss                      |   ![](media/image-63.png)  | Für die Verbindung eines Anzeigegeräts mit dem System.                                                                                                                                                                                                                      |
| Serieller Anschluss                   |   ![](media/image-64.png)  | Für die Verbindungsherstellung zwischen einem seriellen Gerät und dem System                                                                                                                                                                                                                       |
| iDRAC9 Enterprise-Anschluss        |   ![](media/image-65.png)  | Für den Remotezugriff auf iDRAC                                                                                                                                                                                                                                       |
| CMA-Stromversorgungsbuchse                | Nicht zutreffend                       | Über die Cable Management Arm-Stromversorgungsbuchse (CMA) können Sie das Kabel des Statusanzeigekabels mit CMA verbinden.                                                                                                                                                                     |
| Systemidentifizierungsknopf  |   ![](media/image-66.png) | Der Systemidentifizierungsknopf (ID) befindet sich sowohl auf der Vorder- als auch auf Rückseite des Servers. Wenn Sie auf den Knopf drücken, blinkt die LED-Anzeige für die Systemintegrität/System-ID. Über den System-ID-Knopf können Sie außerdem den iDRAC zurücksetzen und über den Durchlaufmodus (Step-Through Mode) auf das BIOS zugreifen.  |

## <a name="right-control-panel"></a>Rechtes Bedienfeld

In der folgenden Abbildung wird das rechte Bedienfeld des PowerEdge Tactical R640-Servers gezeigt.

Abbildung 5. Rechtes Bedienfeld des PowerEdge Tactical R640-Servers

![](media/image-67.png)

In der folgenden Tabelle werden die Bedienelemente und LED-Anzeigen im rechten Bedienfeld beschrieben.

Tabelle 3: Bedienelemente und LED-Anzeigen im rechten Bedienfeld des PowerEdge Tactical R640-Servers

| Element  | LED-Anzeige oder Knopf  | Symbol                      | BESCHREIBUNG                                                                                                                                                                                               |
|-------|----------------------|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1     | Netzschalter         |   ![](media/image-68.png)  | Gibt an, ob das System ein- oder ausgeschaltet ist. Drücken Sie den Netzschalter, um das System manuell ein- oder auszuschalten.  **Hinweis:** Drücken Sie den Netzschalter, um ein ACPI-kompatibles Betriebssystem ordnungsgemäß herunterzufahren.  |
| 2     | USB-Anschluss             | ![](media/image-70.png)    | Der USB-Anschluss weist 4 Pins auf und ist 2.0-kompatibel. Dank dieses Anschlusses können Sie USB-Geräte mit dem System verbinden.                                                                                                         |
| 3     | iDRAC Direct-LED     | Nicht zutreffend                       | Die iDRAC Direct-LED-Anzeige blinkt, wenn der iDRAC Direct-Anschluss aktiv mit einem Gerät verbunden ist.                                                                                        |
| 4     | iDRAC Direct-Anschluss    |   ![](media/image-65.png) | Der iDRAC Direct-Anschluss ist Micro USB 2.0-kompatibel. Dieser Anschluss ermöglicht den Zugriff auf die iDRAC Direct-Features.                                                                                              |

## <a name="inside-the-poweredge-tactical-r640"></a>Das Innere des PowerEdge Tactical R640-Servers

Die folgende Abbildung zeigt die inneren Komponenten des PowerEdge Tactical R640-Servers.

Abbildung 6. Das Innere des PowerEdge Tactical R640-Hardwarelebenszyklushosts

![](media/image-71.png)

Abbildung 7. Das Innere der PowerEdge Tactical R640-Skalierungseinheit

![](media/image-72.png)

## <a name="motherboard"></a>Hauptplatine

In der folgenden Abbildung ist das Layout der Hauptplatine dargestellt.

Abbildung 8. Layout der PowerEdge Tactical R640-Hauptplatine

![](media/image-73.png)

## <a name="memory"></a>Arbeitsspeicher

Die folgende Abbildung zeigt die Arbeitsspeicherinformationen des PowerEdge Tactical R640-Servers.

Abbildung 9. Arbeitsspeicherinformationen des PowerEdge Tactical R640-Servers

![](media/image-74.png)
