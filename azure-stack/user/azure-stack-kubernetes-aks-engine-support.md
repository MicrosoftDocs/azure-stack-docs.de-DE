---
title: Supportrichtlinien für die AKS-Engine in Azure Stack Hub
description: Dieses Thema enthält die Supportrichtlinien für die AKS-Engine in Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/0102020
ms.openlocfilehash: 9969447ab737c6913576c73b2e53453a6cb8e1f6
ms.sourcegitcommit: ccc4ee05d71496653b6e27de1bb12e4347e20ba4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102231760"
---
# <a name="support-policies-for-aks-engine-on-azure-stack-hub"></a>Supportrichtlinien für die AKS-Engine in Azure Stack Hub

Dieser Artikel enthält Details zu den Richtlinien für technischen Support und Einschränkungen für die AKS-Engine in Azure Stack Hub. Der Artikel enthält auch Details zum Kubernetes-Marketplace-Element, zu Open-Source-Komponenten von Drittanbietern und zur Sicherheits- oder Patchverwaltung. 

## <a name="self-managed-kubernetes-clusters-on-azure-stack-hub-with-aks-engine"></a>Selbstverwaltete Kubernetes-Cluster in Azure Stack Hub mit AKS-Engine

IaaS-Cloudkomponenten (Infrastructure-as-a-Service), z. B. Compute- oder Netzwerkkomponenten, bieten Benutzern auf niedriger Ebene Zugriff auf Steuerelemente und Anpassungsoptionen. Die AKS-Engine ermöglicht es dem Benutzer, Kubernetes-Cluster mit transparenter Nutzung dieser IaaS-Komponenten einzurichten, damit Benutzer auf alle Aspekte ihrer Bereitstellungen zugreifen und sie beeinflussen können.

Wenn ein Cluster erstellt wird, definiert der Kunde die Master- und Workerknoten von Kubernetes, die von der AKS-Engine erstellt werden. Auf diesen Knoten werden Kundenworkloads ausgeführt. Kunden sind die Besitzer der Master- und Workerknoten und können sie anzeigen oder ändern. Sorglos geänderte Knoten können zu Daten- und Workloadverlusten führen und dazu, dass der Cluster nicht mehr funktioniert. Außerdem überschreiben AKS-Engine-Vorgänge, wie z. B. Upgrade oder Skalierung, alle außerhalb des zulässigen Bereichs liegenden Änderungen. Wenn der Cluster also beispielsweise über statische Pods verfügt, werden diese bei einem Upgradevorgang der AKS-Engine nicht beibehalten.

Da Clusterknoten von Kunden privaten Code ausführen und vertrauliche Daten speichern, kann der Microsoft-Support nur eingeschränkt darauf zugreifen. Der Microsoft-Support kann sich ohne ausdrückliche Genehmigung oder Hilfe des Kunden nicht bei diesen Knoten anmelden, Befehle ausführen oder Protokolle anzeigen.

## <a name="version-support"></a>Versionsunterstützung

Der Support für die AKS-Engine-Version folgt demselben Muster, das auch von der restlichen Azure Stack Hub-Supportrichtlinie festgelegt wird, was heißt, dass der Support einer Version der AKS-Engine in Azure Stack Hub auf der n-2-Formel basiert. Wenn die neueste Version der AKS-Engine beispielsweise v0.55.0 ist, wird folgende Gruppe von Versionen unterstützt: 0.48.0, 0.51.0, 0.55.0. Außerdem ist es wichtig, die Azure Stack Hub-Updateversion und die entsprechende Zuordnung zur von der AKS-Engine unterstützten Version einzuhalten. Dies wird in den [Versionshinweisen zur AKS-Engine ](kubernetes-aks-engine-release-notes.md#aks-engine-and-azure-stack-version-mapping) verwaltet.

## <a name="aks-engine-supported-areas"></a>Von der AKS-Engine unterstützte Bereiche

Microsoft bietet technischen Support für Folgendes:

-  Probleme mit AKS-Engine-Befehlen: bereitstellen, generieren, aktualisieren und skalieren. Das Verhalten des Tools in Azure sollte konsistent sein.
-  Probleme mit einem gemäß der [Übersicht über die AKS-Engine](azure-stack-kubernetes-aks-engine-overview.md) bereitgestellten Kubernetes-Cluster.
-  Probleme mit der Konnektivität mit anderen Azure Stack Hub-Diensten 
-  Probleme mit der Kubernetes-API-Konnektivität
-  Probleme mit der Funktionalität des Azure Stack Hub-Kubernetes-Anbieters und der Konnektivität mit Azure Resource Manager
-  Probleme mit der von der AKS-Engine generierten Konfiguration nativer Azure Stack Hub-Artefakte wie Lastenausgleichsmodulen, Netzwerksicherheitsgruppen, VNETs, Subnetzen, Netzwerkschnittstellen, Routingtabelle, Verfügbarkeitsgruppen, öffentlichen IP-Adressen, Speicherkonto und virtuellen Computern 
-  Probleme mit Netzwerkleistung und Wartezeit
-  Probleme mit dem AKS-Basisimage, das von der AKS-Engine in getrennten Bereitstellungen verwendet wird. 

## <a name="aks-engine-areas-not-supported"></a>Nicht unterstützte AKS-Engine-Bereiche

Microsoft bietet für Folgendes keinen technischen Support:

-  Verwendung der AKS-Engine in Azure.
-  Azure Stack Hub-Kubernetes-Marketplace-Element.
-  Verwendung der folgenden Optionen und Add-Ons von AKS-Engine zur Clusterdefinition.
    -  Nicht unterstützte Add-Ons:  
            – Azure AD-Podidentität  
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
-  Drittanbietersoftware. Diese Software kann z. B. Tools für Sicherheitsscans und Netzwerkgeräte oder -software umfassen.
-  Probleme mit Multi-Cloud- oder Multi-Vendor-Erweiterungen. Microsoft unterstützt z. B. keine Probleme bei der Ausführung einer vereinten Multipublic-Cloudanbieterlösung.
-  Netzwerkanpassungen, die nicht im Abschnitt [Von der AKS-Engine unterstützte Bereiche](#aks-engine-supported-areas) aufgeführt werden.
-  In Produktionsumgebungen sollten nur hochverfügbare Kubernetes-Cluster verwendet werden (also Cluster, die mit mindestens drei Mastern und drei Agent-Knoten bereitgestellt wurden). Alles darunter kann in Produktionsbereitstellungen nicht unterstützt werden.

##  <a name="security-issues-and-patching"></a>Sicherheitsprobleme und -patches

Wenn in einer oder mehreren Komponenten der AKS-Engine oder des Kubernetes-Anbieters für Azure Stack Hub eine Sicherheitsschwachstelle gefunden wird, stellt Microsoft den Kunden einen Patch für die betroffenen Cluster bereit, um das Problem zu umgehen. Alternativ wird das Team den Benutzern eine Anleitung für ein Upgrade bereitstellen. Beachten Sie, dass das Patchen eine Clusterdowntime erfordern könnte. Wenn Neustarts erforderlich sind, setzt Microsoft die Kunden über diese Anforderung in Kenntnis. Wenn Benutzer die Patches nicht gemäß den Anweisungen von Microsoft anwenden, ist ihr Cluster weiterhin anfällig für das Sicherheitsproblem.

## <a name="kubernetes-marketplace-item"></a>Kubernetes-Marketplace-Element

Benutzer können ein Kubernetes-Marketplace-Element herunterladen, mit dem sie Kubernetes-Cluster indirekt mithilfe der AKS-Engine über eine Vorlage im Azure Stack Hub-Benutzerportal bereitstellen können. Das ist einfacher als die direkte Verwendung der AKS-Engine. Das Kubernetes-Marketplace-Element ist ein praktisches Tool zur schnellen Einrichtung von Clustern für Demonstrationen, Tests und für die Entwicklung. Es ist nicht für die Produktion vorgesehen, da es nicht zu der Gruppe von Elementen gehört, für die Microsoft Support bietet.

## <a name="preview-features"></a>Vorschaufeatures

Für Features und Funktionen, die ausführliche Tests und Benutzerfeedback erfordern, veröffentlicht Microsoft neue Previewfunktionen oder Features hinter einem Featureflag. Betrachten Sie diese Features als Features einer Vorab- oder Betaversion. Previewfunktionen oder Features mit Featureflag sind nicht für die Produktionsumgebung vorgesehen. Kontinuierliche Änderungen an Funktionalität und Verhalten, Fehlerbehebungen und andere Änderungen können zu instabilen Clustern und Downtime führen. Diese Features werden von Microsoft nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die [AKS-Engine in Azure Stack Hub](azure-stack-kubernetes-aks-engine-overview.md).