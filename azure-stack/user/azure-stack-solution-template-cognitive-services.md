---
title: Bereitstellen von Azure Cognitive Services in Azure Stack Hub
description: Erfahren Sie, wie Sie Azure Cognitive Services in Azure Stack Hub bereitstellen.
author: mattbriggs
ms.topic: article
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: guanghu
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: d5cfb45be74122ec07a7632f9f6c7ef04b6f8c4a
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77701818"
---
# <a name="deploy-azure-cognitive-services-to-azure-stack-hub"></a>Bereitstellen von Azure Cognitive Services in Azure Stack Hub

> [!Note]  
> Die Azure Cognitive Services in Azure Stack Hub befinden sich in der Vorschau.

Sie können Azure Cognitive Services mit Containerunterstützung in Azure Stack Hub verwenden. Containerunterstützung in Azure Cognitive Services ermöglicht es Ihnen, dieselben umfassenden APIs zu nutzen, die in Azure zur Verfügung stehen. Durch Ihre Nutzung von Containern können Sie flexibel festlegen, wo die in [Docker-Containern](https://www.docker.com/what-container) übermittelten Services bereitgestellt und gehostet werden sollen. Die Containerunterstützung ist derzeit als Vorschauversion für einige Azure Cognitive Services verfügbar, einschließlich [Maschinelles Sehen](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home), [Gesichtserkennung](https://docs.microsoft.com/azure/cognitive-services/face/overview) und [Textanalyse](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) sowie [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto) (LUIS).

Die Verwendung von Containern ist ein Ansatz zur Softwareverteilung, bei dem eine App oder ein Dienst, einschließlich der Abhängigkeiten und Konfiguration, als Containerimage gepackt wird. Mit wenigen oder keinen Änderungen können Sie ein Image auf einem Containerhost bereitstellen. Jeder Container ist von anderen Containern und dem zugrunde liegenden Betriebssystem isoliert. Das System selbst enthält nur die Komponenten, die zur Ausführung Ihres Images erforderlich sind. Ein Containerhost hat einen kleineren Fußabdruck als ein virtueller Computer. Sie können auch Container aus Images für kurzfristige Aufgaben erstellen und wieder entfernen, wenn sie nicht mehr benötigt werden.

## <a name="use-containers-with-cognitive-services-on-azure-stack-hub"></a>Verwenden von Containern mit Cognitive Services in Azure Stack Hub

- **Kontrolle über Daten**  
  Ermöglichen Sie es Ihren App-Benutzern, ihre Daten während der Verwendung von Cognitive Services kontrollieren zu können. Sie können Cognitive Services an App-Benutzer übermitteln, die keine Daten an die globale Azure-Umgebung oder die öffentliche Cloud senden können.

- **Kontrolle über Modellaktualisierungen**  
  Machen Sie für App-Benutzer Versionsupdates der Modelle verfügbar, die in deren Lösung bereitgestellt wurden.

- **Portable Architektur**  
  Aktivieren Sie die Erstellung einer portablen App-Architektur, sodass Sie Ihre Lösung in der öffentlichen Cloud, einer privaten Cloud lokal oder Edge bereitstellen können. Sie können Ihren Container in Azure Kubernetes Service, Azure Container Instances oder einem Kubernetes-Cluster in Azure Stack Hub bereitstellen. Weitere Informationen finden Sie unter [Bereitstellen von Kubernetes in Azure Stack Hub](azure-stack-solution-template-kubernetes-deploy.md).

- **Hoher Durchsatz und niedrige Latenz**  
   Bieten Sie Ihren App-Benutzern die Möglichkeit zum Skalieren mit Lastspitzen für hohen Durchsatz und niedrige Latenz. Aktivieren Sie Cognitive Services zur Ausführung in Azure Kubernetes Service in physischer Nähe zu ihrer App-Logik und den zugehörigen Daten.

Stellen Sie mit Azure Stack Hub Cognitive Services-Container in einem Kubernetes-Cluster zusammen mit Ihren App-Containern bereit, um Hochverfügbarkeit und eine elastische Skalierung zu erzielen. Sie können Ihre App entwickeln, indem Sie Cognitive Services mit Komponenten kombinieren, die in App Services, Functions, Blob Storage, SQL- oder mySQL-Datenbanken integriert sind.

Weitere Informationen zu Cognitive Services-Containern finden Sie unter [Containerunterstützung in Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support).

## <a name="deploy-the-azure-face-api"></a>Bereitstellen der Azure-Gesichtserkennungs-API

In diesem Artikel wird beschrieben, wie Sie die Azure-Gesichtserkennungs-API in einem Kubernetes-Cluster in Azure Stack Hub bereitstellen. Mit dem gleichen Ansatz können Sie auch andere Cognitive Services-Container in Azure Stack Hub-Kubernetes-Clustern bereitstellen.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen, müssen Sie Folgendes durchführen:

1.  Fordern Sie Zugriff auf die Containerregistrierung an, um Images des Gesichtserkennungscontainers aus der Containerregistrierung von Azure Cognitive Services zu pullen. Ausführlichere Informationen finden Sie unter [Anfordern des Zugriffs auf die private Containerregistrierung](https://docs.microsoft.com/azure/cognitive-services/face/face-how-to-install-containers#request-access-to-the-private-container-registry).

2.  Bereiten Sie einen Kubernetes-Cluster in Azure Stack Hub vor. Informationen dazu finden Sie im Artikel [Bereitstellen von Kubernetes in Azure Stack Hub](azure-stack-solution-template-kubernetes-deploy.md).

## <a name="create-azure-resources"></a>Erstellen von Azure-Ressourcen

Erstellen Sie in Azure eine Cognitive Service-Ressource, um eine Vorschau der Gesichtserkennungs-, LUIS- oder Texterkennungscontainer anzuzeigen. Sie müssen den Abonnementschlüssel und die Endpunkt-URL der Ressource verwenden, um die Cognitive Services-Container zu instanziieren.

1. Erstellen Sie eine Azure-Ressource im Azure-Portal. Wenn Sie die Container für die Gesichtserkennung in einer Vorschau anzeigen möchten, müssen Sie zuerst im Azure-Portal eine entsprechende Gesichtserkennungsressource erstellen. Weitere Informationen finden Sie unter [Quickstart: Erstellen eines Cognitive Services-Kontos im Azure-Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).

   > [!Note]
   >  Die Ressource für Gesichtserkennung oder maschinelles Sehen muss den F0-Tarif verwenden.

2. Rufen Sie die Endpunkt-URL und den Abonnementschlüssel für die Azure-Ressource ab. Verwenden Sie nach der Erstellung der Azure-Ressource den Abonnementschlüssel und die Endpunkt-URL aus der Ressource, um den entsprechenden Gesichtserkennungs-, LUIS- oder Texterkennungscontainer für die Vorschau zu instanziieren.

## <a name="create-a-kubernetes-secret"></a>Erstellen eines Kubernetes-Geheimnisses 

Nutzen Sie den Kubectl-Befehl „create secret“, um auf die private Containerregistrierung zuzugreifen. Ersetzen Sie `<username>` durch den Benutzernamen und `<password>` durch das Kennwort. Diese Anmeldeinformationen wurden Ihnen vom Azure Cognitive Services-Team bereitgestellt.

```bash  
    kubectl create secret docker-registry <secretName> \
        --docker-server='containerpreview.azurecr.io' \
        --docker-username='<username>' \
        --docker-password='<password>' 
```

## <a name="prepare-a-yaml-configure-file"></a>Vorbereiten einer YAML-Konfigurationsdatei

Verwenden Sie die YAML-Konfigurationsdatei, um die Bereitstellung von Cognitive Services auf dem Kubernetes-Cluster zu vereinfachen.

Hier ist ein Beispiel für eine YAML-Konfigurationsdatei zum Bereitstellen des Gesichtserkennungsdiensts in Azure Stack Hub:

```Yaml  
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: <deploymentName>
spec:
  replicas: <replicaNumber>
  template:
    metadata:
      labels:
        app: <appName>
    spec:
      containers:
      - name: <containersName>
        image: <ImageLocation>
        env: 
        - name: EULA
          value: accept 
        - name: Billing
          value: <billingURL> 
        - name: apikey
          value: <apiKey>
        tty: true
        stdin: true
        ports:
        - containerPort: 5000 
      imagePullSecrets:
        - name: <secretName>
---
apiVersion: v1
kind: Service
metadata:
  name: <LBName>
spec:
  type: LoadBalancer
  ports:
  - port: 5000
    targetPort : 5000
    name: <PortsName>
  selector:
    app: <appName>
```

Verwenden Sie in dieser YAML-Konfigurationsdatei das Geheimnis, mit dem Sie die Cognitive Service-Containerimages aus der Azure Container Registry (ACR) abgerufen haben. Die Geheimnisdatei wird verwendet, um ein bestimmtes Replikat des Containers bereitzustellen. Sie können auch einen Lastenausgleich erstellen, um sicherzustellen, dass Benutzer auf diesen Dienst extern zugreifen können.

Details zu den Schlüsselfeldern:

| Feld | Notizen |
| --- | --- |
| replicaNumber | Definiert die ersten Replikate der zu erstellenden Instanzen. Dies können Sie zu einem späteren Zeitpunkt nach der Bereitstellung skalieren. |
| ImageLocation | Gibt den Speicherort für das spezifische Cognitive Service-Containerimage in ACR an. Beispielsweise der Gesichtserkennungsdienst: `aicpppe.azurecr.io/microsoft/cognitive-services-face` |
| BillingURL |Die Endpunkt-URL, die Sie sich im Schritt [Erstellen von Azure-Ressourcen](#create-azure-resources) notiert haben |
| ApiKey | Der Abonnementschlüssel, den Sie sich im Schritt [Erstellen von Azure-Ressourcen](#create-azure-resources) notiert haben |
| SecretName | Der Name des Geheimnisses, das Sie im Schritt [Erstellen eines Kubernetes-Geheimnisses](#create-a-kubernetes-secret) erstellt haben |

## <a name="deploy-the-cognitive-service"></a>Bereitstellen des Cognitive Service

Verwenden Sie den folgenden Befehl, um die Cognitive Services-Container bereitzustellen:

```bash  
    Kubectl apply -f <yamlFineName>
```
Verwenden Sie den folgenden Befehl, um den Prozess der Bereitstellung zu überwachen: 
```bash  
    Kubectl get pod - watch
```

## <a name="test-the-cognitive-service"></a>Testen des Cognitive Service

Greifen Sie auf die [OpenAPI-Spezifikation](https://swagger.io/docs/specification/about/) über die relative **/swagger**-URI für diesen Container zu. In dieser Spezifikation, die bisher als Swagger-Spezifikation bezeichnet wurde, werden die Vorgänge beschrieben, die von einem instanziierten Container unterstützt werden. Der folgende URI bietet beispielsweise Zugriff auf die OpenAPI-Spezifikation für den Container für die Standpunktanalyse, der im vorherigen Beispiel instanziiert wurde:

```HTTP  
http:<External IP>:5000/swagger
```

Sie können die externe IP-Adresse mithilfe des folgenden Befehls abrufen: 

```bash  
    Kubectl get svc <LoadBalancerName>
```

## <a name="try-the-services-with-python"></a>Ausprobieren der Dienste mit Python

Sie können versuchen, die Cognitive Services in Ihrer Azure Stack Hub-Instanz zu überprüfen, indem Sie einige einfache Python-Skripts ausführen. Als Referenz stehen Ihnen offizielle Python-Schnellstartbeispiele für [maschinelles Sehen](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home), [Gesichtserkennung](https://docs.microsoft.com/azure/cognitive-services/face/overview), [Textanalyse](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) und [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto) (LUIS) zur Verfügung.

Bei der Verwendung von Python-Apps müssen in Bezug auf die Überprüfung der Dienste, die in Containern ausgeführt werden, zwei Dinge beachtet werden: 
1. Cognitive Services in Containern benötigen keine Unterschlüssel für die Authentifizierung, aber es ist weiterhin eine beliebige Zeichenfolge als Platzhalter erforderlich, um die Anforderungen des SDK zu erfüllen. 
2. Ersetzen Sie die Basis-URL durch die tatsächliche IP-Adresse des Dienstendpunkts.

Hier ist ein Python-Beispielskript angegeben, das vom Python SDK für Gesichtserkennungsdienste verwendet wird, um Gesichter in einem Bild zu erkennen und zu umranden:

```Python  
import cognitive_face as CF

# Cognitive Services in container do not need sub keys for authentication
# Keep the invalid key to satisfy the SDK inputs requirement. 
KEY = '0'  #  (keeping the quotes in place).
CF.Key.set(KEY)

# Get your actual Ip Address of service endpoint of your cognitive service on Azure Stack Hub
BASE_URL = 'http://<svc IP Address>:5000/face/v1.0/'  
CF.BaseUrl.set(BASE_URL)

# You can use this example JPG or replace the URL below with your own URL to a JPEG image.
img_url = 'https://raw.githubusercontent.com/Microsoft/Cognitive-Face-Windows/master/Data/detection1.jpg'
faces = CF.face.detect(img_url)
print(faces)

```

## <a name="next-steps"></a>Nächste Schritte

[Installieren und Ausführen von Containern für Maschinelles Sehen-API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers)

[Installieren und Ausführen von Containern für Gesichtserkennungs-API](https://docs.microsoft.com/azure/cognitive-services/face/face-how-to-install-containers)

[Installieren und Ausführen von Containern für Textanalyse-API](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers)

[Installieren und Ausführen von Containern für Language Understanding (LUIS)-API](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto)
