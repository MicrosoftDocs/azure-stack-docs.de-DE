---
title: Verwenden der Datenübertragungstools im Azure Stack Hub-Speicher
description: Hier erfahren Sie mehr über die Datenübertragungstools für Azure Stack Hub-Speicher.
author: mattbriggs
ms.topic: conceptual
ms.date: 11/22/2020
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 11/22/2020
ms.openlocfilehash: d35ee0999dfa25e5cee12ff3df3c91b945733430
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95518023"
---
# <a name="use-data-transfer-tools-in-azure-stack-hub-storage"></a>Verwenden der Datenübertragungstools im Azure Stack Hub-Speicher

Azure Stack Hub bietet eine Reihe von Speicherdiensten für Datenträger, Blobs, Tabellen und Warteschlangen sowie Funktionen zur Kontoverwaltung. Mit einer Reihe von Azure Storage-Tools können Sie Daten verwalten oder in bzw. aus Azure Stack Hub-Speicher verschieben. Dieser Artikel enthält eine Übersicht über die verfügbaren Tools.

Ihre Anforderungen bestimmen, welche der folgenden Tools sich am besten für Sie eignen:

* [AzCopy](#azcopy)

    Ein speicherspezifisches Befehlszeilenprogramm, das Sie herunterladen können, um Daten zwischen Objekten im gleichen oder in einem anderen Speicherkonto zu kopieren.

* [Azure PowerShell](#azure-powershell)

    Eine aufgabenbasierte Befehlszeilenshell und Skriptsprache, die speziell für die Systemverwaltung entwickelt wurde.

* [Azure-Befehlszeilenschnittstelle](#azure-cli)

    Ein plattformübergreifendes Tool auf Open-Source-Basis, das eine Reihe von Befehlen zum Arbeiten mit der Azure- und Azure Stack Hub-Plattform bietet.

* [Microsoft Azure Storage-Explorer](#microsoft-azure-storage-explorer)

    Eine benutzerfreundliche eigenständige App mit einer Benutzeroberfläche.

* [blobfuse](#blobfuse)

    Ein virtueller Dateisystemtreiber für Azure Blob Storage, der Ihnen den Zugriff auf vorhandene Blockblobdaten in Ihrem Speicherkonto über das Linux-Dateisystem ermöglicht.

Aufgrund der Unterschiede bei den Speicherdiensten zwischen Azure und Azure Stack Hub gelten für jedes der in den folgenden Abschnitten beschriebenen Tools möglicherweise besondere Anforderungen. Einen Vergleich zwischen Azure Stack Hub-Speicher und Azure Storage finden Sie unter [Azure Stack Hub-Speicher: Unterschiede und Überlegungen](azure-stack-acs-differences.md).

## <a name="azcopy"></a>AzCopy

AzCopy ist ein Befehlszeilenprogramm zum Kopieren von Daten in bzw. aus Microsoft Azure Blob und Table Storage. Hierzu werden einfache Befehle mit optimaler Leistung verwendet. Sie können Daten aus einem Objekt in ein anderes im gleichen oder in einem anderen Speicherkonto kopieren.

### <a name="download-and-install-azcopy"></a>Herunterladen und Installieren von AzCopy

::: moniker range=">=azs-1811"
* Laden Sie für das Update 1811 oder neuere Versionen [AzCopy V10+](/azure/storage/common/storage-use-azcopy-v10#download-azcopy) herunter.
::: moniker-end

::: moniker range="<azs-1811"
* Laden Sie für vorherige Versionen (Update 1802 bis 1809) [AzCopy 7.1.0](https://aka.ms/azcopyforazurestack20170417) herunter.
::: moniker-end

### <a name="azcopy-101-configuration-and-limits"></a>Konfiguration und Einschränkungen von AzCopy 10.1

AzCopy 10.1 kann jetzt so konfiguriert werden, dass ältere API-Versionen verwendet werden können. Dies ermöglicht (eingeschränkte) Unterstützung für Azure Stack Hub.
Um die API-Version für AzCopy für die Unterstützung von Azure Stack Hub zu konfigurieren, legen Sie die Umgebungsvariable `AZCOPY_DEFAULT_SERVICE_API_VERSION` auf `2017-11-09` fest.

| Betriebssystem | Get-Help  |
|--------|-----------|
| **Windows** | Verwenden Sie in einer Eingabeaufforderung: `set AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09`<br> Verwenden Sie in PowerShell: `$env:AZCOPY_DEFAULT_SERVICE_API_VERSION="2017-11-09"`|
| **Linux** | `export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09` |
| **MacOS** | `export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09` |

In AzCopy 10.1 werden die folgenden Funktionen für Azure Stack Hub unterstützt:

| Funktion | Unterstützte Aktionen |
| --- | --- |
|Container verwalten|Erstellen eines Containers<br>Auflisten der Inhalte von Containern
|Auftrag verwalten|Anzeigen von Aufträgen<br>Fortsetzen eines Auftrags
|Blob entfernen|Entfernen eines einzelnen Blobs<br>Entfernen eines ganzen oder teilweisen virtuellen Verzeichnisses
|Hochladen der Datei|Hochladen einer Datei<br>Hochladen eines Verzeichnisses<br>Hochladen der Inhalte eines Verzeichnisses
|Herunterladen der Datei|Herunterladen einer Datei<br>Herunterladen eines Verzeichnisses<br>Herunterladen der Inhalte eines Verzeichnisses
|Datei synchronisieren|Synchronisieren eines Containers mit einem lokalen Dateisystem<br>Synchronisieren eines lokalen Dateisystem mit einem Container

   > [!NOTE]
   > * Azure Stack Hub unterstützt keine Bereitstellung von Anmeldeinformationen für die Autorisierung für AzCopy mithilfe von Azure Active Directory (AD). Sie müssen für den Zugriff auf Speicherobjekte in Azure Stack Hub ein SAS-Token (Shared Access Signature) verwenden.
   > * Azure Stack Hub unterstützt keine synchrone Datenübertragung zwischen zwei Blobspeicherorten in Azure Stack Hub sowie zwischen Azure Storage und Azure Stack Hub. Sie können „azcopy cp“ nicht verwenden, um Daten direkt mit AzCopy 10.1 von Azure Stack Hub in Azure Storage (oder umgekehrt) zu verschieben.

### <a name="azcopy-command-examples-for-data-transfer"></a>AzCopy-Beispielbefehle für die Datenübertragung

Die folgenden Beispiele behandeln typische Szenarien für das Kopieren von Daten in und aus Azure Stack Hub-Blobs. Weitere Informationen finden Sie unter [Erste Schritte mit AzCopy](/azure/storage/common/storage-use-azcopy-v10).

### <a name="download-all-blobs-to-a-local-disk"></a>Herunterladen aller Blobs auf einen lokalen Datenträger

```
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive=true
```

### <a name="upload-single-file-to-virtual-directory"></a>Hochladen einer einzelnen Datei in ein virtuelles Verzeichnis

```
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

### <a name="azcopy-known-issues"></a>Bekannte Probleme mit AzCopy

 - AzCopy-Vorgänge für Dateispeicher sind nicht verfügbar, da in Azure Stack Hub noch kein Dateispeicher verfügbar ist.
 - Wenn Sie Daten zwischen zwei Blobspeicherorten in Azure Stack Hub oder mithilfe von AzCopy 10.1 zwischen Azure Stack Hub und Azure Storage übertragen möchten, müssen Sie die Daten zuerst an einen lokalen Speicherort herunterladen und dann erneut in das Zielverzeichnis in Azure Stack Hub oder in Azure Storage hochladen. Oder Sie können AzCopy 7.1 verwenden und die Übertragung mit der Option **/SyncCopy** angeben, um die Daten zu kopieren.  
 - Die Linux-Version von AzCopy unterstützt nur das Update 1802 oder höhere Versionen und unterstützt den Tabellenspeicherdienst nicht.
 - Wenn Sie Daten in und aus Ihrem Azure Table-Speicherdienst kopieren möchten, [installieren Sie AzCopy, Version 7.3.0](https://aka.ms/azcopyforazurestack20171109).
 
## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell ist ein Modul, das Cmdlets für die Verwaltung von Diensten in Azure und Azure Stack Hub bereitstellt. Bei diesem Modul handelt es sich um eine aufgabenbasierte Befehlszeilenshell und Skriptsprache, die speziell für die Systemverwaltung entwickelt wurde.

### <a name="install-and-configure-powershell-for-azure-stack-hub"></a>Installieren und Konfigurieren von PowerShell für die Verwendung mit Azure Stack Hub

Für die Verwendung von Azure Stack Hub sind mit Azure Stack Hub kompatible Azure PowerShell-Module erforderlich. Weitere Informationen finden Sie unter [Installieren von PowerShell für Azure Stack Hub](../operator/powershell-install-az-module.md) und [Konfigurieren der PowerShell-Umgebung des Azure Stack Hub-Benutzers](azure-stack-powershell-configure-user.md).

### <a name="powershell-sample-script-for-azure-stack-hub"></a>PowerShell-Beispielskript für Azure Stack Hub 
### <a name="az-modules"></a>[Az-Module](#tab/az1)

In diesem Beispiel wird davon ausgegangen, dass Sie die [Installation von PowerShell für Azure Stack Hub](../operator/powershell-install-az-module.md) erfolgreich durchgeführt haben. Dieses Skript unterstützt Sie beim Abschluss der Konfiguration und fordert Sie zur Eingabe Ihrer Azure Stack Hub-Mandantenanmeldeinformationen auf, um Ihr Konto der lokalen PowerShell-Umgebung hinzuzufügen. Als Nächstes legt das Skript das standardmäßige Azure-Abonnement fest, erstellt ein neues Speicherkonto in Azure, erstellt in dem neuen Speicherkonto einen neuen Container und lädt eine vorhandene Imagedatei (Blob) in diesen Container hoch. Nachdem das Skript alle Blobs in diesem Container aufgelistet hat, erstellt es ein neues Zielverzeichnis auf dem lokalen Computer und lädt dann die Image-Datei herunter.

1. Installieren Sie [mit Azure Stack Hub kompatible Azure PowerShell-Module](../operator/powershell-install-az-module.md).
2. Laden Sie die [Tools herunter, die für die Arbeit mit Azure Stack Hub benötigt werden](../operator/azure-stack-powershell-download.md).
3. Öffnen Sie **Windows PowerShell ISE**, wählen Sie **Als Administrator ausführen**, und klicken Sie dann auf **Datei** > **Neu**, um eine neue Skriptdatei zu erstellen.
4. Kopieren Sie das folgende Skript, und fügen Sie es in die neue Skriptdatei ein.
5. Aktualisieren Sie die Skriptvariablen auf Basis der Konfigurationseinstellungen.
   > [!NOTE]
   > Dieses Skript muss im Stammverzeichnis für **AzureStack_Tools** ausgeführt werden.

```powershell  
# begin

$ARMEvnName = "AzureStackUser" # set AzureStackUser as your Azure Stack Hub environment name
$ARMEndPoint = "https://management.local.azurestack.external" 
$GraphAudience = "https://graph.windows.net/" 
$AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com" 

$SubscriptionName = "basic" # Update with the name of your subscription.
$ResourceGroupName = "myTestRG" # Give a name to your new resource group.
$StorageAccountName = "azsblobcontainer" # Give a name to your new storage account. It must be lowercase.
$Location = "Local" # Choose "Local" as an example.
$ContainerName = "photo" # Give a name to your new container.
$ImageToUpload = "C:\temp\Hello.jpg" # Prepare an image file and a source directory in your local computer.
$DestinationFolder = "C:\temp\download" # A destination directory in your local computer.

# Import the Connect PowerShell module"
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser -Force
Import-Module .\Connect\AzureStack.Connect.psm1

# Configure the PowerShell environment
# Register an Az environment that targets your Azure Stack Hub instance
Add-AzEnvironment -Name $ARMEvnName -ARMEndpoint $ARMEndPoint 

# Login
$TenantID = Get-AzsDirectoryTenantId -AADTenantName $AADTenantName -EnvironmentName $ARMEvnName
Add-AzAccount -EnvironmentName $ARMEvnName -TenantId $TenantID 

# Set a default Azure subscription.
Select-AzSubscription -SubscriptionName $SubscriptionName

# Create a new Resource Group 
New-AzResourceGroup -Name $ResourceGroupName -Location $Location

# Create a new storage account.
New-AzStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Location $Location -Type Standard_LRS

# Set a default storage account.
Set-AzCurrentStorageAccount -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName 

# Create a new container.
New-AzureStorageContainer -Name $ContainerName -Permission Off

# Upload a blob into a container.
Set-AzureStorageBlobContent -Container $ContainerName -File $ImageToUpload

# List all blobs in a container.
Get-AzureStorageBlob -Container $ContainerName

# Download blobs from the container:
# Get a reference to a list of all blobs in a container.
$blobs = Get-AzureStorageBlob -Container $ContainerName

# Create the destination directory.
New-Item -Path $DestinationFolder -ItemType Directory -Force  

# Download blobs into the local destination directory.
$blobs | Get-AzureStorageBlobContent -Destination $DestinationFolder

# end
```
### <a name="azurerm-modules"></a>[AzureRM-Module](#tab/azurerm1)

In diesem Beispiel wird davon ausgegangen, dass Sie die [Installation von PowerShell für Azure Stack Hub](../operator/azure-stack-powershell-install.md) erfolgreich durchgeführt haben. Dieses Skript unterstützt Sie beim Abschluss der Konfiguration und fordert Sie zur Eingabe Ihrer Azure Stack Hub-Mandantenanmeldeinformationen auf, um Ihr Konto der lokalen PowerShell-Umgebung hinzuzufügen. Als Nächstes legt das Skript das standardmäßige Azure-Abonnement fest, erstellt ein neues Speicherkonto in Azure, erstellt in dem neuen Speicherkonto einen neuen Container und lädt eine vorhandene Imagedatei (Blob) in diesen Container hoch. Nachdem das Skript alle Blobs in diesem Container aufgelistet hat, erstellt es ein neues Zielverzeichnis auf dem lokalen Computer und lädt dann die Image-Datei herunter.

1. Installieren Sie [mit Azure Stack Hub kompatible Azure PowerShell-Module](../operator/azure-stack-powershell-install.md).
2. Laden Sie die [Tools herunter, die für die Arbeit mit Azure Stack Hub benötigt werden](../operator/azure-stack-powershell-download.md).
3. Öffnen Sie **Windows PowerShell ISE**, wählen Sie **Als Administrator ausführen**, und klicken Sie dann auf **Datei** > **Neu**, um eine neue Skriptdatei zu erstellen.
4. Kopieren Sie das folgende Skript, und fügen Sie es in die neue Skriptdatei ein.
5. Aktualisieren Sie die Skriptvariablen auf Basis der Konfigurationseinstellungen.
   > [!NOTE]
   > Dieses Skript muss im Stammverzeichnis für **AzureStack_Tools** ausgeführt werden.

```powershell  
# begin

$ARMEvnName = "AzureStackUser" # set AzureStackUser as your Azure Stack Hub environment name
$ARMEndPoint = "https://management.local.azurestack.external" 
$GraphAudience = "https://graph.windows.net/" 
$AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com" 

$SubscriptionName = "basic" # Update with the name of your subscription.
$ResourceGroupName = "myTestRG" # Give a name to your new resource group.
$StorageAccountName = "azsblobcontainer" # Give a name to your new storage account. It must be lowercase.
$Location = "Local" # Choose "Local" as an example.
$ContainerName = "photo" # Give a name to your new container.
$ImageToUpload = "C:\temp\Hello.jpg" # Prepare an image file and a source directory in your local computer.
$DestinationFolder = "C:\temp\download" # A destination directory in your local computer.

# Import the Connect PowerShell module"
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser -Force
Import-Module .\Connect\AzureStack.Connect.psm1

# Configure the PowerShell environment
# Register an AzureRM environment that targets your Azure Stack Hub instance
Add-AzureRMEnvironment -Name $ARMEvnName -ARMEndpoint $ARMEndPoint 

# Login
$TenantID = Get-AzsDirectoryTenantId -AADTenantName $AADTenantName -EnvironmentName $ARMEvnName
Add-AzureRMAccount -EnvironmentName $ARMEvnName -TenantId $TenantID 

# Set a default Azure subscription.
Select-AzureRMSubscription -SubscriptionName $SubscriptionName

# Create a new Resource Group 
New-AzureRMResourceGroup -Name $ResourceGroupName -Location $Location

# Create a new storage account.
New-AzureRMStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Location $Location -Type Standard_LRS

# Set a default storage account.
Set-AzureRMCurrentStorageAccount -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName 

# Create a new container.
New-AzureRMureStorageContainer -Name $ContainerName -Permission Off

# Upload a blob into a container.
Set-AzureRMureStorageBlobContent -Container $ContainerName -File $ImageToUpload

# List all blobs in a container.
Get-AzureRMureStorageBlob -Container $ContainerName

# Download blobs from the container:
# Get a reference to a list of all blobs in a container.
$blobs = Get-AzureStorageBlob -Container $ContainerName

# Create the destination directory.
New-Item -Path $DestinationFolder -ItemType Directory -Force  

# Download blobs into the local destination directory.
$blobs | Get-AzureStorageBlobContent -Destination $DestinationFolder

# end
```

---



### <a name="powershell-known-issues"></a>PowerShell – Bekannte Probleme

Die kompatible Azure PowerShell-Modulversion für Azure Stack Hub ist derzeit 1.2.11 für Benutzervorgänge. Sie unterscheidet sich von der neuesten Version von Azure PowerShell. Dieser Unterschied wirkt sich wie folgt auf den Betrieb der Speicherdienste aus:

Das Format des Rückgabewerts von `Get-AzStorageAccountKey` in Version 1.2.11 hat zwei Eigenschaften: `Key1` und `Key2`. Die aktuelle Azure-Version gibt dagegen ein Array mit allen Kontoschlüsseln zurück.

```powershell
# This command gets a specific key for a storage account, 
# and works for Azure PowerShell version 1.4, and later versions.
(Get-AzStorageAccountKey -ResourceGroupName "RG01" `
-AccountName "MyStorageAccount").Value[0]

# This command gets a specific key for a storage account, 
# and works for Azure PowerShell version 1.3.2, and previous versions.
(Get-AzStorageAccountKey -ResourceGroupName "RG01" `
-AccountName "MyStorageAccount").Key1
```

Weitere Informationen finden Sie unter [Get-AzureRMStorageAccountKey](/powershell/module/Az.storage/Get-AzStorageAccountKey).

## <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

Die Azure CLI ist die Befehlszeilenumgebung von Azure und dient zum Verwalten von Azure-Ressourcen. Sie können sie unter macOS, Linux und Windows installieren und über die Befehlszeile ausführen.

Azure CLI ist für die Verwaltung von Azure-Ressourcen über die Befehlszeile sowie die Erstellung von Automatisierungsskripts für Azure Resource Manager optimiert. Sie bietet viele der im Azure Stack Hub-Portal vorhandenen Funktionen, so z.B. umfangreiche Datenzugriffsfunktionen.

Azure Stack Hub erfordert Azure CLI Version 2.0 oder höher. Weitere Informationen zum Installieren und Konfigurieren von Azure CLI mit Azure Stack Hub finden Sie im Artikel zum [Installieren und Konfigurieren der CLI für Azure Stack Hub](azure-stack-version-profiles-azurecli2.md). Weitere Informationen zur Verwendung der Azure CLI für einige Aufgaben in Verbindung mit Ressourcen in Ihrem Azure Stack Hub-Speicherkonto finden Sie unter [Verwenden der Azure CLI mit Azure Storage](/azure/storage/storage-azure-cli).

### <a name="azure-cli-sample-script-for-azure-stack-hub"></a>Azure CLI-Beispielskript für Azure Stack Hub

Wenn Sie die Installation und Konfiguration der CLI abgeschlossen haben, können Sie die folgenden Schritte ausprobieren, um mit einem kleinen Shell-Beispielskript für die Interaktion mit Azure Stack Hub-Speicherressourcen zu arbeiten. Dieses Skript führt folgende Aktionen aus:

* Erstellen eines Blobcontainers in Ihrem Speicherkonto
* Hochladen einer vorhandenen Datei (als Blob) in den Container
* Auflisten aller Blobs im Container
* Herunterladen der Datei in ein von Ihnen angegebenes Ziel auf dem lokalen Computer

Stellen Sie vor der Ausführung dieses Skripts sicher, dass Sie eine Verbindung mit der gewünschten Azure Stack Hub-Instanz herstellen und sich dort anmelden können.

1. Öffnen Sie Ihren bevorzugten Texteditor, in den Sie das kopierte vorhergehende Skript einfügen.
2. Aktualisieren Sie die Skriptvariablen entsprechend Ihren Konfigurationseinstellungen.
3. Nachdem Sie die erforderlichen Variablen aktualisiert haben, speichern Sie das Skript, und beenden Sie den Editor. In den nächsten Schritten wird vorausgesetzt, dass Sie Ihr **my_storage_sample.sh** genannt haben.
4. Markieren Sie das Skript bei Bedarf als ausführbar:`chmod +x my_storage_sample.sh`
5. Führen Sie das Skript aus. Beispielsweise in Bash:`./my_storage_sample.sh`

```azurecli
#!/bin/bash
# A simple Azure Stack Hub storage example script

export AZURESTACK_RESOURCE_GROUP=<resource_group_name>
export AZURESTACK_RG_LOCATION="local"
export AZURESTACK_STORAGE_ACCOUNT_NAME=<storage_account_name>
export AZURESTACK_STORAGE_CONTAINER_NAME=<container_name>
export AZURESTACK_STORAGE_BLOB_NAME=<blob_name>
export FILE_TO_UPLOAD=<file_to_upload>
export DESTINATION_FILE=<destination_file>

echo "Creating the resource group..."
az group create --name $AZURESTACK_RESOURCE_GROUP --location $AZURESTACK_RG_LOCATION

echo "Creating the storage account..."
az storage account create --name $AZURESTACK_STORAGE_ACCOUNT_NAME --resource-group $AZURESTACK_RESOURCE_GROUP --account-type Standard_LRS

echo "Creating the blob container..."
az storage container create --name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME

echo "Uploading the file..."
az storage blob upload --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --file $FILE_TO_UPLOAD --name $AZURESTACK_STORAGE_BLOB_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME

echo "Listing the blobs..."
az storage blob list --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME --output table

echo "Downloading the file..."
az storage blob download --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME --name $AZURESTACK_STORAGE_BLOB_NAME --file $DESTINATION_FILE --output table

echo "Done"
```

## <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage-Explorer

Azure Storage-Explorer ist eine eigenständige App von Microsoft. Mit ihr können Sie Daten aus Azure Storage und Azure Stack Hub-Speicher einfach auf Computern unter Windows, macOS und Linux nutzen. Microsoft Azure Storage-Explorer bietet eine komfortable Verwaltung für Ihre Azure Stack Hub-Speicherdaten.

* Weitere Informationen zum Konfigurieren von Azure Storage-Explorer für die Verwendung mit Azure Stack Hub finden Sie unter [Herstellen einer Verbindung von Storage-Explorer mit einem Azure Stack Hub-Abonnement](azure-stack-storage-connect-se.md).
* Weitere Informationen zum Microsoft Azure Storage-Explorer finden Sie unter [Erste Schritte mit dem Storage-Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer).

## <a name="blobfuse"></a>blobfuse 

[blobfuse](https://github.com/Azure/azure-storage-fuse) ist ein virtueller Dateisystemtreiber für Azure Blob Storage, der Ihnen den Zugriff auf vorhandene Blockblobdaten in Ihrem Speicherkonto über das Linux-Dateisystem ermöglicht. Azure Blob Storage ist ein Objektspeicherdienst und verfügt daher nicht über einen hierarchischen Namespace. Blobfuse stellt diesen Namespace mithilfe des Schemas für virtuelle Verzeichnisse bereit. Dabei wird als Trennzeichen ein Schrägstrich (`/`) verwendet. blobfuse funktioniert sowohl unter Azure als auch unter Azure Stack Hub. 

Weitere Informationen zum Bereitstellen von Blobspeicher als Dateisystem mit blobfuse unter Linux finden Sie unter [Einbinden von Blob Storage als Dateisystem mit blobfuse](/azure/storage/blobs/storage-how-to-mount-container-linux). 

Für Azure Stack Hub muss *blobEndpoint* neben „accountName“, „accountKey/sasToken“ und „containerName“ bei der Konfiguration der Anmeldeinformationen für das Speicherkonto angegeben werden.

Im Azure Stack Development Kit (ASDK) sollte *blobEndpoint* auf `myaccount.blob.local.azurestack.external` festgelegt werden. Wenden Sie sich hinsichtlich des integrierten Azure Stack Hub-Systems an Ihren Cloudadministrator, wenn Sie sich beim Endpunkt nicht sicher sind.

Beachten Sie, dass *accountKey* und *sasToken* nicht gleichzeitig konfiguriert werden können. Wenn ein Speicherkontoschlüssel angegeben wird, hat die Konfigurationsdatei für die Anmeldeinformationen das folgende Format:

```
accountName myaccount 
accountKey myaccesskey== 
containerName mycontainer 
blobEndpoint myaccount.blob.local.azurestack.external
```

Wenn ein SAS-Token angegeben wird, hat die Konfigurationsdatei für die Anmeldeinformationen das folgende Format:

```  
accountName myaccount 
sasToken ?mysastoken 
containerName mycontainer 
blobEndpoint myaccount.blob.local.azurestack.external
```

## <a name="next-steps"></a>Nächste Schritte

* [Herstellen einer Verbindung von Storage-Explorer mit einem Azure Stack Hub-Abonnement](azure-stack-storage-connect-se.md)
* [Erste Schritte mit dem Storage-Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Azure Stack-Speicher: Unterschiede und Überlegungen](azure-stack-acs-differences.md)
* [Einführung in Azure Storage](/azure/storage/common/storage-introduction)
