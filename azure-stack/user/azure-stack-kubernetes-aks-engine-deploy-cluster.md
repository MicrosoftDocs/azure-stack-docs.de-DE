---
title: Bereitstellen eines Kubernetes-Cluster mit der AKS-Engine in Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen Kubernetes-Cluster in Azure Stack von einer Client-VM bereitstellen, auf der die AKS-Engine ausgeführt wird.
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
ms.date: 09/25/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/25/2019
ms.openlocfilehash: 957ea4bc52e6f629ffd7fbd06a14d8dc2fb85021
ms.sourcegitcommit: d967cf8cae320fa09f1e97eeb888e3db5b6e7972
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71279178"
---
# <a name="deploy-a-kubernetes-cluster-with-the-aks-engine-on-azure-stack"></a>Bereitstellen eines Kubernetes-Cluster mit der AKS-Engine in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Sie können einen Kubernetes-Cluster in Azure Stack von einer Client-VM bereitstellen, auf der die AKS-Engine ausgeführt wird. In diesem Artikel untersuchen wir das Schreiben einer Clusterspezifikation, indem wir einen Cluster mit der `apimodel.json`-Datei bereitstellen und Ihren Cluster durch Bereitstellen von MySQL mit Helm überprüfen.

## <a name="define-a-cluster-specification"></a>Definieren einer Clusterspezifikation

Sie können eine Clusterspezifikation in einer Dokumentdatei mithilfe des JSON-Formats namens [API-Modell](https://github.com/Azure/aks-engine/blob/master/docs/topics/architecture.md#architecture-diagram) angeben. Die AKS-Engine verwendet eine Clusterspezifikation im API-Modell, um den Cluster zu erstellen. 

### <a name="update-the-api-model"></a>Aktualisieren des API-Modells

Dieser Abschnitt zeigt das Erstellen eines API-Modells für Ihren Cluster.

1.  Beginnen Sie mit der Verwendung des [Beispiels](https://github.com/Azure/aks-engine/tree/master/examples/azure-stack) einer API-Modelldatei für Azure Stack, und erstellen Sie eine lokale Kopie für Ihre Bereitstellung. Führen Sie auf dem Computer, auf dem Sie die AKS-Engine installiert haben, Folgendes aus:

    ```bash
    curl -o kubernetes-azurestack.json https://raw.githubusercontent.com/Azure/aks-engine/master/examples/azure-stack/kubernetes-azurestack.json
    ```

    > [!Note]  
    > Wenn Sie die Verbindungen getrennt haben, können Sie die Datei herunterladen und manuell auf den getrennten Computer kopieren, auf dem Sie sie bearbeiten möchten. Sie können die Datei mithilfe von Tools wie [PuTTY oder WinSCP](https://www.suse.com/documentation/opensuse103/opensuse103_startup/data/sec_filetrans_winssh.html) auf Ihren Linux-Computer kopieren.

2.  Um sie in einem Editor zu öffnen, können Sie nano verwenden:

    ```bash
    nano ./kubernetes-azurestack.json
    ```

    > [!Note]  
    > Wenn nano nicht installiert ist, können Sie nano auf Ubuntu installieren: `sudo apt-get install nano`.

3.  Suchen Sie in der Datei „kubernetes-azurestack.json“ nach `orchestratorRelease`. Wählen Sie eine der unterstützten Kubernetes-Versionen aus. Beispiel: 1.11, 1.12, 1.13, 1.14. Die Versionen sind häufig Updates. Geben Sie die Version als „x.xx“ anstelle von „x.xx.x“ an. Eine Liste der aktuellen Versionen finden Sie unter [Supported Kubernetes Versions](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions) (Unterstützte Kubernetes-Versionen). Sie können die unterstützte Version ermitteln, indem Sie den folgenden AKS-Engine-Befehl ausführen:

    ```bash
    aks-engine get-versions
    ```

4.  Suchen Sie nach `portalURL`, und geben Sie die URL für das Mandantenportal an. Beispiel: `https://portal.local.azurestack.external`.

5.  Legen Sie im Array `masterProfile` die folgenden Felder fest:

    | Feld | BESCHREIBUNG |
    | --- | --- |
    | dnsPrefix | Geben Sie eine eindeutige Zeichenfolge ein, die zur Identifizierung des Hostnamens von virtuellen Computern dienen soll. Beispielsweise einen Namen, der auf dem Namen der Ressourcengruppe basiert. |
    | count |  Geben Sie die Anzahl der Masters ein, die für die Bereitstellung vorgesehen sind. Der minimale Wert für eine Hochverfügbarkeitsbereitstellung beträgt 3, aber 1 ist für Bereitstellungen ohne Hochverfügbarkeit zulässig. |
    | vmSize |  Geben Sie [eine Größe ein, die von Azure Stack unterstützt wird](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes), z. B. `Standard_D2_v2`. |
    | distro | Geben Sie `aks-ubuntu-16.04` ein. |

6.  Aktualisieren Sie im Array `agentPoolProfiles`:

    | Feld | BESCHREIBUNG |
    | --- | --- |
    | count | Geben Sie die Anzahl der Agents ein, die für die Bereitstellung vorgesehen sind. |
    | vmSize | Geben Sie [eine Größe ein, die von Azure Stack unterstützt wird](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes), z. B. `Standard_D2_v2`. |
    | distro | Geben Sie `aks-ubuntu-16.04` ein. |

7.  Aktualisieren Sie im Array `linuxProfile`:

    | Feld | BESCHREIBUNG |
    | --- | --- |
    | adminUsername | Geben Sie den VM-Administratorbenutzernamen ein. |
    | ssh | Geben Sie den öffentlichen Schlüssel ein, der für die SSH-Authentifizierung bei virtuellen Computern verwendet wird. |

### <a name="more-information-about-the-api-model"></a>Weitere Informationen zum API-Modell

- Eine vollständige Referenz aller verfügbaren Optionen im API-Modell finden Sie in den [Clusterdefinitionen](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md).  
- Ausführliche Informationen zu bestimmten Optionen für Azure Stack finden Sie in den [Einzelheiten zur Azure Stack-Clusterdefinition](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#cluster-definition-aka-api-model).  

## <a name="deploy-a-kubernetes-cluster"></a>Bereitstellen eines Kubernetes-Clusters

Nachdem Sie alle erforderlichen Werte in Ihrem API-Modell gesammelt haben, können Sie Ihren Cluster erstellen. Sie sollten jetzt Folgendes tun:

Bitten Sie Ihren Azure Stack-Operator um Folgendes:

- Überprüfen der Integrität des Systems, schlagen Sie die Ausführung von `Test-AzureStack` und des Hardwareüberwachungstools Ihres OEM-Herstellers vor.
- Überprüfen der Systemkapazität einschließlich Ressourcen wie Arbeitsspeicher, Speicher und öffentlicher IP-Adressen.
- Geben Sie Details zu dem Kontingent an, das Ihrem Abonnement zugeordnet ist, damit Sie überprüfen können, ob noch genügend Kapazität für die Anzahl der zu verwendenden VMs vorhanden ist.

Fahren Sie mit der Bereitstellung eines Clusters fort:

1.  Überprüfen Sie die verfügbaren Parameter für die AKS-Engine in Azure Stack-[CLI-Flags](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#cli-flags).

    | Parameter | Beispiel | BESCHREIBUNG |
    | --- | --- | --- |
    | azure-env | AzureStackCloud | Um der AKS-Engine mitzuteilen, dass die Zielplattform Azure Stack ist, verwenden Sie `AzureStackCloud`. |
    | identity-system | adfs | Optional. Geben Sie Ihre Identitätsverwaltungslösung an, wenn Sie Active Directory-Verbunddienste (AD FS) nutzen. |
    | location | local | Der Regionsname für Ihre Azure Stack-Instanz. Für das ASDK ist `local` für die Region festgelegt. |
    | resource-group | kube-rg | Geben Sie den Namen einer neuen Ressourcengruppe ein, oder wählen Sie eine vorhandene Ressourcengruppe aus. Der Ressourcenname muss alphanumerisch und in Kleinbuchstaben angegeben sein. |
    | api-model | ./kubernetes-azurestack.json | Pfad zur Clusterkonfigurationsdatei oder zum API-Modell. |
    | output-directory | kube-rg | Geben Sie den Namen des Verzeichnisses ein, in dem die Ausgabedatei `apimodel.json` sowie andere generierte Dateien enthalten sein sollen. |
    | client-id | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Geben Sie die Dienstprinzipal-GUID ein. Die als Anwendungs-ID identifizierte Client-ID, wenn Ihr Azure Stack-Administrator den Dienstprinzipal erstellt hat. |
    | client-secret | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Geben Sie das Dienstprinzipalgeheimnis ein. Dies ist das Clientgeheimnis, das Sie beim Erstellen Ihres Diensts einrichten. |
    | subscription-id | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Geben Sie Ihre Abonnement-ID ein. Weitere Informationen finden Sie unter [Abonnieren von Angeboten](https://docs.microsoft.com/azure-stack/user/azure-stack-subscribe-services#subscribe-to-an-offer). |

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

Überprüfen Sie Ihren Cluster durch Bereitstellen von MySQL mit Helm zum Überprüfen Ihres Clusters.

1. Rufen Sie die öffentliche IP-Adresse eines Ihrer Masterknoten über das Azure Stack-Portal ab.

2. Stellen Sie von einem Computer, der Zugriff auf Ihre Azure Stack-Instanz hat, über SSH mit einem Client wie z. B. PuTTY oder MobaXterm eine Verbindung mit dem neuen Masterknoten her. 

3. Verwenden Sie für den SSH-Benutzernamen „azureuser“ und die Datei mit dem privaten Schlüssel des Schlüsselpaars, das Sie für die Bereitstellung des Clusters angegeben haben.

4.  Führen Sie die folgenden Befehle aus:

    ```bash
    sudo snap install helm –classic
    helm repo update
    helm install stable/mysql
    ```

5. Wenn Sie nach dem Versuch, `install stable/mysql` auszuführen, eine Fehlermeldung wie `Error: incompatible versions client[v2.XX.X] server[v2.YY.Y]` erhalten, gehen Sie wie folgt vor: Führen Sie die folgenden Befehle aus:

    ```bash 
    helm init --force-upgrade
    and retry:
    helm install stable/mysql
    ```

6.  Um den Test zu bereinigen, suchen Sie den Namen, der für die MySQL-Bereitstellung verwendet wird. Im folgenden Beispiel lautet der Name `wintering-rodent`. Löschen Sie ihn dann. 

    Führen Sie die folgenden Befehle aus:

    ```bash
    helm ls
    NAME REVISION UPDATED STATUS CHART APP VERSION NAMESPACE
    wintering-rodent 1 Thu Oct 18 15:06:58 2018 DEPLOYED mysql-0.10.1 5.7.14 default
    helm delete wintering-rodent
    ```

    In der CLI wird Folgendes angezeigt:
    ```bash
    release "wintering-rodent" deleted
    ```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Problembehandlung bei der AKS-Engine in Azure Stack](azure-stack-kubernetes-aks-engine-troubleshoot.md)