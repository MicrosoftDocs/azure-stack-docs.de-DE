---
title: Konfigurieren der Hybrid Cloud-Konnektivität mit Azure und Azure Stack | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie die Hybrid Cloud-Konnektivität mit Azure und Azure Stack konfigurieren.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/14/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 00f7c3b990e9930571ace8adec9a33d0c9105be6
ms.sourcegitcommit: 261df5403ec01c3af5637a76d44bf030f9342410
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66252034"
---
# <a name="tutorial-configure-hybrid-cloud-connectivity-with-azure-and-azure-stack"></a>Tutorial: Konfigurieren der Hybrid Cloud-Konnektivität mit Azure und Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Mithilfe des Hybridkonnektivitätsmusters können Sie auf Ressourcen mit Sicherheit in globalen Azure- und Azure Stack-Instanzen zugreifen.

In diesem Tutorial erstellen Sie eine Beispielumgebung, die Folgendes ermöglicht:

> [!div class="checklist"]
> - Lokale Aufbewahrung von Daten zur Erfüllung datenschutzbezogener und rechtlicher Anforderungen bei gleichzeitigem Zugriff auf globale Azure-Ressourcen
> - Verwendung eines Legacysystems sowie einer über die Cloud skalierten App-Bereitstellung und entsprechender Ressourcen in globalen Azure-Instanzen

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack ist eine Erweiterung von Azure. Mit Azure Stack holen Sie sich die Agilität und Innovation von Cloud Computing in Ihre lokale Umgebung. Sie erhalten die einzige Hybrid Cloud, mit der Sie Hybrid-Apps überall entwickeln und bereitstellen können.  
> 
> Im Whitepaper [Design Considerations for Hybrid Applications](https://aka.ms/hybrid-cloud-applications-pillars) (Entwurfsüberlegungen für Hybridanwendungen) werden die wichtigen Aspekte in Bezug auf die Softwarequalität (Platzierung, Skalierbarkeit, Verfügbarkeit, Resilienz, Verwaltbarkeit und Sicherheit) beschrieben, die für das Entwerfen, Bereitstellen und Betreiben von Hybridanwendungen erforderlich sind. Die Überlegungen zum Entwurf dienen als Hilfe beim Optimieren des Designs von Hybridanwendungen, um für Produktionsumgebungen das Auftreten von Problemen zu minimieren.


## <a name="prerequisites"></a>Voraussetzungen

Es sind einige Komponenten erforderlich, um eine Hybrid-Verbindungsbereitstellung zu erstellen. Da die Vorbereitung für einige dieser Komponenten länger dauert, sollten Sie entsprechend planen.

**Azure Stack**

Ein Azure-OEM/-Hardwarepartner kann eine Azure Stack-Produktionsumgebung bereitstellen. Ein Azure Stack Development Kit (ASDK) kann von allen Benutzern bereitgestellt werden.

**Azure Stack-Komponenten**

Ein Azure Stack-Bediener muss App Service bereitstellen, Pläne, Angebote und ein Mandantenabonnement erstellen und das Windows Server 2016-Image hinzufügen. Wenn Sie bereits über einige dieser Komponenten verfügen, sollten Sie vor dem Beginn dieses Tutorials sicherstellen, dass diese die Anforderungen erfüllen.

In diesem Tutorial wird davon ausgegangen, dass Sie bereits über Grundkenntnisse in Bezug auf Azure und Azure Stack verfügen. Lesen Sie die folgenden Artikel, um vor dem Starten des Tutorials weitere Informationen zu erhalten:

 - [Einführung in Azure](https://azure.microsoft.com/overview/what-is-azure/)
 - [Azure Stack Key Concepts (Wichtige Begriffe für Azure Stack)](../operator/azure-stack-overview.md)

### <a name="azure"></a>Azure

 - Wenn Sie kein Azure-Abonnement besitzen, können Sie ein  [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)  erstellen, bevor Sie beginnen.
 - Erstellen Sie eine  [Web-App](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts) in Azure. Notieren Sie sich die Web-App-URL, da Sie sie im Tutorial benötigen.

### <a name="azure-stack"></a>Azure Stack

 - Verwenden Sie Ihre Azure Stack-Produktionsumgebung, oder stellen Sie das Azure Stack Development Kit bereit. Es befindet sich unter https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1.
   >[!Note]
   >Da die Bereitstellung des ASDK bis zu sieben Stunden dauern kann, sollten Sie entsprechend planen.

 - Stellen Sie PaaS-Dienste als  [App Service](../operator/azure-stack-app-service-deploy.md)  für Azure Stack bereit.
 - [Erstellen Sie Pläne und Angebote](../operator/azure-stack-plan-offer-quota-overview.md) in der Azure Stack-Umgebung.
 - [Erstellen Sie ein Mandantenabonnement](../operator/azure-stack-subscribe-plan-provision-vm.md) in Ihrer Azure Stack-Umgebung.

### <a name="before-you-begin"></a>Voraussetzungen

Überprüfen Sie, ob Sie die folgenden Kriterien erfüllen, bevor Sie mit der Konfiguration der Hybrid Cloud-Konnektivität beginnen:

 - Sie benötigen eine extern zugängliche, öffentliche IPv4-Adresse für Ihr VPN-Gerät. Diese IP-Adresse darf sich nicht hinter einer NAT befinden.
 - Alle Ressourcen werden in derselben Region bzw. an demselben Standort bereitgestellt.

#### <a name="tutorial-example-values"></a>Beispielwerte für Tutorial

In den Beispielen dieses Tutorials werden die folgenden Werte verwendet. Sie können diese Werte zum Erstellen einer Testumgebung verwenden oder zum besseren Verständnis der Beispiele heranziehen. Weitere Informationen zu Gatewayeinstellungen im Allgemeinen finden Sie unter  [Informationen zu VPN Gateway-Einstellungen](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings).

Verbindungsangaben:

 - **VPN-Typ:**  routenbasiert
 - **Verbindungstyp**: Site-to-Site (IPsec)
 - **Gatewaytyp**: VPN
 - **Azure-Verbindungsname**: Azure-Gateway-AzureStack-S2SGateway (automatisch vom Portal ausgefüllt)
 - **Azure Stack-Verbindungsname**: AzureStack-Gateway-Azure-S2SGateway (automatisch vom Portal ausgefüllt)
 - **Gemeinsam verwendeter Schlüssel**: Beliebiger, mit VPN-Hardware kompatibler Schlüssel mit entsprechenden Werten auf beiden Seiten der Verbindung
 - **Abonnement**: Beliebiges bevorzugtes Abonnement
 - **Ressourcengruppe**: Test-Infra

Netzwerk- und Subnetz-IP-Adressen:

| Azure/Azure Stack-Verbindung | NAME | Subnetz | IP-Adresse |
|-------------------------------------|---------------------------------------------|---------------------------------------|-----------------------------|
| Azure: VNet | ApplicationvNet<br>10.100.102.9/23 | ApplicationSubnet<br>10.100.102.0/24 |  |
|  |  | GatewaySubnet<br>10.100.103.0/24 |  |
| Azure Stack: VNet | ApplicationvNet<br>10.100.100.0/23 | ApplicationSubnet <br>10.100.100.0/24 |  |
|  |  | GatewaySubnet <br>10.100101.0/24 |  |
| Azure: Gateway für virtuelle Netzwerke | Azure-Gateway |  |  |
| Azure Stack: Gateway für virtuelle Netzwerke | AzureStack-Gateway |  |  |
| Azure: öffentliche IP-Adresse | Azure-GatewayPublicIP |  | Bei Erstellung bestimmt |
| Azure Stack: öffentliche IP-Adresse | AzureStack-GatewayPublicIP |  | Bei Erstellung bestimmt |
| Azure: lokales Netzwerkgateway | AzureStack-S2SGateway<br>   10.100.100.0/23 |  | Azure Stack: öffentlicher IP-Wert |
| Azure Stack: lokales Netzwerkgateway | Azure-S2SGateway<br>10.100.102.0/23 |  | Azure: öffentlicher IP-Wert |

## <a name="create-a-virtual-network-in-global-azure-and-azure-stack"></a>Erstellen eines virtuellen Netzwerks in globalen Azure- und Azure Stack-Instanzen

Führen Sie die folgenden Schritte aus, um mit dem Portal ein virtuelles Netzwerk zu erstellen. Sie können  [diese Beispielwerte](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values)  verwenden, wenn Sie diesen Artikel als Tutorial nutzen. Falls Sie den Artikel für die Konfiguration einer Produktionsumgebung verwenden, müssen Sie die Beispieleinstellungen aber durch Ihre eigenen Werte ersetzen.

> [!IMPORTANT]
> Die IP-Adressen in den VNet-Adressräumen von Azure oder Azure Stack dürfen sich nicht überschneiden.

Erstellen Sie in Azure wie folgt ein VNet:

1. Stellen Sie in Ihrem Browser eine Verbindung mit dem [Azure-Portal](https://portal.azure.com/)  her, und melden Sie sich mit Ihrem Azure-Konto an.
2. Wählen Sie  **Ressource erstellen** aus. Geben Sie im Feld  **Marketplace durchsuchen**  die Zeichenfolge `virtual network` ein. Suchen Sie in der Ergebnisliste nach  **Virtuelles Netzwerk** , und wählen Sie dann die Option  **Virtuelles Netzwerk** aus.
3. Wählen Sie in der Liste **Bereitstellungsmodell**  auswählen die Option  **Resource Manager** und dann  **Erstellen** aus.
4. Konfigurieren Sie unter **Virtuelles Netzwerk erstellen** die VNet-Einstellungen. Die erforderlichen Feldnamen sind durch ein vorangestelltes rotes Sternchen gekennzeichnet.  Wenn Sie einen gültigen Wert eingeben, wird das Sternchen in ein grünes Häkchen geändert.

Erstellen Sie wie folgt in Azure Stack ein VNet:

* Wiederholen Sie die vorherigen Schritte (1 bis 4) mit dem Azure Stack-**Mandantenportal**.

## <a name="add-a-gateway-subnet"></a>Hinzufügen eines Gatewaysubnetzes

Bevor Sie das virtuelle Netzwerk mit einem Gateway verbinden, müssen Sie das Gatewaysubnetz für das virtuelle Netzwerk erstellen, mit dem Sie eine Verbindung herstellen möchten. Für die Gatewaydienste werden die IP-Adressen verwendet, die Sie im Gatewaysubnetz angeben.

Navigieren Sie im  [Azure-Portal](https://portal.azure.com/) zum virtuellen Resource Manager-Netzwerk, in dem Sie ein virtuelles Netzwerkgateway erstellen möchten.

1. Wählen Sie das VNet aus, um die Seite **Virtuelles Netzwerk** zu öffnen.
2. Wählen Sie in  **EINSTELLUNGEN** die Option  **Subnetze** aus.
3. Wählen Sie auf der Seite  **Subnetze**  die Option  **+Gatewaysubnetz**  aus, um die Seite  **Subnetz hinzufügen** zu öffnen.

    ![Gatewaysubnetz hinzufügen](media/azure-stack-solution-hybrid-connectivity/image4.png)

4. Als  **Name**  für das Subnetz wird automatisch der Wert „GatewaySubnet“ eingefügt. Dieser Wert ist erforderlich, damit Azure das Subnetz als Gatewaysubnetz erkennt.
5. Passen Sie die angegebenen Werte für **Adressbereich** an Ihre Konfigurationsanforderungen an, und wählen Sie dann  **OK**.

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>Erstellen eines Gateways für virtuelle Netzwerke in Azure und Azure Stack

Führen Sie die folgenden Schritte aus, um in Azure ein Gateway für virtuelle Netzwerke zu erstellen.

1. Wählen Sie im Portal auf der linken Seite die Option **+**  , und geben Sie im Suchfeld als Suchbegriff „Gateway für virtuelle Netzwerke“ ein.
2. Wählen Sie in  **Ergebnisse** die Option  **Gateway für virtuelle Netzwerke** aus.
3. Wählen Sie unter **Gateway für virtuelle Netzwerke** die Option  **Erstellen** aus, um die Seite  **Gateway für virtuelle Netzwerke erstellen**  zu öffnen.
4. Geben Sie unter **Gateway für virtuelle Netzwerke erstellen** die Werte für Ihr Netzwerkgateway an, wie unter **Beispielwerte für Tutorial** gezeigt, und geben Sie dann die folgenden weiteren Werte an:

   - **SKU:** Basic
   - **Virtuelles Netzwerk**: Wählen Sie das zuvor erstellte virtuelle Netzwerk aus. Das von Ihnen erstellte Gatewaysubnetz wird automatisch ausgewählt.
   - **Erste IP-Konfiguration**:  Dies ist die öffentliche IP-Adresse Ihres Gateways.
     - Wählen Sie **Gateway-IP-Konfiguration erstellen**. Sie gelangen auf die Seite **Öffentliche IP-Adresse wählen**.
     - Wählen Sie **+Neu erstellen**, um die Seite **Öffentliche IP-Adresse erstellen** zu öffnen.
     - Geben Sie unter **Name** einen Namen für die öffentliche IP-Adresse ein. Behalten Sie für die SKU die Einstellung **Basic** bei, und wählen Sie dann **OK**, um Ihre Änderungen zu speichern.

       > [!Note]
       > Derzeit unterstützt VPN Gateway nur die dynamische Zuweisung öffentlicher IP-Adressen. Dies bedeutet aber nicht, dass sich die IP-Adresse ändert, nachdem sie Ihrem VPN-Gateway zugewiesen wurde. Die öffentliche IP-Adresse ändert sich nur, wenn das Gateway gelöscht und neu erstellt wird. Die IP-Adresse ändert sich nicht, wenn die Größe geändert wird, das VPN-Gateway zurückgesetzt wird oder andere interne Wartungs-/Upgradevorgänge für das VPN-Gateway durchgeführt werden.

4. Überprüfen Sie Ihre Gatewayeinstellungen.
5. Wählen Sie  **Erstellen** , um das VPN-Gateway zu erstellen. Die Gatewayeinstellungen werden überprüft, und die Kachel „Gateway des virtuellen Netzwerks wird bereitgestellt“ wird auf Ihrem Dashboard angezeigt.

   >[!Note]
   >Die Erstellung eines Gateways kann bis zu 45 Minuten dauern. Unter Umständen müssen Sie die Portalseite aktualisieren, um den Status „Abgeschlossen“ anzuzeigen.

    Nach der Erstellung des Gateways wird die zugewiesene IP-Adresse unter dem virtuellen Netzwerk im Portal angezeigt. Das Gateway wird als verbundenes Gerät angezeigt. Wählen Sie das Gerät aus, um weitere Informationen zum Gateway anzuzeigen.

6. Wiederholen Sie die vorherigen Schritte (1 - 5) für Ihre Azure Stack-Bereitstellung.

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack"></a>Erstellen des lokalen Netzwerkgateways in Azure und Azure Stack

Mit dem Gateway des lokalen Netzwerks ist normalerweise Ihr lokaler Standort gemeint. Sie geben dem Standort einen Namen, auf den von Azure oder Azure Stack verwiesen werden kann, und geben dann Folgendes an:

- Die IP-Adresse des lokalen VPN-Geräts, für das Sie eine Verbindung erstellen.
- Die IP-Adresspräfixe, die über das VPN-Gateway an das VPN-Gerät weitergeleitet werden. Die von Ihnen angegebenen Adresspräfixe befinden sich in Ihrem lokalen Netzwerk.

  >[!Note]
  >Wenn am lokalen Netzwerk Änderungen vorgenommen werden oder Sie die öffentliche IP-Adresse des VPN-Geräts ändern müssen, können Sie diese Werte später bequem aktualisieren.

1. Wählen Sie im Portal die Option  **+Ressource erstellen** aus.
2. Geben Sie im Suchfeld den Text  **Gateway für lokales Netzwerk** ein, und drücken Sie dann die  **EINGABETASTE** , um die Suche zu starten. Daraufhin wird eine Liste mit Ergebnissen zurückgegeben.
3. Wählen Sie  **Gateway für lokales Netzwerk** und anschließend **Erstellen** , um die Seite  **Lokales Netzwerkgateway erstellen**  zu öffnen.
4. Geben Sie unter **Lokales Netzwerkgateway erstellen** die Werte für Ihr Gateway für das lokale Netzwerk an, indem Sie **Beispielwerte für Tutorial** verwenden. Fügen Sie die folgenden zusätzlichen Werte ein.

    - **IP-Adresse**: Die öffentliche IP-Adresse des VPN-Geräts, mit dem Azure oder Azure Stack eine Verbindung herstellen soll. Geben Sie eine gültige öffentliche IP-Adresse an, die nicht hinter einer Netzwerkadressenübersetzung angeordnet ist, damit Azure die Adresse erreichen kann. Falls Sie die IP-Adresse gerade nicht zur Hand haben, können Sie einen Wert aus dem Beispiel als Platzhalter verwenden. In diesem Fall müssen Sie allerdings später den Platzhalter durch die öffentliche IP-Adresse Ihres VPN-Geräts ersetzen. Azure kann erst dann eine Verbindung mit dem Gerät herstellen, wenn Sie eine gültige Adresse angeben.
    - **Adressraum**: Dies ist der Adressbereich für das Netzwerk, das dieses lokale Netzwerk darstellt. Sie können mehrere Adressraumbereiche hinzufügen. Achten Sie darauf, dass sich die angegebenen Bereiche nicht mit den Bereichen anderer Netzwerke überschneiden, mit denen Sie eine Verbindung herstellen möchten. Azure leitet den Adressbereich, den Sie angeben, an die lokale IP-Adresse des VPN-Geräts weiter. Verwenden Sie Ihre eigenen Werte, wenn Sie eine Verbindung mit Ihrem lokalen Standort herstellen möchten, und keinen Beispielwert.
    - **BGP-Einstellungen konfigurieren**: Nur beim Konfigurieren von BGP verwenden. Lassen Sie sie andernfalls deaktiviert.
    - **Abonnement**: Vergewissern Sie sich, dass das richtige Abonnement angezeigt wird.
    - **Ressourcengruppe**: Wählen Sie die Ressourcengruppe aus, die Sie verwenden möchten. Sie können entweder eine neue Ressourcengruppe erstellen oder eine auswählen, die Sie bereits erstellt haben.
    - **Standort**: Wählen Sie den Standort aus, an dem dieses Objekt erstellt wird. Es empfiehlt sich unter Umständen, den gleichen Ort auszuwählen, an dem sich auch Ihr VNet befindet, aber dies ist nicht zwingend erforderlich.
5. Wenn Sie mit dem Angeben der erforderlichen Werte fertig sind, wählen Sie **Erstellen** , um das Gateway für das lokale Netzwerk zu erstellen.
6. Wiederholen Sie diese Schritte (1 - 5) für Ihre Azure Stack-Bereitstellung.

## <a name="configure-your-connection"></a>Konfigurieren der Verbindung

Für Site-to-Site-Verbindungen mit einem lokalen Netzwerk ist ein VPN-Gerät erforderlich. Das von Ihnen konfigurierte VPN-Gerät wird als „Verbindung“ bezeichnet. Sie benötigen Folgendes, um die Verbindung zu konfigurieren:

- Einen gemeinsam verwendeten Schlüssel. Dies ist derselbe gemeinsame Schlüssel, den Sie beim Erstellen Ihrer Site-to-Site-VPN-Verbindung angeben. In unseren Beispielen verwenden wir einen einfachen gemeinsamen Schlüssel. Es wird empfohlen, einen komplexeren Schlüssel zu generieren.
- Die öffentliche IP-Adresse Ihres Gateways für virtuelle Netzwerke. Sie können die öffentliche IP-Adresse mit dem Azure-Portal, mit PowerShell oder mit der CLI anzeigen. Die öffentliche IP-Adresse Ihres VPN-Gateways können Sie über das Azure-Portal ermitteln, indem Sie zu „Gateways für virtuelle Netzwerke“ navigieren und auf den Namen Ihres Gateways klicken.

Führen Sie die folgenden Schritte aus, um eine Site-to-Site-VPN-Verbindung zwischen dem Gateway des virtuellen Netzwerks und Ihrem lokalen VPN-Gerät herzustellen.

1. Klicken Sie im Azure-Portal auf  **+ Ressource erstellen**.
2. Suchen Sie nach **Verbindungen**.
3. Wählen Sie unter **Ergebnisse** die Option  **Verbindungen** aus.
4. Wählen Sie unter **Verbindung** die Option **Erstellen**.
5. Konfigurieren Sie unter **Verbindung erstellen** die folgenden Einstellungen:

    - **Verbindungstyp**: Wählen Sie „Site-to-Site (IPsec)“ aus.
    - **Ressourcengruppe**: Wählen Sie Ihre Testressourcengruppe aus.
    - **Gateway für virtuelle Netzwerke**: Wählen Sie das erstellte Gateway für virtuelle Netzwerke aus.
    - **Lokales Netzwerkgateway**: Wählen Sie das erstellte lokale Netzwerkgateway aus.
    - **Verbindungsname**: Dieses Feld wird automatisch mit den Werten der beiden Gateways ausgefüllt.
    - **Gemeinsam verwendeter Schlüssel**: Dieser Wert muss dem Wert entsprechen, den Sie für Ihr lokales VPN-Gerät verwenden. Im Tutorialbeispiel wird „abc123“ verwendet. Sie können (und sollten) allerdings einen komplexeren Wert verwenden. Entscheidend ist Folgendes: Dieser Wert MUSS dem Wert entsprechen, den Sie beim Konfigurieren Ihres VPN-Geräts angeben.
    - Die Werte für **Abonnement**, **Ressourcengruppe** und **Standort** sind festgelegt.

6. Wählen Sie **OK**, um die Verbindung zu erstellen.

Die Verbindung wird auf der Seite  **Verbindungen**  des Gateways für virtuelle Netzwerke angezeigt. Der Status wechselt von  *Unbekannt*  zu  *Verbindung wird hergestellt* und dann zu  *Erfolgreich*.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Azure-Cloudmustern finden Sie unter [Cloudentwurfsmuster](https://docs.microsoft.com/azure/architecture/patterns).
