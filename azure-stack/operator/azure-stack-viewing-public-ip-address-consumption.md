---
title: Anzeigen der Nutzung öffentlicher IP-Adressen in Azure Stack | Microsoft-Dokumentation
description: Administratoren können die Nutzung öffentlicher IP-Adressen in einer Region anzeigen.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
origin.date: 01/14/2019
ms.date: 03/18/2019
ms.author: v-jay
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 2cb1dde60f8d8aa76e0b678347df9be120c39e7c
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "64308730"
---
# <a name="view-public-ip-address-consumption-in-azure-stack"></a>Anzeigen der Nutzung öffentlicher IP-Adressen in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Als Cloudadministrator können Sie Folgendes anzeigen:
 - Die Anzahl der öffentlichen IP-Adressen, die Mandanten zugeordnet wurden
 - Die Anzahl der öffentlichen IP-Adressen, die noch für die Zuordnung verfügbar sind
 - Der Prozentsatz der öffentlichen IP-Adressen, die an diesem Standort zugeordnet wurden

Auf der Kachel **Nutzung öffentlicher IP-Pools** wird die Anzahl der öffentlichen IP-Adressen angezeigt, die in allen öffentlichen IP-Adresspools verwendet werden. Die Kachel zeigt für jede IP-Adresse die Nutzung für IaaS-VM-Instanzen von Mandanten, Fabric-Infrastrukturdienste und öffentliche IP-Adressressourcen, die von den Mandanten explizit erstellt wurden.

Diese Kachel soll Azure Stack-Betreibern einen Eindruck von der Anzahl öffentlicher IP-Adressen vermitteln, die an diesem Standort verwendet werden. Anhand dieser Anzahl können Administratoren feststellen, ob diese Ressource knapp wird.

Das Menüelement **Öffentliche IP-Adressen** unter **Mandantenressourcen** listet nur die öffentlichen IP-Adressen auf, die *explizit von Mandanten erstellt wurden*. Sie finden das Menüelement im Bereich **Ressourcenanbieter** unter **Netzwerk**. Die Anzahl der **verwendeten** öffentlichen IP-Adressen auf der Kachel **Nutzung öffentlicher IP-Pools** unterscheidet sich immer von der Anzahl auf der Kachel **Öffentliche IP-Adressen** unter **Mandantenressourcen** (d.h. sie ist immer größer).

## <a name="view-the-public-ip-address-usage-information"></a>Anzeigen der Informationen zur Nutzung öffentlicher IP-Adressen

So zeigen Sie die Gesamtanzahl der öffentlichen IP-Adressen an, die in der Region verwendet wurden:

1. Wählen Sie im Azure Stack-Administratorportal **Alle Dienste** aus. Wählen Sie unter der Kategorie **VERWALTUNG** die Option **Netzwerk**.
1. Im Bereich **Netzwerk** wird die Kachel **Nutzung öffentlicher IP-Pools** im Abschnitt **Übersicht** angezeigt.

![Bereich „Netzwerkressourcenanbieter“](media/azure-stack-viewing-public-ip-address-consumption/image01.png)

Die Angabe unter **Verwendet** stellt die Anzahl der zugewiesenen öffentlichen IP-Adressen aus öffentlichen IP-Adresspools dar. Der Wert unter **Frei** gibt die Anzahl der öffentlichen IP-Adressen von öffentlichen IP-Adresspools an, die nicht zugewiesen wurden und weiterhin verfügbar sind. Der Wert von **% verwendet** gibt die Anzahl der verwendeten oder zugewiesenen Adressen als Prozentsatz der Gesamtzahl der öffentlichen IP-Adressen in öffentlichen IP-Adresspools an diesem Speicherort an.

## <a name="view-the-public-ip-addresses-that-were-created-by-tenant-subscriptions"></a>Anzeigen der öffentlichen IP-Adressen, die von Mandantenabonnements erstellt wurden

Klicken Sie unter **Mandantenressourcen** auf **Öffentliche IP-Adressen**. Überprüfen Sie die Liste der öffentlichen IP-Adressen, die explizit von Mandantenabonnements in einer bestimmten Region erstellt wurden.

![Öffentliche IP-Adressen von Mandanten](media/azure-stack-viewing-public-ip-address-consumption/image02.png)

Sie stellen möglicherweise fest, dass einige öffentliche IP-Adressen, die dynamisch zugewiesen wurden, in der Liste angezeigt werden. Ihnen wurde jedoch noch keine Adresse zugeordnet. Die Adressressource wurde im Netzwerkressourcenanbieter erstellt, jedoch noch nicht im Netzwerkcontroller.

Der Netzwerkcontroller weist dieser Ressource erst dann eine Adresse zu, wenn sie an eine Schnittstelle, einen Netzwerkadapter, ein Lastenausgleichsmodul oder ein Gateway für virtuelle Netzwerke gebunden ist. Wenn die öffentliche IP-Adresse an eine Schnittstelle gebunden ist, weist der Netzwerkcontroller ihr eine IP-Adresse zu. Die Adresse wird im Feld **Adresse** angezeigt.

## <a name="view-the-public-ip-address-information-summary-table"></a>Übersichtstabelle zum Anzeigen der Informationen zu öffentlichen IP-Adressen

In verschiedenen Fällen werden öffentliche IP-Adressen zugewiesen, die bestimmen, ob die Adresse in der einen oder anderen Liste angezeigt wird.

| **Zuweisungsfall der öffentlichen IP-Adresse** | **Wird in Nutzungsübersicht angezeigt** | **Wird in Liste öffentlicher IP-Adressen von Mandanten angezeigt** |
| --- | --- | --- |
| Dynamische öffentliche IP-Adresse, die noch nicht einem Netzwerkadapter oder Lastenausgleichsmodul zugewiesen ist (temporär) |Nein  |Ja |
| Dynamische öffentliche IP-Adresse, die einem Netzwerkadapter oder Lastenausgleichsmodul zugewiesen ist. |Ja |Ja |
| Statische öffentliche IP-Adresse, die einem Mandanten-Netzwerkadapter oder Lastenausgleichsmodul zugewiesen ist. |Ja |Ja |
| Statische öffentliche IP-Adresse, die einem Fabricinfrastruktur-Dienstendpunkt zugewiesen ist. |Ja |Nein  |
| Öffentliche IP-Adresse, die implizit für IaaS-VM-Instanzen erstellt wird und für ausgehende NAT im virtuellen Netzwerk verwendet wird. Diese werden im Hintergrund erstellt, wenn ein Mandant eine VM-Instanz erstellt, sodass virtuelle Computer Informationen an das Internet senden können. |Ja |Nein  |

## <a name="next-steps"></a>Nächste Schritte

[Verwalten von Speicherkonten in Azure Stack](azure-stack-manage-storage-accounts.md)

<!-- Update_Description: wording update -->