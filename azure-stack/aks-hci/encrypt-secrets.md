---
title: Verschlüsseln von etcd-Geheimnissen in AKS auf Azure Stack HCI
description: Erfahren Sie, wie etcd-Geheimnisse in AKS auf Azure Stack HCI verschlüsselt werden.
author: aabhathipsay
ms.topic: how-to
ms.date: 02/02/2021
ms.author: aabha
ms.reviewer: ''
ms.openlocfilehash: 82d40c2f97171196b90171e3dda850990bf2fbab
ms.sourcegitcommit: 2c6418ee465e67edd417961b1f5211b2e09dbd5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102116715"
---
# <a name="encrypt-etcd-secrets-on-aks-on-azure-stack-hci-clusters"></a>Verschlüsseln von etcd-Geheimnissen in AKS auf Azure Stack HCI-Clustern

Ein Geheimnis in Kubernetes ist ein Objekt, das eine kleine Menge an sensiblen Daten enthält, z. B. Kennwörter und SSH-Schlüssel. Auf dem Kubernetes-API-Server werden Geheimnisse in  _etcd_ gespeichert. Dabei handelt es sich um einen hochverfügbaren Schlüssel-Wert-Speicher, der als Kubernetes-Sicherungsspeicher für alle Clusterdaten verwendet wird. Wenn Sie die Verschlüsselung von etcd-Geheimnissen aktivieren, können Sie [ruhende Geheimnisdaten verschlüsseln](https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/) und außerdem die Verwaltung und Rotation von Verschlüsselungsschlüsseln automatisieren. 

> [!NOTE]
> In dieser Vorschauversion ist die Verschlüsselung von etcd-Geheimnissen nur für neue Workloadcluster verfügbar. 

## <a name="enable-encryption-of-etcd-secrets"></a>Aktivieren der Verschlüsselung von etcd-Geheimnissen

Verwenden Sie den `-enableSecretsEncryption`-Parameter des Befehls [New-AksHciCluster](./new-akshcicluster.md), um die Verschlüsselung von etcd-Geheimnissen zu aktivieren und die Rotation des Verschlüsselungsschlüssels zu automatisieren, wie unten gezeigt: 

```powershell
New-AksHciCluster -name mynewcluster -enableSecretsEncryption
```

Nachdem Sie einen neuen Cluster mit dem `-enableSecretsEncryption`-Parameter bereitgestellt haben, können Sie diese Funktion nicht mehr deaktivieren.

## <a name="monitor-and-troubleshoot"></a>Überwachung und Problembehandlung

Überprüfen Sie die verfügbare [Dokumentation und die Skripts](https://github.com/microsoft/AKS-HCI-Apps), um die Anwendungsbereitstellung in Kubernetes-Clustern zu vereinfachen.

- Befolgen Sie zum Einrichten der Protokollierung mithilfe von Elasticsearch, Fluent Bit und Kibana die Schritte zum [Installieren der Tools und Einrichten von Protokollierung](https://github.com/microsoft/AKS-HCI-Apps/tree/main/Logging).
- Um das Überwachungstool Prometheus zu verwenden, führen Sie die Schritte zum [Installieren von Prometheus in einem Kubernetes-Cluster aus](https://github.com/microsoft/AKS-HCI-Apps/tree/main/Monitoring#certs-and-keys-monitoring).

> [!NOTE]
> Sie finden die Protokolle auf dem Knoten der Steuerungsebene unter `/var/log/pods`.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Verschlüsseln ruhender Geheimnisdaten](https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data)
- [Verwenden eines KMS-Anbieters für Datenverschlüsselung](https://kubernetes.io/docs/tasks/administer-cluster/kms-provider/)

## <a name="next-steps"></a>Nächste Schritte

In diesem Leitfaden haben Sie erfahren, wie Sie die Verschlüsselung von etcd-Geheimnissen für neue Workloadcluster aktivieren. Als Nächstes haben Sie folgende Möglichkeiten:
- [Bereitstellen von Linux-Anwendungen in einem Kubernetes-Cluster](./deploy-linux-application.md).
- [Bereitstellen einer Windows Server-Anwendung in einem Kubernetes-Cluster](./deploy-windows-application.md).
