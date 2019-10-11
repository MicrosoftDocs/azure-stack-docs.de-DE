---
title: Entwickeln von Apps für Azure Stack | Microsoft-Dokumentation
description: Überlegungen zur Entwicklung bei der Prototyperstellung von App in Azure Stack mithilfe von Azure-Diensten
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
ms.date: 10/01/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 0f1450ff7ce6d7e4cffee3d31f80b046f07f9ac1
ms.sourcegitcommit: bbf3edbfc07603d2c23de44240933c07976ea550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71714771"
---
# <a name="develop-for-azure-stack"></a>Entwickeln für Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Sie können noch heute mit der Entwicklung von Apps loslegen, auch wenn Sie keinen Zugriff auf eine Azure Stack-Umgebung haben. Azure Stack stellt Microsoft Azure-Dienste bereit, die in Ihrem Rechenzentrum ausgeführt werden, was bedeutet, dass Sie die gleichen Azure-Tools und -Prozesse für die Entwicklung auf Azure Stack verwenden können.

## <a name="development-considerations"></a>Überlegungen zur Entwicklung

Mit etwas Vorbereitung sowie den Informationen aus den folgenden Themen können Sie Azure zum Emulieren einer Azure Stack-Umgebung nutzen.

* In Azure haben Sie die Möglichkeit, Azure Resource Manager-Vorlagen zu erstellen, die in Azure Stack bereitgestellt werden können. In den [Überlegungen zu Vorlagen](azure-stack-develop-templates.md) finden Sie Anleitungen zum Entwickeln von Vorlagen, um die Portabilität zu gewährleisten.
* Zwischen Azure und Azure Stack gibt es Unterschiede in Bezug auf Dienstverfügbarkeit und Dienstversionsverwaltung. Mit dem [Azure Stack-Richtlinienmodul](azure-stack-policy-module.md) können Sie die Azure-Dienstverfügbarkeit und Azure-Ressourcentypen auf das Angebot von Azure Stack beschränken. Durch die Einschränkung von Diensten wird sichergestellt, dass Ihre App Dienste nutzt, die für Azure Stack verfügbar sind.
* Bei den [Azure Stack-Schnellstartvorlagen](https://github.com/Azure/AzureStack-QuickStart-Templates) handelt es sich um allgemeine Szenariobeispiele dazu, wie Sie Vorlagen für die Bereitstellung sowohl in Azure als auch in Azure Stack entwickeln können.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure Stack-Entwicklung finden Sie in den folgenden Artikeln:

* [Bewährte Methoden für Azure Resource Manager-Vorlagen](azure-stack-develop-templates.md)
* [Azure Stack-Schnellstartvorlagen auf GitHub](https://github.com/Azure/AzureStack-QuickStart-Templates)
