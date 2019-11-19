---
title: Bereitstellen einer KI-basierten Lösung zur Ermittlung der Kundenfrequenz mithilfe von Azure und Azure Stack Hub
description: Erfahren Sie, wie Sie eine KI-basierte Lösung zur Ermittlung der Kundenfrequenz mithilfe von Azure und Azure Stack Hub bereitstellen. Mithilfe dieser Lösung lässt sich der Kundenverkehr in Einzelhandelsfilialen analysieren.
author: BryanLa
ms.service: azure-stack
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 41166490ef2a79bc61375479d8c83e0205ab44a6
ms.sourcegitcommit: 5c92a669007ab4aaffe4484f1d8836a40340dde1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73639987"
---
# <a name="deploy-an-ai-based-footfall-detection-solution-using-azure-and-azure-stack-hub"></a>Bereitstellen einer KI-basierten Lösung zur Ermittlung der Kundenfrequenz mithilfe von Azure und Azure Stack Hub

*Anwendungsbereich: Integrierte Azure Stack Hub-Systeme und Azure Stack Development Kit*

In diesem Artikel wird beschrieben, wie eine Lösung bereitgestellt wird, die mithilfe von Azure, Azure Stack Hub und dem Custom Vision AI Dev Kit Erkenntnisse aus der Praxis generiert.

In diesem Thema lernen Sie Folgendes:

> [!div class="checklist"]
> - Bereitstellen von nativen cloudbasierten Anwendungspaketen (CNAB) auf Edge-Ebene 
> - Bereitstellen einer cloudgrenzenübergreifenden Anwendung
> - Verwenden des Custom Vision AI Dev Kit für das Rückschließen auf Edge-Ebene

> [!Tip]  
> ![hybrid-pillars.png](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)  
> Microsoft Azure Stack Hub ist eine Erweiterung von Azure. Mit Azure Stack Hub holen Sie sich die Agilität und Innovation von Cloud Computing in Ihre lokale Umgebung. Sie erhalten die einzige Hybrid Cloud, mit der Sie Hybrid-Apps überall entwickeln und bereitstellen können.  
> 
> Im Artikel [Entwurfsüberlegungen für Hybridanwendungen](overview-app-design-considerations.md) werden die wichtigen Aspekte in Bezug auf die Softwarequalität (Platzierung, Skalierbarkeit, Verfügbarkeit, Resilienz, Verwaltbarkeit und Sicherheit) beschrieben, die für das Entwerfen, Bereitstellen und Betreiben von Hybridanwendungen erforderlich sind. Die Überlegungen zum Entwurf dienen als Hilfe beim Optimieren des Designs von Hybrid-Apps, um für Produktionsumgebungen das Auftreten von Problemen zu minimieren.

## <a name="prerequisites"></a>Voraussetzungen 

Schritte vor dem Beginnen mit diesem Bereitstellungsleitfaden:

- Prüfen der [Übersicht über die Lösung zur Ermittlung der Kundenfrequenz](pattern-retail-footfall-detection.md) 
- Verschaffen Sie sich Benutzerzugriff auf ein Azure Stack Development Kit (ASDK) oder ein Abonnement für eine Instanz eines integrierten Azure Stack Hub-Systems.
  - Der [Ressourcenanbieter für Azure App Service in Azure Stack Hub](../operator/azure-stack-app-service-overview.md) muss installiert sein. Sie benötigen Benutzerzugriff auf Ihre Azure Stack Hub-Instanz oder müssen für die Installation mit Ihrem Administrator zusammenarbeiten.
  - Sie benötigen ein Abonnement für ein Angebot, das ein Azure App Service- und Azure Storage-Kontingent bietet. Sie benötigen Benutzerzugriff, um ein Angebot zu erstellen.
- Zugriff auf ein Azure-Abonnement
  - Wenn Sie kein Azure-Abonnement haben, können Sie sich [für ein kostenloses Testkonto](https://azure.microsoft.com/free/) registrieren, bevor Sie beginnen.
- Erstellen Sie in Ihrem Verzeichnis zwei Dienstprinzipale:
  - Einen, der für die Nutzung mit Azure-Ressourcen konfiguriert ist und Zugriff auf den Geltungsbereich des Azure-Abonnements hat. 
  - Einen, der für die Nutzung mit Azure Stack Hub-Ressourcen konfiguriert ist und Zugriff auf den Geltungsbereich des Azure Stack Hub-Abonnements hat. 
  - Informationen zum Erstellen von Dienstprinzipalen und Autorisieren des Zugriffs finden Sie unter [Verwenden einer App-Identität für den Ressourcenzugriff](../operator/azure-stack-create-service-principals.md). Wenn Sie die Azure CLI bevorzugen, siehe [Erstellen eines Azure-Dienstprinzipals mit der Azure CLI](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest).
- Bereitstellen von Azure Cognitive Services in Azure oder Azure Stack Hub
  - [Erfahren Sie zunächst mehr über Cognitive Services](https://azure.microsoft.com/services/cognitive-services/).
  - Lesen Sie dann [Bereitstellen von Azure Cognitive Services in Azure Stack Hub](../user/azure-stack-solution-template-cognitive-services.md), um Cognitive Services in Azure Stack Hub bereitzustellen. Zuerst müssen Sie sich für den Zugriff auf die Vorschauversion registrieren.
- Sie können anschließend ein nicht konfiguriertes Azure Custom Vision AI Dev Kit klonen oder herunterladen. Einzelheiten finden Sie unter [Vision AI DevKit](https://azure.github.io/Vision-AI-DevKit-Pages/).
- Registrieren Sie sich für ein Power BI-Konto.
- Holen Sie sich einen Abonnementschlüssel für die Azure Cognitive Services-Gesichtserkennungs-API und eine Endpunkt-URL. Sie finden beides in der kostenlosen Testversion unter [Cognitive Services ausprobieren](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Oder befolgen Sie die Anweisungen unter [Erstellen eines Cognitive Services-Kontos](/azure/cognitive-services/cognitive-services-apis-create-account).
- Installieren Sie die folgenden Entwicklungsressourcen:
  - [Azure CLI 2.0](../user/azure-stack-version-profiles-azurecli2.md)
  - [Docker CE](https://hub.docker.com/search/?type=edition&offering=community)
  - [Porter](https://porter.sh/). Mit Porter können Sie Cloudanwendungen bereitstellen, indem Sie CNAB-Paketmanifeste verwenden, die für Sie bereitgestellt werden.
  - [Visual Studio Code](https://code.visualstudio.com/)
  - [Azure IoT Tools für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  - [Python-Erweiterung für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  - [Python](https://www.python.org/)

## <a name="deploy-the-hybrid-cloud-application"></a>Bereitstellen der Hybrid Cloud-Anwendung

Zuerst generieren Sie mit der Porter CLI einen Satz mit Anmeldeinformationen und stellen dann die Cloudanwendung bereit.  

1. Klonen oder laden Sie den Code des Lösungsbeispiels von https://github.com/azure-samples/azure-intelligent-edge-patterns herunter. 

1. Porter generiert einen Satz mit Anmeldeinformationen, der die Bereitstellung der Anwendung automatisiert. Bevor Sie den Befehl zur Generierung von Anmeldeinformationen ausführen, stellen Sie sicher, dass Sie über Folgendes verfügen:

    - Einen Dienstprinzipal für den Zugriff auf Azure-Ressourcen, einschließlich Dienstprinzipal-ID, Schlüssels und DNS des Mandanten.
    - Die Abonnement-ID Ihres Azure-Abonnements.
    - Einen Dienstprinzipal für den Zugriff auf Azure Stack Hub-Ressourcen, einschließlich Dienstprinzipal-ID, Schlüssel und DNS des Mandanten.
    - Die Abonnement-ID Ihres Azure Stack Hub-Abonnements.
    - Den Schlüssel für die Azure Cognitive Services-Gesichtserkennungs-API und die Ressourcenendpunkt-URL.

1. Führen Sie den Porter-Prozess zur Erstellung von Anmeldeinformationen aus, und befolgen Sie die Anweisungen:

   ```porter
   porter creds generate --tag intelligentedge/footfall-cloud-deployment:0.1.0
   ```

1. Porter erfordert auch die Ausführung einer Reihe von Parametern. Erstellen Sie eine Parametertextdatei, und geben Sie die folgenden Name-Wert-Paare ein. Fragen Sie Ihren Azure Stack Hub-Administrator, wenn Sie Unterstützung bei den erforderlichen Werten benötigen.

   > [!NOTE] 
   > Der Wert `resource suffix` wird verwendet, um sicherzustellen, dass die Ressourcen Ihrer Bereitstellung in Azure eindeutige Namen haben. Es muss sich um eine eindeutige Zeichenfolge aus Buchstaben und Zahlen mit maximal 8 Zeichen handeln.

    ```
    azure_stack_tenant_arm="Your Azure Stack Hub tenant endpoint"
    azure_stack_storage_suffix="Your Azure Stack Hub storage suffix"
    azure_stack_keyvault_suffix="Your Azure Stack Hub keyVault suffix"
    resource_suffix="A unique string to identify your deployment"
    azure_location="A valid Azure region"
    azure_stack_location="Your Azure Stack Hub location identifier"
    powerbi_display_name="Your first and last name"
    powerbi_principal_name="Your Power BI account email address"
    ```
   Speichern Sie die Textdatei, und notieren Sie sich ihren Pfad.

1. Sie sind jetzt bereit, die Hybrid Cloud-Anwendung mithilfe von Porter bereitzustellen. Führen Sie den Installationsbefehl aus, und beobachten Sie, wie Ressourcen für Azure und Azure Stack Hub bereitgestellt werden:

    ```porter
    porter install footfall-cloud –tag intelligentedge/footfall-cloud-deployment:0.1.0 –creds footfall-cloud-deployment –param-file "path-to-cloud-parameters-file.txt"
    ```

1. Notieren Sie sich nach Abschluss der Bereitstellung die folgenden Werte:
    - Die Verbindungszeichenfolge der Kamera
    - Die Verbindungszeichenfolge des Speicherkontos für Bilder
    - Die Ressourcengruppennamen

## <a name="prepare-the-custom-vision-ai-dev-kit"></a>Vorbereiten des Custom Vision AI Dev Kit

Richten Sie als Nächstes das Custom Vision AI Dev Kit wie im [Schnellstart zum Vision AI DevKit ](https://azure.github.io/Vision-AI-DevKit-Pages/docs/quick_start/) gezeigt ein. Sie richten auch Ihre Kamera ein, die Sie mit der im vorherigen Schritt angegebenen Verbindungszeichenfolge testen.

## <a name="deploy-the-camera-application"></a>Bereitstellen der Kameraanwendung

Generieren Sie mit der Porter CLI einen Satz mit Anmeldeinformationen und stellen dann die Kameraanwendung bereit.

1. Porter generiert einen Satz mit Anmeldeinformationen, der die Bereitstellung der Anwendung automatisiert. Bevor Sie den Befehl zur Generierung von Anmeldeinformationen ausführen, stellen Sie sicher, dass Sie über Folgendes verfügen:
    
    - Einen Dienstprinzipal für den Zugriff auf Azure-Ressourcen, einschließlich Dienstprinzipal-ID, Schlüssels und DNS des Mandanten.
    - Die Abonnement-ID Ihres Azure-Abonnements.
    - Die beim Bereitstellen der Cloudanwendung bereitgestellte Verbindungszeichenfolge für das Speicherkonto für Bilder.

1. Führen Sie den Porter-Prozess zur Erstellung von Anmeldeinformationen aus, und befolgen Sie die Anweisungen:

    ```porter
    porter creds generate --tag intelligentedge/footfall-camera-deployment:0.1.0
    ```

1. Porter erfordert auch die Ausführung einer Reihe von Parametern. Erstellen Sie eine Parametertextdatei, und geben Sie den folgenden Text ein. Fragen Sie Ihren Azure Stack Hub-Administrator, wenn Sie einige der erforderlichen Werten nicht kennen sollten.

    > [!NOTE]
    > Der Wert `deployment suffix` wird verwendet, um sicherzustellen, dass die Ressourcen Ihrer Bereitstellung in Azure eindeutige Namen haben. Es muss sich um eine eindeutige Zeichenfolge aus Buchstaben und Zahlen mit maximal 8 Zeichen handeln.

    ```
    iot_hub_name="Name of the IoT Hub deployed"
    deployment_suffix="Unique string here"
    ```

    Speichern Sie die Textdatei, und notieren Sie sich ihren Pfad.

4. Sie sind jetzt bereit, die Kameraanwendung mithilfe von Porter bereitzustellen. Führen Sie den Installationsbefehl aus, und beobachten Sie, wie die IoT Edge-Bereitstellung erstellt wird.

    ```porter
    porter install footfall-camera –tag intelligentedge/footfall-camera-deployment:0.1.0 –creds footfall-camera-deployment –param-file "path-to-camera-parameters-file.txt"
    ```

5. Vergewissern Sie sich, dass die Bereitstellung der Kamera abgeschlossen ist, indem Sie den Kamerafeed bei `https://<camera-ip>:3000/` einsehen, wobei `<camara-ip>` die IP-Adresse der Kamera ist. Dieser Schritt kann bis zu 10 Minuten dauern.

## <a name="configure-azure-stream-analytics"></a>Konfigurieren von Azure Stream Analytics

Da nun die Daten von der Kamera zu Azure Stream Analytics übertragen werden, müssen wir sie manuell für die Kommunikation mit Power BI autorisieren.

1.  Öffnen Sie im Azure-Portal **Alle Ressourcen** und dann den Auftrag *process-footfall\[Ihr_Suffix\]* .

2.  Wählen Sie im Bereich „Stream Analytics-Auftrag“ im Abschnitt **Auftragstopologie** die Option **Ausgaben**.

3.  Wählen Sie die Ausgabesenke **traffic-output** aus.

4.  Wählen Sie **Autorisierung erneuern** aus, und melden Sie sich bei Ihrem Power BI-Konto an.
    
    ![Aufforderung zum Erneuern der Autorisierung](./media/solution-deployment-guide-retail-footfall-detection/image2.png)

5.  Speichern Sie die Ausgabeeinstellungen.

6.  Navigieren Sie zum Bereich **Übersicht**, und klicken Sie auf **Starten**, um das Senden von Daten an Power BI zu starten.

7.  Wählen Sie als Startzeit für die Auftragsausgabe die Option **Jetzt** und anschließend **Starten**. Der Auftragsstatus kann über die Benachrichtigungsleiste angezeigt werden.

## <a name="create-a-power-bi-dashboard"></a>Erstellen eines Power BI-Dashboards

1.  Navigieren Sie nach erfolgreichem Abschluss des Auftrags zu [Power BI](https://powerbi.com/), und melden Sie sich mit Ihrem Geschäfts-, Schul- oder Unikonto an. Wenn die Abfrage des Stream Analytics-Auftrags Ergebnisse ausgibt, befindet sich das von Ihnen erstellte Dataset *footfall-dataset* auf der Registerkarte **Datasets**.

2.  Wählen Sie in Ihrem Power BI-Arbeitsbereich die Option **+ Erstellen** aus, um ein neues Dashboard namens *Footfall Analysis* zu erstellen.

3.  Wählen Sie im oberen Fensterbereich **Kachel hinzufügen** aus. Wählen Sie dann **Benutzerdefinierte Streamingdaten** und anschließend **Weiter** aus. Wählen Sie unter **Ihre Datasets** das Dataset **footfall-dataset** aus. Wählen Sie in der Dropdownliste **Visualisierungstyp** die Option **Karte** aus, und fügen Sie unter **Felder** die Option **age** hinzu. Wählen Sie **Weiter** aus, um einen Namen für die Kachel einzugeben, und wählen Sie dann **Übernehmen** aus, um die Kachel zu erstellen.

4.  Sie können nach Wunsch weitere Felder und Karten hinzufügen.

## <a name="test-your-solution"></a>Testen Ihrer Lösung

Beobachten Sie, wie sich die Daten auf den Karten, die Sie in Power BI erstellt haben, ändern, wenn verschiedene Personen sich an der Kamera vorbeigehen. Es kann bis zu 20 Sekunden dauern, bis Rückschlüsse angezeigt werden.

## <a name="remove-your-solution"></a>Entfernen der Lösung

Wenn Sie Ihre Lösung entfernen möchten, führen Sie in Porter die folgenden Befehle aus, wobei Sie die gleichen Parameterdateien verwenden, die Sie für die Bereitstellung erstellt haben: 

```porter
porter uninstall footfall-cloud –tag intelligentedge/footfall-cloud-deployment:0.1.0 –creds footfall-cloud-deployment –param-file "path-to-cloud-parameters-file.txt"

porter uninstall footfall-camera –tag intelligentedge/footfall-camera-deployment:0.1.0 –creds footfall-camera-deployment –param-file "path-to-camera-parameters-file.txt"
```
## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Überlegungen zum Entwurf von Hybrid Cloud-Apps](overview-app-design-considerations.md).
- Überprüfen Sie den [Code dieses Beispiels auf GitHub](https://github.com/Azure-Samples/azure-intelligent-edge-patterns/tree/master/footfall-analysis), und schlagen Sie Verbesserungen vor.
