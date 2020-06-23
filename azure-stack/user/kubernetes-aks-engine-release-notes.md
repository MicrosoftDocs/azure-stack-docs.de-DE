---
title: Versionshinweise für die AKS-Engine (Azure Kubernetes Service) in Azure Stack Hub
description: Es werden die Schritte beschrieben, die Sie beim Anwenden des Updates auf die AKS-Engine in Azure Stack Hub ausführen müssen.
author: mattbriggs
ms.topic: article
ms.date: 4/23/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 4/23/2020
ms.openlocfilehash: 9fd9671d4dbdad1dbf43b151c481fdbd9ca2c0ab
ms.sourcegitcommit: f0ee2a3af78dd6d6e2806710681d52b763948967
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/08/2020
ms.locfileid: "84533687"
---
# <a name="release-notes-for-the-aks-engine-on-azure-stack-hub"></a>Versionshinweise für die AKS-Engine in Azure Stack Hub

In diesem Artikel wird der Inhalt des Updates für die AKS-Engine (Azure Kubernetes Service) in Azure Stack Hub beschrieben. Das Update umfasst Verbesserungen und Fehlerbehebungen für das aktuelle Release der AKS-Engine für die Azure Stack Hub-Plattform. Beachten Sie, dass der Zweck dieses Artikels nicht darin besteht, die Releaseinformationen für die AKS-Engine der globalen Azure-Umgebung zu dokumentieren.

## <a name="update-planning"></a>Updateplanung

Mit dem Upgradebefehl für die AKS-Engine wird der Upgradevorgang Ihres Clusters vollständig automatisiert. Dies betrifft die Aufgaben in Bezug auf die Bereiche virtuelle Computer (VMs), Netzwerk, Speicher, Kubernetes und Orchestrierung. Lesen Sie sich vor dem Anwenden des Updates die Informationen in diesem Artikel durch.

### <a name="upgrade-considerations"></a>Überlegungen zur Aktualisierung

-   Verwenden Sie das richtige Marketplace-Element (AKS Base Ubuntu 16.04-LTS Image Distro) für Ihre Version der AKS-Engine? Informationen zu den Versionen finden Sie im Abschnitt [Herunterladen des neuen AKS Base Ubuntu-Images und der AKS-Engine-Versionen](#download-new-image-and-aks-engine).

-   Verwenden Sie die richtige Clusterspezifikation (apimodel.json) und Ressourcengruppe für den Zielcluster? Als Sie für den Cluster die ursprüngliche Bereitstellung durchgeführt haben, wurde diese Datei in Ihrem Ausgabeverzeichnis generiert. Informationen zu den Parametern des „deploy“-Befehls finden Sie unter [Bereitstellen eines Kubernetes-Clusters](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-deploy-cluster#deploy-a-kubernetes-cluster).

-   Verwenden Sie einen zuverlässigen Computer, auf dem die AKS-Engine ausgeführt wird und über den Sie Upgradevorgänge ausführen?

-   Beim Aktualisieren eines betriebsbereiten Clusters mit aktiven Workloads können Sie das Upgrade anwenden, ohne dass diese beeinträchtigt werden (sofern der Cluster normal ausgelastet ist). Sie sollten aber über einen zusätzlichen Cluster als Reserve verfügen, damit Benutzer unter Umständen an diesen Cluster umgeleitet werden können.

-   Führen Sie den Befehl wenn möglich von einem virtuellen Computer in der Azure Stack Hub-Umgebung aus, um die Anzahl der Netzwerkhops und potenzielle Verbindungsfehler zu verringern.

-   Stellen Sie sicher, dass das Kontingent Ihres Abonnements ausreicht, um den gesamten Prozess auszuführen. Während des Prozesses werden neue virtuelle Computer zugeordnet. Es ergibt sich die gleiche Anzahl von VMs wie vorher, aber Sie sollten trotzdem einplanen, dass während des Vorgangs ggf. weitere VMs erstellt werden.

-   Systemupdates oder geplante Tasks sind nicht vorgesehen.

-   Richten Sie ein gestaffeltes Upgrade in einem Cluster ein, für den die gleichen Werte wie für den Produktionscluster konfiguriert sind, und testen Sie das Upgrade dort, bevor Sie es im Produktionscluster ausführen.

### <a name="use-the-upgrade-command"></a>Verwenden des „upgrade“-Befehls

Sie müssen den „upgrade“-Befehl der AKS-Engine verwenden, wie dies im Artikel [Aktualisieren eines Kubernetes-Clusters in Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-upgrade) beschrieben ist.

### <a name="upgrade-interruptions"></a>Unterbrechungen des Upgrades

Es kann auch vorkommen, dass das Upgrade des Clusters aufgrund von unerwarteten Faktoren unterbrochen wird. Eine Unterbrechung kann auftreten, wenn die AKS-Engine einen Fehler meldet oder falls während des Ausführungsprozesses der AKS-Engine etwas passiert. Untersuchen Sie die Ursache der Unterbrechung, beseitigen Sie sie, und führen Sie den gleichen „upgrade“-Befehl erneut aus, um den Upgradevorgang fortzusetzen. Da der Befehl **upgrade** idempotent ist, sollte das Upgrade des Clusters nach dem erneuten Ausführen des Befehls fortgesetzt werden. Normalerweise führen Unterbrechungen dazu, dass sich der benötigte Zeitraum für das Update erhöht, aber die Durchführung sollte davon nicht beeinträchtigt werden.

### <a name="estimated-upgrade-time"></a>Geschätzte Upgradedauer

Die geschätzte Dauer beträgt zwischen 12 und 15 Minuten pro VM im Cluster. Bei einem Cluster mit 20 Knoten kann ein Upgrade beispielsweise ungefähr fünf (5) Stunden dauern.

## <a name="download-new-image-and-aks-engine"></a>Herunterladen eines neuen Images und der AKS-Engine

Laden Sie die neuen Versionen des AKS Base Ubuntu-Images und der AKS-Engine herunter.

Wie in der Dokumentation zur AKS-Engine für Azure Stack Hub beschrieben, werden für die Bereitstellung eines Kubernetes-Clusters zwei Komponenten benötigt: 
- Binärdatei für AKS-Engine
- AKS Base Ubuntu 16.04-LTS Image Distro

Neue Versionen hierfür sind unter diesem Update verfügbar:

-   Der Azure Stack Hub-Operator muss ein neues AKS Base Ubuntu-Image herunterladen:

    -   Name: `AKS Base Ubuntu 16.04-LTS Image Distro, March 2020`
    -   Version: `2020.03.19`
    -   Befolgen Sie die Anweisungen im Artikel [Hinzufügen von Voraussetzungen für die Azure Kubernetes Services-Engine (AKS) zum Azure Stack Hub-Marketplace](https://docs.microsoft.com/azure-stack/operator/azure-stack-aks-engine).

-   Der Administrator des Kubernetes-Clusters muss die neue Version 0.48.0 der AKS-Engine herunterladen. Weitere Informationen finden Sie im Artikel [Installieren der AKS-Engine unter Linux in Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-deploy-linux). Sie können den gleichen Prozess wie bei der ersten Installation des Clusters verwenden. Beim Update wird die vorherige Binärdatei überschrieben. Wenn Sie das Skript `get-akse.sh` verwendet haben, führen Sie beispielsweise die Schritte aus, die im Artikel [Installieren in einer verbundenen Umgebung](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-deploy-linux#install-in-a-connected-environment) beschrieben sind. Für die Installation auf einem Windows-System gilt der gleiche Prozess ([Installieren der AKS-Engine unter Windows in Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-deploy-windows)).

## <a name="kubernetes-version-upgrade-path"></a>Upgradepfad für Kubernetes-Version

Sie finden die aktuelle Version und die Upgradeversion jeweils in der folgenden Tabelle für Azure Stack Hub. Verwenden Sie nicht den Befehl `get-versions` der AKS-Engine, da er auch die in der globalen Azure-Umgebung unterstützten Versionen umfasst. Die folgende Tabelle mit den Versionen und Upgrades gilt für den AKS-Engine-Cluster in Azure Stack Hub.

| **Aktuelle Version** | **Upgrade verfügbar** |
| --------------------| ----------------------|
| 1.14.7 | 1.15.10 |
| 1.14.8 | 1.15.10 |
| 1.15.4 | 1.15.10 |
| 1.15.5 | 1.15.10 |

## <a name="whats-new"></a>Neues 

-   Unterstützung für Kubernetes-Version 1.15.10 ([\#2834](https://github.com/Azure/aks-engine/issues/2834)). Achten Sie beim Bereitstellen eines neuen Clusters darauf, dass Sie in der JSON-Datei Ihres API-Modells (AKS-Clusterdefinitionsdatei) die Versionsnummer und die Nummer der Nebenversion für das Release angeben. Ein Beispiel finden Sie unter [kubernetes-azurestack.json](https://raw.githubusercontent.com/Azure/aks-engine/master/examples/azure-stack/kubernetes-azurestack.json):

    - `"orchestratorRelease": "1.15`,

    - `"orchestratorVersion": "1.15.10"`

    > [!Note]  
    > Falls die Kubernetes-Version in der JSON-Datei des API-Modells nicht explizit angegeben ist, wird Version `1.15` verwendet ([\#2932](https://github.com/Azure/aks-engine/issues/2932)). Für „orchestratorVersion“ wird dann standardmäßig ` 1.15.11` verwendet, und dies führt während der Bereitstellung des Clusters zu einem Fehler.

-   Bei Version 0.43.1 der AKS-Engine passen die Standardeinstellungen für die Häufigkeit, mit der der Cloudanbieter die Steuerungsschleife und andere Aufgaben durchführt, nicht gut zu den Schwellenwerten für eingehende Anforderungen, die von Azure Stack Hub Resource Manager verwendet werden. Bei diesem Update werden die Standardeinstellungen für Azure Stack Hub geändert, um die Wiederholungslast für Azure Stack Hub Resource Manager zu reduzieren ([\#2861](https://github.com/Azure/aks-engine/issues/2861)).

-   Die neuen Überprüfungsschritte in der AKS-Engine führen entweder zur Beendigung der Ausführung oder zur Anzeige von Warnungen, wenn die JSON-Datei des API-Modells Eigenschaften enthält, die von Azure Stack Hub nicht unterstützt werden ([\#2717](https://github.com/Azure/aks-engine/issues/2717)).

-   Mit einer neuen Verifizierungsmethode überprüft die AKS-Engine die Verfügbarkeit der Version des AKS-Basisimages, das für die ausgeführte Version der AKS-Engine benötigt wird ([\#2342](https://github.com/Azure/aks-engine/issues/2342)). Dies erfolgt nach der Analyse der API-Modelldatei und vor dem Aufrufen von Azure Stack Hub Resource Manager.

-   Mit der neuen Option „--control-plane-only“ der AKS-Engine für den „upgrade“-Befehl kann der Benutzer die Upgradevorgänge so ausrichten, dass sie nur für die virtuellen Mastercomputer erfolgen ([\#2635](https://github.com/Azure/aks-engine/issues/2635)).

-   Es wird ein Update auf Linux-Kernel-Version 4.15.0-1071-azure für Ubuntu 16.04-LTS durchgeführt. Weitere Informationen finden Sie unter [Package: linux-image-4.15.0-1071-azure (4.15.0-1071.76) \[security\]](https://packages.ubuntu.com/xenial/linux-image-4.15.0-1071-azure).

-   Neue Hyperkube-Updates zur Unterstützung der Kubernetes-Versionen 1.14.8 und 1.15.10.

-   kubectl-Update zur Anpassung der Version von Kubernetes für den Cluster. Diese Komponente ist auf den Masterknoten des Kubernetes-Clusters verfügbar. Sie können sie ausführen, indem Sie per SSH auf einen Master zugreifen.

-   Updates für das Azure Container Monitor-Add-On mit dem aktuellen [Release vom Februar 2020](https://github.com/microsoft/Docker-Provider/blob/ci_feature_prod/README.md) ([\#2850](https://github.com/Azure/aks-engine/issues/2850)).

-   Upgrade von `coredns` auf Version 1.6.6 ([\#2555](https://github.com/Azure/aks-engine/issues/2555)).

-   Upgrade von `etcd` auf Version 3.3.18 ([\#2462](https://github.com/Azure/aks-engine/issues/2462)).

-   Upgrade von `moby` auf Version 3.0.11 ([\#2887](https://github.com/Azure/aks-engine/issues/2887)).

-   Mit diesem Release der AKS-Engine wird die Abhängigkeit von `k8s.gcr.io` beendet und beim Erstellen von Images nun die offizielle Vorgehensweise mit `Kubernetes MCR registry @ mcr.microsoft.com` verwendet ([\#2722](https://github.com/Azure/aks-engine/issues/2722)).

## <a name="known-issues"></a>Bekannte Probleme

-  Die parallele Bereitstellung mehrerer Kubernetes-Dienste in einem Cluster kann zu einem Fehler in der grundlegenden Konfiguration für den Lastenausgleich führen. Führen Sie nach Möglichkeit nur die Bereitstellung für jeweils einen Dienst durch.

-  Wenn der Befehl „get-versions“ der AKS-Engine ausgeführt wird, werden Informationen erzeugt, die für Azure und für Azure Stack Hub gelten. Es kann aber nicht explizit ermittelt werden, welche Informationen für Azure Stack Hub bestimmt sind. Verwenden Sie diesen Befehl nicht, um herauszufinden, welche Versionen bereit für ein Upgrade sind. Nutzen Sie die oben angegebene Referenztabelle für Upgrades.

-  Das AKS-Engine-Tool ist ein gemeinsames Quellcoderepository für Azure und Azure Stack Hub. Das Lesen der vielen Versionshinweise und Pull Requests kann bei Ihnen ggf. den Eindruck erwecken, dass das Tool auch noch andere Versionen von Kubernetes und Betriebssystemplattformen als oben angegeben unterstützt. Ignorieren Sie diese Informationen, und verwenden Sie die obige Versionstabelle als offiziellen Leitfaden für dieses Update.

## <a name="reference"></a>Verweis

Hier ist eine Liste mit den behobenen Fehlern und eine Auflistung aller Versionshinweise von Version 0.44.0 bis 0.48.0 angegeben. Beachten Sie, dass die Auflistung der Versionen sowohl für Azure als auch für Azure Stack Hub gilt.

### <a name="bug-fixes"></a>Behebung von Programmfehlern

-   Fehlende Anführungszeichen für `userAssignedIdentityId` in `azure.json` für Windows ([\#2327](https://github.com/Azure/aks-engine/issues/2327))

-   `update config`-Add-Ons nur für Upgrade ([\#2282](https://github.com/Azure/aks-engine/issues/2282))

-   Timeoutverlängerung für Abruf der Verwaltungs-IP auf Windows-Knoten ([\#2284](https://github.com/Azure/aks-engine/issues/2284))

-   1\.0.28 Azure CNI-ZIP-Datei für Windows-VHD hinzugefügt ([\#2268](https://github.com/Azure/aks-engine/issues/2268))

-   Standardreihenfolge für Festlegung von IPAddressCount korrigiert ([\#2358](https://github.com/Azure/aks-engine/issues/2358))

-   Update auf Verwendung von nur einer omsagent-YAML-Datei für alle k8s-Versionen, um manuelle Fehler zu vermeiden und die Verwaltung zu vereinfachen ([\#2692](https://github.com/Azure/aks-engine/issues/2692))

### <a name="release-notes"></a>Versionshinweise

Dies sind alle Versionshinweise, die für Azure und Azure Stack Hub gemeinsam gelten.

-   https://github.com/Azure/aks-engine/releases/tag/v0.44.0
-   https://github.com/Azure/aks-engine/releases/tag/v0.44.1
-   https://github.com/Azure/aks-engine/releases/tag/v0.44.2
-   https://github.com/Azure/aks-engine/releases/tag/v0.45.0
-   https://github.com/Azure/aks-engine/releases/tag/v0.46.0
-   https://github.com/Azure/aks-engine/releases/tag/v0.46.1
-   https://github.com/Azure/aks-engine/releases/tag/v0.46.2
-   https://github.com/Azure/aks-engine/releases/tag/v0.46.3
-   https://github.com/Azure/aks-engine/releases/tag/v0.47.0
-   https://github.com/Azure/aks-engine/releases/tag/v0.48.0

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die [AKS-Engine in Azure Stack Hub](azure-stack-kubernetes-aks-engine-overview.md).
