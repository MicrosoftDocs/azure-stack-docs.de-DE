---
title: Herstellen einer Verbindung zwischen Azure Stack Hub und Azure über ein VPN
description: Herstellen einer Verbindung zwischen virtuellen Netzwerken in Azure Stack Hub und virtuellen Netzwerken in Azure über ein VPN
author: sethmanheim
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: sethm
ms.reviewer: scottnap
ms.lastreviewed: 10/24/2019
ms.openlocfilehash: dc143c4cfc6beec14891caa7e23d3f058f49eae5
ms.sourcegitcommit: 0aa5f7f20690839661c8bb3bfdbe32f82bec0c64
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/21/2020
ms.locfileid: "86567670"
---
# <a name="connect-azure-stack-hub-to-azure-using-vpn"></a>Herstellen einer Verbindung zwischen Azure Stack Hub und Azure über ein VPN

In diesem Artikel wird das Erstellen eines Site-to-Site-VPN beschrieben, mit dem ein virtuelles Netzwerk in Azure Stack Hub mit einem virtuellen Netzwerk in Azure verbunden werden soll.

## <a name="before-you-begin"></a>Voraussetzungen

Vergewissern Sie sich vor dem Durchführen der Verbindungskonfiguration, dass Sie über folgende Elemente verfügen:

* Eine Bereitstellung (mit mehreren Knoten) für integrierte Azure Stack Hub-Systeme, die direkt mit dem Internet verbunden ist. Ihr Bereich mit den externen öffentlichen IP-Adressen muss direkt über das öffentliche Internet erreichbar sein.
* Ein gültiges Azure-Abonnement. Wenn Sie kein Azure-Abonnement haben, [können Sie hier ein kostenloses Azure-Konto erstellen](https://azure.microsoft.com/free/?b=17.06).

### <a name="vpn-connection-diagram"></a>VPN-Verbindungsdiagramm

Die folgende Abbildung zeigt die endgültige Verbindungskonfiguration:

![Konfiguration einer Site-to-Site-VPN-Verbindung](media/azure-stack-connect-vpn/azure-stack-connect-vpn-image2.svg)

### <a name="network-configuration-example-values"></a>Beispielwerte für Netzwerkkonfiguration

Die Tabelle mit den Beispielen für die Netzwerkkonfiguration enthält die Werte, die für die Beispiele in diesem Artikel verwendet werden. Sie können diese Werte verwenden oder sie zum besseren Verständnis der Beispiele in diesem Artikel heranziehen:

|   |Azure Stack Hub|Azure|
|---------|---------|---------|
|Name des virtuellen Netzwerks     |Azs-VNet|AzureVNet |
|Adressraum des virtuellen Netzwerks |10.1.0.0/16|10.100.0.0/16|
|Subnetzname     |FrontEnd|FrontEnd|
|Subnetzadressbereich|10.1.0.0/24 |10.100.0.0/24 |
|Gatewaysubnetz     |10.1.1.0/24|10.100.1.0/24|

## <a name="create-the-network-resources-in-azure"></a>Erstellen der Netzwerkressourcen in Azure

Erstellen Sie zunächst die Netzwerkressourcen für Azure. Die folgenden Anweisungen zeigen, wie die Ressourcen mithilfe des [Azure-Portals](https://portal.azure.com/) erstellt werden.

### <a name="create-the-virtual-network-and-virtual-machine-vm-subnet"></a>Erstellen des Subnetzes für das virtuelle Netzwerk und virtuelle Computer (VM)

1. Melden Sie sich mit Ihrem Azure-Konto beim [Azure-Portal](https://portal.azure.com/) an.
2. Klicken Sie im Benutzerportal auf **+ Ressource erstellen**.
3. Wechseln Sie zu **Marketplace**, und wählen Sie dann **Netzwerk** aus.
4. Wählen Sie **Virtuelles Netzwerk** aus.
5. Verwenden Sie als **Name**, **Adressraum**, **Subnetzname** und **Subnetzadressbereich** für Azure die Werte aus der Netzwerk-Konfigurationstabelle.
6. Erstellen Sie entweder eine neue **Ressourcengruppe**, oder wählen Sie **Use existing** (Vorhandene verwenden) auswählen, wenn Sie bereits über eine verfügen.
7. Wählen Sie den **Standort** Ihres virtuellen Netzwerks aus.  Wenn Sie die Beispielwerte verwenden, wählen Sie **USA, Osten** oder einen anderen Standort aus.
8. Wählen Sie die Option **An Dashboard anheften** aus.
9. Klicken Sie auf **Erstellen**.

### <a name="create-the-gateway-subnet"></a>Erstellen des Gatewaysubnetzes

1. Öffnen Sie über das Dashboard die zuvor erstellte Ressource des virtuellen Netzwerks (**AzureVNet**).
2. Wählen Sie im Bereich **Einstellungen** die Option **Subnetze** aus.
3. Klicken Sie auf **Gatewaysubnetz**, um dem virtuellen Netzwerk ein Gatewaysubnetz hinzuzufügen.
4. Der Name des Subnetzes wird standardmäßig auf **GatewaySubnet** festgelegt.

   >[!IMPORTANT]
   >Gatewaysubnetze sind spezielle Subnetze und müssen diesen spezifischen Namen besitzen, damit sie ordnungsgemäß funktionieren.

5. Vergewissern Sie sich, dass im Feld **Adressbereich** die Adresse **10.100.1.0/24** angegeben ist.
6. Wählen Sie **OK**, um das Gatewaysubnetz zu erstellen.

### <a name="create-the-virtual-network-gateway"></a>Erstellen des Gateways für das lokale Netzwerk

1. Wählen Sie im Azure-Portal die Option **+ Ressource erstellen**.

2. Wechseln Sie zu **Marketplace**, und wählen Sie dann **Netzwerk** aus.
3. Wählen Sie in der Liste mit den Netzwerkressourcen den Eintrag **Gateway des virtuellen Netzwerks** aus.
4. Geben Sie im Feld **Name** die Zeichenfolge **Azure-GW** ein.
5. Klicken Sie auf **Virtuelles Netzwerk**, um ein virtuelles Netzwerk auszuwählen. Wählen Sie anschließend aus der Liste **AzureVnet** aus.
6. Wählen Sie **Öffentliche IP-Adresse**. Wählen Sie im Bereich **Öffentliche IP-Adresse auswählen** die Option **Neu erstellen** aus.
7. Geben Sie im Feld **Name** den Namen **Azure-GW-PiP** ein, und klicken Sie auf **OK**.
8. Vergewissern Sie sich, dass **Abonnement** und **Speicherort** korrekt sind. Die Ressource kann an das Dashboard angeheftet werden. Klicken Sie auf **Erstellen**.

### <a name="create-the-local-network-gateway-resource"></a>Erstellen der Ressource des lokalen Netzwerkgateways

1. Wählen Sie im Azure-Portal die Option **+ Ressource erstellen**.

2. Wechseln Sie zu **Marketplace**, und wählen Sie dann **Netzwerk** aus.
3. Wählen Sie in der Ressourcenliste den Eintrag für das **lokale Netzwerkgateway** aus.
4. Geben Sie im Feld **Name** die Zeichenfolge **Azs-GW** ein.
5. Geben Sie im Feld **IP-Adresse** die öffentliche IP-Adresse für Ihr Gateway des virtuellen Azure Stack Hub-Netzwerks ein, die in der Netzwerkkonfigurationstabelle oben aufgelistet ist.
6. Geben Sie im Feld **Adressraum** für Azure Stack Hub den Adressraum **10.1.0.0/24** und **10.1.1.0/24** für **AzureVNet** ein.
7. Vergewissern Sie sich, dass Ihr **Abonnement**, die **Ressourcengruppe** und der **Speicherort** richtig sind, und klicken Sie auf **Erstellen**.

## <a name="create-the-connection"></a>Erstellen der Verbindung

1. Klicken Sie im Benutzerportal auf **+ Ressource erstellen**.
2. Wechseln Sie zu **Marketplace**, und wählen Sie dann **Netzwerk** aus.
3. Wählen Sie in der Ressourcenliste die Option **Verbindung** aus.
4. Wählen Sie im Einstellungsbereich **Grundlagen** unter **Verbindungstyp** die Option **Site-to-Site (IPsec)** aus.
5. Wählen Sie Werte für **Abonnement**, **Ressourcengruppe** und **Speicherort** aus, und wählen Sie anschließend **OK**.
6. Wählen Sie im Bereich **Einstellungen** die Option **Gateway des virtuellen Netzwerks** aus, und wählen Sie anschließend **Azure-GW** aus.
7. Wählen Sie **Gateway des lokalen Netzwerks** aus, und wählen Sie dann **Azs-GW** aus.
8. Geben Sie unter **Verbindungsname** den Namen **Azure-Azs** ein.
9. Geben Sie unter **Gemeinsam verwendeter Schlüssel (PSK)** den Wert **12345** ein, und wählen Sie **OK** aus.

   >[!NOTE]
   >Falls Sie einen anderen Wert für den gemeinsam verwendeten Schlüssel nutzen, sollten Sie nicht vergessen, dass er dem Wert für den gemeinsam verwendeten Schlüssel entsprechen muss, den Sie am anderen Ende der Verbindung erstellt haben.

10. Überprüfen Sie den Bereich **Zusammenfassung**, und klicken Sie auf **OK**.

## <a name="create-a-custom-ipsec-policy"></a>Erstellen einer benutzerdefinierten IPsec-Richtlinie

Da sich die Azure Stack Hub-Standardparameter für IPsec-Richtlinien für [Builds ab 1910](azure-stack-vpn-gateway-settings.md#ipsecike-parameters) geändert haben, ist eine benutzerdefinierte IPsec-Richtlinie erforderlich, um Azure und Azure Stack Hub auf den gleichen Stand zu bringen.

1. Erstellen einer benutzerdefinierten Richtlinie:

   ```powershell
     $IPSecPolicy = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup ECP384  `
     -IpsecEncryption GCMAES256 -IpsecIntegrity GCMAES256 -PfsGroup ECP384 -SALifeTimeSeconds 27000 `
     -SADataSizeKilobytes 102400000 
   ```

2. Übernehmen der Richtlinie für die Verbindung:

   ```powershell
   $Connection = Get-AzVirtualNetworkGatewayConnection -Name myTunnel -ResourceGroupName myRG
   Set-AzVirtualNetworkGatewayConnection -IpsecPolicies $IPSecPolicy -VirtualNetworkGatewayConnection $Connection
   ```

## <a name="create-a-vm"></a>Erstellen einer VM

Erstellen Sie jetzt in Azure eine VM, und platzieren Sie sie im VM-Subnetz Ihres virtuellen Netzwerks.

1. Wählen Sie im Azure-Portal die Option **+ Ressource erstellen**.
2. Wechseln Sie zu **Marketplace**, und wählen Sie dann **Compute** aus.
3. Wählen Sie in der Liste mit den VM-Images das Image **Windows Server 2016 Datacenter Eval** aus.
4. Geben Sie im Bereich **Grundlagen** unter **Name** den Namen **AzureVM** ein.
5. Geben Sie einen gültigen Benutzernamen und ein gültiges Kennwort ein. Mit diesem Konto melden Sie sich am virtuellen Computer an, nachdem dieser erstellt wurde.
6. Geben Sie Werte für **Abonnement**, **Ressourcengruppe** und **Speicherort** an, und wählen Sie anschließend **OK**.
7. Wählen Sie im Bereich **Größe** eine VM-Größe für diese Instanz aus, und klicken Sie dann auf **Auswählen**.
8. Im Abschnitt **Einstellungen** können Sie die Standardeinstellungen verwenden. Überprüfen Sie Folgendes, bevor Sie **OK** wählen:

   * Das virtuelle Netzwerk **AzureVnet** ist ausgewählt.
   * Das Subnetz ist auf **10.100.0.0/24** festgelegt.

   Klicken Sie auf **OK**.

9. Überprüfen Sie die Einstellungen im Bereich **Zusammenfassung**, und klicken Sie anschließend auf **OK**.

## <a name="create-the-network-resources-in-azure-stack-hub"></a>Erstellen der Netzwerkressourcen in Azure Stack Hub

Erstellen Sie als Nächstes die Netzwerkressourcen in Azure Stack Hub.

### <a name="sign-in-as-a-user"></a>Anmelden als Benutzer

Ein Dienstadministrator kann sich als Benutzer anmelden, um die Pläne, Angebote und Abonnements zu testen, die den Benutzern zur Verfügung stehen. [Erstellen Sie ein Benutzerkonto](../operator/azure-stack-add-new-user-aad.md), bevor Sie sich anmelden, falls Sie diesen Schritt noch nicht ausgeführt haben.

### <a name="create-the-virtual-network-and-a-vm-subnet"></a>Erstellen des virtuellen Netzwerks und eines VM-Subnetzes

1. Verwenden Sie ein Benutzerkonto für die Anmeldung beim Benutzerportal.
2. Klicken Sie im Benutzerportal auf **+ Ressource erstellen**.

    ![Neues virtuelles Netzwerk erstellen](media/azure-stack-connect-vpn/image3.png)

3. Wechseln Sie zu **Marketplace**, und wählen Sie dann **Netzwerk** aus.
4. Wählen Sie **Virtuelles Netzwerk** aus.
5. Verwenden Sie für **Name**, **Adressraum**, **Subnetzname** und **Subnetzadressbereich** die Werte aus der Netzwerk-Konfigurationstabelle.
6. In **Abonnement** wird das zuvor von Ihnen erstellte Abonnement angezeigt.
7. Als **Ressourcengruppe** können Sie entweder eine Ressourcengruppe erstellen oder **Use existing** (Vorhandene verwenden) auswählen, sofern Sie bereits über eine Ressourcengruppe verfügen.
8. Überprüfen Sie den standardmäßigen Speicherort.
9. Wählen Sie die Option **An Dashboard anheften** aus.
10. Klicken Sie auf **Erstellen**.

### <a name="create-the-gateway-subnet"></a>Erstellen des Gatewaysubnetzes

1. Öffnen Sie im Dashboard die zuvor erstellte Ressource des virtuellen Netzwerks „Azs-VNet“.
2. Wählen Sie im Bereich **Einstellungen** die Option **Subnetze** aus.
3. Wählen Sie **Gatewaysubnetz**, um dem virtuellen Netzwerk ein Gatewaysubnetz hinzuzufügen.

    ![Gatewaysubnetz hinzufügen](media/azure-stack-connect-vpn/image4.png)

4. Der Subnetzname ist standardmäßig auf **GatewaySubnet** festgelegt. Sie müssen den Namen **GatewaySubnet** verwenden, damit Gatewaysubnetze ordnungsgemäß funktionieren.
5. Überprüfen Sie, ob die unter **Adressbereich** angegebene Adresse **10.1.1.0/24** lautet.
6. Wählen Sie **OK**, um das Gatewaysubnetz zu erstellen.

### <a name="create-the-virtual-network-gateway"></a>Erstellen des Gateways für das lokale Netzwerk

1. Klicken Sie im Azure Stack Hub-Portal auf **+ Ressource erstellen**.
2. Wechseln Sie zu **Marketplace**, und wählen Sie dann **Netzwerk** aus.
3. Wählen Sie in der Liste mit den Netzwerkressourcen den Eintrag **Gateway des virtuellen Netzwerks** aus.
4. Geben Sie unter **Name** den Namen **Azs-GW** ein.
5. Wählen Sie **Virtuelles Netzwerk**, um ein virtuelles Netzwerk auszuwählen. Wählen Sie aus der Liste **Azs-VNet** aus.
6. Wählen Sie das Menüelement **Öffentliche IP-Adresse**. Wählen Sie im Bereich **Öffentliche IP-Adresse auswählen** die Option **Neu erstellen** aus.
7. Geben Sie unter **Name** den Namen **Azs-GW-PiP** ein, und klicken Sie auf **OK**.
8. Für **VPN-Typ** ist standardmäßig **Routenbasiert** ausgewählt. Behalten Sie **Routenbasiert** als VPN-Typ bei.

9. Vergewissern Sie sich, dass **Abonnement** und **Speicherort** korrekt sind. Die Ressource kann an das Dashboard angeheftet werden. Klicken Sie auf **Erstellen**.

### <a name="create-the-local-network-gateway"></a>Erstellen des Gateways des lokalen Netzwerks

Es gibt Unterschiede zwischen einem *Gateway des lokalen Netzwerks* in Azure Stack Hub und in einer Azure-Bereitstellung.

In einer Azure-Bereitstellung stellt ein lokales Netzwerkgateway ein lokales physisches Gerät (am Benutzerstandort) dar, für das Sie eine Verbindung mit einem Gateway für virtuelle Netzwerke in Azure herstellen. In Azure Stack Hub sind jedoch beide Enden der Verbindung Gateways für virtuelle Netzwerke.

Ganz allgemein wird mit der Ressource des lokalen Netzwerkgateways immer das Remotegateway am anderen Ende der Verbindung angegeben.

### <a name="create-the-local-network-gateway-resource"></a>Erstellen der Ressource des lokalen Netzwerkgateways

1. Melden Sie sich beim Azure Stack Hub-Portal an.
2. Klicken Sie im Benutzerportal auf **+ Ressource erstellen**.
3. Wechseln Sie zu **Marketplace**, und wählen Sie dann **Netzwerk** aus.
4. Wählen Sie in der Ressourcenliste den Eintrag für das **lokale Netzwerkgateway** aus.
5. Geben Sie im Feld **Name** die Zeichenfolge **Azure-GW** ein.
6. Geben Sie im Feld **IP-Adresse** die öffentliche IP-Adresse für das Gateway für virtuelle Netzwerke in Azure **Azure-GW-PiP** ein. Diese Adresse wird weiter oben in der Netzwerk-Konfigurationstabelle angezeigt.
7. Geben Sie im Feld **Adressraum** für den Adressraum des zuvor erstellten Azure VNETs **10.100.0.0/24** und **10.100.1.0/24** ein.

8. Vergewissern Sie sich, dass die Werte für **Abonnement**, **Ressourcengruppe** und **Speicherort** richtig sind, und wählen Sie **Erstellen** aus.

### <a name="create-the-connection"></a>Erstellen der Verbindung

1. Klicken Sie im Benutzerportal auf **+ Ressource erstellen**.
2. Wechseln Sie zu **Marketplace**, und wählen Sie dann **Netzwerk** aus.
3. Wählen Sie in der Ressourcenliste die Option **Verbindung** aus.
4. Wählen Sie im Einstellungsbereich **Grundlagen** unter **Verbindungstyp** die Option **Site-to-Site (IPsec)** aus.
5. Wählen Sie Werte für **Abonnement**, **Ressourcengruppe** und **Speicherort** aus, und wählen Sie anschließend **OK**.
6. Wählen Sie im Bereich **Einstellungen** die Option **Gateway des virtuellen Netzwerks** aus, und wählen Sie anschließend **Azs-GW** aus.
7. Wählen Sie **Gateway des lokalen Netzwerks** aus, und wählen Sie dann **Azure-GW** aus.
8. Geben Sie unter **Verbindungsname** den Namen **Azs-Azure** ein.
9. Geben Sie unter **Gemeinsam verwendeter Schlüssel (PSK)** den Wert **12345** ein, und klicken Sie auf **OK**.

10. Wählen Sie im Bereich **Zusammenfassung** die Option **OK** aus.

### <a name="create-a-vm"></a>Erstellen einer VM

Erstellen Sie zum Überprüfen der VPN-Verbindung zwei virtuelle Computer: einen in Azure und einen in Azure Stack Hub. Nachdem Sie diese virtuellen Computer erstellt haben, können Sie sie zum Senden und Empfangen von Daten über den VPN-Tunnel verwenden.

1. Wählen Sie im Azure-Portal die Option **+ Ressource erstellen**.
2. Wechseln Sie zu **Marketplace**, und wählen Sie dann **Compute** aus.
3. Wählen Sie in der Liste mit den VM-Images das Image **Windows Server 2016 Datacenter Eval** aus.
4. Geben Sie im Bereich **Grundlagen** unter **Name** den Namen **Azs-VM** ein.
5. Geben Sie einen gültigen Benutzernamen und ein gültiges Kennwort ein. Mit diesem Konto melden Sie sich am virtuellen Computer an, nachdem dieser erstellt wurde.
6. Geben Sie Werte für **Abonnement**, **Ressourcengruppe** und **Speicherort** an, und wählen Sie anschließend **OK**.
7. Wählen Sie im Bereich **Größe** eine VM-Größe für diese Instanz aus, und klicken Sie dann auf **Auswählen**.
8. Übernehmen Sie im Bereich **Einstellungen** die Standardeinstellungen. Stellen Sie sicher, dass das virtuelle Netzwerk **Azs-VNet** ausgewählt ist. Stellen Sie sicher, dass das Subnetz auf **10.1.0.0/24** festgelegt ist. Klicken Sie anschließend auf **OK**.

9. Überprüfen Sie die Einstellungen im Bereich **Zusammenfassung**, und klicken Sie anschließend auf **OK**.

## <a name="test-the-connection"></a>Testen der Verbindung

Nachdem die Site-to-Site-Verbindung hergestellt wurde, sollten Sie sicherstellen, dass Daten in beide Richtungen fließen können. Die einfachste Möglichkeit zum Testen der Verbindung ist das Durchführen eines Ping-Tests:

* Melden Sie sich an der VM an, die Sie in Azure Stack Hub erstellt haben, und pingen Sie die VM in Azure.
* Melden Sie sich an der VM an, die Sie in Azure erstellt haben, und pingen Sie die VM in Azure Stack Hub.

>[!NOTE]
>Um sicherzustellen, dass Sie den Datenverkehr über die Site-to-Site-Verbindung senden, sollten Sie den Ping nicht an die VIP-Adresse, sondern an die direkte IP-Adresse (DIP) der VM im Remotesubnetz senden.

### <a name="sign-in-to-the-user-vm-in-azure-stack-hub"></a>Anmelden beim virtuellen Benutzercomputer in Azure Stack Hub

1. Melden Sie sich beim Azure Stack Hub-Portal an.
2. Wählen Sie in der Navigationsleiste links die Option **Virtuelle Computer** aus.
3. Suchen Sie in der Liste mit den virtuellen Computern nach dem zuvor erstellten Computer **Azs-VM**, und wählen Sie ihn aus.
4. Wählen Sie im Bereich für die VM die Option **Verbinden**. Öffnen Sie anschließend die Datei „Azs-VM.rdp“.

     ![Schaltfläche „Verbinden“](media/azure-stack-connect-vpn/image17.png)

5. Melden Sie sich mit dem Konto an, das Sie beim Erstellen der VM konfiguriert haben.
6. Öffnen Sie eine Windows PowerShell-Eingabeaufforderung mit erhöhten Rechten.
7. Geben Sie **ipconfig /all** ein.
8. Suchen Sie in der Ausgabe die **IPv4-Adresse**, und speichern Sie diese zur späteren Verwendung. Diese Adresse wird später von Azure aus gepingt. In der Beispielumgebung wird die Adresse **10.1.0.4** verwendet, in Ihrer Umgebung lautet sie aber unter Umständen anders. Sie sollte innerhalb des zuvor erstellten Subnetzes **10.1.0.0/24** liegen.
9. Führen Sie den folgenden PowerShell-Befehl aus, um eine Firewallregel zu erstellen, die zulässt, dass die VM auf Pings reagiert:

   ```powershell
   New-NetFirewallRule `
    -DisplayName "Allow ICMPv4-In" `
    -Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-azure"></a>Anmelden beim virtuellen Mandantencomputer in Azure

1. Melden Sie sich beim Azure-Portal an.
2. Wählen Sie in der Navigationsleiste links die Option **Virtuelle Computer** aus.
3. Suchen Sie in der Liste mit den VMs nach dem zuvor erstellten Computer **Azure-VM**, und wählen Sie ihn aus.
4. Wählen Sie im Abschnitt für die VM die Option **Verbinden**.
5. Melden Sie sich mit dem Konto an, das Sie beim Erstellen der VM konfiguriert haben.
6. Öffnen Sie ein **Windows PowerShell**-Fenster mit erhöhten Rechten.
7. Geben Sie **ipconfig /all** ein.
8. Daraufhin sollte eine IPv4-Adresse aus dem Adressbereich **10.100.0.0/24** angezeigt werden. In der Beispielumgebung wird die Adresse **10.100.0.4** verwendet, Ihre Adresse lautet aber unter Umständen anders.
9. Führen Sie den folgenden PowerShell-Befehl aus, um eine Firewallregel zu erstellen, die zulässt, dass die VM auf Pings reagiert:

   ```powershell
   New-NetFirewallRule `
    -DisplayName "Allow ICMPv4-In" `
    -Protocol ICMPv4
   ```

10. Pingen Sie von der VM in Azure die VM in Azure Stack Hub über den Tunnel. Hierzu pingen Sie die DIP-Adresse, die Sie für „Azs-VM“ erfasst haben. In der Beispielumgebung wird die Adresse **10.1.0.4** verwendet. Achten Sie jedoch darauf, dass Sie die Adresse pingen, die Sie in Ihrem Lab notiert haben. Das Ergebnis sollte in etwa wie im folgenden Screenshot aussehen:

    ![Ping erfolgreich](media/azure-stack-connect-vpn/image19b.png)

11. Wenn Sie eine Antwort vom virtuellen Remotecomputer erhalten, war der Test erfolgreich. Sie können das VM-Fenster schließen.

Außerdem sollten Sie einen eingehenderen Test der Datenübertragung durchführen (z. B. durch das Kopieren von Dateien unterschiedlicher Größe in beide Richtungen).

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Anzeigen der Datenübertragungsstatistik über die Gatewayverbindung

Im Abschnitt **Verbindung** können Sie die Datenmenge ermitteln, die über die Site-to-Site-Verbindung übertragen wird. Mit diesem Test können Sie auch überprüfen, ob der soeben gesendete Ping tatsächlich die VPN-Verbindung durchlaufen hat.

1. Verwenden Sie Ihr Benutzerkonto zur Anmeldung am Benutzerportal, während Sie an der virtuellen Benutzer-VM in Azure Stack Hub angemeldet sind.
2. Wechseln Sie zu **Alle Ressourcen**, und wählen Sie die Verbindung **Azs-Azure** aus. **Verbindungen** wird angezeigt.
3. Im Bereich **Verbindung** wird die Statistik für **eingehende Daten** und **ausgehende Daten** angezeigt. Die hohen Zahlen im folgenden Screenshot sind auf zusätzliche Dateiübertragungen zurückzuführen. Dort sollten andere Werte als Null angezeigt werden.

    ![Eingehende und ausgehende Daten](media/azure-stack-connect-vpn/Connection.png)

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen von Apps in Azure und Azure Stack Hub](https://github.com/Azure-Samples/azure-intelligent-edge-patterns/tree/master/hybrid-devops)
