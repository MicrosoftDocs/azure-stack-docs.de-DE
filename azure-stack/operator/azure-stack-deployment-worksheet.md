---
title: Arbeitsblatt für die Bereitstellung von integrierten Azure Stack Hub-Systemen
description: Erfahren Sie, wie Sie das Tool für Bereitstellungsarbeitsblätter zum Bereitstellen von Azure Stack Hub installieren und verwenden.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 04/19/2019
ms.author: inhenkel
ms.reviewer: wamota
ms.lastreviewed: 04/19/2019
ms.openlocfilehash: 8c13121f5d591abca8a6c83771848d97cc9106db
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "77700067"
---
# <a name="deployment-worksheet-for-azure-stack-hub-integrated-systems"></a>Arbeitsblatt für die Bereitstellung von integrierten Azure Stack Hub-Systemen

Das Arbeitsblatt für die Azure Stack Hub-Bereitstellung ist eine Windows Forms-Anwendung, mit der alle erforderlichen Bereitstellungsinformationen und -entscheidungen an einem zentralen Ort zusammengefasst werden. Sie können das Arbeitsblatt für die Bereitstellung während der Planung ausfüllen und dann vor Beginn der Bereitstellung prüfen.

Bei den für das Arbeitsblatt erforderlichen Informationen geht es um die Bereiche Netzwerk, Sicherheit und Identität. Es müssen wichtige Entscheidungen getroffen werden, für die ggf. Wissen in vielen unterschiedlichen Bereichen benötigt wird. Aus diesem Grund kann es ratsam sein, Teams mit Wissen in diesen Bereichen zurate zu ziehen, um das Arbeitsblatt auszufüllen.

Beim Ausfüllen des Arbeitsblatts müssen Sie unter Umständen vor der Bereitstellung einige Konfigurationsänderungen an Ihrer Netzwerkumgebung vornehmen. Dies kann die Reservierung von IP-Adressräumen für die Azure Stack Hub-Lösung sowie die Konfiguration von Routern, Switches und Firewalls zur Vorbereitung der Konnektivität mit der neuen Azure Stack Hub-Lösung umfassen.

> [!NOTE]
> Weitere Informationen zum Ausfüllen des Tools für Bereitstellungsarbeitsblätter finden Sie in [diesem Artikel in der Azure Stack Hub-Dokumentation](azure-stack-datacenter-integration.md).

[![Arbeitsblatt für die Bereitstellung](media/azure-stack-deployment-worksheet/depworksheet.png "Arbeitsblatt für die Bereitstellung")](media/azure-stack-deployment-worksheet/depworksheet.png)

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

**Import**: Ermöglicht den Import einer Datei mit Azure Stack Hub-Konfigurationsdaten (ConfigurationData.json), die von diesem Tool generiert wurde, oder von Daten, die mit einer vorherigen Version des Arbeitsblatts für die Bereitstellung erstellt wurden. Beim Durchführen eines Imports werden die Formulare zurückgesetzt und alle zuvor eingegebenen Einstellungen oder generierten Daten gelöscht.

**Export**: Dient zum Überprüfen der derzeit eingegebenen Daten in den Formularen, Generieren der IP-Subnetze und -Zuweisungen und Speichern des Inhalts als Konfigurationsdateien im JSON-Format. Sie können diese Dateien dann verwenden, um die Netzwerkkonfiguration zu generieren und Azure Stack Hub zu installieren.

**Generieren**: Dient zum Überprüfen der derzeit eingegebenen Daten und Generieren der Netzwerkübersicht, ohne dass die JSON-Dateien für die Bereitstellung exportiert werden müssen. Zwei neue Registerkarten werden erstellt, wenn der Vorgang **Generieren** erfolgreich ist: **Subnet Summary** (Subnetzzusammenfassung) und **IP Assignments** (IP-Zuweisungen). Sie können die Daten auf diesen Registerkarten analysieren, um sicherzustellen, dass die Netzwerkzuweisungen wie erwartet erfolgen.

**Alles löschen**: Löscht alle Daten, die derzeit in den Formularen als Eingaben vorhanden sind, und führt die Zurücksetzung auf die Standardwerte durch.

**Save or Open your work in-progress** (Arbeit speichern oder öffnen): Sie können teilweise eingegebene Daten speichern und öffnen, während Sie daran arbeiten, indem Sie die Optionen **Datei > Speichern** und **Datei > Öffnen** verwenden. Dies unterscheidet sich von den Optionen **Import** und **Export**, weil dabei alle Daten eingegeben und überprüft worden sein müssen. Bei „Öffnen“/„Speichern“ wird keine Überprüfung durchgeführt, und es müssen nicht in alle Felder Daten eingegeben worden sein, um Ihre Arbeit speichern zu können.

**Logging and Warning messages** (Protokollierung und Warnmeldungen): Während der Verwendung des Formulars werden im PowerShell-Fenster unter Umständen nicht kritische Warnmeldungen angezeigt. Kritische Fehler werden als Popupmeldung angezeigt. Die optionale ausführliche Protokollierung, wobei auch ein Protokoll auf Datenträger geschrieben wird, kann als Hilfe bei der Behandlung von Problemen aktiviert werden.

Starten Sie das Tool mit ausführlicher Protokollierung wie folgt:

   ```PowerShell
   Start-DeploymentWorksheet -EnableLogging
   ```

Sie finden das gespeicherte Protokoll im Verzeichnis **Temp** des aktuellen Benutzers, z. B.: **C:\Users\me\AppData\Local\Temp\Microsoft_AzureStack\DeploymentWorksheet_Log.txt**.

## <a name="next-steps"></a>Nächste Schritte

* [Verbindungsmodelle für die Azure Stack Hub-Bereitstellung](azure-stack-connection-models.md)
