---
title: Übersicht über die Rechenzentrumsfirewall in Azure Stack HCI
description: In diesem Thema erfahren Sie mehr über die Rechenzentrumsfirewall in Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 11/17/2020
ms.openlocfilehash: 34efe18aee4a62481d81a5e9a810a0a71d97b063
ms.sourcegitcommit: 40d3f3f0ac088d1590d1fb64ca05ac1dabf4e00c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874956"
---
# <a name="what-is-datacenter-firewall"></a>Was ist die Rechenzentrumsfirewall?

> Gilt für: Azure Stack HCI, Version 20H2; Windows Server 2019

Die Rechenzentrumsfirewall ist eine Netzwerkschicht und eine zustandsbehaftete softwaredefinierte Netzwerkfirewall mit einem 5-Tupel (Protokoll-, Quell- und Zielportnummern sowie Quell- und Ziel-IP-Adressen). Die Rechenzentrumsfirewall schützt die Datenverkehrsflüsse Ost-West und Nord-Süd in der gesamten Netzwerkschicht der virtuellen Netzwerke und traditionellen VLAN-Netzwerken.

## <a name="how-datacenter-firewall-works"></a>Funktionsweise der Rechenzentrumsfirewall

Sie aktivieren und konfigurieren die Rechenzentrumsfirewall, indem Sie Zugriffssteuerungslisten (ACLs, Access Control Lists) erstellen, die auf ein Subnetz oder eine Netzwerkschnittstelle angewendet werden. Firewallrichtlinien werden am vSwitch-Port jeder Mandanten-VM erzwungen. Die Richtlinien werden über das Mandantenportal übermittelt, und der [Netzwerkcontroller](network-controller-overview.md) verteilt sie an alle anwendbaren Hosts.

Mandantenadministratoren können Firewallrichtlinien installieren und konfigurieren, um ihre Netzwerke vor unerwünschtem Datenverkehr aus Internet- und Intranetnetzwerken zu schützen.

:::image type="content" source="media/datacenter-firewall/multitenant-firewall-overview.png" alt-text="Rechenzentrumsfirewall im Netzwerkstapel" border="false":::

Richtlinien für Rechenzentrumsfirewalls können vom Dienstanbieteradministrator oder dem Mandantenadministrator über den Netzwerkcontroller sowie Northbound-APIs verwaltet werden. Über Windows Admin Center können Sie auch Richtlinien für Rechenzentrumsfirewalls konfigurieren und verwalten.

## <a name="advantages-for-cloud-service-providers"></a>Vorteile für Clouddienstanbieter

Die Rechenzentrumsfirewall bietet die folgenden Vorteile für CSPs:

- Eine hochgradig skalierbare, verwaltbare und diagnostizierbare softwarebasierte Firewalllösung, die Mandanten angeboten werden kann

- Freiheit zum Verschieben von Mandanten-VMs auf verschiedene Computehosts, ohne die Firewallrichtlinien für Mandanten zu beeinträchtigen

    - Wird als Firewall für den Host-Agent des vSwitch-Ports bereitgestellt

    - Mandanten-VMs erhalten die Richtlinien, die ihrer Host-Agent-Firewall des vSwitch-Ports zugewiesen sind

    - Firewallregel werden in jedem vSwitch-Port konfiguriert, unabhängig vom eigentlichen Host, auf dem die VM ausgeführt wird.

- Bietet Schutz für Mandanten-VMs unabhängig vom Gastbetriebssystem des Mandanten

## <a name="advantages-for-tenants"></a>Vorteile für Mandanten

Die Rechenzentrumsfirewall bietet die folgenden Vorteile für Mandanten:

- Möglichkeit zum Definieren von Firewallregeln zum Schutz von Workloads mit Internetzugriff und internen Workloads in Netzwerken

- Möglichkeit zum Definieren von Firewallregeln zum Schutz von Datenverkehr zwischen VMs im gleichen Subnetz der Ebene 2 (L2. Layer 2) und zwischen VMs in unterschiedlichen L2-Subnetzen

- Möglichkeit zum Definieren von Firewallregeln, um den Netzwerkdatenverkehr zwischen lokalen Mandantennetzwerken und ihren virtuellen Netzwerken beim Dienstanbieter zu schützen und zu isolieren

- Möglichkeit zum Anwenden von Firewallrichtlinien auf herkömmliche VLAN-Netzwerke und überlagerungsbasierte virtuelle Netzwerke

## <a name="next-steps"></a>Nächste Schritte

Verwandte Informationen finden Sie außerdem unter:

- [Verwenden der Rechenzentrumsfirewall für softwaredefinierte Netzwerke in der Azure Stack HCI](../manage/use-datacenter-firewall.md)
- [SDN in Azure Stack HCI](software-defined-networking.md)