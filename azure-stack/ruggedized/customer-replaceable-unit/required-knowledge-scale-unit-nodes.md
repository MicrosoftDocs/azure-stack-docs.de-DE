---
title: Sie müssen über die erforderlichen Kenntnisse zum Arbeiten mit Skalierungseinheitknoten in einer Tactical Cloud Appliance verfügen.
description: Lesen Sie mehr über die erforderlichen Kenntnisse zum Arbeiten mit Skalierungseinheitknoten in einer Tactical Cloud Appliance (TCA).
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: f108559f191abd7a4d936f9f187384d150df8d62
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97392785"
---
# <a name="required-knowledge-for-working-with-scale-unit-nodes-in-a-tactical-cloud-appliance"></a>Sie müssen über die erforderlichen Kenntnisse zum Arbeiten mit Skalierungseinheitknoten in einer Tactical Cloud Appliance verfügen.

Zum Abschließen von FRU-Prozeduren müssen Ihnen die folgenden Konzepte, Anleitungen und Websites vertraut sein, und Sie müssen darauf zugreifen können.

## <a name="privileged-access-workstation-and-the-privileged-endpoint"></a>Arbeitsstation mit privilegiertem Zugriff und dem privilegierten Endpunkt

Bei der Arbeitsstation mit privilegiertem Zugriff (Privileged Access Workstation, PAW) handelt es sich um eine dedizierte Arbeitsstation die über das Internet und externe Bedrohungsvektoren geschützt wird. Sie kann sich auf dem Hardwarelebenszyklus-Host (HLH) oder extern im Kundennetzwerk mit routingfähigem Zugriff auf die Azure Stack Hub-Skalierungseinheitknoten befinden.

Sie müssen sich über den Remotedesktop anmelden, um auf die PAW zugreifen zu können. Rufen Sie die Anmeldeinformationen und die IP-Adresse des Kunden ab.

Auf diesem Computer können Sie auch auf den privilegierten Endpunkt (PEP) zugreifen.
Weitere Informationen zur Arbeitsstation mit privilegiertem Zugriff sowie zum PEP finden Sie unter „Arbeitsstation mit privilegiertem Zugriff und privilegierter Endpunktzugriff“.

## <a name="azure-stack-hub-administrator-portal"></a>Azure Stack Hub-Verwaltungsportal

Rufen Sie die Anmeldeinformationen und die URL des Administratorportals vom Kunden ab.
Weitere Informationen finden Sie unter [Verwenden des Administratorportals](https://docs.microsoft.com/azure-stack/operator/azure-stack-manage-portals)
[in Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-manage-portals).

## <a name="dell-emc-poweredge-r640-installation-and-service-manual"></a>Installations- und Servicehandbuch für Dell EMC PowerEdge R640

Ausführliche Informationen zu einer physischen Ersetzung der relevanten Hardware finden Sie in den Prozeduren zum Installieren und Entfernen von PowerEdge R640-Systemkomponenten im [Dell EMC PowerEdge R640 Installations- und Service-Handbuch](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4).
Navigieren Sie zum Abschnitt [Installieren und Entfernen](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/installing-and-removing-system-components?guid=guid-5a5943c4-fe26-4faa-a10c-2afa4c1993ff&lang=en-us)
[von Systemkomponenten](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/installing-and-removing-system-components?guid=guid-5a5943c4-fe26-4faa-a10c-2afa4c1993ff&lang=en-us).

## <a name="microsoft-azure-stack-hub-tactical-cloud-appliance-service-manual"></a>Serviceleitfaden zu Tactical Cloud Appliances in Microsoft Azure Stack Hub

Der Serviceleitfaden zu Tactical Cloud Appliances in Microsoft Azure Stack Hub enthält Anleitungen für das Entfernen von Servern von Skalierungseinheitknoten aus Tracewell Tactical Pods.

## <a name="dell-emc-poweredge-idrac"></a>iDRAC-Webschnittstelle für Dell EMC PowerEdge

Sie müssen über Navigation und Verwendung der iDRAC-Webschnittstelle für Dell EMC PowerEdge informiert sein. Weitere Informationen zur Verwendung von iDRAC finden Sie im [Integrierten Benutzerleitfaden zum Dell Remote Access Controller 9](https://www.dell.com/support/manuals/us/en/04/poweredge-r840/idrac9_4.00.00.00_ug_new/overview-of-idrac?guid=guid-a03c2558-4f39-40c8-88b8-38835d0e9003).

