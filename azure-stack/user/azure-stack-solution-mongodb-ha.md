---
title: Bereitstellen einer MongoDB-Hochverfügbarkeitslösung in Azure und Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine MongoDB-Hochverfügbarkeitslösung in Azure und Azure Stack bereitstellen.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: solution
ms.date: 06/20/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/20/2019
ms.openlocfilehash: aa48e3b40afc841a26f15ce06870d002261c5932
ms.sourcegitcommit: 2a4cb9a21a6e0583aa8ade330dd849304df6ccb5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/17/2019
ms.locfileid: "68286838"
---
# <a name="deploy-a-highly-available-mongodb-solution-to-azure-and-azure-stack"></a>Bereitstellen einer MongoDB-Hochverfügbarkeitslösung in Azure und Azure Stack

In diesem Artikel werden Sie schrittweise durch die automatisierte Bereitstellung eines einfachen MongoDB-Hochverfügbarkeitsclusters (HA) mit einem Standort für die Notfallwiederherstellung (DR) in zwei Azure Stack-Umgebungen geführt. Weitere Informationen zu MongoDB und zur Hochverfügbarkeit finden Sie unter [Replikatgruppenmitglieder](https://docs.mongodb.com/manual/core/replica-set-members/).

In dieser Lösung erstellen Sie eine Beispielumgebung, die Folgendes ermöglicht:

> [!div class="checklist"]
> - Orchestrieren einer Bereitstellung in zwei Azure Stacks
> - Verwenden von Docker zur Minimierung von Abhängigkeitsproblemen mit Azure-API-Profilen
> - Bereitstellen eines einfachen MongoDB-Hochverfügbarkeitsclusters mit einem Standort für die Notfallwiederherstellung


> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack ist eine Erweiterung von Azure. Mit Azure Stack holen Sie sich die Agilität und Innovation von Cloud Computing in Ihre lokale Umgebung. Sie erhalten die einzige Hybrid Cloud, mit der Sie Hybrid-Apps überall entwickeln und bereitstellen können.  
> 
> Im Artikel [Entwurfsüberlegungen für Hybridanwendungen](azure-stack-edge-pattern-overview.md) werden die wichtigen Aspekte in Bezug auf die Softwarequalität (Platzierung, Skalierbarkeit, Verfügbarkeit, Resilienz, Verwaltbarkeit und Sicherheit) beschrieben, die für das Entwerfen, Bereitstellen und Betreiben von Hybridanwendungen erforderlich sind. Die Überlegungen zum Entwurf dienen als Hilfe beim Optimieren des Designs von Hybrid-Apps, um für Produktionsumgebungen das Auftreten von Problemen zu minimieren.



## <a name="architecture-for-mongodb-with-azure-stack"></a>Architektur für MongoDB mit Azure Stack

![Hochverfügbare MongoDB in Azure Stack](media/azure-stack-solution-mongdb-ha/image1.png)

## <a name="prerequisites-for-mongodb-with-azure-stack"></a>Voraussetzungen für MongoDB mit Azure Stack

  - Zwei verbundene integrierte Azure Stack-Systeme (Azure Stack); diese Bereitstellung funktioniert nicht mit Azure Stack Development Kits (ASDKs). Weitere Informationen zu Azure Stack finden Sie unter [Was ist Azure Stack?](https://azure.microsoft.com/overview/azure-stack/).
      - Ein Mandantenabonnement auf jedem Azure Stack.    
      - **Notieren Sie sich jede Abonnement-ID und den Azure Resource Manager-Endpunkt für jeden Azure Stack.**
  - Ein Dienstprinzipal für Azure Active Directory (Azure AD), der über Berechtigungen für das Mandantenabonnement auf jedem Azure Stack verfügt. Möglicherweise müssen Sie zwei Dienstprinzipale erstellen, wenn die Azure Stacks auf unterschiedlichen Azure AD-Mandanten bereitgestellt sind. Informationen zum Erstellen eines Dienstprinzipals für Azure Stack finden Sie unter [Erstellen von Dienstprinzipalen, um Anwendungen Zugriff auf Azure Stack-Ressourcen zu gewähren](https://docs.microsoft.com/azure-stack/user/azure-stack-create-service-principals).    
      - **Notieren Sie sich die Anwendungs-ID, den geheimen Clientschlüssel und den Mandantennamen (xxxxx.onmicrosoft.com) jedes Dienstprinzipals.**
  - Ubuntu 16.04 syndiziert in jeden Marketplace von Azure Stack. Weitere Informationen zur Marketplace-Syndikation finden Sie unter [Herunterladen von Marketplace-Elementen von Azure in Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item).
  - [Docker für Windows](https://docs.docker.com/docker-for-windows/), auf Ihrem lokalen Computer installiert.

## <a name="get-the-docker-image"></a>Abrufen des Docker-Images

Docker-Images für jede Bereitstellung beseitigen Abhängigkeitsprobleme zwischen verschiedenen Versionen von Azure PowerShell.
1.  Stellen Sie sicher, dass Docker für Windows Windows-Container verwendet.
2.  Führen Sie Folgendes in einer Eingabeaufforderung mit erhöhten Rechten aus, um den Docker-Container mit den Bereitstellungsskripts abzurufen.
```powershell  
docker pull intelligentedge/mongodb-hadr:1.0.0
```

## <a name="deploy-the-clusters"></a>Bereitstellen der Cluster

1.  Sobald das Containerimage erfolgreich abgerufen wurde, starten Sie das Image.

    ```powershell  
    docker run -it intelligentedge/mongodb-hadr:1.0.0 powershell
    ```

2.  Nachdem der Container gestartet wurde, erhalten Sie im Container ein PowerShell-Terminal mit erhöhten Rechten. Wechseln Sie die Verzeichnisse, um zu dem Bereitstellungsskript zu gelangen.

    ```powershell  
    cd .\MongoHADRDemo\
    ```

3.  Führen Sie die Bereitstellung aus. Geben Sie die Anmeldeinformationen und Ressourcennamen an, wenn erforderlich. Hochverfügbarkeit (HA) bezieht sich auf den Azure Stack, in dem der HA-Cluster bereitgestellt wird, und Notfallwiederherstellung (DR) bezieht sich auf den Azure Stack, in dem der DR-Cluster bereitgestellt wird.

    ```powershell
    .\Deploy-AzureResourceGroup.ps1 `
    -AzureStackApplicationId_HA "applicationIDforHAServicePrincipal" `
    -AzureStackApplicationSercet_HA "clientSecretforHAServicePrincipal" `
    -AADTenantName_HA "hatenantname.onmicrosoft.com" `
    -AzureStackResourceGroup_HA "haresourcegroupname" `
    -AzureStackArmEndpoint_HA "https://management.haazurestack.com" `
    -AzureStackSubscriptionId_HA "haSubscriptionId" `
    -AzureStackApplicationId_DR "applicationIDforDRServicePrincipal" `
    -AzureStackApplicationSercet_DR "ClientSecretforDRServicePrincipal" `
    -AADTenantName_DR "drtenantname.onmicrosoft.com" `
    -AzureStackResourceGroup_DR "drresourcegroupname" `
    -AzureStackArmEndpoint_DR "https://management.drazurestack.com" `
    -AzureStackSubscriptionId_DR "drSubscriptionId"
    ```

4.  Geben Sie `Y` ein, um die Installation des NuGet-Anbieters zuzulassen, wodurch die Installation der „2018-03-01-Hybrid“-Module des API-Profils ausgelöst wird.

5.  Die HA-Ressourcen werden zuerst bereitgestellt. Überwachen Sie die Bereitstellung, und warten Sie, bis sie abgeschlossen ist. Sobald die Meldung angezeigt wird, die besagt, dass die HA-Bereitstellung abgeschlossen ist, können Sie im Portal des Azure Stacks mit HA die bereitgestellten Ressourcen überprüfen. 

6.  Fahren Sie mit der Bereitstellung von DR-Ressourcen fort, und entscheiden Sie, ob Sie für den Azure Stack mit DR eine Jumpbox für die Interaktion mit dem Cluster aktivieren möchten.

7.  Warten Sie, bis die DR-Ressourcenbereitstellung abgeschlossen ist.

8.  Nach Abschluss der Bereitstellung der DR-Ressource beenden Sie den Container.

  ```powershell
  exit
  ```

## <a name="next-steps"></a>Nächste Schritte

  - Wenn Sie die Jumpbox-VM in dem Azure Stack mit DR aktiviert haben, können Sie eine Verbindung über SSH herstellen und mit dem MongoDB-Cluster interagieren, indem Sie die Mongo-CLI installieren. Weitere Informationen zur Interaktion mit MongoDB finden Sie unter [Die Mongo-Shell](https://docs.mongodb.com/manual/mongo/).

  - Weitere Informationen zu Hybrid Cloud-Anwendungen finden Sie unter [Hybrid Cloud-Lösungen](https://aka.ms/azsdevtutorials).

  - Ändern Sie den Code dieses Beispiels auf [GitHub](https://github.com/Azure-Samples/azure-intelligent-edge-patterns).