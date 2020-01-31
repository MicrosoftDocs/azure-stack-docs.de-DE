---
title: Erste Schritte mit den Azure Stack Hub-Speicherentwicklungstools
description: Anleitung für die ersten Schritte mit Azure Stack Hub-Speicher-Entwicklungstools
author: mattbriggs
ms.author: mabrigg
ms.date: 1/22/2020
ms.topic: conceptual
ms.reviewer: xiaofmao
ms.lastreviewed: 02/27/2019
ms.openlocfilehash: eed80065694512b0f180a4bc28be54d0cace12e7
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883159"
---
# <a name="get-started-with-azure-stack-hub-storage-development-tools"></a>Erste Schritte mit den Azure Stack Hub-Speicherentwicklungstools

Microsoft Azure Stack Hub bietet eine Reihe von Speicherdiensten einschließlich Blob-, Tabellen- und Warteschlangenspeicher.

Nutzen Sie diesen Artikel als Leitfaden für die ersten Schritte mit den Azure Stack Hub-Speicherentwicklungstools. Ausführlichere Informationen und Beispielcode finden Sie in den entsprechenden Azure-Speichertutorials.

> [!NOTE]  
> Es gibt Unterschiede zwischen Azure Stack Hub-Speicher und Azure-Speicher sowie besondere Anforderungen für die jeweilige Plattform. Beispielsweise gelten bestimmte Anforderungen in Bezug auf die Clientbibliotheken und Endpunktsuffixe für Azure Stack Hub. Weitere Informationen finden Sie unter [Azure Stack Hub-Speicher: Unterschiede und Überlegungen](azure-stack-acs-differences.md).

## <a name="azure-client-libraries"></a>Azure-Clientbibliotheken

Bei den Speicherclientbibliotheken müssen Sie auf die Version achten, die mit der REST-API kompatibel ist. Sie müssen auch den Azure Stack Hub-Endpunkt in Ihrem Code angeben.

::: moniker range=">=azs-1811"
### <a name="1811-update-or-newer-versions"></a>Update 1811 oder neuere Versionen

| Clientbibliothek | Von Azure Stack Hub unterstützte Version | Link | Endpunktspezifikation |
|----------------|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET | 9.2.0 | NuGet-Paket:<br><https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0><br> <br>GitHub-Release:<br><https://github.com/Azure/azure-storage-net/releases/tag/v9.2.0> | app.config-Datei |
| Java | 7.0.0 | Maven-Paket:<br><https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/7.0.0><br> <br>GitHub-Release:<br><https://github.com/Azure/azure-storage-java/releases/tag/v7.0.0> | Verbindungszeichenfolgen-Setup |
| Node.js | 2.8.3 | NPM-Link:<br><https://www.npmjs.com/package/azure-storage><br>(Ausführung: `npm install azure-storage@2.8.3`)<br> <br>GitHub-Release:<br><https://github.com/Azure/azure-storage-node/releases/tag/v2.8.3> | Dienstinstanzdeklaration |
| C++ | 5.2.0 | NuGet-Paket:<br><https://www.nuget.org/packages/Microsoft.Azure.Storage.CPP.v140/5.2.0><br> <br>GitHub-Release:<br><https://github.com/Azure/azure-storage-cpp/releases/tag/v5.2.0> | Verbindungszeichenfolgen-Setup |
| PHP | 1.2.0 | GitHub-Release:<br>Allgemein: <https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-common><br>Blob: <https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-blob><br>Queue:<br><https://github.com/Azure/azure-storage-php/releases/tag/v1.1.1-queue><br>Table: <https://github.com/Azure/azure-storage-php/releases/tag/v1.1.0-table><br> <br>Installation über Composer (Weitere Informationen finden Sie [weiter unten](#install-php-client-via-composer---current).) | Verbindungszeichenfolgen-Setup |
| Python | 1.1.0 | GitHub-Release:<br>Allgemein:<br><https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-common><br>Blob:<br><https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-blob><br>Queue:<br><https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-queue> | Dienstinstanzdeklaration |
| Ruby | 1.0.1 | RubyGems-Paket:<br>Allgemein:<br><https://rubygems.org/gems/azure-storage-common/versions/1.0.1><br>Blob: <https://rubygems.org/gems/azure-storage-blob/versions/1.0.1><br>Queue: <https://rubygems.org/gems/azure-storage-queue/versions/1.0.1><br>Table: <https://rubygems.org/gems/azure-storage-table/versions/1.0.1><br> <br>GitHub-Release:<br>Allgemein: <https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common><br>Blob: <https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob><br>Queue: <https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-queue><br>Table: <https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-table> | Verbindungszeichenfolgen-Setup |

#### <a name="install-php-client-via-composer---current"></a>Installation des PHP-Clients per Composer – Aktuell

Installation über Composer: (Verwenden Sie das Blob als Beispiel).

1. Erstellen Sie eine Datei mit dem Namen **composer.json** im Stammverzeichnis des Projekts mit folgendem Code:

    ```json
    {
      "require": {
      "Microsoft/azure-storage-blob":"1.2.0"
      }
    }
    ```

2. Laden Sie [composer.phar](https://getcomposer.org/composer.phar) in das Stammverzeichnis des Projekts herunter.
3. Führen Sie `php composer.phar install` aus.
::: moniker-end

::: moniker range=">=azs-1802 <=azs-1809"
### <a name="previous-versions-1802-to-1809-update"></a>Vorherige Versionen (1802 bis Update 1809)

| Clientbibliothek | Von Azure Stack Hub unterstützte Version | Link | Endpunktspezifikation |
|----------------|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET | 8.7.0 | NuGet-Paket:<br><https://www.nuget.org/packages/WindowsAzure.Storage/8.7.0><br> <br>GitHub-Release:<br><https://github.com/Azure/azure-storage-net/releases/tag/v8.7.0> | app.config-Datei |
| Java | 6.1.0 | Maven-Paket:<br><https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0><br> <br>GitHub-Release:<br><https://github.com/Azure/azure-storage-java/releases/tag/v6.1.0> | Verbindungszeichenfolgen-Setup |
| Node.js | 2.7.0 | NPM-Link:<br><https://www.npmjs.com/package/azure-storage><br>(Ausführung: `npm install azure-storage@2.7.0`)<br> <br>GitHub-Release:<br><https://github.com/Azure/azure-storage-node/releases/tag/v2.7.0> | Dienstinstanzdeklaration |
| C++ | 3.1.0 | NuGet-Paket:<br><https://www.nuget.org/packages/wastorage.v140/3.1.0><br> <br>GitHub-Release:<br><https://github.com/Azure/azure-storage-cpp/releases/tag/v3.1.0> | Verbindungszeichenfolgen-Setup |
| PHP | 1.0.0 | GitHub-Release:<br>Allgemein: <https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-common><br>Blob: <https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-blob><br>Queue:<br><https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-queue><br>Table: <https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-table><br> <br>Installation über Composer (Details finden Sie weiter unten.) | Verbindungszeichenfolgen-Setup |
| Python | 1.0.0 | GitHub-Release:<br>Allgemein:<br><https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common><br>Blob:<br><https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-blob><br>Queue:<br><https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-queue> | Dienstinstanzdeklaration |
| Ruby | 1.0.1 | RubyGems-Paket:<br>Allgemein:<br><https://rubygems.org/gems/azure-storage-common/versions/1.0.1><br>Blob: <https://rubygems.org/gems/azure-storage-blob/versions/1.0.1><br>Queue: <https://rubygems.org/gems/azure-storage-queue/versions/1.0.1><br>Table: <https://rubygems.org/gems/azure-storage-table/versions/1.0.1><br> <br>GitHub-Release:<br>Allgemein: <https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common><br>Blob: <https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob><br>Queue: <https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-queue><br>Table: <https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-table> | Verbindungszeichenfolgen-Setup |

#### <a name="install-php-client-via-composer---previous"></a>Installation des PHP-Clients per Composer – Vorherige

Installation per Composer: (Blob als Beispiel verwenden).

1. Erstellen Sie eine Datei mit dem Namen **composer.json** im Stammverzeichnis des Projekts mit folgendem Code:

   ```json
    {
      "require": {
      "Microsoft/azure-storage-blob":"1.0.0"
      }
    }
   ```

2. Laden Sie [composer.phar](https://getcomposer.org/composer.phar) in das Stammverzeichnis des Projekts herunter.
3. Führen Sie `php composer.phar install` aus.
:::moniker-end

## <a name="endpoint-declaration"></a>Endpunktdeklaration

Ein Azure Stack Hub-Endpunkt besteht aus zwei Teilen: dem Namen einer Region und der Azure Stack Hub-Domäne.
Im Azure Stack Development Kit ist der Standardendpunkt **local.azurestack.external**.
Wenden Sie sich an den Cloudadministrator, falls Sie nicht sicher sind, wie Ihr Endpunkt lautet.

## <a name="examples"></a>Beispiele

### <a name="net"></a>.NET

Für Azure Stack Hub ist das Endpunktsuffix in der Datei „app.config“ angegeben:

```xml
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=local.azurestack.external;" />
```

### <a name="java"></a>Java

Für Azure Stack Hub ist das Endpunktsuffix im Setup der Verbindungszeichenfolge angegeben:

```java
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=local.azurestack.external";
```

### <a name="nodejs"></a>Node.js

Für Azure Stack Hub ist das Endpunktsuffix in der Deklarationsinstanz angegeben:

```nodejs
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob.local.azurestack.external');
```

### <a name="c"></a>C++

Für Azure Stack Hub ist das Endpunktsuffix im Setup der Verbindungszeichenfolge angegeben:

```cpp
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=local.azurestack.external"));
```

### <a name="php"></a>PHP

Für Azure Stack Hub ist das Endpunktsuffix im Setup der Verbindungszeichenfolge angegeben:

```php
$connectionString = 'BlobEndpoint=https://<storage account name>.blob.local.azurestack.external/;
QueueEndpoint=https:// <storage account name>.queue.local.azurestack.external/;
TableEndpoint=https:// <storage account name>.table.local.azurestack.external/;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Für Azure Stack Hub ist das Endpunktsuffix in der Deklarationsinstanz angegeben:

```python
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix='local.azurestack.external')
```

### <a name="ruby"></a>Ruby

Für Azure Stack Hub ist das Endpunktsuffix im Setup der Verbindungszeichenfolge angegeben:

```ruby
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=local.azurestack.external
```

## <a name="blob-storage"></a>Blob Storage

Die folgenden Tutorials zu Azure Blob Storage gelten für Azure Stack Hub. Beachten Sie die im vorherigen Abschnitt [Beispiele](#examples) beschriebene bestimmte Endpunktsuffix-Voraussetzung für Azure Stack Hub.

* [Erste Schritte mit Azure Blob Storage mit .NET](/azure/storage/blobs/storage-dotnet-how-to-use-blobs)
* [Gewusst wie: Verwenden von Blob Storage mit Java](/azure/storage/blobs/storage-java-how-to-use-blob-storage)
* [Gewusst wie: Verwenden von Blob Storage mit Node.js](/azure/storage/blobs/storage-nodejs-how-to-use-blob-storage)
* [Verwenden des BLOB-Speichers mit C++](/azure/storage/blobs/storage-c-plus-plus-how-to-use-blobs)
* [Gewusst wie: Verwenden von Blob Storage mit PHP](/azure/storage/blobs/storage-php-how-to-use-blobs)
* [Verwenden von Azure Blob Storage mit Python](/azure/storage/blobs/storage-python-how-to-use-blob-storage)
* [Gewusst wie: Verwenden von Blob Storage mit Ruby](/azure/storage/blobs/storage-ruby-how-to-use-blob-storage)

## <a name="queue-storage"></a>Queue Storage

Die folgenden Tutorials zu Azure Queue Storage gelten für Azure Stack Hub. Beachten Sie die im vorherigen Abschnitt [Beispiele](#examples) beschriebene bestimmte Endpunktsuffix-Voraussetzung für Azure Stack Hub.

* [Erste Schritte mit Azure Queue Storage mit .NET](/azure/storage/queues/storage-dotnet-how-to-use-queues)
* [Gewusst wie: Verwenden von Queue Storage mit Java](/azure/storage/queues/storage-java-how-to-use-queue-storage)
* [Gewusst wie: Verwenden von Queue Storage mit Node.js](/azure/storage/queues/storage-nodejs-how-to-use-queues)
* [Verwenden des Warteschlangenspeichers mit C++](/azure/storage/queues/storage-c-plus-plus-how-to-use-queues)
* [Gewusst wie: Verwenden von Queue Storage mit PHP](/azure/storage/queues/storage-php-how-to-use-queues)
* [Gewusst wie: Verwenden von Queue Storage mit Python](/azure/storage/queues/storage-python-how-to-use-queue-storage)
* [Gewusst wie: Verwenden von Queue Storage mit Ruby](/azure/storage/queues/storage-ruby-how-to-use-queue-storage)

## <a name="table-storage"></a>Table Storage

Die folgenden Tutorials zu Azure Table Storage gelten für Azure Stack Hub. Beachten Sie die im vorherigen Abschnitt [Beispiele](#examples) beschriebene bestimmte Endpunktsuffix-Voraussetzung für Azure Stack Hub.

* [Erste Schritte mit Azure Table Storage mit .NET](/azure/cosmos-db/table-storage-how-to-use-dotnet)
* [Gewusst wie: Verwenden von Table Storage mit Java](/azure/cosmos-db/table-storage-how-to-use-java)
* [Verwenden des Azure-Tabellenspeichers mit Node.js](/azure/cosmos-db/table-storage-how-to-use-nodejs)
* [Verwenden des Tabellenspeichers mit C++](/azure/cosmos-db/table-storage-how-to-use-c-plus)
* [Gewusst wie: Verwenden von Table Storage mit PHP](/azure/cosmos-db/table-storage-how-to-use-php)
* [Verwenden von Table Storage in Python](/azure/cosmos-db/table-storage-how-to-use-python)
* [Gewusst wie: Verwenden von Table Storage mit Ruby](/azure/cosmos-db/table-storage-how-to-use-ruby)

## <a name="next-steps"></a>Nächste Schritte

* [Einführung in Azure Storage](/azure/storage/common/storage-introduction)
