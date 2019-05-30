---
title: Azure Stack-Telemetriedaten | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Azure Stack-Telemetrieeinstellungen mithilfe von PowerShell konfigurieren.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 24948fb53ed4c5bdbbe0490f581daff7c66c33bc
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66267434"
---
# <a name="azure-stack-telemetry"></a>Azure Stack-Telemetriedaten

Azure Stack-Systemdaten (Telemetriedaten) werden über die Benutzererfahrung im verbundenen Modus automatisch an Microsoft hochgeladen. Die gesammelten Azure Stack-Telemetriedaten werden von Microsoft-Teams in erster Linie zum Optimieren der Kundenerfahrung sowie zum Analysieren der Sicherheit, Integrität, Qualität und Leistung genutzt.

Telemetriedaten liefern Azure Stack-Betreibern wertvolle Einblicke in Unternehmensbereitstellungen und ermöglichen die Mitgestaltung zukünftiger Versionen von Azure Stack.

> [!NOTE]
> Azure Stack kann auch für die Weiterleitung abrechnungsrelevanter Nutzungsinformationen an Azure konfiguriert werden. Dies ist für Azure Stack-Kunden mit mehreren Knoten erforderlich, die sich für eine nutzungsbasierte Bezahlung entscheiden. Verwendungsberichte werden unabhängig von Telemetriedaten gesteuert und sind für Kunden mit mehreren Knoten, die sich für das Kapazitätsmodell entscheiden, sowie für Azure Stack Development Kit-Benutzer nicht erforderlich. Für diese Szenarien können Verwendungsberichte [mithilfe des Registrierungsskripts](../operator/azure-stack-usage-reporting.md) deaktiviert werden.

Azure Stack-Telemetriedaten basieren auf der Komponente „Benutzererfahrung und Telemetrie im verbundenen Modus“ von Windows Server 2016. Diese verwendet die Ablaufprotokollierungstechnologie [Ereignisablaufverfolgung für Windows (Event Tracing for Windows, ETW)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) zur Erfassung und Speicherung von Telemetrieereignissen und -daten. Die gleiche Protokollierungstechnologie wird von Azure Stack-Komponenten zum Veröffentlichen von Ereignissen und Daten verwendet, die mithilfe der öffentlichen APIs für die Protokollierung und Nachverfolgung von Betriebssystemereignissen erfasst werden. Beispiele für Azure Stack-Komponenten sind etwa Netzwerkressourcenanbieter, Speicherressourcenanbieter, Überwachungsressourcenanbieter und Aktualisierungsressourcenanbieter. Die Komponente „Benutzererfahrung und Telemetrie im verbundenen Modus“ verschlüsselt Daten per SSL und nutzt das Anheften von Zertifikaten, um Telemetriedaten über HTTPS an den Datenverwaltungsdienst von Microsoft zu übertragen.

> [!NOTE]
> Zur Unterstützung des Telemetriedatenflusses muss Port 443 (HTTPS) in Ihrem Netzwerk geöffnet sein. Die Komponente „Benutzererfahrung und Telemetrie im verbundenen Modus“ stellt unter https://v10.vortex-win.data.microsoft.com eine Verbindung mit dem Datenverwaltungsdienst von Microsoft her. Außerdem stellt die Komponente „Benutzererfahrung und Telemetrie im verbundenen Modus“ eine Verbindung mit https://settings-win.data.microsoft.com her, um Konfigurationsinformationen herunterzuladen.

## <a name="privacy-considerations"></a>Datenschutzaspekte
Der ETW-Dienst leitet Telemetriedaten an geschützten Cloudspeicher zurück. Der Zugriff auf Telemetriedaten beruht auf dem Prinzip der geringsten Rechte. Auf die Telemetriedaten können nur Microsoft-Mitarbeiter zugreifen, die aus geschäftlichen Gründen unbedingt darauf zugreifen müssen. Microsoft gibt keine persönlichen Daten von Kunden an Dritte weiter – es sei denn, dies geschieht auf Wunsch des Kunden oder im eingeschränkten Rahmen der [Datenschutzbestimmungen für Azure Stack](https://privacy.microsoft.com/PrivacyStatement). Wir geben Geschäftsberichte mit aggregierten, anonymisierten Telemetrieinformationen an OEMs und Partner weiter. Entscheidungen zur Weitergabe von Daten werden von einem internen Microsoft-Team getroffen, das unter anderem Interessenvertreter für die Bereiche Datenschutz, Recht und Datenverwaltung umfasst.

Bei Microsoft gilt das Prinzip der Informationsminimierung. Unser Ziel ist es, nur Informationen zu erfassen, die wir benötigen, und diese nur so lange zu speichern, wie dies zur Bereitstellung eines Diensts oder zu Analysezwecken erforderlich ist. Ein Großteil der Informationen zur Funktion des Azure Stack-Systems und der Azure-Dienste wird innerhalb von sechs Monaten gelöscht. Zusammengefasste oder aggregierte Daten werden für einen längeren Zeitraum gespeichert.

Wir sind uns der Bedeutung des Datenschutzes und der Sicherheit der Informationen unserer Kunden bewusst. Azure Stack bietet daher ein durchdachtes und umfassendes Datenschutzkonzept für unsere Kunden. IT-Administratoren können Features und Datenschutzeinstellungen jederzeit anpassen. Wie setzen uns klar für Transparenz und Vertrauen ein:
- Wir informieren Kunden offen darüber, welche Arten von Daten erfasst werden.
- Wir überlassen Unternehmenskunden die Kontrolle und ermöglichen ihnen die Anpassung ihrer eigenen Datenschutzeinstellungen.
- Datenschutz und Sicherheit unserer Kunden haben für uns höchste Priorität.
- Wir informieren transparent über die Verwendung der Telemetriedaten.
- Wir nutzen Telemetriedaten zur Verbesserung der Kundenerfahrung.

Die Erfassung vertraulicher Informationen wie Kreditkartennummern, Benutzernamen und Kennwörtern, E-Mail-Adressen und Ähnlichem ist von Microsoft nicht beabsichtigt. Sollten wir feststellen, dass versehentlich vertrauliche Informationen empfangen wurden, werden diese gelöscht.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Beispiele für die Verwendung der Telemetriedaten durch Microsoft
Telemetriedaten spielen eine wichtige Rolle für die schnelle Erkennung und Behebung kritischer Zuverlässigkeitsprobleme in den Bereitstellungen und Konfigurationen unserer Kunden. Auf der Grundlage der erfassten Telemetriedaten können wir zeitnah Probleme mit Diensten oder Hardwarekonfigurationen ermitteln. Dank der Möglichkeit, diese Daten von Kunden zu erhalten und Verbesserungen innerhalb des Ökosystems voranzutreiben, kann Microsoft bei seinen integrierten Azure Stack-Lösungen ein noch höheres Qualitätsniveau erreichen.

Anhand von Telemetriedaten kann Microsoft auch besser nachvollziehen, wie Kunden Komponenten bereitstellen, Features verwenden und Dienste nutzen, um ihre geschäftlichen Ziele zu erreichen. Auf der Grundlage der gewonnenen Erkenntnisse aus diesen Daten können Entwicklungsinvestitionen in Bereichen priorisiert werden, die sich direkt auf die Erfahrungen und Workloads unserer Kunden auswirken.

Ein Beispiel hierfür wäre etwa die Kundennutzung von Containern, Speicher und Netzwerkkonfigurationen, die Azure Stack-Rollen zugeordnet sind. Wir nutzen die Einblicke auch, um Verbesserungen für einige unserer Verwaltungs- und Überwachungslösungen voranzutreiben und sie intelligenter zu machen. Dadurch können Kunden Qualitätsprobleme besser diagnostizieren und Kosten sparen, da sie seltener beim Support von Microsoft anrufen müssen.

## <a name="manage-telemetry-collection"></a>Verwalten der Erfassung von Telemetriedaten
Wir raten davon ab, die Telemetrie in Ihrer Organisation zu deaktivieren, da die bereitstellten Daten zur Verbesserung der Produktfunktionalität und -stabilität beitragen. Uns ist jedoch bewusst, dass eine Deaktivierung in bestimmten Szenarien erforderlich sein kann.

In einem solchen Fall können Sie die Ebene der an Microsoft gesendeten Telemetriedaten konfigurieren – entweder mithilfe von Registrierungseinstellungen (vor der Bereitstellung) oder mithilfe der Telemetrieendpunkte (nach der Bereitstellung).

### <a name="set-telemetry-level-in-the-windows-registry"></a>Festlegen der Telemetrieebene in der Windows-Registrierung
Mit dem Registrierungs-Editor von Windows kann die Telemetrieebene auf dem physischen Hostcomputer vor der Bereitstellung von Azure Stack manuell festgelegt werden. Ist bereits eine Verwaltungsrichtlinie (beispielsweise eine Gruppenrichtlinie) vorhanden, wird diese Registrierungseinstellung dadurch überschrieben.

Starten Sie das System mit „CloudBuilder.vhdx1“, bevor Sie Azure Stack auf dem Development Kit-Host bereitstellen, und führen Sie das folgende Skript in einem PowerShell-Fenster mit erhöhten Rechten aus:

```powershell
### Get current AllowTelemetry value on DVM Host
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
### Set & Get updated AllowTelemetry value for ASDK-Host
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name "AllowTelemetry" -Value '0' # Set this value to 0,1,2,or3.  
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
```

Die Telemetrieebenen sind kumulativ und in vier Ebenen (0–3) unterteilt:

**0 (Sicherheit)** . Nur Sicherheitsdaten. Informationen, die zur Gewährleistung der Betriebssystemsicherheit benötigt werden – beispielsweise Daten zu den Einstellungen der Komponente „Benutzererfahrung und Telemetrie im verbundenen Modus“ und Windows Defender-Daten. Auf dieser Ebene werden keine Azure Stack-spezifischen Telemetriedaten ausgegeben.

**1 (Standard)** . Sicherheitsdaten sowie grundlegende Integritäts- und Qualitätsdaten. Grundlegende Geräteinformationen wie qualitätsbezogene Daten, App-Kompatibilität, App-Nutzungsdaten und Daten der Sicherheitsebene. Wenn Sie die Telemetrieebene auf „Standard“ festlegen, wird die Azure Stack-Telemetrie aktiviert. Auf dieser Ebene werden unter anderem folgende Ebene erfasst:

- **Grundlegende Geräteinformationen** zum Nachvollziehen der Arten und Konfigurationen nativer und virtualisierter Windows Server 2016-Instanzen im Ökosystem. Hierzu zählen unter anderem folgende Informationen:
  - Computerattribute wie OEM und Modell
  - Netzwerkattribute wie Anzahl und Geschwindigkeit von Netzwerkkarten
  - Prozessor- und Arbeitsspeicherattribute wie Anzahl von Kernen und Arbeitsspeichergröße
  - Speicherattribute wie Laufwerkanzahl, Art und Größe
- **Telemetriefunktion** einschließlich hochgeladener Ereignisse (in Prozent), gelöschter Ereignisse und Zeitpunkt des letzten Uploads
- **Qualitätsbezogene Informationen**, die Microsoft einen grundlegenden Einblick in die Leistung von Azure Stack geben. Ein Beispiel wäre etwa die Anzahl kritischer Warnungen für eine bestimmte Hardwarekonfiguration.
- **Kompatibilitätsdaten**, die Aufschluss darüber geben, welche Ressourcenanbieter in einem System und auf einem virtuellen Computer installiert sind und die Identifizierung möglicher Kompatibilitätsprobleme ermöglichen.

**2 (Erweitert)** . Zusätzliche Informationen, etwa zur Verwendung und Leistung des Betriebssystems und anderer Azure Stack-Dienste, sowie erweiterte Zuverlässigkeitsdaten und Daten der Ebenen „Standard“ und „Sicherheit“.

**3 (Vollständig)** . Alle Daten, die zum Identifizieren und Beheben von Problemen benötigt werden oder dabei hilfreich sind, sowie Daten der Ebenen **Sicherheit**, **Standard**, und **Erweitert**.

> [!NOTE]
> Standardmäßig wird die erweiterte Telemetrieebene (2) verwendet.

Wenn Sie die Windows- und die Azure Stack-Telemetrie deaktivieren, wird die SQL-Telemetrie deaktiviert. Weitere Informationen zu den Auswirkungen der Windows Server-Telemetrieeinstellungen finden Sie im [Whitepaper zur Windows-Telemetrie](https://aka.ms/winservtelemetry).

> [!IMPORTANT]
> Diese Telemetrieebenen gelten nur für Microsoft Azure Stack-Komponenten. Microsoft-fremde Softwarekomponenten und Dienste, die auf dem Hardwarelebenszyklushost von Azure Stack-Hardwarepartnern ausgeführt werden, kommunizieren möglicherweise außerhalb dieser Telemetrieebenen mit den dazugehörigen Clouddiensten. Wenden Sie sich an den Anbieter Ihrer Azure Stack-Hardwarelösung, um sich über dessen Telemetrierichtlinie sowie über Ihre Verwendungsoptionen zu informieren.

### <a name="enable-or-disable-telemetry-after-deployment"></a>Aktivieren oder Deaktivieren der Telemetrie nach der Bereitstellung

Wenn Sie die Telemetrie nach der Bereitstellung aktivieren oder deaktivieren möchten, benötigen Sie Zugriff auf den privilegierten Endpunkt (Privileged End Point, PEP), der auf den virtuellen ERCS-Computern verfügbar gemacht wird.
1.  Aktivieren: `Set-Telemetry -Enable`
2.  Deaktivieren: `Set-Telemetry -Disable`

Parameterdetails:
> Parameter „Enable“: Aktiviert das Hochladen von Telemetriedaten.
> 
> Parameter „Disable“: Deaktiviert das Hochladen von Telemetriedaten.  

**Skript zum Aktivieren der Telemetrie:**
```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
$psSession = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred
Invoke-Command -Session $psSession {Set-Telemetry -Enable}
if($psSession)
{
    Remove-PSSession $psSession
}
```

**Skript zum Deaktivieren der Telemetrie:**
```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
$psSession = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred
Invoke-Command -Session $psSession {Set-Telemetry -Disable}
if($psSession)
{
    Remove-PSSession $psSession
}
```

## <a name="next-steps"></a>Nächste Schritte
[Starten und Beenden des ASDK](asdk-start-stop.md)
