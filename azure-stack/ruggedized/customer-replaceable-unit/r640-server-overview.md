---
title: -Server – Übersicht
description: Enthält Informationen zum Server für Azure Stack Hub Ruggedized
author: justinha
ms.topic: how-to
ms.date: 01/28/2021
ms.author: justinha
ms.reviewer: thoroet
ms.lastreviewed: ''
ms.openlocfilehash: a922c99121157c03705784dd99b8abf97fbec507
ms.sourcegitcommit: 283b1308142e668749345bf24b63d40172559509
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99577363"
---
# <a name="server-overview"></a>-Server – Übersicht

Dieser Abschnitt enthält eine Übersicht über die Serverkomponenten.

## <a name="chassis-front-view"></a>Frontansicht des Gehäuses

In der folgenden Abbildung ist der 2U-Server dargestellt.


![Diagramm: Vorderansicht des Servergehäuses](media/image-60.png)

In der folgenden Tabelle sind die Features auf der Vorderseite des Gehäuses beschrieben.

| Anschlüsse, Panels und Steckplätze  | BESCHREIBUNG                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Linkes Kontrollfeld        | Enthält die LED-Anzeigen für die Systemintegrität, die System-ID, die Status und iDRAC Quick Sync 2 (drahtlos). <br>**Hinweis:** Die iDRAC Quick Sync 2-LED-Anzeige ist nur in bestimmten Konfigurationen verfügbar. <br>Status-LEDs: Für die Ermittlung von fehlerhaften Hardwarekomponenten. Es gibt bis zu fünf Status-LEDs und eine LED-Leiste für die Gesamtintegrität des Systems (Gehäuseintegritäts- und -system ID). <br>Quick Sync 2 (drahtlos): Zeigt ein Quick Sync-fähiges System an. Das Quick Sync-Feature ist optional. Dieses Feature ermöglicht die Systemverwaltung mithilfe mobiler Geräte. Dieses Feature aggregiert Hardware- oder Firmwareinventar und verschiedene Diagnose- und Fehlerinformationen auf Systemebene, die bei der Problembehandlung des Systems eingesetzt werden können.  |
| VGA-Anschluss                  | Für die Verbindung eines Anzeigegeräts mit dem System.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| Rechtes Bedienfeld       | Enthält den Netzschalter, den USB-Anschluss, den iDRAC Direct-Microanschluss und die iDRAC Direct-Status-LED                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |

## <a name="chassis-back-view"></a>Rückansicht des Gehäuses

In der folgenden Abbildung sind die Komponenten auf der Rückseite des Gehäuses dargestellt.


![Diagramm: Rückansicht des Servergehäuses](media/image-61.png)

In der folgenden Tabelle sind die Features auf der Gehäuserückseite beschrieben.


| Anschlüsse, Panels und Steckplätze       | Symbol                      | BESCHREIBUNG                                                                                                                                                                                                                                                                 |
|-------------------------------|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Festplatten                   | Nicht zutreffend                       | Betriebssystem und Datenspeicher für die Serverknoten „Hardwarelebenszyklushost“ und „Skalierungseinheit“                                                                                                                                                                                                                        |
| BOSS-Modul                   | Nicht zutreffend                       | Boot Optimized Storage Solution (BOSS), für die Installation des Betriebssystems oder Hypervisors                                                                                                                                                                                 |
| Stromversorgungseinheiten (2)        | Nicht zutreffend                       | Diese versorgen den Server mit Strom und werden aus Redundanzgründen doppelt bereitgestellt.                                                                                                                                                                                                 |
| USB 3.0-Anschlüsse                 |  :::image type="icon" source="media/image-62.png"::: | Die USB-Anschlüsse weisen 9 Pins auf und sind 3.0-kompatibel. Dank dieser Anschlüsse können Sie USB-Geräte mit dem System verbinden.                                                                                                                                                                     |
| VGA-Anschluss                      |   :::image type="icon" source="media/image-63.png":::  | Für die Verbindung eines Anzeigegeräts mit dem System.                                                                                                                                                                                                                      |
| Serieller Anschluss                   |   :::image type="icon" source="media/image-64.png":::  | Für die Verbindungsherstellung zwischen einem seriellen Gerät und dem System                                                                                                                                                                                                                       |
| iDRAC9 Enterprise-Anschluss        |   :::image type="icon" source="media/image-65.png":::  | Für den Remotezugriff auf iDRAC                                                                                                                                                                                                                                       |
| CMA-Stromversorgungsbuchse                | Nicht zutreffend                       | Über die Cable Management Arm-Stromversorgungsbuchse (CMA) können Sie das Kabel des Statusanzeigekabels mit CMA verbinden.                                                                                                                                                                     |
| Systemidentifizierungsknopf  |   :::image type="icon" source="media/image-66.png"::: | Der Systemidentifizierungsknopf (ID) befindet sich sowohl auf der Vorder- als auch auf Rückseite des Servers. Wenn Sie auf den Knopf drücken, blinkt die LED-Anzeige für die Systemintegrität/System-ID. Über den System-ID-Knopf können Sie außerdem den iDRAC zurücksetzen und über den Durchlaufmodus (Step-Through Mode) auf das BIOS zugreifen.  |

## <a name="right-control-panel"></a>Rechtes Bedienfeld

In der folgenden Abbildung ist das rechte Bedienfeld dargestellt.

![Diagramm: Netzschalter und USB-Anschlüsse auf dem Bedienfeld](media/image-67.png)

In der folgenden Tabelle werden die Bedienelemente und LED-Anzeigen im rechten Bedienfeld beschrieben.


| Element  | LED-Anzeige oder Knopf  | Symbol                      | Beschreibung                                                                                                                                                                                               |
|-------|----------------------|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1     | Netzschalter         |   :::image type="icon" source="media/image-68.png":::  | Gibt an, ob das System ein- oder ausgeschaltet ist. Drücken Sie den Netzschalter, um das System manuell ein- oder auszuschalten.  **Hinweis:** Drücken Sie den Netzschalter, um ein ACPI-kompatibles Betriebssystem ordnungsgemäß herunterzufahren.  |
| 2     | USB-Anschluss             | :::image type="icon" source="media/image-70.png":::    | Der USB-Anschluss weist 4 Pins auf und ist 2.0-kompatibel. Dank dieses Anschlusses können Sie USB-Geräte mit dem System verbinden.                                                                                                         |
| 3     | iDRAC Direct-LED     | Nicht zutreffend                       | Die iDRAC Direct-LED-Anzeige blinkt, wenn der iDRAC Direct-Anschluss aktiv mit einem Gerät verbunden ist.                                                                                        |
| 4     | iDRAC Direct-Anschluss    |   :::image type="icon" source="media/image-65.png"::: | Der iDRAC Direct-Anschluss ist Micro USB 2.0-kompatibel. Dieser Anschluss ermöglicht den Zugriff auf die iDRAC Direct-Features.                                                                                              |

## <a name="inside-the-server"></a>Innenbereich des Servers

In den folgenden Abbildungen sind die Komponenten im Inneren des Servers dargestellt.

![Abbildung mit der Draufsicht der internen R640 HLH-Serverkomponenten](media/image-71.png)


![Abbildung mit der Draufsicht der internen R640 SU-Serverkomponenten](media/image-72.png)

## <a name="motherboard"></a>Hauptplatine

In der folgenden Abbildung ist das Layout der Hauptplatine dargestellt.


![Abbildung, in der die R640-Wartungsinformationen und das Layout der Hauptplatine dargestellt sind](media/image-73.png)

## <a name="memory"></a>Arbeitsspeicher

Die folgende Abbildung enthält Informationen zum Arbeitsspeicher.


![Abbildung, in der die R640-Arbeitsspeicherinformationen gezeigt werden](media/image-74.png)
