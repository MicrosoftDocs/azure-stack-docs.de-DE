---
title: Bereitstellungsarbeitsblatt für Azure Stack Hub
description: Erfahren Sie, wie Sie das Tool für Bereitstellungsarbeitsblätter zum Bereitstellen von Azure Stack Hub installieren und verwenden.
author: PatAltimore
ms.topic: article
ms.date: 04/19/2019
ms.author: patricka
ms.reviewer: wamota
ms.lastreviewed: 04/19/2019
ms.openlocfilehash: afdad104cad67e928e1d6d988138ab1c8555631c
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97870884"
---
# <a name="deployment-worksheet-for-azure-stack-hub-integrated-systems"></a>Arbeitsblatt für die Bereitstellung von integrierten Azure Stack Hub-Systemen

Das Arbeitsblatt für die Azure Stack Hub-Bereitstellung ist eine Windows Forms-App, mit der alle erforderlichen Bereitstellungsinformationen und -entscheidungen an einem zentralen Ort zusammengefasst werden. Sie können das Arbeitsblatt für die Bereitstellung während der Planung ausfüllen und dann vor Beginn der Bereitstellung prüfen.

Bei den für das Arbeitsblatt erforderlichen Informationen geht es um die Bereiche Netzwerk, Sicherheit und Identität. Diese Informationen erfordern möglicherweise spezifische Kenntnisse in bestimmten Bereichen, daher sollten Sie sich an Experten beraten, um das Arbeitsblatt abzuschließen.

Beim Ausfüllen des Arbeitsblatts müssen Sie unter Umständen vor der Bereitstellung einige Konfigurationsänderungen an Ihrer Netzwerkumgebung vornehmen. Diese Änderungen können die Reservierung von IP-Adressräumen für die Azure Stack Hub-Lösung sowie die Konfiguration von Routern, Switches und Firewalls zur Vorbereitung der Konnektivität mit der neuen Azure Stack Hub-Lösung umfassen.

> [!NOTE]
> Weitere Informationen zum Ausfüllen des Arbeitsblatttools für die Bereitstellung finden Sie unter [Überlegungen zur Planung der Rechenzentrumsintegration für in Azure Stack Hub integrierte Systeme](azure-stack-datacenter-integration.md).

[![Bereitstellungsarbeitsblatt für die Azure Stack Hub-Bereitstellung](media/azure-stack-deployment-worksheet/depworksheet.png "Arbeitsblatt für die Bereitstellung")](media/azure-stack-deployment-worksheet/depworksheet.png)

## <a name="installing-the-windows-powershell-module"></a>Installieren des Windows PowerShell-Moduls

Für jede Version des Arbeitsblatts für die Bereitstellung müssen Sie eine einmalige Installation eines PowerShell-Moduls für jeden Computer ausführen, auf dem das Arbeitsblatt für die Bereitstellung verwendet werden soll.

> [!NOTE]  
> Der Computer muss über eine Internetverbindung verfügen, damit diese Methode funktioniert.

1. Öffnen Sie eine PowerShell-Eingabeaufforderung mit erhöhten Rechten.

2. Installieren Sie im PowerShell-Fenster das Modul über den [PowerShell-Katalog](https://www.powershellgallery.com/packages/Azs.Deployment.Worksheet/):

   ```PowerShell
   Install-Module -Name Azs.Deployment.Worksheet -Repository PSGallery
   ```

Drücken Sie **Y**, um die Installation fortzusetzen, falls Sie eine Meldung zur Installation über ein nicht vertrauenswürdiges Repository erhalten.

## <a name="use-the-deployment-worksheet-tool"></a>Verwenden des Tools „Arbeitsblatt für die Bereitstellung“

Führen Sie die folgenden Schritte aus, um das Arbeitsblatt für die Bereitstellung auf einem Computer zu starten und zu nutzen, auf dem Sie das PowerShell-Modul für das Arbeitsblatt für die Bereitstellung installiert haben:

1. Starten Sie Windows PowerShell (verwenden Sie nicht die PowerShell ISE, da es hierbei zu unerwarteten Ergebnissen kommen kann). Es ist nicht erforderlich, PowerShell als Administrator auszuführen.

2. Importieren Sie das PowerShell-Modul **AzS.Deployment.Worksheet**:

   ```PowerShell
   Import-Module AzS.Deployment.Worksheet
   ```

3. Starten Sie das Arbeitsblatt für die Bereitstellung, nachdem der Import des Moduls abgeschlossen ist:

   ```PowerShell
   Start-DeploymentWorksheet
   ```

Das Arbeitsblatt für die Bereitstellung besteht aus separaten Registerkarten zum Sammeln von Umgebungseinstellungen, z. B. **Kundeneinstellungen**, **Netzwerkeinstellungen** und **Skalierungseinheit #** . Sie müssen alle Werte (Ausnahme: Kennzeichnung **Optional**) auf allen Registerkarten angeben, bevor Dateien mit Konfigurationsdaten generiert werden können. Nachdem alle erforderlichen Werte in das Tool eingegeben wurden, können Sie über das Menü **Aktion** die Optionen **Import**, **Export** und **Generieren** verwenden. Folgende JSON-Dateien sind für die Bereitstellung erforderlich:

**Import**: Ermöglicht den Import einer Datei mit Azure Stack Hub-Konfigurationsdaten (ConfigurationData.json), die von diesem Tool generiert wurde, oder dieser Dateien, die mit einer vorherigen Version des Arbeitsblatts für die Bereitstellung erstellt wurden. Beim Durchführen eines Imports werden die Formulare zurückgesetzt und alle zuvor eingegebenen Einstellungen oder generierten Daten gelöscht.

**Export**: Dient zum Überprüfen der derzeit eingegebenen Daten in den Formularen, Generieren der IP-Subnetze und -Zuweisungen und Speichern des Inhalts als Konfigurationsdateien im JSON-Format. Sie können diese Dateien dann verwenden, um die Netzwerkkonfiguration zu generieren und Azure Stack Hub zu installieren.

**Generieren**: Dient zum Überprüfen der derzeit eingegebenen Daten und Generieren der Netzwerkübersicht, ohne dass die JSON-Dateien für die Bereitstellung exportiert werden müssen. Zwei neue Registerkarten werden erstellt, wenn der Vorgang **Generieren** erfolgreich ist: **Subnet Summary** (Subnetzzusammenfassung) und **IP Assignments** (IP-Zuweisungen). Sie können die Daten auf diesen Registerkarten analysieren, um sicherzustellen, dass die Netzwerkzuweisungen wie erwartet erfolgen.

**Alles löschen**: Löscht alle Daten, die derzeit in den Formularen als Eingaben vorhanden sind, und führt die Zurücksetzung auf die Standardwerte durch.

**Save or Open your work in-progress** (Arbeit speichern oder öffnen): Sie können teilweise eingegebene Daten speichern und öffnen, während Sie daran arbeiten, indem Sie die Optionen **Datei > Speichern** und **Datei > Öffnen** verwenden. Diese Funktion unterscheidet sich von den Optionen **Import** und **Export**, weil dabei alle Daten eingegeben und überprüft worden sein müssen. Bei „Öffnen“/„Speichern“ wird keine Überprüfung durchgeführt, und es müssen nicht in alle Felder Daten eingegeben worden sein, um Ihre Arbeit speichern zu können.

**Logging and Warning messages** (Protokollierung und Warnmeldungen): Während der Verwendung des Formulars werden im PowerShell-Fenster unter Umständen nicht kritische Warnmeldungen angezeigt. Kritische Fehler werden als Popupmeldung angezeigt. Die optionale ausführliche Protokollierung, wobei auch ein Protokoll auf Datenträger geschrieben wird, kann als Hilfe bei der Behandlung von Problemen aktiviert werden.

Starten Sie das Tool mit ausführlicher Protokollierung wie folgt:

   ```PowerShell
   Start-DeploymentWorksheet -EnableLogging
   ```

Sie finden das gespeicherte Protokoll im Verzeichnis **Temp** des aktuellen Benutzers, z. B.: **C:\Users\me\AppData\Local\Temp\Microsoft_AzureStack\DeploymentWorksheet_Log.txt**.

## <a name="next-steps"></a>Nächste Schritte

* [Verbindungsmodelle für die Azure Stack Hub-Bereitstellung](azure-stack-connection-models.md)
