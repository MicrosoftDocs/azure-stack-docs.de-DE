---
title: Schnellstart zum Erstellen eines Kubernetes-Clusters mit dem Windows Admin Center
description: Erfahren Sie, wie Sie einen Kubernetes-Cluster mit dem Windows Admin Center erstellen.
author: davannaw-msft
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: dawhite
ms.openlocfilehash: ddaac062a147440b4ce290ad988629c417a5932a
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90949376"
---
# <a name="quickstart-create-a-kubernetes-cluster-on-azure-stack-hci-using-windows-admin-center"></a>Schnellstart: Erstellen eines Kubernetes-Clusters in Azure Stack HCI mithilfe des Windows Admin Centers.

> Gilt für: Azure Stack HCI

Nachdem Sie den Azure Kubernetes Service-Host eingerichtet haben, können Sie mithilfe des Windows Admin Centers einen Kubernetes-Cluster erstellen. Informationen, wie Sie stattdessen PowerShell verwenden können, finden Sie unter [Erstellen eines Kubernetes-Clusters mit PowerShell](create-kubernetes-cluster-powershell.md).

Lassen Sie uns anfangen:

1. [Richten Sie Azure Kubernetes Service ein](setup.md), und überprüfen Sie die [Systemanforderungen](system-requirements.md), sofern Sie dies noch nicht getan haben.
1. Um mit dem Erstellen eines Kubernetes-Clusters im Windows Admin Center zu beginnen, klicken Sie auf die Schaltfläche **Hinzufügen** im Gatewaybildschirm.
2. Wählen Sie im Bereich **Ressourcen hinzufügen oder erstellen** unter **Kubernetes Cluster (Vorschau)** die Option **Neu erstellen** aus, um den Kubernetes-Cluster-Assistenten zu starten. Die Schaltfläche „Hinzufügen“ ist in Kubernetes-Clustern zwar in der öffentlichen Vorschau vorhanden, sie ist aber nicht funktionsfähig. Sie können den Assistenten zu jedem Zeitpunkt während der Erstellung des Kubernetes-Clusters beenden, aber beachten Sie, dass Ihr Fortschritt nicht gespeichert wird. 

    ![Veranschaulicht das Blatt „Ressourcen hinzufügen oder erstellen“ im Windows Admin Center, das nun die neue Kachel für Kubernetes-Cluster enthält.](.\media\create-kubernetes-cluster\add-connection.png)

3. Überprüfen Sie die Voraussetzungen für das System, das den Kubernetes-Cluster hosten soll und die für Windows Admin Center. Klicken Sie anschließend auf **Weiter**. 
4. Konfigurieren Sie auf der Seite **Grundlagen** Informationen zu Ihrem Cluster, z. B. Azure Arc-Integration, Azure Kubernetes Service-Hostinformationen und Größe des primären Knotenpools.  Azure Kubernetes Service-Hostinformationen und Größe des primären Knotenpools. Das Feld „Azure Kubernetes Service-Host“ erfordert den vollqualifizierten Domänennamen des Azure Stack HCI-Clusters, in dem Sie Ihren Kubernetes-Cluster bereitstellen möchten. Sie müssen die Hosteinrichtung für dieses System über das Azure Kubernetes Service-Tool abgeschlossen haben. Während der öffentlichen Vorschauphase kann die Knotenanzahl nicht bearbeitet werden und ist standardmäßig 2, aber die Knotengröße kann für größere Workloads konfiguriert werden. Wählen Sie nach Abschluss der Einstellungen **Weiter** aus.

    ![Veranschaulicht die Seite „Grundlagen“ des Kubernetes-Cluster-Assistenten.](.\media\create-kubernetes-cluster\basics.png)

5. Sie können zusätzliche Knotenpools konfigurieren, um Ihre Workloads auf der Seite **Knotenpools** auszuführen. Während der öffentlichen Vorschauphase können Sie bis zu einen Windows-Knotenpool und einen Linux-Knotenpool (zusätzlich zum Systemknotenpool) hinzufügen. Wählen Sie **Weiter**, wenn Sie fertig sind.
6. Geben Sie Ihre Netzwerkkonfiguration auf der Seite **Netzwerk** an. Wenn Sie „Erweitert“ auswählen, können Sie den Adressbereich anpassen, der beim Erstellen eines virtuellen Netzwerks für Knoten im Cluster verwendet wird. Wenn Sie „Grundlegend“ auswählen, werden virtuelle Netzwerke für Knoten im Cluster mit einem Standardadressbereich erstellt. Die Netzwerkeinstellungen (Load Balancer, Netzwerkrichtlinie und HTTP-Anwendungsrouting) können in der öffentlichen Vorschauversion nicht geändert werden. Wählen Sie nach Abschluss der Einstellungen **Weiter** aus.

    ![Veranschaulicht die Seite „Netzwerk“ des Kubernetes-Cluster-Assistenten.](.\media\create-kubernetes-cluster\networking.png)

7. Verbinden Sie auf der Seite **Integration** Ihren Cluster mit zusätzlichen Diensten, z. B. dem Kubernetes-Dashboard, dem beständigen Speicher und Azure Monitor. Der beständige Speicher ist der einzige Dienst, der auf dieser Seite konfiguriert werden muss. In der öffentlichen Vorschauversion nimmt der Ort des beständigen Speichers standardmäßig den Speicherort an, der während der Hosteinrichtung ausgewählt wurde. Dieses Feld kann zurzeit nicht bearbeitet werden. Wählen Sie **Weiter**, wenn Sie fertig sind.
8. Überprüfen Sie Ihre getroffene Auswahl auf der Seite **Überprüfen und erstellen**. Wenn Sie damit zufrieden sind, wählen Sie **Erstellen** aus, um die Bereitstellung zu starten. Der Fortschritt Ihrer Bereitstellung wird am oberen Rand dieser Seite angezeigt. 
9. Nachdem Ihre Bereitstellung abgeschlossen wurde, finden Sie auf der Seite **Nächste Schritte** Informationen zum Verwalten Ihres Clusters. Diese Seite enthält auch Ihren SSH-Schlüssel und das Token für das Kubernetes-Dashboard. Wenn Sie das Kubernetes-Dashboard oder die Azure Arc-Integration im vorherigen Schritt deaktiviert haben, sind einige der Informationen und Anweisungen auf dieser Seite möglicherweise nicht verfügbar oder nicht funktionsfähig.

> [!IMPORTANT] 
> Wir empfehlen, dass Sie Ihren SSH-Schlüssel und das Token des Kubernetes-Dashboards an einem sicheren, zugänglichen Speicherort aufbewahren.

In dieser Schnellstartanleitung haben Sie einen Azure Kubernetes Service-Host eingerichtet und einen Kubernetes-Cluster bereitgestellt. 

Weitere Informationen zu Azure Kubernetes Service in Azure Stack HCI und eine exemplarische Vorgehensweise zum Bereitstellen und Verwalten von Linux-Anwendungen in Azure Kubernetes Service in Azure Stack HCI finden Sie in diesem Tutorial.
