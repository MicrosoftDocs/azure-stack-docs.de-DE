---
title: Bereitstellen von Kubernetes in Azure Stack mithilfe von Active Directory-Verbunddienste (AD FS) | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Kubernetes in Azure Stack mithilfe von Active Directory-Verbunddienste (AD FS) bereitstellen.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: nav
ms.topic: article
ms.date: 06/18/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 06/18/2019
ms.openlocfilehash: 1a2dbe009a6953990ce59e930490cc48cc0dd458
ms.sourcegitcommit: 104ccafcb72a16ae7e91b154116f3f312321cff7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/21/2019
ms.locfileid: "67308733"
---
# <a name="deploy-kubernetes-to-azure-stack-using-active-directory-federated-services"></a>Bereitstellen von Kubernetes in Azure Stack mithilfe von Active Directory-Verbunddienste

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

> [!Note]  
> Kubernetes in Azure Stack befindet sich in der Vorschauphase. Ein Szenario mit nicht verbundenem Azure Stack wird von der Vorschau zurzeit nicht unterstützt. Verwenden Sie das Marketplace-Element nur für Entwicklungs- und Testszenarien.

Sie können die Schritte in diesem Artikel zum Bereitstellen und Einrichten der Ressourcen für Kubernetes befolgen. Verwenden Sie diese Schritte, wenn Sie Active Directory-Verbunddienste (AD FS) als Ihren Identitätsverwaltungsdienst nutzen.

## <a name="prerequisites"></a>Voraussetzungen 

Stellen Sie zum Einstieg sicher, dass Sie über die erforderlichen Berechtigungen verfügen und dass Ihre Azure Stack-Instanz vorbereitet ist.

1. Generieren Sie ein öffentlich-privates SSH-Schlüsselpaar für die Anmeldung beim virtuellen Linux-Computer in Azure Stack. Sie benötigen den öffentlichen Schlüssel bei der Clustererstellung.

    Anweisungen zum Generieren eines Schlüssels finden Sie unter [SSH Key Generation](azure-stack-dev-start-howto-ssh-public-key.md) (SSH-Schlüsselgenerierung).

1. Stellen Sie sicher, dass Sie ein gültiges Abonnement im Azure Stack-Mandantenportal besitzen und ausreichend öffentliche IP-Adressen zum Hinzufügen neuer Anwendungen verfügbar sind.

    Der Cluster kann nicht in einem Azure Stack-Abonnement vom Typ **Administrator** bereitgestellt werden. Sie müssen ein Abonnement vom Typ **Benutzer** verwenden. 

1. Falls der Kubernetes-Cluster in Ihrem Marketplace nicht verfügbar ist, wenden Sie sich an Ihren Azure Stack-Administrator.

## <a name="create-a-service-principal"></a>Erstellen eines Dienstprinzipals

Falls Sie als Identitätslösung AD FS verwenden, müssen Sie mit Ihrem Azure Stack-Administrator zusammenarbeiten, um den Dienstprinzipal einzurichten. Der Dienstprinzipal verschafft Ihrer Anwendung Zugriff auf Azure Stack-Ressourcen.

1. Ihr Azure Stack-Administrator stellt Ihnen die Informationen für den Dienstprinzipal bereit. Die Dienstprinzipalinformationen sollten wie folgt aussehen:

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

1. Öffnen Sie das [Azure Stack-Portal](https://portal.local.azurestack.external).

1. Wählen Sie **+ Ressource erstellen** > **Compute** > **Kubernetes-Cluster** aus. Klicken Sie auf **Erstellen**.

    ![Bereitstellen einer Lösungsvorlage](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1. Grundlagen

1. Wählen Sie unter „Kubernetes-Cluster erstellen“ die Option **Grundlagen**.

    ![Bereitstellen einer Lösungsvorlage](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. Wählen Sie die ID für Ihr **Abonnement** aus.

1. Geben Sie den Namen einer neuen Ressourcengruppe ein, oder wählen Sie eine vorhandene Ressourcengruppe aus. Der Ressourcenname muss alphanumerisch und in Kleinbuchstaben angegeben sein.

1. Wählen Sie den **Standort** der Ressourcengruppe aus. Dies ist die Region, die Sie für die Azure Stack-Installation auswählen.

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

1. Wählen Sie **AD FS** als **Azure Stack-Identitätssystem** für Ihre Azure Stack-Installation aus.

1. Geben Sie unter **Service Principal ClientId** (Dienstprinzipal-ClientId) einen Wert ein. Dieser wird vom Kubernetes Azure-Cloudanbieter verwendet. Die als Anwendungs-ID identifizierte Client-ID, wenn Ihr Azure Stack-Administrator den Dienstprinzipal erstellt hat.

1. Geben Sie das **Clientgeheimnis des Dienstprinzipals** ein. Dies ist der geheime Clientschlüssel, der Ihnen von Ihrem Azure Stack-Administrator für Ihren AD FS-Dienstprinzipal bereitgestellt wurde.

1. Geben Sie die **Kubernetes-Version** ein. Dabei handelt es sich um die Version des Kubernetes Azure-Anbieters. Azure Stack veröffentlicht für jede Azure Stack-Version einen benutzerdefinierten Kubernetes-Build.

### <a name="3-summary"></a>3. Zusammenfassung

1. Wählen Sie „Zusammenfassung“. Auf dem Blatt wird eine Überprüfungsnachricht für Ihre Konfigurationseinstellungen des Kubernetes-Clusters angezeigt.

    ![Bereitstellen einer Lösungsvorlage](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. Überprüfen Sie Ihre Einstellungen.

3. Wählen Sie **OK**, um Ihren Cluster bereitzustellen.

> [!TIP]  
>  Wenn Sie Fragen zu Ihrer Bereitstellung haben, können Sie Ihre Frage im [Azure Stack-Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) stellen bzw. ermitteln, ob jemand die Frage dort bereits beantwortet hat. 

## <a name="next-steps"></a>Nächste Schritte

[Herstellen einer Clusterverbindung](azure-stack-solution-template-kubernetes-deploy.md#connect-to-your-cluster)

[Aktivieren des Kubernetes-Dashboards](azure-stack-solution-template-kubernetes-dashboard.md)