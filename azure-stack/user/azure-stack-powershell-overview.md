---
title: PowerShell in Azure Stack Hub | Microsoft-Dokumentation
description: PowerShell in Azure Stack Hub verfügt über eine Reihe von Modulen und Kontexten.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: da899ed4e926e4ead7ee74e76aedb4f4dec9a93b
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76536469"
---
# <a name="get-started-with-powershell-in-azure-stack-hub"></a>Erste Schritte mit PowerShell in Azure Stack Hub

PowerShell ist für die Verwaltung von Ressourcen über die Befehlszeile optimiert. Sie können PowerShell verwenden, wenn Sie automatisierte Tools erstellen möchten, die das Azure Resource Manager-Modell nutzen. Ein PowerShell-Modul lässt sich als eine Reihe von PowerShell-Funktionen definieren, die gruppiert werden, um alle Aspekte eines bestimmten Bereichs zu verwalten. Bei der Arbeit mit Azure Stack Hub müssen Sie mit verschiedenen Sätzen von PowerShell-Cmdlets umgehen.

Dieser Artikel hilft Ihnen bei der Orientierung in Bezug auf die verschiedenen PowerShell-Module, die in Azure Stack Hub verwendet werden. Bei Verwendung von PowerShell in Azure Stack Hub können Sie mit vier API-Sätzen interagieren. Dies ist in der folgenden Tabelle dargestellt:

| API | PowerShell-Referenz | REST-Referenz |
| --- | --- | --- |
| Azure Resource Manager (global) | [Azure PowerShell-Module](https://github.com/Azure/azure-powershell/blob/master/documentation/azure-powershell-modules.md) | [REST-API-Browser](https://docs.microsoft.com/rest/api/) |
| Azure Stack Hub: Resource Manager | [Verwalten von API-Versionsprofilen in Azure Stack Hub](azure-stack-version-profiles.md) | [Verwalten von API-Versionsprofilen in Azure Stack Hub](azure-stack-version-profiles.md) |
| Azure Stack Hub-Administratorendpunkte | [Azure Stack Hub-Administratormodul](https://docs.microsoft.com/powershell/azure/azure-stack/overview) | [REST-API-Browser – Azure Stack Hub](https://docs.microsoft.com/rest/api/?term=Azure%20Azure%20Stack%20Admin) |
| Privilegierter Endpunkt in Azure Stack Hub | [Verwenden des privilegierten Endpunkts in Azure Stack Hub](../operator/azure-stack-privileged-endpoint.md) | |

Jede Schnittstelle kontaktiert Ressourcenanbieter in globalen Azure- oder Azure Stack Hub-Umgebungen. Ressourcenanbieter aktivieren Azure-Funktionen. Beispielweise ermöglicht der Azure Compute-Ressourcenanbieter programmatischen Zugriff auf die Erstellung und Verwaltung von virtuellen Computern und deren unterstützenden Ressourcen.

Ressourcenanbieter stellen sowohl Funktionalität als auch Steuerelemente für die Verwaltung und Konfiguration der Ressource bereit. Sie können programmgesteuert auf die Ressourcenanbieter zugreifen, indem Sie Azure Resource Manager verwenden. Die Schnittstelle dient wiederum als Oberfläche für PowerShell, die Azure CLI und Ihre eigenen REST-Clients.

## <a name="where-to-find-azure-stack-hub-powershell"></a>Hier finden Sie Azure Stack Hub PowerShell

Im folgenden Blockdiagramm sind die Beziehungen zwischen den Sätzen mit den PowerShell-Modulen dargestellt. Von Ihrem Computer aus können Sie die PowerShell-Module laden und sowohl globale Azure- als auch Azure Stack Hub-Umgebungen verwalten.

![Azure Stack Hub PowerShell](media/azure-stack-powershell-overview/Azure-Stack-PowerShell.png)

### <a name="global-azure"></a>Globale Azure-Umgebung

Azure PowerShell enthält eine Reihe von Cmdlets, für die die aktuelle Version von Azure Resource Manager für die Arbeit mit Ihren Azure-Ressourcen verwendet wird. Für Azure PowerShell wird die .NET Standard-Version verwendet. Dies bedeutet, dass Sie Versionen von PowerShell mit Windows, macOS und Linux nutzen können. Azure PowerShell steht auch in Azure Cloud Shell zur Verfügung. Weitere Informationen finden Sie unter [Erste Schritte mit Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

### <a name="azure-stack-hub-resource-manager"></a>Azure Stack Hub: Resource Manager

Azure Stack Hub PowerShell verfügt über eine Reihe von Cmdlets, die vorherige Azure Resource Manager-Versionen verwenden. Diese Cmdlets sind mit den Ressourcenanbietern in Azure Stack Hub kompatibel. Jeder Ressourcenanbieter in Azure Stack Hub verwendet eine ältere Version des Anbieters in der globalen Azure-Umgebung. Zum Koordinieren der von Azure Stack Hub unterstützten Version der einzelnen Anbieter können Sie API-Profile verwenden. Azure Stack Hub PowerShell verwendet PowerShell 5.1 und ist nur unter Windows verfügbar. Weitere Informationen finden Sie unter [Verwalten von API-Versionsprofile in Azure Stack Hub](azure-stack-version-profiles.md).

### <a name="azure-stack-hub-administrator"></a>Azure Stack Hub-Administrator

Azure Stack Hub stellt dem Cloudoperator eine Reihe von Ressourcenanbietern für die Installation und Verwaltung von Azure Stack Hub zur Verfügung. In der globalen Azure-Umgebung wird diese Interaktion vom Benutzer abstrahiert und im Hintergrund als Teil von Azure verarbeitet. Mit Azure Stack Hub können Unternehmen aber eine private Cloud unterstützen. Um diese Aufgaben durchzuführen, interagiert der Operator mit den Azure Stack Hub-Administrator-APIs. Weitere Informationen finden Sie unter [Installieren von PowerShell für Azure Stack Hub](../operator/azure-stack-powershell-install.md).

### <a name="azure-stack-hub-privileged-endpoint"></a>Privilegierter Endpunkt in Azure Stack Hub

Für Operatoraktivitäten in Azure Stack Hub, z. B. zum Testen der Installations- und Zugriffsprotokolle, können Operatoren mit dem privilegierten Endpunkt (PEP) interagieren. Der PEP ist eine vorkonfigurierte PowerShell-Remotekonsole, mit der Betreiber über ausreichende Zugriffsberechtigungen für bestimmte Aufgaben verfügen. Der Endpunkt nutzt PowerShell Just Enough Administration (JEA), um einen eingeschränkten Satz von Cmdlets verfügbar zu machen. Weitere Informationen finden Sie unter [Verwenden des privilegierten Endpunkts in Azure Stack Hub](../operator/azure-stack-privileged-endpoint.md).

### <a name="azure-stack-hub-tools"></a>Azure Stack Hub-Tools

Azure Stack Hub stellt Skripts und zusätzliche Cmdlets zur Verfügung, die im GitHub-Repository *AzureStack-Tools* verfügbar sind. AzureStack-Tools hostet PowerShell-Module zum Verwalten und Bereitstellen von Ressourcen für Azure Stack Hub. Sie können diese PowerShell-Module in das Azure Stack Development Kit oder in einen Windows-basierten externen Client herunterladen und verwenden, wenn Sie die Einrichtung der VPN-Konnektivität planen. Weitere Informationen finden Sie auf der Seite [AzureStack-Tools](https://github.com/Azure/AzureStack-Tools).

## <a name="work-with-powershell-in-azure-stack-hub"></a>Verwenden von PowerShell in Azure Stack Hub

PowerShell ist eine Möglichkeit, um programmgesteuert mit Azure Resource Manager zu interagieren. Sie können mit einer interaktiven Eingabeaufforderung arbeiten oder – falls Sie Aufgaben automatisieren – Skripts schreiben.

Wenn Sie viel Zeit mit der Arbeit mit Azure Stack Hub PowerShell verbringen, werden Sie immer wieder Module installieren und neu installieren. Wenn Sie gleichzeitig in der globalen Azure-Umgebung arbeiten, kann dies eine ziemliche Herausforderung darstellen, da Sie Ihre Module je nach Ziel immer wieder deinstallieren und neu installieren müssen. 

Mithilfe von Docker-Containern können Sie die verschiedenen Versionen von PowerShell auf Ihrem lokalen Computer isolieren. Unter [Verwenden von Docker zum Ausführen von PowerShell](azure-stack-powershell-user-docker.md) finden Sie Informationen zur Verwendung von Docker-Containern mit dem Ziel, zwischen den verschiedenen PowerShell-Modulsätzen hin und her zu wechseln.


## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [API-Profile für PowerShell](azure-stack-version-profiles.md) in Azure Stack Hub.
- Installieren Sie [Azure Stack Hub PowerShell](../operator/azure-stack-powershell-install.md).
- Erfahren Sie mehr über das Erstellen von [Azure Resource Manager-Vorlagen](azure-stack-develop-templates.md) für cloudübergreifende Konsistenz.
