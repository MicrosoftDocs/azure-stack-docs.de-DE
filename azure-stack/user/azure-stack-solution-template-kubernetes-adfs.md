---
title: Bereitstellen von Kubernetes in Azure Stack Hub mithilfe der Active Directory-Verbunddienste (Active Directory Federated Services, AD FS)
description: Erfahren Sie, wie Sie Kubernetes in Azure Stack Hub mithilfe der Active Directory-Verbunddienste (AD FS) bereitstellen.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 06/18/20192
ms.openlocfilehash: 5a1b36f7640f3259a4b18f087ebf4bad49f3c4ed
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77703671"
---
# <a name="deploy-kubernetes-to-azure-stack-hub-using-active-directory-federated-services"></a>Bereitstellen von Kubernetes in Azure Stack Hub mithilfe der Active Directory-Verbunddienste

> [!Note]  
> Verwenden Sie das Kubernetes-Azure Stack-Marketplace-Element nur, um Cluster als Proof of Concept bereitzustellen. Verwenden Sie für unterstützte Kubernetes-Cluster in Azure Stack  [die AKS-Engine](azure-stack-kubernetes-aks-engine-overview.md).

Sie können die Schritte in diesem Artikel zum Bereitstellen und Einrichten der Ressourcen für Kubernetes befolgen. Verwenden Sie diese Schritte, wenn Sie Active Directory-Verbunddienste (AD FS) als Ihren Identitätsverwaltungsdienst nutzen.

## <a name="prerequisites"></a>Voraussetzungen 

Stellen Sie zum Einstieg sicher, dass Sie über die erforderlichen Berechtigungen verfügen und dass Ihre Azure Stack Hub-Instanz vorbereitet ist.

1. Generieren Sie ein öffentlich-privates SSH-Schlüsselpaar für die Anmeldung bei der Linux-VM in Azure Stack Hub. Sie benötigen den öffentlichen Schlüssel bei der Clustererstellung.

    Anweisungen zum Generieren eines Schlüssels finden Sie unter [SSH Key Generation](azure-stack-dev-start-howto-ssh-public-key.md) (SSH-Schlüsselgenerierung).

1. Stellen Sie sicher, dass Sie ein gültiges Abonnement im Azure Stack Hub-Mandantenportal besitzen und ausreichend öffentliche IP-Adressen zum Hinzufügen neuer Anwendungen verfügbar sind.

    Der Cluster kann nicht in einem Azure Stack Hub-Abonnement vom Typ **Administrator** bereitgestellt werden. Sie müssen ein Abonnement vom Typ **Benutzer** verwenden. 

1. Falls der Kubernetes-Cluster in Ihrem Marketplace nicht verfügbar ist, wenden Sie sich an Ihren Azure Stack Hub-Administrator.

## <a name="create-a-service-principal"></a>Erstellen eines Dienstprinzipals

Falls Sie als Identitätslösung AD FS verwenden, müssen Sie mit Ihrem Azure Stack Hub-Administrator zusammenarbeiten, um den Dienstprinzipal einzurichten. Der Dienstprinzipal verschafft Ihrer Anwendung Zugriff auf Azure Stack Hub-Ressourcen.

1. Ihr Azure Stack Hub-Administrator stellt Ihnen die Informationen für den Dienstprinzipal bereit. Die Dienstprinzipalinformationen sollten wie folgt aussehen:

     ```Text  
       ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
       ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
       ClientSecret          : <your client secret>
       Thumbprint            : <often this value is empty>
       ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
       PSComputerName        : 192.168.200.224
       RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
     ```

2. Weisen Sie dem neuen Dienstprinzipal eine Rolle als Mitwirkender für Ihr Abonnement zu. Eine Anleitung hierzu finden Sie unter [Zuweisen einer Rolle](../operator/azure-stack-add-users-adfs.md).

## <a name="deploy-kubernetes"></a>Bereitstellen von Kubernetes

1. Öffnen Sie das [Azure Stack Hub-Portal](https://portal.local.azurestack.external).

1. Wählen Sie **+ Ressource erstellen** > **Compute** > **Kubernetes-Cluster** aus. Klicken Sie auf **Erstellen**.

    ![Bereitstellen einer Lösungsvorlage](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1. Grundlagen

1. Wählen Sie unter „Kubernetes-Cluster erstellen“ die Option **Grundlagen**.

    ![Bereitstellen einer Lösungsvorlage](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. Wählen Sie die ID für Ihr **Abonnement** aus.

1. Geben Sie den Namen einer neuen Ressourcengruppe ein, oder wählen Sie eine vorhandene Ressourcengruppe aus. Der Ressourcenname muss alphanumerisch und in Kleinbuchstaben angegeben sein.

1. Wählen Sie den **Standort** der Ressourcengruppe aus. Dies ist die Region, die Sie für die Azure Stack Hub-Installation auswählen.

### <a name="2-kubernetes-cluster-settings"></a>2. Einstellungen für Kubernetes-Cluster

1. Wählen Sie unter „Kubernetes Cluster erstellen“ die Option **Kubernetes Cluster Settings** (Einstellungen für Kubernetes-Cluster).

    ![Bereitstellen einer Lösungsvorlage](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-adfs.png)

1. Geben Sie unter **Linux VM Admin Username** (Benutzername des Linux-VM-Administrators) einen Namen ein. Hierbei handelt es sich um den Benutzernamen für die virtuellen Linux-Computer, die Teil des Kubernetes-Clusters und des DVM (Deployment Virtual Machine, virtueller Computer für die Bereitstellung) sind.

1. Geben Sie den **öffentlichen SSH-Schlüssel** für die Autorisierung bei allen Linux-Computern ein, die als Teil des Kubernetes-Clusters und des DVM erstellt werden.

1. Geben Sie das für die Region eindeutige **DNS-Präfix des Masterprofils** ein. Diese Angabe muss ein für die Region eindeutiger Name sein, beispielsweise `k8s-12345`. Es wird empfohlen, den gleichen Namen wie für die Ressourcengruppe zu wählen.

    > [!Note]  
    > Verwenden Sie für jeden Cluster ein neues, eindeutiges DNS-Präfix des Masterprofils.

1. Wählen Sie **Kubernetes Master Pool Profile Count** (Kubernetes-Masterpool-Profilanzahl). Die Anzahl gibt die Anzahl von Knoten im Masterpool an. Sie können einen Wert von 1 bis 7 angeben. Dieser Wert sollte eine ungerade Zahl sein.

1. Wählen Sie **The VMSize of the Kubernetes master VMs** (VM-Größe der Kubernetes-Master-VMs).

1. Wählen Sie **Kubernetes Node Pool Profile Count** (Kubernetes-Knotenpool-Profilanzahl). Er gibt die Anzahl von Agents im Cluster an. 

1. Wählen Sie **VMSize of the Kubernetes node VMs** (VM-Größe der Kubernetes-Knoten-VMs). Hiermit wird die VM-Größe der Kubernetes-Knoten-VMs angegeben. 

1. Wählen Sie **AD FS** als **Azure Stack Hub-Identitätssystem** für Ihre Azure Stack Hub-Installation aus.

1. Geben Sie unter **Service Principal ClientId** (Dienstprinzipal-ClientId) einen Wert ein. Dieser wird vom Kubernetes Azure-Cloudanbieter verwendet. Die als Anwendungs-ID identifizierte Client-ID, wenn Ihr Azure Stack Hub-Administrator den Dienstprinzipal erstellt hat.

1. Geben Sie das **Clientgeheimnis des Dienstprinzipals** ein. Dies ist der geheime Clientschlüssel, der Ihnen von Ihrem Azure Stack Hub-Administrator für Ihren AD FS-Dienstprinzipal bereitgestellt wurde.

1. Geben Sie die **Kubernetes-Version** ein. Dabei handelt es sich um die Version des Kubernetes Azure-Anbieters. Azure Stack Hub veröffentlicht für jede Azure Stack Hub-Version einen benutzerdefinierten Kubernetes-Build.

### <a name="3-summary"></a>3. Zusammenfassung

1. Wählen Sie „Zusammenfassung“. Auf dem Blatt wird eine Überprüfungsnachricht für Ihre Konfigurationseinstellungen des Kubernetes-Clusters angezeigt.

    ![Bereitstellen einer Lösungsvorlage](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. Überprüfen Sie Ihre Einstellungen.

3. Wählen Sie **OK**, um Ihren Cluster bereitzustellen.

> [!TIP]  
>  Wenn Sie Fragen zu Ihrer Bereitstellung haben, können Sie Ihre Frage im [Azure Stack Hub-Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) stellen bzw. ermitteln, ob jemand die Frage dort bereits beantwortet hat. 

## <a name="next-steps"></a>Nächste Schritte

[Herstellen einer Clusterverbindung](azure-stack-solution-template-kubernetes-deploy.md#connect-to-your-cluster)

[Aktivieren des Kubernetes-Dashboards](azure-stack-solution-template-kubernetes-dashboard.md)