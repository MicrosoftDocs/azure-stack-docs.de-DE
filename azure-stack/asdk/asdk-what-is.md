---
title: Einführung in das Azure Stack Development Kit (ASDK) | Microsoft-Dokumentation
description: Beschreibt das ASDK sowie gängige Anwendungsfälle für die Evaluierung von Microsoft Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 02/08/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: edde001bece869e1398ee13fbcc7b3fa32616cfa
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "64312741"
---
# <a name="what-is-the-azure-stack-development-kit"></a>Was ist das Azure Stack Development Kit?
[Integrierte Microsoft Azure Stack-Systeme](../operator/azure-stack-overview.md) haben eine Größe von 4 bis 16 Knoten, und der Support wird von einem Hardwarepartner und von Microsoft gemeinsam bereitgestellt. Mit integrierten Azure Stack-Systemen ermöglichen Sie neue Szenarien für Ihre Produktionsworkloads. Wenn Sie ein Azure Stack-Betreiber sind, der die Infrastruktur integrierter Systeme verwaltet und Dienste anbietet, hilft Ihnen die [Dokumentation für Betreiber](/azure-stack/operator) weiter.

Das Azure Stack Development Kit (ASDK) ist eine Azure Stack-Bereitstellung mit einem einzelnen Knoten, die Sie **kostenlos** herunterladen und verwenden können. Alle ASDK-Komponenten werden auf virtuellen Computern installiert, die auf einem einzelnen Hostservercomputer ausgeführt werden, der die [Mindesthardwareanforderungen](asdk-deploy-considerations.md#hardware) erfüllen muss. Das ASDK stellt eine *produktionsfremde* Umgebung bereit, in der Sie anhand von mit Azure konsistenten APIs und Tools Azure Stack evaluieren und moderne Anwendungen entwickeln können. 

> [!IMPORTANT]
> Das ASDK ist nicht für die Verwendung in einer Produktionsumgebung vorgesehen, und diese Art der Verwendung wird nicht unterstützt.

Da alle ASDK-Komponenten auf einem einzelnen Development Kit-Hostcomputer bereitgestellt werden, sind die verfügbaren physischen Ressourcen begrenzt. Bei ASDK-Bereitstellungen befinden sich die virtuellen Computer der Azure Stack-Infrastruktur und die virtuellen Mandantencomputer auf dem gleichen Servercomputer. Diese Konfiguration ist nicht für die Beurteilung von Skalierung oder Leistung vorgesehen.

Das ASDK ist als Azure-konsistente Hybrid Cloud-Umgebung für folgende Benutzer konzipiert:
- **Administratoren** (Azure Stack-Betreiber): Das ASDK eignet sich hervorragend, um sich über die verfügbaren Azure Stack-Dienste zu informieren und sie zu evaluieren.
- **Entwickler:** Das ASDK kann für die lokale Entwicklung von Hybridanwendungen und modernen Anwendungen verwendet werden (Entwicklungs-/Testumgebungen). Dies ermöglicht die Wiederholung der Entwicklungserfahrung vor der Azure Stack-Bereitstellung in der Produktionsumgebung (oder parallel dazu). 

Weitere Informationen zum ASDK erhalten Sie im folgenden kurzen Video:

> [!VIDEO https://www.youtube.com/embed/dbVWDrl00MM]


## <a name="asdk-and-multi-node-azure-stack-differences"></a>Unterschiede zwischen ASDK und Azure Stack mit mehreren Knoten
Zwischen ASDK-Bereitstellungen mit einem einzelnen Knoten und Azure Stack-Bereitstellungen mit mehreren Knoten gibt es einige wichtige Unterschiede:

|BESCHREIBUNG|ASDK|Azure Stack mit mehreren Knoten|
|-----|-----|-----|
|**Skalieren**|Alle Komponenten werden auf einem Servercomputer mit einem einzelnen Knoten installiert.|Kann zwischen 4 und 16 Knoten umfassen.|
|**Resilienz**|Die Konfiguration mit einem einzelnen Knoten bietet keine Hochverfügbarkeit.|Funktionen für [Hochverfügbarkeit](../operator/azure-stack-overview.md#providing-high-availability) werden unterstützt.|
|**Netzwerk**|Der ASDK-Host leitet den gesamten ASDK-Netzwerkdatenverkehr weiter. Es müssen keine zusätzlichen Switchanforderungen erfüllt werden.|Bei Bereitstellungen mit mehreren Knoten ist eine komplexere [Netzwerkroutinginfrastruktur](../operator/azure-stack-network.md#network-infrastructure) erforderlich, einschließlich TOR-Switches (Top-Of-Rack), BMC-Switches (Baseboard Management Controller, Baseboard-Verwaltungscontroller) und Grenzswitches (Datencenter-Netzwerkswitches).|
|**Patch- und Updateprozess**|Wenn Sie zu einer neuen ASDK-Version wechseln möchten, müssen Sie das ASDK erneut auf dem Development Kit-Hostcomputer bereitstellen.|Zum Aktualisieren der installierten Azure Stack-Version wird der Prozess zum [Patchen und Aktualisieren](../operator/azure-stack-updates.md) verwendet.|
|**Unterstützung**|MSDN-Azure Stack-Forum. Microsoft-Kundendienst und -Support (Customer Service and Support, CSS) steht für produktionsfremde Umgebungen *nicht* zur Verfügung.|[MSDN-Azure Stack-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack) und uneingeschränkter CSS-Support.|
| | |

## <a name="learn-about-available-services"></a>Weitere Informationen zu verfügbaren Diensten
Als Azure Stack-Betreiber müssen Sie wissen, welche Dienste Sie Ihren Benutzern zur Verfügung stellen können. Azure Stack unterstützt einen Teil der Azure-Dienste, und die Liste mit den unterstützten Diensten entwickelt sich im Laufe der Zeit weiter.

### <a name="foundational-services"></a>Grundlegende Dienste
Azure Stack umfasst bei der Bereitstellung des ASDKs standardmäßig folgende grundlegenden Dienste:
- Compute
- Storage
- Netzwerk
- Key Vault

Mit diesen grundlegenden Dienste können Sie Ihren Benutzern Infrastructure-as-a-Service (IaaS) mit Minimalkonfiguration anbieten.

### <a name="additional-services"></a>Zusätzliche Dienste
Derzeit werden folgende zusätzliche PaaS-Dienste (Platform-as-a-Service) unterstützt:
- App Service
- Azure-Funktionen
- SQL- und MySQL-Datenbanken

> [!NOTE]
> Damit diese Dienste Benutzern zur Verfügung gestellt werden können, sind zusätzliche Konfigurationsschritte erforderlich. Die Dienste sind nach der ASDK-Installation nicht standardmäßig verfügbar.

## <a name="service-roadmap"></a>Roadmap für Dienste
Die Unterstützung von Azure-Diensten in Azure Stack wird kontinuierlich weiter ausgebaut. Informationen zur weiteren Entwicklung von Azure Stack finden Sie in der [Roadmap für Azure Stack](https://azure.microsoft.com/roadmap/?tag=azure-stack). 


## <a name="next-steps"></a>Nächste Schritte
Um erste Schritte in Azure Stack ausführen zu können, müssen Sie zunächst [das neueste ASDK](asdk-download.md) herunterladen und den ASDK-Hostcomputer vorbereiten. Nachdem Sie den Development Kit-Host vorbereitet haben, können Sie das ASDK installieren und sich beim Administrator- und Benutzerportal anmelden, um mit der Verwendung von Azure Stack zu beginnen.