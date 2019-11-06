---
title: Verschieben Ihres Marketplace-Elementclusters in die AKS-Engine in Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihren Marketplace-Elementcluster in Azure Stack in die AKS-Engine verschieben.
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
ms.date: 10/29/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 10/29/2019
ms.openlocfilehash: 41ffa9d9d9f96506d30a6a3c69a557cdba034843
ms.sourcegitcommit: 4d7611d81da6f2f8ef50adab3c09f9122a75bc9d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73145842"
---
# <a name="move-your-marketplace-item-cluster-to-the-aks-engine-on-azure-stack"></a>Verschieben Ihres Marketplace-Elementclusters in die AKS-Engine in Azure Stack

Das Kubernetes-Azure Stack-Marketplace-Element verwendet eine Azure Resource Manager-Vorlage zum Bereitstellen eines virtuellen Bereitstellungscomputers, um die AKS-Engine herunterzuladen und zu installieren und das zum Beschreiben des Clusters verwendete API-Eingabemodell zu generieren. Anschließend wird die AKS-Engine im virtuellen Computer ausgeführt und der Cluster bereitgestellt. In diesem Artikel wird erläutert, wie Sie auf die AKS-Engine und die entsprechenden Dateien zugreifen, um sie dann zum Ausführen von Aktualisierungs- und Skalierungsvorgängen im Kubernetes-Cluster verwenden zu können.

## <a name="access-aks-engine-in-the-dvm"></a>Zugreifen auf die AKS-Engine im virtuellen Bereitstellungscomputer

Nachdem die über das Kubernetes-Azure Stack-Marketplace-Element initiierte Bereitstellung erfolgreich ausgeführt wurde, finden Sie die installierte AKS-Engine, die zum Bereitstellen des Clusters verwendet wurde, in dem virtuellen Bereitstellungscomputer, der in der für den Cluster angegebenen Ressourcengruppe erstellt wurde. Dieser virtuelle Computer gehört nicht zum Kubernetes-Cluster, stattdessen wird er in einem eigenen VNET erstellt. Den virtuellen Computer und die AKS-Engine innerhalb des virtuellen Computers finden Sie mit den folgenden Schritten:

1.  Öffnen Sie das Azure Stack-Benutzerportal, und suchen Sie die Ressourcengruppe, die Sie für den Kubernetes-Cluster angegeben haben.
2.  Suchen Sie in der Ressourcengruppe den virtuellen Bereitstellungscomputer. Der Name beginnt mit dem Präfix **vmd-** .
3.  Wählen Sie den virtuellen Bereitstellungscomputer aus. Suchen Sie unter **Übersicht** nach der öffentlichen IP-Adresse. Verwenden Sie diese Adresse und Ihre Konsolen-App, z. B. PuTTY, um eine SSH-Sitzung mit dem virtuellen Computer einzurichten.
4.  In Ihrer Sitzung auf dem virtuellen Bereitstellungscomputer finden Sie die AKS-Engine unter folgendem Pfad: `./var/lib/waagent/custom-script/download/0/bin/aks-engine`
5.  Suchen Sie die Datei `.json`, in der die als Eingabe in der AKS-Engine verwendeten Cluster beschrieben werden. Die Datei befindet sich unter `/var/lib/waagent/custom-script/download/0/bin/azurestack.json`. Beachten Sie, dass die Datei die Dienstprinzipal-Anmeldeinformationen für die Bereitstellung des Clusters enthält. Wenn Sie die Datei aufbewahren möchten, empfiehlt es sich, sie in einen geschützten Speicher zu verschieben.
6.  Navigieren Sie zu dem von der AKS-Engine generierten Ausgabeverzeichnis unter `/var/lib/waagent/custom-script/download/0/_output/<resource group name>`. Suchen Sie in diesem Verzeichnis die Ausgabe `apimodel.json` unter dem Pfad `/var/lib/waagent/custom-script/download/0/bin/apimodel.json`. Das Verzeichnis und die Datei `apimodel.json` enthalten alle generierten Zertifikate, Schlüssel und Anmeldeinformationen, die für die Bereitstellung des Kubernetes-Clusters benötigt wurden. Speichern Sie diese Ressourcen an einem sicheren Speicherort.
7.  Suchen Sie die Kubernetes-Konfigurationsdatei, die häufig als **kubeconfig**-Datei bezeichnet wird, unter dem entsprechenden Pfad, wobei Sie den Standardbezeichner Ihrer Azure Stack-Instanz verwenden. Diese Datei ist nützlich, wenn Sie **kubectl** für den Zugriff auf Ihren Kubernetes-Cluster einrichten möchten.

## <a name="use-the-aks-engine-with-your-newly-created-cluster"></a>Verwenden der AKS-Engine mit dem neu erstellten Cluster

Nachdem Sie die AKS-Engine, die Eingabedatei „apimodel.json“, das Ausgabeverzeichnis und die Ausgabedatei „apimodel.json“ gefunden haben, speichern Sie sie an einem sicheren Speicherort. Sie können die AKS-Engine-Binärdatei und die Ausgabedatei `apimodel.json` auf jedem virtuellen Linux-Computer verwenden.

1.  Kopieren Sie zur Verwendung der AKS-Engine zum Ausführen von Vorgängen wie **Aktualisieren** und **Skalieren** die Binärdatei **aks-engine** auf den Zielcomputer. Wenn Sie denselben **vmd-** Computer verwenden, kopieren Sie sie in ein Verzeichnis.

2.  Erstellen Sie ein Verzeichnis mit dem Namen des Clusters oder einem anderen gut zu merkenden Namen, der sich auf den neuen Cluster bezieht, und speichern Sie die Ausgabedatei „apimodel.json“ in diesem Verzeichnis. Achten Sie darauf, dass es sich um ein geschütztes Verzeichnis handelt, da diese Datei Anmeldeinformationen enthält. Anschließend können Sie die AKS-Engine ausführen, um Vorgänge wie [Skalieren](azure-stack-kubernetes-aks-engine-scale.md) oder [Aktualisieren](azure-stack-kubernetes-aks-engine-upgrade.md) auszuführen.

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die [AKS-Engine in Azure Stack](azure-stack-kubernetes-aks-engine-overview.md).  
- [Problembehandlung bei der AKS-Engine in Azure Stack](azure-stack-kubernetes-aks-engine-troubleshoot.md)  

