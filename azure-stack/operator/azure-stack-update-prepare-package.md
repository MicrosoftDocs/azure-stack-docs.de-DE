---
title: Vorbereiten eines Updatepakets in Azure Stack Hub
description: Hier erfahren Sie, wie Sie ein Updatepaket in Azure Stack Hub vorbereiten.
author: sethmanheim
ms.topic: how-to
ms.date: 07/22/2020
ms.author: sethm
ms.lastreviewed: 09/10/2019
ms.reviewer: sranthar
ms.openlocfilehash: ef2813c34bd089ef1bb429ba704e02faa06005be
ms.sourcegitcommit: 16ff77f7157e5b04a8cd401b095f7b71f51d5a11
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2020
ms.locfileid: "86949626"
---
# <a name="prepare-an-azure-stack-hub-update-package"></a>Vorbereiten eines Azure Stack Hub-Updatepakets

Dieser Artikel bietet eine Übersicht über die Vorbereitung von Azure Stack Hub-Updatepaketen, damit diese zum Aktualisieren Ihrer Azure Stack Hub-Umgebung verwendet werden können. Dieser Vorgang umfasst die folgenden Schritte:

- [Herunterladen des Updatepakets](#download-the-update-package)
- [Importieren des Updatepakets in Ihre Azure Stack Hub-Umgebung über das Azure Stack Hub-Administratorportal](#import-and-install-updates)

Auf Systemen, die eine Verbindung mit den automatischen Updateendpunkten herstellen können, werden Azure Stack Hub-Softwareupdates und -Hotfixes automatisch heruntergeladen und vorbereitet. Auf Systemen ohne Konnektivität und für alle Updates von der OEM-Version (Original Equipment Manufacturer, Originalgerätehersteller) muss das Updatepaket wie in diesem Artikel beschrieben vorbereitet werden.  

In der folgenden Tabelle ist angegeben, wann die Updatepakete manuell vorbereitet werden müssen und wann sie automatisch vorbereitet werden.

| Updatetyp | Konnektivität | Erforderliche Aktion |
| --- | --- | --- |
| Azure Stack Hub-Softwareupdates | Verbunden | Die Updates werden automatisch heruntergeladen und vorbereitet, wenn das Update angewandt wird. |
| Azure Stack Hub-Hotfixes | Verbunden | Die Updates werden automatisch heruntergeladen und vorbereitet, wenn das Update angewandt wird. |
| OEM-Paketupdates | Verbunden | Das Updatepaket muss vorbereitet werden. Führen Sie die Schritte in diesem Artikel aus. |
| Azure Stack Hub-Softwareupdates | Verbindung getrennt oder schwach | Das Updatepaket muss vorbereitet werden. Führen Sie die Schritte in diesem Artikel aus. |
| Azure Stack Hub-Hotfixes | Verbindung getrennt oder schwach | Das Updatepaket muss vorbereitet werden. Führen Sie die Schritte in diesem Artikel aus. |
| OEM-Paketupdates | Verbindung getrennt oder schwach | Das Updatepaket muss vorbereitet werden. Führen Sie die Schritte in diesem Artikel aus. |

## <a name="download-the-update-package"></a>Herunterladen des Updates

Das Updatepaket für Azure Stack Hub-Updates und -Hotfixes ist für verbundene Systeme auf dem Blatt „Update“ im Portal verfügbar. Laden Sie das Paket herunter, und verschieben Sie es an einen Speicherort, auf den von Ihrer Azure Stack Hub-Instanz zugegriffen werden kann, wenn Sie ein OEM-Paket aktualisieren oder wenn Sie ein nicht verbundenes System warten. Sie müssen das Paket möglicherweise auch herunterladen und an einen zugänglichen Speicherort hochladen, wenn Sie ein System mit einer Verbindung ausführen, die nur zeitweilig verfügbar ist.

Überprüfen Sie den Paketinhalt. Ein Updatepaket besteht in der Regel aus folgenden Dateien:

- **Einer selbstextrahierenden Datei „\<PackageName>.zip“** . Diese Datei enthält die Nutzlast für das Update.
- **Einer Datei „Metadata.xml“.** Diese Datei enthält wichtige Informationen zum Update wie z. B. Herausgeber, Name, Voraussetzungen, Größe und Supportpfad-URL.

### <a name="automatic-download-and-preparation-for-update-packages"></a>Automatisches Herunterladen und Vorbereiten von Updatepaketen

Azure Stack Hub-Softwareupdates und -Hotfixes werden auf Systemen mit Internetverbindung mit den **Azure Stack Hub-Endpunkten für automatische Updates** (<https://*.azureedge.net> und <https://aka.ms/azurestackautomaticupdate>) automatisch vorbereitet. Weitere Informationen zum Einrichten der Konnektivität mit den **Azure Stack Hub-Endpunkten für automatische Updates** finden Sie in der Beschreibung der Endpunkte für **Patches und Updates** unter [Azure Stack Hub-Firewallintegration](./azure-stack-integrate-endpoints.md#ports-and-urls-outbound).

### <a name="where-to-download-azure-stack-hub-update-packages"></a>Downloadorte von Azure Stack Hub-Updatepaketen

Azure Stack Hub-Updates für [vollständige und Express-Updates](./azure-stack-updates.md#update-package-types) werden auf einem sicheren Azure-Endpunkt gehostet. Bei Verfügbarkeit von Azure Stack Hub-Updates werden diese für Azure Stack Hub-Operatoren mit verbundenen Instanzen [automatisch im Administratorportal angezeigt](#automatic-download-and-preparation-for-update-packages). Für Systeme ohne oder mit schwacher Internetkonnektivität können Updatepakete mit dem [Downloadprogramm für Azure Stack Hub-Updates](https://aka.ms/azurestackupdatedownload) heruntergeladen werden. Azure Stack Hub-Softwareupdatepakete können Updates für Azure Stack Hub-Dienste sowie für das Betriebssystem der Skalierungseinheiten Ihrer Azure Stack Hub-Instanz enthalten.

>[!NOTE]
>Das Updatepaket selbst und sein Inhalt (z. B. Binärdateien, PowerShell-Skripts usw.) werden mit Zertifikaten signiert, die im Besitz von Microsoft sind. Bei einer Manipulation des Pakets wird die Signatur ungültig.

### <a name="where-to-download-azure-stack-hub-hotfix-packages"></a>Downloadorte von Azure Stack Hub-Hotfixpaketen

Pakete für [Azure Stack Hub-Hotfixes](./azure-stack-updates.md#update-package-types) werden auf demselben sicheren Azure-Endpunkt gehostet wie Azure Stack Hub-Updates. Bei Verfügbarkeit von Azure Stack Hub-Updates werden diese für Azure Stack Hub-Operatoren mit verbundenen Instanzen [automatisch im Administratorportal angezeigt](#automatic-download-and-preparation-for-update-packages). Sie können sie mithilfe der eingebetteten Links in jedem der jeweiligen Hotfix-KB-Artikel herunterladen, z. B. [Azure Stack Hub-Hotfix 1.1906.11.52](https://support.microsoft.com/help/4515650). Außerdem finden Sie Links zu Hotfixes in den Versionshinweisen zu Ihrer Azure Stack Hub-Version.

### <a name="where-to-download-oem-update-packages"></a>Downloadorte von OEM-Updatepaketen

Ihr OEM-Anbieter veröffentlicht möglicherweise auch Updates, z. B. Treiber- und Firmwareupdates. Diese Updates werden zwar als separate [OEM-Paketupdates](./azure-stack-updates.md#update-package-types) vom Hardwareanbieter bereitgestellt, doch sie werden auf die gleiche Weise importiert, installiert und verwaltet wie Updatepakete von Microsoft. Eine Liste mit Kontaktlinks von Anbietern finden Sie im Artikel [Anwenden von OEM-Updates (Originalgerätehersteller) auf Azure Stack Hub](./azure-stack-update-oem.md#oem-contact-information).

## <a name="import-and-install-updates"></a>Importieren und Installieren von Updates

Das folgende Verfahren zeigt, wie Updatepakete im Administratorportal importiert und installiert werden.

> [!IMPORTANT]  
> Unterrichten Sie die Benutzer über alle Wartungsvorgänge, und planen Sie normale Wartungsfenster möglichst außerhalb der Geschäftszeiten. Wartungsvorgänge können sowohl Benutzerworkloads als auch Portalvorgänge beeinträchtigen.

1. Wählen Sie im Administratorportal **Alle Dienste** aus. Wählen Sie anschließend in der Kategorie **Daten und Speicher** die Option **Speicherkonten** aus. Alternativ dazu beginnen Sie im Filterfeld mit der Eingabe von **Speicherkonten**, und wählen Sie den Eintrag dann aus.

    ![Azure Stack Hub-Update](./media/azure-stack-update-prepare-package/image1.png)

2. Geben Sie in das Filterfeld **update** ein, und wählen Sie das Speicherkonto **updateadminaccount**.

3. Klicken Sie in den Speicherkontodetails unter **Dienste** auf **Blobs**.

    ![Azure Stack Hub-Update – Blob](./media/azure-stack-update-prepare-package/image2.png)

4. Wählen Sie unter **Blob-Dienst** die Option **+ Container** aus, um einen Container zu erstellen. Geben Sie einen Namen (z. B. **update-1811**) ein, und klicken Sie dann auf **OK**.

    ![Azure Stack Hub-Update – Container](./media/azure-stack-update-prepare-package/image3.png)

5. Wählen Sie nach dem Erstellen des Containers den Containernamen und dann **Hochladen** aus, um die Paketdateien in den Container hochzuladen.

    ![Azure Stack Hub-Update – Upload](./media/azure-stack-update-prepare-package/image4.png)

6. Wählen Sie unter **Blob hochladen** das Ordnersymbol aus, navigieren Sie zur ZIP-Datei des Updatepakets, und wählen Sie dann im Datei-Explorer-Fenster **Öffnen** aus.

7. Wählen Sie unter **Blob hochladen** die Option **Hochladen** aus.

    ![Azure Stack Hub-Update – Blob hochladen](./media/azure-stack-update-prepare-package/image5.png)

8. Wiederholen Sie die Schritte 6 und 7 für die Datei **Metadata.xml** und alle zusätzlichen ZIP-Dateien im Updatepaket. Falls die Datei **Supplemental Notice.txt** enthalten ist, importieren Sie sie nicht.

9. Anschließend können Sie die Benachrichtigungen überprüfen. (Wählen Sie das Glockensymbol in der oberen rechten Ecke des Portals aus.) Eine Benachrichtigungen sollte darauf hinweisen, dass der Upload abgeschlossen ist.

10. Kehren Sie zum Blatt **Update** auf dem Dashboard zurück. Auf dem Blatt sollte angegeben sein, dass ein Update verfügbar ist. Dies weist darauf hin, dass das Update erfolgreich vorbereitet wurde. Wählen Sie das Blatt aus, um das neu hinzugefügte Updatepaket zu überprüfen.

11. Wählen Sie zum Installieren des Updates das als **Bereit** markierte Paket aus. Klicken Sie dann entweder mit der rechten Maustaste auf das Paket, und wählen Sie **Jetzt aktualisieren** aus, oder wählen Sie im oberen Bereich die Aktion **Jetzt aktualisieren** aus.

12. Wenn Sie „Update wird installiert...“ auswählen, können Sie den Status im Bereich **Update run details** (Updateausführungsdetails) anzeigen. Hier können Sie auch **Download summary** (Zusammenfassung herunterladen) auswählen, um die Protokolldateien herunterzuladen. Protokolle der Updateausführungen sind im Anschluss an den Versuch sechs Monate lang verfügbar.

13. Wenn das Update abgeschlossen ist, wird auf dem Blatt „Update“ die aktualisierte Azure Stack Hub-Version angezeigt.

Sie können Updates manuell aus dem Speicherkonto löschen, nachdem sie in Azure Stack Hub installiert wurden. Azure Stack Hub führt in regelmäßigen Abständen eine Überprüfung auf ältere Updatepakete durch und entfernt diese aus dem Speicher. Es kann in Azure Stack Hub bis zu zwei Wochen dauern, bis die alten Pakete entfernt werden.

## <a name="next-steps"></a>Nächste Schritte

[Anwenden des Updates](azure-stack-apply-updates.md)
