---
title: Entwickeln von Apps für Azure Stack | Microsoft-Dokumentation
description: Überlegungen zur Entwicklung bei der Prototyperstellung von Anwendungen in Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: d3ebc6b1-0ffe-4d3e-ba4a-388239d6cdc3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: 2f0d311851abe1c0fb01ec08dc82626805b35eb1
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "64310350"
---
# <a name="develop-for-azure-stack"></a>Entwickeln für Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Sie können noch heute mit der Entwicklung von Anwendungen loslegen, auch wenn Sie keinen Zugriff auf eine Azure Stack-Umgebung haben. Da Azure Stack Microsoft Azure-Dienste bereitstellt, die in Ihrem Datencenter ausgeführt werden, können Sie ähnliche Tools und Prozesse zum Entwickeln für Azure Stack verwenden wie für Azure.

## <a name="development-considerations"></a>Überlegungen zur Entwicklung

Mit etwas Vorbereitung sowie den Informationen aus den folgenden Themen können Sie Azure zum Emulieren einer Azure Stack-Umgebung nutzen.

* In Azure haben Sie die Möglichkeit, Azure Resource Manager-Vorlagen zu erstellen, die in Azure Stack bereitgestellt werden können. In den [Überlegungen zu Vorlagen](azure-stack-develop-templates.md) finden Sie Anleitungen zum Entwickeln von Vorlagen, um die Portabilität zu gewährleisten.
* Zwischen Azure und Azure Stack gibt es Unterschiede in Bezug auf Dienstverfügbarkeit und Dienstversionsverwaltung. Mit dem [Azure Stack-Richtlinienmodul](azure-stack-policy-module.md) können Sie die Azure-Dienstverfügbarkeit und Azure-Ressourcentypen auf das Angebot von Azure Stack beschränken. Durch die Einschränkung von Diensten wird sichergestellt, dass Ihre Anwendung Dienste nutzt, die für Azure Stack verfügbar sind.
* Bei den [Azure Stack-Schnellstartvorlagen](https://github.com/Azure/AzureStack-QuickStart-Templates) handelt es sich um allgemeine Szenariobeispiele dazu, wie Sie Vorlagen entwickeln können, die in Azure und in Azure Stack bereitgestellt werden können.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure Stack-Entwicklung finden Sie in den folgenden Artikeln:

* [Bewährte Methoden für Azure Resource Manager-Vorlagen](azure-stack-develop-templates.md)
* [Azure Stack-Schnellstartvorlagen auf GitHub](https://github.com/Azure/AzureStack-QuickStart-Templates)