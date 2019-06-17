---
title: PowerShell in Azure Stack | Microsoft-Dokumentation
description: PowerShell in Azure Stack verfügt über eine Reihe von Modulen und Kontexten.
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
ms.date: 04/25/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 8f5d97969c96e8f3546d37ffed28b6332d80dc05
ms.sourcegitcommit: 2ee75ded704e8cfb900d9ac302d269c54a5dd9a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66394422"
---
# <a name="get-started-with-powershell-in-azure-stack"></a>Erste Schritte mit PowerShell in Azure Stack

PowerShell ist für die Verwaltung von Ressourcen über die Befehlszeile optimiert. Sie können PowerShell verwenden, wenn Sie automatisierte Tools erstellen möchten, die das Azure Resource Manager-Modell nutzen. Ein PowerShell-Modul lässt sich als eine Reihe von PowerShell-Funktionen definieren, die gruppiert werden, um alle Aspekte eines bestimmten Bereichs zu verwalten. Bei der Arbeit mit Azure Stack müssen Sie mit verschiedenen Sätzen von PowerShell-Cmdlets umgehen.

Dieser Artikel hilft Ihnen bei der Orientierung in Bezug auf die verschiedenen PowerShell-Module, die in Azure Stack verwendet werden. Bei Verwendung von PowerShell in Azure Stack können Sie mit vier API-Sätzen interagieren. Dies ist in der folgenden Tabelle dargestellt:

| API | PowerShell-Referenz | REST-Referenz |
| --- | --- | --- |
| Azure Resource Manager (global) | [Azure PowerShell-Module](https://github.com/Azure/azure-powershell/blob/master/documentation/azure-powershell-modules.md) | [REST-API-Browser](https://docs.microsoft.com/rest/api/) |
| Azure Stack Resource Manager | [Verwalten von API-Versionsprofilen in Azure Stack](azure-stack-version-profiles.md) | [Verwalten von API-Versionsprofilen in Azure Stack](azure-stack-version-profiles.md) |
| Azure Stack-Administratorendpunkte | [Azure Stack-Administratormodul](https://docs.microsoft.com/powershell/azure/azure-stack/overview) | [REST-API-Browser – Azure Stack](https://docs.microsoft.com/rest/api/?term=Azure%20Azure%20Stack%20Admin) |
| Privilegierter Endpunkt in Azure Stack | [Verwenden des privilegierten Endpunkts in Azure Stack](../operator/azure-stack-privileged-endpoint.md) | |

Jede Schnittstelle kontaktiert Ressourcenanbieter in globalen Azure- oder Azure Stack-Umgebungen. Ressourcenanbieter aktivieren Azure-Funktionen. Beispielweise ermöglicht der Azure Compute-Ressourcenanbieter programmatischen Zugriff auf die Erstellung und Verwaltung von virtuellen Computern und deren unterstützenden Ressourcen.

Ressourcenanbieter stellen sowohl Funktionalität als auch Steuerelemente für die Verwaltung und Konfiguration der Ressource bereit. Sie können programmgesteuert auf die Ressourcenanbieter zugreifen, indem Sie Azure Resource Manager verwenden. Die Schnittstelle dient wiederum als Oberfläche für PowerShell, die Azure CLI und Ihre eigenen REST-Clients.

## <a name="where-to-find-azure-stack-powershell"></a>Hier finden Sie Azure Stack PowerShell

Im folgenden Blockdiagramm sind die Beziehungen zwischen den Sätzen mit den PowerShell-Modulen dargestellt. Von Ihrem Computer aus können Sie die PowerShell-Module laden und sowohl globale Azure- als auch Azure Stack-Umgebungen verwalten.

![Azure Stack PowerShell](media/azure-stack-powershell-overview/Azure-Stack-PowerShell.png)

### <a name="global-azure"></a>Globale Azure-Umgebung

Azure PowerShell enthält eine Reihe von Cmdlets, für die die aktuelle Version von Azure Resource Manager für die Arbeit mit Ihren Azure-Ressourcen verwendet wird. Für Azure PowerShell wird die .NET Standard-Version verwendet. Dies bedeutet, dass Sie Versionen von PowerShell mit Windows, macOS und Linux nutzen können. Azure PowerShell steht auch in Azure Cloud Shell zur Verfügung. Weitere Informationen finden Sie unter [Erste Schritte mit Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

### <a name="azure-stack-resource-manager"></a>Azure Stack Resource Manager

Azure Stack PowerShell verfügt über eine Reihe von Cmdlets, die vorherige Azure Resource Manager-Versionen verwenden. Diese Cmdlets sind mit den Ressourcenanbietern in Azure Stack kompatibel. Jeder Ressourcenanbieter in Azure Stack verwendet eine ältere Version des Anbieters in der globalen Azure-Umgebung. Zum Koordinieren der von Azure Stack unterstützten Version der einzelnen Anbieter können Sie API-Profile verwenden. Azure Stack PowerShell verwendet PowerShell 5.1 und ist nur unter Windows verfügbar. Weitere Informationen finden Sie unter [Verwalten von API-Versionsprofile in Azure Stack](azure-stack-version-profiles.md).

### <a name="azure-stack-administrator"></a>Azure Stack-Administrator

Azure Stack stellt dem Cloudoperator eine Reihe von Ressourcenanbietern für die Installation und Verwaltung von Azure Stack zur Verfügung. In der globalen Azure-Umgebung wird diese Interaktion vom Benutzer abstrahiert und im Hintergrund als Teil von Azure verarbeitet. Mit Azure Stack können Unternehmen aber eine private Cloud unterstützen. Um diese Aufgaben durchzuführen, interagiert der Operator mit den Azure Stack-Administrator-APIs. Weitere Informationen finden Sie unter [Installieren von PowerShell für Azure Stack](../operator/azure-stack-powershell-install.md).

### <a name="azure-stack-privileged-endpoint"></a>Privilegierter Endpunkt in Azure Stack

Für Operatoraktivitäten in Azure Stack, z. B. zum Testen der Installations- und Zugriffsprotokolle, können Operatoren mit dem privilegierten Endpunkt (PEP) interagieren. Der PEP ist eine vorkonfigurierte PowerShell-Remotekonsole, mit der Betreiber über ausreichende Zugriffsberechtigungen für bestimmte Aufgaben verfügen. Der Endpunkt nutzt PowerShell Just Enough Administration (JEA), um einen eingeschränkten Satz von Cmdlets verfügbar zu machen. Weitere Informationen finden Sie unter [Verwenden des privilegierten Endpunkts in Azure Stack](../operator/azure-stack-privileged-endpoint.md).

### <a name="azure-stack-tools"></a>Azure Stack-Tools

Azure Stack stellt Skripts und zusätzliche Cmdlets zur Verfügung, die im GitHub-Repository *AzureStack-Tools* verfügbar sind. AzureStack-Tools hostet PowerShell-Module zum Verwalten und Bereitstellen von Ressourcen für Azure Stack. Sie können diese PowerShell-Module in das Azure Stack Development Kit oder in einen Windows-basierten externen Client herunterladen und verwenden, wenn Sie die Einrichtung der VPN-Konnektivität planen. Weitere Informationen finden Sie auf der Seite [AzureStack-Tools](https://github.com/Azure/AzureStack-Tools).

## <a name="work-with-powershell-in-azure-stack"></a>Verwenden von PowerShell in Azure Stack

PowerShell ist eine Möglichkeit, um programmgesteuert mit Azure Resource Manager zu interagieren. Sie können mit einer interaktiven Eingabeaufforderung arbeiten oder – falls Sie Aufgaben automatisieren – Skripts schreiben.

Wenn Sie viel Zeit mit der Arbeit mit Azure Stack PowerShell verbringen, werden Sie immer wieder Module installieren und neu installieren. Wenn Sie gleichzeitig in der globalen Azure-Umgebung arbeiten, kann dies eine ziemliche Herausforderung darstellen, da Sie Ihre Module je nach Ziel immer wieder deinstallieren und neu installieren müssen. 

Mithilfe von Docker-Containern können Sie die verschiedenen Versionen von PowerShell auf Ihrem lokalen Computer isolieren. Unter [Verwenden von Docker zum Ausführen von PowerShell](azure-stack-powershell-user-docker.md) finden Sie Informationen zur Verwendung von Docker-Containern mit dem Ziel, zwischen den verschiedenen PowerShell-Modulsätzen hin und her zu wechseln.


## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [API-Profile für PowerShell](azure-stack-version-profiles.md) in Azure Stack.
- Installieren Sie [Azure Stack-PowerShell](../operator/azure-stack-powershell-install.md).
- Erfahren Sie mehr über das Erstellen von [Azure Resource Manager-Vorlagen](azure-stack-develop-templates.md) für cloudübergreifende Konsistenz.
