---
title: 'Azure Stack Hub-Speicher: Unterschiede und Überlegungen | Microsoft-Dokumentation'
description: In diesem Artikel lernen Sie die Unterschiede zwischen Azure Stack Hub-Speicher und Azure-Speicher kennen und erhalten weitere Informationen zur Bereitstellung von Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviwer: xiaofmao
ms.lastreviewed: 01/30/2019
ms.openlocfilehash: dc50320941ab12fbd3f8b6b43f44e5ef9ddc7c45
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76535806"
---
# <a name="azure-stack-hub-storage-differences-and-considerations"></a>Azure Stack Hub-Speicher: Unterschiede und Überlegungen

Bei Azure Stack Hub-Speicher handelt es sich um eine Reihe von Speicherclouddiensten in Microsoft Azure Stack Hub. Der Azure Stack Hub-Speicher bietet Blob-, Tabellen-, Warteschlangen- und Kontoverwaltungsfunktionen mit einer mit Azure konsistenten Semantik.

In diesem Artikel sind die bekannten Unterschiede zwischen Azure Stack Hub-Speicher und Azure Storage-Diensten zusammengefasst. Außerdem werden die Aspekte erläutert, die beim Bereitstellen von Azure Stack Hub berücksichtigt werden müssen. Informationen zu allgemeinen Unterschieden zwischen globalen Azure-Umgebungen und Azure Stack Hub finden Sie im Artikel [Wichtige Aspekte](azure-stack-considerations.md).

## <a name="cheat-sheet-storage-differences"></a>Cheat Sheet: Speicherunterschiede

| Funktion | Azure (global) | Azure Stack Hub |
| --- | --- | --- |
|File Storage|Cloudbasierte SMB-Dateifreigaben unterstützt|Noch nicht unterstützt
|Azure Storage Service Encryption für ruhende Daten|256-Bit-AES-Verschlüsselung. Unterstützung von Verschlüsselung mit vom Kunden verwalteten Schlüsseln in Key Vault|BitLocker-128-Bit-AES-Verschlüsselung. Verschlüsselung mit vom Kunden verwalteten Schlüsseln wird nicht unterstützt.
|Speicherkontotyp|Konten vom Typ „Allgemein v1 (GPv1)“ und „Allgemein v2 (GPv2)“ sowie Blobspeicherkonten|Nur Konten vom Typ „Allgemein v1 (GPv1)“
|Replikationsoptionen|Lokal redundanter Speicher, georedundanter Speicher, schreibgeschützter georedundanter Speicher und zonenredundanter Speicher|Lokal redundanter Speicher.
|Storage Premium|Speicher mit hoher Leistung und geringer Latenz. Unter Storage Premium-Konten werden nur Seitenblobs unterstützt.|Kann bereitgestellt werden, aber ohne Leistungsgrenzwerte oder Garantien. Die Nutzung von Blockblobs, Anfügeblobs, Tabellen und Warteschlangen in Storage Premium-Konten wird nicht blockiert.
|Verwaltete Datenträger|Unterstützung für Premium und Standard|Unterstützt bei Verwendung von Version 1808 oder höher.
|Blobname|1\.024 Zeichen (2.048 Bytes)|880 Zeichen (1.760 Bytes)
|Maximale Blockblobgröße|4,75 TB (100 MB X 50.000 Blöcke)|4,75 TB (100 MB x 50.000 Blöcke) für das Update 1802 oder eine neuere Version. 50.000 x 4 MB (ca. 195 GB) für vorherige Versionen.
|Seitenblob-Momentaufnahmenkopie|Die Sicherung nicht verwalteter Azure-VM-Datenträger, die an einen ausgeführten virtuellen Computer angefügt sind, wird unterstützt.|Noch nicht unterstützt.
|Inkrementelle Momentaufnahmenkopie des Seitenblobs|Unterstützung für Premium- und Standard-Azure-Seitenblobs|Noch nicht unterstützt.
|Abrechnung von Seitenblobs|Gebühren fallen für individuelle Seiten an – unabhängig davon, ob sich diese im Blob oder in der Momentaufnahme befinden. Für Momentaufnahmen, die einem Blob zugeordnet sind, fallen keine zusätzlichen Gebühren an, bis das Basisblob aktualisiert wird.|Gebühren fallen für das Basisblob und für zugeordnete Momentaufnahmen an. Für jede einzelne Momentaufnahme fallen zusätzliche Gebühren an.
|Speicherebenen für Blobspeicher|Speicherebenen „Heiß“ (Hot), „Kalt“ (Cool) und „Archiv“.|Noch nicht unterstützt.
|Vorläufiges Löschen für Blobspeicher|Allgemein verfügbar|Noch nicht unterstützt.
|Maximale Seitenblobgröße|8 TB|1 TB
|Seitenblob – Seitengröße|512 Bytes|4 KB
|Größe für Tabellenpartitionsschlüssel und Zeilenschlüssel|1\.024 Zeichen (2.048 Bytes)|400 Zeichen (800 Bytes)
|Momentaufnahme eines Blobs|Die maximale Anzahl von Momentaufnahmen eines Blobs ist nicht beschränkt.|Die maximale Anzahl von Momentaufnahmen eines Blobs beträgt 1.000.
|Azure AD-Authentifizierung für Speicher|In der Vorschau|Noch nicht unterstützt.
|Unveränderliche Blobs|Allgemein verfügbar|Noch nicht unterstützt.
|Firewallregeln und VNET-Regeln für Speicher|Allgemein verfügbar|Noch nicht unterstützt.|

Es gibt auch Unterschiede zu Speichermetriken:

* Die Transaktionsdaten in Speichermetriken unterscheiden nicht zwischen interner oder externer Netzwerkbandbreite.
* Die Transaktionsdaten in Speichermetriken beinhalten keinen Zugriff auf die bereitgestellten Datenträger über virtuelle Computer.

## <a name="api-version"></a>API-Version

Die folgenden Versionen werden für Azure Stack Hub-Speicher unterstützt:

Azure Storage-Dienst-APIs:

Update 1811 oder neuere Versionen:

- [2017-11-09](https://docs.microsoft.com/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](https://docs.microsoft.com/rest/api/storageservices/version-2017-07-29)
- [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Vorherige Versionen:

- [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Verwaltungs-APIs für Azure Storage-Dienste:

Update 1811 oder neuere Versionen:

- [2017-10-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2017-06-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-12-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-05-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

Vorherige Versionen:

- [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

## <a name="powershell-version"></a>PowerShell-Version

Bei der PowerShell-Version für das Speichermodul müssen Sie auf die Version achten, die mit der REST-API kompatibel ist. 

| Modul | Unterstützte Version | Verwendung |
|----------------|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure.Storage | [4.5.0](https://www.powershellgallery.com/packages/Azure.Storage/4.5.0) | Verwaltet Blobs, Warteschlangen und Tabellen in Azure Stack Hub-Speicherkonten. |
| AzureRM.Storage | [5.0.4](https://www.powershellgallery.com/packages/AzureRM.Storage/5.0.4) | Erstellt und verwaltet Speicherkonten in Azure Stack Hub. |


Weitere Informationen zu von Azure Stack Hub unterstützten Speicherclientbibliotheken finden Sie unter: [Erste Schritte mit den Azure Stack Hub-Speicherentwicklungstools](azure-stack-storage-dev.md).

## <a name="next-steps"></a>Nächste Schritte

* [Erste Schritte mit den Azure Stack Hub-Speicherentwicklungstools](azure-stack-storage-dev.md)
* [Verwenden von Datenübertragungstools für Azure Stack Hub-Speicher](azure-stack-storage-transfer.md)
* [Einführung in Azure Stack Hub-Speicher](azure-stack-storage-overview.md)
