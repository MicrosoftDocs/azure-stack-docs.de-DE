---
title: Verwalten von Netzwerkressourcen
titleSuffix: Azure Stack Hub
description: Informieren Sie sich, wie Sie Netzwerkressourcen einschließlich des MAC-Adresspools und der Nutzung öffentlicher IP-Adressen in einer Region verwalten.
author: mattbriggs
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: scottnap
ms.lastreviewed: 09/17/2019
ms.openlocfilehash: 3542706d71ed6800cd246cbc0d59a5443ed3edbc
ms.sourcegitcommit: 0a3c8b0bf9c116a5caaeca453a2bbc6e7f7cbfb9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77147820"
---
# <a name="manage-network-resources-in-azure-stack-hub"></a>Verwalten von Netzwerkressourcen in Azure Stack Hub

## <a name="mac-address-pool"></a>MAC-Adresspool

Für Azure Stack Hub wird ein statischer MAC-Adresspool genutzt, um MAC-Adressen automatisch zu generieren und virtuellen Computern (VMs) zuzuweisen. Dieser MAC-Adresspool wird während der Bereitstellung automatisch generiert und umfasst den folgenden Bereich:

- StartMacAddress: 00-1D-D8-B7-00-00
- EndMacAddress: 00-1D-D8-F4-FF-FF

> [!Note]  
> Dieser MAC-Adresspool ist für jedes Azure Stack Hub-System gleich und nicht konfigurierbar.

Je nachdem, wie für die virtuellen Netzwerke eine Verbindung mit vorhandenen Unternehmensnetzwerken hergestellt wird, kommt es ggf. zu doppelten MAC-Adressen von VMs.

Weitere Informationen zur Nutzung des MAC-Adresspools erhalten Sie über das Cmdlet [Get-AzsMacAddressPool](https://docs.microsoft.com/powershell/module/azs.fabric.admin/get-azsmacaddresspool) im PowerShell-Modul für Azure Stack Hub-Administratoren.

## <a name="view-public-ip-address-consumption-in-azure-stack-hub"></a>Anzeigen der Nutzung öffentlicher IP-Adressen in Azure Stack Hub

Als Cloudadministrator können Sie Folgendes anzeigen:
 - Die Anzahl der öffentlichen IP-Adressen, die Mandanten zugeordnet wurden
 - Die Anzahl der öffentlichen IP-Adressen, die noch für die Zuordnung verfügbar sind
 - Der Prozentsatz der öffentlichen IP-Adressen, die an diesem Standort zugeordnet wurden

Auf der Kachel **Nutzung öffentlicher IP-Pools** wird die Anzahl der öffentlichen IP-Adressen angezeigt, die in allen öffentlichen IP-Adresspools verwendet werden. Die Kachel zeigt für jede IP-Adresse die Nutzung für IaaS-VM-Instanzen von Mandanten, Fabric-Infrastrukturdienste und öffentliche IP-Adressressourcen, die von den Mandanten explizit erstellt wurden.

Diese Kachel soll Azure Stack Hub-Operators einen Eindruck von der Anzahl öffentlicher IP-Adressen vermitteln, die an diesem Standort verwendet werden. Anhand dieser Anzahl können Administratoren feststellen, ob diese Ressource knapp wird.

Das Menüelement **Öffentliche IP-Adressen** unter **Mandantenressourcen** listet nur die öffentlichen IP-Adressen auf, die *explizit von Mandanten erstellt wurden*. Sie finden das Menüelement im Bereich **Ressourcenanbieter** -> **Netzwerk**. Die Anzahl der **verwendeten** öffentlichen IP-Adressen auf der Kachel **Nutzung öffentlicher IP-Pools** unterscheidet sich immer von der Anzahl auf der Kachel **Öffentliche IP-Adressen** unter **Mandantenressourcen** (d.h. sie ist immer größer).

### <a name="view-the-public-ip-address-usage-information"></a>Anzeigen der Informationen zur Nutzung öffentlicher IP-Adressen

So zeigen Sie die Gesamtanzahl der öffentlichen IP-Adressen an, die in der Region verwendet wurden:

1. Wählen Sie im Azure Stack Hub-Administratorportal **Alle Dienste** aus. Klicken Sie dann unter der Kategorie **VERWALTUNG** auf **Netzwerk**.
1. Im Bereich **Netzwerk** wird die Kachel **Nutzung öffentlicher IP-Pools** im Abschnitt **Übersicht** angezeigt.

    ![Bereich für Netzwerkressourcenanbieter im Azure Stack Hub-Administratorportal](media/azure-stack-viewing-public-ip-address-consumption/ip-address-consumption-01.png)

Die Angabe unter **Verwendet** stellt die Anzahl der zugewiesenen öffentlichen IP-Adressen aus öffentlichen IP-Adresspools dar. Der Wert unter **Frei** gibt die Anzahl der öffentlichen IP-Adressen von öffentlichen IP-Adresspools an, die nicht zugewiesen wurden und weiterhin verfügbar sind. Der Wert von **% verwendet** gibt die Anzahl der verwendeten oder zugewiesenen Adressen als Prozentsatz der Gesamtzahl der öffentlichen IP-Adressen in öffentlichen IP-Adresspools an diesem Speicherort an.

### <a name="view-the-public-ip-addresses-that-were-created-by-tenant-subscriptions"></a>Anzeigen der öffentlichen IP-Adressen, die von Mandantenabonnements erstellt wurden

Klicken Sie unter **Mandantenressourcen** auf **Öffentliche IP-Adressen**. Überprüfen Sie die Liste der öffentlichen IP-Adressen, die explizit von Mandantenabonnements in einer bestimmten Region erstellt wurden.

![Öffentliche IP-Adressen von Mandanten im Azure Stack Hub-Administratorportal](media/azure-stack-viewing-public-ip-address-consumption/ip-address-consumption-02.png)

Sie stellen möglicherweise fest, dass einige öffentliche IP-Adressen, die dynamisch zugewiesen wurden, in der Liste angezeigt werden. Ihnen wurde jedoch noch keine Adresse zugeordnet. Die Adressressource wurde im Netzwerkressourcenanbieter erstellt, jedoch noch nicht im Netzwerkcontroller.

Der Netzwerkcontroller weist dieser Ressource erst dann eine Adresse zu, wenn sie an eine Schnittstelle, einen Netzwerkadapter, ein Lastenausgleichsmodul oder ein Gateway für virtuelle Netzwerke gebunden ist. Wenn die öffentliche IP-Adresse an eine Schnittstelle gebunden ist, weist der Netzwerkcontroller ihr eine IP-Adresse zu. Die Adresse wird im Feld **Adresse** angezeigt.

### <a name="view-the-public-ip-address-information-summary-table"></a>Übersichtstabelle zum Anzeigen der Informationen zu öffentlichen IP-Adressen

In verschiedenen Fällen werden öffentliche IP-Adressen zugewiesen, die bestimmen, ob die Adresse in der einen oder anderen Liste angezeigt wird.

| **Zuweisungsfall der öffentlichen IP-Adresse** | **Wird in Nutzungsübersicht angezeigt** | **Wird in Liste öffentlicher IP-Adressen von Mandanten angezeigt** |
| --- | --- | --- |
| Dynamische öffentliche IP-Adresse, die noch nicht einem Netzwerkadapter oder Lastenausgleichsmodul zugewiesen ist (temporär). |Nein |Ja |
| Dynamische öffentliche IP-Adresse, die einem Netzwerkadapter oder Lastenausgleichsmodul zugewiesen ist. |Ja |Ja |
| Statische öffentliche IP-Adresse, die einem Mandanten-Netzwerkadapter oder Lastenausgleichsmodul zugewiesen ist. |Ja |Ja |
| Statische öffentliche IP-Adresse, die einem Fabricinfrastruktur-Dienstendpunkt zugewiesen ist. |Ja |Nein |
| Öffentliche IP-Adresse, die implizit für IaaS-VM-Instanzen erstellt wird und für ausgehende NAT im virtuellen Netzwerk verwendet wird. Diese werden im Hintergrund erstellt, wenn ein Mandant eine VM-Instanz erstellt, sodass virtuelle Computer Informationen an das Internet senden können. |Ja |Nein |

## <a name="next-steps"></a>Nächste Schritte

[Verwalten von Speicherkonten in Azure Stack Hub](azure-stack-manage-storage-accounts.md)
