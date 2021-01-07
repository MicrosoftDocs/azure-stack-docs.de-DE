---
title: Referenz für den Infrastructure Backup-Dienst
description: Referenzmaterial für den Infrastructure Backup-Dienst in Azure Stack Hub.
author: PatAltimore
ms.topic: article
ms.date: 02/12/2019
ms.author: patricka
ms.reviewer: hectorl
ms.lastreviewed: 10/25/2019
ms.openlocfilehash: 7fc20dfa20c0088f81d386d2b371b6a17bf45d5f
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97871615"
---
# <a name="infrastructure-backup-service-reference"></a>Referenz für den Infrastructure Backup-Dienst

## <a name="azure-backup-infrastructure"></a>Azure-Sicherungsinfrastruktur

Azure Stack Hub besteht aus vielen Diensten, die das Portal (Azure Resource Manager) und die gesamte Infrastrukturverwaltung umfassen. Ziel der App-ähnlichen Verwaltung von Azure Stack Hub ist die Reduzierung der Komplexität für Bediener der Lösung.

Der Infrastructure Backup-Dienst ist so konzipiert, dass die komplexen Vorgänge zur Sicherung und Wiederherstellung von Daten intern in den Infrastrukturdiensten durchgeführt werden, sodass Bediener sich auf die Verwaltung der Lösung und die Einhaltung einer SLA für Benutzer konzentrieren können.

Um zu vermeiden, dass Sicherungen auf demselben System gespeichert werden, ist der Export der Sicherungsdaten in eine externe Freigabe erforderlich. Aufgrund der Erfordernis einer externen Freigabe kann der Administrator basierend auf vorhandenen Richtlinien des Unternehmens zur Sicherung und Notfallwiederherstellung den Speicherort der Daten flexibel festlegen.

### <a name="infrastructure-backup-service-components"></a>Komponenten des Infrastructure Backup-Diensts

Der Infrastructure Backup-Dienst enthält die folgenden Komponenten:

 - **Infrastructure Backup Controller**  
 Der Infrastructure Backup Controller wird mit jeder Azure Stack Hub-Cloud instanziiert und ist in jeder Azure Stack Hub-Cloud enthalten.
 - **Backup Resource Provider**  
 Der Backup Resource Provider (Backup RP) besteht aus der Benutzeroberfläche und APIs, die grundlegende Sicherungsfunktionen für die Azure Stack Hub-Infrastruktur bereitstellen.

#### <a name="infrastructure-backup-controller"></a>Infrastructure Backup Controller

Der Infrastructure Backup Controller ist ein Service Fabric-Dienst, der für eine Azure Stack Hub-Cloud instanziiert wird. Sicherungsressourcen werden auf regionaler Ebene erstellt und erfassen regionsspezifische Daten von AD, Zertifizierungsstellen, Azure Resource Manager, CRP, SRP, NRP, Key Vault und RBAC.

### <a name="backup-resource-provider"></a>Backup Resource Provider

Der Backup Resource Provider dient im Azure Stack Hub-Portal als Benutzeroberfläche zur Basiskonfiguration und Anzeige von Sicherungsressourcen. Operatoren können auf der Benutzeroberfläche folgende Aktionen ausführen:

 - Erstmaliges Aktivieren der Sicherung durch Angabe des externen Speicherorts, der Anmeldeinformationen und des Verschlüsselungsschlüssels.
 - Anzeigen der fertig erstellten Sicherungsressourcen und des Status der noch in Erstellung befindlichen Ressourcen.
 - Ändern des Speicherorts, in dem Backup Controller Sicherungsdaten speichert.
 - Ändern der Anmeldeinformationen, über die Backup Controller auf den externen Speicherort zugreift.
 - Ändern des Verschlüsselungsschlüssels, mit dem Backup Controller Sicherungen verschlüsselt.


## <a name="backup-controller-requirements"></a>Anforderungen an Backup Controller

In diesem Abschnitt werden die wichtigen Anforderungen für den Infrastructure Backup-Dienst beschrieben. Sie sollten die Informationen sorgfältig überprüfen, bevor Sie die Sicherung für Ihre Azure Stack Hub-Instanz aktivieren. Auch später sollten Sie während der Bereitstellung und beim nachfolgenden Betrieb bei Bedarf als Referenz darauf zurückgreifen.

Es handelt sich um folgende Anforderungen:

  - **Softwareanforderungen:** unterstützte Speicherorte und Angaben zur Größe 
  - **Netzwerkanforderungen:** Netzwerkanforderungen für unterschiedliche Speicherorte  

### <a name="software-requirements"></a>Softwareanforderungen

#### <a name="supported-storage-locations"></a>Unterstützte Speicherorte

| Speicherort                                                                 | Details                                                                                                                                                  |
|----------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| SMB-Dateifreigabe, gehostet auf einem Speichergerät innerhalb der vertrauenswürdigen Netzwerkumgebung. | SMB-Freigabe im selben Rechenzentrum, in dem Azure Stack Hub bereitgestellt wird, oder in einem anderen Rechenzentrum. Mehrere Azure Stack Hub-Instanzen können dieselbe Dateifreigabe verwenden. |
| SMB-Dateifreigabe in Azure.                                                          | Derzeit nicht unterstützt.                                                                                                                                 |
| Blob Storage in Azure.                                                            | Derzeit nicht unterstützt.                                                                                                                                 |

#### <a name="supported-smb-versions"></a>Unterstützte SMB-Versionen

| SMB | Version |
|-----|---------|
| SMB | 3.x     |

#### <a name="smb-encryption"></a>SMB-Verschlüsselung

::: moniker range=">=azs-1907"

Der Infrastructure Backup-Dienst unterstützt das Übertragen von Sicherungsdaten an einen externen Speicherort mit aktivierter serverseitiger SMB-Verschlüsselung. Wenn der Server keine SMB-Verschlüsselung unterstützt oder die Funktion auf ihm nicht aktiviert ist, greift der Infrastructure Backup-Dienst auf die unverschlüsselte Datenübertragung zurück. Sicherungsdaten, die auf dem externen Speicherort platziert werden, sind im Ruhezustand immer verschlüsselt, unabhängig von der SMB-Verschlüsselung.

::: moniker-end

#### <a name="storage-location-sizing"></a>Speicherortgröße

Sie sollten die Sicherung mindestens zweimal täglich durchführen und Sicherungen höchstens sieben Tage lang aufbewahren. Dies ist das Standardverhalten bei aktivierten Infrastruktursicherungen auf Azure Stack Hub.

| Umgebungsgröße | Voraussichtliche Größe der Sicherung | Gesamtmenge des erforderlichen Speicherplatzes |
|-------------------|--------------------------|--------------------------------|
| 4 bis 16 Knoten        | 20 GB                    | 280 GB                        |
| ASDK              | 10 GB                    | 140 GB                        |

### <a name="network-requirements"></a>Netzwerkanforderungen

| Speicherort                                                                 | Details                                                                                                                                                                                 |
|----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| SMB-Dateifreigabe, gehostet auf einem Speichergerät innerhalb der vertrauenswürdigen Netzwerkumgebung. | Port 445 ist erforderlich, wenn sich die Azure Stack Hub-Instanz in einer Umgebung mit Firewall befindet. Infrastructure Backup Controller initiiert eine Verbindung mit dem SMB-Dateiserver über Port 445. |
| Zur Verwendung des FQDN des Dateiservers muss der Name über den PEP aufgelöst werden können.             |                                                                                                                                                                                         |

#### <a name="firewall-rules"></a>Firewallregeln
Stellen Sie sicher, dass Sie Firewallregeln einrichten, um Verbindungen zwischen den ERCS-VMs und dem externen Speicherort zuzulassen. 

| `Source` | Ziel | Protokoll/Port |
|------------------|-----------------------|--------------------------------|
| ERCS-VM 1        | Speicherort      | 445/SMB                        |
| ERCS-VM 2        | Speicherort      | 445/SMB                        |
| ERCS-VM 3        | Speicherort      | 445/SMB                        |

> [!Note]  
> Es müssen keine eingehenden Ports geöffnet werden.

### <a name="encryption-requirements"></a>Verschlüsselungsanforderungen

Ab 1901 verwendet der Infrastructure Backup-Dienst ein Zertifikat mit einem öffentlichen Schlüssel (.CER), um Sicherungsdaten zu verschlüsseln, und ein Zertifikat mit dem privaten Schlüssel (.PFX) zum Entschlüsseln von Sicherungsdaten während der Cloudwiederherstellung.

 - Das Zertifikat wird für den Transport von Schlüsseln verwendet und nicht zum Einrichten einer sicheren, authentifizierten Kommunikation. Aus diesem Grund kann das Zertifikat ein selbstsigniertes Zertifikat sein. Azure Stack Hub muss für dieses Zertifikat weder Stamm noch Vertrauensstellung überprüfen, sodass kein externer Internetzugriff erforderlich ist.

Das selbstsignierte Zertifikat besteht aus zwei Teilen: einem mit dem öffentlichen Schlüssel und einem mit dem privaten Schlüssel:

 - Verschlüsseln von Sicherungsdaten: Zertifikat mit dem öffentlichen Schlüssel (in .CER-Datei exportiert) wird verwendet, um Sicherungsdaten zu verschlüsseln.
 - Entschlüsseln von Sicherungsdaten: Zertifikat mit dem privaten Schlüssel (in .PFX-Datei exportiert) wird verwendet, um Sicherungsdaten zu entschlüsseln.

Das Zertifikat mit dem öffentlichen Schlüssel (.CER) wird nicht von der internen Geheimnisrotation verwaltet. Um das Zertifikat zu rotieren, müssen Sie ein neues selbstsigniertes Zertifikat erstellen und die Sicherungseinstellungen mit der neuen Datei (.CER) aktualisieren. 
 
 - Alle vorhandene Sicherungen bleiben mit dem vorherigen Verschlüsselungsschlüssel verschlüsselt. Neue Sicherungen verwenden den neuen öffentlichen Schlüssel.
 
Das mit dem privaten Schlüssel (.PFX) während der Cloudwiederherstellung verwendete Zertifikat wird aus Sicherheitsgründen von Azure Stack Hub nicht behalten. Diese Datei müssen während der Cloudwiederherstellung explizit bereitgestellt werden.  

**Abwärtskompatibilitätsmodus** Ab 1901 ist die Unterstützung für Verschlüsselungsschlüssel veraltet und wird in einem zukünftigen Release entfernt. Wenn Sie von 1811 aktualisiert haben, als für die Sicherung bereits die Verwendung eines Verschlüsselungsschlüssels aktiviert war, verwendet Azure Stack Hub weiterhin den Verschlüsselungsschlüssel. Der Abwärtskompatibilitätsmodus wird für mindestens drei Releases unterstützt. Nach diesem Zeitraum ist dann ein Zertifikat erforderlich.

 * Das Aktualisieren vom Verschlüsselungsschlüssel auf ein Zertifikat ist ein unidirektionaler Vorgang.  
 * Alle vorhandenen Sicherungen bleiben mit dem Verschlüsselungsschlüssel verschlüsselt. Neue Sicherungen verwenden das Zertifikat. 

## <a name="infrastructure-backup-limits"></a>Beschränkungen von Infrastructure Backup

Berücksichtigen Sie folgende Einschränkungen beim Planen, Bereitstellen und Betreiben von Microsoft Azure Stack Hub-Instanzen. Die Einschränkungen sind in der folgenden Tabelle beschrieben.

### <a name="infrastructure-backup-limits"></a>Beschränkungen von Infrastructure Backup

| Grenzwertbezeichner                                                 | Begrenzung        | Kommentare                                                                                                                                    |
|------------------------------------------------------------------|--------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Sicherungstyp                                                      | Nur vollständig    | Infrastructure Backup Controller unterstützt nur vollständige Sicherungen. Inkrementelle Sicherungen werden nicht unterstützt.                                          |
| Geplante Sicherungen                                                | Geplant und manuell  | Backup Controller unterstützt geplante und bedarfsgesteuerte Sicherungen.                                                                                 |
| Maximale Anzahl gleichzeitiger Sicherungsaufträge                                   | 1            | Pro Backup Controller-Instanz wird nur ein aktiver Sicherungsauftrag unterstützt.                                                                  |
| Netzwerkswitchkonfiguration                                     | Nicht zutreffend | Der Administrator muss die Netzwerkswitchkonfiguration mithilfe von OEM-Tools sichern. Informationen finden Sie in der Dokumentation der einzelnen OEM-Anbieter zu Azure Stack Hub. |
| Hardwarelebenszyklus-Host                                          | Nicht zutreffend | Der Administrator muss den Hardwarelebenszyklus-Host mithilfe der OEM-Tools sichern. Informationen finden Sie in der Dokumentation der einzelnen OEM-Anbieter zu Azure Stack Hub.      |
| Maximale Anzahl der Dateifreigaben                                    | 1            | Zum Speichern von Sicherungsdaten kann nur eine Dateifreigabe verwendet werden.                                                                                        |
| Backup App Services-, Function-, SQL-, MySQL-Ressourcenanbieterdaten | Nicht zutreffend | Informationen finden Sie in der Anleitung zur Bereitstellung und Verwaltung von durch Microsoft erstellten Erweiterungs-RPs.                                                  |
| Sicherung von Drittanbieter-RPs                              | Nicht zutreffend | Informationen finden Sie in der Anleitung zur Bereitstellung und Verwaltung von durch Drittanbieter erstellten Erweiterungs-RPs.                                          |

## <a name="next-steps"></a>Nächste Schritte

 - Weitere Informationen zum Infrastructure Backup-Dienst finden Sie unter [Sicherung und Datenwiederherstellung für Azure Stack Hub mit dem Infrastructure Backup-Dienst](azure-stack-backup-infrastructure-backup.md).
