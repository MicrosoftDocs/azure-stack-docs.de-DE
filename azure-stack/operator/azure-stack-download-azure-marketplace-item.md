---
title: Herunterladen von Marketplace-Elementen aus Azure und Veröffentlichen in Azure Stack Hub
description: Es wird beschrieben, wie Sie Marketplace-Elemente aus Azure herunterladen und in Azure Stack Hub veröffentlichen.
author: sethmanheim
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 12/23/2019
ms.openlocfilehash: a4882ca540b2a72d77195ee12a5d5ae0be87931d
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77700016"
---
# <a name="download-marketplace-items-to-azure-stack-hub"></a>Herunterladen von Marketplace-Elementen in Azure Stack Hub 

Als Cloudbetreiber können Sie Elemente aus dem Marketplace in Azure Stack Hub herunterladen und mithilfe der Azure Stack Hub-Umgebung für alle Benutzer verfügbar machen. Die zur Auswahl stehenden Optionen stammen aus einer speziell zusammengestellten Liste mit Azure Marketplace-Elementen, die vorab getestet wurden und für Azure Stack Hub geeignet sind. Dieser Liste werden regelmäßig weitere Elemente hinzugefügt. Prüfen Sie daher, ob neue Inhalte verfügbar sind.

Es gibt zwei Szenarien für das Herunterladen von Marketplace-Produkten:

- **Verbundenes Szenario**: Hierfür muss Ihre Azure Stack Hub-Umgebung mit dem Internet verbunden sein. Elemente werden über das Azure Stack Hub-Administratorportal gesucht und heruntergeladen.
- **Nicht verbundenes oder partiell verbundenes Szenario**: Für dieses Szenario müssen Sie mit dem Tool für die Marketplace-Syndikation auf das Internet zugreifen, um Marketplace-Elemente herunterzuladen. Anschließend übertragen Sie Ihre Downloads in die nicht verbundene Azure Stack Hub-Installation. Für dieses Szenario wird PowerShell verwendet.

Eine komplette Liste der zum Download verfügbaren Marketplace-Elemente finden Sie unter [Für Azure Stack Hub verfügbare Azure Marketplace-Elemente](azure-stack-marketplace-azure-items.md). Eine Liste mit den aktuellen Ergänzungen, Entfernungen und Updates in Azure Stack Hub Marketplace finden Sie unter [Änderungen im Azure Stack Hub-Marketplace](azure-stack-marketplace-changes.md).

> [!NOTE]
> Der Katalog unterscheidet sich je nach der Cloud, mit der Ihr Azure Stack Hub-System verbunden ist. Die Cloudumgebung wird durch das Azure-Abonnement bestimmt, das Sie zum Registrieren von Azure Stack Hub verwenden.

## <a name="connected-scenario"></a>Verbundenes Szenario

Wenn Azure Stack Hub eine Verbindung mit dem Internet herstellt, können Sie mithilfe des Administratorportals Marketplace-Elemente herunterladen.

### <a name="prerequisites"></a>Voraussetzungen

Ihre Azure Stack Hub-Bereitstellung muss über eine Internetverbindung verfügen und bei Azure registriert sein.

### <a name="use-the-portal-to-download-marketplace-items"></a>Herunterladen von Marketplace-Elementen mithilfe des Portals

1. Melden Sie sich beim Azure Stack Hub-Administratorportal an.

2. Überprüfen Sie den verfügbaren Speicherplatz, bevor Sie Marketplace-Elemente herunterladen. Wenn Sie später Elemente zum Download auswählen, können Sie die Downloadgröße mit der verfügbaren Speicherkapazität vergleichen. Ziehen Sie bei eingeschränkter Kapazität Optionen für die [Verwaltung des verfügbaren Speicherplatzes](azure-stack-manage-storage-shares.md#manage-available-space) in Betracht.

   Überprüfen des verfügbaren Speicherplatzes: Wählen Sie unter **Regionsverwaltung** die entsprechende Region aus, und navigieren Sie dann zu **Ressourcenanbieter** > **Speicher**:

   ![Überprüfen des Speicherplatzes im Azure Stack Hub-Administratorportal](media/azure-stack-download-azure-marketplace-item/storage.png)

3. Öffnen Sie den Azure Stack Hub-Marketplace, und stellen Sie eine Verbindung mit Azure her. Wählen Sie dazu den **Marketplace-Verwaltungsdienst** aus, wählen Sie **Marketplace-Elemente** aus, und wählen Sie dann **Aus Azure hinzufügen** aus:

   ![Hinzufügen von Marketplace-Elementen aus Azure](media/azure-stack-download-azure-marketplace-item/marketplace.png)

4. Jede Position zeigt auch die derzeit verfügbare Version. Wenn mehrere Versionen eines Marketplace-Elements verfügbar sind, ist in der Spalte **Version** **Mehrere** angegeben. Sie können auf die einzelnen Elemente klicken, um eine Beschreibung und zusätzliche Informationen anzuzeigen, u. a. die Downloadgröße:

   ![Hinzufügen aus Azure](media/azure-stack-download-azure-marketplace-item/add-from-azure1.png)

5. Wenn die Version eines Elements als **Mehrere** angegeben ist, können Sie das Element auswählen und in der daraufhin angezeigten Dropdownliste eine bestimmte Version wählen:

   ![Hinzufügen aus Azure](media/azure-stack-download-azure-marketplace-item/add-from-azure3.png)

6. Wählen Sie das gewünschte Element aus, und klicken Sie auf **Weiter**. Die Downloadzeiten variieren und hängen von der Netzwerkkonnektivität ab. Wenn der Download abgeschlossen ist, können Sie das neue Marketplace-Element entweder als Azure Stack Hub-Bediener oder -Benutzer bereitstellen.

7. Klicken Sie zum Bereitstellen des heruntergeladenen Elements auf **+ Ressource erstellen**, und suchen Sie in den Kategorien nach dem neuen Marketplace-Element. Wählen Sie als Nächstes das Element aus, um den Bereitstellungsprozess zu starten. Der Prozess variiert für verschiedene Marketplace-Elemente.

## <a name="disconnected-or-a-partially-connected-scenario"></a>Nicht verbundenes oder partiell verbundenes Szenario

Wenn Azure Stack Hub über begrenzte oder keine Internetkonnektivität verfügt, verwenden Sie PowerShell und das *Tool für die Marketplace-Syndikation*, um die Marketplace-Elemente auf einen Computer mit Internetkonnektivität herunterzuladen. Anschließend übertragen Sie die Elemente in Ihre Azure Stack Hub-Umgebung. In einer nicht verbundenen Umgebung können Sie Marketplace-Elemente nicht mit dem Azure Stack Hub-Portal herunterladen.

Das Tool für die Marketplace-Syndikation kann auch in einem verbundenen Szenario verwendet werden.

Dieses Szenario besteht aus zwei Teilen:

- **Teil 1**: Herunterladen von Elementen aus dem Marketplace. Sie Konfigurieren auf dem Computer mit Internetzugriff PowerShell und laden das Tool für die Syndikation und anschließend Elemente aus Azure Marketplace herunter.
- **Teil 2**: Hochladen und Veröffentlichen in Azure Stack Hub-Marketplace. Sie verschieben die heruntergeladenen Dateien in die Azure Stack Hub-Umgebung und veröffentlichen sie anschließend im Azure Stack Hub-Marketplace.

### <a name="prerequisites"></a>Voraussetzungen

- Eine verbundene Umgebung (muss nicht Azure Stack Hub sein). Sie müssen verbunden sein, um die Liste der Produkte von Azure mit Details abrufen und alle Elemente lokal herunterladen zu können. Für die übrigen Schritte ist keine Internetkonnektivität erforderlich. Ein Katalog mit den von Ihnen zuvor heruntergeladenen Elementen wird erstellt, den Sie in Ihrer nicht verbundenen Umgebung verwenden können.

- Wechselmedien, um eine Verbindung mit der getrennten Umgebung herzustellen und alle erforderlichen Artefakte zu übertragen.

- Eine nicht verbundene Azure Stack Hub-Umgebung mit den folgenden Voraussetzungen:

  - Ihre Azure Stack Hub-Bereitstellung muss bei Azure registriert sein.

  - Auf dem Computer mit Internetverbindung muss **mindestens Version 1.2.11 des Azure Stack Hub-PowerShell-Moduls**  installiert sein. [Installieren Sie die Azure Stack Hub-spezifischen PowerShell-Module](azure-stack-powershell-install.md), falls diese noch nicht vorhanden sind.

  - Die [PowerShell-Umgebung für den Azure Stack Hub-Bediener](azure-stack-powershell-configure-admin.md) muss konfiguriert sein, um den Import eines heruntergeladenen Marketplace-Elements zu ermöglichen.

- Laden Sie mit dem folgenden Befehl das Modul „Azs.Syndication.Admin“ aus dem PowerShell-Katalog herunter:
  ```
  Install-Module -Name Azs.Syndication.Admin
  ```

Nach Ihrer Registrierung Ihrer Azure Stack-Instanz können Sie die folgende Meldung ignorieren, die auf dem Blatt für die Marketplace-Verwaltung angezeigt wird, da sie für den Anwendungsfall mit getrennter Umgebung nicht relevant ist:

![Marketplace-Verwaltung](media/azure-stack-download-azure-marketplace-item/toolsmsg.png)

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>Herunterladen von Marketplace-Elementen mithilfe des Tools für die Marketplace-Syndikation

> [!IMPORTANT]
> Jedes Mal, wenn Sie Marketplace-Elemente in einem nicht verbundenen Szenario herunterladen, müssen Sie das Tool für die Marketplace-Syndikation herunterladen. An diesem Tool werden häufig Änderungen vorgenommen, und es sollte immer die aktuelle Version für jeden Download verwendet werden.

1. Öffnen Sie auf einem Computer mit Internetverbindung eine PowerShell-Konsole als Administrator.

2. Melden Sie sich mit dem Azure-Konto, das Sie zum Registrieren von Azure Stack Hub verwendet haben, bei der entsprechenden Azure-Cloud und dem entsprechenden Azure AD-Mandanten an. Führen Sie zum Hinzufügen des Kontos in PowerShell **Add-AzureRmAccount** aus. 

   ```powershell  
   Login-AzureRmAccount -Environment AzureCloud -Tenant '<mydirectory>.onmicrosoft.com'
   ```
   Sie werden aufgefordert, Ihre Anmeldeinformationen für das Azure-Konto einzugeben. Je nach Konfiguration Ihres Kontos müssen Sie ggf. die zweistufige Authentifizierung verwenden.

   > [!NOTE]
   > Wenn Ihre Sitzung abgelaufen ist, Ihr Kennwort geändert wurde oder Sie lediglich Konten wechseln möchten, führen Sie das folgende Cmdlet aus, bevor Sie sich mit **Add-AzureRmAccount** anmelden: **Remove-AzureRmAccount-Scope Process**.

3. Sollten Sie über mehrere Abonnements verfügen, führen Sie den folgenden Befehl aus, um das für die Registrierung verwendete Abonnement auszuwählen:

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID 'Your Azure Subscription GUID' | Select-AzureRmSubscription
   ```

4. Laden Sie die aktuelle Version des Tools für die Marketplace-Syndikation herunter, sofern Sie dies noch nicht im Schritt „Voraussetzungen“ getan haben:

   ```powershell
   Install-Module -Name Azs.Syndication.Admin
   ```

5. Führen Sie den folgenden Befehl aus, um die herunterzuladenden Marketplace-Elemente wie VM-Images, Erweiterungen oder Lösungsvorlagen auszuwählen. 

   ```powershell
   $products = Select-AzsMarketplaceItem
   ```

   Dadurch wird eine Tabelle angezeigt, in der alle Azure Stack-Registrierungen aufgelistet sind, die im ausgewählten Abonnement verfügbar sind. Wählen Sie die Registrierung aus, die der Azure Stack-Umgebung entspricht, für die Sie die Marketplace-Elemente herunterladen, und wählen Sie **OK** aus.

     ![Auswählen der Azure Stack-Registrierungen](media/azure-stack-download-azure-marketplace-item/select-registration.png)

   Nun sollte eine zweite Tabelle mit allen Marketplace-Elementen angezeigt werden, die für den Download verfügbar sind. Wählen Sie das Element aus, das Sie herunterladen möchten, und notieren Sie sich die **Version**. Sie können **STRG** gedrückt halten, um mehrere Images auszuwählen.
     ![Auswählen der Azure Stack-Registrierungen](media/azure-stack-download-azure-marketplace-item/select-products.png)
  
   Die Liste mit den Images kann mithilfe der Option **Kriterien hinzufügen** gefiltert werden.
   ![Auswählen der Azure Stack-Registrierungen](media/azure-stack-download-azure-marketplace-item/select-products-with-filter.png)

   Nachdem Sie Ihre Auswahl getroffen haben, wählen Sie „OK“ aus.

6. Die IDs für die Marketplace-Elemente, die Sie für den Download ausgewählt haben, werden in der Variablen `$products` gespeichert. Verwenden Sie den folgenden Befehl, um den Download der ausgewählten Elemente zu starten. Ersetzen Sie „Destination folder path in quotes“ durch einen Speicherort, an dem die aus dem Azure-Marketplace heruntergeladenen Dateien gespeichert werden sollen:

    ```powershell
    $products | Export-AzsMarketplaceItem  -RepositoryDir "Destination folder path in quotes"
    ```

7. Die Dauer des Downloads hängt von der Größe des Elements ab. Nach Abschluss des Downloads ist das Element in dem Ordner verfügbar, den Sie im Skript angegeben haben. Der Download enthält eine VHD-Datei (für virtuelle Computer) oder eine ZIP-Datei (für VM-Erweiterungen und Ressourcenanbieter). Darüber hinaus kann auch ein Katalogpaket im Format*AZPKG* enthalten sein, bei dem es sich um eine ZIP-Datei handelt.

8. Im Falle eines Downloadfehlers können Sie den Vorgang mithilfe des folgenden PowerShell-Cmdlets erneut ausführen:

    ```powershell
    $products | Export-AzsMarketplaceItem  -RepositoryDir "Destination folder path in quotes"
    ```

9. Es empfiehlt sich, auch das Modul **Azs.Syndication.Admin** lokal zu exportieren, damit Sie es auf den Computer kopieren können, auf dem Sie Marketplace-Elemente in Azure Stack Hub importieren.

   > [!NOTE]
   > Der Zielordner für den Export dieses Moduls muss sich von dem Speicherort unterscheiden, an den Sie die Marketplace-Elemente exportiert haben.

    ```powershell
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Azs.Syndication.Admin -Path "Destination folder path in quotes" -Force
    ```

### <a name="import-the-download-and-publish-to-azure-stack-hub-marketplace-using-powershell"></a>Importieren des Downloads und Veröffentlichen im Azure Stack Hub-Marketplace mithilfe von PowerShell

1. Die[zuvor lokal heruntergeladenen](#use-the-marketplace-syndication-tool-to-download-marketplace-items) Dateien müssen auf einen Computer verschoben werden, der mit Ihrer Azure Stack Hub-Umgebung verbunden ist. Das Tool für die Marketplace-Syndikation muss ebenfalls für Ihre Azure Stack Hub-Umgebung verfügbar sein, da es für den Importvorgang benötigt wird.

   Die folgende Abbildung zeigt ein Beispiel für die Ordnerstruktur. **D:\downloadfolder** enthält alle heruntergeladenen Marketplace-Elemente. Jeder Unterordner ist ein Marketplace-Element (z. B.**microsoft.custom-script-linux-arm-2.0.3**) und ist nach der Produkt-ID benannt. In den Unterordnern befindet sich jeweils der heruntergeladene Inhalt des Marketplace-Elements.

   ![Verzeichnisstruktur für Marketplace-Downloads](media/azure-stack-download-azure-marketplace-item/mp1.png)

2. Gehen Sie gemäß der Anleitung in [diesem Artikel](azure-stack-powershell-configure-admin.md) vor, um die PowerShell-Sitzung für Azure Stack Hub-Bediener zu konfigurieren.

3. Melden Sie sich bei Ihrer Azure Stack Hub-Instanz mit einer Identität an, die über Besitzerzugriff auf das Standardabonnement des Anbieters verfügt.

4. Führen Sie das folgende Skript aus, um das Syndikationsmodul zu importieren und das Tool für die Marketplace-Syndikation zu starten:

    ```powershell
    Import-AzsMarketplaceItem -RepositoryDir "Source folder path in quotes"
    ```

5. Nach erfolgreicher Ausführung des Skripts sollten die Marketplace-Elemente über den Azure Stack Hub-Marketplace verfügbar sein.
