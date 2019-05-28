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
ms.openlocfilehash: 0cef39147fdbc62fe0652b1e387aa23f5ecb8487
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782889"
---
# <a name="get-started-with-powershell-on-azure-stack"></a>Erste Schritte mit PowerShell in Azure Stack

PowerShell ist für die Verwaltung von Ressourcen über die Befehlszeile optimiert. Sie können PowerShell verwenden, wenn Sie automatisierte Tools erstellen möchten, die das Azure Resource Manager-Modell nutzen. Ein PowerShell-Modul lässt sich als eine Reihe von PowerShell-Funktionen definieren, die gruppiert werden, um alle Aspekte eines bestimmten Bereichs zu verwalten. Um mit Azure Stack zu arbeiten, müssen Sie mit verschiedenen Sätzen von PowerShell-Cmdlets umgehen.

Dieser Artikel hilft Ihnen, sich auf die verschiedenen PowerShell-Module zu konzentrieren, die in Azure Stack verwendet werden. Es gibt vier Sätze von APIs, mit denen Sie interagieren können, wenn Sie PowerShell in Azure Stack verwenden.

| API | PowerShell-Referenz | REST-Referenz |
| --- | --- | --- |
| 1. Azure Resource Manager (global) | [Azure PowerShell-Module](https://github.com/Azure/azure-powershell/blob/master/documentation/azure-powershell-modules.md) | [REST-API-Browser](https://docs.microsoft.com/rest/api/) |
| 2. Azure Stack Resource Manager | [Verwalten von API-Versionsprofilen in Azure Stack](azure-stack-version-profiles.md) | [Verwalten von API-Versionsprofilen in Azure Stack](azure-stack-version-profiles.md) |
| 3. Azure Stack-Administratorendpunkte | [Azure Stack-Administratormodul](https://docs.microsoft.com/powershell/azure/azure-stack/overview) | [REST-API-Browser – Azure Stack](https://docs.microsoft.com/rest/api/?term=Azure%20Azure%20Stack%20Admin) |
| 4.  Privilegierter Endpunkt in Azure Stack | [Verwenden des privilegierten Endpunkts in Azure Stack](../operator/azure-stack-privileged-endpoint.md) | |

Jede Schnittstelle kontaktiert Ressourcenanbieter in globalen Azure- oder Azure Stack-Umgebungen. Ressourcenanbieter aktivieren Azure-Funktionen. Beispielweise bietet der Azure Compute-Ressourcenanbieter programmatischen Zugriff auf die Erstellung und Verwaltung von virtuellen Computern und deren unterstützenden Ressourcen.

Ressourcenanbieter stellen Funktionalität, aber auch Steuerelemente für die Verwaltung und Konfiguration der Ressource bereit. Sie können programmgesteuert mit dem Azure Resource Manager auf die Ressourcenanbieter zugreifen, und die Schnittstelle wiederum bietet eine Oberfläche für PowerShell, Azure CLI und Ihre eigenen REST-Clients.

## <a name="where-to-find-azure-stack-powershell"></a>Hier finden Sie Azure Stack PowerShell

Das folgende Blockdiagramm zeigt die Beziehung jedes einzelnen Satzes von PowerShell-Modulen. Von Ihrem Computer aus können Sie die PowerShell-Module laden und sowohl globale Azure- als auch Azure Stack-Umgebungen verwalten.

![Azure Stack PowerShell](media/azure-stack-powershell-overview/Azure-Stack-PowerShell.png)

### <a name="global-azure"></a>Globale Azure-Umgebung

Azure PowerShell enthält eine Reihe von Cmdlets, die die aktuelle Azure Resource Manager-Version für die Arbeit mit Ihren Azure-Ressourcen verwenden. Azure PowerShell verwendet .NET Standard. Dies bedeutet, dass Sie Versionen von PowerShell mit Windows, macOS und Linux verwenden können. Azure PowerShell steht auch in Azure Cloud Shell zur Verfügung. Weitere Informationen finden Sie unter [Erste Schritte mit Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

### <a name="azure-stack-resource-manager"></a>Azure Stack Resource Manager

Azure Stack PowerShell bietet eine Reihe von Cmdlets, die vorherige Azure Resource Manager-Versionen verwenden. Diese Cmdlets sind mit den Ressourcenanbietern in Azure Stack kompatibel. Jeder Ressourcenanbieter in Azure Stack verwendet eine ältere Version des Anbieters in der globalen Azure-Umgebung. Zum Koordinieren der von Azure Stack unterstützten Version der einzelnen Anbieter können Sie API-Profile verwenden. Azure Stack PowerShell verwendet PowerShell 5.1 und ist nur unter Windows verfügbar. Weitere Informationen finden Sie unter [Verwalten von API-Versionsprofile in Azure Stack](azure-stack-version-profiles.md).

### <a name="azure-stack-administrator"></a>Azure Stack-Administrator

Azure Stack stellt dem Cloudoperator eine Reihe von Ressourcenanbietern für Installation und Verwaltung von Azure Stack zur Verfügung. Im der globalen Azure-Umgebung wird diese Interaktion vom Benutzer abstrahiert und als Teil von Azure hinter den Kulissen verarbeitet. Azure Stack bietet Unternehmen jedoch die Möglichkeit, eine private Cloud zu unterstützen. Um diese Aufgaben auszuführen, interagiert der Operator mit den Azure Stack-Administrator-APIs. Weitere Informationen finden Sie unter [Installieren von PowerShell für Azure Stack](../operator/azure-stack-powershell-install.md).

### <a name="azure-stack-privileged-endpoint"></a>Privilegierter Endpunkt in Azure Stack

Für Operatoraktivitäten in Azure Stack, z.B. zum Testen der Installations- und Zugriffsprotokolle, können Operatoren mit dem privilegierten Endpunkt (PEP) interagieren. Bei dem PEP handelt es sich um eine vorkonfigurierte Remote-PowerShell-Konsole, die Operatoren gerade so viel Zugriff bietet, um bestimmte Aufgaben ausführen zu können. Der Endpunkt nutzt PowerShell JEA (Just Enough Administration), um einen eingeschränkten Satz von Cmdlets verfügbar zu machen. Weitere Informationen finden Sie unter [Verwenden des privilegierten Endpunkts in Azure Stack](../operator/azure-stack-privileged-endpoint.md).

### <a name="azurestack-tools"></a>AzureStack-Tools

Darüber hinaus stellt Azure Stack Skripts und zusätzliche Cmdlets zur Verfügung, die in einem GitHub-Repository (Azure Stack-Tools) verfügbar sind. AzureStack-Tools hostet PowerShell-Module zum Verwalten und Bereitstellen von Ressourcen für Azure Stack. Sie können diese PowerShell-Module in das Azure Stack Development Kit oder in einen Windows-basierten externen Client herunterladen und verwenden, wenn Sie planen, VPN-Konnektivität einzurichten. Weitere Informationen finden Sie unter [AzureStack-Tools](https://github.com/Azure/AzureStack-Tools).

## <a name="working-with-powershell-on-azure-stack"></a>Arbeiten mit PowerShell in Azure Stack

PowerShell bietet eine programmgesteuerte Möglichkeit, mit Azure Resource Manager zu interagieren. Sie können entweder mit einer interaktiven Eingabeaufforderung arbeiten oder Skripts zum Automatisieren von Aufgaben schreiben.

Wenn Sie viel Zeit mit der Arbeit mit Azure Stack PowerShell verbringen, werden Sie immer wieder Module installieren und neu installieren. Dies kann eine Herausforderung darstellen, wenn Sie gleichzeitig in der globalen Azure-Umgebung arbeiten, da Sie Ihre Module je nach Ihrem Ziel immer wieder deinstallieren und neu installieren müssen. Mithilfe von Docker-Containern können Sie die verschiedenen Versionen von PowerShell auf Ihrem lokalen Computer isolieren. [Verwenden von Docker zum Ausführen von PowerShell](azure-stack-powershell-user-docker.md) behandelt die Verwendung von Docker-Containern, sodass Sie zwischen den verschiedenen PowerShell-Modulsätzen hin und her wechseln können.


## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [API-Profile für PowerShell](azure-stack-version-profiles.md) in Azure Stack.
- [Installieren von Azure Stack-PowerShell](../operator/azure-stack-powershell-install.md)
- Erfahren Sie mehr über das Erstellen von [Azure Resource Manager-Vorlagen](azure-stack-develop-templates.md) für cloudübergreifende Konsistenz.