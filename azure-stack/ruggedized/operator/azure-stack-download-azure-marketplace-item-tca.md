---
title: Herunterladen von Marketplace-Elementen aus Azure und Veröffentlichen in Azure Stack | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie Marketplace-Elemente aus Azure herunterladen und in Azure Stack veröffentlichen.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/02/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 01/02/2020
ms.openlocfilehash: a3d8fcc1c3ffa65539b233a8b89f55d57f61a5ef
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910617"
---
# <a name="download-marketplace-items-to-azure-stack-hub-ruggedized"></a>Herunterladen von Marketplace-Elementen in Azure Stack Hub (Ruggedized)

Als Cloudbetreiber können Sie Elemente aus dem Marketplace in Azure Stack Hub herunterladen und mithilfe der Azure Stack Hub-Umgebung für alle Benutzer verfügbar machen. Die zur Auswahl stehenden Optionen stammen aus einer speziell zusammengestellten Liste mit Azure Marketplace-Elementen, die vorab getestet wurden und für Azure Stack geeignet sind. Dieser Liste werden regelmäßig weitere Elemente hinzugefügt. Prüfen Sie daher, ob neue Inhalte verfügbar sind.

Es gibt zwei Szenarien für das Herunterladen von Marketplace-Produkten:

- **Verbundenes Szenario**: Hierfür muss Ihre Azure Stack Hub-Umgebung mit dem Internet verbunden sein. Elemente werden über das Azure Stack Hub-Administratorportal gesucht und heruntergeladen.
- **Nicht verbundenes oder partiell verbundenes Szenario**: Für dieses Szenario müssen Sie mit dem Tool für die Marketplace-Syndikation auf das Internet zugreifen, um Marketplace-Elemente herunterzuladen. Anschließend übertragen Sie Ihre Downloads in die nicht verbundene Azure Stack-Installation. Für dieses Szenario wird PowerShell verwendet.

Eine komplette Liste der zum Download verfügbaren Marketplace-Elemente finden Sie unter [Für Azure Stack verfügbare Azure Marketplace-Elemente](../../operator/azure-stack-marketplace-azure-items.md). Eine Liste mit den aktuellen Ergänzungen, Entfernungen und Updates in Azure Stack Marketplace finden Sie unter [Änderungen im Azure Stack-Marketplace](../../operator/azure-stack-marketplace-changes.md).

> [!NOTE]
> Der Katalog unterscheidet sich je nach der Cloud, mit der Ihr Azure Stack Hub-System verbunden ist. Die Cloudumgebung wird durch das Azure-Abonnement bestimmt, das Sie zum Registrieren von Azure Stack Hub verwenden.

## <a name="connected-scenario"></a>Verbundenes Szenario

Wenn Azure Stack Hub eine Verbindung mit dem Internet herstellt, können Sie mithilfe des Administratorportals Marketplace-Elemente herunterladen.

### <a name="prerequisites"></a>Voraussetzungen

Ihre Azure Stack Hub-Bereitstellung muss über eine Internetverbindung verfügen und [bei Azure registriert](registration-tzl.md) sein.

### <a name="use-the-portal-to-download-marketplace-items"></a>Herunterladen von Marketplace-Elementen mithilfe des Portals

1. Melden Sie sich beim Azure Stack Hub-Administratorportal an.

2. Überprüfen Sie den verfügbaren Speicherplatz, bevor Sie Marketplace-Elemente herunterladen. Wenn Sie später Elemente zum Download auswählen, können Sie die Downloadgröße mit der verfügbaren Speicherkapazität vergleichen. Ziehen Sie bei eingeschränkter Kapazität Optionen für die [Verwaltung des verfügbaren Speicherplatzes](../../operator/azure-stack-manage-storage-shares.md#manage-available-space) in Betracht.

   Überprüfen des verfügbaren Speicherplatzes: Wählen Sie unter **Regionsverwaltung** die entsprechende Region aus, und navigieren Sie dann zu **Ressourcenanbieter** > **Speicher**:

   ![Überprüfen des Speicherplatzes im Azure Stack-Administratorportal](media/azure-stack-download-azure-marketplace-item-tzl/storage.png)

3. Öffnen Sie den Azure Stack Hub-Marketplace, und stellen Sie eine Verbindung mit Azure her. Wählen Sie dazu den **Marketplace-Verwaltungsdienst** aus, wählen Sie **Marketplace-Elemente** aus, und wählen Sie dann **Aus Azure hinzufügen** aus:

   ![Hinzufügen von Marketplace-Elementen aus Azure](media/azure-stack-download-azure-marketplace-item-tzl/marketplace.png)

4. Jede Position zeigt auch die derzeit verfügbare Version. Wenn mehrere Versionen eines Marketplace-Elements verfügbar sind, ist in der Spalte **Version** **Mehrere** angegeben. Sie können auf die einzelnen Elemente klicken, um eine Beschreibung und zusätzliche Informationen anzuzeigen, u. a. die Downloadgröße:

   ![Screenshot der Seite „Add from Azure“ (Aus Azure hinzufügen) mit hervorgehobenem Eintrag „Mehrere“ in der Spalte „Version“](media/azure-stack-download-azure-marketplace-item-tzl/add-from-azure1.png)

5. Wenn die Version eines Elements als **Mehrere** angegeben ist, können Sie das Element auswählen und in der daraufhin angezeigten Dropdownliste eine bestimmte Version wählen:

   ![Hinzufügen aus Azure](media/azure-stack-download-azure-marketplace-item-tzl/add-from-azure3.png)

6. Wählen Sie das gewünschte Element aus, und klicken Sie auf **Weiter**. Die Downloadzeiten variieren und hängen von der Netzwerkkonnektivität ab. Wenn der Download abgeschlossen ist, können Sie das neue Marketplace-Element entweder als Azure Stack-Betreiber oder -Benutzer bereitstellen.

7. Klicken Sie zum Bereitstellen des heruntergeladenen Elements auf **+ Ressource erstellen**, und suchen Sie in den Kategorien nach dem neuen Marketplace-Element. Wählen Sie als Nächstes das Element aus, um den Bereitstellungsprozess zu starten. Der Prozess variiert für verschiedene Marketplace-Elemente.

## <a name="disconnected-or-a-partially-connected-scenario"></a>Nicht verbundenes oder partiell verbundenes Szenario

In einer getrennten Umgebung können Sie Marketplace-Elemente nicht aus Azure herunterladen. Sie müssen das Offlinesyndikationstool verwenden, um die Marketplace-Elemente auf ein lokales Laufwerk herunterzuladen und diese Elemente von dort aus dann in Azure Stack Hub hochladen.

Sie können [die Offlinesyndikationstools hier herunterladen](https://aka.ms/azsSyndicationtool).

### <a name="download-marketplace-items-from-azure"></a>Herunterladen von Marketplace-Elementen aus Azure

#### <a name="prerequisites"></a>Voraussetzungen

- Azure PowerShell muss auf dem Computer installiert sein.

- Sie müssen über die Azure-Abonnement-ID, die Registrierungsressourcengruppe und den Registrierungsnamen verfügen, die Sie bei der Registrierung von Azure Stack Hub verwendet haben. Diese Informationen finden Sie im Azure Stack Hub-Operatorportal auf der Registerkarte **Eigenschaften** des Blatts **Region Management** (Regionsverwaltung).

- Sie benötigen den Benutzernamen und das Kennwort für das Konto mit Besitzer-/Mitwirkendenberechtigungen für die Azure-Abonnement-ID.

- Darüber hinaus ist ein lokales Laufwerk oder ein Netzwerkpfad erforderlich, auf das bzw. unter dem die Offlinedateien geschrieben werden können.

#### <a name="download-items"></a>Herunterladen von Elementen

1. Öffnen Sie PowerShell, und navigieren Sie zum extrahierten Ordner.

2. Führen Sie das PowerShell-Skript **Invoke-AzSMarketplaceDownload.ps1** aus:

    ```powershell
    .\Invoke-AzSMarketplaceDownload.ps1 -RegistrationSubscriptionId '<subscription ID>' ` 
       -RegistrationResourceGroup 'azurestack' -RegistrationName '<registration name>' `
       -TenantName mytenant.onmicrosoft.com -DownloadFolder 'F:\offlineSyndication'
    ```

    Wenn Sie sich bereits über Azure PowerShell angemeldet haben, können Sie alternativ den Azure-Kontext übergeben:

    ```powershell
    Add-AzureRmAccount -Environment AzureCloud -Tenant mytenant.onmicrosoft.com 
    .\Invoke-AzSMarketplaceDownload.ps1 -RegistrationResourceGroup 'azurestack' -RegistrationName '<registration name>' -DownloadFolder 'F:\offlineSyndication' -AzureContext $(Get-AzureRMContext)
    ```

    Wenn Sie den Azure-Kontext nicht übergeben, werden Sie aufgefordert, sich anzumelden.

3. Ein Fenster wird angezeigt, in dem Sie das Produkt auswählen können, das Sie herunterladen möchten. Wenn Sie beim Klicken die STRG-TASTE gedrückt halten, können Sie mehrere Elemente auswählen.

4. Klicken Sie auf **OK**. Hierdurch werden das Marketplace-Element und seine Abhängigkeiten heruntergeladen, sofern vorhanden.

### <a name="upload-marketplace-items-to-azure-stack-hub"></a>Hochladen von Marketplace-Elementen in Azure Stack Hub

#### <a name="prerequisites"></a>Voraussetzungen

- Resource Manager-Endpunkt des Azure Stack-Administrators und Verzeichnismandant

- Zugriff auf die Offline-Marketplace-Elemente

#### <a name="upload-items"></a>Hochladen von Elementen

1. Öffnen Sie PowerShell, und navigieren Sie zum extrahierten Ordner.

2. Führen Sie das PowerShell-Skript **Invoke-AzSMarketplaceUpload.ps1** aus:

    ```powershell
    .\Invoke-AzsMarketplaceUpload.ps1 -AzureStackCloudName "AzureStack-Admin" -AzureStackAdminARMEndpoint https://adminmanagement.<region>.<fqdn> -TenantName mytenant.onmicrosoft.com -DownloadFolder F:\offlineSyndication
    ```

    Alternativ können Sie die Azure Stack-Umgebung selbst in Azure PowerShell einrichten, sich beim Resource Manager-Endpunkt des Administrators authentifizieren und den Kontext an das Skript übergeben:

    ```powershell
    Add-AzureRmEnvironment -Name Redmond-Admin -ARMEndpoint https://adminmanagement.redmond.azurestack.corp.microsoft.com

    Add-AzureRmAccount -Environment Redmond-Admin

    .\Invoke-AzsMarketplaceUpload.ps1 -DownloadFolder F:\Downloads\offlining -AzureContext $(Get-AzureRmContext)
    ```

    Mit dieser Prozedur werden die Marketplace-Elemente in die angegebene Azure Stack Hub-Instanz hochgeladen.
