---
title: Herstellen einer Verbindung mit Azure Kubernetes Service auf einem Azure Stack HCI-Cluster mit Azure Arc für Kubernetes
description: Herstellen einer Verbindung mit Azure Kubernetes Service auf einem Azure Stack HCI-Cluster mit Azure Arc für Kubernetes
author: abha
ms.topic: how-to
ms.date: 09/22/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: 24dc2efdc591404db1bbfc30cf9c1bc83e2ed356
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90949314"
---
# <a name="connect-an-azure-kubernetes-service-on-azure-stack-hci-cluster-to-azure-arc-for-kubernetes"></a>Herstellen einer Verbindung mit Azure Kubernetes Service auf einem Azure Stack HCI-Cluster mit Azure Arc für Kubernetes

Wenn eine Azure Kubernetes Service-Instanz auf dem Azure Stack HCI-Cluster an Azure Arc angefügt wird, wird sie im Azure-Portal angezeigt. Er verfügt dann über eine Azure Resource Manager-ID und eine verwaltete Identität. Cluster sind an Azure-Standardabonnements angefügt, befinden sich in einer Ressourcengruppe und können wie jede andere Azure-Ressource Tags erhalten.

Zum Herstellen einer Verbindung zwischen einem Kubernetes-Cluster und Azure muss ein Clusteradministrator entsprechende Agents bereitstellen. Diese Agents werden in einem Kubernetes-Namespace namens `azure-arc` ausgeführt und sind Kubernetes-Standardbereitstellungen. Die Agents sind verantwortlich für die Konnektivität mit Azure, die Erfassung von Azure Arc-Protokollen und -Metriken sowie die Überwachung von Konfigurationsanforderungen.

Kubernetes mit Azure Arc-Unterstützung unterstützt branchenübliches SSL zum Schützen von Daten während der Übertragung. Die Daten werden außerdem im Ruhezustand verschlüsselt in einer Azure Cosmos DB-Datenbank gespeichert, um ihre Vertraulichkeit zu gewährleisten.

Die folgenden Schritte bieten eine exemplarische Vorgehensweise zum Durchführen des Onboardings für eine Azure Kubernetes Service-Instanz auf Azure Stack HCI-Clustern mit Azure Arc. **Sie können diese Schritte überspringen, wenn Sie für Ihren Kubernetes-Cluster bereits über das Windows Admin Center in Azure Arc das Onboarding durchgeführt haben.**

## <a name="before-you-begin"></a>Voraussetzungen

Vergewissern Sie sich, dass die folgenden Anforderungen erfüllt sind:

* Ein Azure Kubernetes Service in einem Azure Stack HCI-Cluster mit mindestens einem Linux-Workerknoten, der aktiv ist und ausgeführt wird. 

* Sie benötigen eine kubeconfig-Datei, um auf den Cluster und die Rolle des Clusteradministrators auf dem Cluster zuzugreifen, um Arc-fähige Kubernetes-Agents bereitstellen zu können.
* Das Azure Kubernetes Service in Azure Stack HCI PowerShell-Modul ist installiert.
* Azure CLI, Version 2.3 und höher, ist zum Installieren der Azure Arc-fähigen Kubernetes CLI-Erweiterungen erforderlich. [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli?view=azure-cli-latest) Sie können auch auf die neueste Version aktualisieren, um sicherzustellen, dass Sie über Azure CLI, Version 2.3 oder höher, verfügen.
* Ein Azure-Abonnement, bei dem Ihnen die Rolle „Besitzer“ oder „Mitwirkender“ zugeordnet ist. 
* Führen Sie die Befehle in diesem Dokument in einem PowerShell-Fenster mit Administratorrechten aus.


## <a name="network-requirements"></a>Netzwerkanforderungen

Azure Arc-Agents müssen über die folgenden Protokolle/Ports/ausgehenden URLs verfügen, um zu funktionieren.

* TCP auf Port 443 --> `https://:443`
* TCP auf Port 9418 --> `git://:9418`

| Endpunkt (DNS)                                                                                               | BESCHREIBUNG                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `https://management.azure.com`                                                                                 | Erforderlich, damit der Agent eine Verbindung mit Azure herstellen und den Cluster registrieren kann                                                        |
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com` | Datenebenen-Endpunkt, über den der Agent Statusinformationen mithilfe von Push übermitteln und Konfigurationsinformationen abrufen kann                                      |
| `https://docker.io`                                                                                            | Erforderlich zum Pullen von Containerimages                                                                                         |
| `https://github.com`, git://github.com                                                                         | Beispiel-GitOps-Repositorys werden auf GitHub gehostet. Für den Konfigurations-Agent ist eine Verbindung mit dem von Ihnen angegebenen Git-Endpunkt erforderlich. |
| `https://login.microsoftonline.com`                                                                            | Erforderlich zum Abrufen und Aktualisieren von Azure Resource Manager-Token                                                                                    |
| `https://azurearcfork8s.azurecr.io`                                                                            | Erforderlich zum Pullen von Containerimages für Azure Arc-Agents                                                                  |
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`                                                                            |  Erforderlich zum Pullen vom System zugewiesener Zertifikate für verwaltete Identitäten                                                                  |

## <a name="step-1-log-in-to-azure"></a>Schritt 1: Anmelden an Azure

Melden Sie sich bei Azure an, und legen Sie nach der Anmeldung ein Azure-Abonnement, bei dem Ihnen die Rolle „Besitzer“ oder „Mitwirkender“ zugeordnet ist, als Ihr Standardabonnement fest.

```console
az login
az account set --subscription "00000000-aaaa-bbbb-cccc-000000000000"
```

## <a name="step-2-register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Schritt 2: Registrieren der beiden Anbieter für Kubernetes mit Azure Arc-Aktivierung:

Sie können diesen Schritt überspringen, wenn Sie die beiden Anbieter bereits für den Azure Arc-fähigen Kubernetes-Dienst in Ihrem Abonnement registriert haben. Die Registrierung ist ein asynchroner Prozess und muss einmal pro Abonnement erfolgen. Sie kann ca. 10 Minuten dauern. 

```console
az provider register --namespace Microsoft.Kubernetes
az provider register --namespace Microsoft.KubernetesConfiguration
```

Mit den folgenden Befehlen können Sie überprüfen, ob Sie registriert sind:

```console
az provider show -n Microsoft.Kubernetes -o table
az provider show -n Microsoft.KubernetesConfiguration -o table
```

## <a name="step-3-create-a-resource-group"></a>Schritt 3: Erstellen einer Ressourcengruppe

Sie benötigen eine Ressourcengruppe, die die verbundene Clusterressource enthalten soll. Sie können eine vorhandene Ressourcengruppe an den Standorten USA (Osten) oder Europa (Westen) verwenden. Wenn Sie über keine bestehende Ressourcengruppe am Standort USA (Osten) oder Europa (Westen) verfügen, erstellen Sie mithilfe des folgenden Befehls eine neue Ressourcengruppe:

```console
az group create --name AzureArcTest -l EastUS -o table
```

## <a name="step-4-create-a-new-service-principal"></a>Schritt 4: Erstellen eines neuen Dienstprinzipals


Sie können diesen Schritt überspringen, wenn Sie bereits einen Dienstprinzipal mit der Rolle `contributor` erstellt haben und die Werte für App-ID, Kennwort und Mandant des Dienstprinzipals kennen.

Erstellen Sie einen neuen Dienstprinzipal mit einem aussagekräftigen Namen. Dieser Name muss in Ihrem Azure Active Directory-Mandanten eindeutig sein: Standardmäßig hat ein Dienstprinzipal die Rolle `Contributor`. Diese Rolle besitzt uneingeschränkte Berechtigungen für Lese- und Schreibvorgänge in einem Azure-Konto. Sie können diesen Dienstprinzipal auch wiederverwenden, um das Onboarding von mehreren Clustern in Azure Arc durchzuführen. Legen Sie den Bereich Ihres Dienstprinzipals auf *Abonnements/Ressourcengruppe* fest. *Stellen Sie sicher, dass Sie die Werte für App-ID, Kennwort und Mandant des Dienstprinzipals speichern, da Sie diese Angaben in den folgenden Schritten benötigen werden.*

```console
az ad sp create-for-RBAC --name "azure-arc-for-k8s" --scope /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}
```

**Ausgabe:**

```
{
  "appId": "00000000-0000-0000-0000-000000000000",
  "displayName": "azure-arc-for-k8s",
  "name": "https://azure-arc-for-k8s",
  "password": "aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee",
  "tenant": "ffffffff-gggg-hhhh-iiii-jjjjjjjjjjjj"
}
```
## <a name="step-5-save-service-principal-details"></a>Schritt 5: Speichern von Details zu Dienstprinzipalen
Speichern Sie die Werte für App-ID, Kennwort und Mandant des erstellten Dienstprinzipals sowie den Clusternamen, die Azure-Abonnement-ID, den Ressourcengruppennamen und den Standort in PowerShell-Variablen. Dadurch wird sichergestellt, dass Sie die Details in anderen Tutorials wiederverwenden können. Stellen Sie sicher, dass Sie diese Werte auch in einem Editor speichern, falls Sie Ihre PowerShell-Sitzung schließen möchten.

```PowerShell
$clusterName = #<name of your Kubernetes cluster>
$resourceGroup = #<Azure resource group to store your connected Kubernetes cluster in Azure Arc>
$location = #<Azure resource group location. This can only be eastus or westeurope for Azure Arc for Kubernetes>
$subscriptionId = #<Azure subscription Id>
$appId = #<appID from the service principal created above>
$password = #<password from the service principal created above>
$tenant = #<tenant from the service principal created above>
```
Stellen Sie sicher, dass Sie den Variablen die richtigen Werte zugewiesen haben, indem Sie Folgendes ausführen:

```PowerShell
echo $clusterName 
echo $resourceGroup
echo $location 
echo $subscriptionId 
echo $appId 
echo $password 
echo $tenant 
```

## <a name="step-6-connect-to-azure-arc-using-service-principal-and-the-aks-hci-powershell-module"></a>Schritt 6: Herstellen einer Verbindung mit Azure Arc mithilfe des Dienstprinzipals und des AKS-HCI-PowerShell-Moduls

Als nächstes werden wir unseren Kubernetes-Cluster mithilfe des Dienstprinzipals und des AKS-HCI-PowerShell-Moduls mit Azure verbinden. In diesem Schritt werden Azure Arc-Agents für Kubernetes im Namespace `azure-arc` bereitgestellt.

Verweisen Sie auf den neu erstellten Dienstprinzipal, und führen Sie den Befehl `Install-AksHciArcOnboarding` aus, der im AKS-HCI-PowerShell-Modul verfügbar ist.

```PowerShell
Install-AksHciArcOnboarding -clusterName $clusterName -resourcegroup $resourceGroup -location $location -subscriptionid $subscriptionId -clientid $appId -clientsecret $password -tenantid $tenant
```
## <a name="verify-connected-cluster"></a>Überprüfen des verbundenen Clusters

Sie können Ihre Kubernetes-Clusterressource im [Azure-Portal](https://portal.azure.com/) anzeigen. Nachdem Sie das Portal in Ihrem Browser geöffnet haben, navigieren Sie anhand der früher im PowerShell-Befehl `Install-AksHciArcOnboarding` verwendeten Eingaben für Ressourcenname und Ressourcengruppenname zu der Ressourcengruppe und der Azure Arc-fähigen Kubernetes-Ressource.

> [!NOTE]
> Nach dem Onboarding des Clusters dauert es ungefähr 5 bis 10 Minuten, bis die Clustermetadaten (Clusterversion, Agent-Version, Anzahl der Knoten) auf der Übersichtsseite der Kubernetes-Ressource, für die Azure Arc aktiviert ist, im Azure-Portal angezeigt wird.

Informationen zum Löschen Ihres Clusters oder zum Verbinden Ihres Clusters, falls er sich hinter einem ausgehenden Proxyserver befindet, finden Sie unter [Herstellen einer Verbindung für einen Azure Arc-fähigen Kubernetes-Cluster](/azure/azure-arc/kubernetes/connect-cluster).

## <a name="azure-arc-agents-for-kubernetes"></a>Azure Arc-Agents für Kubernetes

Kubernetes mit Azure Arc-Aktivierung stellt einige Operatoren im Namespace `azure-arc` bereit. So können Sie diese Bereitstellungen und Pods anzeigen:

```console
kubectl -n azure-arc get deployments,pods
```

Kubernetes mit Azure Arc-Aktivierung besteht aus einigen Agents (Operatoren), die in Ihrem Cluster ausgeführt und im Namespace `azure-arc` bereitgestellt werden.

* `deployment.apps/config-agent` prüft, ob auf den verbundenen Cluster Ressourcen für die Konfiguration der Quellcodeverwaltung angewendet werden und aktualisiert den Konformitätszustand.
* `deployment.apps/controller-manager` ist ein Operator für Operatoren und koordiniert Interaktionen zwischen Azure Arc-Komponenten.
* `deployment.apps/metrics-agent` erfasst Metriken für andere Arc-Agents, um sicherzustellen, dass diese Agents eine optimale Leistung aufweisen.
* `deployment.apps/cluster-metadata-operator` erfasst Clustermetadaten: Clusterversion, Knotenanzahl und Version des Azure Arc-Agents.
* `deployment.apps/resource-sync-agent` synchronisiert die oben erwähnten Clustermetadaten mit Azure.
* `deployment.apps/clusteridentityoperator`: Kubernetes mit Azure Arc-Aktivierung unterstützt derzeit systemseitig zugewiesene Identitäten. clusteridentityoperator verwaltet das von anderen Agents für die Kommunikation mit Azure verwendete MSI-Zertifikat (Managed Service Identity, Verwaltete Dienstidentität).
* `deployment.apps/flux-logs-agent` sammelt Protokolle von den im Rahmen der Konfiguration der Quellcodeverwaltung bereitgestellten Flux-Operatoren.

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden von GitOps für eine Konfiguration mit Azure Arc-Aktivierung (Vorschau)](/azure/azure-arc/kubernetes/use-gitops-connected-cluster)
* [Verwenden von Azure Policy zum Anwenden von Clusterkonfigurationen im großen Stil (Vorschau)](/azure/azure-arc/kubernetes/use-azure-policy)
* [Aktivieren von Azure Monitor auf einem Azure Arc-fähigen Kubernetes-Cluster](/azure/azure-monitor/insights/container-insights-enable-arc-enabled-clusters)
