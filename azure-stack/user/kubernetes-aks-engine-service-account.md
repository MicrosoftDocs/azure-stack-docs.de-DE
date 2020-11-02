---
title: Aktivieren der Dienstkontotoken-Volumeprojektion für die AKS-Engine in Azure Stack Hub
description: Erfahren Sie, wie Sie die Dienstkontotoken-Volumeprojektion für die AKS-Engine in Azure Stack Hub aktivieren.
author: mattbriggs
ms.topic: article
ms.date: 10/23/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 10/23/2020
ms.openlocfilehash: 5e712577bb01f7f084c24eadfd3931ebb67f23d9
ms.sourcegitcommit: 716ca50bd198fd51a4eec5b40d5247f6f8c16530
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92906182"
---
# <a name="enabling-service-account-token-volume-projection-for-the-aks-engine-on-azure-stack-hub"></a>Aktivieren der Dienstkontotoken-Volumeprojektion für die AKS-Engine in Azure Stack Hub

Istio ist eine konfigurierbare Open-Source-Dienst-Mesh-Ebene, mit der die Container in einem Kubernetes-Cluster verbunden, überwacht und gesichert werden. Istio 1.3 und höher verwenden eine Funktion in Kubernetes, die als *Dienstkontotoken-Volumeprojektion* bezeichnet wird. Diese Funktion ist in Kubernetes-Clustern, die von der AKS-Engine bereitgestellt werden, nicht standardmäßig aktiviert. In diesem Artikel finden Sie die JSON-Eigenschaften für das API-Modell im `apiServerConfig`-Element, das die Kubernetes-API-Serverflags zeigt, die zum Aktivieren der Dienstkontotoken-Volumeprojektion für Ihren Cluster erforderlich sind.

Weitere Informationen zur Dienstkontotoken-Volumeprojektion finden Sie unter [Dienstkontotoken-Volumeprojektion](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection).

## <a name="enable-service-account-token-volume-projection"></a>Aktivieren der Volumenprojektion für Dienstkontotoken

Um die Dienstkontotoken-Volumeprojektion zu aktivieren, fügen Sie die folgenden Einstellungen in Ihrer JSON-Datei des API-Modells hinzu. 

```json
{
    "kubernetesConfig": {
        "apiServerConfig": {
            "--service-account-api-audiences": "api,istio-ca",
            "--service-account-issuer": "kubernetes.default.svc",
            "--service-account-signing-key-file": "/etc/kubernetes/certs/apiserver.key"
        }
    }
}
```

> [!Note]  
> Möglicherweise müssen Sie `--service-account-api-audiences` und `--service-account-issuer` an Ihren spezifischen Anwendungsfall anpassen.

Ein vollständiges API-Beispielmodell finden Sie unter [istio.json](https://github.com/Azure/aks-engine/blob/master/examples/service-mesh/istio.json).

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die [AKS-Engine in Azure Stack Hub](azure-stack-kubernetes-aks-engine-overview.md).
- [Aktualisieren eines Kubernetes-Clusters in Azure Stack Hub](azure-stack-kubernetes-aks-engine-upgrade.md)
