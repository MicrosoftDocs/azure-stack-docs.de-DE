---
title: Einrichten der Voraussetzungen für die AKS-Engine in Azure Stack Hub
description: Richten Sie die Anforderungen für die Ausführung der ASK-Engine in Ihrer Azure Stack Hub-Instanz ein.
author: mattbriggs
ms.topic: article
ms.date: 09/08/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/08/2020
ms.openlocfilehash: 9dbe60bfbc9b15f75b31e423bf08c988d9e168be
ms.sourcegitcommit: 2407498dc34158a49959d9f87f84d6a1cde0cca6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89560945"
---
# <a name="set-up-the-prerequisites-for-the-aks-engine-on-azure-stack-hub"></a>Einrichten der Voraussetzungen für die AKS-Engine in Azure Stack Hub

Sie können die AKS-Engine auf einem virtuellen Computer in Ihrer Umgebung oder auf einem beliebigen Clientcomputer installieren, der Zugriff auf Ihren Azure Stack Hub Resource Manager-Endpunkt hat. Sie benötigen Folgendes, bevor Sie die Engine ausführen: einen AKS-Basis-Ubuntu-Server und eine benutzerdefinierte Linux-Skripterweiterung, die in Ihrem Abonnement verfügbar sind, eine Dienstprinzipalidentität, die einer Mitwirkendenrolle zugewiesen wurde, und ein privates/öffentliches Schlüsselpaar für den SSH-Zugriff auf Ihren Ubuntu-Server. Wenn Sie das Azure Stack Development Kit verwenden, muss Ihr Computer außerdem den entsprechenden Zertifikaten vertrauen.

Wenn Sie die Voraussetzungen erfüllen, können Sie mit der [Definition Ihres Clusters](azure-stack-kubernetes-aks-engine-deploy-cluster.md) beginnen.

Wenn Sie der Cloudbetreiber für Azure Stack Hub sind und die AKS-Engine anbieten möchten, befolgen Sie die Anweisungen unter [Hinzufügen der AKS-Engine zum Azure Stack Hub-Marketplace](../operator/azure-stack-aks-engine.md).

## <a name="prerequisites-for-the-aks-engine"></a>Voraussetzungen für die AKS-Engine

Zur Verwendung der AKS-Engine benötigen Sie die folgenden Ressourcen. Beachten Sie, dass die AKS-Engine von Mandanten von Azure Stack Hub verwendet werden soll, um Kubernetes-Cluster in Ihrem Mandantenabonnement bereitzustellen. Der einzige Teil, in dem die Beteiligung des Azure Stack Hub-Operators erforderlich sein könnte, ist das Herunterladen von Marketplace-Elementen und das Erstellen einer Dienstprinzipalidentität. Details finden Sie in der folgenden Tabelle.

Der Cloudbetreiber muss über die folgenden Elemente verfügen.

| Voraussetzung | BESCHREIBUNG | Erforderlich | Instructions |
| --- | --- | --- | --- | --- |
| Azure Stack Hub 2002 oder höher | Die AKS-Engine erfordert Azure Stack Hub 2002 oder höher. | Erforderlich | Wenn Sie nicht sicher sind, welche Version von Azure Stack Hub verwendet wird, wenden Sie sich an Ihren Cloudbetreiber. |
| Benutzerdefinierte Linux-Skripterweiterung | Benutzerdefinierte Linux-Skripterweiterung 2.0<br>Angebot: Custom Script for Linux 2.0<br>Version: 2.0.6 (oder neueste Version)<br>Herausgeber: Microsoft Corp. | Erforderlich | Wenn Sie in Ihrem Abonnement nicht über dieses Element verfügen, wenden Sie sich an Ihren Cloudbetreiber. |
| AKS-Basis-Ubuntu-Image | AKS Base Ubuntu 16.04: LTS-Image, August 2020 (2020.08.24) | Erforderlich | Wenn Sie in Ihrem Abonnement nicht über dieses Element verfügen, wenden Sie sich an Ihren Cloudbetreiber. Weitere Informationen zur Versionsabhängigkeit finden Sie unter [Abstimmen der Engine auf die Basisimageversion](#matching-engine-to-base-image-version).<br> Wenn Sie der Cloudbetreiber für Azure Stack Hub sind und die AKS-Engine anbieten möchten, befolgen Sie die Anweisungen unter [Hinzufügen der AKS-Engine zum Azure Stack Hub-Marketplace](../operator/azure-stack-aks-engine.md). |
| Dienstprinzipalidentität (SPN) |  Eine Anwendung, Ressourcen über Azure Resource Manager bereitstellen oder konfigurieren muss, muss von einem Dienstprinzipal dargestellt werden. | Erforderlich | Möglicherweise müssen Sie sich für dieses Element an den Azure Stack Hub-Operator wenden.<br>Wenn eine Azure Active Directory-Dienstprinzipalidentität (Azure AD) verwendet wird, ist für die virtuellen Computer im Kubernetes-Cluster Internetzugriff erforderlich, damit der Dienstprinzipal über Azure AD authentifiziert werden kann. Besteht kein Internetzugriff, ist der Kubernetes-Cluster nicht funktionsfähig.<br>Anweisungen finden Sie unter [Verwenden einer App-Identität für den Ressourcenzugriff](../operator/azure-stack-create-service-principals.md). |
| (SPN) zugewiesene Rolle **Mitwirkender** | Um einer Anwendung den Zugriff auf Ressourcen in Ihrem Abonnement unter Verwendung ihres Dienstprinzipals zu gestatten, müssen Sie den Dienstprinzipal einer Rolle für eine bestimmte Ressource zuweisen. | Erforderlich | Eine Anleitung hierzu finden Sie unter [Zuweisen einer Rolle](../operator/azure-stack-create-service-principals.md#assign-a-role). |


Sie können die folgenden Elemente festlegen.

| Voraussetzung | BESCHREIBUNG | Erforderlich | Instructions |
| --- | --- | --- | --- |
| Azure Stack Hub-Abonnement | Sie greifen über Abonnements auf Angebote in Ihrer Azure Stack Hub-Instanz zu. Das Angebot enthält die Dienste, die Ihnen zur Verfügung stehen. | Erforderlich | Zum Bereitstellen von Mandantenworkloads in Azure Stack Hub müssen Sie zuerst ein [Azure Stack Hub-Abonnement](./azure-stack-subscribe-services.md) erhalten. |
| Resource group | Eine Ressourcengruppe ist ein Container, der verwandte Ressourcen für eine Azure-Lösung enthält. Wenn Sie keine vorhandene Ressourcengruppe angeben, erstellt das Tool eine für Sie. | Optional | [Verwalten von Azure Resource Manager-Gruppen mithilfe des Azure-Portals](/azure/azure-resource-manager/manage-resource-groups-portal) |
| Privater öffentlicher Schlüssel | Zur Verwendung einer offenen SSH-Verbindung zwischen Ihrem Entwicklungscomputer und der Server-VM auf Ihrer Azure Stack Hub-Instanz, auf der Ihre Web-App gehostet wird, müssen Sie ein öffentlich-privates SSH-Schlüsselpaar (Secure Shell) erstellen. | Erforderlich | Anweisungen zum Generieren eines Schlüssels finden Sie unter [SSH Key Generation](./azure-stack-dev-start-howto-ssh-public-key.md) (SSH-Schlüsselgenerierung).|


> [!NOTE]  
> Sie können die Voraussetzungen für die AKS-Engine auch mit [Azure CLI für Azure Stack Hub](./azure-stack-version-profiles-azurecli2.md) oder [PowerShell für Azure Stack Hub](../operator/azure-stack-powershell-install.md) schaffen.

## <a name="matching-engine-to-base-image-version"></a>Abstimmen der Engine auf die Basisimageversion

Die AKS-Engine stellt ein angepasstes Ubuntu Server-Betriebssystem für jeden Clusterknoten bereit; **AKS Base Ubuntu 16.04-LTS Image Distro**. Alle AKS-Engine-Versionen sind von einer bestimmten Imageversion abhängig, die von Ihrem Azure Stack Hub-Operator in Ihrer Azure Stack Hub-Instanz verfügbar gemacht wird. Eine Tabelle mit den AKS-Engine-Versionen und den entsprechenden unterstützten Kubernetes-Versionen finden Sie unter [Supported Kubernetes Versions](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-aks-engine-versions) (Unterstützte Kubernetes-Versionen). Beispielsweise hängt die AKS-Engine-Version `v0.55.0` von der Version `2020.08.24` des AKS Base Ubuntu 16.04-LTS Image Distro ab. Bitten Sie Ihren Azure Stack Hub-Operator, die spezifische Imageversion vom Azure Marketplace in den Azure Stack Hub-Marketplace herunterzuladen.

Wenn das Image nicht in Ihrem Azure Stack Hub-Marketplace verfügbar ist, lösen Sie einen Fehler aus. Wenn Sie beispielsweise derzeit Version 0.55.0 der AKS-Engine verwenden und die Version `2020.08.24` von AKS Base Ubuntu 16.04-LTS Image Distro nicht verfügbar ist, wird beim Ausführen der AKS-Engine folgende Fehlermeldung angezeigt: 

```Text  
The platform image 'microsoft-aks:aks:aks-ubuntu-1604-202003:2020.08.24' is not available. 
Verify that all fields in the storage profile are correct.
```

Sie können die aktuelle Version der AKS-Engine überprüfen, indem Sie den folgenden Befehl ausführen:

```bash  
$ aks-engine version
Version: v0.55.0
GitCommit: 44a35c00c
GitTreeState: clean
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Bereitstellen der AKS-Engine unter Windows in Azure Stack Hub](azure-stack-kubernetes-aks-engine-deploy-windows.md)  
> [Bereitstellen der AKS-Engine unter Linux in Azure Stack Hub](azure-stack-kubernetes-aks-engine-deploy-linux.md)
