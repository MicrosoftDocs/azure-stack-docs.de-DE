---
title: Aktualisieren eines Kubernetes-Clusters in Azure Stack | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie einen Kubernetes-Cluster in Azure Stack aktualisieren.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na (Kubernetes)
ms.devlang: nav
ms.topic: article
ms.date: 10/16/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 10/16/2019
ms.openlocfilehash: 39eebfbc4d60d4cd68bb33d6efcf35cc12ffe313
ms.sourcegitcommit: 5ef433aa6b75cdfb557fab0ef9308ff2118e66e5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73594888"
---
# <a name="upgrade-a-kubernetes-cluster-on-azure-stack"></a>Aktualisieren eines Kubernetes-Clusters in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

## <a name="upgrade-a-cluster"></a>Aktualisieren eines Clusters

Mit der AKS-Engine können Sie den Cluster aktualisieren, der ursprünglich mit dem Tool bereitgestellt wurde. Sie können die Cluster mithilfe der AKS-Engine verwalten. Die Verwaltungsaufgaben ähneln den IaaS-Systemen. Beachten Sie die Verfügbarkeit neuer Updates, und wenden Sie sie mit der AKS-Engine an.

Mit dem Upgradebefehl werden die Kubernetes-Version und das Betriebssystem-Basisimage aktualisiert. Bei jeder Ausführung des Upgradebefehls erstellt die AKS-Engine für jeden Knoten des Clusters einen neuen virtuellen Computer unter Verwendung des AKS-Basisimages, das der Version der verwendeten **aks-engine** zugeordnet ist. Mit dem Befehl `aks-engine upgrade` können Sie die Währung aller Master- und Agent-Knoten im Cluster beibehalten. 

Microsoft verwaltet Ihren Cluster nicht. Microsoft stellt jedoch das Tool und das VM-Image bereit, mit denen Sie Ihren Cluster verwalten können. 

Für ein bereitgestelltes Cluster decken Upgrades Folgendes ab:

-   kubernetes
-   Azure Stack-Kubernetes-Anbieter
-   Basisbetriebssystem

Beachten Sie bei der Aktualisierung eines Produktionsclusters Folgendes:

-   Verwenden Sie die richtige Clusterspezifikation (`apimodel.json`) und Ressourcengruppe für den Zielcluster?
-   Verwenden Sie einen zuverlässigen Computer als Clientcomputer, auf dem die AKS-Engine ausgeführt wird und von dem Sie Upgradevorgänge ausführen?
-   Stellen Sie sicher, dass ein funktionsfähiger Sicherungscluster vorhanden ist.
-   Führen Sie den Befehl wenn möglich von einem virtuellen Computer in der Azure Stack-Umgebung aus, um die Anzahl der Netzwerkhops und potenzielle Verbindungsfehler zu verringern.
-   Stellen Sie sicher, dass das Kontingent Ihres Abonnements ausreicht, um den gesamten Prozess auszuführen. Während des Prozesses werden neue virtuelle Computer zugeordnet.
-   Systemupdates oder geplante Tasks sind nicht vorgesehen.
-   Richten Sie ein gestaffeltes Upgrade auf einem Cluster ein, der genau wie der Produktionscluster konfiguriert ist, und testen Sie das Upgrade dort, bevor Sie es im Produktionscluster ausführen.

## <a name="steps-to-upgrade-to-a-newer-kubernetes-version"></a>Schritte zum Durchführen eines Upgrades auf eine neuere Kubernetes-Version

> [!Note]  
> Das AKS-Basisimage wird auch aktualisiert, wenn Sie eine neuere Version der AKS-Engine verwenden und das Image im Marketplace verfügbar ist.

In den folgenden Anweisungen werden die minimalen Schritte zum Ausführen des Upgrades verwendet. Weitere Details finden Sie im Artikel [Upgrading Kubernetes Clusters](https://github.com/Azure/aks-engine/blob/master/docs/topics/upgrade.md) (Aktualisieren von Kubernetes-Clustern).

1. Sie müssen zunächst die Versionen bestimmen, die Sie für das Upgrade als Ziel festlegen können. Diese Version hängt von der Version ab, die Sie zurzeit verwenden, und verwenden Sie dann diesen Versionswert, um das Upgrade durchzuführen.

    Führen Sie die folgenden Befehle aus:

    ```bash  
    $ aks-engine get-versions
    Version Upgrades
    1.15.0
    1.14.3  1.15.0
    1.14.1  1.14.3, 1.15.0
    1.13.7  1.14.1, 1.14.3
    1.13.5  1.13.7, 1.14.1, 1.14.3
    1.12.8  1.13.5, 1.13.7
    1.12.7  1.12.8, 1.13.5, 1.13.7
    1.11.10 1.12.7, 1.12.8
    1.11.9  1.11.10, 1.12.7, 1.12.8
    1.10.13 1.11.9, 1.11.10
    1.10.12 1.10.13, 1.11.9, 1.11.10
    1.9.11  1.10.12, 1.10.13
    1.9.10  1.9.11, 1.10.12, 1.10.13
    1.6.9   1.9.10, 1.9.11
    ```

    Wenn beispielsweise gemäß der Ausgabe des `get-versions`-Befehls Ihre aktuelle Kubernetes-Version „1.13.5“ ist, können Sie ein Upgrade auf „1.13.7, 1.14.1, 1.14.3“ durchführen.

2. Sammeln Sie die Informationen, die Sie zum Ausführen des `upgrade`-Befehls benötigen. Für das Upgrade werden die folgenden Parameter verwendet:

    | Parameter | Beispiel | BESCHREIBUNG |
    | --- | --- | --- |
    | azure-env | AzureStackCloud | Um der AKS-Engine mitzuteilen, dass die Zielplattform Azure Stack ist, verwenden Sie `AzureStackCloud`. |
    | location | local | Der Regionsname für Ihre Azure Stack-Instanz. Für das ASDK ist `local` für die Region festgelegt. |
    | resource-group | kube-rg | Geben Sie den Namen einer neuen Ressourcengruppe ein, oder wählen Sie eine vorhandene Ressourcengruppe aus. Der Ressourcenname muss alphanumerisch und in Kleinbuchstaben angegeben sein. |
    | subscription-id | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Geben Sie Ihre Abonnement-ID ein. Weitere Informationen finden Sie unter [Abonnieren von Angeboten](https://docs.microsoft.com/azure-stack/user/azure-stack-subscribe-services#subscribe-to-an-offer). |
    | api-model | ./kubernetes-azurestack.json | Pfad zur Clusterkonfigurationsdatei oder zum API-Modell. |
    | client-id | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Geben Sie die Dienstprinzipal-GUID ein. Die als Anwendungs-ID identifizierte Client-ID, wenn Ihr Azure Stack-Administrator den Dienstprinzipal erstellt hat. |
    | client-secret | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Geben Sie das Dienstprinzipalgeheimnis ein. Dies ist das Clientgeheimnis, das Sie beim Erstellen Ihres Diensts einrichten. |
    | identity-system | adfs | Optional. Geben Sie Ihre Identitätsverwaltungslösung an, wenn Sie Active Directory-Verbunddienste (AD FS) nutzen. |

3. Führen Sie mit Ihren Werten den folgenden Befehl aus:

    ```bash  
    aks-engine upgrade \
    --azure-env AzureStackCloud   
    --location <for an ASDK is local> \
    --resource-group kube-rg \
    --subscription-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --api-model kube-rg/apimodel.json \
    --upgrade-version 1.13.5 \
    --client-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --client-secret xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --identity-system adfs # required if using AD FS
    ```

4.  Wenn aus einem beliebigen Grund beim Upgradevorgang ein Fehler auftritt, können Sie den Upgradebefehl erneut ausführen, nachdem Sie das Problem behoben haben. Die AKS-Engine setzt den Vorgang dort fort, wo zuvor der Fehler aufgetreten ist.

## <a name="steps-to-only-upgrade-the-os-image"></a>Schritte zum alleinigen Upgrade des Betriebssystemimages

1. Sehen Sie sich die [Tabelle der unterstützten Kubernetes-Versionen](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions) an, und überprüfen Sie, ob Sie über die Version von aks-engine und des AKS-Basisimages verfügen, für die Sie das Upgrade durchführen möchten. Führen Sie `aks-engine version` aus, um die Version von aks-engine anzuzeigen.
2. Aktualisieren Sie die AKS-Engine entsprechend. Führen Sie auf dem Computer, auf dem Sie aks-engine installiert haben, Folgendes aus: `./get-akse.sh --version vx.xx.x`. Ersetzen Sie dabei **x.xx.x** durch die Zielversion.
3. Bitten Sie Ihren Azure Stack-Operator, die erforderliche Version des AKS-Basisimages im Azure Stack-Marketplace hinzuzufügen, den Sie verwenden möchten.
4. Führen Sie den Befehl `aks-engine upgrade` mit derselben Version von Kubernetes aus, die Sie bereits verwenden, fügen Sie jedoch `--force` hinzu. Ein Beispiel finden Sie unter [Erzwingen eines Upgrades](#forcing-an-upgrade).


## <a name="forcing-an-upgrade"></a>Erzwingen eines Upgrades

Unter bestimmten Bedingungen möchten Sie möglicherweise ein Upgrade Ihres Clusters erzwingen. Beispielsweise stellen Sie am ersten Tag einen Cluster in einer nicht verbundenen Umgebung mithilfe der neuesten Kubernetes-Version bereit. Am folgenden Tag gibt Ubuntu einen Patch zu einem Sicherheitsrisiko heraus, für den Microsoft ein neues **AKS-Basisimage** generiert. Sie können das neue Image anwenden, indem Sie ein Upgrade mit der gleichen Kubernetes-Version erzwingen, die Sie bereits bereitgestellt haben.

```bash  
aks-engine upgrade \
--azure-env AzureStackCloud   
--location <for an ASDK is local> \
--resource-group kube-rg \
--subscription-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
--api-model kube-rg/apimodel.json \
--upgrade-version 1.13.5 \
--client-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
--client-secret xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
--force
```

Anweisungen dazu finden Sie unter [Force upgrade](https://github.com/Azure/aks-engine/blob/master/docs/topics/upgrade.md#force-upgrade) (Upgrade erzwingen).

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die [AKS-Engine in Azure Stack](azure-stack-kubernetes-aks-engine-overview.md).
- [Skalieren eines Kubernetes-Cluster in Azure Stack](azure-stack-kubernetes-aks-engine-scale.md)