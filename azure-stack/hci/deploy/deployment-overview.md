---
title: Übersicht über die Azure Stack HCI-Bereitstellung
description: Enthält eine Übersicht über den Prozess zur Bereitstellung von Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 12/7/2020
ms.openlocfilehash: 11c82a35d17a61fee474123a658f91ea90790221
ms.sourcegitcommit: 62eb5964a824adf7faee58c1636b17fedf4347e9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96778018"
---
# <a name="what-is-the-deployment-process-for-azure-stack-hci"></a>Was ist der Bereitstellungsprozess für Azure Stack HCI?

> Gilt für: Azure Stack HCI, Version 20H2

Dieses Thema enthält eine allgemeine Schritt-für-Schritt-Übersicht über den Prozess für die Azure Stack HCI-Bereitstellung und Links zu ausführlicheren Informationen.

## <a name="plan"></a>Planen

Führen Sie vor der Bereitstellung eine sorgfältige Planung in Bezug auf den Speicher, das Netzwerk und die Anforderungen an das Clustering an mehreren Standorten (falls zutreffend) durch.

### <a name="plan-storage"></a>Planen des Speichers

Für Azure Stack HCI werden branchenübliche Server mit lokal angefügten Laufwerken verwendet, um softwaredefinierten Speicher zu erstellen, der hochgradig verfügbar und skalierbar ist. Um Ihre Leistungs- und Kapazitätsanforderungen zu erfüllen, sollten Sie beim [Auswählen der Laufwerke](../concepts/choose-drives.md) und [Planen der Volumes](../concepts/plan-volumes.md) mit Bedacht vorgehen.

### <a name="plan-networking"></a>Planen von Netzwerken

Notieren Sie sich die Servernamen, Domänennamen, RDMA-Protokolle und -Versionen sowie die VLAN-ID für Ihre Bereitstellung.

### <a name="plan-stretched-clusters"></a>Planen von gestreckten Clustern

Falls sich Ihre Azure Stack HCI-Bereitstellung über mehrere Standorte erstrecken soll, sollten Sie ermitteln, wie viele Server an jedem Standort benötigt werden und ob die Clusterkonfiguration den Typ „Aktiv/Passiv“ oder „Aktiv/Aktiv“ haben soll. Weitere Informationen finden Sie im Artikel mit der [Übersicht über gestreckte Cluster](../concepts/stretched-clusters.md).

## <a name="deploy"></a>Bereitstellen

Ermitteln Sie vor der Bereitstellung des Betriebssystems, ob Ihre Hardware die [Systemanforderungen](../concepts/system-requirements.md) für Azure Stack HCI erfüllt. [Installieren Sie anschließend Windows Admin Center](/windows-server/manage/windows-admin-center/deploy/install), um Ihren Azure Stack HCI-Cluster zu verwalten.

### <a name="1-deploy-azure-stack-hci"></a>1. Bereitstellen von Azure Stack HCI

[Laden Sie Azure Stack HCI herunter](https://azure.microsoft.com/products/azure-stack/hci/hci-download/), und stellen Sie das Azure Stack HCI-[Betriebssystem](operating-system.md) auf allen Servern bereit, die in den Cluster einbezogen werden sollen. Wenn Sie Azure Stack HCI-Lösungshardware für integrierte Systeme über den [Azure Stack HCI-Katalog](https://aka.ms/azurestackhcicatalog) von Ihrem bevorzugten Hardwarepartner erworben haben, sollte das Azure Stack HCI-Betriebssystem vorinstalliert sein. In diesem Fall können Sie diesen Schritt überspringen und mit Schritt 2 fortfahren.

### <a name="2-create-the-cluster"></a>2. Erstellen Sie den Cluster.

Erstellen Sie einen Failovercluster, indem Sie [Windows Admin Center](create-cluster.md) oder [PowerShell](create-cluster-powershell.md) verwenden. Zur Erzielung von nativer Notfallwiederherstellung und Geschäftskontinuität können Sie einen [gestreckten Cluster](../concepts/stretched-clusters.md) bereitstellen, der über zwei geografisch getrennte Standorte reicht.

### <a name="3-set-up-a-cluster-witness"></a>3. Einrichten eines Clusterzeugen

Das [Einrichten einer Zeugenressource](witness.md) ist für alle Cluster obligatorisch. Für Cluster mit zwei Knoten wird ein Zeuge benötigt, damit beim Versetzen eines Servers in den Offlinezustand nicht auch der andere Knoten nicht mehr verfügbar ist. Cluster mit drei oder mehr Knoten müssen einen Zeugen aufweisen, um eine Situation zu überstehen, in der zwei Server ausfallen oder in den Offlinezustand versetzt werden. 

### <a name="4-register-with-azure"></a>4. Registrieren bei Azure

Für Azure Stack HCI ist eine Verbindung mit Azure erforderlich. Informationen zum Herstellen einer Verbindung zwischen Ihrem Cluster und Azure finden Sie im Artikel zur [Registrierung von Azure Stack HCI bei Azure](register-with-azure.md). Nach der Registrierung wird der Cluster im Hintergrund automatisch verbunden.

### <a name="5-validate-the-cluster"></a>5. Überprüfen des Clusters

Nachdem Sie den Cluster erstellt und registriert haben, sollten Sie die [Clustervalidierungstests ausführen](validate.md). Hierdurch können Sie Probleme mit der Hardware oder Konfiguration ermitteln, bevor der Cluster in der Produktion eingesetzt wird.

### <a name="6-deploy-storage"></a>6. Bereitstellen von Speicher

[Erstellen Sie Volumes](../manage/create-volumes.md) in einem Cluster mit nur einem Standort, oder [erstellen Sie Volumes, und richten Sie die Replikation in einem gestreckten Cluster ein](../manage/create-stretched-volumes.md).

### <a name="7-deploy-workloads"></a>7. Bereitstellen von Workloads

Sie können nun Workloads auf Azure Stack HCI bereitstellen, indem Sie Windows Admin Center verwenden.

Ausführliche Informationen zu den ersten Schritten bei der Bereitstellung einer Azure Kubernetes Service-Workload auf Azure Stack HCI finden Sie in der folgenden Übersicht: [Was ist Azure Kubernetes Service auf Azure Stack HCI?](https://docs.microsoft.com/azure-stack/aks-hci/overview)

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über die Bereitstellung des Azure Stack HCI-Betriebssystems.

> [!div class="nextstepaction"]
> [Bereitstellen des Azure Stack HCI-Betriebssystems](operating-system.md)
