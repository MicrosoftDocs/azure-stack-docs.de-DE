---
title: Referenz für den Infrastructure Backup-Dienst für Azure Stack | Microsoft-Dokumentation
description: Dieser Artikel enthält Referenzmaterial für den Infrastructure Backup-Dienst für Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: D6EC0224-97EA-446C-BC95-A3D32F668E2C
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: mabrigg
ms.reviewer: hectorl
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: 7b95dba4b3462b2c0e36aa1464faf217fccd7a6a
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65617807"
---
# <a name="infrastructure-backup-service-reference"></a>Referenz für den Infrastructure Backup-Dienst

## <a name="azure-backup-infrastructure"></a>Azure-Sicherungsinfrastruktur

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Azure Stack besteht aus vielen Diensten, die das Portal, Azure Resource Manager und die Infrastrukturverwaltung umfassen. Ziel der anwendungsähnlichen Verwaltung von Azure Stack ist die Reduzierung der Komplexität für Bediener der Lösung.

Infrastructure Backup ist so konzipiert, dass die komplexen Vorgänge zur Sicherung und Wiederherstellung von Daten intern in den Infrastrukturdiensten durchgeführt werden, sodass Bediener sich auf die Verwaltung der Lösung und die Einhaltung einer SLA für Benutzer konzentrieren können.

Um zu vermeiden, dass Sicherungen auf demselben System gespeichert werden, ist der Export der Sicherungsdaten in eine externe Freigabe erforderlich. Aufgrund der Erfordernis einer externen Freigabe kann der Administrator basierend auf vorhandenen Richtlinien des Unternehmens zur Sicherung und Notfallwiederherstellung den Speicherort der Daten flexibel festlegen. 

### <a name="infrastructure-backup-components"></a>Infrastructure Backup-Komponenten

Infrastructure Backup enthält die folgenden Komponenten:

 - **Infrastructure Backup Controller**  
 Infrastructure Backup Controller wird mit jeder Azure Stack-Cloud instanziiert und ist in jeder Azure Stack-Cloud enthalten.
 - **Backup Resource Provider**  
 Backup Resource Provider (Backup RP) besteht aus der Benutzeroberfläche und Anwendungsprogrammierschnittstellen (APIs), die grundlegende Sicherungsfunktionen für die Azure Stack-Infrastruktur bereitstellen.

#### <a name="infrastructure-backup-controller"></a>Infrastructure Backup Controller

Infrastructure Backup Controller ist ein Service Fabric-Dienst, der für eine Azure Stack-Cloud instanziiert wird. Sicherungsressourcen werden auf regionaler Ebene erstellt und erfassen regionsspezifische Daten von AD, Zertifizierungsstellen, Azure Resource Manager, CRP, SRP, NRP, KeyVault und RBAC. 

### <a name="backup-resource-provider"></a>Backup Resource Provider

Backup Resource Provider dient als Benutzeroberfläche im Azure Stack-Portal zur Basiskonfiguration und Anzeige von Sicherungsressourcen. Der Bediener kann über die Benutzeroberfläche die folgenden Vorgänge durchführen:

 - Erstmaliges Aktivieren der Sicherung durch Angabe des externen Speicherorts, der Anmeldeinformationen und des Verschlüsselungsschlüssels
 - Anzeigen der fertig erstellten Sicherungsressourcen und des Status der noch in Erstellung befindlichen Ressourcen
 - Ändern des Speicherorts, in dem Backup Controller Sicherungsdaten speichert
 - Ändern der Anmeldeinformationen, über die Backup Controller auf den externen Speicherort zugreift
 - Ändern des Verschlüsselungsschlüssels, mit dem Backup Controller Sicherungen verschlüsselt 


## <a name="backup-controller-requirements"></a>Anforderungen an Backup Controller

In diesem Abschnitt werden die wichtigen Anforderungen für Infrastructure Backup beschrieben. Sie sollten die Informationen sorgfältig überprüfen, bevor Sie die Sicherung für Ihre Azure Stack-Instanz aktivieren. Auch später sollten Sie während der Bereitstellung und beim nachfolgenden Betrieb bei Bedarf als Referenz darauf zurückgreifen.

Es handelt sich um folgende Anforderungen:

  - **Softwareanforderungen:** unterstützte Speicherorte und Angaben zur Größe 
  - **Netzwerkanforderungen:** Netzwerkanforderungen für unterschiedliche Speicherorte  

### <a name="software-requirements"></a>Softwareanforderungen

#### <a name="supported-storage-locations"></a>Unterstützte Speicherorte

| Speicherort                                                                 | Details                                                                                                                                                  |
|----------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| SMB-Dateifreigabe, gehostet auf einem Speichergerät innerhalb der vertrauenswürdigen Netzwerkumgebung | SMB-Freigabe im selben Rechenzentrum, in dem Azure Stack bereitgestellt wird, oder in einem anderen Rechenzentrum. Mehrere Azure Stack-Instanzen können dieselbe Dateifreigabe verwenden. |
| SMB-Dateifreigabe in Azure                                                          | Derzeit nicht unterstützt.                                                                                                                                 |
| Blob Storage in Azure                                                            | Derzeit nicht unterstützt.                                                                                                                                 |

#### <a name="supported-smb-versions"></a>Unterstützte SMB-Versionen

| SMB | Version |
|-----|---------|
| SMB | 3.x     |

#### <a name="storage-location-sizing"></a>Speicherortgröße 

Infrastructure Backup Controller sichert Daten bedarfsgesteuert. Es empfiehlt sich, die Sicherung mindestens zweimal täglich durchzuführen und Sicherungen höchstens sieben Tage lang aufzubewahren. 

**1811 und höher**

| Umgebungsgröße | Voraussichtliche Größe der Sicherung | Gesamtmenge des erforderlichen Speicherplatzes |
|-------------------|--------------------------|--------------------------------|
| 4 bis 16 Knoten        | 20 GB                    | 280 GB                        |
| ASDK              | 10 GB                    | 140 GB                        |

**Vor 1811**

| Umgebungsgröße | Voraussichtliche Größe der Sicherung | Gesamtmenge des erforderlichen Speicherplatzes |
|-------------------|--------------------------|--------------------------------|
| 4–16 Knoten, ASDK  | 10 GB                     | 140 GB                        |

### <a name="network-requirements"></a>Netzwerkanforderungen

| Speicherort                                                                 | Details                                                                                                                                                                                 |
|----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| SMB-Dateifreigabe, gehostet auf einem Speichergerät innerhalb der vertrauenswürdigen Netzwerkumgebung | Port 445 ist erforderlich, wenn sich die Azure Stack-Instanz in einer Umgebung mit Firewall befindet. Infrastructure Backup Controller initiiert eine Verbindung mit dem SMB-Dateiserver über Port 445. |
| Zur Verwendung des FQDN des Dateiservers muss der Name über den PEP aufgelöst werden können.             |                                                                                                                                                                                         |

> [!Note]  
> Es müssen keine eingehenden Ports geöffnet werden.

### <a name="encryption-requirements"></a>Verschlüsselungsanforderungen

Ab 1901 verwendet der Infrastructure Backup-Dienst ein Zertifikat mit einem öffentlichen Schlüssel (.CER), um Sicherungsdaten zu verschlüsseln, und ein Zertifikat mit dem privaten Schlüssel (.PFX) zum Entschlüsseln von Sicherungsdaten während der Cloudwiederherstellung.   
 - Das Zertifikat wird für den Transport von Schlüsseln verwendet und nicht zum Einrichten einer sicheren, authentifizierten Kommunikation. Aus diesem Grund kann das Zertifikat ein selbstsigniertes Zertifikat sein. Azure Stack muss für dieses Zertifikat weder Stamm noch Vertrauensstellung überprüfen, sodass kein externer Internetzugriff erforderlich ist.
 
Das selbstsignierte Zertifikat besteht aus zwei Teilen: einem mit dem öffentlichen Schlüssel und einem mit dem privaten Schlüssel:
 - Verschlüsseln von Sicherungsdaten: Zertifikat mit dem öffentlichen Schlüssel (in .CER-Datei exportiert) wird verwendet, um Sicherungsdaten zu verschlüsseln.
 - Entschlüsseln von Sicherungsdaten: Zertifikat mit dem privaten Schlüssel (in .PFX-Datei exportiert) wird verwendet, um Sicherungsdaten zu entschlüsseln.

Das Zertifikat mit dem öffentlichen Schlüssel (.CER) wird nicht von der internen Geheimnisrotation verwaltet. Um das Zertifikat zu rotieren, müssen Sie ein neues selbstsigniertes Zertifikat erstellen und die Sicherungseinstellungen mit der neuen Datei (.CER) aktualisieren.  
 - Alle vorhandene Sicherungen bleiben mit dem vorherigen Verschlüsselungsschlüssel verschlüsselt. Neue Sicherungen verwenden den neuen öffentlichen Schlüssel. 
 
Das mit dem privaten Schlüssel (.PFX) während der Cloudwiederherstellung verwendete Zertifikat wird von Azure Stack aus Sicherheitsgründen nicht behalten. Diese Datei müssen während der Cloudwiederherstellung explizit bereitgestellt werden.  

**Abwärtskompatibilitätsmodus** Ab 1901 ist die Unterstützung für Verschlüsselungsschlüssel veraltet und wird in einem zukünftigen Release entfernt. Wenn Sie von 1811 aktualisiert haben, als für die Sicherung bereits die Verwendung eines Verschlüsselungsschlüssels aktiviert war, verwendet Azure Stack weiterhin den Verschlüsselungsschlüssel. Der Abwärtskompatibilitätsmodus wird für mindestens 3 Releases unterstützt. Nach diesem Zeitraum ist dann ein Zertifikat erforderlich. 
 * Das Aktualisieren vom Verschlüsselungsschlüssel auf ein Zertifikat ist ein unidirektionaler Vorgang.  
 * Alle vorhandenen Sicherungen bleiben mit dem Verschlüsselungsschlüssel verschlüsselt. Neue Sicherungen verwenden das Zertifikat. 

## <a name="infrastructure-backup-limits"></a>Beschränkungen von Infrastructure Backup

Berücksichtigen Sie folgende Einschränkungen beim Planen, Bereitstellen und Betreiben von Microsoft Azure Stack-Instanzen. Die Einschränkungen sind in der folgenden Tabelle beschrieben.

### <a name="infrastructure-backup-limits"></a>Beschränkungen von Infrastructure Backup

| Grenzwertbezeichner                                                 | Begrenzung        | Kommentare                                                                                                                                    |
|------------------------------------------------------------------|--------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Sicherungstyp                                                      | Nur vollständig    | Infrastructure Backup Controller unterstützt nur vollständige Sicherungen. Inkrementelle Sicherungen werden nicht unterstützt.                                          |
| Geplante Sicherungen                                                | Geplant und manuell  | Backup Controller unterstützt geplante und bedarfsgesteuerte Sicherungen.                                                                                 |
| Maximale Anzahl gleichzeitiger Sicherungsaufträge                                   | 1            | Pro Backup Controller-Instanz wird nur ein aktiver Sicherungsauftrag unterstützt.                                                                  |
| Netzwerkswitchkonfiguration                                     | Nicht zutreffend | Der Administrator muss die Netzwerkswitchkonfiguration mithilfe von OEM-Tools sichern. Informationen finden Sie in der Dokumentation der einzelnen OEM-Anbieter zu Azure Stack. |
| Hardwarelebenszyklus-Host                                          | Nicht zutreffend | Der Administrator muss den Hardwarelebenszyklus-Host mithilfe der OEM-Tools sichern. Informationen finden Sie in der Dokumentation der einzelnen OEM-Anbieter zu Azure Stack.      |
| Maximale Anzahl der Dateifreigaben                                    | 1            | Zum Speichern von Sicherungsdaten kann nur eine Dateifreigabe verwendet werden.                                                                                        |
| Backup App Services-, Function-, SQL-, MySQL-Ressourcenanbieterdaten | Nicht zutreffend | Informationen finden Sie in der Anleitung zur Bereitstellung und Verwaltung von durch Microsoft erstellten Erweiterungs-RPs.                                                  |
| Sicherung von Drittanbieter-RPs                              | Nicht zutreffend | Informationen finden Sie in der Anleitung zur Bereitstellung und Verwaltung von durch Drittanbieter erstellten Erweiterungs-RPs.                                          |

## <a name="next-steps"></a>Nächste Schritte

 - Weitere Informationen zum Infrastructure Backup-Dienst finden Sie unter [Sicherung und Datenwiederherstellung für Azure Stack mit dem Infrastructure Backup-Dienst](azure-stack-backup-infrastructure-backup.md).
