---
title: Erforderliche Kenntnisse zur Verwendung des Hardwarelebenszyklushosts
description: In diesem Artikel erhalten Sie Informationen zu den erforderlichen Kenntnissen zur Verwendung des Hardwarelebenszyklushosts.
author: PatAltimore
ms.topic: how-to
ms.date: 02/05/2021
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 3d3e650e9b6b1b6c37e2f265aa5c049246600c2f
ms.sourcegitcommit: 5ea0e915f24c8bcddbcaf8268e3c963aa8877c9d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100487781"
---
# <a name="required-knowledge-for-working-with-the-hardware-lifecycle-host"></a>Erforderliche Kenntnisse zur Verwendung des Hardwarelebenszyklushosts

Zum Abschließen von FRU-Prozeduren müssen Ihnen die folgenden Konzepte und Anleitungen vertraut sein, und Sie müssen darauf zugreifen können.

## <a name="hardware-lifecycle-host"></a>Hardwarelebenszyklus-Host

Der Hardwarelebenszyklushost (Hardware Lifecycle Host, HLH) ist ein physischer Verwaltungsserver, der sich auf dem Azure Stack Hub-Rack befindet. Sie können über eine der folgenden drei Methoden eine Verbindung zum Host herstellen, um darauf zuzugreifen:

* Direkt (Notfallwagen)
* iDRAC (Dienstport)
* iDRAC (IP-Zugriff)

Wenn Sie sich innerhalb des Rechenzentrums befinden, können Sie eine direkte Verbindung zum HLH mithilfe von VGA- und USB-Anschlüssen herstellen. Sie können beispielsweise einen Notfallwagen verbinden.

Wenn Sie sich innerhalb des Rechenzentrums befinden, können Sie Ihren Laptop mithilfe eines USB-Kabels mit dem iDRAC 9-Dienstport verbinden. Weitere Informationen finden Sie unter „Zugreifen auf die iDRAC-Schnittstelle über eine direkte USB-Verbindung“.

Arbeiten Sie mit dem Kunden zusammen, um eine Verbindung zum HLH über das Verwaltungsnetzwerk des Kunden und über die Verwaltungsarbeitsstation zur iDRAC-IP herzustellen.

> [!NOTE]
> Nur Netzwerke, die zuvor den Switch-ACLs hinzugefügt wurden, können eine direkte HLH-iDRAC-Verbindung herstellen.

## <a name="credentials"></a>Anmeldeinformationen

Arbeiten Sie mit dem Kunden zusammen, um Anmeldeinformationen für Folgendes zu erhalten:

* HLH
* administrator
* iDRAC-Konto (optional)

Ein Windows-Konto mit vollständigen Administratorrechten

Wenn Sie nicht mit einem Notfallwagen eine direkte Verbindung zum Server herstellen, benötigen Sie Anmeldeinformationen für das iDRAC-Konto, um virtuellen KVM-Zugriff zu erhalten.


