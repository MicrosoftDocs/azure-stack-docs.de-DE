---
title: Referenz für den Infrastructure Backup-Dienst | Microsoft-Dokumentation
description: Referenzmaterial für den Infrastructure Backup-Dienst in Azure Stack.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: D6EC0224-97EA-446C-BC95-A3D32F668E2C
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: 282d6f3a501550e49424c257b928e708f63ccadc
ms.sourcegitcommit: 245a4054a52e54d5989d6148fbbe386e1b2aa49c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2019
ms.locfileid: "70974852"
---
# <a name="infrastructure-backup-service-reference"></a>Referenz für den Infrastructure Backup-Dienst

## <a name="azure-backup-infrastructure"></a>Azure-Sicherungsinfrastruktur

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Azure Stack besteht aus vielen Diensten, die das Portal (Azure Resource Manager) und die gesamte Infrastrukturverwaltung umfassen. Ziel der App-ähnlichen Verwaltung von Azure Stack ist die Reduzierung der Komplexität für Bediener der Lösung.

Der Infrastructure Backup-Dienst ist so konzipiert, dass die komplexen Vorgänge zur Sicherung und Wiederherstellung von Daten intern in den Infrastrukturdiensten durchgeführt werden, sodass Bediener sich auf die Verwaltung der Lösung und die Einhaltung einer SLA für Benutzer konzentrieren können.

Um zu vermeiden, dass Sicherungen auf demselben System gespeichert werden, ist der Export der Sicherungsdaten in eine externe Freigabe erforderlich. Aufgrund der Erfordernis einer externen Freigabe kann der Administrator basierend auf vorhandenen Richtlinien des Unternehmens zur Sicherung und Notfallwiederherstellung den Speicherort der Daten flexibel festlegen.

### <a name="infrastructure-backup-service-components"></a>Komponenten des Infrastructure Backup-Diensts

Der Infrastructure Backup-Dienst enthält die folgenden Komponenten:

 - **Infrastructure Backup Controller**  
 Infrastructure Backup Controller wird mit jeder Azure Stack-Cloud instanziiert und ist in jeder Azure Stack-Cloud enthalten.
 - **Backup Resource Provider**  
 Backup Resource Provider (Backup RP) besteht aus der Benutzeroberfläche und APIs, die grundlegende Sicherungsfunktionen für die Azure Stack-Infrastruktur bereitstellen.

#### <a name="infrastructure-backup-controller"></a>Infrastructure Backup Controller

Der Infrastructure Backup Controller ist ein Service Fabric-Dienst, der für eine Azure Stack-Cloud instanziiert wird. Sicherungsressourcen werden auf regionaler Ebene erstellt und erfassen regionsspezifische Daten von AD, Zertifizierungsstellen, Azure Resource Manager, CRP, SRP, NRP, Key Vault und RBAC.

### <a name="backup-resource-provider"></a>Backup Resource Provider

Backup Resource Provider dient als Benutzeroberfläche im Azure Stack-Portal zur Basiskonfiguration und Anzeige von Sicherungsressourcen. Operatoren können auf der Benutzeroberfläche folgende Aktionen ausführen:

 - Erstmaliges Aktivieren der Sicherung durch Angabe des externen Speicherorts, der Anmeldeinformationen und des Verschlüsselungsschlüssels.
 - Anzeigen der fertig erstellten Sicherungsressourcen und des Status der noch in Erstellung befindlichen Ressourcen.
 - Ändern des Speicherorts, in dem Backup Controller Sicherungsdaten speichert.
 - Ändern der Anmeldeinformationen, über die Backup Controller auf den externen Speicherort zugreift.
 - Ändern des Verschlüsselungsschlüssels, mit dem Backup Controller Sicherungen verschlüsselt.


## <a name="backup-controller-requirements"></a>Anforderungen an Backup Controller

In diesem Abschnitt werden die wichtigen Anforderungen für den Infrastructure Backup-Dienst beschrieben. Sie sollten die Informationen sorgfältig überprüfen, bevor Sie die Sicherung für Ihre Azure Stack-Instanz aktivieren. Auch später sollten Sie während der Bereitstellung und beim nachfolgenden Betrieb bei Bedarf als Referenz darauf zurückgreifen.

Es handelt sich um folgende Anforderungen:

  - **Softwareanforderungen:** unterstützte Speicherorte und Angaben zur Größe 
  - **Netzwerkanforderungen:** Netzwerkanforderungen für unterschiedliche Speicherorte  

### <a name="software-requirements"></a>Softwareanforderungen

#### <a name="supported-storage-locations"></a>Unterstützte Speicherorte

| Speicherort                                                                 | Details                                                                                                                                                  |
|----------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| SMB-Dateifreigabe, gehostet auf einem Speichergerät innerhalb der vertrauenswürdigen Netzwerkumgebung. | SMB-Freigabe im selben Rechenzentrum, in dem Azure Stack bereitgestellt wird, oder in einem anderen Rechenzentrum. Mehrere Azure Stack-Instanzen können dieselbe Dateifreigabe verwenden. |
| SMB-Dateifreigabe in Azure.                                                          | Derzeit nicht unterstützt.                                                                                                                                 |
| Blob Storage in Azure.                                                            | Derzeit nicht unterstützt.                                                                                                                                 |

#### <a name="supported-smb-versions"></a>Unterstützte SMB-Versionen

| SMB | Version |
|-----|---------|
| SMB | 3.x     |

#### <a name="smb-encryption"></a>SMB-Verschlüsselung

**1907 und höher**

Der Infrastructure Backup-Dienst unterstützt das Übertragen von Sicherungsdaten an einen externen Speicherort mit aktivierter serverseitiger SMB-Verschlüsselung. Wenn der Server keine SMB-Verschlüsselung unterstützt oder die Funktion auf ihm nicht aktiviert ist, greift der Infrastructure Backup-Dienst auf die unverschlüsselte Datenübertragung zurück. Sicherungsdaten, die auf dem externen Speicherort platziert werden, sind im Ruhezustand immer verschlüsselt, unabhängig von der SMB-Verschlüsselung.

#### <a name="storage-location-sizing"></a>Speicherortgröße

Sie sollten die Sicherung mindestens zweimal täglich durchführen und Sicherungen höchstens sieben Tage lang aufbewahren. Dies ist das Standardverhalten bei aktivierten Infrastruktursicherungen auf Azure Stack.

**1907 und höher**

***Mit dem Azure AD-Identitätsanbieter verbundenes System***

| Umgebungsgröße | Voraussichtliche Größe der Sicherung | Gesamtmenge des erforderlichen Speicherplatzes |
|-------------------|--------------------------|--------------------------------|
| 4–16 Knoten/ASDK   | 1 GB                     | 20 GB                          |

***Per ADFS mit dem AD-Identitätsanbieter des Unternehmens verbundenes System***

| Umgebungsgröße | Voraussichtliche Größe der Sicherung | Gesamtmenge des erforderlichen Speicherplatzes |
|-------------------|--------------------------|--------------------------------|
| 4 bis 16 Knoten        | 20 GB                    | 280 GB                        |
| ASDK              | 10 GB                    | 140 GB                        |

**Vor 1907**

| Umgebungsgröße | Voraussichtliche Größe der Sicherung | Gesamtmenge des erforderlichen Speicherplatzes |
|-------------------|--------------------------|--------------------------------|
| 4 bis 16 Knoten        | 20 GB                    | 280 GB                        |
| ASDK              | 10 GB                    | 140 GB                        |

### <a name="network-requirements"></a>Netzwerkanforderungen

| Speicherort                                                                 | Details                                                                                                                                                                                 |
|----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| SMB-Dateifreigabe, gehostet auf einem Speichergerät innerhalb der vertrauenswürdigen Netzwerkumgebung. | Port 445 ist erforderlich, wenn sich die Azure Stack-Instanz in einer Umgebung mit Firewall befindet. Infrastructure Backup Controller initiiert eine Verbindung mit dem SMB-Dateiserver über Port 445. |
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

**Abwärtskompatibilitätsmodus** Ab 1901 ist die Unterstützung für Verschlüsselungsschlüssel veraltet und wird in einem zukünftigen Release entfernt. Wenn Sie von 1811 aktualisiert haben, als für die Sicherung bereits die Verwendung eines Verschlüsselungsschlüssels aktiviert war, verwendet Azure Stack weiterhin den Verschlüsselungsschlüssel. Der Abwärtskompatibilitätsmodus wird für mindestens drei Releases unterstützt. Nach diesem Zeitraum ist dann ein Zertifikat erforderlich.

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
