---
title: Erforderliche Kenntnisse zur Verwendung des Hardwarelebenszyklushosts
description: In diesem Artikel erhalten Sie Informationen zu den erforderlichen Kenntnissen zur Verwendung des Hardwarelebenszyklushosts.
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: f4dc52cff6f04cdfdafedd845ccf40843687e7a3
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97392718"
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

## <a name="dell-emc-poweredge-r640-installation-and-service-manual"></a>Installations- und Servicehandbuch für Dell EMC PowerEdge R640

Weitere Informationen zu einer physischen Ersetzung der relevanten Hardware finden Sie in den FRU-Austauschprozeduren für PowerEdge R640 im [Dell](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4)
[EMC PowerEdge R640 Installations- und Service-Handbuch](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4).
Navigieren Sie zum Abschnitt [Installieren und Entfernen von Systemkomponenten](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/installing-and-removing-system-components?guid=guid-5a5943c4-fe26-4faa-a10c-2afa4c1993ff&lang=en-us).

## <a name="microsoft-azure-stack-hub-tactical-cloud-appliance-service-manual"></a>Serviceleitfaden zu Tactical Cloud Appliances in Microsoft Azure Stack Hub

Das Servicehandbuch zu Tactical Cloud Appliances in Microsoft Azure Stack Hub enthält Anleitungen für das Entfernen des Hardwarelebenszyklushosts aus Tracewell Tactical Pods.

## <a name="dell-emc-poweredge-idrac"></a>iDRAC-Webschnittstelle für Dell EMC PowerEdge

Sie müssen über Navigation und Verwendung der iDRAC-Webschnittstelle für Dell EMC PowerEdge informiert sein.

