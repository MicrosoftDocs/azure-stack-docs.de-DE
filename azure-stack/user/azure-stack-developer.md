---
title: Entwickeln von Apps für Azure Stack Hub | Microsoft-Dokumentation
description: Überlegungen zur Entwicklung bei der Prototyperstellung von App in Azure Stack Hub mithilfe von Azure-Diensten.
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
ms.openlocfilehash: ba5aefa61db489f5f7063ebc4785785ba2f26f4c
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75883727"
---
# <a name="develop-for-azure-stack-hub"></a>Entwickeln für Azure Stack Hub

Sie können noch heute mit der Entwicklung von Apps loslegen, auch wenn Sie keinen Zugriff auf eine Azure Stack Hub-Umgebung haben. Azure Stack Hub stellt Microsoft Azure-Dienste bereit, die in Ihrem Rechenzentrum ausgeführt werden, was bedeutet, dass Sie die gleichen Azure-Tools und -Prozesse für die Entwicklung auf Azure Stack Hub verwenden können.

## <a name="development-considerations"></a>Überlegungen zur Entwicklung

Mit etwas Vorbereitung sowie den Informationen aus den folgenden Themen können Sie Azure zum Emulieren einer Azure Stack Hub-Umgebung nutzen.

* In Azure haben Sie die Möglichkeit, Azure Resource Manager-Vorlagen zu erstellen, die in Azure Stack Hub bereitgestellt werden können. In den [Überlegungen zu Vorlagen](azure-stack-develop-templates.md) finden Sie Anleitungen zum Entwickeln von Vorlagen, um die Portabilität zu gewährleisten.
* Zwischen Azure und Azure Stack Hub gibt es Unterschiede in Bezug auf Dienstverfügbarkeit und Dienstversionsverwaltung. Mit dem [Azure Stack Hub-Richtlinienmodul](azure-stack-policy-module.md) können Sie die Azure-Dienstverfügbarkeit und Azure-Ressourcentypen auf das Angebot von Azure Stack Hub beschränken. Durch die Einschränkung von Diensten wird sichergestellt, dass Ihre App Dienste nutzt, die für Azure Stack Hub verfügbar sind.
* Bei den [Azure Stack Hub-Schnellstartvorlagen](https://github.com/Azure/AzureStack-QuickStart-Templates) handelt es sich um allgemeine Szenariobeispiele dazu, wie Sie Vorlagen für die Bereitstellung sowohl in Azure als auch in Azure Stack Hub entwickeln können.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure Stack-Entwicklung finden Sie in den folgenden Artikeln:

* [Bewährte Methoden für Azure Resource Manager-Vorlagen](azure-stack-develop-templates.md)
* [Azure Stack Hub-Schnellstartvorlagen auf GitHub](https://github.com/Azure/AzureStack-QuickStart-Templates)
