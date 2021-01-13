---
title: Azure Stack Hub Ruggedized OEM-Versionshinweise
description: Azure Stack Hub Ruggedized OEM-Versionshinweise
author: sethmanheim
ms.topic: article
ms.date: 12/08/2020
ms.author: sethm
ms.reviewer: danlewi
ms.lastreviewed: 12/08/2020
ms.openlocfilehash: a7765eb44017b8d6521930de24794b630ae22344
ms.sourcegitcommit: c5d46662492887b70a599a60f3c3d27e3460a742
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/07/2021
ms.locfileid: "97965459"
---
# <a name="azure-stack-hub-ruggedized-2008-oem-release-notes"></a>Azure Stack Hub Ruggedized 2008 OEM-Versionshinweise

Dieser Artikel enthält Release- und Versionsinformationen für Azure Stack Hub Ruggedized.

## <a name="overview"></a>Übersicht

In diesem Dokument werden die Inhalte der Azure Stack Hub Ruggedized-Erstanbieterupdates für Firmware und Treiber beschrieben. Dieses Update enthält Verbesserungen und Fehlerbehebungen für das neueste Release von Azure Stack Hub. Unten sind die Downloadlinks aufgeführt:

* https://aka.ms/azshwpackage
* https://aka.ms/azshwmanifest

## <a name="baseline-and-document-history"></a>Baseline und Dokumentversionsverlauf

| Release | Date       | Beschreibung der Änderungen         |
|---------|------------|--------------------------------|
| 2008    | 13.10.2020 | 2.2.2010.5-OEM-Paketupdates |

## <a name="firmware"></a>Firmware

### <a name="bios"></a>Bios

| Releaseversion | Firmware version | Änderungen |
|-----------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---|---|
| 2008            | 2.8.2            | Es wurde das in den BIOS-Versionen 2.6.4 bis 2.8.1 aufgetretene Problem behoben, dass beim Einschalten ein Systemreset erfolgt ist, während auf dem Startbildschirm „Arbeitsspeicher wird konfiguriert“ angezeigt wurde. Das Problem betrifft DDR4- und NVDIMM-N-Arbeitsspeicherkonfigurationen.<br><br>Erweiterung zur Behebung der Sicherheitsrisiken (Common Vulnerabilities and Exposures (CVE, Allgemeine Sicherheitslücken und Schwachstellen)), z. B. CVE-2020-0545, CVE-2020-0548 und CVE-2020-0549. |   |   |
| 2005            | 2.7.7            |                                                                                                                                                                                                                                                                                                                                                                                                               |   |   |
|                 |                  |                                                                                                                                                                                                                                                                                                                                                                                                               |   |   |

### <a name="idrac"></a>iDRAC

| Releaseversion | Firmware version | Änderungen |
|-----------------|------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---|---|
| 2008            | 4.22.0.0         | 167411: Es wurde ein Problem behoben, das während der Aktualisierung der Firmware über die Redfish-API zum Erzwingen des Systemneustarts geführt hat.<br><br>155376: Es wurde ein Problem behoben, das beim Erfassen von SupportAssist-Protokollen zum Neustart von iDRAC geführt hat.<br><br>162778: Das Problem wurde behoben, dass aufgrund der virtuellen Konsole nicht genügend Arbeitsspeicher für iDRAC zur Verfügung stand. |   |   |
| 2005            | 4.10.10.10       |                                                                                                                                                                                                                                                                                  |   |   |
|                 |                  |                                                                                                                                                                                                                                                                                  |   |   |

### <a name="nic-azure-stack-nodes"></a>NIC-Azure Stack-Knoten

| Releaseversion    | Firmware version    | Änderungen                                                                                                          |
|--------------------|---------------------|------------------------------------------------------------------------------------------------------------------|
|     2008           |     14.27.60.08     | Es wurde ein fatal_assert der Firmware korrigiert, wobei aufgrund des Wartens von init_hca auf die Freigabe der Flowsperre durch den Timer IRISC nicht mehr reagiert hat. |
|     2005           |     14.26.60.00     |                                                                                                                  |

### <a name="nic-hlh"></a>NIC-HLH

| Releaseversion    | Firmware version    | Änderungen                                                                                                                                                                   |
|--------------------|---------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     2008           |     19.5.12         | Es wurde ein Problem mit Intel (R) Ethernet X710-10-GB-Adaptern behoben, bei dem die LED des Geräts nach dem Blinken weiter leuchtet, wenn die Geräte-LED in den über F2 aufgerufenen Geräteeinstellungen auf den Blinkmodus festgelegt ist. |
|     2005           |     19.0.12         |                                                                                                                                                                           |

### <a name="hba-azure-stack-node-capacity-drives"></a>HBA – Azure Stack-Knoten-Kapazitätslaufwerke

| Releaseversion    | Firmware version    | Änderungen    |
|--------------------|---------------------|------------|
|     2008           |     16.17.01.00     |            |
|     2005           |     16.17.00.05     |            |

### <a name="hba-hlh-capacity-drives"></a>HBA – HLH-Kapazitätslaufwerke

|     Releaseversion |     Firmware version |     Änderungen                                                                                           |
|---------------------|----------------------|-------------------------------------------------------------------------------------------------------|
| 2005, 2008          | 25.5.7.0005          | Es wurde ein Problem behoben, bei dem ein Controller aufgrund einer unvollständigen Konfiguration von nicht flüchtigem Speicher beim Starten nicht mehr reagierte. |

### <a name="hba---boot-drives"></a>HBA – Startlaufwerke

| Releaseversion | Firmware version | Änderungen                                                                                   |
|-----------------|------------------|-------------------------------------------------------------------------------------------|
| 2005, 2008      | 2.5.13.3024      | Es wurde eine PPID-Abweichung für M.2-Laufwerke des BOSS-Controllers auf der Bestandsseite der iDRAC-Hardware behoben. |

### <a name="cpld"></a>CPLD

| Releaseversion | Firmware version | Änderungen                                                                                                                                                                                                |
|-----------------|------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     2008        |     9.0.6        | Behebt mögliche Probleme, bei denen iDRAC während eines Firmwareupdates nicht mehr reagiert.<br> Signalrauschen wird jetzt gefiltert, um falsche Fehlermeldungen zu verhindern.<br> Die Hostspeicherzuordnung wurde geändert, um die potenzielle Deaktivierung vorderer USB-Anschlüsse zu verhindern. |
|     2005        |     1.0.6        |                                                                                                                                                                                                        |

### <a name="drive-fw"></a>Laufwerksfirmware

| Releaseversion | Firmware version | Änderungen                                |
|-----------------|------------------|----------------------------------------|
| 2008            | S402             | Ermöglicht zukünftige Onlinefirmwareupdates |
| 2005            | S401             |                                        |

## <a name="drivers"></a>Treiber

### <a name="nic"></a>NIC

| Releaseversion | Firmware version | Änderungen                                |
|-----------------|------------------|----------------------------------------|
| 2008            | 2.40.22511.0    |  |
| 2005            | 2.30.21713.0 |                                        |

### <a name="hba---capacity-drives"></a>HBA – Kapazitätslaufwerke

| Releaseversion | Firmware version | Änderungen |
|-----------------|------------------|---------|
|  2005, 2008   |  2.51.25.02  |         |

### <a name="hba---boot-drives"></a>HBA – Startlaufwerke

| Releaseversion | Firmware version | Änderungen |
|-----------------|------------------|---------|
|  2005, 2008   |  1.2.0.1051 |         |

### <a name="intel-chipset"></a>Intel-Chipsatz

| Releaseversion | Firmware version | Änderungen |
|-----------------|------------------|---------|
|  2005, 2008   | 10.1.18243.8188 |         |
