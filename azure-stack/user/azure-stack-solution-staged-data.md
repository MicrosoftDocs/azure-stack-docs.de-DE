---
title: Bereitstellen einer Lösung für die gestaffelte Datenanalyse in Azure Stack | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie eine Lösung für die gestaffelte Datenanalyse in Azure Stack bereitstellen.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 06/20/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/20/2019
ms.openlocfilehash: 7148e93977f50a7c64d79c422c43c6825b22b4a3
ms.sourcegitcommit: 104ccafcb72a16ae7e91b154116f3f312321cff7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/21/2019
ms.locfileid: "67308994"
---
# <a name="tutorial-deploy-a-staged-data-analytics-solution-to-azure-stack"></a>Tutorial: Bereitstellen einer Lösung für die gestaffelte Datenanalyse in Azure Stack

In diesem Artikel wird Ihnen gezeigt, wie Sie eine Lösung zum Erfassen von Daten bereitstellen, die gleich nach der Erfassung analysiert werden müssen, damit schnelle Entscheidungen getroffen werden können. Häufig wird diese Datensammlung ohne Internetzugriff durchgeführt. Nachdem die Konnektivität hergestellt wurde, müssen Sie ggf. eine ressourcenintensive Analyse der Daten durchführen, um weitere Erkenntnisse zu gewinnen.

In diesem Tutorial erstellen Sie eine Beispielumgebung, die Folgendes ermöglicht:

> [!div class="checklist"]
> - Erstellen des Rohdaten-Speicherblobs
> - Erstellen einer neuen Azure Stack-Funktion, um bereinigte Daten aus Azure Stack nach Azure zu verschieben
> - Erstellen einer Funktion, die durch Blob Storage ausgelöst wird
> - Erstellen eines Azure Stack-Speicherkontos mit einem Blob und einer Warteschlange
> - Erstellen einer Funktion mit Auslösung per Warteschlange
> - Testen der Funktion mit Auslösung per Warteschlange

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack ist eine Erweiterung von Azure. Mit Azure Stack holen Sie sich die Agilität und Innovation des Cloud Computing in Ihre lokale Umgebung. Sie erhalten die einzige Hybrid Cloud, mit der Sie Hybrid-Apps überall entwickeln und bereitstellen können.  
> 
> Im Whitepaper [Design Considerations for Hybrid Applications](https://aka.ms/hybrid-cloud-applications-pillars) (Entwurfsüberlegungen für Hybridanwendungen) werden die wichtigen Aspekte in Bezug auf die Softwarequalität (Platzierung, Skalierbarkeit, Verfügbarkeit, Resilienz, Verwaltbarkeit und Sicherheit) beschrieben, die für das Entwerfen, Bereitstellen und Betreiben von Hybridanwendungen erforderlich sind. Die Überlegungen zum Entwurf dienen als Hilfe beim Optimieren des Designs von Hybridanwendungen, um für Produktionsumgebungen das Auftreten von Problemen zu minimieren.

## <a name="architecture-for-staged-data-analytics"></a>Architektur für gestaffelte Datenanalyse

![Gestaffelte Datenanalyse](media/azure-stack-solution-staged-data/image1.png)

## <a name="prerequisites-for-staged-data-analytics"></a>Voraussetzungen für gestaffelte Datenanalyse

  - Ein Azure-Abonnement.
  - Ein Dienstprinzipal für Azure Active Directory (Azure AD), der über Berechtigungen für das Mandantenabonnement in Azure und Azure Stack verfügt. Möglicherweise müssen Sie zwei Dienstprinzipale erstellen, wenn Azure Stack einen anderen AAD-Mandanten als Ihr Azure-Abonnement verwendet. Informationen zum Erstellen eines Dienstprinzipals für Azure Stack finden Sie unter [Erstellen von Dienstprinzipalen, um Anwendungen Zugriff auf Azure Stack-Ressourcen zu gewähren](https://docs.microsoft.com/azure-stack/user/azure-stack-create-service-principals).
      - **Notieren Sie sich die Anwendungs-ID, den geheimen Clientschlüssel, die Azure AD-Mandanten-ID und den Mandantennamen (xxxxx.onmicrosoft.com) jedes Dienstprinzipals.**
  - Sie müssen eine Sammlung von Daten für die Datenanalyse bereitstellen. Beispieldaten werden bereitgestellt.
  - [Docker für Windows](https://docs.docker.com/docker-for-windows/), auf Ihrem lokalen Computer installiert.

## <a name="get-the-docker-image"></a>Abrufen des Docker-Images

Docker-Images für jede Bereitstellung beseitigen Abhängigkeitsprobleme zwischen verschiedenen Versionen von Azure PowerShell.
1.  Stellen Sie sicher, dass Docker für Windows Windows-Container verwendet.
2.  Führen Sie Folgendes in einer Eingabeaufforderung mit erhöhten Rechten aus, um den Docker-Container mit den Bereitstellungsskripts abzurufen.

```
 docker pull intelligentedge/stageddatasolution:1.0.0
```

## <a name="deploy-the-solution"></a>Bereitstellen der Lösung

1.  Sobald das Containerimage erfolgreich abgerufen wurde, starten Sie das Image.

      ```powershell  
      docker run -it intelligentedge/stageddatasolution:1.0.0 powershell
      ```

2.  Nachdem der Container gestartet wurde, erhalten Sie im Container ein PowerShell-Terminal mit erhöhten Rechten. Wechseln Sie die Verzeichnisse, um zu dem Bereitstellungsskript zu gelangen.

      ```powershell  
      cd .\SDDemo\
      ```

3.  Führen Sie die Bereitstellung aus. Geben Sie die Anmeldeinformationen und Ressourcennamen an, wenn erforderlich. Hochverfügbarkeit (HA) bezieht sich auf den Azure Stack, in dem der HA-Cluster bereitgestellt wird, und Notfallwiederherstellung (DR) bezieht sich auf den Azure Stack, in dem der DR-Cluster bereitgestellt wird.

      ```powershell
      .\DeploySolution-Azure-AzureStack.ps1 `
      -AzureApplicationId "applicationIDforAzureServicePrincipal" `
      -AzureApplicationSercet "clientSecretforServicePrincipal" `
      -AzureTenantId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" `
      -AzureStackAADTenantName "azurestacktenant.onmicrosoft.com" `
      -AzureStackTenantARMEndpoint "https://management.haazurestack.com" `
      -AzureStackApplicationId "applicationIDforStackServicePrincipal" `
      -AzureStackApplicationSercet "ClientSecretforStackServicePrincipal" `
      -AzureStackTenantId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" `
      -ResourcePrefix "aPrefixForResources"
      ```

1.  Wenn Sie aufgefordert werden, geben Sie eine Region für die Azure-Bereitstellung und Application Insights ein.

2.  Geben Sie „Y“ (J) ein, um die Installation des NuGet-Anbieters zuzulassen, wodurch die Installation der „2018-03-01-Hybrid“-Module des API-Profils ausgelöst wird, um die Bereitstellung in Azure und Azure Stack zu ermöglichen.

3.  Sobald die Ressourcen bereitgestellt sind, testen Sie, dass die Daten sowohl für Azure Stack als auch für Azure generiert werden.

    ```powershell  
      .\TDAGenerator.exe
    ```

4.  Sie können überprüfen, dass die Daten verarbeitet werden, indem Sie zu den in Azure oder Azure Stack bereitgestellten Webanwendungen wechseln.

### <a name="azure-web-app"></a>Azure-Web-App
 
![Lösung für die gestaffelte Datenanalyse](media/azure-stack-solution-staged-data/image2.png)
 
### <a name="azure-stack-web-app"></a>Azure Stack-Web-App
 
![Lösung für die gestaffelte Datenanalyse für Azure Stack](media/azure-stack-solution-staged-data/image3.png)

## <a name="next-steps"></a>Nächste Schritte

  - Weitere Informationen zu Hybrid Cloud-Anwendungen finden Sie unter [Hybrid Cloud-Lösungen](https://aka.ms/azsdevtutorials).

  - Ändern Sie den Code dieses Beispiels auf [GitHub](https://github.com/Azure-Samples/azure-intelligent-edge-patterns).
