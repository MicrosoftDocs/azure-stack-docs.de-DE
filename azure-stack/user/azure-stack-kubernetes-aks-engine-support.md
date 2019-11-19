---
title: Supportrichtlinien für die AKS-Engine in Azure Stack | Microsoft-Dokumentation
description: Dieses Thema enthält die Supportrichtlinien für die AKS-Engine in Azure Stack.
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
ms.date: 09/14/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/14/2019
ms.openlocfilehash: 8c83e0b006904482634e50bdf217ec5d236e4e5c
ms.sourcegitcommit: 5ef433aa6b75cdfb557fab0ef9308ff2118e66e5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73594952"
---
# <a name="support-policies-for-aks-engine-on-azure-stack"></a>Supportrichtlinien für die AKS-Engine in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Dieser Artikel enthält Details zu den Richtlinien für technischen Support und Einschränkungen für die AKS-Engine in Azure Stack. Der Artikel enthält auch Details zum Kubernetes-Marketplace-Element, zu Open-Source-Komponenten von Drittanbietern und zur Sicherheits- oder Patchverwaltung. 

> [!IMPORTANT]
> Die AKS-Engine ist zurzeit als öffentliche Vorschauversion verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="self-managed-kubernetes-clusters-on-azure-stack-with-aks-engine"></a>Selbstverwaltete Kubernetes-Cluster in Azure Stack mit AKS-Engine

IaaS-Cloudkomponenten (Infrastructure-as-a-Service), z. B. Compute- oder Netzwerkkomponenten, bieten Benutzern auf niedriger Ebene Zugriff auf Steuerelemente und Anpassungsoptionen. Die AKS-Engine ermöglicht dem Benutzer, Kubernetes-Cluster mit transparentem Einsatz dieser IaaS-Komponenten einzurichten; Benutzer können auf alle Aspekte ihrer Bereitstellungen zugreifen und sie beeinflussen.

Wenn ein Cluster erstellt wird, definiert der Kunde die Master- und Workerknoten von Kubernetes, die von der AKS-Engine erstellt werden. Auf diesen Knoten werden Kundenworkloads ausgeführt. Kunden sind die Besitzer der Master- und Workerknoten und können sie anzeigen oder ändern. Sorglos geänderte Knoten können zu Daten- und Workloadverlusten führen und dazu, dass der Cluster nicht mehr funktioniert. Außerdem überschreiben AKS-Engine-Vorgänge, wie z. B. Upgrade oder Skalierung, alle außerhalb des zulässigen Bereichs liegenden Änderungen. Wenn der Cluster z. B. über statische Pods verfügt, werden diese nach einem Upgradevorgang der AKS-Engine nicht beibehalten.

Da Clusterknoten von Kunden privaten Code ausführen und vertrauliche Daten speichern, kann der Microsoft-Support nur eingeschränkt darauf zugreifen. Der Microsoft-Support kann sich ohne ausdrückliche Genehmigung oder Hilfe des Kunden nicht bei diesen Knoten anmelden, Befehle ausführen oder Protokolle anzeigen.

## <a name="aks-engine-supported-areas"></a>Von der AKS-Engine unterstützte Bereiche

Microsoft bietet technischen Support für Folgendes:

-  Probleme mit AKS-Engine-Befehlen: bereitstellen, generieren, aktualisieren und skalieren. Das Verhalten des Tools in Azure sollte konsistent sein.
-  Probleme mit einem gemäß der [Übersicht über die AKS-Engine](azure-stack-kubernetes-aks-engine-overview.md) bereitgestellten Kubernetes-Cluster.
-  Probleme mit der Konnektivität mit anderen Azure Stack-Diensten 
-  Probleme mit der Kubernetes-API-Konnektivität
-  Probleme mit der Funktionalität des Azure Stack-Kubernetes-Anbieters und der Konnektivität mit Azure Resource Manager
-  Probleme mit der von der AKS-Engine generierten Konfiguration nativer Azure Stack-Artefakte wie Lastenausgleichsmodulen, Netzwerksicherheitsgruppen, VNETs, Subnetzen, Netzwerkschnittstellen, Routingtabelle, Verfügbarkeitsgruppen, öffentlichen IP-Adressen, Speicherkonto und VMs 
-  Probleme mit Netzwerkleistung und Wartezeit
-  Probleme mit dem AKS-Basisimage, das von der AKS-Engine in getrennten Bereitstellungen verwendet wird. 

## <a name="aks-engine-areas-not-supported"></a>Nicht unterstützte AKS-Engine-Bereiche

Microsoft bietet für Folgendes keinen technischen Support:

-  Verwendung der AKS-Engine in Azure.
-  Azure Stack-Kubernetes-Marketplace-Element.
-  Verwendung der folgenden Optionen und Add-Ons von AKS-Engine zur Clusterdefinition.
    -  Nicht unterstützte Add-Ons:  
            – AAD-Podidentität  
            – ACI-Connector  
            – Blobfuse Flex Volume  
            – Automatische Clusterskalierung  
            – Containerüberwachung  
            – KeyVault Flex Volume  
            – NVIDIA-Geräte-Plug-In  
            – Rescheduler  
            – SMB Flex Volume  
        
    -  Nicht unterstützte Clusterdefinitionsoptionen:  
            – Unter KubernetesConfig:  
                    – cloudControllerManagerConfig  
                    – enableDataEncryptionAtRest  
                    – enableEncryptionWithExternalKms  
                    – enablePodSecurityPolicy  
                    – etcdEncryptionKey  
                    – useInstanceMetadata  
                    – useManagedIdentity  
                    – azureCNIURLLinux  
                    – azureCNIURLWindows  
            – Unter masterProfile:  
                    – availabilityZones  
            – Unter agentPoolProfiles:  
                    – availabilityZones  
                    – singlePlacementGroup  
                    – scaleSetPriority  
                    – scaleSetEvictionPolicy  
                    – acceleratedNetworkingEnabled  
                    – acceleratedNetworkingEnabledWindows

-  Kubernetes-Konfigurationsänderungen, die außerhalb des Kubernetes-Konfigurationsspeichers etcd beibehalten werden. Beispielsweise statische Pods, die auf Knoten des Clusters ausgeführt werden.
-  Fragen zur Verwendung von Kubernetes. Der Microsoft-Support bietet z. B. keine Empfehlungen zur Erstellung benutzerdefinierter Eingangscontroller, zur Verwendung von Anwendungsworkloads oder zur Anwendung von Open-Source-Softwarepaketen oder -Tools bzw. zu Softwarepaketen oder Tools von Drittanbietern.
-  Open-Source-Projekte von Drittanbietern, die nicht als Teil des Kubernetes-Clusters von der AKS-Engine bereitgestellt werden. Diese Projekte könnten Kubeadm, Kubespray, Native, Istio, Helm, Envoy oder andere einbeziehen.
-  Verwenden der AKS-Engine in Anwendungsfallszenarien, die nicht in [Mit der AKS-Engine unterstützte Szenarien](azure-stack-kubernetes-aks-engine-overview.md#supported-scenarios-with-the-aks-engine) angegeben werden.
-  Drittanbietersoftware. Diese Software kann z. B. Tools für Sicherheitsscans und Netzwerkgeräte oder -software umfassen.
-  Probleme mit Multi-Cloud- oder Multi-Vendor-Erweiterungen. Microsoft unterstützt z. B. keine Probleme bei der Ausführung einer vereinten Multipublic-Cloudanbieterlösung.
-  Netzwerkanpassungen, die nicht im Abschnitt [Von der AKS-Engine unterstützte Bereiche](#aks-engine-supported-areas) aufgeführt werden.

##  <a name="security-issues-and-patching"></a>Sicherheitsprobleme und -patches

Wenn in einer oder mehreren Komponenten der AKS-Engine oder des Kubernetes-Anbieters für Azure Stack eine Sicherheitsschwachstelle gefunden wird, stellt Microsoft den Kunden einen Patch für die betroffenen Cluster bereit, um das Problem zu umgehen. Alternativ wird das Team den Benutzern eine Anleitung für ein Upgrade bereitstellen. Beachten Sie, dass das Patchen eine Clusterdowntime erfordern könnte. Wenn Neustarts erforderlich sind, setzt Microsoft die Kunden über diese Anforderung in Kenntnis. Wenn Benutzer die Patches nicht gemäß den Anweisungen von Microsoft anwenden, ist ihr Cluster weiterhin anfällig für das Sicherheitsproblem.

## <a name="kubernetes-marketplace-item"></a>Kubernetes-Marketplace-Element

Benutzer können ein Kubernetes-Marketplace-Element herunterladen, mit dem sie Kubernetes-Cluster indirekt über eine Vorlage im Azure Stack-Benutzerportal mithilfe der AKS-Engine bereitstellen können. Dies ist einfacher als die direkte Verwendung der AKS-Engine. Mit diesem nützlichen Tool können schnell Cluster für Demonstrationen, Tests und Entwicklung eingerichtet werden. Es ist nicht für die Produktion vorgesehen, da es nicht zu der Gruppe von Elementen gehört, für die Microsoft Support bietet.

## <a name="preview-features"></a>Previewfunktionen

Für Features und Funktionen, die ausführliche Tests und Benutzerfeedback erfordern, veröffentlicht Microsoft neue Previewfunktionen oder Features hinter einem Featureflag. Betrachten Sie diese Features als Features einer Vorab- oder Betaversion. Previewfunktionen oder Features mit Featureflag sind nicht für die Produktionsumgebung vorgesehen. Kontinuierliche Änderungen an Funktionalität und Verhalten, Fehlerbehebungen und andere Änderungen können zu instabilen Clustern und Downtime führen. Diese Features werden von Microsoft nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die [AKS-Engine in Azure Stack](azure-stack-kubernetes-aks-engine-overview.md).