---
title: Was ist Azure Kubernetes Service in Azure Stack HCI?
description: Azure Kubernetes Service in Azure Stack HCI ist eine lokale Implementierung von Azure Kubernetes Service (AKS), mit dem ausgeführte Containeranwendungen im großen Stil automatisiert werden.
ms.topic: overview
author: jasongerend
ms.author: jgerend
ms.date: 09/22/2020
ms.openlocfilehash: ef2cc07ca9c228b9d427d11f8065e2d943e1626b
ms.sourcegitcommit: 296c95cad20ed62bdad0d27f1f5246bfc1c81d5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93064615"
---
# <a name="what-is-azure-kubernetes-service-on-azure-stack-hci"></a>Was ist Azure Kubernetes Service in Azure Stack HCI?

Azure Kubernetes Service in Azure Stack HCI ist eine lokale Implementierung von Azure Kubernetes Service (AKS), mit dem ausgeführte Containeranwendungen im großen Stil automatisiert werden. Azure Kubernetes Service ist nun in Azure Stack HCI als Vorschauversion verfügbar, sodass der Einstieg in das Hosten von Linux- und Windows-Container in Ihrem Rechenzentrum erleichtert wird.

Um mit dem lokalen Azure Kubernetes Service zu beginnen, [registrieren Sie sich für die Vorschau](https://aka.ms/AKS-HCI-Evaluate) (während der Vorschauphase fallen keine zusätzlichen Kosten an), und lesen Sie dann [Einrichten von Azure Kubernetes Service in Azure Stack HCI](setup.md). Informationen, wie Sie stattdessen Azure Kubernetes Service zum Orchestrieren Ihrer cloudbasierten Container verwenden, finden Sie unter [Azure Kubernetes Service in Azure](/azure/aks/intro-kubernetes).

In den folgenden Abschnitten werden einige der Gründe für die Verwendung von Azure Kubernetes Service in Azure Stack HCI erläutert. Anschließend werden einige häufig gestellte Fragen zu dem Dienst und zu den ersten Schritten damit beantwortet. Hintergrundinformationen zu Containern finden Sie unter [Windows und Container](/virtualization/windowscontainers/about/).

## <a name="automate-management-of-containerized-applications"></a>Automatisieren der Verwaltung containerisierter Anwendungen

Zwar können Sie ein paar Container manuell mithilfe von Docker und Windows verwalten, doch Apps nutzen häufig fünf, zehn oder sogar Hunderte von Containern, wo dann der Kubernetes-Orchestrator ins Spiel kommt.

Kubernetes ist ein Open-Source-Orchestrator zum Automatisieren der Verwaltung von Containern im großen Stil. Azure Kubernetes Service vereinfacht die lokale Kubernetes-Bereitstellung, indem er Assistenten zum Einrichten von Kubernetes und wesentlichen Add-Ons in Azure Stack HCI sowie zum Erstellen von Kubernetes-Clustern zum Hosten Ihrer Workloads bereitstellt.

Im Folgenden finden Sie einige der Funktionen, die von Azure Kubernetes Service während der Vorschauphase in Azure Stack HCI bereitgestellt werden:

- Bereitstellen containerisierter Apps im großen Stil in einem Cluster aus VMs (als Kubernetes-Cluster bezeichnet), die über den Azure Stack HCI-Cluster verteilt ausgeführt werden
- Failover bei Ausfall eines Knotens im Kubernetes-Cluster
- Bereitstellen und Verwalten von Linux- und Windows-basierten containerisierten Apps
- Planen von Workloads
- Überwachen der Integrität
- Zentrales Hoch- oder Herunterskalieren durch Hinzufügen oder Entfernen von Knoten zum Kubernetes-Cluster
- Verwalten von Netzwerken
- Ermitteln von Diensten
- Koordinieren von App-Upgrades
- Zuweisen von Pods zu Clusterknoten mit Clusterknotenaffinität

Weitere Informationen zu Kubernetes finden Sie unter [Kubernetes.io](https://kubernetes.io).

## <a name="simplify-setting-up-kubernetes"></a>Vereinfachen der Einrichtung von Kubernetes

Azure Kubernetes Service vereinfacht den Vorgang des Einrichtens von Kubernetes in Azure Stack HCI und umfasst die folgenden Funktionen:

- Einen Windows Admin Center-Assistenten zum Einrichten von Kubernetes und seiner Abhängigkeiten (z. B. kubeadm, kubelet, kubectl und ein Pod-Netzwerk-Add-on).
- Einen Windows Admin Center-Assistenten zum Erstellen von Kubernetes-Clustern für die Ausführung Ihrer containerisierten Anwendungen.
- PowerShell-Cmdlets zum Einrichten von Kubernetes und Erstellen von Kubernetes-Clustern für den Fall, dass Sie lieber ein Skript für die Hosteinrichtung und Kubernetes-Clustererstellung schreiben möchten.

## <a name="view-and-manage-kubernetes-using-on-premises-tools-or-azure-arc"></a>Anzeigen und Verwalten von Kubernetes mittels lokaler Tools oder Azure Arc

Nachdem Sie Azure Kubernetes Service in Ihrem Azure Stack HCI-Cluster eingerichtet und einen Kubernetes-Cluster erstellt haben, bieten wir Ihnen mehrere Möglichkeiten zum Verwalten und Überwachen Ihrer Kubernetes-Infrastruktur:

- **Lokal mithilfe von beliebten Tools wie Kubectl und Kubernetes-Dashboard** : Verwenden einer webbasierte Open Source-Schnittstelle zum Bereitstellen von Anwendungen in einem Kubernetes-Cluster, zum Verwalten von Clusterressourcen, für die Problembehandlung und zum Anzeigen ausgeführter Anwendungen.
- **Im Azure-Portal unter Verwendung von Azure Arc** : Verwenden eines Azure-Diensts, um Azure Kubernetes Service und Kubernetes-Cluster zu verwalten, die in Ihrer Cloud- und lokalen Umgebungen bereitgestellt sind. Sie können Azure Arc verwenden, um Kubernetes-Cluster sowie Knoten zu einem Kubernetes-Cluster hinzuzufügen und zu entfernen, Netzwerkeinstellungen zu ändern und Add-Ons zu installieren.
<br>Azure Arc ermöglicht Ihnen außerdem die Verwaltung Ihrer Kubernetes-Cluster mit anderen Azure-Diensten, einschließlich:

  - Azure Monitor
  - Azure Policy
  - Rollenbasierte Access Control

## <a name="run-linux-and-windows-containers"></a>Ausführen von Linux- und Windows-Containern

Azure Kubernetes Service unterstützt vollständig sowohl Linux- als auch Windows-basierte Container. Wenn Sie einen Kubernetes-Cluster in Azure Stack HCI erstellen, können Sie auswählen, ob Knotenpools (Gruppen identischer VMs) zum Ausführen von Linux-Containern, Windows-Containern oder von beiden erstellt werden sollen. 

Azure Kubernetes Service erstellt die Linux- und Windows-VMs, sodass Sie die Linux- oder Windows-Betriebssysteme nicht direkt verwalten müssen.

## <a name="secure-your-container-infrastructure"></a>Schützen Ihrer Containerinfrastruktur

Azure Kubernetes Service umfasst eine Reihe von Funktionen zum Schutz ihrer Containerinfrastruktur:

- **Die Hypervisor-basierte Isolation für Workerknoten** : Jeder Kubernetes-Cluster wird auf seinem eigenen dedizierten und isolierten Satz von virtuellen Computern ausgeführt, sodass Mandanten dieselbe physische Infrastruktur gemeinsam nutzen können.
- **Von Microsoft verwalteten Linux- und Windows-Images für Workerknoten** : Auf Workerknoten werden von Microsoft erstellte Images von virtuellen Linux- und Windows-Computern ausgeführt, um bewährte Sicherheitsmethoden einzuhalten. Außerdem aktualisiert Microsoft diese Images monatlich mit den neuesten Sicherheitsupdates.

Sicherheit ist ein fortlaufender Investitionsbereich für die Vorschauversion von Azure Kubernetes Service in Azure Stack HCI – bleiben Sie also dran.

## <a name="where-can-i-run-azure-kubernetes-service"></a>Wo kann ich Azure Kubernetes Service ausführen?

Azure Kubernetes Service ist auf den folgenden Plattformen verfügbar:

- In der Azure Cloud über [Azure Kubernetes Service in Azure](/azure/aks/intro-kubernetes)
- Lokal über Azure Kubernetes Service in Azure Stack HCI (wovon dieser Artikel vollständig handelt)
- Lokal in einer Azure Stack Hub-Umgebung, in der die [AKS-Engine in Azure Stack Hub](../user/azure-stack-kubernetes-aks-engine-overview.md) verwendet wird.

## <a name="how-does-kubernetes-work-on-azure-stack-hci"></a>Wie funktioniert Kubernetes in Azure Stack HCI?

Azure Kubernetes Service funktioniert bei der Ausführung in Azure Stack HCI ein wenig anders als bei der Verwendung in der Azure Cloud:

- Der Kubernetes-Dienst in Azure ist ein gehosteter Dienst, bei dem ein großer Teil der Verwaltungsinfrastruktur für Kubernetes (Steuerungsebene) für Sie verwaltet wird. Sowohl die Steuerungsebene als auch Ihre containerisierten Anwendungen werden in virtuellen Azure-Computern ausgeführt.
- Mit Azure Kubernetes Service in Azure Stack HCI richten Sie den Dienst direkt in Ihrem Azure Stack HCI-Cluster ein, sodass Sie quasi die Kontrolle über die Steuerungsebene erhalten. Die Steuerungsebene, Ihre containerisierten Anwendungen und Azure Kubernetes Service selbst werden alle auf virtuellen Computern ausgeführt, die von Ihrem hyperkonvergenten Cluster gehostet werden.

Nachdem Azure Kubernetes Service in Ihrem Azure Stack HCI-Cluster eingerichtet wurde, funktioniert er ähnlich wie der gehostete Azure Kubernetes-Dienst: Sie verwenden den Dienst, um Kubernetes-Cluster zu erstellen, in denen Ihre containerisierten Anwendungen ausgeführt werden. Diese Kubernetes-Cluster sind Gruppen von VMs, die als Workerknoten fungieren und Ihre Anwendungscontainer ausführen. Der Kubernetes-Cluster enthält außerdem eine Steuerungsebene, die aus Kubernetes-Systemdiensten besteht, die zum Orchestrieren der Anwendungscontainer verwendet werden.

Im Folgenden finden Sie ein paar vereinfachte Diagramme, die zeigen, wie die Architekturen von Azure Kubernetes Service sich bei der Ausführung in Azure und in Azure Stack HCI unterscheiden.

:::image type="content" source="media\overview\aks-azure-architecture.png" alt-text="Architektur des in Azure gehosteten Azure Kubernetes Service, die zeigt, wie die Plattformdienste und der Großteil der Steuerungsebene von Azure verwaltet werden, während Kubernetes-Cluster, in denen Ihre containerisierten Anwendungen ausgeführt werden, vom Kunden verwaltet werden." lightbox="media\overview\aks-azure-architecture.png":::

:::image type="content" source="media\overview\aks-hci-architecture.png" alt-text="Architektur von Azure Kubernetes Service in Azure Stack HCI, die zeigt, wie alles auf dem Azure Stack HCI-Cluster ausgeführt wird, einschließlich der Azure Kubernetes Service-Plattform, der Steuerungsebene und der Kubernetes-Cluster, die ihre containerisierten Anwendungen ausführen." lightbox="media\overview\aks-hci-architecture.png":::

## <a name="what-you-need-to-get-started"></a>Was Sie für die ersten Schritte benötigen

In den folgenden Abschnitten wird zusammengefasst, was Sie zum Ausführen von Azure Kubernetes Service in Azure Stack HCI benötigen. Die vollständigen Details erfahren Sie unter [Vor der Installation von Azure Kubernetes Service in Azure Stack HCI](system-requirements.md).

### <a name="on-your-windows-admin-center-system"></a>Auf Ihrem Windows Admin Center-System

Für Ihr Windows Admin Center-Verwaltungssystem gelten die folgenden Anforderungen:

- Windows 10 (wir unterstützen zurzeit keine Windows Admin Center-Server)
- 60 GB freier Speicherplatz
- Registrierung bei Azure
- In derselben Domäne wie der Azure Stack HCI-Cluster

### <a name="on-the-azure-stack-hci-cluster-that-hosts-azure-kubernetes-service"></a>Im Azure Stack HCI-Cluster, der Azure Kubernetes Service hostet

Für den Cluster, der Azure Stack HCI, Version 20H2 oder höher, ausführt, gelten die folgenden Anforderungen:

- Maximal vier Server im Cluster für diese Vorschauversion
- 1 TB verfügbare Kapazität im Speicherpool für Azure Kubernetes Service
- Mindestens 30 GB verfügbarer Arbeitsspeicher für die Ausführung von Azure Kubernetes Service-VMs
- Alle Server im Cluster müssen die Region und Sprachauswahl „EN-US“ für diese Vorschauversion verwenden.

Allgemeine Azure Stack HCI-Systemanforderungen finden Sie unter [Systemanforderungen für Azure Stack HCI](../hci/concepts/system-requirements.md).

### <a name="the-network-configuration-for-azure-stack-hci"></a>Die Netzwerkkonfiguration für Azure Stack HCI

Für das Netzwerk, das mit den virtuellen Computern im Azure Stack HCI-Cluster verbunden ist, ist ein dedizierter Bereich von DHCP-IPv4-Adressen erforderlich, die für Azure Kubernetes Service verfügbar sind, und auf die die VMs im Azure Stack HCI-Cluster zugreifen können.

Sie können keine VLAN-Tags in Ihrem Netzwerk für Azure Kubernetes Service in Azure Stack HCI verwenden. Verwenden Sie Zugriffsports (ohne Tags) an Ihren Netzwerkswitches für das Netzwerk, das von Azure Stack HCI und den virtuellen Azure Kubernetes Service-Computern verwendet wird.

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den ersten Schritten mit Azure Kubernetes Service in Azure Stack HCI finden Sie in folgenden Artikeln:

- [Überprüfen der Anforderungen](system-requirements.md)
- [Einrichten von Azure Kubernetes Service in Azure Stack HCI](create-kubernetes-cluster.md)
