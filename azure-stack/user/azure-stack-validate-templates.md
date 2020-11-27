---
title: Verwenden des Validierungstools für Vorlagen in Azure Stack Hub
description: Überprüfen Sie Vorlagen für die Bereitstellung in Azure Stack Hub mit dem Validierungstool für Vorlagen.
author: sethmanheim
ms.topic: article
ms.date: 11/22/2020
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 11/22/2020
ms.openlocfilehash: 0631058a3eade431769a5651bb37441b835eb3e6
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95518057"
---
# <a name="use-the-template-validation-tool-in-azure-stack-hub"></a>Verwenden des Validierungstools für Vorlagen in Azure Stack Hub

Mit dem Validierungstool für Vorlagen können Sie überprüfen, ob Ihre Azure Resource Manager-[Vorlagen](azure-stack-arm-templates.md) in Azure Stack Hub bereitgestellt werden können. Das Tool für die Vorlagenvalidierung ist im GitHub-Repository mit den Azure Stack Hub-Tools verfügbar. Laden Sie die Azure Stack Hub-Tools herunter, indem Sie die im Artikel [Herunterladen von Tools von GitHub](../operator/azure-stack-powershell-download.md) beschriebenen Schritte befolgen.

## <a name="overview"></a>Übersicht

Um eine Vorlage zu überprüfen, müssen Sie zunächst eine Datei mit Cloudfunktionen erstellen und dann das Validierungstool ausführen. Verwenden Sie die folgenden PowerShell-Module aus den Azure Stack Hub-Tools:

- Im Ordner **CloudCapabilities**: Mit **Az.CloudCapabilities.psm1** wird eine JSON-Datei mit Cloudfunktionen erstellt, die die Dienste und Versionen in einer Azure Stack Hub-Cloud darstellt.
- Im Ordner **TemplateValidator**: In **Az.TemplateValidator.psm1** wird eine JSON-Datei mit Cloudfunktionen verwendet, um Vorlagen für die Bereitstellung in Azure Stack Hub zu testen.

## <a name="build-the-cloud-capabilities-file"></a>Erstellen der Datei mit Cloudfunktionen

Bevor Sie die Vorlagenvalidierung verwenden, führen Sie das PowerShell-Modul **Az.CloudCapabilities** aus, um eine JSON-Datei zu erstellen.

> [!NOTE]
> Wenn Sie Ihr integriertes System aktualisieren oder neue Dienste oder virtuelle Erweiterungen hinzufügen, sollten Sie dieses Modul erneut ausführen.


### <a name="az-modules"></a>[Az-Module](#tab/az1)

1. Stellen Sie sicher, dass Sie mit Azure Stack Hub verbunden sind. Sie können diese Schritte auf dem ASDK-Host (Azure Stack Development Kit) ausführen oder ein [VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) verwenden, um eine Verbindung von Ihrer Arbeitsstation aus herzustellen.
2. Importieren Sie das PowerShell-Modul **Az.CloudCapabilities**:

    ```powershell
    Import-Module .\CloudCapabilities\Az.CloudCapabilities.psm1
    ```

3. Verwenden Sie das Cmdlet **Get-CloudCapabilities**, um Dienstversionen abzurufen und eine JSON-Datei mit Cloudfunktionen zu erstellen. Wenn Sie `-OutputPath` nicht angeben, wird die Datei **AzureCloudCapabilities.Json** im aktuellen Verzeichnis erstellt. Verwenden Sie den tatsächlichen Azure-Speicherort:

```powershell
Get-AzCloudCapability -Location <your location> -Verbose
```

### <a name="azurerm-modules"></a>[AzureRM-Module](#tab/azurerm1)

1. Stellen Sie sicher, dass Sie mit Azure Stack Hub verbunden sind. Sie können diese Schritte auf dem ASDK-Host (Azure Stack Development Kit) ausführen oder ein [VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) verwenden, um eine Verbindung von Ihrer Arbeitsstation aus herzustellen.
2. Importieren Sie das PowerShell-Modul **Az.CloudCapabilities**:

    ```powershell
    Import-Module .\CloudCapabilities\Az.CloudCapabilities.psm1
    ```

3. Verwenden Sie das Cmdlet **Get-CloudCapabilities**, um Dienstversionen abzurufen und eine JSON-Datei mit Cloudfunktionen zu erstellen. Wenn Sie `-OutputPath` nicht angeben, wird die Datei **AzureCloudCapabilities.Json** im aktuellen Verzeichnis erstellt. Verwenden Sie den tatsächlichen Azure-Speicherort:

```powershell
Get-AzureRMCloudCapability -Location <your location> -Verbose
```

---

## <a name="validate-templates"></a>Überprüfen von Vorlagen

Mit diesen Schritten überprüfen Sie Vorlagen, indem Sie das PowerShell-Modul **Az.TemplateValidator** verwenden. Sie können Ihre eigenen Vorlagen oder die [Azure Stack Hub-Vorlagen für den Schnellstart](https://github.com/Azure/AzureStack-QuickStart-Templates) verwenden.

### <a name="az-modules"></a>[Az-Module](#tab/az2)

1. Importieren Sie das PowerShell-Modul **Az.TemplateValidator.psm1**:

    ```powershell
    cd "c:\AzureStack-Tools-az\TemplateValidator"
    Import-Module .\Az.TemplateValidator.psm1
    ```

2. Führen Sie das Validierungssteuerelement für Vorlagen aus:

```powershell
Test-AzTemplate -TemplatePath <path to template.json or template folder> `
-CapabilitiesPath <path to cloudcapabilities.json> `
-Verbose
```

### <a name="azurerm-modules"></a>[AzureRM-Module](#tab/azurerm2)

1. Importieren Sie das PowerShell-Modul **Az.TemplateValidator.psm1**:

    ```powershell
    cd "c:\AzureStack-Tools-az\TemplateValidator"
    Import-Module .\Az.TemplateValidator.psm1
    ```

2. Führen Sie das Validierungssteuerelement für Vorlagen aus:

```powershell
Test-AzureRMTemplate -TemplatePath <path to template.json or template folder> `
-CapabilitiesPath <path to cloudcapabilities.json> `
-Verbose
```

---

Warnungen oder Fehler bei der Vorlagenvalidierung werden in der PowerShell-Konsole angezeigt und in einer HTML-Datei im Quellverzeichnis protokolliert. Der folgende Screenshot ist ein Beispiel für einen Validierungsbericht:

![Vorlagenvalidierungsbericht](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>Parameter

Das Validierungs-Cmdlet für Vorlagen unterstützt die folgenden Parameter.

| Parameter | BESCHREIBUNG | Erforderlich |
| ----- | -----| ----- |
| `TemplatePath` | Gibt den Pfad an, um Azure Resource Manager-Vorlagen rekursiv zu suchen. | Ja |
| `TemplatePattern` | Gibt den Namen der abzugleichenden Vorlagendateien an | Nein |
| `CapabilitiesPath` | Gibt den Pfad zu der JSON-Datei mit Cloudfunktionen an. | Ja |
| `IncludeComputeCapabilities` | Enthält die Auswertung von IaaS-Ressourcen wie VM-Größen und VM-Erweiterungen. | Nein |
| `IncludeStorageCapabilities` | Enthält die Auswertung von Speicherressourcen wie SKU-Typen. | Nein |
| `Report` | Gibt den Namen des generierten HTML-Berichts an. | Nein |
| `Verbose` | Protokolliert Fehler und Warnungen in der Konsole. | Nein|

### <a name="examples"></a>Beispiele

In diesem Beispiel werden alle [Vorlagen aus dem Azure Stack Hub-Schnellstart](https://github.com/Azure/AzureStack-QuickStart-Templates) überprüft, die in den lokalen Speicher heruntergeladen wurden. Im Beispiel werden auch die Größen und Erweiterungen virtueller Computer anhand der ASDK-Funktionen überprüft.

### <a name="az-modules"></a>[Az-Module](#tab/az3)

```powershell
test-AzTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json `
-TemplatePattern MyStandardTemplateName.json `
-IncludeComputeCapabilities `
-Report TemplateReport.html
```
### <a name="azurerm-modules"></a>[AzureRM-Module](#tab/azurerm3)

```powershell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json `
-TemplatePattern MyStandardTemplateName.json `
-IncludeComputeCapabilities `
-Report TemplateReport.html
```
---

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen von Vorlagen in Azure Stack Hub](azure-stack-arm-templates.md)
- [Entwickeln von Vorlagen für Azure Stack Hub](azure-stack-develop-templates.md)
