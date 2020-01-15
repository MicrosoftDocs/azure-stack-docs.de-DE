---
title: Herunterladen von Marketplace-Elementen aus Azure und Veröffentlichen in Azure Stack | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie Marketplace-Elemente aus Azure herunterladen und in Azure Stack veröffentlichen.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/23/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 12/23/2018
ms.openlocfilehash: 4072330d48c63d72fb0d2504d84a67f06b1cebea
ms.sourcegitcommit: b2418661bfa3a791e65b9b487e20982dba3e4c41
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75757000"
---
# <a name="download-marketplace-items-to-azure-stack-hub"></a>Herunterladen von Marketplace-Elementen in Azure Stack Hub 

Als Cloudbetreiber können Sie Elemente aus dem Marketplace in Azure Stack Hub herunterladen und mithilfe der Azure Stack Hub-Umgebung für alle Benutzer verfügbar machen. Die zur Auswahl stehenden Optionen stammen aus einer speziell zusammengestellten Liste mit Azure Marketplace-Elementen, die vorab getestet wurden und für Azure Stack geeignet sind. Dieser Liste werden regelmäßig weitere Elemente hinzugefügt. Prüfen Sie daher, ob neue Inhalte verfügbar sind.

Es gibt zwei Szenarien für das Herunterladen von Marketplace-Produkten:

- **Verbundenes Szenario**: Hierfür muss Ihre Azure Stack Hub-Umgebung mit dem Internet verbunden sein. Elemente werden über das Azure Stack Hub-Administratorportal gesucht und heruntergeladen.
- **Nicht verbundenes oder partiell verbundenes Szenario**: Für dieses Szenario müssen Sie mit dem Tool für die Marketplace-Syndikation auf das Internet zugreifen, um Marketplace-Elemente herunterzuladen. Anschließend übertragen Sie Ihre Downloads in die nicht verbundene Azure Stack-Installation. Für dieses Szenario wird PowerShell verwendet.

Eine komplette Liste der zum Download verfügbaren Marketplace-Elemente finden Sie unter [Für Azure Stack verfügbare Azure Marketplace-Elemente](azure-stack-marketplace-azure-items.md). Eine Liste mit den aktuellen Ergänzungen, Entfernungen und Updates in Azure Stack Marketplace finden Sie unter [Änderungen im Azure Stack-Marketplace](azure-stack-marketplace-changes.md).

> [!NOTE]
> Der Katalog unterscheidet sich je nach der Cloud, mit der Ihr Azure Stack Hub-System verbunden ist. Die Cloudumgebung wird durch das Azure-Abonnement bestimmt, das Sie zum Registrieren von Azure Stack Hub verwenden.

## <a name="connected-scenario"></a>Verbundenes Szenario

Wenn Azure Stack Hub eine Verbindung mit dem Internet herstellt, können Sie mithilfe des Administratorportals Marketplace-Elemente herunterladen.

### <a name="prerequisites"></a>Voraussetzungen

Ihre Azure Stack Hub-Bereitstellung muss über eine Internetverbindung verfügen und bei Azure registriert sein.

### <a name="use-the-portal-to-download-marketplace-items"></a>Herunterladen von Marketplace-Elementen mithilfe des Portals

1. Melden Sie sich beim Azure Stack Hub-Administratorportal an.

2. Überprüfen Sie den verfügbaren Speicherplatz, bevor Sie Marketplace-Elemente herunterladen. Wenn Sie später Elemente zum Download auswählen, können Sie die Downloadgröße mit der verfügbaren Speicherkapazität vergleichen. Ziehen Sie bei eingeschränkter Kapazität Optionen für die  [Verwaltung des verfügbaren Speicherplatzes](azure-stack-manage-storage-shares.md#manage-available-space) in Betracht.

   Überprüfen des verfügbaren Speicherplatzes: Wählen Sie unter  **Regionsverwaltung** die entsprechende Region aus, und navigieren Sie dann zu  **Ressourcenanbieter** > **Speicher**:

   ![Überprüfen des Speicherplatzes im Azure Stack-Administratorportal](media/azure-stack-download-azure-marketplace-item/storage.png)

3. Öffnen Sie den Azure Stack Hub-Marketplace, und stellen Sie eine Verbindung mit Azure her. Wählen Sie dazu den  **Marketplace-Verwaltungsdienst**  aus, wählen Sie  **Marketplace-Elemente** aus, und wählen Sie dann  **Aus Azure hinzufügen** aus:

   ![Hinzufügen von Marketplace-Elementen aus Azure](media/azure-stack-download-azure-marketplace-item/marketplace.png)

4. Jede Position zeigt auch die derzeit verfügbare Version. Wenn mehrere Versionen eines Marketplace-Elements verfügbar sind, ist in der Spalte **Version** **Mehrere** angegeben. Sie können auf die einzelnen Elemente klicken, um eine Beschreibung und zusätzliche Informationen anzuzeigen, u. a. die Downloadgröße:

   ![Hinzufügen aus Azure](media/azure-stack-download-azure-marketplace-item/add-from-azure1.png)

5. Wenn die Version eines Elements als **Mehrere** angegeben ist, können Sie das Element auswählen und in der daraufhin angezeigten Dropdownliste eine bestimmte Version wählen:

   ![Hinzufügen aus Azure](media/azure-stack-download-azure-marketplace-item/add-from-azure3.png)

6. Wählen Sie das gewünschte Element aus, und klicken Sie auf  **Herunterladen**. Die Downloadzeiten variieren und hängen von der Netzwerkkonnektivität ab. Wenn der Download abgeschlossen ist, können Sie das neue Marketplace-Element entweder als Azure Stack-Betreiber oder -Benutzer bereitstellen.

7. Klicken Sie zum Bereitstellen des heruntergeladenen Elements auf  **+ Ressource erstellen**, und suchen Sie in den Kategorien nach dem neuen Marketplace-Element. Wählen Sie als Nächstes das Element aus, um den Bereitstellungsprozess zu starten. Der Prozess variiert für verschiedene Marketplace-Elemente.

## <a name="disconnected-or-a-partially-connected-scenario"></a>Nicht verbundenes oder partiell verbundenes Szenario

Wenn Azure Stack Hub über begrenzte oder keine Internetkonnektivität verfügt, verwenden Sie PowerShell und das  *Tool für die Marketplace-Syndikation* , um die Marketplace-Elemente auf einen Computer mit Internetkonnektivität herunterzuladen. Anschließend übertragen Sie die Elemente in Ihre Azure Stack Hub-Umgebung. In einer nicht verbundenen Umgebung können Sie Marketplace-Elemente nicht mit dem Azure Stack Hub-Portal herunterladen.

Das Tool für die Marketplace-Syndikation kann auch in einem verbundenen Szenario verwendet werden.

Dieses Szenario besteht aus zwei Teilen:

- **Teil 1**: Herunterladen von Elementen aus dem Marketplace. Sie Konfigurieren auf dem Computer mit Internetzugriff PowerShell und laden das Tool für die Syndikation und anschließend Elemente aus Azure Marketplace herunter.
- **Teil 2**: Hochladen und Veröffentlichen in Azure Stack Hub-Marketplace. Sie verschieben die heruntergeladenen Dateien in die Azure Stack Hub-Umgebung, importieren sie in Azure Stack und veröffentlichen sie anschließend in Azure Stack-Marketplace.

### <a name="prerequisites"></a>Voraussetzungen

- Eine verbundene Umgebung (muss nicht Azure Stack Hub sein). Sie müssen verbunden sein, um die Liste der Produkte von Azure mit Details abrufen und alle Elemente lokal herunterladen zu können. Für die übrigen Schritte ist keine Internetkonnektivität erforderlich. Ein Katalog mit den von Ihnen zuvor heruntergeladenen Elementen wird erstellt, den Sie in Ihrer nicht verbundenen Umgebung verwenden können.

- Wechselmedien, um eine Verbindung mit der getrennten Umgebung herzustellen und alle erforderlichen Artefakte zu übertragen.

- Eine nicht verbundene Azure Stack Hub-Umgebung mit den folgenden Voraussetzungen:

  - Ihre Azure Stack-Bereitstellung muss bei Azure registriert sein.

  - Auf dem Computer mit Internetverbindung muss mindestens  **Version 1.2.11 des Azure Stack-PowerShell-Moduls ** installiert sein.  [Installieren Sie die Azure Stack-spezifischen PowerShell-Module](azure-stack-powershell-install.md), falls diese noch nicht vorhanden sind.

  - Die [PowerShell-Umgebung für den Azure Stack-Betreiber](azure-stack-powershell-configure-admin.md) muss konfiguriert sein, um den Import eines heruntergeladenen Marketplace-Elements zu ermöglichen.

  - Klonen Sie das GitHub-Repository mit den [Azure Stack-Tools](https://github.com/Azure/AzureStack-Tools) .

- Sie benötigen ein [Speicherkonto](azure-stack-manage-storage-accounts.md) in Azure Stack mit einem öffentlich zugänglichen Container (Speicherblob). Sie verwenden den Container als temporären Speicher für die Katalogdateien der Marketplace-Elemente. Falls Sie mit Speicherkonten und Containern nicht vertraut sind, hilft Ihnen der Artikel [Schnellstart: Hochladen, Herunterladen und Auflisten von Blobs über das Azure-Portal](/azure/storage/blobs/storage-quickstart-blobs-portal) in der Azure-Dokumentation weiter.

- Das Tool für die Marketplace-Syndikation wird beim ersten Verfahren heruntergeladen.

- Sie können [AzCopy](/azure/storage/common/storage-use-azcopy) installieren, um die Downloadleistung zu optimieren, aber dies ist nicht zwingend erforderlich.

Nach Ihrer Registrierung können Sie die folgende Meldung ignorieren, die auf dem Blatt für die Marketplace-Verwaltung angezeigt wird, da sie für den Anwendungsfall mit getrennter Umgebung nicht relevant ist:

![Marketplace-Verwaltung](media/azure-stack-download-azure-marketplace-item/toolsmsg.png)

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>Herunterladen von Marketplace-Elementen mithilfe des Tools für die Marketplace-Syndikation

> [!IMPORTANT]
> Jedes Mal, wenn Sie Marketplace-Elemente in einem nicht verbundenen Szenario herunterladen, müssen Sie das Tool für die Marketplace-Syndikation herunterladen. An diesem Skript werden häufig Änderungen vorgenommen, und es sollte immer die aktuelle Version für jeden Download verwendet werden.

1. Öffnen Sie auf einem Computer mit Internetverbindung eine PowerShell-Konsole als Administrator.

2. Fügen Sie das Azure-Konto hinzu, das Sie für die Registrierung von Azure Stack verwendet haben. Führen Sie zum Hinzufügen des Kontos in PowerShell **Add-AzureRmAccount** ohne Parameter aus. Sie werden aufgefordert, Ihre Anmeldeinformationen für das Azure-Konto einzugeben. Je nach Konfiguration Ihres Kontos müssen Sie ggf. die zweistufige Authentifizierung verwenden.

   > [!NOTE]
   > Wenn Ihre Sitzung abgelaufen ist, Ihr Kennwort geändert wurde oder Sie lediglich Konten wechseln möchten, führen Sie das folgende Cmdlet aus, bevor Sie sich mit **Add-AzureRmAccount** anmelden: **Remove-AzureRmAccount-Scope Process**.

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

5. Führen Sie die folgenden Befehle aus, um das Syndikationsmodul zu importieren und zu starten:

   ```powershell  
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1
   ```

6. Führen Sie den folgenden Befehl aus, um Marketplace-Elemente wie VM-Images, Erweiterungen oder Lösungsvorlagen zu exportieren. Ersetzen Sie „Destination folder path in quotes“ durch einen Speicherort, an dem die aus dem Azure Marketplace heruntergeladenen Dateien gespeichert werden sollen:

   ```powershell
   Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes" -azCopyDownloadThreads "[optional - AzCopy threads number]" -azureContext $AzureContext
   ```

   Führen Sie den folgenden Befehl aus, um Ressourcenanbieter oder PaaS-Dienste zu exportieren:

   ```powershell
   Export-AzSOfflineResourceProvider -destination "Destination folder path" -azCopyDownloadThreads "AzCopy threads number" -azureContext $AzureContext
   ```

   Das `-azCopyDownloadThreads` ist optional. Der Parameter sollte nur verwendet werden, wenn Sie über ein Netzwerk mit geringer Bandbreite verfügen und Premiumdownload verwenden. Diese Option gibt die Anzahl der gleichzeitigen Vorgänge in AzCopy an. Wenn Sie ein Netzwerk mit geringer Bandbreite verwenden, können Sie eine niedrigere Anzahl angeben, um Fehler durch gleichzeitig verwendete Ressourcen zu vermeiden. Weitere Informationen finden Sie in diesem [Artikel zu Azure](/previous-versions/azure/storage/storage-use-azcopy#specify-the-number-of-concurrent-operations-to-start).

   Der Parameter `-azureContext` ist ebenfalls optional. Wenn Sie den Azure-Kontext nicht angeben, verwendet das Cmdlet den Azure-Standardkontext.

7. Wenn das Tool ausgeführt wird, wird ein Bildschirm mit der Liste verfügbarer Azure Marketplace-Elemente angezeigt, der in etwa wie auf der folgenden Abbildung aussieht:

   ![Marketplace-Elemente](media/azure-stack-download-azure-marketplace-item/tool1.png)

8. Wenn mehrere Versionen eines Marketplace-Elements verfügbar sind, enthält die Spalte **Version** die Angabe **Mehrere Versionen**. Wenn die Version eines Elements als **Mehrere Versionen ** angegeben ist, können Sie das Element auswählen und im daraufhin angezeigten Auswahlfenster eine bestimmte Version wählen.

9. Wählen Sie das Element aus, das Sie herunterladen möchten, und notieren Sie sich die **Version**. Sie können **STRG** gedrückt halten, um mehrere Images auszuwählen. Sie benötigen die *Version* , wenn Sie das Element in der nächsten Prozedur importieren.

    Die Liste mit den Images kann mithilfe der Option **Kriterien hinzufügen** gefiltert werden.

10. Wenn Sie die Azure Storage-Tools nicht installiert haben, erhalten Sie die folgende Meldung. Laden Sie zum Installieren dieser Tools [AzCopy](/azure/storage/common/storage-use-azcopy#download-azcopy) herunter:

    ![Speichertools](media/azure-stack-download-azure-marketplace-item/vmnew1.png)

11. Klicken Sie auf **OK**, lesen Sie sich dann die rechtlichen Bedingungen durch, und bestätigen Sie diese.

12. Die Dauer des Downloads hängt von der Größe des Elements ab. Nach Abschluss des Downloads ist das Element in dem Ordner verfügbar, den Sie im Skript angegeben haben. Der Download enthält eine VHD-Datei (für virtuelle Computer) oder eine ZIP-Datei (für VM-Erweiterungen und Ressourcenanbieter). Darüber hinaus kann auch ein Katalogpaket im Format *AZPKG* enthalten sein, bei dem es sich einfach um eine ZIP-Datei handelt.

13. Im Falle eines Downloadfehlers können Sie den Vorgang mithilfe des folgenden PowerShell-Cmdlets erneut ausführen:

   ```powershell
   # for Marketplace items
   Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes"

   # for Resource providers
   Export-AzSOfflineResourceProvider -Destination "Destination folder path in quotes"
   ```

   Entfernen Sie vor der Wiederholung den Produktordner, in dem der Fehler beim Herunterladen aufgetreten ist. Wenn das Downloadskript beispielsweise beim Herunterladen in **D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1** fehlschlägt, entfernen Sie den Ordner **D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1** , und führen Sie das Cmdlet erneut aus.

### <a name="import-the-download-and-publish-to-azure-stack-marketplace-using-powershell"></a>Importieren des Downloads und Veröffentlichen im Azure Stack-Marketplace mithilfe von PowerShell

1. Die [zuvor heruntergeladenen](#use-the-marketplace-syndication-tool-to-download-marketplace-items) Dateien müssen an einen lokalen Speicherort verschoben werden, damit sie für Ihre Azure Stack-Umgebung zur Verfügung stehen. Das Tool für die Marketplace-Syndikation muss ebenfalls für Ihre Azure Stack-Umgebung verfügbar sein, da es für den Importvorgang benötigt wird.

   Die folgende Abbildung zeigt ein Beispiel für eine Ordnerstruktur. **D:\downloadfolder** enthält alle heruntergeladenen Marketplace-Elemente. Jeder Unterordner ist ein Marketplace-Element (z. B. **microsoft.custom-script-linux-arm-2.0.3**) und ist nach der Produkt-ID benannt. In den Unterordnern befindet sich jeweils der heruntergeladene Inhalt des Marketplace-Elements.

   ![Verzeichnisstruktur für Marketplace-Downloads](media/azure-stack-download-azure-marketplace-item/mp1.png)

2. Gehen Sie gemäß der Anleitung in [diesem Artikel](azure-stack-powershell-configure-admin.md) vor, um die PowerShell-Sitzung für Azure Stack-Betreiber zu konfigurieren.

3. Führen Sie das folgende Skript aus, um das Syndikationsmodul zu importieren und das Tool für die Marketplace-Syndikation zu starten:

   ```powershell
   $credential = Get-Credential -Message "Enter the azure stack operator credential:"
   Import-AzSOfflineMarketplaceItem -origin "marketplace content folder" -AzsCredential $credential
   ```

   Der Parameter `-origin` gibt den Ordner auf der obersten Ebene an, der alle heruntergeladenen Produkte enthält, z. B, `"D:\downloadfolder"`.

   Der Parameter `-AzsCredential` ist optional. Dieser Parameter dient dazu, das Zugriffstoken zu verlängern, falls es abgelaufen ist. Wenn das Token abläuft und der Parameter `-AzsCredential` nicht angegeben ist, werden Sie zur Eingabe der Anmeldeinformationen des Bedieners aufgefordert.

   > [!NOTE]
   > AD FS unterstützt nur die interaktive Authentifizierung mit Benutzeridentitäten. Wenn ein Objekt mit Anmeldeinformationen erforderlich ist, müssen Sie einen Dienstprinzipalnamen (SPN) verwenden. Weitere Informationen zum Einrichten eines Dienstprinzipals mit Azure Stack und AD FS als Identitätsverwaltungsdienst finden Sie unter [Verwalten eines AD FS-Dienstprinzipals](azure-stack-create-service-principals.md#manage-an-ad-fs-service-principal).

4. Nach erfolgreicher Ausführung des Skripts sollte das Element über Azure Stack-Marketplace verfügbar sein.
