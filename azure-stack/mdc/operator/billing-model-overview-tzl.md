---
title: Übersicht über das Kundenabrechnungsmodell in Azure Stack | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Azure Stack-Benutzern der Ressourcenverbrauch in Rechnung gestellt wird.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 12/23/2019
ms.openlocfilehash: bd20dee177ac1f061b954eb0285b3103747675d1
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96934844"
---
# <a name="billing-model-overview"></a>Übersicht über das Abrechnungsmodell

Als MDC- oder Azure Stack Hub Ruggedized-Benutzer basiert die Abrechnung von Microsoft darauf, wie lang Sie über die einzelnen Appliances verfügen. Die Gebühr fällt pro Zeitraum an und umfasst das Recht, die grundlegenden Compute-, Speicher- und Netzwerkdienste zu verwenden. Die Abrechnung erfolgt basierend auf der Nutzung von App Service, Event Hubs und allen anderen PaaS-Dienste sowie von Windows Server-PAYG-VMs, die auf Azure Stack Hub Ruggedized und MDC ausgeführt werden. Wenn Sie über keinerlei Verbindung verfügen und keine Nutzungsdaten melden können, benötigen Sie Kapazitätslizenzen für PaaS-Dienste und BYOL (Bring Your Own Licenses) für Windows-VMs.

Die Abrechnung beginnt 14 Tage nach dem Versand einer bestellten Appliance. Für Azure Stack Hub Containerized beträgt der anfängliche Abrechnungszeitraum ein Jahr. Nach einem Jahr kann die Bestellung um ein weiteres Jahr verlängert werden, und ein weiteres Jahr Nutzung wird in Rechnung gestellt. Wenn die Bestellung nicht verlängert und das Gerät nicht zurückgegeben wird, wird die Abrechnung monatlich fortgesetzt. Für Azure Stack Hub Ruggedized erfolgt die Abrechnung immer monatlich.

Bei Rückgabe der Appliance an Microsoft endet die Abrechnung, sobald die Appliance im Microsoft-Lager eingeht.

## <a name="next-steps"></a>Nächste Schritte

[Analysieren der Azure Stack-Nutzung mit lokalen Verbrauchseinheiten](analyze-usage-tzl.md)
