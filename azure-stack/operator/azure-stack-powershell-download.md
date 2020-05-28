---
title: Herunterladen von Azure Stack Hub-Tools aus GitHub
description: Erfahren Sie, wie Sie die Tools herunterladen, die für die Arbeit mit Azure Stack Hub benötigt werden.
author: mattbriggs
ms.topic: article
ms.date: 4/22/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 4/22/2020
ms.openlocfilehash: c85982690578cbfbed65c83fb2c83792ce6b4fb2
ms.sourcegitcommit: cad40ae88212cc72f40c84a1c88143ea0abb65ef
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84111191"
---
# <a name="download-azure-stack-hub-tools-from-github"></a>Herunterladen von Azure Stack Hub-Tools aus GitHub

**AzureStack-Tools** ist ein [GitHub-Repository](https://github.com/Azure/AzureStack-Tools), das PowerShell-Module zum Verwalten und Bereitstellen von Ressourcen für Azure Stack Hub enthält. Sie können diese PowerShell-Module in das Azure Stack Development Kit (ASDK) oder in einen Windows-basierten externen Client herunterladen und verwenden, wenn Sie die Einrichtung der VPN-Konnektivität planen. 

## <a name="get-tools-for-azure-stack-hub-azurerm-module"></a>Abrufen von Tools für das Azure Stack Hub-AzureRM-Modul

Um diese Tools zu erhalten, klonen Sie das GitHub-Repository aus dem Branch `master`, oder laden Sie den Ordner **AzureStack-Tools** herunter, indem Sie das folgende Skript ausführen:

```powershell
# Change directory to the root directory.
cd \

# Download the tools archive.
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files.
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
cd AzureStack-Tools-master

```
Weitere Informationen zur Verwendung des AzureRM-Moduls für Azure Stack Hub finden Sie unter [Installieren des PowerShell AzureRM-Moduls für Azure Stack Hub](azure-stack-powershell-install.md).

## <a name="get-tools-for-azure-stack-hub-az-preview-module"></a>Abrufen von Tools für das Azure Stack Hub-Az-Modul (Vorschauversion)

Um diese Tools zu erhalten, klonen Sie das GitHub-Repository aus dem Branch `az`, oder laden Sie den Ordner **AzureStack-Tools** herunter, indem Sie das folgende Skript ausführen:

```powershell
# Change directory to the root directory.
cd \

# Download the tools archive.
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/az.zip `
  -OutFile master.zip

# Expand the downloaded files.
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
cd AzureStack-Tools-master

```

Weitere Informationen zur Verwendung des Az-Moduls für Azure Stack Hub finden Sie unter [Installieren des PowerShell Az-Moduls für Azure Stack Hub (Vorschauversion)](powershell-install-az-module.md).

## <a name="functionality-provided-by-the-modules"></a>Von den Modulen bereitgestellte Funktionen

Das Repository **AzureStack-Tools** enthält PowerShell-Module, die die folgenden Funktionen für Azure Stack Hub unterstützen:  

| Funktionalität | BESCHREIBUNG | Wer kann dieses Modul verwenden? |
| --- | --- | --- |
| [Cloudfunktionen](../user/azure-stack-validate-templates.md) | Verwenden Sie dieses Modul, um die Cloudfunktionen einer Cloud zu nutzen. Sie können beispielsweise Cloudfunktionen wie die API-Version und Azure Resource Manager-Ressourcen nutzen. Außerdem können Sie die VM-Erweiterungen für Azure Stack Hub und Azure-Clouds nutzen. | Cloudoperatoren und Benutzer |
| [Resource Manager-Richtlinie für Azure Stack Hub](../user/azure-stack-policy-module.md) | Verwenden Sie dieses Modul zum Konfigurieren eines Azure-Abonnements oder einer Azure-Ressourcengruppe mit der gleichen Versionsverwaltung und Dienstverfügbarkeit wie Azure Stack Hub. | Cloudoperatoren und Benutzer |
| [Registrieren bei Azure](azure-stack-registration.md ) | Verwenden Sie dieses Modul zum Registrieren Ihrer ASDK-Instanz bei Azure. Nach der Registrierung können Sie Azure Marketplace-Elemente herunterladen und in Azure Stack Hub verwenden. | Cloudoperatoren |
| [Azure Stack Hub-Bereitstellung](../asdk/asdk-install.md) | Verwenden Sie dieses Modul zum Vorbereiten des Azure Stack Hub-Hostcomputers auf die Bereitstellung und die erneute Bereitstellung mithilfe des Azure Stack Hub-VHD-Images (virtuelle Festplatte). | Cloudoperatoren|
| [Herstellen einer Verbindung mit Azure Stack Hub](azure-stack-powershell-install.md) | Verwenden Sie dieses Modul, um die VPN-Konnektivität für Azure Stack Hub zu konfigurieren. | Cloudoperatoren und Benutzer |
| [Validierungssteuerelement für Vorlagen](../user/azure-stack-validate-templates.md) | Verwenden Sie dieses Modul, um zu überprüfen, ob eine vorhandene oder eine neue Vorlage in Azure Stack Hub bereitgestellt werden kann. | Cloudoperatoren und Benutzer|

## <a name="next-steps"></a>Nächste Schritte

- [Erste Schritte mit PowerShell in Azure Stack Hub](../user/azure-stack-powershell-overview.md)
- [Konfigurieren der PowerShell-Umgebung des Azure Stack Hub-Benutzers](../user/azure-stack-powershell-configure-user.md)
- [Herstellen einer Verbindung mit dem Azure Stack Development Kit über VPN](../asdk/asdk-connect.md)
