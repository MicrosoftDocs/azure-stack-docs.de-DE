---
title: Bewährte Methoden für den Infrastructure Backup-Dienst für Azure Stack – MDC | Microsoft-Dokumentation
description: Wenden Sie bei der Bereitstellung und Verwaltung von Azure Stack für ein Modular Data Center (MDC) diese bewährten Methoden an, um den Verlust von Daten bei einem schwerwiegenden Fehler zu minimieren.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: 0bbe41c26861d2a407641e06a2298ec5d3badf0f
ms.sourcegitcommit: 5fbc60b65d27c916ded7a95ba4102328d550c7e5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97598554"
---
# <a name="infrastructure-backup-service-best-practices---modular-data-center-mdc"></a>Bewährte Methoden für den Infrastructure Backup-Dienst – Modular Data Center (MDC)

*Anwendungsbereich: Modular Data Center, Azure Stack Hub Ruggedized*

Sehen Sie sich diese bewährten Methoden regelmäßig an, um sicherzustellen, dass sie in Ihrer installierten Version weiterhin umgesetzt sind, wenn Änderungen am Vorgangsfluss vorgenommen werden. Wenn beim Implementieren dieser bewährten Methoden Probleme auftreten, können Sie sich an den Microsoft-Support wenden.

## <a name="configuration-best-practices"></a>Bewährte Methoden für die Konfiguration

Infrastruktursicherungen sind bei der Bereitstellung eines neuen Systems standardmäßig aktiviert und werden intern gespeichert. Über das Azure Stack-Portal oder PowerShell können Sie einen externen Speicherort bereitstellen, um die Sicherungen an einen sekundären Speicherort zu exportieren.

### <a name="networking"></a>Netzwerk

Für die UNC-Zeichenfolge (Universal Naming Convention) für den Pfad muss ein vollqualifizierter Domänenname (FQDN) verwendet werden. Die IP-Adresse kann verwendet werden, wenn die Namensauflösung nicht möglich ist. Eine UNC-Zeichenfolge gibt den Speicherort von Ressourcen (z.B. freigegebene Dateien oder Geräte) an.

### <a name="encryption"></a>Verschlüsselung

Das Verschlüsselungszertifikat wird zum Verschlüsseln der Sicherungsdaten verwendet, die in einen externen Speicher exportiert werden. Das Zertifikat kann ein selbstsigniertes Zertifikat sein, da das Zertifikat nur für den Transport von Schlüsseln verwendet wird. Weitere Informationen zum Erstellen eines Zertifikats finden Sie unter New-SelfSignedCertificate.

Das Zertifikat muss an einem sicheren Ort gespeichert werden. Das CER-Format des Zertifikats wird nur zum Verschlüsseln von Daten und nicht zum Herstellen der Kommunikation verwendet.

## <a name="operational-best-practices"></a>Bewährte Methoden für den Betrieb

### <a name="backups"></a>Backups

- Sicherungsaufträge werden während der Ausführung des Systems ausgeführt, sodass es zu keiner Downtime der Verwaltungsoberfläche oder Benutzer-Apps kommt. Bei einer angemessen ausgelasteten Lösung dauern Sicherungsaufträge erwartungsgemäß 20 bis 40 Minuten.

- Bereitstellung weiterer Anweisungen zur manuellen Sicherung von Netzwerkswitches und des Hardwarelebenszyklus-Hosts (HLH)

### <a name="folder-names"></a>Ordnernamen

- Infrastructure erstellt automatisch den Ordner „MASBACKUP“. Dabei handelt es sich um eine von Microsoft verwaltete Freigabe. Sie können Freigaben auf der gleichen Ebene wie „MASBACKUP“ erstellen. Es wird davon abgeraten, Ordner oder Speicherdaten im Ordner „MASBACKUP“ zu erstellen, die nicht von Azure Stack erstellt werden.

- Verwenden Sie den Benutzer-FQDN und die Region in Ihrem Ordnernamen, um Sicherungsdaten aus unterschiedlichen Clouds zu unterscheiden. Der FQDN Ihrer Azure Stack-Bereitstellung und -Endpunkte ist die Kombination aus den Parametern „Region“ und „externer Domänenname“. Weitere Informationen finden Sie unter [Integration des Azure Stack-Rechenzentrums – DNS](../../operator/azure-stack-integrate-dns.md).

Beispiel: Die Sicherungsfreigabe AzSBackups wird unter fileserver01.contoso.com gehostet. In dieser Dateifreigabe gibt es möglicherweise einen Ordner pro Azure Stack-Bereitstellung mit dem externen Domänennamen und einen Unterordner mit dem Namen der Region.

- FQDN: contoso.com
- Region: nyc

```shell
\\fileserver01.contoso.com\AzSBackups
\\fileserver01.contoso.com\AzSBackups\contoso.com
\\fileserver01.contoso.com\AzSBackups\contoso.com\nyc
\\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\MASBackup
```

Im Ordner `MASBackup` speichert Azure Stack die Sicherungsdaten. Verwenden Sie diesen Ordner nicht, um Ihre eigenen Daten zu speichern. Auch OEMs sollten diesen Ordner nicht verwenden, um Sicherungsdaten zu speichern.

Für OEMs wird empfohlen, Sicherungsdaten für ihre Komponenten unter dem Ordner der Region zu speichern. Jeder Netzwerkswitch, Hardwarelebenszyklus-Host (HLH) usw. kann im jeweiligen eigenen Unterordner gespeichert werden. Beispiel:

```shell
\\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\HLH
\\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Switches
\\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\DeploymentData
\\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Registration
```

### <a name="monitoring"></a>Überwachung

Die folgenden Warnungen werden vom System unterstützt:

| Warnung                                                   | BESCHREIBUNG                                                                                     | Wiederherstellung                                                                                                                                |
|---------------------------------------------------------|-------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Fehler bei der Sicherung, da die Kapazität der Dateifreigabe erschöpft ist. | Die Kapazität der Dateifreigabe ist erschöpft, und der Sicherungscontroller kann keine Sicherungsdateien in den Speicherort exportieren. | Fügen Sie zusätzliche Speicherkapazität hinzu, und wiederholen Sie den Sicherungsvorgang. Löschen Sie vorhandene Sicherungen (zuerst die ältesten Sicherungen), um Speicherplatz freizugeben.                    |
| Fehler bei der Sicherung aufgrund von Verbindungsproblemen             | Im Netzwerk zwischen Azure Stack und der Dateifreigabe treten Probleme auf.                          | Beheben Sie das Netzwerkproblem, und wiederholen Sie den Sicherungsvorgang.                                                                                            |
| Fehler bei der Sicherung aufgrund eines Fehlers im Pfad.                | Der Dateifreigabepfad kann nicht aufgelöst werden.                                                          | Ordnen Sie die Freigabe über einen anderen Computer zu, um sicherzustellen, dass der Zugriff auf die Freigabe möglich ist. Möglicherweise müssen Sie den Pfad aktualisieren, wenn er nicht mehr gültig ist.       |
| Fehler bei der Sicherung aufgrund eines Authentifizierungsproblems.               | Möglicherweise liegt ein Problem mit den Anmeldeinformationen oder ein Netzwerkproblem vor, das sich auf die Authentifizierung auswirkt.    | Ordnen Sie die Freigabe über einen anderen Computer zu, um sicherzustellen, dass der Zugriff auf die Freigabe möglich ist. Möglicherweise müssen Sie die Anmeldeinformationen aktualisieren, wenn sie nicht mehr gültig sind. |
| Fehler bei der Sicherung aufgrund eines allgemeinen Fehlers.                    | Die fehlerhafte Anforderung kann auf einen zeitweiligen Fehler zurückzuführen sein. Wiederholen Sie den Sicherungsvorgang.                    | Wenden Sie sich an den Support.                                                                                                                               |

### <a name="infrastructure-backup-service-components"></a>Komponenten des Infrastructure Backup-Diensts

Der Infrastructure Backup-Dienst enthält die folgenden Komponenten:

- **Infrastructure Backup Controller**: Infrastructure Backup Controller wird mit jeder Azure Stack-Cloud instanziiert und ist in jeder Azure Stack-Cloud enthalten.

- **Backup Resource Provider**: Backup Resource Provider (Backup RP) besteht aus der Benutzeroberfläche und APIs, die grundlegende Sicherungsfunktionen für die Azure Stack-Infrastruktur bereitstellen.

### <a name="infrastructure-backup-controller"></a>Infrastructure Backup Controller

Der Infrastructure Backup Controller ist ein Service Fabric-Dienst, der für eine Azure Stack-Cloud instanziiert wird. Sicherungsressourcen werden auf regionaler Ebene erstellt und erfassen regionsspezifische Dienstdaten von AD, Zertifizierungsstellen, Azure Resource Manager, CRP, SRP, NRP, Key Vault und RBAC.

### <a name="backup-resource-provider"></a>Backup Resource Provider

Backup Resource Provider dient als Benutzeroberfläche im Azure Stack-Portal zur Basiskonfiguration und Anzeige von Sicherungsressourcen. Operatoren können auf der Benutzeroberfläche folgende Aktionen ausführen:

- Erstmaliges Aktivieren der Sicherung durch Angabe des externen Speicherorts, der Anmeldeinformationen und des Verschlüsselungsschlüssels.
- Anzeigen der fertig erstellten Sicherungsressourcen und des Status der noch in Erstellung befindlichen Ressourcen.
- Ändern des Speicherorts, in dem Backup Controller Sicherungsdaten speichert.
- Ändern der Anmeldeinformationen, über die Backup Controller auf den externen Speicherort zugreift.
- Ändern des Verschlüsselungszertifikats, mit dem Backup Controller Sicherungen verschlüsselt

## <a name="backup-controller-requirements"></a>Anforderungen an Backup Controller

In diesem Abschnitt werden wichtige Anforderungen für den Infrastructure Backup-Dienst beschrieben. Sie sollten diese Informationen sorgfältig lesen, bevor Sie die Sicherung für Ihre Azure Stack-Instanz aktivieren. Auch später sollten Sie während der Bereitstellung und des darauffolgenden Betriebs bei Bedarf darauf zurückgreifen.

Es handelt sich um folgende Anforderungen:

- **Softwareanforderungen**: Beschreibung der unterstützten Speicherorte und Hilfestellungen in Bezug auf die Größe
- **Netzwerkanforderungen**: Beschreibung der Netzwerkanforderungen für verschiedene Speicherorte

### <a name="software-requirements"></a>Softwareanforderungen

#### <a name="supported-storage-locations"></a>Unterstützte Speicherorte

| Speicherort                                                                  | Details                                                                                                                                                  |
|-----------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| SMB-Dateifreigabe, gehostet auf einem Speichergerät innerhalb der vertrauenswürdigen Netzwerkumgebung. | SMB-Freigabe im selben Rechenzentrum, in dem Azure Stack bereitgestellt wird, oder in einem anderen Rechenzentrum. Mehrere Azure Stack-Instanzen können dieselbe Dateifreigabe verwenden. |
| SMB-Dateifreigabe in Azure.                                                          | Derzeit nicht unterstützt.                                                                                                                                 |
| Blob Storage in Azure.                                                            | Derzeit nicht unterstützt.                                                                                                                                 |

#### <a name="supported-smb-versions"></a>Unterstützte SMB-Versionen

|SMB  |Version  |
|---------|---------|
|SMB     |   3.x      |

#### <a name="smb-encryption"></a>SMB-Verschlüsselung

Der Infrastructure Backup-Dienst unterstützt das Übertragen von Sicherungsdaten an einen externen Speicherort mit aktivierter serverseitiger SMB-Verschlüsselung. Wenn der Server keine SMB-Verschlüsselung unterstützt oder das Feature auf ihm nicht aktiviert ist, greift der Infrastructure Backup-Dienst auf die unverschlüsselte Datenübertragung zurück. Sicherungsdaten an dem externen Speicherort sind im Ruhezustand immer verschlüsselt, unabhängig von der SMB-Verschlüsselung.

#### <a name="storage-location-sizing"></a>Speicherortgröße

Sie sollten mindestens zweimal täglich eine Sicherung durchführen und Sicherungen höchstens sieben Tage lang aufbewahren. Dies ist das Standardverhalten bei aktivierten Infrastruktursicherungen auf Azure Stack.


|Umgebungsgröße  |Voraussichtliche Größe der Sicherung  |Gesamtmenge des erforderlichen Speicherplatzes  |
|---------|---------|---------|
|4 bis 16 Knoten     |  20 GB       |  280 GB       |
<!-- TZLASDKFIX 
|ASDK     |   10 GB      |   140 GB     |
-->
### <a name="network-requirements"></a>Netzwerkanforderungen

|Speicherort  |Details  |
|---------|---------|
|SMB-Dateifreigabe, gehostet auf einem Speichergerät innerhalb der vertrauenswürdigen Netzwerkumgebung.     |  Port 445 ist erforderlich, wenn sich die Azure Stack-Instanz in einer Umgebung mit Firewall befindet. Infrastructure Backup Controller initiiert eine Verbindung mit dem SMB-Dateiserver über Port 445.       |
| Zur Verwendung des FQDN des Dateiservers muss der Name über den PEP aufgelöst werden können.     |         |

> [!NOTE]
> Es müssen keine eingehenden Ports geöffnet werden.

### <a name="encryption-requirements"></a>Verschlüsselungsanforderungen

Der Infrastructure Backup-Dienst verwendet ein Zertifikat mit einem öffentlichen Schlüssel (.CER), um Sicherungsdaten zu verschlüsseln. Das Zertifikat wird für den Transport von Schlüsseln verwendet und nicht zum Einrichten einer sicheren, authentifizierten Kommunikation. Aus diesem Grund kann das Zertifikat ein selbstsigniertes Zertifikat sein. Azure Stack muss für dieses Zertifikat weder den Stamm noch die Vertrauensstellung überprüfen, sodass kein externer Internetzugriff erforderlich ist.

Das selbstsignierte Zertifikat besteht aus zwei Teilen: einem mit dem öffentlichen Schlüssel und einem mit dem privaten Schlüssel:

- Verschlüsseln von Sicherungsdaten: Zertifikat mit dem öffentlichen Schlüssel (in .CER-Datei exportiert) wird verwendet, um Sicherungsdaten zu verschlüsseln.
- Entschlüsseln von Sicherungsdaten: Zertifikat mit dem privaten Schlüssel (in .PFX-Datei exportiert) wird verwendet, um Sicherungsdaten zu entschlüsseln.

Das Zertifikat mit dem öffentlichen Schlüssel (.CER) wird nicht von der internen Geheimnisrotation verwaltet. Zum Rotieren des Zertifikats müssen Sie ein neues selbstsigniertes Zertifikat erstellen und die Sicherungseinstellungen mit der neuen Datei (.CER) aktualisieren.

Alle vorhandene Sicherungen bleiben mit dem vorherigen Verschlüsselungsschlüssel verschlüsselt. Neue Sicherungen verwenden den neuen öffentlichen Schlüssel.

Das mit dem privaten Schlüssel (.PFX) während der Cloudwiederherstellung verwendete Zertifikat wird von Azure Stack aus Sicherheitsgründen nicht beibehalten.

## <a name="infrastructure-backup-limits"></a>Beschränkungen von Infrastructure Backup

Berücksichtigen Sie folgende Einschränkungen beim Planen, Bereitstellen und Betreiben von Microsoft Azure Stack-Instanzen. Die Einschränkungen sind in der folgenden Tabelle beschrieben.

|Grenzwertbezeichner  |Begrenzung  |Kommentare  |
|---------|---------|---------|
|Sicherungstyp     | Nur vollständig        | Infrastructure Backup Controller unterstützt nur vollständige Sicherungen. Inkrementelle Sicherungen werden nicht unterstützt.        |
|Geplante Sicherungen     | Geplant und manuell        | Backup Controller unterstützt geplante und bedarfsgesteuerte Sicherungen.        |
|Maximale Anzahl gleichzeitiger Sicherungsaufträge      | 1        | Pro Backup Controller-Instanz wird nur ein aktiver Sicherungsauftrag unterstützt.        |
|Netzwerkswitchkonfiguration     | Nicht zutreffend         | Der Administrator muss die Netzwerkswitchkonfiguration mithilfe von OEM-Tools sichern. Informationen finden Sie in der Dokumentation der einzelnen OEM-Anbieter zu Azure Stack.        |
|Hardwarelebenszyklus-Host     | Nicht zutreffend         | Der Administrator muss den Hardwarelebenszyklus-Host mithilfe der OEM-Tools sichern. Informationen finden Sie in der Dokumentation der einzelnen OEM-Anbieter zu Azure Stack.        |
|Maximale Anzahl der Dateifreigaben     | 1        | Zum Speichern von Sicherungsdaten kann nur eine Dateifreigabe verwendet werden.        |
|Backup value-add resource providers (Sicherung von Ressourcenanbietern, die Mehrwert schaffen)     | Inbegriffen        | Infrastructure Backup umfasst die Sicherung für Event Hubs RP, IoT Hub RP und Data Box Edge RP.        |

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich weiter über den [Infrastructure Backup-Dienst](../../operator/azure-stack-backup-reference.md).
