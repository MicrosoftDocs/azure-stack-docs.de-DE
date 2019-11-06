---
title: Einrichten eines Multi-Site-to-Site-VPN-Tunnels in Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen Multi-Site-to-Site-VPN-Tunnel in Azure Stack einrichten.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 09/19/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/19/2019
ms.openlocfilehash: d85de1892e2e6620249ff3a95ee2debb01b81981
ms.sourcegitcommit: cc3534e09ad916bb693215d21ac13aed1d8a0dde
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73168283"
---
# <a name="how-to-set-up-a-multiple-site-to-site-vpn-tunnel-in-azure-stack"></a>Einrichten eines Multi-Site-to-Site-VPN-Tunnels in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

In diesem Artikel wird erläutert, wie Sie die Lösung mit einer Azure Stack-Resource Manager-Vorlage bereitstellen können. Mit der Lösung werden mehrere Ressourcengruppen mit zugeordneten virtuellen Netzwerken und Informationen zum Verbinden dieser Systeme erstellt.

Die Vorlagen finden Sie im GitHub-Repository [Azure Intelligent Edge Patterns](https://github.com/Azure-Samples/azure-intelligent-edge-patterns). Die Vorlage befindet sich im Ordner **rras-gre-vnet-vnet**. 

## <a name="scenarios"></a>Szenarien

![](./media/azure-stack-network-howto-vpn-tunnel/scenarios.png)

## <a name="create-multiple-vpn-tunnels"></a>Erstellen mehrerer VPN-Tunnel

![](./media/azure-stack-network-howto-vpn-tunnel/image1.png)

-  Stellen Sie eine Anwendung mit drei Ebenen bereit: Webebene (WebTier), Anwendungsebene (AppTier) und Datenbankebene (DBTier).

-  Stellen Sie die ersten zwei Vorlagen auf separaten Azure Stack-Instanzen bereit.

-  **WebTier** wird auf PPE1 bereitgestellt und **AppTier** auf PPE2.

-  Verbinden Sie **WebTier** und **AppTier** mit einem IKE-Tunnel.

-  Verbinden Sie **AppTier** mit einem lokalen System, das Sie mit **DBTier** benennen.

## <a name="steps-to-deploy-multiple-vpns"></a>Schritte zum Bereitstellen mehrerer VPNs

Hierbei handelt es sich um einen Vorgang mit mehreren Schritten. Für diese Lösung verwenden Sie das Azure Stack-Portal. Sie können jedoch PowerShell, die Azure-Befehlszeilenschnittstelle oder andere Infrastructure-as-Code-Toolketten verwenden, um die Ausgaben zu erfassen und dann als Eingaben zu verwenden.

![alt text](./media/azure-stack-network-howto-vpn-tunnel/image2.png)

## <a name="walkthrough"></a>Exemplarische Vorgehensweise

### <a name="deploy-web-tier-to-azure-stack-instances-ppe1"></a>Bereitstellen der Webebene auf der Azure Stack-Instanz PPE1

1.  Öffnen Sie das Azure Stack-Benutzerportal, und wählen Sie **Ressource erstellen** aus.

2.  Wählen Sie **Vorlagenbereitstellung** aus.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image3.png)

3.  Kopieren Sie den Inhalt der Datei „azuredeploy.json“ aus dem Repository **azure-intelligent-edge-patterns/rras-vnet-vpntunnel**, und fügen Sie ihn in das Vorlagenfenster ein. Die in der Vorlage enthaltenen Ressourcen werden angezeigt. Wählen Sie **Speichern** aus.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image4.png)

4.  Geben Sie einen Namen für die **Ressourcengruppe** ein, und überprüfen Sie die Parameter.

    > ![Hinweis]  
    > Der WebTier-Adressraum ist **10.10.0.0/16**, und Sie können sehen, dass **PPE1** der Speicherort der Ressourcengruppe ist.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image5.png)

### <a name="deploy-app-tier-to-the-second-azure-stack-instances"></a>Bereitstellen der Anwendungsebene auf der zweiten Azure Stack-Instanz

Sie können wie bei der Bereitstellung von **WebTier** vorgehen. Es werden jedoch andere Parameter verwendet, wie im Folgenden gezeigt:

> [!Note]  
> Der AppTier-Adressraum ist **10.20.0.0/16**, und Sie können sehen, dass **WestUS2** der Speicherort der Ressourcengruppe ist.

![](./media/azure-stack-network-howto-vpn-tunnel/image6.png)

### <a name="review-the-deployments-for-web-tier-and-app-tier-and-capture-outputs"></a>Überprüfen der Bereitstellungen für die Web- und die Anwendungsebene und Erfassen der Ausgaben

1.  Überprüfen Sie, ob die Bereitstellung erfolgreich abgeschlossen wurde. Wählen Sie **Ausgaben** aus.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image7.png)

3.  Kopieren Sie die ersten vier Werte in Ihre Editor-Anwendung.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image8.png)

5.  Wiederholen Sie diese Schritte für die Bereitstellung für **AppTier**.

![](./media/azure-stack-network-howto-vpn-tunnel/image9.png)

![](./media/azure-stack-network-howto-vpn-tunnel/image10.png)

### <a name="create-tunnel-from-web-tier-to-app-tier"></a>Erstellen eines Tunnels von der Webebene zur Anwendungsebene

1.  Öffnen Sie das Azure Stack-Benutzerportal, und wählen Sie **Ressource erstellen** aus.

2.  Wählen Sie **Vorlagenbereitstellung** aus.

3.  Fügen Sie den Inhalt aus **azuredeploy.tunnel.ike.json** ein.

4.  Wählen Sie **Parameter bearbeiten** aus.

![](./media/azure-stack-network-howto-vpn-tunnel/image11.png)

### <a name="create-tunnel-from-app-tier-to-web-tier"></a>Erstellen eines Tunnels von der Anwendungsebene zur Webebene

1.  Öffnen Sie das Azure Stack-Benutzerportal, und wählen Sie **Ressource erstellen** aus.

2.  Wählen Sie **Vorlagenbereitstellung** aus.

3.  Fügen Sie den Inhalt aus **azuredeploy.tunnel.ike.json** ein.

4.  Wählen Sie **Parameter bearbeiten** aus.

![](./media/azure-stack-network-howto-vpn-tunnel/image12.png)

### <a name="viewing-tunnel-deployment"></a>Anzeigen der Tunnelbereitstellung

Wenn Sie die Ausgabe über die benutzerdefinierte Skripterweiterung anzeigen, können Sie sehen, wie der Tunnel erstellt wird. Außerdem sollte der Status angezeigt werden. Sie sehen, dass auf der einen Seite der Status **Connecting** (Verbindung wird hergestellt) angezeigt und auf die Bereitstellung der anderen Seite gewartet wird. Für die andere Seite wird nach erfolgter Bereitstellung der Status **Connected** (Verbunden) angezeigt.

![](./media/azure-stack-network-howto-vpn-tunnel/image13.png)

![](./media/azure-stack-network-howto-vpn-tunnel/image14.png)

![](./media/azure-stack-network-howto-vpn-tunnel/image15.png)

### <a name="troubleshooting-on-the-rras-vm"></a>Beheben von Problemen mit dem virtuellen RRAS-Computer

1.  Ändern Sie die RDP-Regel von **Verweigern** in **Zulassen**.

2.  Stellen Sie über einen Remotedesktopclient (DRP) mit den bei der Bereitstellung festgelegten Anmeldeinformationen eine Verbindung mit dem System her.

3.  Öffnen Sie PowerShell über eine Eingabeaufforderung mit erhöhten Rechten, und führen Sie `get-VPNS2SInterface` aus.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image16.png)

5.  Verwalten Sie das System mithilfe des Cmdlets **RemoteAccess**.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image17.png)

### <a name="install-rras-on-a-on-premises-vm-db-tier"></a>Installieren von RRAS in der Datenbankebene eines lokalen virtuellen Computers

1.  Das Ziel ist ein Windows 2016-Image.

2.  Wenn Sie das Skript `Add-Site2SiteIKE.ps1` aus dem Repository kopieren und lokal ausführen, werden mit dem Skript **WindowsFeature** und **RemoteAccess** installiert.

    > [!Note]
    > Abhängig von Ihrer Umgebung müssen Sie möglicherweise das System neu starten.

    Entsprechende Informationen finden Sie in der Netzwerkkonfiguration des lokalen Computers.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image18.png)

3.  Führen Sie das Skript aus, und fügen Sie die in der AppTier-Vorlagenbereitstellung erfassten Parameter für die **Ausgabe** hinzu.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image19.png)

5.  Der Tunnel ist damit konfiguriert und wartet auf die AppTier-Verbindung.

### <a name="configure-app-tier-to-db-tier"></a>Konfigurieren der Verbindung von der Anwendungsebene zur Datenbankebene

1.  Öffnen Sie das Azure Stack-Benutzerportal, und wählen Sie **Ressource erstellen** aus.

2.  Wählen Sie **Vorlagenbereitstellung** aus.

3.  Fügen Sie den Inhalt aus **azuredeploy.tunnel.ike.json** ein.

4.  Wählen Sie **Parameter bearbeiten** aus.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image20.png)

5.  Vergewissern Sie sich, dass „AppTier“ ausgewählt ist und das interne Remotenetzwerk unter „10.99.0.1“ festgelegt ist.

### <a name="confirm-tunnel-between-app-tier-and-db-tier"></a>Überprüfen des Tunnels zwischen der Anwendungsebene und der Datenbankebene

1.  Führen Sie eine benutzerdefinierte Skripterweiterung aus, um den Tunnel zu überprüfen, ohne sich bei dem virtuellen Computer anzumelden.

2.  Wechseln Sie zum virtuellen RRAS-Computer (AppTier).

3.  Wählen Sie **Erweiterungen** aus, und führen Sie die **benutzerdefinierte Skripterweiterung** aus.

4.  Navigieren Sie im Repository **azure-intelligent-edge-patterns/rras-vnet-vpntunnel** zum Skriptverzeichnis. Wählen Sie **Get-VPNS2SInterfaceStatus.ps1** aus.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image21.png)

5.  Wenn Sie RDP aktivieren und sich anmelden, dann PowerShell öffnen und `get-vpns2sinterface` ausführen, können Sie sehen, dass der Tunnel verbunden ist.

    **DBTier**

    ![](./media/azure-stack-network-howto-vpn-tunnel/image22.png)

    **AppTier**

    ![](./media/azure-stack-network-howto-vpn-tunnel/image23.png)

    > [!Note]  
    > Sie können die RDP-Verbindung sowohl vom ersten zum zweiten Computer als auch vom zweiten zum ersten Computer testen.

    > [!Note]  
    > Um diese Lösung lokal zu implementieren, müssen Sie Routen für das Azure Stack-Remotenetzwerk in der Switchinginfrastruktur oder zumindest auf spezifischen virtuellen Computern bereitstellen.

### <a name="deploying-a-gre-tunnel"></a>Bereitstellen eines GRE-Tunnels

Bei dieser exemplarischen Vorgehensweise wurde die [IKE-Vorlage](network-howto-vpn-tunnel-ipsec.md) verwendet. Sie können jedoch auch einen [GRE-Tunnel](network-howto-vpn-tunnel-gre.md) bereitstellen. Dieser Tunnel bietet einen höheren Durchsatz.

Der Prozess ist nahezu identisch. Wenn Sie jedoch die Tunnelvorlage in der vorhandenen Infrastruktur bereitstellen, müssen Sie die Ausgaben aus dem anderen System für die ersten drei Eingaben verwenden. Sie müssen den Wert von **LOCALTUNNELGATEWAY** für die Ressourcengruppe kennen, in der die Bereitstellung erfolgt, und nicht für die Ressourcengruppe, mit der eine Verbindung hergestellt werden soll.

![](./media/azure-stack-network-howto-vpn-tunnel/image24.png)

## <a name="next-steps"></a>Nächste Schritte

[Azure Stack-Netzwerke: Unterschiede und Überlegungen](azure-stack-network-differences.md)  
[Erstellen eines VPN-Tunnels mithilfe von GRE](network-howto-vpn-tunnel-gre.md)  
[Erstellen eines VPN-Tunnels mithilfe von IPsec](network-howto-vpn-tunnel-ipsec.md)