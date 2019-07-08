---
title: Hinzufügen von Kubernetes zum Azure Stack-Marketplace | Microsoft Docs
description: Erfahren Sie, wie Sie dem Azure Stack-Marketplace Kubernetes hinzufügen.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 06/18/2019
ms.openlocfilehash: 61d2739475a0593671e7a363671dd2859a6e6f24
ms.sourcegitcommit: 3f52cf06fb5b3208057cfdc07616cd76f11cdb38
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/21/2019
ms.locfileid: "67316245"
---
# <a name="add-kubernetes-to-the-azure-stack-marketplace"></a>Hinzufügen von Kubernetes zum Azure Stack-Marketplace

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

> [!note]  
> Kubernetes in Azure Stack befindet sich in der Vorschauphase. Ein Szenario mit nicht verbundenem Azure Stack wird von der Vorschau zurzeit nicht unterstützt. Verwenden Sie das Marketplace-Element nur für Entwicklungs- und Testszenarien.

Sie können Kubernetes als ein Marketplace-Element für Ihre Benutzer anbieten. Ihre Benutzer können dann Kubernetes in einem einzelnen, koordinierten Vorgang bereitstellen.

Im folgenden Artikel wird das Bereitstellen der Ressourcen für einen eigenständigen Kubernetes-Cluster mithilfe einer Azure Resource Manager-Vorlage betrachtet. Bevor Sie beginnen, überprüfen Sie Ihren Azure Stack und Ihre globalen Azure-Mandanteneinstellungen. Sammeln Sie die erforderlichen Informationen zu Ihrem Azure Stack. Fügen Sie Ihrem Mandanten und dem Azure Stack Marketplace erforderliche Ressourcen hinzu. Der Cluster ist von einem Ubuntu-Server, einem benutzerdefinierten Skript und davon abhängig, dass das Marketplace-Element „Kubernetes-Cluster“ im Marketplace vorhanden ist.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>Erstellen eines Plans, eines Angebots und eines Abonnements

Erstellen Sie einen Plan, ein Angebot und ein Abonnement für das Marketplace-Element für Kubernetes. Sie können auch einen vorhandenen Plan und ein vorhandenes Angebot verwenden.

1. Melden Sie sich beim [Verwaltungsportal](https://adminportal.local.azurestack.external) an.

1. Erstellen Sie einen Plan als Basisplan. Anweisungen hierzu finden Sie unter [Erstellen von Plänen in Azure Stack](azure-stack-create-plan.md).

1. Erstellen Sie ein Angebot. Anweisungen hierzu finden Sie unter [Erstellen von Angeboten in Azure Stack](azure-stack-create-offer.md).

1. Wählen Sie **Angebote** aus, und suchen Sie das Angebot, das Sie erstellt haben.

1. Wählen Sie auf dem Blatt „Angebot“ die Option **Übersicht** aus.

1. Wählen Sie **Status ändern** aus. Wählen Sie **Öffentlich** aus.

1. Wählen Sie **Ressource erstellen** > **Angebote und Pläne** > **Abonnement** aus, um ein Abonnement zu erstellen.

    a. Geben Sie einen **Anzeigenamen** ein.

    b. Geben Sie einen **Benutzer** ein. Verwenden Sie das Azure AD-Konto, das mit Ihrem Mandanten verknüpft ist.

    c. **Anbieterbeschreibung**

    d. Legen Sie **Verzeichnismandant** auf den Azure AD-Mandanten für Ihren Azure Stack fest. 

    e. Wählen Sie **Angebot** aus. Wählen Sie den Namen des Angebots aus, das Sie erstellt haben. Notieren Sie sich die Abonnement-ID.

## <a name="create-a-service-principal-and-credentials-in-ad-fs"></a>Erstellen eines Dienstprinzipals und von Anmeldeinformationen in AD FS

Wenn Sie Active Directory-Verbunddienste (AD FS) als Ihren Identitätsverwaltungsdienst verwenden, müssen Sie einen Dienstprinzipal für Benutzer erstellen, die einen Kubernetes-Cluster bereitstellen. Erstellen Sie einen Dienstprinzipal mithilfe eines geheimen Clientschlüssels. Anleitungen finden Sie unter [Erstellen eines Dienstprinzipals mithilfe eines geheimen Clientschlüssels](azure-stack-create-service-principals.md#create-a-service-principal-that-uses-client-secret-credentials).

## <a name="add-an-ubuntu-server-image"></a>Fügen Sie ein Ubuntu-Serverimage hinzu.

Fügen Sie dem Marketplace das folgende Ubuntu-Serverimage hinzu:

1. Melden Sie sich beim [Verwaltungsportal](https://adminportal.local.azurestack.external) an.

1. Wählen Sie **Alle Dienste** aus, und wählen Sie dann unter der Kategorie **VERWALTUNG** die Option **Marketplace-Verwaltung** aus.

1. Wählen Sie **+ Add from Azure** (+ Aus Azure hinzufügen) aus.

1. Geben Sie `Ubuntu Server` ein.

1. Wählen Sie die neueste Version des Servers aus. Überprüfen Sie die vollständige Version, und stellen Sie sicher, dass Sie über die neueste Version verfügen:
    - **Herausgeber**: Canonical
    - **Angebot**: UbuntuServer
    - **Version**: 16.04.201806120 (oder neueste Version)
    - **SKU**: 16.04-LTS

1. Wählen Sie **Herunterladen** aus.

## <a name="add-a-custom-script-for-linux"></a>Hinzufügen eines benutzerdefinierten Skripts für Linux

Fügen Sie Kubernetes aus dem Marketplace hinzu:

1. Öffnen Sie das [Verwaltungsportal](https://adminportal.local.azurestack.external).

1. Wählen Sie **Alle Dienste** aus, und wählen Sie dann unter der Kategorie **VERWALTUNG** die Option **Marketplace-Verwaltung** aus.

1. Wählen Sie **+ Add from Azure** (+ Aus Azure hinzufügen) aus.

1. Geben Sie `Custom Script for Linux` ein.

1. Wählen Sie das Skript mit dem folgenden Profil aus:
   - **Angebot**: Custom Script for Linux 2.0
   - **Version**: 2.0.6 (oder neueste Version)
   - **Herausgeber**: Microsoft Corp.

     > [!Note]  
     > Möglicherweise werden mehrere Versionen des benutzerdefinierten Skripts für Linux aufgeführt. Sie müssen die neueste Version des Elements hinzufügen.

1. Wählen Sie **Herunterladen** aus.


## <a name="add-kubernetes-to-the-marketplace"></a>Hinzufügen von Kubernetes zum Marketplace

1. Öffnen Sie das [Verwaltungsportal](https://adminportal.local.azurestack.external).

1. Wählen Sie **Alle Dienste** aus, und wählen Sie dann unter der Kategorie **VERWALTUNG** die Option **Marketplace-Verwaltung** aus.

1. Wählen Sie **+ Add from Azure** (+ Aus Azure hinzufügen) aus.

1. Geben Sie `Kubernetes` ein.

1. Wählen Sie `Kubernetes Cluster` aus.

1. Wählen Sie **Herunterladen** aus.

    > [!note]  
    > Es kann fünf Minuten dauern, bis das Marketplace-Element im Marketplace angezeigt wird.

    ![kubernetes](../user/media/azure-stack-solution-template-kubernetes-deploy/marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes"></a>Aktualisieren oder Entfernen von Kubernetes 

Beim Aktualisieren des Kubernetes-Elements müssen Sie das vorherige Element im Marketplace entfernen. Befolgen Sie die Anweisungen in diesem Artikel, um das Kubernetes-Update zum Marketplace hinzuzufügen.

So entfernen Sie das Kubernetes-Element:

1. Stellen Sie als Operator eine Verbindung mit Azure Stack über PowerShell her. Anleitungen dazu finden Sie unter [Herstellen einer Verbindung mit Azure Stack über PowerShell als Operator](azure-stack-powershell-configure-admin.md).

2. Suchen Sie im Katalog nach dem aktuellen Element „Kubernetes-Cluster“.

    ```powershell  
    Get-AzsGalleryItem | Select Name
    ```
    
3. Notieren Sie sich den Namen des aktuellen Elements, z.B. `Microsoft.AzureStackKubernetesCluster.0.3.0`.

4. Verwenden Sie das folgende PowerShell-Cmdlet, um das Element zu entfernen:

    ```powershell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.3.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>Nächste Schritte

[Bereitstellen von Kubernetes in Azure Stack](../user/azure-stack-solution-template-kubernetes-deploy.md)

[Übersicht über das Anbieten von Diensten in Azure Stack](azure-stack-offer-services-overview.md)
