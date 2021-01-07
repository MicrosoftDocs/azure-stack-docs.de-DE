---
title: Lebenszyklusverwaltung der MDC-Netzwerkkonfiguration
description: Erfahren Sie mehr über die Lebenszyklusverwaltung der Netzwerkkonfiguration für Azure Modular Data Center.
author: PatAltimore
ms.author: patricka
ms.service: azure-stack
ms.topic: conceptual
ms.date: 01/02/2020
ms.lastreviewed: 01/02/2020
ms.openlocfilehash: 5453738374f9377645a6ba594dda08b8668931fe
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97872329"
---
# <a name="configuration-lifecycle-management"></a>Lebenszyklusverwaltung der Konfiguration

Dieser Artikel behandelt die Lebenszyklusverwaltung der Netzwerkkonfiguration für Azure Modular Data Center.

## <a name="update-configuration"></a>Aktualisieren der Konfiguration

Diese Methode zum Aktualisieren von Switchkonfigurationen im Außeneinsatz gilt für alle Typen von Konfigurationsupdates. Aufgrund vieler unbekannter Faktoren, die sich unserer Kontrolle entziehen, z. B. bei manuellen Konfigurationen durch den Kunden oder OEM, umfasst dieser manuelle Vorgang mehrere Schritte. Zu diesem Zeitpunkt gibt es keine Garantie für Uptime. Updates sollten während eines Wartungsfensters durchgeführt werden.

1. Erstellen Sie eine Sicherung der aktuellen Konfigurationsdateien für die Switches „edge1“, „edge2“, „tor1“, „tor2“ und „BMC“. Kopieren Sie diese Dateien vom Switch.

1. Notieren Sie sich die Buildnummer des Toolkits der derzeitigen Konfiguration. Diese Nummer sollte bei allen Konfigurationen im Banner **motd** enthalten sein. Suchen Sie nach **BUILDNUMBER**.

1. Generieren Sie die anfänglichen Konfigurationen mit der Toolkitversion aus Schritt 2 neu.

1. Laden Sie die Konfigurationen aus Schritt 3 auf die jeweiligen Switches. Ziel dieser Aktion ist die *Übertragung* der von unseren Tools generierten Konfiguration von einem Switch, um eine Baseline zu schaffen. Diese Aktion kann vom OEM auf separater Hardware durchgeführt werden, z. B. im Testbereich des OEM oder vor Ort beim Kunden.

1. Erstellen Sie eine Sicherung der Konfigurationsdatei aus Schritt 4. Kopieren Sie sie an einen Remotespeicherort.

1. Vergleichen Sie mit einem Vergleichstool Ihrer Wahl die Sicherung der aktuellen Konfiguration aus Schritt 1 mit der Sicherung der Baselinekonfiguration aus Schritt 5. Notieren oder kopieren Sie alle Unterschiede, die pro Gerät in die aktualisierte Switchkonfiguration übernommen werden sollen.

1. Führen Sie das neue Toolkit aus, mit dem die aktualisierten Switchkonfigurationen generiert werden.

1. Führen Sie die Unterschiede aus Schritt 6 mit den neuen Switchkonfigurationen zusammen.

1. Laden Sie die neuen Konfigurationen auf die jeweiligen Switches. Führen Sie die Befehle zur Nachprüfung aus, die im Ausgabeverzeichnis des Tools bereitgestellt werden.

1. Speichern Sie die Konfigurationen.

## <a name="ntp"></a>NTP

Sie können den privilegierten Endpunkt (PEP) verwenden, um den Zeitserver in Azure Stack zu aktualisieren. Verwenden Sie einen Hostnamen, der in mindestens zwei IP-Adressen von NTP-Servern (Network Time Protocol) aufgelöst wird.

Azure Stack verwendet NTP zum Herstellen einer Verbindung mit Zeitservern im Internet. NTP-Server stellen eine genaue Systemzeit bereit. Die Uhrzeit wird auf physischen Netzwerkswitches, auf dem Hardwarelebenszyklus-Host, im Infrastrukturdienst und auf virtuellen Computern von Azure Stack verwendet. Wird die Uhr nicht synchronisiert, treten in Azure Stack unter Umständen schwerwiegende Netzwerk- und Authentifizierungsprobleme auf. Protokolldateien, Dokumente und andere Dateien werden möglicherweise mit falschen Zeitstempeln erstellt.

Die Angabe eines Zeitservers (NTP) ist erforderlich, damit Azure Stack die Uhrzeit synchronisieren kann. Bei der Bereitstellung von Azure Stack geben Sie die Adresse eines NTP-Servers an. Die Uhrzeit ist ein kritischer Rechenzentrumsinfrastruktur-Dienst. Ändert sich der Dienst, müssen Sie die Uhrzeit aktualisieren.

Azure Stack unterstützt die Synchronisierungs der Uhrzeit nur mit einem Zeitserver (NTP). Sie können nicht mehrere NTPs bereitstellen, mit denen Azure Stack die Uhrzeit synchronisiert. Wir empfehlen, dass Sie einen DNS-Eintrag (Domain Name System) einrichten, der in mehrere NTP-Server aufgelöst wird.

### <a name="update-ntp-post-deployment"></a>Aktualisieren von NTP nach der Bereitstellung

1. Stellen Sie eine Verbindung mit dem PEP her. Zum Entsperren des privilegierten Endpunkts müssen Sie kein Supportticket erstellen.

1. Führen Sie den folgenden Befehl aus, um den aktuellen konfigurierten NTP-Server zu überprüfen.

    ```powershell
    Get-AzsTimeSource
    ```

1. Führen Sie den folgenden Befehl zum Aktualisieren von Azure Stack aus, um den neuen NTP-Server zu verwenden und sofort die Uhrzeit zu synchronisieren.

    ```powershell
    Set-AzsTimeSource -Timeserver NEWTIMESERVER -resync
    ```

>[!NOTE]
>Mit diesem Verfahren wird nicht der Zeitserver auf den physischen Switches aktualisiert.

## <a name="dns"></a>DNS

In diesem Abschnitt wird erläutert, wie Sie DNS-Weiterleitungen aktualisieren, um externe Namen aufzulösen.

### <a name="update-the-dns-forwarder-in-azure-stack"></a>Aktualisieren der DNS-Weiterleitung in Azure Stack

Mindestens eine erreichbare DNS-Weiterleitung ist erforderlich, damit die Azure Stack-Infrastruktur externe Namen auflösen kann. Für die Bereitstellung von Azure Stack muss eine DNS-Weiterleitung verfügbar gemacht werden. Diese Eingabe wird als Weiterleitung für die internen Azure Stack-DNS-Server verwendet und ermöglicht die externe Namensauflösung für Dienste wie Authentifizierung, Marketplace-Verwaltung oder Nutzung.

DNS ist ein wichtiger Rechenzentrumsinfrastruktur-Dienst, der sich ändern kann. Wenn er sich ändert, muss Azure Stack aktualisiert werden.

In diesem Artikel wird beschrieben, wie Sie den privilegierten Endpunkt (PEP) zum Aktualisieren der DNS-Weiterleitung in Azure Stack verwenden. Es wird empfohlen, zwei zuverlässige IP-Adressen für die DNS-Weiterleitung zu verwenden.

1. Stellen Sie eine Verbindung mit dem PEP her. Es ist nicht erforderlich, den PEP zu entsperren, indem Sie ein Supportticket erstellen.

1. Führen Sie den folgenden Befehl aus, um die aktuelle konfigurierte DNS-Weiterleitung zu überprüfen. Als Alternative können Sie auch die Regionseigenschaften des Verwaltungsportals verwenden.

    ```powershell
    Get-AzsDnsForwarder 
    ```

1. Führen Sie den folgenden Befehl aus, um Azure Stack zu aktualisieren und die neue DNS-Weiterleitung zu verwenden.

    ```powershell
    Set-AzsDnsForwarder -IPAddress "IPAddress 1", "IPAddress 2"
    ```

1. Überprüfen Sie die Ausgabe des Befehls auf Fehler.
