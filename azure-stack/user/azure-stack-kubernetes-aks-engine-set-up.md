---
title: Einrichten der Voraussetzungen für die AKS-Engine in Azure Stack | Microsoft-Dokumentation
description: Richten Sie die Anforderungen für die Ausführung der ASK-Engine auf Ihrer Azure Stack-Instanz ein.
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
ms.openlocfilehash: aa60a2fec7cb8c06d855b070fb93cdf26a5481ba
ms.sourcegitcommit: 0b783e262ac87ae67929dbd4c366b19bf36740f0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74310181"
---
# <a name="set-up-the-prerequisites-for-the-aks-engine-on-azure-stack"></a>Einrichten der Voraussetzungen für die AKS-Engine in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Sie können die AKS-Engine auf einem virtuellen Computer in Ihrer Umgebung oder auf einem beliebigen Clientcomputer installieren, der Zugriff auf Ihren Azure Stack Resource Manager-Endpunkt hat. Sie benötigen Folgendes, bevor Sie die Engine ausführen: einen AKS-Basis-Ubuntu-Server und eine benutzerdefinierte Linux-Skripterweiterung, die in Ihrem Abonnement verfügbar sind, eine Dienstprinzipalidentität, die einer Mitwirkendenrolle zugewiesen wurde, und ein privates/öffentliches Schlüsselpaar für den SSH-Zugriff auf Ihren Ubuntu-Server. Wenn Sie das Azure Stack Development Kit verwenden, muss Ihr Computer außerdem den entsprechenden Zertifikaten vertrauen.

Wenn Sie die Voraussetzungen erfüllen, können Sie mit der [Definition Ihres Clusters](azure-stack-kubernetes-aks-engine-deploy-cluster.md) beginnen.

Wenn Sie der Cloudbediener für Azure Stack sind und die AKS-Engine anbieten möchten, befolgen Sie die Anweisungen unter [Hinzufügen der AKS-Engine zum Azure Stack-Marketplace](../operator/azure-stack-aks-engine.md).

## <a name="prerequisites-for-the-aks-engine"></a>Voraussetzungen für die AKS-Engine

Zur Verwendung der AKS-Engine benötigen Sie die folgenden Ressourcen. Beachten Sie, dass die AKS-Engine von Mandanten von Azure Stack verwendet werden soll, um Kubernetes-Cluster in Ihrem Mandantenabonnement bereitzustellen. Der einzige Teil, in dem die Beteiligung des Azure Stack-Operators erforderlich sein könnte, ist das Herunterladen von Marketplace-Elementen und das Erstellen einer Dienstprinzipalidentität. Details finden Sie in der folgenden Tabelle.

| Voraussetzung | BESCHREIBUNG | Erforderlich | Anleitung |
| --- | --- | --- | --- |
| Benutzerdefinierte Linux-Skripterweiterung | Benutzerdefinierte Linux-Skripterweiterung 2.0<br>Angebot: Custom Script for Linux 2.0<br>Version: 2.0.6 (oder neueste Version)<br>Herausgeber: Microsoft Corp. | Erforderlich | Wenn Sie in Ihrem Abonnement nicht über dieses Element verfügen, wenden Sie sich an Ihren Cloudbetreiber. |
| AKS-Basis-Ubuntu-Image | AKS-Basisimage<br>Angebot: aks<br> 2019.10.24 (oder neuere Version)<br>Herausgeber: microsoft-aks<br>SKU: aks-ubuntu-1604-201910 | Erforderlich | Wenn Sie in Ihrem Abonnement nicht über dieses Element verfügen, wenden Sie sich an Ihren Cloudbetreiber. Weitere Informationen zur Versionsabhängigkeit finden Sie unter [Abstimmen der Engine auf die Basisimageversion](#matching-engine-to-base-image-version).<br> Wenn Sie der Cloudbediener für Azure Stack sind und die AKS-Engine anbieten möchten, befolgen Sie die Anweisungen unter [Hinzufügen der AKS-Engine zum Azure Stack-Marketplace](../operator/azure-stack-aks-engine.md). |
| Azure Stack-Abonnement | Sie greifen über Abonnements auf Angebote in ihrer Azure Stack-Instanz zu. Das Angebot enthält die Dienste, die Ihnen zur Verfügung stehen. | Erforderlich | Zum Bereitstellen von Mandantenworkloads in Azure Stack müssen Sie zuerst ein [Azure Stack-Abonnement](https://docs.microsoft.com/azure-stack/user/azure-stack-subscribe-services) erhalten. |
| Dienstprinzipalidentität (SPN) |  Eine Anwendung, Ressourcen über Azure Resource Manager bereitstellen oder konfigurieren muss, muss von einem Dienstprinzipal dargestellt werden. | Erforderlich | Möglicherweise müssen Sie sich für dieses Element an den Azure Stack-Operator wenden.  Anweisungen finden Sie unter [Verwenden einer App-Identität für den Ressourcenzugriff](https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals). |
| (SPN) zugewiesene Rolle **Mitwirkender** | Um einer Anwendung den Zugriff auf Ressourcen in Ihrem Abonnement unter Verwendung ihres Dienstprinzipals zu gestatten, müssen Sie den Dienstprinzipal einer Rolle für eine bestimmte Ressource zuweisen. | Erforderlich | Eine Anleitung hierzu finden Sie unter [Zuweisen einer Rolle](https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals#assign-a-role). |
| Resource group | Eine Ressourcengruppe ist ein Container, der verwandte Ressourcen für eine Azure-Lösung enthält. Wenn Sie keine vorhandene Ressourcengruppe angeben, erstellt das Tool eine für Sie. | Optional | [Verwalten von Azure Resource Manager-Gruppen mithilfe des Azure-Portals](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-portal) |
| Privater öffentlicher Schlüssel | Zur Verwendung einer offenen SSH-Verbindung zwischen Ihrem Entwicklungscomputer und der Server-VM auf Ihrer Azure Stack-Instanz, auf der Ihre Web-App gehostet wird, müssen Sie ein öffentlich-privates SSH-Schlüsselpaar (Secure Shell) erstellen. | Erforderlich | Anweisungen zum Generieren eines Schlüssels finden Sie unter [SSH Key Generation](https://docs.microsoft.com/azure-stack/user/azure-stack-dev-start-howto-ssh-public-key) (SSH-Schlüsselgenerierung).|

> [!Note]  
> Sie können die Voraussetzungen für die AKS-Engine auch mit [Azure CLI für Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-azurecli2) oder [PowerShell für Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install) schaffen.

## <a name="matching-engine-to-base-image-version"></a>Abstimmen der Engine auf die Basisimageversion

Die **AKS-Engine** verwendet ein erstelltes Image, das AKS-Basisimage. Alle AKS-Engine-Versionen sind von einer bestimmten Imageversion abhängig, die von Ihrem Azure Stack-Operator in ihrer Azure Stack-Instanz verfügbar gemacht wird. Eine Tabelle mit den AKS-Engine-Versionen und den entsprechenden unterstützten Kubernetes-Versionen finden Sie unter [Supported Kubernetes Versions](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions) (Unterstützte Kubernetes-Versionen). Beispielsweise hängt die AKS-Engine-Version `v0.43.0` von der Version `2019.10.24` des AKS-Basisimage ab. Bitten Sie Ihren Azure Stack-Operator, die spezifische Imageversion vom Azure Marketplace in den Azure Stack-Marketplace herunterzuladen.

Wenn das Image nicht in Ihrem Azure Stack-Marketplace verfügbar ist, lösen Sie einen Fehler aus. Wenn Sie beispielsweise derzeit AKS-Engine Version 0.43.0 verwenden und AKS-Basisimageversion `2019.10.24` nicht verfügbar ist, wird beim Ausführen der AKS-Engine folgende Fehlermeldung angezeigt: 

```Text  
The platform image 'microsoft-aks:aks:aks-ubuntu-1604-201908:2019.08.09' is not available. 
Verify that all fields in the storage profile are correct.
```

Sie können die aktuelle Version der AKS-Engine überprüfen, indem Sie den folgenden Befehl ausführen:

```bash  
$ aks-engine version
Version: v0.39.1
GitCommit: 6fff62731
GitTreeState: clean
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Bereitstellen der AKS-Engine unter Windows in Azure Stack](azure-stack-kubernetes-aks-engine-deploy-windows.md)  
> [Bereitstellen der AKS-Engine unter Linux in Azure Stack](azure-stack-kubernetes-aks-engine-deploy-linux.md)