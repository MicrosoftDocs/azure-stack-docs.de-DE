---
title: Herunterladen von Azure Stack-Tools von GitHub | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Tools herunterladen, die für die Arbeit mit Azure Stack benötigt werden.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 09/18/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 09/18/2019
ms.openlocfilehash: 89a31f6d7894ed9af710263298fef03604d1351b
ms.sourcegitcommit: acebda8a42ac8ecdeba490fc1738e9041479dab0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72813988"
---
# <a name="download-azure-stack-tools-from-github"></a>Herunterladen von Azure Stack-Tools von GitHub

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

**AzureStack-Tools** ist ein [GitHub-Repository](https://github.com/Azure/AzureStack-Tools), das PowerShell-Module zum Verwalten und Bereitstellen von Ressourcen für Azure Stack hostet. Sie können diese PowerShell-Module in das Azure Stack Development Kit (ASDK) oder in einen Windows-basierten externen Client herunterladen und verwenden, wenn Sie die Einrichtung der VPN-Konnektivität planen. Sie erhalten diese Tools, indem Sie das GitHub-Repository klonen oder den Ordner **AzureStack-Tools** herunterladen. Führen Sie dazu das folgende Skript aus:

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

## <a name="functionality-provided-by-the-modules"></a>Von den Modulen bereitgestellte Funktionen

Das Repository **AzureStack-Tools** verfügt über PowerShell-Module, die die folgenden Funktionen für Azure Stack unterstützen:  

| Funktionalität | BESCHREIBUNG | Wer kann dieses Modul verwenden? |
| --- | --- | --- |
| [Cloudfunktionen](../user/azure-stack-validate-templates.md) | Verwenden Sie dieses Modul, um die Cloudfunktionen einer Cloud zu nutzen. Sie können beispielsweise Cloudfunktionen wie die API-Version und Azure Resource Manager-Ressourcen nutzen. Außerdem können Sie die VM-Erweiterungen für Azure Stack und Azure-Clouds nutzen. | Cloudoperatoren und Benutzer |
| [Resource Manager-Richtlinie für Azure Stack](../user/azure-stack-policy-module.md) | Verwenden Sie dieses Modul zum Konfigurieren eines Azure-Abonnements oder einer Azure-Ressourcengruppe mit der gleichen Versionsverwaltung und Dienstverfügbarkeit wie Azure Stack. | Cloudoperatoren und Benutzer |
| [Registrieren bei Azure](azure-stack-registration.md ) | Verwenden Sie dieses Modul zum Registrieren Ihrer ASDK-Instanz bei Azure. Nach der Registrierung können Sie Azure Marketplace-Elemente herunterladen und in Azure Stack verwenden. | Cloudoperatoren |
| [Azure Stack-Bereitstellung](../asdk/asdk-install.md) | Verwenden Sie dieses Modul zum Vorbereiten des Azure Stack-Hostcomputers auf die Bereitstellung und die erneute Bereitstellung mithilfe des Azure Stack-VHD-Images (virtuelle Festplatte). | Cloudoperatoren|
| [Herstellen einer Verbindung mit Azure Stack](azure-stack-powershell-install.md) | Verwenden Sie dieses Modul, um die VPN-Konnektivität für Azure Stack zu konfigurieren. | Cloudoperatoren und Benutzer |
| [Validierungssteuerelement für Vorlagen](../user/azure-stack-validate-templates.md) | Verwenden Sie dieses Modul, um zu überprüfen, ob eine vorhandene oder eine neue Vorlage für Azure Stack bereitgestellt werden kann. | Cloudoperatoren und Benutzer|

## <a name="next-steps"></a>Nächste Schritte

- [Erste Schritte mit PowerShell in Azure Stack](../user/azure-stack-powershell-overview.md)
- [Konfigurieren der PowerShell-Umgebung des Azure Stack-Benutzers](../user/azure-stack-powershell-configure-user.md).
- [Herstellen einer Verbindung mit dem Azure Stack Development Kit über VPN](../asdk/asdk-connect.md)
