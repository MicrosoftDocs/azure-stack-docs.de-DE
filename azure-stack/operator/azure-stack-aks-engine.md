---
title: Hinzufügen von Voraussetzungen für die Azure Kubernetes Services-Engine (AKS) zum Azure Stack-Marketplace | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Voraussetzungen für die AKS-Engine zum Azure Stack-Marketplace hinzufügen.
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
ms.date: 11/05/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: c21223ca8926a1e2b3ca1b06752a79a14adaeda1
ms.sourcegitcommit: 5c92a669007ab4aaffe4484f1d8836a40340dde1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73636879"
---
# <a name="add-the-azure-kubernetes-services-aks-engine-prerequisites-to-the-azure-stack-marketplace"></a>Hinzufügen von Voraussetzungen für die Azure Kubernetes Services-Engine (AKS) zum Azure Stack-Marketplace

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Sie können es Ihren Benutzern ermöglichen, die Azure Kubernetes Service-Engine (AKS) einzurichten, indem Sie die in diesem Artikel beschriebenen Elemente zu Ihrer Azure Stack-Instanz hinzufügen. Ihre Benutzer können dann einen Kubernetes-Cluster in einem einzelnen, koordinierten Vorgang bereitstellen. In diesem Artikel werden die Schritte erläutert, die Sie ausführen müssen, um die AKS-Engine Ihren Benutzern sowohl in verbundenen als auch in nicht verbundenen Umgebungen zur Verfügung zu stellen. Die AKS-Engine benötigt eine Dienstprinzipalidentität sowie im Marketplace eine benutzerdefinierte Skripterweiterung und das AKS-Basisimage.

Die [AKS-Engine](https://github.com/Azure/aks-engine) verwendet ein erstelltes Image, das AKS-Basisimage. Jede Version der AKS-Engine hängt von einer bestimmten Imageversion ab, die Sie in Ihrer Azure Stack-Instanz zur Verfügung stellen können. Eine Tabelle mit den AKS-Engine-Versionen und den entsprechenden Kubernetes-Versionen finden Sie unter [Supported Kubernetes Versions](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions) (Unterstützte Kubernetes-Versionen).

> [!IMPORTANT]
> Die AKS-Engine ist zurzeit als öffentliche Vorschauversion verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="check-your-users-service-offering"></a>Überprüfen des Dienstangebots für Ihre Benutzer

Ihre Benutzer benötigen einen Plan, ein Angebot und ein Azure Stack-Abonnement mit genügend Speicherplatz. Benutzer möchten häufig Cluster mit bis zu sechs virtuellen Computern bereitstellen, die sich aus drei Masterknoten und drei Workerknoten zusammensetzen. Sie müssen sicherstellen, dass dafür ein ausreichend großes Kontingent verfügbar ist.

Weitere Informationen zum Planen und Einrichten eines Dienstangebots finden Sie in der [Übersicht über das Anbieten von Diensten in Azure Stack](service-plan-offer-subscription-overview.md).

## <a name="create-a-service-principal-and-credentials"></a>Erstellen eines Dienstprinzipals und der zugehörigen Anmeldeinformationen

Für den Kubernetes-Cluster sind ein Dienstprinzipal und rollenbasierte Berechtigung in Azure Stack erforderlich.

### <a name="create-an-spn-in-azure-ad"></a>Erstellen eines Dienstprinzipals in Azure AD

Wenn Sie Azure Active Directory (Azure AD) als Identitätsverwaltungsdienst verwenden, müssen Sie einen Dienstprinzipal für Benutzer erstellen, die einen Kubernetes-Cluster bereitstellen. Erstellen Sie einen Dienstprinzipal mithilfe eines geheimen Clientschlüssels. Eine Anleitung dazu finden Sie unter [Erstellen eines Dienstprinzipals, der Anmeldeinformationen mit einem geheimen Clientschlüssel verwendet](azure-stack-create-service-principals.md#create-a-service-principal-that-uses-a-client-secret-credential).

### <a name="create-an-spn-in-ad-fs"></a>Erstellen eines Dienstprinzipals in AD FS

Wenn Sie Active Directory-Verbunddienste (AD FS) als Ihren Identitätsverwaltungsdienst verwenden, müssen Sie einen Dienstprinzipal für Benutzer erstellen, die einen Kubernetes-Cluster bereitstellen. Erstellen Sie einen Dienstprinzipal mithilfe eines geheimen Clientschlüssels. Anleitungen finden Sie unter [Erstellen eines Dienstprinzipals mithilfe eines geheimen Clientschlüssels](azure-stack-create-service-principals.md#create-a-service-principal-that-uses-client-secret-credentials).

## <a name="add-the-aks-base-image"></a>Hinzufügen des AKS-Basisimages

Sie können das AKS-Basisimage zum Marketplace hinzufügen, indem Sie das Element aus Azure abrufen. Wenn keine Verbindung zu Ihrer Azure Stack-Instanz besteht, lesen Sie die Anweisungen unter [Herunterladen von Marketplace-Elementen aus Azure](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item?view=azs-1908#disconnected-or-a-partially-connected-scenario), um das Element hinzuzufügen. Fügen Sie das in Schritt 5 angegebene Element hinzu.

Fügen Sie das folgende Element zum Marketplace hinzu:

1. Melden Sie sich beim [Verwaltungsportal](https://adminportal.local.azurestack.external) an.

1. Wählen Sie **Alle Dienste** aus, und wählen Sie dann unter der Kategorie **VERWALTUNG** die Option **Marketplace-Verwaltung** aus.

1. Wählen Sie **+ Add from Azure** (+ Aus Azure hinzufügen) aus.

1. Geben Sie `AKS Base` ein.

1. Wählen Sie die Imageversion aus, die der Version der AKS-Engine entspricht. Eine Liste der AKS-Basisimages für verschiedene Versionen der AKS-Engine finden Sie unter [Supported Kubernetes Versions](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions) (Unterstützte Kubernetes-Versionen). 

    Wählen Sie in der Liste Folgendes aus:
    - **Herausgeber**: Azure Kubernetes Service
    - **Angebot**: aks
    - **Version**: AKS-Basisimage 16.04-LTS Image Distro, October 2019 (2019.10.24 oder Version mit Zuordnung zur AKS-Engine)

1. Wählen Sie **Herunterladen** aus.

## <a name="add-a-custom-script-extension"></a>Hinzufügen einer benutzerdefinierten Skripterweiterung

Sie können das benutzerdefinierte Skript zum Marketplace hinzufügen, indem Sie das Element aus Azure abrufen. Wenn keine Verbindung zu Ihrer Azure Stack-Instanz besteht, lesen Sie die Anweisungen unter [Herunterladen von Marketplace-Elementen aus Azure](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item?view=azs-1908#disconnected-or-a-partially-connected-scenario), um das Element hinzuzufügen.  Fügen Sie das in Schritt 5 angegebene Element hinzu.

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

## <a name="next-steps"></a>Nächste Schritte

[Was ist die AKS-Engine in Azure Stack?](../user/azure-stack-kubernetes-aks-engine-overview.md)

[Übersicht über das Anbieten von Diensten in Azure Stack](service-plan-offer-subscription-overview.md)
