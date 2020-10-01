---
title: Übersicht über die Azure Stack HCI-Bereitstellung
description: Enthält eine Übersicht über den Prozess zur Bereitstellung von Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/09/2020
ms.openlocfilehash: 34a93a65d45861c7c7ff1727347cc95465968151
ms.sourcegitcommit: 69cfff119ab425d0fbb71e38d1480d051fc91216
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/30/2020
ms.locfileid: "91572516"
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

### <a name="1-before-you-begin"></a>1. Voraussetzungen

[Ermitteln Sie zunächst, ob Ihre Hardware die grundlegenden Anforderungen erfüllt, und sammeln Sie die erforderlichen Informationen](before-you-start.md) für die Bereitstellung von Azure Stack HCI. [Installieren Sie anschließend Windows Admin Center](/windows-server/manage/windows-admin-center/deploy/install), um Ihren Azure Stack HCI-Cluster zu verwalten.

### <a name="2-deploy-azure-stack-hci"></a>2. Bereitstellen von Azure Stack HCI

[Laden Sie Azure Stack HCI herunter](https://azure.microsoft.com/products/azure-stack/hci/hci-download/), und stellen Sie das Azure Stack HCI-[Betriebssystem](operating-system.md) auf allen Servern bereit, die in den Cluster einbezogen werden sollen.

### <a name="3-create-the-cluster"></a>3. Erstellen des Clusters

Erstellen Sie einen Failovercluster, indem Sie [Windows Admin Center](create-cluster.md) oder [PowerShell](create-cluster-powershell.md) verwenden. Zur Erzielung von nativer Notfallwiederherstellung und Geschäftskontinuität können Sie einen [gestreckten Cluster](../concepts/stretched-clusters.md) bereitstellen, der über zwei geografisch getrennte Standorte reicht.

### <a name="4-set-up-a-cluster-witness"></a>4. Einrichten eines Clusterzeugen

Das [Einrichten einer Zeugenressource](witness.md) ist für alle Cluster obligatorisch. Für Cluster mit zwei Knoten wird ein Zeuge benötigt, damit beim Versetzen eines Servers in den Offlinezustand nicht auch der andere Knoten nicht mehr verfügbar ist. Cluster mit drei oder mehr Knoten müssen einen Zeugen aufweisen, um eine Situation zu überstehen, in der zwei Server ausfallen oder in den Offlinezustand versetzt werden. 

### <a name="5-register-with-azure"></a>5. Registrieren bei Azure

Für Azure Stack HCI ist eine Verbindung mit Azure erforderlich. Informationen zum Herstellen einer Verbindung zwischen Ihrem Cluster und Azure finden Sie im Artikel zur [Registrierung von Azure Stack HCI bei Azure](register-with-azure.md). Nach der Registrierung wird der Cluster im Hintergrund automatisch verbunden.

### <a name="6-validate-the-cluster"></a>6. Überprüfen des Clusters

Nachdem Sie den Cluster erstellt und registriert haben, sollten Sie die [Clustervalidierungstests ausführen](validate.md). Hierdurch können Sie Probleme mit der Hardware oder Konfiguration ermitteln, bevor der Cluster in der Produktion eingesetzt wird.

### <a name="7-deploy-storage"></a>7. Bereitstellen von Speicher

[Erstellen Sie Volumes](../manage/create-volumes.md) in einem Cluster mit nur einem Standort, oder [erstellen Sie Volumes, und richten Sie die Replikation in einem gestreckten Cluster ein](../manage/create-stretched-volumes.md).

### <a name="8-deploy-workloads"></a>8. Bereitstellen von Workloads

Sie können nun [virtuelle Computer erstellen](../manage/vm.md) und Workloads in Azure Stack HCI bereitstellen, indem Sie Windows Admin Center verwenden.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich darüber, welche Schritte Sie vor der Bereitstellung von Azure Stack HCI ausführen müssen.

> [!div class="nextstepaction"]
> [Voraussetzungen](before-you-start.md)
