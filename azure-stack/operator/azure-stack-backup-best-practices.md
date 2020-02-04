---
title: Best Practices für den Infrastructure Backup-Dienst für Azure Stack Hub
description: Wenden Sie bei der Bereitstellung und Verwaltung von Azure Stack Hub eine Reihe bewährter Methoden an, um den Verlust von Daten bei einem schwerwiegenden Fehler zu minimieren.
author: justinha
ms.topic: article
ms.date: 02/08/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 880c5dfb72d0f70cc8748f2528a3c36562bebe93
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76878053"
---
# <a name="infrastructure-backup-service-best-practices"></a>Bewährte Methoden für den Infrastructure Backup-Dienst

Wenden Sie bei der Bereitstellung und Verwaltung von Azure Stack Hub eine Reihe bewährter Methoden an, um den Verlust von Daten bei einem schwerwiegenden Fehler zu minimieren.

Überprüfen Sie die bewährten Methoden regelmäßig, um sicherzustellen, dass Ihre Installation weiterhin kompatibel ist, wenn Änderungen am Vorgangsfluss vorgenommen werden. Wenn beim Implementieren dieser bewährten Methoden Probleme auftreten, können Sie sich an den Microsoft-Support wenden.

## <a name="configuration-best-practices"></a>Bewährte Methoden für die Konfiguration

### <a name="deployment"></a>Bereitstellung

Aktivieren Sie Infrastructure Backup nach der Bereitstellung jeder Azure Stack Hub-Cloud. Mithilfe von Azure Stack Hub PowerShell können Sie Sicherungen über jeden Client/Server mit Zugriff auf den API-Endpunkt der Bedienerverwaltung planen.

### <a name="networking"></a>Netzwerk

Für die UNC-Zeichenfolge (Universal Naming Convention) für den Pfad muss ein vollqualifizierter Domänenname (FQDN) verwendet werden. Die IP-Adresse kann verwendet werden, wenn die Namensauflösung nicht möglich ist. Eine UNC-Zeichenfolge gibt den Speicherort von Ressourcen (z.B. freigegebene Dateien oder Geräte) an.

### <a name="encryption"></a>Verschlüsselung

#### <a name="version-1901-and-newer"></a>Version 1901 und neuer

Das Verschlüsselungszertifikat wird zum Verschlüsseln der Sicherungsdaten verwendet, die in einen externen Speicher exportiert werden. Das Zertifikat kann ein selbstsigniertes Zertifikat sein, da das Zertifikat nur für den Transport von Schlüsseln verwendet wird. Weitere Informationen zum Erstellen eines Zertifikats finden Sie unter New-SelfSignedCertificate.
  
Der Schlüssel muss in einem sicheren Speicherort (z.B. globales Azure Key Vault-Zertifikat) gespeichert werden. Das CER-Format des Zertifikats dient zum Verschlüsseln von Daten. Das PFX-Format muss während der Bereitstellung der Cloudwiederherstellung von Azure Stack Hub verwendet werden, um Sicherungsdaten zu entschlüsseln.

![Speichern Sie das Zertifikat an einem sicheren Speicherort.](media/azure-stack-backup/azure-stack-backup-encryption-store-cert.png)

#### <a name="1811-and-older"></a>1811 und älter

Der Verschlüsselungsschlüssel wird zum Verschlüsseln der Sicherungsdaten verwendet, die in einen externen Speicher exportiert werden. Der Schlüssel wird im Rahmen der [Aktivierung der Sicherung für Azure Stack Hub mit PowerShell](azure-stack-backup-enable-backup-powershell.md) generiert.

Der Schlüssel muss in einem sicheren Speicherort (z.B. globales Azure Key Vault-Geheimnis) gespeichert werden. Dieser Schlüssel muss während der erneuten Bereitstellung von Azure Stack Hub verwendet werden.

![Speichern des Schlüssels an einem sicheren Speicherort](media/azure-stack-backup/azure-stack-backup-encryption2.png)

## <a name="operational-best-practices"></a>Bewährte Methoden für den Betrieb

### <a name="backups"></a>Backups

 - Sicherungsaufträge werden während der Ausführung des Systems ausgeführt, sodass bei der Verwaltung und in den Benutzer-Apps keine Ausfallzeiten entstehen. Die Sicherungsaufträge für eine Lösung mit angemessener Last dauern erwartungsgemäß 20 bis 40 Minuten.
 - Entsprechend den OEM-Anweisungen sollten manuell gesicherte Netzwerkswitches und der Hardwarelebenszyklus-Host (Hardware Lifecycle Host, HLH) in der gleichen Sicherungsfreigabe gespeichert werden, in der der Infrastructure Backup-Controller Sicherungsdaten auf Steuerungsebene speichert. Es empfiehlt sich, Switch- und HLH-Konfigurationen im Ordner der Region zu speichern. Bei mehreren Azure Stack Hub-Instanzen in derselben Region empfiehlt sich die Verwendung eines Bezeichners für jede Konfiguration, die zu einer Skalierungseinheit gehört.

### <a name="folder-names"></a>Ordnernamen

 - Infrastructure erstellt automatisch den Ordner „MASBACKUP“. Dabei handelt es sich um eine von Microsoft verwaltete Freigabe. Sie können Freigaben auf der gleichen Ebene wie „MASBACKUP“ erstellen. Es wird davon abgeraten, Ordner oder Speicherdaten im Ordner „MASBACKUP“ zu erstellen, die nicht von Azure Stack Hub erstellt werden.
 -  Verwenden Sie den Benutzer-FQDN und die Region in Ihrem Ordnernamen, um Sicherungsdaten aus unterschiedlichen Clouds zu unterscheiden. Der FQDN Ihrer Azure Stack Hub-Bereitstellung und -Endpunkte ist die Kombination aus den Parametern „Region“ und „externer Domänenname“. Weitere Informationen finden Sie unter [Integration des Azure Stack Hub-Rechenzentrums – DNS](azure-stack-integrate-dns.md).

Beispiel: Die Sicherungsfreigabe AzSBackups wird unter fileserver01.contoso.com gehostet. In dieser Dateifreigabe gibt es möglicherweise einen Ordner pro Azure Stack Hub-Bereitstellung mit dem externen Domänennamen und einen Unterordner mit dem Namen der Region.

FQDN: contoso.com  
Region: nyc


    \\fileserver01.contoso.com\AzSBackups
    \\fileserver01.contoso.com\AzSBackups\contoso.com
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\MASBackup

Im Ordner „MASBackup“ speichert Azure Stack Hub die Sicherungsdaten. Verwenden Sie diesen Ordner nicht, um Ihre eigenen Daten zu speichern. Auch OEMs sollten diesen Ordner nicht verwenden, um Sicherungsdaten zu speichern.

Für OEMs wird empfohlen, Sicherungsdaten für ihre Komponenten unter dem Ordner der Region zu speichern. Jeder Netzwerkswitch, Hardwarelebenszyklus-Host (HLH) usw. kann im jeweiligen eigenen Unterordner gespeichert werden. Beispiel:

    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\HLH
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Switches
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\DeploymentData
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Registration

### <a name="monitoring"></a>Überwachung

Die folgenden Warnungen werden vom System unterstützt:

| Warnung                                                   | Beschreibung                                                                                     | Wiederherstellung                                                                                                                                |
|---------------------------------------------------------|-------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Fehler bei der Sicherung, da die Kapazität der Dateifreigabe erschöpft ist. | Die Kapazität der Dateifreigabe ist erschöpft, und der Sicherungscontroller kann keine Sicherungsdateien in den Speicherort exportieren. | Fügen Sie zusätzliche Speicherkapazität hinzu, und wiederholen Sie den Sicherungsvorgang. Löschen Sie vorhandene Sicherungen (zuerst die ältesten Sicherungen), um Speicherplatz freizugeben.                    |
| Fehler bei der Sicherung aufgrund von Verbindungsproblemen             | Im Netzwerk zwischen Azure Stack Hub und der Dateifreigabe treten Probleme auf.                          | Beheben Sie das Netzwerkproblem, und wiederholen Sie den Sicherungsvorgang.                                                                                            |
| Fehler bei der Sicherung aufgrund eines Fehlers im Pfad.                | Der Dateifreigabepfad kann nicht aufgelöst werden.                                                          | Ordnen Sie die Freigabe über einen anderen Computer zu, um sicherzustellen, dass der Zugriff auf die Freigabe möglich ist. Möglicherweise müssen Sie den Pfad aktualisieren, wenn er nicht mehr gültig ist.       |
| Fehler bei der Sicherung aufgrund eines Authentifizierungsproblems.               | Möglicherweise liegt ein Problem mit den Anmeldeinformationen oder ein Netzwerkproblem vor, das sich auf die Authentifizierung auswirkt.    | Ordnen Sie die Freigabe über einen anderen Computer zu, um sicherzustellen, dass der Zugriff auf die Freigabe möglich ist. Möglicherweise müssen Sie die Anmeldeinformationen aktualisieren, wenn sie nicht mehr gültig sind. |
| Fehler bei der Sicherung aufgrund eines allgemeinen Fehlers.                    | Die fehlerhafte Anforderung kann auf einen zeitweiligen Fehler zurückzuführen sein. Wiederholen Sie den Sicherungsvorgang.                    | Wenden Sie sich an den Support.                                                                                                                               |

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich das Referenzmaterial für den [Infrastructure Backup-Dienst](azure-stack-backup-reference.md) an.

Aktivieren Sie den [Infrastructure Backup-Dienst](azure-stack-backup-enable-backup-console.md).
