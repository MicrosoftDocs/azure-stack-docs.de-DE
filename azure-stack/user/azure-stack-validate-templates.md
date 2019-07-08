---
title: Verwenden eines Validierungstools für Vorlagen zum Überprüfen von Vorlagen für Azure Stack | Microsoft-Dokumentation
description: Überprüfen der Vorlagen für die Bereitstellung in Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: 3cba34e2748d00ebb886e7122ce1dd7151325c85
ms.sourcegitcommit: 07c51a03f07a6a3ee2721aa942d31a7a4c6a339b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/12/2019
ms.locfileid: "67028290"
---
# <a name="check-your-templates-for-azure-stack-with-the-template-validation-tool"></a>Überprüfen Ihrer Vorlagen für Azure Stack mit dem Validierungstool für Vorlagen

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Sie können das Tool für die Vorlagenvalidierung verwenden, um zu überprüfen, ob Ihre [Azure Resource Manager-Vorlagen](azure-stack-arm-templates.md) in Azure Stack bereitgestellt werden können. Das Tool für die Vorlagenvalidierung ist als Teil der Azure Stack-Tools verfügbar. Laden Sie die Azure Stack-Tools herunter, indem Sie die im Artikel [Herunterladen von Tools von GitHub](../operator/azure-stack-powershell-download.md) beschriebenen Schritte befolgen.

## <a name="overview"></a>Übersicht

Um eine Vorlage zu überprüfen, müssen Sie zunächst eine Datei mit Cloudfunktionen erstellen und dann das Validierungstool ausführen. Sie verwenden die folgenden PowerShell-Module aus den Azure Stack-Tools:

- Im Ordner **CloudCapabilities**: `AzureRM.CloudCapabilities.psm1` erstellt eine JSON-Datei mit Cloudfunktionen, die die Dienste und Versionen in einer Azure Stack-Cloud darstellt.
- Im Ordner **TemplateValidator**: `AzureRM.TemplateValidator.psm1` verwendet eine JSON-Datei mit Cloudfunktionen, um Vorlagen für die Bereitstellung in Azure Stack zu testen.

## <a name="build-the-cloud-capabilities-file"></a>Erstellen der Datei mit Cloudfunktionen

Bevor Sie die Vorlagenvalidierung verwenden, führen Sie das PowerShell-Modul **AzureRM.CloudCapabilities** aus, um eine JSON-Datei zu erstellen.

>[!NOTE]
> Wenn Sie Ihr integriertes System aktualisieren oder neue Dienste oder virtuelle Erweiterungen hinzufügen, sollten Sie dieses Modul erneut ausführen.

1. Stellen Sie sicher, dass Sie mit Azure Stack verbunden sind. Sie können diese Schritte auf dem Azure Stack Development Kit-Host ausführen oder ein [VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) verwenden, um eine Verbindung von Ihrer Arbeitsstation aus herzustellen.
2. Importieren Sie das PowerShell-Modul **AzureRM.CloudCapabilities**:

    ```powershell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ```

3. Verwenden Sie das Cmdlet `Get-CloudCapabilities`, um Dienstversionen abzurufen und eine JSON-Datei mit Cloudfunktionen zu erstellen. Wenn Sie `-OutputPath` nicht angeben, wird die Datei „AzureCloudCapabilities.Json“ im aktuellen Verzeichnis erstellt. Verwenden Sie den tatsächlichen Azure-Speicherort:

    ```powershell
    Get-AzureRMCloudCapability -Location <your location> -Verbose
    ```

## <a name="validate-templates"></a>Überprüfen von Vorlagen

Mit diesen Schritten überprüfen Sie Vorlagen, indem Sie das PowerShell-Modul **AzureRM.TemplateValidator** verwenden. Sie können Ihre eigenen Vorlagen verwenden, oder die [Azure Stack Quickstart templates (Azure Stack-Vorlagen für den Schnellstart)](https://github.com/Azure/AzureStack-QuickStart-Templates) verwenden.

1. Importieren Sie das PowerShell-Modul **AzureRM.TemplateValidator.psm1**:

    ```powershell
    cd "c:\AzureStack-Tools-master\TemplateValidator"
    Import-Module .\AzureRM.TemplateValidator.psm1
    ```

2. Führen Sie das Validierungssteuerelement für Vorlagen aus:

    ```powershell
    Test-AzureRMTemplate -TemplatePath <path to template.json or template folder> `
    -CapabilitiesPath <path to cloudcapabilities.json> `
    -Verbose
    ```

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

In diesem Beispiel werden alle [Vorlagen aus dem Azure Stack-Schnellstart](https://github.com/Azure/AzureStack-QuickStart-Templates) überprüft, die in den lokalen Speicher heruntergeladen wurden. Im Beispiel werden auch die Größen und Erweiterungen virtueller Computer anhand der Azure Stack Development Kit-Funktionen überprüft:

```powershell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json `
-TemplatePattern MyStandardTemplateName.json `
-IncludeComputeCapabilities `
-Report TemplateReport.html
```

## <a name="next-steps"></a>Nächste Schritte

- [Deploy templates to Azure Stack (Bereitstellen von Vorlagen in Azure Stack)](azure-stack-arm-templates.md)
- [Entwickeln von Vorlagen für Azure Stack](azure-stack-develop-templates.md)
