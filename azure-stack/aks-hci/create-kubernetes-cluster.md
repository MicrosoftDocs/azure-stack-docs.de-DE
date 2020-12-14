---
title: Schnellstart zum Erstellen eines Kubernetes-Clusters mit dem Windows Admin Center
description: Erfahren Sie, wie Sie einen Kubernetes-Cluster mit dem Windows Admin Center erstellen.
author: davannaw-msft
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: dawhite
ms.openlocfilehash: 7a9c1bf5d89348175b7ac25a149fe01f384964a2
ms.sourcegitcommit: 0efffe1d04a54062a26d5c6ce31a417f511b9dbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96612402"
---
# <a name="quickstart-create-a-kubernetes-cluster-on-azure-stack-hci-using-windows-admin-center"></a>Schnellstart: Erstellen eines Kubernetes-Clusters in Azure Stack HCI mithilfe des Windows Admin Centers.

> Gilt für: Azure Stack HCI, Windows Server 2019 Datacenter

Nachdem Sie den Azure Kubernetes Service-Host eingerichtet haben, können Sie mithilfe des Windows Admin Centers einen Kubernetes-Cluster erstellen. Informationen, wie Sie stattdessen PowerShell verwenden können, finden Sie unter [Erstellen eines Kubernetes-Clusters mit PowerShell](create-kubernetes-cluster-powershell.md).

Bevor Sie mit dem Assistenten zum Erstellen von Kubernetes-Clustern fortfahren, stellen Sie sicher, dass Sie [Azure Kubernetes Service eingerichtet](setup.md) und die [Systemanforderungen](system-requirements.md) überprüft haben, falls dies noch nicht geschehen ist. Der Assistent zum Erstellen von Kubernetes-Clustern kann über die Seite [Alle Verbindungen](#creating-a-kubernetes-cluster-from-the-all-connections-page) oder über das [Azure Kubernetes Service-Hostdashboard](#creating-a-kubernetes-cluster-from-the-azure-kubernetes-service-host-dashboard) erreicht werden.

## <a name="creating-a-kubernetes-cluster-from-the-all-connections-page"></a>Erstellen eines Kubernetes-Clusters auf der Seite „Alle Verbindungen“ 

Es gibt zwei Möglichkeiten, einen Kubernetes-Cluster in Windows Admin Center zu erstellen. Eine dieser Optionen besteht darin, die Seite **Alle Verbindungen** zu verwenden. Führen Sie die folgenden Schritte aus, und fahren Sie dann mit dem Abschnitt [Assistent zum Erstellen von Kubernetes-Clustern](#the-kubernetes-cluster-create-wizard) fort: 

1. Um mit dem Erstellen eines Kubernetes-Clusters im Windows Admin Center zu beginnen, klicken Sie auf die Schaltfläche **Hinzufügen** im Gatewaybildschirm. 
2. Wählen Sie im Bereich **Ressourcen hinzufügen oder erstellen** unter **Kubernetes Cluster (Vorschau)** die Option **Neu erstellen** aus, um den Kubernetes-Cluster-Assistenten zu starten. Die Schaltfläche „Hinzufügen“ ist in Kubernetes-Clustern zwar in der öffentlichen Vorschau vorhanden, sie ist aber nicht funktionsfähig. Sie können den Assistenten zu jedem Zeitpunkt während der Erstellung des Kubernetes-Clusters beenden. Beachten Sie aber, dass Ihr Fortschritt nicht gespeichert wird. 


    ![Veranschaulicht das Blatt „Ressourcen hinzufügen oder erstellen“ in Windows Admin Center, das nun die neue Kachel für Kubernetes-Cluster enthält.](.\media\create-kubernetes-cluster\add-connection.png)
  
## <a name="creating-a-kubernetes-cluster-from-the-azure-kubernetes-service-host-dashboard"></a>Erstellen eines Kubernetes-Clusters mithilfe des Azure Kubernetes Service-Hostdashboards  

Sie können einen Kubernetes-Clusters auch mithilfe des Azure Kubernetes Service-Hostdashboards erstellen. Dieses Dashboard finden Sie im Azure Kubernetes Service-Tool, wenn Sie mit dem System verbunden sind, das einen Azure Kubernetes Service-Host verwendet. Führen Sie die folgenden Schritte aus, und fahren Sie dann mit dem Abschnitt [Assistent zum Erstellen von Kubernetes-Clustern](#the-kubernetes-cluster-create-wizard) fort: 

1. Stellen Sie eine Verbindung mit dem System her, in dem Sie Ihren Kubernetes-Cluster erstellen möchten, und navigieren Sie zum **Azure Kubernetes Service**-Tool. Für dieses System muss bereits ein Azure Kubernetes Service-Host eingerichtet sein.
2. Wählen Sie unter der Überschrift **Kubernetes-Cluster** die Schaltfläche **Cluster hinzufügen** aus.

![Zeigt das Dashboard des Azure Kubernetes Service-Tools, das nach dem Einrichten eines Azure Kubernetes Service-Hosts angezeigt wird.](.\media\setup\dashboard.png)
  
## <a name="the-kubernetes-cluster-create-wizard"></a>Der Assistent zum Erstellen von Kubernetes-Clustern
Sie erreichen den Assistenten zum Erstellen von Kubernetes-Clustern über die Seite **Alle Verbindungen** oder über das Azure Kubernetes Service-Tool. Lassen Sie uns anfangen:  

1. Überprüfen Sie die Voraussetzungen für das System, das den Kubernetes-Cluster hosten soll und die für Windows Admin Center. Klicken Sie anschließend auf **Weiter**. 
2. Konfigurieren Sie auf der Seite **Grundlagen** Informationen zu Ihrem Cluster, z. B. Azure Arc-Integration, Azure Kubernetes Service-Hostinformationen und Größe des primären Knotenpools. Das Feld „Azure Kubernetes Service-Host“ erfordert den vollqualifizierten Domänennamen des Azure Stack HCI- oder Windows Server 2019 Datacenter-Clusters, den Sie beim Durcharbeiten der Seite [Setup](setup.md) verwendet haben. Sie müssen die Hosteinrichtung für dieses System über das Azure Kubernetes Service-Tool abgeschlossen haben. Während der öffentlichen Vorschauphase kann die Knotenanzahl nicht bearbeitet werden und ist standardmäßig 2, aber die Knotengröße kann für größere Workloads konfiguriert werden. Wählen Sie nach Abschluss der Einstellungen **Weiter** aus.

 [ ![Zeigt die Seite „Grundlagen“ des Kubernetes-Cluster-Assistenten.](.\media\create-kubernetes-cluster\basics.png) ](.\media\create-kubernetes-cluster\basics.png#lightbox)
 
3. Sie können zusätzliche Knotenpools konfigurieren, um Ihre Workloads auf der Seite **Knotenpools** auszuführen. Während der öffentlichen Vorschauphase können Sie bis zu einen Windows-Knotenpool und einen Linux-Knotenpool (zusätzlich zum Systemknotenpool) hinzufügen. Wenn Sie die Azure Arc-Integration später in diesem Assistenten aktivieren möchten, benötigen Sie mindestens einen Linux-Knotenpool, der nicht der primäre Knotenpool ist. Wählen Sie **Weiter**, wenn Sie fertig sind.
4. Geben Sie Ihre Netzwerkkonfiguration auf der Seite **Netzwerk** an. Wenn Sie „Erweitert“ auswählen, können Sie den Adressbereich anpassen, der beim Erstellen eines virtuellen Netzwerks für Knoten im Cluster verwendet wird. Wenn Sie „Grundlagen“ auswählen, werden virtuelle Netzwerke für Knoten im Cluster mit einem Standardadressbereich erstellt. Die Netzwerkeinstellungen (Load Balancer, Netzwerkrichtlinie und HTTP-Anwendungsrouting) können in der öffentlichen Vorschauversion nicht geändert werden. Wählen Sie nach Abschluss der Einstellungen **Weiter** aus.

    [ ![Zeigt die Seite „Netzwerk“ des Kubernetes-Cluster-Assistenten.](.\media\create-kubernetes-cluster\networking.png) ](\media\create-kubernetes-cluster\networking.png#lightbox)

5. Verbinden Sie auf der Seite **Integration** Ihren Cluster mit zusätzlichen Diensten, z. B. mit permanentem Speicher. Sie müssen permanenten Speicher auf dieser Seite konfigurieren. In der öffentlichen Vorschauversion nimmt der Ort des permanenten Speichers standardmäßig den Speicherort an, der während der Hosteinrichtung ausgewählt wurde. Dieses Feld kann zurzeit nicht bearbeitet werden. Wählen Sie **Weiter**, wenn Sie fertig sind.
6. Überprüfen Sie Ihre getroffene Auswahl auf der Seite **Überprüfen und erstellen**. Wenn Sie damit zufrieden sind, wählen Sie **Erstellen** aus, um die Bereitstellung zu starten. Der Fortschritt Ihrer Bereitstellung wird am oberen Rand dieser Seite angezeigt. 
7. Nachdem Ihre Bereitstellung abgeschlossen wurde, finden Sie auf der Seite **Nächste Schritte** Informationen zum Verwalten Ihres Clusters. Diese Seite enthält auch Ihren SSH-Schlüssel. Wenn Sie die Azure Arc-Integration im vorherigen Schritt deaktiviert haben, sind einige der Informationen und Anweisungen auf dieser Seite möglicherweise nicht verfügbar oder nicht funktionsfähig.

> [!IMPORTANT] 
> Es wird empfohlen, Ihren SSH-Schlüssel an einem sicheren, zugänglichen Speicherort zu speichern.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen Kubernetes-Cluster bereitgestellt. Weitere Informationen zu Azure Kubernetes Service auf Azure Stack HCI und eine exemplarische Vorgehensweise zum Bereitstellen und Verwalten von Linux-Anwendungen in AKS auf Azure Stack HCI finden Sie im folgenden Tutorial:

- [Tutorial: Bereitstellen von Linux-Anwendungen in Azure Kubernetes Service in Azure Stack HCI](deploy-linux-application.md)
