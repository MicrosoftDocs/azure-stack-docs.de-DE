---
title: Konfigurieren der Azure Stack Hub-Telemetrie
titleSuffix: Azure Stack
description: Hier erhalten Sie Informationen zur Azure Stack Hub-Telemetrie und erfahren, wie Sie Telemetrieeinstellungen mithilfe von PowerShell konfigurieren.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 1/16/2020
ms.author: mabrigg
ms.reviewer: comartin
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: c16987ac19b47eb4d0fedb147522703b98c8792e
ms.sourcegitcommit: ba2fd47e74adebe1fcbb489d059a2c27d59b179c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/17/2020
ms.locfileid: "76256358"
---
# <a name="configure-azure-stack-hub-telemetry"></a>Konfigurieren der Azure Stack Hub-Telemetrie

Die Azure Stack Hub-Telemetrie lädt über „Benutzererfahrung und Telemetrie im verbundenen Modus“ automatisch Systemdaten an Microsoft hoch. Microsoft-Teams verwenden die von der Azure Stack Hub-Telemetrie erfassten Daten, um die Benutzerfreundlichkeit zu verbessern. Die Daten werden auch für die Analyse von Sicherheit, Integrität, Qualität und Leistung verwendet.

Telemetriedaten liefern Azure Stack Hub-Operatoren wertvolle Erkenntnisse zu Unternehmensbereitstellungen und ermöglichen die Mitgestaltung zukünftiger Versionen von Azure Stack Hub.

> [!NOTE]
> Azure Stack Hub kann auch für die Weiterleitung abrechnungsrelevanter Nutzungsinformationen an Azure konfiguriert werden. Dies ist für Azure Stack Hub-Kunden mit mehreren Knoten erforderlich, die sich für nutzungsbasierte Bezahlung entscheiden. Nutzungsberichte werden unabhängig von Telemetriedaten gesteuert und sind für Kunden mit mehreren Knoten, die sich für das Kapazitätsmodell entscheiden, sowie für Azure Stack Development Kit-Benutzer nicht erforderlich. Für diese Szenarien können Verwendungsberichte [mithilfe des Registrierungsskripts](azure-stack-usage-reporting.md) deaktiviert werden.

Die Azure Stack Hub-Telemetrie basiert auf der Windows Server 2016-Komponente „Benutzererfahrung und Telemetrie im verbundenen Modus“. Diese Komponente verwendet die TraceLogging-Technologie [Ereignisablaufverfolgung für Windows (Event Tracing for Windows, ETW)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx), um Ereignisse und Daten zu erfassen und zu speichern. Die gleiche Technologie wird von Azure Stack-Komponenten zum Veröffentlichen von Ereignissen und Daten verwendet, die mithilfe der öffentlichen APIs für die Protokollierung und Nachverfolgung von Betriebssystemereignissen erfasst werden. Zu diesen Azure Stack Hub-Komponenten zählen beispielsweise folgende Anbieter: Netzwerkressource, Speicherressource, Überwachungsressource und Aktualisierungsressource. Die Komponente „Benutzererfahrung und Telemetrie im verbundenen Modus“ verschlüsselt Daten per SSL und nutzt das Anheften von Zertifikaten, um Daten über HTTPS an den Datenverwaltungsdienst von Microsoft zu übertragen.

> [!IMPORTANT]
> Zur Ermöglichung des Telemetriedatenflusses muss der Port 443 (HTTPS) in Ihrem Netzwerk geöffnet sein. Die Komponente „Benutzererfahrung und Telemetrie im verbundenen Modus“ stellt unter `https://v10.events.data.microsoft.com` eine Verbindung mit dem Datenverwaltungsdienst von Microsoft her. Außerdem stellt die Komponente „Benutzererfahrung und Telemetrie im verbundenen Modus“ eine Verbindung mit `https://settings-win.data.microsoft.com` her, um Konfigurationsinformationen herunterzuladen. Andere Diagnosedatendienste stellen für die Fehlerberichterstattung eine Verbindung mit `https://watson.telemetry.microsoft.com` her.

## <a name="privacy-considerations"></a>Datenschutzaspekte

Der ETW-Dienst leitet Telemetriedaten an geschützten Cloudspeicher zurück. Der Zugriff auf Telemetriedaten beruht auf dem Prinzip der geringsten Rechte. Auf die Telemetriedaten können nur Microsoft-Mitarbeiter zugreifen, die aus geschäftlichen Gründen unbedingt darauf zugreifen müssen. Microsoft gibt keine persönlichen Kundendaten an Dritte weiter – es sei denn, dies geschieht auf Wunsch des Kunden oder im eingeschränkten Rahmen der [Datenschutzerklärung von Microsoft](https://privacy.microsoft.com/PrivacyStatement). An OEMs und Partner weitergegebene Geschäftsberichte enthalten aggregierte, anonymisierte Daten. Entscheidungen zur Weitergabe von Daten werden von einem internen Microsoft-Team getroffen, das unter anderem Interessenvertreter für die Bereiche Datenschutz, Recht und Datenverwaltung umfasst.

Bei Microsoft gilt das Prinzip der Informationsminimierung. Unser Ziel ist es, nur Informationen zu erfassen, die benötigt werden, und diese nur so lange zu speichern, wie dies zur Bereitstellung eines Diensts oder zu Analysezwecken erforderlich ist. Ein Großteil der Informationen zur Funktionsweise des Azure Stack Hub-Systems und der Azure-Dienste wird innerhalb von sechs Monaten gelöscht. Zusammengefasste oder aggregierte Daten werden für einen längeren Zeitraum gespeichert.

Wir sind uns der Bedeutung des Datenschutzes und der Sicherheit der Kundeninformationen bewusst.  Microsoft setzt daher ein durchdachtes und umfassendes Konzept ein, um den Schutz der Privatsphäre und der Kundendaten in Azure Stack Hub zu gewährleisten. IT-Administratoren können Features und Datenschutzeinstellungen jederzeit anpassen. Wie setzen uns klar für Transparenz und Vertrauen ein:

- Wir informieren Kunden offen darüber, welche Arten von Daten erfasst werden.
- Wir überlassen Unternehmenskunden die Kontrolle und ermöglichen ihnen die Anpassung ihrer eigenen Datenschutzeinstellungen.
- Datenschutz und Sicherheit unserer Kunden haben für uns höchste Priorität.
- Wir informieren transparent über die Verwendung der Telemetriedaten.
- Wir nutzen Telemetriedaten zur Verbesserung der Kundenerfahrung.

Microsoft beabsichtigt nicht, vertrauliche Informationen wie Kreditkartennummern, Benutzernamen und Kennwörter, E-Mail-Adressen und Ähnliches zu erfassen. Sollten wir feststellen, dass versehentlich vertrauliche Informationen empfangen wurden, werden diese gelöscht.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Beispiele für die Verwendung der Telemetriedaten durch Microsoft

Telemetriedaten spielen eine wichtige Rolle für die schnelle Erkennung und Behebung kritischer Zuverlässigkeitsprobleme in den Bereitstellungen und Konfigurationen von Kunden. Auf der Grundlage von Telemetriedaten können wir Probleme mit Diensten oder Hardwarekonfigurationen erkennen. Dank der Möglichkeit, diese Daten von Kunden zu erhalten und Verbesserungen innerhalb des Ökosystems voranzutreiben, sorgt Microsoft bei integrierten Azure Stack Hub-Lösungen für ein noch höheres Qualitätsniveau.

Anhand von Telemetriedaten kann Microsoft auch besser nachvollziehen, wie Kunden Komponenten bereitstellen, Features verwenden und Dienste nutzen, um ihre geschäftlichen Ziele zu erreichen. Auf der Grundlage der gewonnenen Erkenntnisse können Entwicklungsinvestitionen in Bereichen priorisiert werden, die sich direkt auf die Erfahrungen und Workloads von Kunden auswirken.

Beispiel hierfür sind etwa die Kundennutzung von Containern, Speicher und Netzwerkkonfigurationen, die mit Azure Stack Hub-Rollen verknüpft sind. Wir nutzen die Erkenntnisse auch, um Verbesserungen bei Verwaltungs- und Überwachungslösungen für Azure Stack Hub voranzutreiben und intelligente Funktionen zu ermöglichen. Dank dieser Verbesserungen können Kunden Probleme einfacher diagnostizieren und Kosten sparen, da sie seltener beim Support von Microsoft anrufen müssen.

## <a name="manage-telemetry-collection"></a>Verwalten der Erfassung von Telemetriedaten

Grundsätzlich raten wir davon ab, Telemetriedaten in Ihrer Organisation zu deaktivieren. In bestimmen Szenarien kann eine Deaktivierung jedoch erforderlich sein.

In einem solchen Szenario können Sie die Ebene der an Microsoft gesendeten Telemetriedaten konfigurieren – entweder mithilfe von Registrierungseinstellungen (vor der Bereitstellung von Azure Stack Hub) oder mithilfe der Telemetrieendpunkte (nach der Bereitstellung von Azure Stack Hub).

### <a name="telemetry-levels-and-data-collection"></a>Telemetrieebenen und Datenerfassung

Bevor Sie Telemetrieeinstellungen ändern, sollten Sie sich mit den Telemetriestufen vertraut machen und sich darüber informieren, welche Daten auf den einzelnen Stufen jeweils gesammelt werden.

Die Telemetrieeinstellungen sind in vier Ebenen (0-3) gruppiert. Diese Ebenen sind kumulativ und wie folgt kategorisiert:

**0 (Sicherheit)**</br>
Nur Sicherheitsdaten. Informationen, die erforderlich sind, um den Schutz des Betriebssystems zu gewährleisten. Dies schließt Daten zu den Einstellungen der Komponente „Benutzererfahrung und Telemetrie im verbundenen Modus“ und zu den Einstellungen von Windows Defender ein. Auf dieser Ebene werden keine Azure Stack Hub-spezifischen Telemetriedaten ausgegeben.

**1 (Standard)**</br>
Sicherheitsdaten sowie grundlegende Integritäts- und Qualitätsdaten. Grundlegende Geräteinformationen wie qualitätsbezogene Daten, App-Kompatibilität, App-Nutzungsdaten und Daten der Ebene **Sicherheit**. Wenn Sie die Telemetrieebene auf „Standard“ festlegen, wird die Azure Stack Hub-Telemetrie aktiviert. Auf dieser Ebene werden unter anderem folgende Ebene erfasst:

- *Grundlegende Geräteinformationen* zum Nachvollziehen der Arten und Konfigurationen nativer und virtueller Windows Server 2016-Instanzen im Ökosystem. Dies schließt Folgendes ein:

  - Computerattribute wie OEM und Modell
  - Netzwerkattribute wie Anzahl und Geschwindigkeit von Netzwerkkarten
  - Prozessor- und Arbeitsspeicherattribute wie Anzahl von Kernen und Größe des installierten Arbeitsspeichers
  - Speicherattribute wie Laufwerkanzahl, Laufwerktyp und Laufwerkgröße

- *Telemetriefunktion* einschließlich hochgeladener Ereignisse (in Prozent), gelöschter Ereignisse und Zeitpunkt des letzten Datenuploads
- *Qualitätsbezogene Informationen*, die Microsoft einen grundlegenden Einblick in die Leistung von Azure Stack Hub geben. Ein Beispiel wäre etwa die Anzahl kritischer Warnungen für eine bestimmte Hardwarekonfiguration.
- *Kompatibilitätsdaten*, die Aufschluss darüber geben, welche Ressourcenanbieter in einem System und auf einem virtuellen Computer installiert sind. Dadurch lassen sich mögliche Kompatibilitätsprobleme erkennen.

**2 (Erweitert)**</br>
Zusätzliche Informationen, etwa zur Verwendung und Leistung des Betriebssystems und der Azure Stack Hub-Dienste, sowie erweiterte Zuverlässigkeitsdaten und Daten der Ebenen **Sicherheit** und **Standard**.

> [!NOTE]
> Dies ist die Standardeinstellung für Telemetriedaten.

**3 (Vollständig)**</br>
Alle Daten, die zum Identifizieren und Beheben von Problemen benötigt werden oder dabei hilfreich sind, sowie Daten der Ebenen **Sicherheit**, **Standard**, und **Erweitert**.

> [!IMPORTANT]
> Diese Telemetrieebenen gelten nur für Microsoft Azure Stack Hub-Komponenten. Microsoft-fremde Softwarekomponenten und Dienste, die auf dem Hardwarelebenszyklushost von Azure Stack Hub-Hardwarepartnern ausgeführt werden, kommunizieren möglicherweise außerhalb dieser Telemetrieebenen mit den dazugehörigen Clouddiensten. Wenden Sie sich an den Anbieter Ihrer Azure Stack Hub-Hardwarelösung, um sich über dessen Telemetrierichtlinie sowie über Ihre Verwendungsoptionen zu informieren.

Wenn Sie die Windows- und die Azure Stack Hub-Telemetrie deaktivieren, wird auch die SQL-Telemetrie deaktiviert. Weitere Informationen zu den Auswirkungen der Windows Server-Telemetrieeinstellungen finden Sie im [Whitepaper zur Windows-Telemetrie](https://aka.ms/winservtelemetry).

### <a name="asdk-set-the-telemetry-level-in-the-windows-registry"></a>ASDK: Festlegen der Telemetrieebene in der Windows-Registrierung

Mit dem Registrierungs-Editor von Windows können Sie die Telemetrieebene auf dem physischen Hostcomputer manuell festlegen, bevor Sie Azure Stack Hub bereitstellen. Ist bereits eine Verwaltungsrichtlinie (beispielsweise eine Gruppenrichtlinie) vorhanden, wird diese Registrierungseinstellung dadurch überschrieben.

Bevor Sie Azure Stack Hub auf dem Development Kit-Host ausführen, starten Sie das System mit „CloudBuilder.vhdx1“, und führen Sie in einem PowerShell-Fenster mit erhöhten Rechten das folgende Skript aus:

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

### <a name="asdk-and-multi-node-enable-or-disable-telemetry-after-deployment"></a>ASDK und mehrere Knoten: Aktivieren oder Deaktivieren der Telemetrie nach der Bereitstellung

Wenn Sie die Telemetrie nach der Bereitstellung aktivieren oder deaktivieren möchten, benötigen Sie Zugriff auf den privilegierten Endpunkt (Privileged End Point, PEP), der auf den virtuellen ERCS-Computern verfügbar gemacht wird.

- Aktivieren: `Set-Telemetry -Enable`
- Deaktivieren: `Set-Telemetry -Disable`

Parameterdetails:
- `.PARAMETER Enable`: Hochladen der Telemetriedaten aktivieren
- `.PARAMETER Disable`: Hochladen der Telemetriedaten deaktivieren  

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

[Registrieren von Azure Stack Hub in Azure](azure-stack-registration.md)