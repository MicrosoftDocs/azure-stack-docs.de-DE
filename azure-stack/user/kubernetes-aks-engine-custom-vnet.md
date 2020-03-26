---
title: Bereitstellen eines Kubernetes-Clusters in einem benutzerdefinierten virtuellen Netzwerk in Azure Stack Hub
description: Hier erfahren Sie, wie Sie einen Kubernetes-Cluster in einem benutzerdefinierten virtuellen Netzwerk in Azure Stack Hub bereitstellen.
author: mattbriggs
ms.topic: article
ms.date: 3/19/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 3/19/2020
ms.openlocfilehash: aac2f9a0991bdae7f15d7fc54517a880ab384785
ms.sourcegitcommit: 17be49181c8ec55e01d7a55c441afe169627d268
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/21/2020
ms.locfileid: "80068943"
---
# <a name="deploy-a-kubernetes-cluster-to-a-custom-virtual-network-on-azure-stack-hub"></a>Bereitstellen eines Kubernetes-Clusters in einem benutzerdefinierten virtuellen Netzwerk in Azure Stack Hub 

Sie können einen Kubernetes-Cluster mithilfe der AKS-Engine (Azure Kubernetes Service) in einem benutzerdefinierten virtuellen Netzwerk bereitstellen. In diesem Artikel erfahren Sie, wie Sie die benötigten Informationen in Ihrem virtuellen Netzwerk finden. Hier werden Schritte zum Berechnen der von Ihrem Cluster verwendeten IP-Adressen, zum Festlegen der Werte im API-Modell sowie zum Festlegen der Routingtabelle und der Netzwerksicherheitsgruppe beschrieben.

Der Kubernetes-Cluster in Azure Stack Hub mit der AKS-Engine verwendet das kubenet-Netzwerk-Plug-In. Informationen zum kubenet-Netzwerk-Plug-In in Azure finden Sie unter [Verwenden von kubenet-Netzwerken mit Ihren eigenen IP-Adressbereichen in Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/configure-kubenet).

## <a name="create-custom-virtual-network"></a>Erstellen des benutzerdefinierten virtuellen Netzwerks

Sie müssen in Ihrer Azure Stack Hub-Instanz über ein benutzerdefiniertes virtuelles Netzwerk verfügen. Weitere Informationen finden Sie unter [Quickstart: Erstellen eines virtuellen Netzwerks im Azure-Portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal).

Erstellen Sie in Ihrem virtuellen Netzwerk ein neues Subnetz. Sie benötigen die Ressourcen-ID und den IP-Adressbereich des Subnetzes. Die Ressourcen-ID und der Bereich werden bei der Clusterbereitstellung in Ihrem API-Modell verwendet.

1. Öffnen Sie in Ihrer Azure Stack Hub-Instanz das Azure Stack Hub-Benutzerportal.
2. Wählen Sie **Alle Ressourcen**.
3. Geben Sie den Namen Ihres virtuellen Netzwerks in das Suchfeld ein.
4. Wählen Sie **Subnetze** >  **+ Subnetze** aus, um ein Subnetz hinzuzufügen.
5. Fügen Sie unter **Name** einen Namen und unter **Adressbereich** einen Adressbereich in CIDR-Notation hinzu. Klicken Sie auf **OK**.
4. Wählen Sie auf der Seite **Virtuelle Netzwerke** die Option **Eigenschaften** aus. Kopieren Sie den unter **Ressourcen-ID** angegebenen Wert, und fügen Sie `/subnets/<nameofyoursubnect>` hinzu. Dieser Wert wird später für den Schlüssel `vnetSubnetId` im API-Modell für Ihren Cluster verwendet. Die Ressourcen-ID für das Subnetz hat folgendes Format:<br>`/subscriptions/SUB_ID/resourceGroups/RG_NAME/providers/Microsoft.Network/virtualNetworks/VNET_NAME/subnets/SUBNET_NAME`

    ![Ressourcen-ID des virtuellen Netzwerks](media/kubernetes-aks-engine-custom-vnet/virtual-network-id.png)

5. Wählen Sie auf der Seite **Virtuelle Netzwerke** die Option **Subnetze** aus. Wählen Sie den Namen des Subnetzes aus (beispielsweise „default“).
    
    ![CIDR-Block des virtuellen Netzwerks](media/kubernetes-aks-engine-custom-vnet/virtual-network-cidr-block.png)
    
6. Notieren Sie sich auf dem Subnetzblatt den Adressbereich und den CIDR-Block des virtuellen Netzwerks (beispielsweise `10.1.0.0 - 10.1.0.255 (256 addresses)` und `10.1.0.0/24`).



## <a name="get-the-ip-address-block"></a>Ermitteln des IP-Adressblocks

Die AKS-Engine unterstützt die Bereitstellung in einem vorhandenen virtuellen Netzwerk. Bei der Bereitstellung in einem vorhandenen Subnetz verwendet der Cluster einen Block mit fortlaufenden IP-Adressen für Agents und einen weiteren für Master.

Sie müssen zwei Werte festlegen. Dazu müssen Sie wissen, wie viele IP-Adressen Sie für Ihren Cluster reservieren müssen, und Ihnen muss die erste der fortlaufenden statischen IP-Adressen im IP-Adressraum des Subnetzes bekannt sein.

Bei Verwendung mehrerer Masterknoten benötigt die AKS-Engine einen Bereich von bis zu 16 ungenutzten IP-Adressen. Der Cluster verwendet jeweils eine IP-Adresse pro Master, und es können bis zu fünf Master verwendet werden. Die AKS-Engine benötigt außerdem die nächsten zehn IP-Adressen nach dem letzten Master für die IP-Adressreservierung. Nach den Mastern und der Reservierung wird noch eine weitere IP-Adresse für den Lastenausgleich benötigt (insgesamt also 16).

Bei der Platzierung Ihres IP-Adressblocks benötigt das Subnetz die folgenden Zuteilungen der vorhandenen IP-Adressen:
 - Die ersten vier IP-Adressen und die letzte IP-Adresse sind reserviert und können nicht in einem Azure-Subnetz verwendet werden.
 - Ein Puffer von 16 IP-Adressen sollte frei bleiben.
 - Der Wert der ersten IP-Adresse Ihres Clusters sollte im hinteren Bereich des Adressraums liegen, um IP-Konflikte zu vermeiden. Weisen Sie der Eigenschaft `firstConsecutiveStaticIP` nach Möglichkeit eine IP-Adresse gegen *Ende* des verfügbaren IP-Adressraums im Subnetz zu.

Das folgende Beispiel zeigt, wie der IP-Adressbereich in einem Subnetz auf der Grundlage dieser verschiedenen Aspekte gefüllt wird. Hier werden drei Master verwendet. Bei Verwendung eines Subnetzes mit 256 Adressen (beispielsweise 10.1.0.0/24) muss die erste der fortlaufenden statischen IP-Adressen auf „207“ festgelegt werden. Die folgende Tabelle enthält die Adressen und die entsprechenden Überlegungen:

| Bereich für Subnetz vom Typ „/24“ | Number | Hinweis |
| --- | --- | --- |
| 10.1.0.0–10.1.03 | 4 | Reserviert im Azure-Subnetz |
| **10.1.0.224**–10.1.0.238 | 14 | Anzahl von IP-Adressen für einen von der AKS-Engine definierten Cluster<br><br> Drei IP-Adressen für drei Master<br>Zehn IP-Adressen als Reserve<br>Eine IP-Adresse für den Lastenausgleich |
| 10.1.0.239–10.1.0.255 | 16 | 16 IP-Adressen als Puffer |
| 10.1.0.256 | 1 | Reserviert im Azure-Subnetz |

In diesem Beispiel müsste die Eigenschaft `firstConsecutiveStaticIP` auf `10.1.0.224` festgelegt werden.

Bei größeren Subnetzen (beispielsweise „/16“ mit mehr als 60.000 Adressen) ist es ggf. nicht ohne Weiteres möglich, Ihre statischen IP-Zuweisungen auf den hinteren Bereich des Netzwerkraums festzulegen. Legen Sie den statischen IP-Adressbereich Ihres Clusters nach den ersten 24 Adressen Ihres IP-Adressbereichs fest, um die Resilienz des Clusters bei der Beanspruchung von Adressen zu gewährleisten.


## <a name="update-the-api-model"></a>Aktualisieren des API-Modells

Aktualisieren Sie das API-Modell, das verwendet wird, um den Cluster über die AKS-Engine in Ihrem benutzerdefinierten virtuellen Netzwerk bereitzustellen.

Legen Sie in **masterProfile** die folgenden Werte fest:

| Feld | Beispiel | BESCHREIBUNG |
| --- | --- | --- |
| vnetSubnetId | `/subscriptions/77e28b6a-582f-42b0-94d2-93b9eca60845/resourceGroups/MDBN-K8S/providers/Microsoft.Network/virtualNetworks/MDBN-K8S/subnets/default` | Geben Sie die Ressourcen-ID des Subnetzes an.  |
| firstConsecutiveStaticIP | 10.1.0.224 | Weisen Sie der Konfigurationseigenschaft `firstConsecutiveStaticIP` eine IP-Adresse gegen *Ende* des verfügbaren IP-Adressraums im gewünschten Subnetz zu. `firstConsecutiveStaticIP` gilt nur für den Masterpool. |

Legen Sie in **agentPoolProfiles** die folgenden Werte fest:

| Feld | Beispiel | BESCHREIBUNG |
| --- | --- | --- |
| vnetSubnetId | `/subscriptions/77e28b6a-582f-42b0-94d2-93b9eca60845/resourceGroups/MDBN-K8S/providers/Microsoft.Network/virtualNetworks/MDBN-K8S/subnets/default` | Geben Sie die Azure Resource Manager-Pfad-ID des Subnetzes an.  |

Beispiel:

```json
"masterProfile": {
  ...
  "vnetSubnetId": "/subscriptions/77e28b6a-582f-42b0-94d2-93b9eca60845/resourceGroups/MDBN-K8S/providers/Microsoft.Network/virtualNetworks/MDBN-K8S/subnets/default",
  "firstConsecutiveStaticIP": "10.1.0.224",
  ...
},
...
"agentPoolProfiles": [
  {
    ...
    "vnetSubnetId": "/subscriptions/77e28b6a-582f-42b0-94d2-93b9eca60845/resourceGroups/MDBN-K8S/providers/Microsoft.Network/virtualNetworks/MDBN-K8S/subnets/default",
    ...
  },

```

## <a name="deploy-your-cluster"></a>Bereitstellen Ihres Clusters

Nachdem Sie die Werte Ihrem API-Modell hinzugefügt haben, können Sie den Cluster mithilfe des Befehls `deploy` und der AKS-Engine über Ihren Clientcomputer bereitstellen. Eine entsprechende Anleitung finden Sie unter [Bereitstellen eines Kubernetes-Clusters](azure-stack-kubernetes-aks-engine-deploy-cluster.md#deploy-a-kubernetes-cluster).

## <a name="set-the-route-table-and-network-security-group"></a>Festlegen der Routingtabelle und der Netzwerksicherheitsgruppe

Kehren Sie nach der Clusterbereitstellung zu Ihrem virtuellen Netzwerk im Azure Stack-Benutzerportal zurück. Legen Sie auf dem Subnetzblatt sowohl die Routingtabelle als auch die Netzwerksicherheitsgruppe (NSG) fest – ohne Verwendung von Azure CNI beispielsweise „`networkPlugin`: `kubenet`“ im API-Modellkonfigurationsobjekt `kubernetesConfig`. Ermitteln Sie nach erfolgreicher Bereitstellung eines Clusters in Ihrem benutzerdefinierten virtuellen Netzwerk die ID der Routingtabellenressource auf dem Blatt **Netzwerk** in der Ressourcengruppe Ihres Clusters.

1. Öffnen Sie in Ihrer Azure Stack Hub-Instanz das Azure Stack Hub-Benutzerportal.
2. Wählen Sie **Alle Ressourcen**.
3. Geben Sie den Namen Ihres virtuellen Netzwerks in das Suchfeld ein.
4. Wählen Sie **Subnetze** und anschließend den Namen des Subnetzes aus, das Ihren Cluster enthält.
    
    ![Routingtabelle und Netzwerksicherheitsgruppe](media/kubernetes-aks-engine-custom-vnet/virtual-network-rt-nsg.png)
    
5. Wählen Sie **Routingtabelle** und anschließend die Routingtabelle für Ihren Cluster aus.
6. Wählen Sie **Netzwerksicherheitsgruppe** und anschließend die NSG für Ihren Cluster aus.

> [!Note]  
> Bei benutzerdefinierten virtuellen Netzwerken für Kubernetes-Windows-Cluster gibt es ein [bekanntes Problem](https://github.com/Azure/aks-engine/issues/371).

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die [AKS-Engine in Azure Stack Hub](azure-stack-kubernetes-aks-engine-overview.md).  
- Lesen Sie [Azure Monitor für Container – Übersicht](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview).