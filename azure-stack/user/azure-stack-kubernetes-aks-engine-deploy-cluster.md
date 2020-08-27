---
title: Bereitstellen eines Kubernetes-Cluster mit der AKS-Engine in Azure Stack Hub
description: Erfahren Sie, wie Sie einen Kubernetes-Cluster in Azure Stack Hub von einer Client-VM bereitstellen, auf der die AKS-Engine ausgeführt wird.
author: mattbriggs
ms.topic: article
ms.date: 07/07/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 07/07/2020
ms.openlocfilehash: e791f05f76831e9448c4eba796ca8a12d631b9cd
ms.sourcegitcommit: a5d3cbe1a10c2a63de95b9e72391dd83473ee299
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88919997"
---
# <a name="deploy-a-kubernetes-cluster-with-the-aks-engine-on-azure-stack-hub"></a>Bereitstellen eines Kubernetes-Cluster mit der AKS-Engine in Azure Stack Hub

Sie können einen Kubernetes-Cluster in Azure Stack Hub von einer Client-VM bereitstellen, auf der die AKS-Engine ausgeführt wird. In diesem Artikel untersuchen wir das Schreiben einer Clusterspezifikation, indem wir einen Cluster mit der `apimodel.json`-Datei bereitstellen und Ihren Cluster durch Bereitstellen von MySQL mit Helm überprüfen.

## <a name="define-a-cluster-specification"></a>Definieren einer Clusterspezifikation

Sie können eine Clusterspezifikation in einer Dokumentdatei mithilfe des JSON-Formats namens [API-Modell](https://github.com/Azure/aks-engine/blob/master/docs/topics/architecture.md#architecture-diagram) angeben. Die AKS-Engine verwendet eine Clusterspezifikation im API-Modell, um den Cluster zu erstellen. 

### <a name="update-the-api-model"></a>Aktualisieren des API-Modells

Dieser Abschnitt zeigt das Erstellen eines API-Modells für Ihren Cluster.

1.  Beginnen Sie mit der Verwendung des [Beispiels](https://github.com/Azure/aks-engine/tree/master/examples/azure-stack) einer API-Modelldatei für Azure Stack Hub, und erstellen Sie eine lokale Kopie für Ihre Bereitstellung. Führen Sie auf dem Computer, auf dem Sie die AKS-Engine installiert haben, Folgendes aus:

    ```bash
    curl -o kubernetes-azurestack.json https://raw.githubusercontent.com/Azure/aks-engine/master/examples/azure-stack/kubernetes-azurestack.json
    ```

    > [!NOTE]  
    > Wenn Sie die Verbindungen getrennt haben, können Sie die Datei herunterladen und manuell auf den getrennten Computer kopieren, auf dem Sie sie bearbeiten möchten. Sie können die Datei mithilfe von Tools wie [PuTTY oder WinSCP](https://www.suse.com/documentation/opensuse103/opensuse103_startup/data/sec_filetrans_winssh.html) auf Ihren Linux-Computer kopieren.

2.  Wenn Sie das API-Modell in einem Editor öffnen möchten, können Sie nano verwenden:

    ```bash
    nano ./kubernetes-azurestack.json
    ```

    > [!NOTE]  
    > Wenn nano nicht installiert ist, können Sie nano auf Ubuntu installieren: `sudo apt-get install nano`.

3.  Suchen Sie in der Datei „kubernetes-azurestack.json“ nach „orchestratorRelease“ und „orchestratorVersion“. Wählen Sie eine der unterstützten Kubernetes-Versionen aus. Verwenden Sie beispielsweise für `orchestratorRelease` 1.14 oder 1.15 und für `orchestratorVersion` 1.14.7 oder 1.15.10. Geben Sie `orchestratorRelease` als x.xx und „orchestratorVersion“ als x.xx.x an. Eine Liste der aktuellen Versionen finden Sie unter [Unterstützte AKS-Engine-Versionen](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-aks-engine-versions).

4.  Suchen Sie nach `customCloudProfile`, und geben Sie die URL für das Mandantenportal an. Beispiel: `https://portal.local.azurestack.external`. 

5. Fügen Sie bei Verwendung von AD FS `"identitySystem":"adfs"` hinzu. Beispiel:

    ```JSON  
        "customCloudProfile": {
            "portalURL": "https://portal.local.azurestack.external",
            "identitySystem": "adfs"
        },
    ```

    > [!NOTE]  
    > Wenn Sie Azure AD für Ihr Identitätssystem verwenden, müssen Sie das Feld **identitySystem** nicht hinzufügen.

6. Suchen Sie nach `portalURL`, und geben Sie die URL für das Mandantenportal an. Beispiel: `https://portal.local.azurestack.external`.

7.  Legen Sie in `masterProfile` die folgenden Felder fest:

    | Feld | BESCHREIBUNG |
    | --- | --- |
    | dnsPrefix | Geben Sie eine eindeutige Zeichenfolge ein, die zur Identifizierung des Hostnamens von virtuellen Computern dienen soll. Beispielsweise einen Namen, der auf dem Namen der Ressourcengruppe basiert. |
    | count |  Geben Sie die Anzahl der Masters ein, die für die Bereitstellung vorgesehen sind. Der minimale Wert für eine Hochverfügbarkeitsbereitstellung beträgt 3, aber 1 ist für Bereitstellungen ohne Hochverfügbarkeit zulässig. |
    | vmSize |  Geben Sie [eine Größe ein, die von Azure Stack Hub unterstützt wird](./azure-stack-vm-sizes.md), z. B. `Standard_D2_v2`. |
    | distro | Geben Sie `aks-ubuntu-16.04` ein. |

8.  Aktualisieren Sie in `agentPoolProfiles` Folgendes:

    | Feld | BESCHREIBUNG |
    | --- | --- |
    | count | Geben Sie die Anzahl der Agents ein, die für die Bereitstellung vorgesehen sind. Pro Abonnement können maximal 50 Knoten verwendet werden. Wenn Sie mehr als einen Cluster pro Abonnement bereitstellen, stellen Sie sicher, dass die Gesamtanzahl der Agents 50 nicht überschreitet. Stellen Sie sicher, dass Sie die Konfigurationselemente aus der [JSON-Datei für das API-Beispielmodell](https://github.com/Azure/aks-engine/blob/master/examples/azure-stack/kubernetes-azurestack.json) verwenden.  |
    | vmSize | Geben Sie [eine Größe ein, die von Azure Stack Hub unterstützt wird](./azure-stack-vm-sizes.md), z. B. `Standard_D2_v2`. |
    | distro | Geben Sie `aks-ubuntu-16.04` ein. |




9.  Aktualisieren Sie in `linuxProfile` Folgendes:

    | Feld | BESCHREIBUNG |
    | --- | --- |
    | adminUsername | Geben Sie den VM-Administratorbenutzernamen ein. |
    | ssh | Geben Sie den öffentlichen Schlüssel ein, der für die SSH-Authentifizierung bei virtuellen Computern verwendet wird. Verwenden Sie `ssh-rsa` und dann den Schlüssel. Anweisungen zum Erstellen eines öffentlichen Schlüssels finden Sie unter [Erstellen eines SSH-Schlüssels für Linux in Azure Stack Hub](create-ssh-key-on-windows.md). |

    Wenn Sie die Bereitstellung in einem benutzerdefinierten virtuellen Netzwerk ausführen möchten, finden Sie Anweisungen zum Ermitteln und Hinzufügen der erforderlichen Schlüssel und Werte zu den entsprechenden Arrays im API-Modell unter [Bereitstellen eines Kubernetes-Clusters zu einem benutzerdefinierten virtuellen Netzwerk in Azure Stack Hub](kubernetes-aks-engine-custom-vnet.md).

    > [!NOTE]  
    > Mit der AKS-Engine für Azure Stack Hub können Sie keine eigenen Zertifikate für die Erstellung des Clusters bereitstellen.

### <a name="more-information-about-the-api-model"></a>Weitere Informationen zum API-Modell

- Eine vollständige Referenz aller verfügbaren Optionen im API-Modell finden Sie in den [Clusterdefinitionen](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md).  
- Ausführliche Informationen zu bestimmten Optionen für Azure Stack Hub finden Sie in den [Einzelheiten zur Azure Stack Hub-Clusterdefinition](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#cluster-definition-aka-api-model).  

## <a name="deploy-a-kubernetes-cluster"></a>Bereitstellen eines Kubernetes-Clusters

Nachdem Sie alle erforderlichen Werte in Ihrem API-Modell gesammelt haben, können Sie Ihren Cluster erstellen. Sie sollten jetzt Folgendes tun:

Bitten Sie Ihren Azure Stack Hub-Operator um Folgendes:

- Überprüfen der Integrität des Systems, schlagen Sie die Ausführung von `Test-AzureStack` und des Hardwareüberwachungstools Ihres OEM-Herstellers vor.
- Überprüfen der Systemkapazität einschließlich Ressourcen wie Arbeitsspeicher, Speicher und öffentlicher IP-Adressen.
- Geben Sie Details zu dem Kontingent an, das Ihrem Abonnement zugeordnet ist, damit Sie überprüfen können, ob noch genügend Kapazität für die Anzahl der zu verwendenden VMs vorhanden ist.

Fahren Sie mit der Bereitstellung eines Clusters fort:

1.  Überprüfen Sie die verfügbaren Parameter für die AKS-Engine in Azure Stack Hub-[CLI-Flags](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#cli-flags).

    | Parameter | Beispiel | BESCHREIBUNG |
    | --- | --- | --- |
    | azure-env | AzureStackCloud | Um der AKS-Engine mitzuteilen, dass die Zielplattform Azure Stack Hub ist, verwenden Sie `AzureStackCloud`. |
    | identity-system | adfs | Optional. Geben Sie Ihre Identitätsverwaltungslösung an, wenn Sie Active Directory-Verbunddienste (AD FS) nutzen. |
    | location | local | Der Regionsname für Ihre Azure Stack Hub-Instanz. Für das ASDK ist `local` für die Region festgelegt. |
    | resource-group | kube-rg | Geben Sie den Namen einer neuen Ressourcengruppe ein, oder wählen Sie eine vorhandene Ressourcengruppe aus. Der Ressourcenname muss alphanumerisch und in Kleinbuchstaben angegeben sein. |
    | api-model | ./kubernetes-azurestack.json | Pfad zur Clusterkonfigurationsdatei oder zum API-Modell. |
    | output-directory | kube-rg | Geben Sie den Namen des Verzeichnisses ein, in dem die Ausgabedatei `apimodel.json` sowie andere generierte Dateien enthalten sein sollen. |
    | client-id | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Geben Sie die Dienstprinzipal-GUID ein. Die als Anwendungs-ID identifizierte Client-ID, wenn Ihr Azure Stack Hub-Administrator den Dienstprinzipal erstellt hat. |
    | client-secret | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Geben Sie das Dienstprinzipalgeheimnis ein. Sie richten den geheimen Clientschlüssel beim Erstellen Ihres Diensts ein. |
    | subscription-id | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Geben Sie Ihre Abonnement-ID ein. Geben Sie ein Abonnement für den Mandanten an. Bereitstellungen für das administrative Abonnement werden nicht unterstützt.  Weitere Informationen finden Sie unter [Abonnieren von Angeboten](./azure-stack-subscribe-services.md#subscribe-to-an-offer). |

    Beispiel:

    ```bash  
    aks-engine deploy \
    --azure-env AzureStackCloud \
    --location <for asdk is local> \
    --resource-group kube-rg \
    --api-model ./kubernetes-azurestack.json \
    --output-directory kube-rg \
    --client-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --client-secret xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --subscription-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --identity-system adfs # required if using AD FS
    ```

2.  Wenn bei der Ausführung aus beliebigem Grund ein Fehler auftritt, nachdem das Ausgabeverzeichnis erstellt wurde, können Sie das Problem beheben und den Befehl erneut ausführen. Wenn Sie die Bereitstellung erneut ausführen und zuvor dasselbe Ausgabeverzeichnis verwendet haben, gibt die AKS-Engine eine Fehlermeldung zurück, die besagt, dass das Verzeichnis bereits vorhanden ist. Sie können das vorhandene Verzeichnis mit folgendem Flag überschreiben: `--force-overwrite`.

3.  Speichern Sie die Clusterkonfiguration der AKS-Engine an einem sicheren, verschlüsselten Speicherort.

    Suchen Sie die `apimodel.json`-Datei. Speichern Sie sie an einem sicheren Speicherort. Diese Datei wird in allen anderen AKS-Engine-Vorgängen als Eingabe verwendet.

    Die generierte `apimodel.json` enthält den Dienstprinzipal, das Geheimnis und den öffentlichen SSH-Schlüssel, die Sie im Eingabe-API-Modell verwenden. Außerdem verfügt sie über alle anderen Metadaten, die die AKS-Engine benötigt, um alle anderen Vorgänge auszuführen. Wenn Sie sie verlieren, kann die AKS-Engine den Cluster nicht konfigurieren.

    Die Geheimnisse sind **unverschlüsselt**. Bewahren Sie die Datei an einem verschlüsselten, sicheren Ort auf. 

## <a name="verify-your-cluster"></a>Überprüfen Ihres Clusters

Überprüfen Sie Ihren Cluster durch Bereitstellen von MySQL mit Helm.

1. Rufen Sie die öffentliche IP-Adresse eines Ihrer Masterknoten über das Azure Stack Hub-Portal ab.

2. Stellen Sie von einem Computer, der Zugriff auf Ihre Azure Stack Hub-Instanz hat, über SSH mit einem Client wie z. B. PuTTY oder MobaXterm eine Verbindung mit dem neuen Masterknoten her. 

3. Verwenden Sie für den SSH-Benutzernamen „azureuser“ und die Datei mit dem privaten Schlüssel des Schlüsselpaars, das Sie für die Bereitstellung des Clusters angegeben haben.

4. Führen Sie die folgenden Befehle aus, um eine Beispielbereitstellung eines Redis-Masters (nur bei verbundenen Stempeln) zu erstellen:

   ```bash
   kubectl apply -f https://k8s.io/examples/application/guestbook/redis-master-deployment.yaml
   ```

    1. Fragen Sie die Liste der Pods ab:

       ```bash
       kubectl get pods
       ```

    2. Die Antwort sollte etwa wie folgt aussehen:

       ```shell
       NAME                            READY     STATUS    RESTARTS   AGE
       redis-master-1068406935-3lswp   1/1       Running   0          28s
       ```

    3. Zeigen Sie die Bereitstellungsprotokolle an:

       ```shell
       kubectl logs -f <pod name>
       ```

    Führen Sie für eine vollständige Bereitstellung einer PHP-Beispiel-App, die den Redis-Master enthält, [diese Schritte](https://kubernetes.io/docs/tutorials/stateless-application/guestbook/) aus.

5. Bei einem getrennten Stempel sollten die folgenden Befehle ausreichen:

    1. Überprüfen Sie zunächst, ob die Clusterendpunkte ausgeführt werden:

       ```bash
       kubectl cluster-info
       ```

       Die Ausgabe sollte in etwa wie folgt aussehen:

       ```shell
       Kubernetes master is running at https://democluster01.location.domain.com
       CoreDNS is running at https://democluster01.location.domain.com/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
       kubernetes-dashboard is running at https://democluster01.location.domain.com/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy
       Metrics-server is running at https://democluster01.location.domain.com/api/v1/namespaces/kube-system/services/https:metrics-server:/proxy
       ```

    2. Überprüfen Sie dann die Knotenzustände:

       ```bash
       kubectl get nodes
       ```

       Die Ausgabe sollte ähnlich der Folgenden aussehen:

       ```shell
       k8s-linuxpool-29969128-0   Ready      agent    9d    v1.15.5
       k8s-linuxpool-29969128-1   Ready      agent    9d    v1.15.5
       k8s-linuxpool-29969128-2   Ready      agent    9d    v1.15.5
       k8s-master-29969128-0      Ready      master   9d    v1.15.5
       k8s-master-29969128-1      Ready      master   9d    v1.15.5
       k8s-master-29969128-2      Ready      master   9d    v1.15.5
       ```

6. Führen Sie den folgenden Befehl aus, um die Redis-Pod-Bereitstellung aus dem vorherigen Schritt zu bereinigen:

    ```bash
    kubectl delete deployment -l app=redis
    ```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Problembehandlung bei der AKS-Engine in Azure Stack Hub](azure-stack-kubernetes-aks-engine-troubleshoot.md)
