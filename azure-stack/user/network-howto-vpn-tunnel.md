---
title: Einrichten eines Multi-Site-to-Site-VPN-Tunnels in Azure Stack Hub
description: Erfahren Sie, wie Sie einen Multi-Site-to-Site-VPN-Tunnel in Azure Stack Hub einrichten.
author: mattbriggs
ms.topic: how-to
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: c7462bebc919b85db64dae9c4a07fdc1ca276e33
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525777"
---
# <a name="how-to-set-up-multiple-site-to-site-vpn-tunnels-in-azure-stack-hub"></a>Einrichten von mehreren Site-to-Site-VPN-Tunneln in Azure Stack Hub

In diesem Artikel wird erläutert, wie Sie die Lösung mit einer Azure Stack Hub-Resource Manager-Vorlage bereitstellen können. Mit der Lösung werden mehrere Ressourcengruppen mit zugeordneten virtuellen Netzwerken und Informationen zum Verbinden dieser Systeme erstellt.

Die Vorlagen finden Sie im GitHub-Repository [Azure Intelligent Edge Patterns](https://github.com/Azure-Samples/azure-intelligent-edge-patterns). Die Vorlage befindet sich im Ordner **rras-gre-vnet-vnet**. 

## <a name="scenarios"></a>Szenarien

![Es werden fünf VPN-Szenarien dargestellt: zwischen zwei Ressourcengruppen innerhalb eines einzelnen Abonnements, zwischen zwei Gruppen in jeweils einem eigenen Abonnement, zwischen zwei Gruppen in separaten Stack-Instanzen, zwischen einer Gruppe und lokalen Ressourcen und zwischen mehreren VPN-Tunneln.](./media/azure-stack-network-howto-vpn-tunnel/scenarios.svg)

## <a name="create-multiple-vpn-tunnels"></a>Erstellen mehrerer VPN-Tunnel

![Das Diagramm zeigt zwei Ressourcengruppen, jede in ihrer eigenen Abonnement- und Stack-Instanz, die über VPN verbunden sind. Und eine dieser beiden Gruppen ist über VPN mit lokalen Ressourcen verbunden.](./media/azure-stack-network-howto-vpn-tunnel/azure-stack-network-vpn-tunnel1.svg)

-  Stellen Sie eine Anwendung mit drei Ebenen bereit: Webebene (WebTier), Anwendungsebene (AppTier) und Datenbankebene (DBTier).

-  Stellen Sie die ersten zwei Vorlagen auf separaten Azure Stack Hub-Instanzen bereit.

-  **WebTier** wird auf PPE1 bereitgestellt und **AppTier** auf PPE2.

-  Verbinden Sie **WebTier** und **AppTier** mit einem IKE-Tunnel.

-  Verbinden Sie **AppTier** mit einem lokalen System, das Sie mit **DBTier** benennen.

## <a name="steps-to-deploy-multiple-vpns"></a>Schritte zum Bereitstellen mehrerer VPNs

Hierbei handelt es sich um einen Vorgang mit mehreren Schritten. Für diese Lösung verwenden Sie das Azure Stack Hub-Portal. Sie können jedoch PowerShell, die Azure-Befehlszeilenschnittstelle oder andere Infrastructure-as-Code-Toolketten verwenden, um die Ausgaben zu erfassen und dann als Eingaben zu verwenden.

![Das Diagramm zeigt fünf Schritte zur Bereitstellung von VPN-Tunneln zwischen zwei Infrastrukturen an. In den ersten beiden Schritten werden aus einer Vorlage zwei Infrastrukturen erstellt. In den nächsten beiden Schritten werden zwei VPN-Tunnel aus einer Vorlage erstellt, und im letzten Schritt werden die Tunnel miteinander verbunden.](./media/azure-stack-network-howto-vpn-tunnel/azure-stack-network-vpn-tunnel2.svg)

## <a name="walkthrough"></a>Exemplarische Vorgehensweise

### <a name="deploy-web-tier-to-azure-stack-hub-instances-ppe1"></a>Bereitstellen der Webebene auf der Azure Stack Hub-Instanz PPE1

1. Öffnen Sie das Azure Stack Hub-Benutzerportal, und wählen Sie **Ressource erstellen** aus.

2. Wählen Sie **Vorlagenbereitstellung** aus.

    ![Das Dialogfeld „Dashboard > Neu“ zeigt verschiedene Optionen an. Die Schaltfläche für die Vorlagenbereitstellung ist hervorgehoben.](./media/azure-stack-network-howto-vpn-tunnel/image3.png)

3. Kopieren Sie den Inhalt der Datei „azuredeploy.json“ aus dem Repository **azure-intelligent-edge-patterns/rras-vnet-vpntunnel**, und fügen Sie ihn in das Vorlagenfenster ein. Die in der Vorlage enthaltenen Ressourcen werden angezeigt. Wählen Sie **Speichern** aus.

    ![Das Dialogfeld „Dashboard > Neu > Lösungsvorlage bereitstellen > Vorlage bearbeiten“ enthält ein Fenster, in das die Datei „azuredeploy.json“ eingefügt wird.](./media/azure-stack-network-howto-vpn-tunnel/image4.png)

4. Geben Sie einen Namen für die **Ressourcengruppe** ein, und überprüfen Sie die Parameter.

    > [!Note]  
    > Der WebTier-Adressraum ist **10.10.0.0/16**, und Sie können sehen, dass **PPE1** der Speicherort der Ressourcengruppe ist.

    ![Das Dialogfeld „Dashboard > Neu > Lösungsvorlage bereitstellen > Parameter“ hat ein Textfeld „Ressourcengruppe“ und ein Optionsfeld. Die Schaltfläche „Neu erstellen“ ist ausgewählt, und der Text lautet „WebTier“.](./media/azure-stack-network-howto-vpn-tunnel/image5.png)

### <a name="deploy-app-tier-to-the-second-azure-stack-hub-instances"></a>Bereitstellen der Anwendungsebene auf der zweiten Azure Stack Hub-Instanz

Sie können wie bei der Bereitstellung von **WebTier** vorgehen. Es werden jedoch andere Parameter verwendet, wie im Folgenden gezeigt:

> [!NOTE]  
> Der AppTier-Adressraum ist **10.20.0.0/16**, und Sie können sehen, dass **WestUS2** der Speicherort der Ressourcengruppe ist.

![Das Dialogfeld „Dashboard > Lösungsvorlage bereitstellen > Parameter“ hat ein Textfeld „Ressourcengruppe“ und ein Optionsfeld. Die Schaltfläche „Neu erstellen“ ist ausgewählt, und der Text lautet „AppTier“. Acht weitere hervorgehobene Textfelder enthalten Vorlagenparameter.](./media/azure-stack-network-howto-vpn-tunnel/image6.png)

### <a name="review-the-deployments-for-web-tier-and-app-tier-and-capture-outputs"></a>Überprüfen der Bereitstellungen für die Web- und die Anwendungsebene und Erfassen der Ausgaben

1. Überprüfen Sie, ob die Bereitstellung erfolgreich abgeschlossen wurde. Klicken Sie auf **Ausgaben**.

    ![Die Option „Ausgaben“ wird im Dialogfeld „Dashboard > Microsoft.Template – Übersicht“ hervorgehoben. Die Meldung lautet „Ihre Bereitstellung ist abgeschlossen“, gefolgt von einer Liste der Ressourcen für die Gruppe WebTier, jeweils mit Name, Typ, Status und einem Link zu Details.](./media/azure-stack-network-howto-vpn-tunnel/image7.png)

1. Kopieren Sie die ersten vier Werte in Ihre Editor-Anwendung.

    ![Das Dialogfeld lautet „Dashboard > Microsoft.Template – Ausgaben“. Die vier Textfelder, die aus der Bereitstellung auf der Webebene kopiert werden müssen, sind hervorgehoben: LOCALTUNNELENDPOINT, LOCALVNETADDRESSSPACE, LOCALVNETGATEWAY und LOCALTUNNELGATEWAY.](./media/azure-stack-network-howto-vpn-tunnel/image8.png)

1. Wiederholen Sie diese Schritte für die Bereitstellung für **AppTier**.

    ![Die Option „Ausgaben“ wird im Dialogfeld „Dashboard > Microsoft.Template – Übersicht“ hervorgehoben. Die Meldung lautet „Ihre Bereitstellung ist abgeschlossen“, gefolgt von einer Liste der Ressourcen für die Gruppe AppTier, jeweils mit Name, Typ, Status und einem Link zu Details.](./media/azure-stack-network-howto-vpn-tunnel/image9.png)

    ![Das Dialogfeld lautet „Dashboard > Microsoft.Template – Ausgaben“. Die vier Textfelder, die aus der Bereitstellung auf der App-Ebene kopiert werden müssen, sind hervorgehoben: LOCALTUNNELENDPOINT, LOCALVNETADDRESSSPACE, LOCALVNETGATEWAY und LOCALTUNNELGATEWAY.](./media/azure-stack-network-howto-vpn-tunnel/image10.png)

### <a name="create-tunnel-from-web-tier-to-app-tier"></a>Erstellen eines Tunnels von der Webebene zur Anwendungsebene

1. Öffnen Sie das Azure Stack Hub-Benutzerportal, und wählen Sie **Ressource erstellen** aus.

2. Wählen Sie **Vorlagenbereitstellung** aus.

3. Fügen Sie den Inhalt aus **azuredeploy.tunnel.ike.json** ein.

4. Wählen Sie **Parameter bearbeiten** aus.

![Das Dialogfeld „Dashboard > Neu > Lösungsvorlage bereitstellen > Parameter“ hat ein Textfeld „Ressourcengruppe“ und ein Optionsfeld. Die Schaltfläche „Vorhandene verwenden“ ist ausgewählt, und der Text lautet „WebTier“. Vier weitere hervorgehobene Textfelder enthalten Vorlagenparameter.](./media/azure-stack-network-howto-vpn-tunnel/image11.png)

### <a name="create-tunnel-from-app-tier-to-web-tier"></a>Erstellen eines Tunnels von der Anwendungsebene zur Webebene

1. Öffnen Sie das Azure Stack Hub-Benutzerportal, und wählen Sie **Ressource erstellen** aus.

2. Wählen Sie **Vorlagenbereitstellung** aus.

3. Fügen Sie den Inhalt aus **azuredeploy.tunnel.ike.json** ein.

4. Wählen Sie **Parameter bearbeiten** aus.

![Das Dialogfeld „Dashboard > Neu > Lösungsvorlage bereitstellen > Parameter“ hat ein Textfeld „Ressourcengruppe“ und ein Optionsfeld. Die Schaltfläche „Vorhandene verwenden“ ist ausgewählt, und der Text lautet „AppTier“. Vier weitere hervorgehobene Textfelder enthalten Vorlagenparameter.](./media/azure-stack-network-howto-vpn-tunnel/image12.png)

### <a name="viewing-tunnel-deployment"></a>Anzeigen der Tunnelbereitstellung

Wenn Sie die Ausgabe über die benutzerdefinierte Skripterweiterung anzeigen, können Sie sehen, wie der Tunnel erstellt wird. Außerdem sollte der Status angezeigt werden. Sie sehen, dass auf der einen Seite der Status **Connecting** (Verbindung wird hergestellt) angezeigt und auf die Bereitstellung der anderen Seite gewartet wird. Für die andere Seite wird nach erfolgter Bereitstellung der Status **Connected** (Verbunden) angezeigt.

![Das Dialogfeld lautet „Dashboard > Ressourcengruppen > WebTier > WebTier-RRAS – Erweiterungen“. Es sind zwei Erweiterungen aufgeführt: CustomScriptExtension, die hervorgehoben ist, und InstallRRAS. Beide zeigen den Status „Bereitstellung erfolgreich“ an.](./media/azure-stack-network-howto-vpn-tunnel/image13.png)

![Das Dialogfeld lautet „Dashboard > Ressourcengruppen > WebTier > WebTier-RRAS – Erweiterungen > CustomScriptExtension“. Der DETAILED STATUS-Wert ist „Bereitstellung erfolgreich“.](./media/azure-stack-network-howto-vpn-tunnel/image14.png)

![Die Ausgaben der benutzerdefinierten Skripterweiterungen für AppTier und WebTier werden in nebeneinanderliegenden Notepad-Fenstern angezeigt. AppTier zeigt einen Tunnelstatus „Verbindung wird hergestellt“ an. WebTier zeigt „Verbunden“ an.](./media/azure-stack-network-howto-vpn-tunnel/image15.png)

### <a name="troubleshooting-on-the-rras-vm"></a>Beheben von Problemen mit dem virtuellen RRAS-Computer

1. Ändern Sie die Regel für Remotedesktops (RDP) von **Verweigern** in **Zulassen**.

1. Stellen Sie über einen RDP-Client mit den bei der Bereitstellung festgelegten Anmeldeinformationen eine Verbindung mit dem System her.

1. Öffnen Sie PowerShell über eine Eingabeaufforderung mit erhöhten Rechten, und führen Sie `get-VpnS2SInterface` aus.

    ![Das PowerShell-Fenster zeigt die Ausführung des Befehls Get-VpnS2SInterface, der Details der Site-to-Site-Schnittstelle an. ConnectionState ist „Verbunden“.](./media/azure-stack-network-howto-vpn-tunnel/image16.png)

1. Verwalten Sie das System mithilfe des Cmdlets **RemoteAccess**.

    ![Auf der App-Ebene läuft ein cmd.exe-Fenster, das die Ausgabe des Befehls ipconfig anzeigt. Zwei Fenster werden als Überlagerungen angezeigt: Ein RDP-Verbindungsfenster, das eine Verbindung mit der Webebene herstellt, und ein Windows-Sicherheitsfenster, das eine Verbindung mit der Webebene herstellt.](./media/azure-stack-network-howto-vpn-tunnel/image17.png)

### <a name="install-rras-on-an-on-premises-vm-db-tier"></a>Installieren von RRAS in der Datenbankebene eines lokalen virtuellen Computers

1. Das Ziel ist ein Windows 2016-Image.

1. Wenn Sie das Skript `Add-Site2SiteIKE.ps1` aus dem Repository kopieren und lokal ausführen, werden mit dem Skript **WindowsFeature** und **RemoteAccess** installiert.

    > [!NOTE]
    > Abhängig von Ihrer Umgebung müssen Sie möglicherweise das System neu starten.

    Entsprechende Informationen finden Sie in der Netzwerkkonfiguration des lokalen Computers.

    ![Es gibt ein cmd.exe-Fenster, das die Ausgabe von ipconfig anzeigt, und ein Fenster für das Netzwerk- und Freigabecenter.](./media/azure-stack-network-howto-vpn-tunnel/image18.png)

1. Führen Sie das Skript aus, und fügen Sie die in der AppTier-Vorlagenbereitstellung erfassten Parameter für die **Ausgabe** hinzu.

    ![Das Skript „Add-Site2SiteIKE.psa“ wird in einem PowerShell-Fenster ausgeführt, und die Ausgabe wird angezeigt.](./media/azure-stack-network-howto-vpn-tunnel/image19.png)

1. Der Tunnel ist damit konfiguriert und wartet auf die AppTier-Verbindung.

### <a name="configure-app-tier-to-db-tier"></a>Konfigurieren der Verbindung von der Anwendungsebene zur Datenbankebene

1. Öffnen Sie das Azure Stack Hub-Benutzerportal, und wählen Sie **Ressource erstellen** aus.

2. Wählen Sie **Vorlagenbereitstellung** aus.

3. Fügen Sie den Inhalt aus **azuredeploy.tunnel.ike.json** ein.

4. Wählen Sie **Parameter bearbeiten** aus.

    ![Das Dialogfeld „Dashboard > Neu > Lösungsvorlage bereitstellen > Parameter“ hat ein Textfeld „Ressourcengruppe“ und ein Optionsfeld. Die Schaltfläche „Vorhandene verwenden“ ist ausgewählt, und der Text lautet „AppTier“. Drei weitere hervorgehobene Textfelder enthalten Vorlagenparameter.](./media/azure-stack-network-howto-vpn-tunnel/image20.png)

5. Vergewissern Sie sich, dass „AppTier“ ausgewählt ist und das interne Remotenetzwerk unter „10.99.0.1“ festgelegt ist.

### <a name="confirm-tunnel-between-app-tier-and-db-tier"></a>Überprüfen des Tunnels zwischen der Anwendungsebene und der Datenbankebene

1. Führen Sie eine benutzerdefinierte Skripterweiterung aus, um den Tunnel zu überprüfen, ohne sich bei dem virtuellen Computer anzumelden.

2. Wechseln Sie zum virtuellen RRAS-Computer (AppTier).

3. Wählen Sie **Erweiterungen** aus, und führen Sie die **benutzerdefinierte Skripterweiterung** aus.

4. Navigieren Sie im Repository **azure-intelligent-edge-patterns/rras-vnet-vpntunnel** zum Skriptverzeichnis. Wählen Sie **Get-VPNS2SInterfaceStatus.ps1** aus.

    ![Das Dialogfeld lautet „Dashboard > Ressourcengruppen > AppTier > AppTier-RRAS – Erweiterungen > CustomScriptExtension“. Der DETAILED STATUS-Wert ist „Bereitstellung erfolgreich“. Details befinden sich in einer Notepad-Fensterüberlagerung.](./media/azure-stack-network-howto-vpn-tunnel/image21.png)

5. Wenn Sie RDP aktivieren und sich anmelden, dann PowerShell öffnen und `get-vpns2sinterface` ausführen, können Sie sehen, dass der Tunnel verbunden ist.

    **DBTier**

    ![Auf DBTier zeigt das PowerShell-Fenster die Ausführung des Befehls Get-VpnS2SInterface, der Details der Site-to-Site-Schnittstelle an. ConnectionState ist „Verbunden“.](./media/azure-stack-network-howto-vpn-tunnel/image22.png)

    **AppTier**

    ![Auf AppTier zeigt das PowerShell-Fenster die Ausführung des Befehls Get-VpnS2SInterface, der Details der Site-to-Site-Schnittstelle an. ConnectionState ist für zwei Ziele „Verbunden“.](./media/azure-stack-network-howto-vpn-tunnel/image23.png)

    > [!NOTE]  
    > Sie können die RDP-Verbindung sowohl vom ersten zum zweiten Computer als auch vom zweiten zum ersten Computer testen.

    > [!NOTE]  
    > Um diese Lösung lokal zu implementieren, müssen Sie Routen für das Azure Stack Hub-Remotenetzwerk in der Switchinginfrastruktur oder zumindest auf spezifischen virtuellen Computern bereitstellen.

### <a name="deploying-a-gre-tunnel"></a>Bereitstellen eines GRE-Tunnels

Bei dieser exemplarischen Vorgehensweise wurde die [IKE-Vorlage](network-howto-vpn-tunnel-ipsec.md) verwendet. Sie können jedoch auch einen [GRE-Tunnel](network-howto-vpn-tunnel-gre.md) bereitstellen. Dieser Tunnel bietet einen höheren Durchsatz.

Der Prozess ist nahezu identisch. Wenn Sie jedoch die Tunnelvorlage in der vorhandenen Infrastruktur bereitstellen, müssen Sie die Ausgaben aus dem anderen System für die ersten drei Eingaben verwenden. Sie müssen den Wert von **LOCALTUNNELGATEWAY** für die Ressourcengruppe kennen, in der die Bereitstellung erfolgt, und nicht für die Ressourcengruppe, mit der eine Verbindung hergestellt werden soll.

![Das Dialogfeld „Dashboard > Neu > Lösungsvorlage bereitstellen > Parameter“ hat ein Textfeld „Ressourcengruppe“ und ein Optionsfeld. Die Schaltfläche „Vorhandene verwenden“ ist ausgewählt, und der Text lautet „AppTier“. Vier weitere hervorgehobene Textfelder enthalten Daten aus WebTier-Ausgaben.](./media/azure-stack-network-howto-vpn-tunnel/image24.png)

## <a name="next-steps"></a>Nächste Schritte

[Azure Stack Hub-Netzwerke: Unterschiede und Überlegungen](azure-stack-network-differences.md)  
[Erstellen eines VPN-Tunnels mithilfe von GRE](network-howto-vpn-tunnel-gre.md)  
[Erstellen eines VPN-Tunnels mithilfe von IPsec](network-howto-vpn-tunnel-ipsec.md)