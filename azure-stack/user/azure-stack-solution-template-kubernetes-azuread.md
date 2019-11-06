---
title: Bereitstellen von Kubernetes in Azure Stack mithilfe von Azure Active Directory (Azure AD) | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Kubernetes in Azure Stack mithilfe von Azure Active Directory (Azure AD) bereitstellen.
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
ms.date: 10/10/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 06/18/2019
ms.openlocfilehash: 902645ffcb6fda4afad76a1a258b55f0ace2b189
ms.sourcegitcommit: 0d27456332031ab98ba2277117395ae5ffcbb79f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73047242"
---
# <a name="deploy-kubernetes-to-azure-stack-using-azure-active-directory"></a>Bereitstellen von Kubernetes in Azure Stack mithilfe von Azure Active Directory

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

> [!Note]  
> Verwenden Sie das Kubernetes-Azure Stack-Marketplace-Element nur, um Cluster als Proof of Concept bereitzustellen. Verwenden Sie für unterstützte Kubernetes-Cluster in Azure Stack  [die AKS-Engine](azure-stack-kubernetes-aks-engine-overview.md).

Sie können die Schritte in diesem Artikel zum Bereitstellen und Einrichten der Ressourcen für Kubernetes in einem einzigen, koordinierten Vorgang befolgen, wenn Sie Azure Active Directory (Azure AD) als Ihren Identitätsverwaltungsdienst verwenden.

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie zum Einstieg sicher, dass Sie über die erforderlichen Berechtigungen verfügen und dass Ihre Azure Stack-Instanz vorbereitet ist.

1. Überprüfen Sie, ob Sie Anwendungen in Ihrem Azure Active Directory-Mandanten (Azure AD) erstellen können. Sie benötigen diese Berechtigungen für die Kubernetes-Bereitstellung.

    Anweisungen zum Überprüfen Ihrer Berechtigungen finden Sie unter [Erstellen einer Azure Active Directory-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff mithilfe des Portals](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

1. Generieren Sie ein öffentlich-privates SSH-Schlüsselpaar für die Anmeldung beim virtuellen Linux-Computer in Azure Stack. Sie benötigen den öffentlichen Schlüssel bei der Clustererstellung.

    Anweisungen zum Generieren eines Schlüssels finden Sie unter [SSH Key Generation](azure-stack-dev-start-howto-ssh-public-key.md) (SSH-Schlüsselgenerierung).

1. Stellen Sie sicher, dass Sie ein gültiges Abonnement im Azure Stack-Mandantenportal besitzen und ausreichend öffentliche IP-Adressen zum Hinzufügen neuer Anwendungen verfügbar sind.

    Der Cluster kann nicht in einem Azure Stack-Abonnement vom Typ **Administrator** bereitgestellt werden. Sie müssen ein Abonnement vom Typ **Benutzer** verwenden. 

1. Falls der Kubernetes-Cluster in Ihrem Marketplace nicht verfügbar ist, wenden Sie sich an Ihren Azure Stack-Administrator.

## <a name="create-a-service-principal"></a>Erstellen eines Dienstprinzipals

Richten Sie einen Dienstprinzipal in Azure AD ein. Der Dienstprinzipal verschafft Ihrer Anwendung Zugriff auf Azure Stack-Ressourcen.

1. Melden Sie sich beim globalen [Azure-Portal](https://portal.azure.com) an.

1. Sie müssen mit dem der Azure Stack-Instanz zugeordneten Azure AD-Mandanten angemeldet sein. Sie können Ihre Anmeldung wechseln, indem Sie auf das Filtersymbol in der Azure-Symbolleiste klicken.

    ![Auswählen des AD-Mandanten](media/azure-stack-solution-template-kubernetes-deploy/tenantselector.png)

1. Erstellen Sie eine Azure AD-Anwendung.

    a. Melden Sie sich über das [Azure-Portal](https://portal.azure.com) bei Ihrem Azure-Konto an.  
    b. Wählen Sie **Azure Active Directory** > **App-Registrierungen** > **Neue Registrierung** aus.  
    c. Geben Sie einen Namen und eine URL für die Anwendung an.  
    d. Wählen Sie die **unterstützten Kontotypen** aus.  
    e.  Fügen Sie `http://localhost` für den URI für die Anwendung hinzu. Wählen Sie als Typ für die zu erstellende Anwendung **Web** aus. Wählen Sie nach dem Festlegen der Werte **Registrieren** aus.

1. Notieren Sie den Wert der **Anwendungs-ID**. Sie benötigen die ID bei der Clustererstellung. Die ID wird als **Client-ID des Dienstprinzipals** bezeichnet.

1. Wählen Sie auf dem Blatt für den Dienstprinzipal **Neuer geheimer Clientschlüssel** aus. Wählen Sie **Einstellungen** > **Schlüssel** aus. Sie müssen einen Authentifizierungsschlüssel für den Dienstprinzipal generieren.

    a. Geben Sie die **Beschreibung** ein.

    b. Wählen Sie unter **Gültig bis** die Option **Läuft nie ab** aus.

    c. Wählen Sie **Hinzufügen**. Notieren Sie sich die Schlüsselzeichenfolge. Sie benötigen die Schlüsselzeichenfolge bei der Clustererstellung. Der Schlüssel wird als **Clientgeheimnis des Dienstprinzipals** bezeichnet.

## <a name="give-the-service-principal-access"></a>Erteilen des Zugriffs für den Dienstprinzipal

Erteilen Sie dem Dienstprinzipal Zugriff auf Ihr Abonnement, sodass er Ressourcen erstellen kann.

1.  Melden Sie sich beim [Azure Stack-Portal](https://portal.local.azurestack.external/) an.

1. Wählen Sie **Alle Dienste** > **Abonnements** aus.

1. Wählen Sie das von Ihrem Operator für die Verwendung des Kubernetes-Clusters erstellte Abonnement aus.

1. Wählen Sie **Zugriffssteuerung (IAM)** > **Rollenzuweisung hinzufügen** aus.

1. Wählen Sie die Rolle **Mitwirkender** aus.

1. Wählen Sie den für Ihren Dienstprinzipal erstellten Anwendungsnamen aus. Unter Umständen müssen Sie den Namen in das Suchfeld eingeben.

1. Klicken Sie auf **Speichern**.

## <a name="deploy-kubernetes"></a>Bereitstellen von Kubernetes

1. Öffnen Sie das [Azure Stack-Portal](https://portal.local.azurestack.external).

1. Wählen Sie **+ Ressource erstellen** > **Compute** > **Kubernetes-Cluster** aus. Klicken Sie auf **Create**.

    ![Bereitstellen einer Lösungsvorlage](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1. Grundlagen

1. Wählen Sie unter „Kubernetes-Cluster erstellen“ die Option **Grundlagen**.

    ![Bereitstellen einer Lösungsvorlage](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. Wählen Sie die ID für Ihr **Abonnement** aus.

1. Geben Sie den Namen einer neuen Ressourcengruppe ein, oder wählen Sie eine vorhandene Ressourcengruppe aus. Der Ressourcenname muss alphanumerisch und in Kleinbuchstaben angegeben sein.

1. Wählen Sie den **Standort** der Ressourcengruppe aus. Dies ist die Region, die Sie für die Azure Stack-Installation auswählen.

### <a name="2-kubernetes-cluster-settings"></a>2. Einstellungen für Kubernetes-Cluster

1. Wählen Sie unter „Kubernetes Cluster erstellen“ die Option **Kubernetes Cluster Settings** (Einstellungen für Kubernetes-Cluster).

    ![Bereitstellen einer Lösungsvorlage](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-aad.png)

1. Geben Sie unter **Linux VM Admin Username** (Benutzername des Linux-VM-Administrators) einen Namen ein. Hierbei handelt es sich um den Benutzernamen für die virtuellen Linux-Computer, die Teil des Kubernetes-Clusters und des DVM (Deployment Virtual Machine, virtueller Computer für die Bereitstellung) sind.

1. Geben Sie den **öffentlichen SSH-Schlüssel** für die Autorisierung bei allen Linux-Computern ein, die als Teil des Kubernetes-Clusters und des DVM erstellt werden.

1. Geben Sie das für die Region eindeutige **DNS-Präfix des Masterprofils** ein. Diese Angabe muss ein für die Region eindeutiger Name sein, beispielsweise `k8s-12345`. Es wird empfohlen, den gleichen Namen wie für die Ressourcengruppe zu wählen.

    > [!Note]  
    > Verwenden Sie für jeden Cluster ein neues, eindeutiges DNS-Präfix des Masterprofils.

1. Wählen Sie **Kubernetes Master Pool Profile Count** (Kubernetes-Masterpool-Profilanzahl). Die Anzahl gibt die Anzahl von Knoten im Masterpool an. Sie können einen Wert von 1 bis 7 angeben. Dieser Wert sollte eine ungerade Zahl sein.

1. Wählen Sie **The VMSize of the Kubernetes master VMs** (VM-Größe der Kubernetes-Master-VMs). Hiermit wird die VM-Größe der Kubernetes-Master-VMs angegeben. 

1. Wählen Sie **Kubernetes Node Pool Profile Count** (Kubernetes-Knotenpool-Profilanzahl). Er gibt die Anzahl von Agents im Cluster an. 

1. Wählen Sie **VMSize of the Kubernetes node VMs** (VM-Größe der Kubernetes-Knoten-VMs). Hiermit wird die VM-Größe der Kubernetes-Knoten-VMs angegeben. 

1. Wählen Sie **Azure AD** als **Azure Stack-Identitätssystem** für Ihre Azure Stack-Installation aus.

1. Geben Sie unter **Service Principal ClientId** (Dienstprinzipal-ClientId) einen Wert ein. Dieser wird vom Kubernetes Azure-Cloudanbieter verwendet. Die als Anwendungs-ID identifizierte Client-ID, wenn Ihr Azure Stack-Administrator den Dienstprinzipal erstellt hat.

1. Geben Sie das **Clientgeheimnis des Dienstprinzipals** ein. Dies ist das Clientgeheimnis, das Sie beim Erstellen Ihres Diensts einrichten.

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
