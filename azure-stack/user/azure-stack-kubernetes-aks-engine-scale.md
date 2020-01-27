---
title: Skalieren eines Kubernetes-Clusters in Azure Stack Hub | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie einen Kubernetes-Cluster in Azure Stack Hub skalieren.
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
ms.date: 11/21/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: 0fd294e0c7379bed75a0eb753678810d06888106
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75878933"
---
# <a name="scale-a-kubernetes-cluster-on-azure-stack-hub"></a>Skalieren eines Kubernetes-Cluster in Azure Stack Hub

Mit dem **scale**-Befehl können Sie den Cluster mit der AKS-Engine skalieren. Der **scale**-Befehl verwendet Ihre Clusterkonfigurationsdatei (`apimodel.json`) im Ausgabeverzeichnis als Eingabe für eine neue Azure Resource Manager-Bereitstellung neu. Die Engine führt den Skalierungsvorgang für den angegebenen Agentpool aus. Wenn der Skalierungsvorgang abgeschlossen ist, aktualisiert die Engine die Clusterdefinition in der gleichen `apimodel.json`-Datei, um die aktualisierte, aktuelle Clusterkonfiguration mit der neuen Knotenanzahl widerzuspiegeln.

## <a name="scale-a-cluster"></a>Skalieren eines Clusters

Der `aks-engine scale`-Befehl kann in einem `aks-engine`-Kubernetes-Cluster die Anzahl der Knoten in einem vorhandenen Agentpool herauf- oder herabsetzen. Knoten werden immer am Ende des Agentpools hinzugefügt oder entfernt. Knoten werden vor dem Löschen gesperrt und ausgeglichen.

### <a name="values-for-the-scale-command"></a>Werte für den scale-Befehl

Die folgenden Parameter werden vom scale-Befehl verwendet, um Ihre Clusterdefinitionsdatei zu suchen und Ihren Cluster zu aktualisieren.

| Parameter | Beispiel | Beschreibung |
| --- | --- | --- | 
| azure-env | AzureStackCloud | Wenn Sie Azure Stack Hub verwenden, müssen die Umgebungsnamen auf `AzureStackCloud` festgelegt werden. | 
| location | local | Dies ist die Region für Ihre Azure Stack Hub-Instanz. Für ein ASDK ist `local` für die Region festgelegt.  | 
| resource-group | kube-rg | Der Name der Ressourcengruppe, die Ihren Cluster enthält. | 
| subscription-id |  | Die GUID des Abonnements, das die Ressourcen enthält, die von Ihrem Cluster verwendet werden. Stellen Sie sicher, dass Ihr Abonnement über ein ausreichendes Kontingent zum Skalieren verfügt. | 
| client-id |  | Die Client-ID des Dienstprinzipals, der beim Erstellen des Clusters mit der AKS-Engine verwendet wird. | 
| client-secret |  | Das Dienstprinzipalgeheimnis, das beim Erstellen des Clusters verwendet wird. | 
| api-model | kube-rg/apimodel.json | Der Pfad zu ihrer Clusterdefinitionsdatei (apimodel.json). Er könnte lauten:  _output/\<dnsPrefix>/apimodel.json | 
| -new-node-count | 9 | Gewünschte Knotenanzahl. | 
| -master-FQDN |  | Master-FQDN. Beim horizontalen Herunterskalieren erforderlich. |
| identity-system | adfs | Optional. Geben Sie Ihre Identitätsverwaltungslösung an, wenn Sie Active Directory-Verbunddienste (AD FS) nutzen. |

Beim Skalieren eines Clusters in Azure Stack Hub müssen Sie den Parameter **--azure-env** angeben. Weitere Informationen zu Parametern und deren Werten, die im **scale**-Befehl für die AKS-Engine verwendet werden, finden Sie unter [Scale – parameters](https://github.com/Azure/aks-engine/blob/master/docs/topics/scale.md#parameters) (Scale – Parameter).

### <a name="command-to-scale-your-cluster"></a>Befehl zum Skalieren des Clusters

Führen Sie zum Skalieren des Clusters folgenden Befehl aus:

```bash
aks-engine scale \
    --azure-env AzureStackCloud   \
    --location <for an ASDK is local> \
    --resource-group <cluster resource group>
    --subscription-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --client-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --client-secret xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --api-model <path to your apomodel.json file>
    --new-node-count <desired node count> \
    --master-FQDN <master FQDN> \
    --identity-system adfs # required if using AD FS
```

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die [AKS-Engine in Azure Stack Hub](azure-stack-kubernetes-aks-engine-overview.md).
- [Aktualisieren eines Kubernetes-Clusters in Azure Stack Hub](azure-stack-kubernetes-aks-engine-upgrade.md)