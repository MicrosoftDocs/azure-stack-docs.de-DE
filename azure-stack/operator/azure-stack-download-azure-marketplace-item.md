---
title: Herunterladen von Marketplace-Elementen aus Azure und Veröffentlichen in Azure Stack | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie Marketplace-Elemente aus Azure herunterladen und in Azure Stack veröffentlichen.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: sethm
ms.reviewer: ihcherie
ms.lastreviewed: 12/10/2018
ms.openlocfilehash: 095744322937a34dffd680b886fd4b06ca65d7d6
ms.sourcegitcommit: 20d1c0ab3892e9c4c71d5b039457f1e15b1c84c7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73618276"
---
# <a name="download-existing-marketplace-items-from-azure-and-publish-to-azure-stack"></a>Herunterladen vorhandener Marketplace-Elemente aus Azure und Veröffentlichen in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Als Cloudbetreiber können Sie Elemente aus Azure Marketplace herunterladen und in Azure Stack bereitstellen. Die zur Auswahl stehenden Optionen stammen aus einer speziell zusammengestellten Liste mit Azure Marketplace-Elementen, die vorab getestet wurden und für Azure Stack geeignet sind. Dieser Liste werden regelmäßig weitere Elemente hinzugefügt. Prüfen Sie daher, ob neue Inhalte verfügbar sind.

Es gibt zwei Szenarien zum Herstellen einer Verbindung mit Azure Marketplace:

- **Verbundenes Szenario:** Hierfür muss Ihre Azure Stack-Umgebung mit dem Internet verbunden sein. Elemente werden über das Azure Stack-Portal gesucht und heruntergeladen.
- **Nicht verbundenes oder partiell verbundenes Szenario:** Für dieses Szenario müssen Sie mit dem Tool für die Marketplace-Syndikation auf das Internet zugreifen, um Marketplace-Elemente herunterzuladen. Anschließend übertragen Sie Ihre Downloads in die nicht verbundene Azure Stack-Installation. Für dieses Szenario wird PowerShell verwendet.

Eine komplette Liste der zum Download verfügbaren Marketplace-Elemente finden Sie unter [Für Azure Stack verfügbare Azure Marketplace-Elemente](azure-stack-marketplace-azure-items.md). Eine Liste mit den aktuellen Ergänzungen, Entfernungen und Updates in Azure Stack Marketplace finden Sie unter [Änderungen im Azure Stack-Marketplace](azure-stack-marketplace-changes.md).

## <a name="connected-scenario"></a>Verbundenes Szenario

Wenn Azure Stack eine Verbindung mit dem Internet herstellt, können Sie mithilfe des Verwaltungsportals Marketplace-Elemente herunterladen.

### <a name="prerequisites"></a>Voraussetzungen

Ihre Azure Stack-Bereitstellung muss über eine Internetverbindung verfügen und [bei Azure registriert](azure-stack-registration.md) sein.

### <a name="use-the-portal-to-download-marketplace-items"></a>Herunterladen von Marketplace-Elementen mithilfe des Portals
  
1. Melden Sie sich beim Azure Stack-Administratorportal an.

2. Überprüfen Sie den verfügbaren Speicherplatz, bevor Sie Marketplace-Elemente herunterladen. Wenn Sie später Elemente zum Download auswählen, können Sie die Downloadgröße mit der verfügbaren Speicherkapazität vergleichen. Ziehen Sie bei eingeschränkter Kapazität Optionen für die [Verwaltung des verfügbaren Speicherplatzes](azure-stack-manage-storage-shares.md#manage-available-space) in Betracht.

    Überprüfen des verfügbaren Speicherplatzes: Wählen Sie unter **Regionsverwaltung** die entsprechende Region aus, und navigieren Sie dann zu **Ressourcenanbieter** > **Speicher**:

    ![Überprüfen des Speicherplatzes im Azure Stack-Administratorportal](media/azure-stack-download-azure-marketplace-item/storage.png)

3. Öffnen Sie den Azure Stack-Marketplace, und stellen Sie eine Verbindung mit Azure her. Wählen Sie dazu den **Marketplace-Verwaltungsdienst** aus, wählen Sie **Marketplace-Elemente** aus, und wählen Sie dann **Aus Azure hinzufügen** aus:

    ![Hinzufügen von Marketplace-Elementen aus Azure](media/azure-stack-download-azure-marketplace-item/marketplace.png)

4. Im Portal wird die Liste der Elemente angezeigt, die im Azure Marketplace zum Download zur Verfügung stehen. Sie können Produkte nach Name, Herausgeber und/oder Produkttyp filtern. Sie können auch auf die einzelnen Elemente klicken, um eine Beschreibung und zusätzliche Informationen anzuzeigen, u.a. die Downloadgröße:

    ![Liste mit Azure Marketplace-Elementen ](media/azure-stack-download-azure-marketplace-item/image03.PNG)

5. Wählen Sie das gewünschte Element aus, und klicken Sie auf **Weiter**. Die Downloadzeiten können variieren.

    ![Herunterladen des Azure Marketplace-Elements](media/azure-stack-download-azure-marketplace-item/image04.png)

    Wenn der Download abgeschlossen ist, können Sie das neue Marketplace-Element entweder als Azure Stack-Betreiber oder -Benutzer bereitstellen.

6. Klicken Sie zum Bereitstellen des heruntergeladenen Elements auf **+ Ressource erstellen**, und suchen Sie in den Kategorien nach dem neuen Marketplace-Element. Wählen Sie als Nächstes das Element aus, um den Bereitstellungsprozess zu starten. Der Prozess variiert für verschiedene Marketplace-Elemente.

## <a name="disconnected-or-a-partially-connected-scenario"></a>Nicht verbundenes oder partiell verbundenes Szenario

Wenn sich Azure Stack im Modus „Getrennt“ befindet, verwenden Sie PowerShell und das *Tool für die Marketplace-Syndikation*, um die Marketplace-Elemente auf einen Computer mit Internetverbindung herunterzuladen. Anschließend übertragen Sie die Elemente in Ihre Azure Stack-Umgebung. In einer nicht verbundenen Umgebung können Sie Marketplace-Elemente nicht mit dem Azure Stack-Portal herunterladen.

Das Tool für die Marketplace-Syndikation kann auch in einem verbundenen Szenario verwendet werden.

Dieses Szenario besteht aus zwei Teilen:

- **Teil 1:** Herunterladen aus dem Azure Marketplace. Konfigurieren Sie auf dem Computer mit Internetzugriff PowerShell, und laden Sie das Tool für die Syndikation und anschließend Elemente aus Azure Marketplace herunter.  
- **Teil 2:** Hochladen und Veröffentlichen in Azure Stack-Marketplace. Sie verschieben die heruntergeladenen Dateien in die Azure Stack-Umgebung, importieren sie in Azure Stack und veröffentlichen sie anschließend in Azure Stack-Marketplace.  

### <a name="prerequisites"></a>Voraussetzungen

- Eine verbundene Umgebung (muss nicht Azure Stack sein). Sie müssen verbunden sein, um die Liste der Produkte von Azure mit Details abrufen und alle Elemente lokal herunterladen zu können. Für die übrigen Schritte ist keine Internetverbindung erforderlich. Ein Katalog mit den von Ihnen zuvor heruntergeladenen Elementen wird erstellt, den Sie in Ihrer nicht verbundenen Umgebung verwenden können.

- Ein USB-Schlüssel oder ein externes Laufwerk, um eine Verbindung mit der getrennten Umgebung herzustellen und alle erforderlichen Artefakte zu übertragen

- Eine nicht verbundene Azure Stack-Umgebung mit den folgenden Voraussetzungen:
  - Ihre Azure Stack-Bereitstellung muss [bei Azure registriert](azure-stack-registration.md) sein.
  - Auf dem Computer mit Internetverbindung muss **mindestens Version 1.2.11 des Azure Stack-PowerShell-Moduls**  installiert sein. [Installieren Sie die Azure Stack-spezifischen PowerShell-Module](azure-stack-powershell-install.md), falls diese noch nicht vorhanden sind.
  - Die [PowerShell-Umgebung für den Azure Stack-Bediener](azure-stack-powershell-configure-admin.md) muss konfiguriert sein, um den Import eines heruntergeladenen Marketplace-Elements zu ermöglichen.
  - Klonen Sie das GitHub-Repository mit den [Azure Stack-Tools](https://github.com/Azure/AzureStack-Tools).

- Sie benötigen ein [Speicherkonto](azure-stack-manage-storage-accounts.md) in Azure Stack mit einem öffentlich zugänglichen Container (Speicherblob). Sie verwenden den Container als temporären Speicher für die Katalogdateien der Marketplace-Elemente. Falls Sie mit Speicherkonten und Containern nicht vertraut sind, hilft Ihnen der Artikel [Schnellstart: Hochladen, Herunterladen und Auflisten von Blobs über das Azure-Portal](/azure/storage/blobs/storage-quickstart-blobs-portal) in der Azure-Dokumentation weiter.

- Das Tool für die Marketplace-Syndikation wird beim ersten Verfahren heruntergeladen.

- Sie können [AzCopy](/azure/storage/common/storage-use-azcopy) installieren, um die Downloadleistung zu optimieren, aber dies ist nicht zwingend erforderlich.

Nach Ihrer Registrierung können Sie die folgende Meldung ignorieren, die auf dem Blatt für die Marketplace-Verwaltung angezeigt wird, da sie für den Anwendungsfall mit getrennter Umgebung nicht relevant ist:

[![Nicht registrierte Nachricht](media/azure-stack-download-azure-marketplace-item/toolsmsgsm.png "Nicht registrierte Nachricht")](media/azure-stack-download-azure-marketplace-item/toolsmsg.png#lightbox)

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>Herunterladen von Marketplace-Elementen mithilfe des Tools für die Marketplace-Syndikation

> [!IMPORTANT]
> Jedes Mal, wenn Sie Marketplace-Elemente in einem nicht verbundenen Szenario herunterladen, müssen Sie das Tool für die Marketplace-Syndikation herunterladen. An diesem Skript werden häufig Änderungen vorgenommen, und es sollte immer die aktuelle Version für jeden Download verwendet werden.

1. Öffnen Sie auf einem Computer mit Internetverbindung eine PowerShell-Konsole als Administrator.

2. Fügen Sie das Azure-Konto hinzu, das Sie für die Registrierung von Azure Stack verwendet haben. Führen Sie zum Hinzufügen des Kontos in PowerShell `Add-AzureRmAccount` ohne Parameter aus. Sie werden aufgefordert, Ihre Anmeldeinformationen für das Azure-Konto einzugeben. Je nach Konfiguration Ihres Kontos müssen Sie ggf. die zweistufige Authentifizierung verwenden.

   [!include[Remove Account](../../includes/remove-account.md)]

3. Sollten Sie über mehrere Abonnements verfügen, führen Sie den folgenden Befehl aus, um das für die Registrierung verwendete Abonnement auszuwählen:  

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID 'Your Azure Subscription GUID' | Select-AzureRmSubscription
   $AzureContext = Get-AzureRmContext
   ```

4. Laden Sie mithilfe des folgenden Skripts die neueste Version des Tools für die Marketplace-Syndikation herunter:  

   ```powershell
   # Download the tools archive.
   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
   invoke-webrequest https://github.com/Azure/AzureStack-Tools/archive/master.zip `
     -OutFile master.zip

   # Expand the downloaded files.
   expand-archive master.zip `
     -DestinationPath `
     -Force

   # Change to the tools directory.
   cd .\AzureStack-Tools-master
   ```

5. Führen Sie die folgenden Befehle aus, um das Syndikationsmodul zu importieren und zu starten. Ersetzen Sie `Destination folder path` durch einen Speicherort, an dem die aus Azure Marketplace heruntergeladenen Dateien gespeichert werden sollen.

   ```powershell  
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1

   Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes"
   ```

   Beachten Sie, dass `Export-AzSOfflineMarketplaceItem` ein zusätzliches `-cloud`-Flag aufweist, das die Cloudumgebung angibt. Der Standardwert ist **azurecloud**.

6. Wenn das Tool ausgeführt wird, wird ein Bildschirm mit der Liste verfügbarer Azure Marketplace-Elemente angezeigt, der in etwa wie auf der folgenden Abbildung aussieht:

   [![Popup mit Azure Marketplace-Elementen](media/azure-stack-download-azure-marketplace-item/image05.png "Marketplace-Elemente")](media/azure-stack-download-azure-marketplace-item/image05.png#lightbox)

7. Wenn Sie die Azure Storage-Tools nicht installiert haben, erhalten Sie die folgende Meldung. Laden Sie zum Installieren dieser Tools [AzCopy](/azure/storage/common/storage-use-azcopy#download-azcopy) herunter:

   ![Speichertools](media/azure-stack-download-azure-marketplace-item/vmnew1.png)

8. Wählen Sie das Element aus, das Sie herunterladen möchten, und notieren Sie sich die **Version**. Sie können **STRG** gedrückt halten, um mehrere Images auszuwählen. Sie benötigen die *Version*, wenn Sie das Element in der nächsten Prozedur importieren.

   Die Liste mit den Images kann mithilfe der Option **Kriterien hinzufügen** gefiltert werden.

9. Klicken Sie auf **OK**, lesen Sie sich dann die rechtlichen Bedingungen durch, und bestätigen Sie diese.

10. Die Dauer des Downloads hängt von der Größe des Elements ab. Nach Abschluss des Downloads ist das Element in dem Ordner verfügbar, den Sie im Skript angegeben haben. Der Download enthält eine VHD-Datei (für virtuelle Computer) oder eine ZIP-Datei (für VM-Erweiterungen). Darüber hinaus kann auch ein Katalogpaket im Format *AZPKG* enthalten sein, bei dem es sich einfach um eine ZIP-Datei handelt.

11. Im Falle eines Downloadfehlers können Sie den Vorgang mithilfe des folgenden PowerShell-Cmdlets erneut ausführen:

    ```powershell
    Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes"
    ```

    Entfernen Sie vor der Wiederholung den Produktordner, in dem der Fehler beim Herunterladen aufgetreten ist. Wenn der Fehler also beispielsweise beim Herunterladen in den Ordner `D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1` aufgetreten ist, entfernen Sie den Ordner `D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1`, und führen Sie anschließend das Cmdlet erneut aus.

### <a name="import-the-download-and-publish-to-azure-stack-marketplace-using-powershell"></a>Importieren des Downloads und Veröffentlichen im Azure Stack-Marketplace mithilfe von PowerShell

1. Die [zuvor heruntergeladenen](#use-the-marketplace-syndication-tool-to-download-marketplace-items) Dateien müssen an einen lokalen Speicherort verschoben werden, damit sie für Ihre Azure Stack-Umgebung zur Verfügung stehen. Das Tool für die Marketplace-Syndikation muss ebenfalls für Ihre Azure Stack-Umgebung verfügbar sein, da es für den Importvorgang benötigt wird.

   Die folgende Abbildung zeigt ein Beispiel für die Ordnerstruktur. `D:\downloadfolder` enthält alle heruntergeladenen Marketplace-Elemente. Jeder Unterordner ist ein Marketplace-Element (z. B. `microsoft.custom-script-linux-arm-2.0.3`) und ist nach der Produkt-ID benannt. In den Unterordnern befindet sich jeweils der heruntergeladene Inhalt des Marketplace-Elements.

   [![Verzeichnisstruktur für Marketplace-Downloads](media/azure-stack-download-azure-marketplace-item/mp1sm.png "Verzeichnisstruktur für Marketplace-Downloads")](media/azure-stack-download-azure-marketplace-item/mp1.png#lightbox)

2. Gehen Sie gemäß der Anleitung in [diesem Artikel](azure-stack-powershell-configure-admin.md) vor, um die PowerShell-Sitzung für Azure Stack-Bediener zu konfigurieren.

3. Führen Sie das folgende Skript aus, um das Syndikationsmodul zu importieren und das Tool für die Marketplace-Syndikation zu starten:

   ```powershell
   $credential = Get-Credential -Message "Enter the azure stack operator credential:"
   Import-AzSOfflineMarketplaceItem -origin "marketplace content folder" -AzsCredential $credential
   ```

   Der Parameter `-origin` gibt den Ordner auf der obersten Ebene an, der alle heruntergeladenen Produkte enthält, z. B. `"D:\downloadfolder"`.

   Das `-AzsCredential` ist optional. Dieser Parameter dient dazu, das Zugriffstoken zu verlängern, falls es abgelaufen ist. Wenn das Token abläuft und der Parameter `-AzsCredential` nicht angegeben ist, werden Sie zur Eingabe der Anmeldeinformationen des Bedieners aufgefordert.

    > [!NOTE]  
    > AD FS unterstützt nur die interaktive Authentifizierung mit Benutzeridentitäten. Wenn ein Objekt mit Anmeldeinformationen erforderlich ist, müssen Sie einen Dienstprinzipalnamen (SPN) verwenden. Weitere Informationen zum Einrichten eines Dienstprinzipals mit Azure Stack und AD FS als Identitätsverwaltungsdienst finden Sie unter [Verwalten eines AD FS-Dienstprinzipals](azure-stack-create-service-principals.md#manage-an-ad-fs-service-principal).

4. Nach erfolgreicher Ausführung des Skripts sollte das Element über Azure Stack-Marketplace verfügbar sein.

## <a name="next-steps"></a>Nächste Schritte

- [Hinzufügen eines benutzerdefinierten VM-Images](azure-stack-add-vm-image.md)
- [Erstellen und Veröffentlichen eines benutzerdefinierten Marketplace-Elements](azure-stack-create-and-publish-marketplace-item.md)
