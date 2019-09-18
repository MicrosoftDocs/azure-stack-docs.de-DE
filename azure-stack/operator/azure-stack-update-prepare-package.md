---
title: Vorbereiten eines Azure Stack-Updatepakets | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie ein Azure Stack-Updatepaket vorbereiten.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2019
ms.author: mabrigg
ms.lastreviewed: 09/10/2019
ms.reviewer: ppacent
ms.openlocfilehash: 515195e30aed9944b8e0cc0e371d08b54ea75189
ms.sourcegitcommit: 38f21e0bcf7b593242ad615c9d8ef8a1ac19c734
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70902667"
---
# <a name="prepare-an-azure-stack-update-package"></a>Vorbereiten eines Azure Stack-Updatepakets

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

Dieser Artikel bietet eine Übersicht über die Vorbereitung von Azure Stack-Updatepaketen, damit diese zum Aktualisieren Ihrer Azure Stack-Umgebung verwendet werden können. Dieser Vorgang besteht aus folgenden Schritten:

- [Herunterladen des Updatepakets](#download-the-update-package)
- [Importieren des Updatepakets in Ihre Azure Stack-Umgebung über das Azure Stack-Administratorportal](#import-and-install-updates)

Auf Systemen, die eine Verbindung mit den automatischen Updateendpunkten herstellen können, werden Azure Stack-Softwareupdates und -Hotfixes automatisch heruntergeladen und vorbereitet. Auf Systemen ohne Konnektivität und für alle Updates vom OEM muss das Updatepaket wie in diesem Thema beschrieben vorbereitet werden.  

In der folgenden Tabelle ist angegeben, wann die Updatepakete manuell vorbereitet werden müssen und wann sie automatisch vorbereitet werden.

| Updatetyp | Konnektivität | Erforderliche Aktion |
| --- | --- | --- |
| Azure Stack-Softwareupdates | Verbunden | Die Updates werden automatisch heruntergeladen und vorbereitet, wenn das Update angewandt wird. |
| Azure Stack-Hotfixes | Verbunden | Die Updates werden automatisch heruntergeladen und vorbereitet, wenn das Update angewandt wird. |
| OEM-Paketupdates | Verbunden | Das Updatepaket muss vorbereitet werden. Führen Sie die Schritte in diesem Artikel aus. |
| Azure Stack-Softwareupdates | Verbindung getrennt oder schwach | Das Updatepaket muss vorbereitet werden. Führen Sie die Schritte in diesem Artikel aus. |
| Azure Stack-Hotfixes | Verbindung getrennt oder schwach | Das Updatepaket muss vorbereitet werden. Führen Sie die Schritte in diesem Artikel aus. |
| OEM-Paketupdates | Verbindung getrennt oder schwach | Das Updatepaket muss vorbereitet werden. Führen Sie die Schritte in diesem Artikel aus. |

## <a name="download-the-update-package"></a>Herunterladen des Updates
Das Updatepaket für Azure Stack-Updates und -Hotfixes ist bei verbundenen Systemen auf dem Blatt „Update“ verfügbar. Sie müssen das Paket herunterladen und an einen Speicherort verschieben, auf den von Ihrer Azure Stack-Instanz zugegriffen werden kann, wenn Sie ein OEM-Paket aktualisieren oder wenn Sie ein nicht verbundenes System warten. Sie müssen das Paket möglicherweise auch herunterladen und an einen zugänglichen Speicherort hochladen, wenn Sie ein System mit einer nur zeitweiligen Verbindung ausführen.

Überprüfen Sie den Paketinhalt. Ein Updatepaket besteht in der Regel aus folgenden Dateien:

-   **Einer selbstextrahierenden ZIP-Datei mit dem Benennungsmuster „\<Paketname>.zip“.** Diese Datei enthält die Nutzlast für das Update.
- **Einer Datei „Metadata.xml“.** Diese Datei enthält wichtige Informationen zum Update, etwa Herausgeber, Name, Voraussetzungen, Größe und Supportpfad-URL.

### <a name="automatic-download-and-preparation-for-update-packages"></a>Automatisches Herunterladen und Vorbereiten von Updatepaketen
Azure Stack-Softwareupdates und -Hotfixes werden auf Systemen mit Internetverbindung mit den **Azure Stack-Endpunkten für automatische Updates** (https://*.azureedge.net und https://aka.ms/azurestackautomaticupdate ) automatisch vorbereitet. Weitere Informationen zum Einrichten der Konnektivität mit den **Azure Stack-Endpunkten für automatische Updates** finden Sie in der Beschreibung der Endpunkte für **Patches und Updates** unter [Azure Stack-Firewallintegration](https://docs.microsoft.com/azure-stack/operator/azure-stack-integrate-endpoints#ports-and-urls-outbound).

### <a name="where-to-download-azure-stack-update-packages"></a>Downloadorte von Azure Stack-Updatepaketen

Azure Stack-Updates für [vollständige und Express-Updates](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates#update-package-types) werden auf einem sicheren Azure-Endpunkt gehostet. Azure Stack-Bedienern mit verbundenen Instanzen werden [die Azure Stack-Updates automatisch im Verwaltungsportal](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-prepare-package#automatic-download-and-preparation-for-update-packages) angezeigt. Für Systeme ohne oder mit schwacher Internetverbindung können Updatepakete mit dem [Downloadprogramm für Azure Stack-Updates](https://aka.ms/azurestackupdatedownload) heruntergeladen werden. Azure Stack-Softwareupdatepakete können Updates für Azure Stack-Dienste sowie das Betriebssystem der Skalierungseinheiten Ihrer Azure Stack-Instanz enthalten.

>[!NOTE]
>Das Updatepaket selbst und sein Inhalt (z. B. Binärdateien, PowerShell-Skripts usw.) werden mit Zertifikaten signiert, die im Besitz von Microsoft sind. Bei einer Manipulation des Pakets wird die Signatur ungültig.


### <a name="where-to-download-azure-stack-hotfix-packages"></a>Downloadorte von Azure Stack-Hotfixpaketen

Pakete für [Azure Stack-Hotfixes](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates#update-package-types) werden auf demselben sicheren Azure-Endpunkt gehostet wie Azure Stack-Updates. Azure Stack-Bedienern mit verbundenen Instanzen werden [die Azure Stack-Updates automatisch im Verwaltungsportal](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-prepare-package#automatic-download-and-preparation-for-update-packages) angezeigt. Sie können sie mithilfe der eingebetteten Links in jedem der jeweiligen Hotfix-KB-Artikel herunterladen, z. B. [Azure Stack-Hotfix 1.1906.11.52](https://support.microsoft.com/help/4515650). Hotfixes finden Sie in den Versionshinweisen zu Ihrer Azure Stack-Version.

### <a name="where-to-download-oem-update-packages"></a>Downloadorte von OEM-Updatepaketen
Ihr OEM-Anbieter veröffentlicht auch Updates, z. B. Treiber- und Firmwareupdates. Diese Updates werden zwar als separate [OEM-Paketupdates](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates#update-package-types) vom Hardwareanbieter bereitgestellt, doch sie werden auf die gleiche Weise importiert, installiert und verwaltet wie Updatepakete von Microsoft. Eine Liste mit Kontaktlinks von Anbietern finden Sie unter [Anwenden von OEM-Updates (Originalgerätehersteller) auf Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-oem#oem-contact-information).

## <a name="import-and-install-updates"></a>Importieren und Installieren von Updates

Das folgende Verfahren zeigt, wie Updatepakete im Verwaltungsportal importiert und installiert werden.

> [!Important]  
> Unterrichten Sie die Benutzer über alle Wartungsvorgänge, und planen Sie normale Wartungsfenster möglichst außerhalb der Geschäftszeiten. Wartungsvorgänge können sowohl Benutzerworkloads als auch Portalvorgänge beeinträchtigen.

1.  Wählen Sie im Verwaltungsportal **Alle Dienste** aus. Wählen Sie anschließend in der Kategorie **Daten und Speicher** die Option **Speicherkonten** aus. (Oder beginnen Sie im Filterfeld mit der Eingabe von **Speicherkonten**, und wählen Sie dann die entsprechende Option aus.)

    ![Azure Stack-Update](./media/azure-stack-update-prepare-package/image1.png) 

1.  Geben Sie in das Filterfeld **update** ein, und wählen Sie das Speicherkonto **updateadminaccount**.

2.  Klicken Sie in den Speicherkontodetails unter **Dienste** auf **Blobs**.

    ![Azure Stack-Update – Blob](./media/azure-stack-update-prepare-package/image2.png)

1.  Wählen Sie unter **Blob-Dienst** die Option **+ Container** aus, um einen Container zu erstellen. Geben Sie einen Namen (etwa *Update-1811*) ein, und wählen Sie dann **OK** aus.

    ![Azure Stack-Update – Container](./media/azure-stack-update-prepare-package/image3.png)

1.  Klicken Sie nach dem Erstellen des Containers auf den Containernamen und dann auf **Hochladen**, um die Paketdateien in den Container hochzuladen.

    ![Azure Stack-Update – Upload](./media/azure-stack-update-prepare-package/image4.png)

1.  Klicken Sie unter **Blob hochladen** auf das Ordnersymbol, navigieren Sie zur ZIP-Datei des Updatepakets, und klicken Sie dann im Explorer-Fenster auf **Öffnen**.

2.  Klicken Sie unter **Blob hochladen** auf **Hochladen**.

    ![Azure Stack-Update – Blob hochladen](./media/azure-stack-update-prepare-package/image5.png)

1.  Wiederholen Sie die Schritte 6 und 7 für die Datei „Metadata.xml“ und alle zusätzlichen ZIP-Dateien im Updatepaket. Falls die Datei „Supplemental Notice.txt“ enthalten ist, importieren Sie sie nicht.

2.  Anschließend können Sie die Benachrichtigungen (Glockensymbol in der oberen rechten Ecke des Portals) überprüfen. In den Benachrichtigungen sollte angegeben sein, dass der Upload abgeschlossen ist.

3.  Kehren Sie zum Blatt „Update“ auf dem Dashboard zurück. Auf dem Blatt sollte angegeben sein, dass ein Update verfügbar ist. Dies weist darauf hin, dass das Update erfolgreich vorbereitet wurde. Klicken Sie auf das Blatt, um das neu hinzugefügte Updatepaket zu überprüfen.

4.  Um das Update zu installieren, wählen Sie das als **Bereit** markierte Paket aus. Klicken Sie dann entweder mit der rechten Maustaste auf das Paket, und wählen Sie **Jetzt aktualisieren**, oder klicken Sie im oberen Bereich auf die Aktion **Jetzt aktualisieren**.

5.  Wenn Sie auf „Update wird installiert...“ klicken, können Sie den Status im Bereich **Update run details** (Updateausführungsdetails) anzeigen. Hier können Sie auch auf **Download summary** (Zusammenfassung herunterladen) klicken, um die Protokolldateien herunterzuladen. Protokolle der Ausführung von Updates sind im Anschluss an den Versuch 6 Monate lang verfügbar.

6.  Wenn das Update abgeschlossen ist, wird auf dem Blatt „Update“ die aktualisierte Azure Stack-Version angezeigt.

Sie können Updates manuell aus dem Speicherkonto löschen, nachdem sie in Azure Stack installiert wurden. Azure Stack führt in regelmäßigen Abständen eine Überprüfung auf ältere Updatepakete durch und entfernt diese aus dem Speicher. Es kann in Azure Stack bis zu zwei Wochen dauern, bis die alten Pakete entfernt werden.

## <a name="next-steps"></a>Nächste Schritte

[Anwenden des Updates](azure-stack-apply-updates.md)
