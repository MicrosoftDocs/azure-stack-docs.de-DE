---
title: PowerShell-Modul AksHci
description: Erfahren Sie, wie Sie die Befehle des AksHci-Moduls verwenden, um AKS auf Azure Stack HCI zu verwalten.
author: jessicaguan
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 8d6b3410fb6fe6a00d602eeda97e172f1bac4062
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "101874444"
---
# <a name="akshci"></a>AksHci 

Befehle für die Interaktion mit Azure Kubernetes Service auf Azure Stack HCI.

## <a name="akshci-cmdlets"></a>AksHci-Cmdlets

|         |            |
| ------- | ---------- |
| [get-akshcicluster](get-akshcicluster.md) | Auflisten bereitgestellter Cluster, einschließlich des Hosts von Azure Kubernetes Service. |
| [get-akshciclusterupgrades](get-akshciclusterupgrades.md) | Abrufen der verfügbaren Upgrades für einen Cluster von Azure Kubernetes Service. |
| [get-akshciconfig](get-akshciconfig.md) | Auflisten der Konfigurationseinstellungen für den Azure Kubernetes Service-Host. |
| [get-akshcicredential](get-akshcicredential.md) | Zugreifen auf Ihren Cluster mit kubectl. |
| [get-akshcieventlog](get-akshcieventlog.md) | Abrufen alle Ereignisprotokolle aus dem AKS HCI-PowerShell-Modul. |
| [get-akshcikubernetesversion](get-akshcikubernetesversion.md) | Auflisten der verfügbaren Version zum Erstellen eines Managed Kubernetes-Clusters. |
| [get-akshcilogs](get-akshcilogs.md) | Erstellen eines ZIP-Ordners mit Protokollen von allen Ihren Pods. |
| [get-akshciupdates](get-akshciupdates.md) | Auflisten verfügbarer Updates für Azure Kubernetes Service auf Azure Stack HCI. |
| [get-akshciversion](get-akshciversion.md) | Abrufen der aktuellen Version von Azure Kubernetes Service auf Azure Stack HCI. |
| [get-akshcivmsize](get-akshcivmsize.md) | Auflisten unterstützter VM-Größen. |
| [initialize-akshcinode](initialize-akshcinode.md) | Ausführen von Überprüfungen für jeden physischen Knoten, um festzustellen, ob alle Anforderungen für die Installation von Azure Kubernetes Service auf Azure Stack HCI erfüllt sind. |
| [install-akshci](install-akshci.md) | Installieren von Azure Kubernetes Service auf Azure Stack HCI-Agents/Diensten und dem Host. |
| [install-akshciadauth](install-akshciadauth.md) | Installieren von Active Directory-Authentifizierung. |
| [install-akshciarconboarding](install-akshciarconboarding.md) | Herunterladen und Installieren von kubectl, dem Befehlszeilentool von Kubernetes. |
| [new-akshcicluster](new-akshcicluster.md) | Erstellen eines neuen Managed Kubernetes-Clusters. |
| [new-akshcinetworksetting](new-akshcinetworksetting.md) | Erstellen eines Objekts für ein neues virtuelles Netzwerk. |
| [remove-akshcicluster](remove-akshcicluster.md) | Löschen eines Managed Kubernetes-Clusters. |
| [restart-akshci](restart-akshci.md) | Neustarten von Azure Kubernetes Service auf Azure Stack HCI und Entfernen aller bereitgestellten Kubernetes-Cluster. |
| [set-akshciclusternodecount](set-akshciclusternodecount.md) | Skalieren der Anzahl von Knoten der Steuerungsebene oder von Workerknoten in einem Cluster. |
| [set-akshciconfig](Sset-akshciconfig.md) | Festlegen oder Aktualisieren der Konfigurationseinstellungen für den Azure Kubernetes Service-Host. |
| [uninstall-akshci](uninstall-akshci.md) | Entfernen von Azure Kubernetes Service auf Azure Stack HCI. |
| [uninstall-akshciadauth](uninstall-akshciadauth.md) | Entfernen von Active Directory-Authentifizierung. |
| [update-akshci](update-akshci.md) | Aktualisieren des Azure Kubernetes Service-Hosts auf die neueste Kubernetes-Version. |
| [update-akshcicluster](update-akshcicluster.md) | Aktualisieren eines Managed Kubernetes-Clusters auf eine neuere Kubernetes- oder Betriebssystemversion. |

