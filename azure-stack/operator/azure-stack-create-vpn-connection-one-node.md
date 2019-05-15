---
title: Erstellen einer Site-to-Site-VPN-Verbindung zwischen zwei virtuellen Netzwerken in unterschiedlichen Azure Stack Development Kit-Umgebungen | Microsoft-Dokumentation
description: Schrittweise Anleitung für Cloudadministratoren zum Erstellen einer Site-to-Site-VPN-Verbindung zwischen zwei Azure Stack Development Kit-Umgebungen mit jeweils einem Knoten.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3f1b4e02-dbab-46a3-8e11-a777722120ec
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: sethm
ms.reviewer: scottnap
ms.lastreviewed: 09/12/2018
ROBOTS: NOINDEX
ms.openlocfilehash: cee1ca68caa6742eb5d965b53b685746d9057691
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985388"
---
# <a name="create-a-site-to-site-vpn-connection-between-two-virtual-networks-in-different-azure-stack-development-kit-environments"></a>Erstellen einer Site-to-Site-VPN-Verbindung zwischen zwei virtuellen Netzwerken in unterschiedlichen Azure Stack Development Kit-Umgebungen

## <a name="overview"></a>Übersicht

Dieser Artikel beschreibt, wie Sie eine Site-to-Site-VPN-Verbindung zwischen zwei virtuellen Netzwerken in zwei separaten Azure Stack Development Kit-Umgebungen (ASDK) erstellen. Im Rahmen der Verbindungskonfiguration werden Sie auch mit der Funktionsweise von VPN-Gateways in Azure Stack vertraut gemacht.

### <a name="connection"></a>Verbindung

Die folgende Abbildung zeigt die endgültige Verbindungskonfiguration.

![Konfiguration einer Site-to-Site-VPN-Verbindung](media/azure-stack-create-vpn-connection-one-node-tp2/OneNodeS2SVPN.png)

### <a name="before-you-begin"></a>Voraussetzungen

Vergewissern Sie sich zum Durchführen der Verbindungskonfiguration zunächst, dass Sie über folgende Elemente verfügen:

* Zwei Server und andere erforderliche Komponenten, die die ASDK-Hardwareanforderungen erfüllen, wie hier beschrieben [Schnellstart: Evaluieren des Azure Stack Development Kit](../asdk/asdk-download.md).
* Das [Bereitstellungspaket für das Azure Stack Development Kit](https://azure.microsoft.com/overview/azure-stack/try/).

## <a name="deploy-the-azure-stack-development-kit-environments"></a>Bereitstellen der Azure Stack Development Kit-Umgebungen

Zum Durchführen der Verbindungskonfiguration müssen Sie zwei ASDK-Umgebungen bereitstellen.

> [!NOTE]
> Führen Sie für jedes bereitgestellte ASDK die folgenden [Bereitstellungsanweisungen](../asdk/asdk-install.md) aus. In diesem Artikel werden die ASDK-Umgebungen als **POC1** und **POC2** bezeichnet.

## <a name="prepare-an-offer-on-poc1-and-poc2"></a>Vorbereiten eines Angebots in POC1 und POC2

Bereiten Sie sowohl in POC1 als auch POC2 ein Angebot vor, damit ein Benutzer das Angebot abonnieren kann, und stellen Sie die virtuellen Computer bereit. Informationen zum Erstellen eines Angebots finden Sie unter [Make virtual machines available to your Azure Stack users](azure-stack-tutorial-tenant-vm.md) (Verfügbarmachen von virtuellen Computern für Azure Stack-Benutzer).

## <a name="review-and-complete-the-network-configuration-table"></a>Überprüfen und Ausfüllen der Netzwerk-Konfigurationstabelle

In der folgenden Tabelle sind die Netzwerkkonfigurationen für beide ASDK-Umgebungen zusammengefasst. Fügen Sie mit dem Verfahren, das auf die Tabelle folgt, die für Ihr Netzwerk spezifische externe BGPNAT-Adresse hinzu.

### <a name="network-configuration-table"></a>Netzwerkkonfigurationstabelle

|   |POC1|POC2|
|---------|---------|---------|
|Name des virtuellen Netzwerks     |VNET-01|VNET-02 |
|Adressraum des virtuellen Netzwerks |10.0.10.0/23|10.0.20.0/23|
|Subnetzname     |Subnet-01|Subnet-02|
|Subnetzadressbereich|10.0.10.0/24 |10.0.20.0/24 |
|Gatewaysubnetz     |10.0.11.0/24|10.0.21.0/24|
|Externe BGPNAT-Adresse     |         |         |

> [!NOTE]
> Die externen BGPNAT-IP-Adressen in der Beispielumgebung sind 10.16.167.195 für POC1 und 10.16.169.131 für POC2. Gehen Sie folgendermaßen vor, um die externen BGPNAT IP-Adressen für Ihre ASDK-Hosts zu ermitteln, und fügen Sie sie dann der vorhergehenden Netzwerk-Konfigurationstabelle hinzu.

### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>Abrufen der IP-Adresse des externen Adapters des virtuellen NAT-Computers

1. Melden Sie sich auf dem physischen Azure Stack-Computer für POC1 an.
2. Fügen Sie Ihr Administratorkennwort in den folgenden PowerShell-Code ein, und führen Sie den Code auf dem POC-Host aus:

   ```powershell
   cd \AzureStack-Tools-master\connect
   Import-Module .\AzureStack.Connect.psm1
   $Password = ConvertTo-SecureString "<your administrator password>" `
    -AsPlainText `
    -Force
   Get-AzureStackNatServerAddress `
    -HostComputer "AzS-bgpnat01" `
    -Password $Password
   ```

3. Fügen Sie die IP-Adresse der Netzwerk-Konfigurationstabelle hinzu, die im vorherigen Abschnitt angezeigt wird.

4. Wiederholen Sie dieses Verfahren auf POC2.

## <a name="create-the-network-resources-in-poc1"></a>Erstellen der Netzwerkressourcen in POC1

Nun erstellen Sie die POC1-Netzwerkressourcen, die Sie zum Einrichten Ihrer Gateways benötigen. Die folgenden Anweisungen beschreiben, wie die Ressourcen mithilfe des Azure Stack-Benutzerportals erstellt werden. Sie können die Ressourcen auch mithilfe von PowerShell-Code erstellen.

![Workflow zum Erstellen von Ressourcen](media/azure-stack-create-vpn-connection-one-node-tp2/image2.png)

### <a name="sign-in-as-a-tenant"></a>Anmelden als Mandant

Ein Dienstadministrator kann sich als Mandant anmelden, um die Pläne, Angebote und Abonnements zu testen, die den Mandanten zur Verfügung stehen. Falls Sie noch kein Mandantenkonto besitzen, [erstellen Sie ein Mandantenkonto](azure-stack-add-new-user-aad.md), bevor Sie sich anmelden.

### <a name="create-the-virtual-network-and-vm-subnet"></a>Erstellen des virtuellen Netzwerks und des VM-Subnetzes

1. Verwenden Sie ein Mandantenkonto für die Anmeldung beim Benutzerportal.
2. Klicken Sie im Benutzerportal auf **+ Ressource erstellen**.
3. Wechseln Sie zu **Marketplace**, und wählen Sie dann **Netzwerk** aus.
4. Wählen Sie **Virtuelles Netzwerk** aus.
5. Verwenden Sie für **Name**, **Adressraum**, **Subnetzname**, und **Subnetzadressbereich** die Werte aus der Netzwerk-Konfigurationstabelle weiter oben.
6. In **Abonnement** wird das zuvor von Ihnen erstellte Abonnement angezeigt.
7. Als **Ressourcengruppe** können Sie entweder eine Ressourcengruppe erstellen oder **Use existing** (Vorhandene verwenden) auswählen, sofern Sie bereits über eine Ressourcengruppe verfügen.
8. Überprüfen Sie den standardmäßigen Speicherort.
9. Wählen Sie die Option **An Dashboard anheften** aus.
10. Klicken Sie auf **Erstellen**.

### <a name="create-the-gateway-subnet"></a>Erstellen des Gatewaysubnetzes

1. Öffnen Sie auf dem Dashboard die virtuelle Netzwerkressource VNET-01, die Sie zuvor erstellt haben.
2. Wählen Sie auf dem Blatt **Einstellungen** die Option **Subnetze** aus.
3. Wählen Sie **Gatewaysubnetz**, um dem virtuellen Netzwerk ein Gatewaysubnetz hinzuzufügen.

    ![Gatewaysubnetz hinzufügen](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)

4. Der Subnetzname ist standardmäßig auf **GatewaySubnet** festgelegt. Gateway-Subnetz sind speziell. Sie müssen den Namen **GatewaySubnet** verwenden, um ordnungsgemäß zu funktionieren.
5. Überprüfen Sie im **Adressbereich**, dass die Adresse **10.0.11.0/24** lautet.
6. Wählen Sie **OK**, um das Gatewaysubnetz zu erstellen.

### <a name="create-the-virtual-network-gateway"></a>Erstellen des Gateways für das lokale Netzwerk

1. Wählen Sie im Azure-Portal die Option **+ Ressource erstellen**.
2. Wechseln Sie zu **Marketplace**, und wählen Sie dann **Netzwerk** aus.
3. Wählen Sie in der Liste mit den Netzwerkressourcen den Eintrag **Gateway des virtuellen Netzwerks** aus.
4. Geben Sie **GW1** als **Name** ein.
5. Wählen Sie **Virtuelles Netzwerk**, um ein virtuelles Netzwerk auszuwählen. Wählen Sie in der Liste die Option **VNET-01** aus.
6. Wählen Sie das Menüelement **Öffentliche IP-Adresse**. Wählen Sie im Fenster **Öffentliche IP-Adresse auswählen** die Option **Neu erstellen** aus.
7. Geben Sie **GW1-PiP** als **Name** ein, und wählen Sie dann **OK**.
8. Als **VPN-Typ** ist standardmäßig **Routenbasiert** ausgewählt. Behalten Sie **Routenbasiert** als VPN-Typ bei.
9. Vergewissern Sie sich, dass **Abonnement** und **Speicherort** korrekt sind. Die Ressource kann an das Dashboard angeheftet werden. Klicken Sie auf **Erstellen**.

### <a name="create-the-local-network-gateway"></a>Erstellen des Gateways des lokalen Netzwerks

Die Implementierung eines *lokalen Netzwerkgateways* ist bei dieser Azure Stack-Evaluierungsbereitstellung ein wenig anders als bei einer tatsächlichen Azure-Bereitstellung.

In einer Azure-Bereitstellung stellt ein lokales Netzwerkgateway ein (aus Sicht des Mandanten) lokales physisches Gerät dar, mit dem eine Verbindung mit einem virtuellen Netzwerkgateway in Azure hergestellt wird. In dieser Azure Stack-Evaluierungsbereitstellung handelt es sich bei beiden Enden der Verbindung um virtuelle Netzwerkgateways.

Etwas allgemeiner betrachtet wird mit der Ressource des lokalen Netzwerkgateways immer das Remotegateway am anderen Ende der Verbindung angegeben. Aufgrund des Azure Stack Development Kit-Designs müssen Sie die IP-Adresse des externen Netzwerkadapters auf dem virtuellen NAT-Computer des anderen ASDK als öffentliche IP-Adresse des lokalen Netzwerkgateways angeben. Anschließend erstellen Sie NAT-Zuordnungen auf dem virtuellen NAT-Computer, um sicherzustellen, dass beide Enden ordnungsgemäß verbunden sind.

### <a name="create-the-local-network-gateway-resource"></a>Erstellen der Ressource des lokalen Netzwerkgateways

1. Melden Sie sich auf dem physischen Azure Stack-Computer für POC1 an.
2. Klicken Sie im Benutzerportal auf **+ Ressource erstellen**.
3. Wechseln Sie zu **Marketplace**, und wählen Sie dann **Netzwerk** aus.
4. Wählen Sie in der Ressourcenliste den Eintrag für das **lokale Netzwerkgateway** aus.
5. Geben Sie **POC2-GW** als **Name** ein.
6. Geben Sie als **IP-Adresse** die Adresse der externen BGPNAT für POC2 ein. Diese Adresse wird weiter oben in der Netzwerk-Konfigurationstabelle angezeigt.
7. Geben Sie als **Adressraum** des POC2 VNETs, das Sie später erstellen, **10.0.20.0/23** ein.
8. Vergewissern Sie sich, dass die Werte für **Abonnement**, **Ressourcengruppe** und **Speicherort** richtig sind, und wählen Sie **Erstellen** aus.

### <a name="create-the-connection"></a>Erstellen der Verbindung

1. Klicken Sie im Benutzerportal auf **+ Ressource erstellen**.
2. Wechseln Sie zu **Marketplace**, und wählen Sie dann **Netzwerk** aus.
3. Wählen Sie in der Ressourcenliste die Option **Verbindung** aus.
4. Wählen Sie auf dem Blatt **Grundlagen** als **Verbindungstyp** die Option **Site-to-Site (IPsec)** aus.
5. Wählen Sie Werte für **Abonnement**, **Ressourcengruppe** und **Speicherort** aus, und wählen Sie anschließend **OK**.
6. Wählen Sie auf dem Blatt **Einstellungen** die Option **Gateway des virtuellen Netzwerks** aus, und wählen Sie dann **GW1**.
7. Wählen Sie **Gateway des lokalen Netzwerks** aus, und wählen Sie dann **POC2-GW**.
8. Geben Sie **POC1-POC2** als **Verbindungsname** ein.
9. Geben Sie in **Gemeinsam verwendeter Schlüssel (PSK)** **12345** ein, und wählen Sie dann **OK**.
10. Wählen Sie auf dem Blatt **Zusammenfassung** **OK**.

### <a name="create-a-vm"></a>Erstellen einer VM

Zum Überprüfen der durch die VPN-Verbindung geleiteten Daten benötigen Sie die virtuellen Computer zum Senden und Empfangen von Daten in den einzelnen Azure Stack Development Kits. Erstellen Sie jetzt einen virtuellen Computer in POC1, und platzieren Sie ihn dann im VM-Subnetz Ihres virtuellen Netzwerks.

1. Wählen Sie im Azure-Portal die Option **+ Ressource erstellen**.
2. Wechseln Sie zu **Marketplace**, und wählen Sie dann **Compute** aus.
3. Wählen Sie in der Liste mit den VM-Images das Image **Windows Server 2016 Datacenter Eval** aus.
4. Geben Sie auf dem Blatt **Grundlagen** **VM01** als **Name** ein.
5. Geben Sie einen gültigen Benutzernamen und das Kennwort ein. Mit diesem Konto melden Sie sich am virtuellen Computer an, nachdem dieser erstellt wurde.
6. Geben Sie Werte für **Abonnement**, **Ressourcengruppe** und **Speicherort** an, und wählen Sie anschließend **OK**.
7. Wählen Sie auf dem Blatt **Größe** für diese Instanz eine Größe für den virtuellen Computer aus, und klicken Sie dann auf **Auswählen**.
8. Akzeptieren Sie auf dem Blatt **Einstellungen** die Standardeinstellungen. Stellen Sie sicher, dass das virtuelle Netzwerk **VNET-01** ausgewählt ist. Stellen Sie sicher, dass das Subnetz auf **10.0.10.0/24** festgelegt ist. Wählen Sie dann **OK**aus.
9. Überprüfen Sie auf dem Blatt **Zusammenfassung** die Einstellungen, und klicken Sie anschließend auf **OK**.

## <a name="create-the-network-resources-in-poc2"></a>Erstellen der Netzwerkressourcen in POC2

Der nächste Schritt ist das Erstellen der Netzwerkressourcen für POC2. Die folgenden Anweisungen zeigen, wie die Ressourcen mithilfe des Benutzerportals erstellt werden.

### <a name="sign-in-as-a-tenant-again"></a>Erneutes Anmelden als Mandant

Ein Dienstadministrator kann sich als Mandant anmelden, um die Pläne, Angebote und Abonnements zu testen, die den Mandanten zur Verfügung stehen. Falls Sie noch kein Mandantenkonto besitzen, [erstellen Sie ein Mandantenkonto](azure-stack-add-new-user-aad.md), bevor Sie sich anmelden.

### <a name="create-virtual-network-and-vm-subnet"></a>Erstellen des virtuellen Netzwerks und des VM-Subnetzes

1. Melden Sie sich mit einem Mandantenkonto an.
2. Klicken Sie im Benutzerportal auf **+ Ressource erstellen**.
3. Wechseln Sie zu **Marketplace**, und wählen Sie dann **Netzwerk** aus.
4. Wählen Sie **Virtuelles Netzwerk** aus.
5. Verwenden Sie für als **Name**, **Adressraum**, **Subnetzname**, und **Subnetzadressbereich** für POC2 die Werte aus der Netzwerk-Konfigurationstabelle weiter oben.
6. In **Abonnement** wird das zuvor von Ihnen erstellte Abonnement angezeigt.
7. Erstellen Sie entweder eine neue **Ressourcengruppe**, oder wählen Sie **Use existing** (Vorhandene verwenden) auswählen, wenn Sie bereits über eine verfügen.
8. Überprüfen Sie den standardmäßigen **Speicherort**.
9. Wählen Sie die Option **An Dashboard anheften** aus.
10. Klicken Sie auf **Erstellen**.

### <a name="create-gateway-subnet"></a>Erstellen eines Gatewaysubnetzes

1. Öffnen Sie über das Dashboard die zuvor erstellte Ressource des virtuellen Netzwerks (**VNET-02**).
2. Wählen Sie auf dem Blatt **Einstellungen** die Option **Subnetze** aus.
3. Wählen Sie **Gatewaysubnetz**, um dem virtuellen Netzwerk ein Gatewaysubnetz hinzuzufügen.
4. Der Name des Subnetzes wird standardmäßig auf **GatewaySubnet** festgelegt. Gatewaysubnetze sind spezielle Subnetze und müssen diesen spezifischen Namen besitzen, damit sie ordnungsgemäß funktionieren.
5. Stellen Sie sicher, dass im Feld **Adressbereich** die Adresse **10.0.21.0/24** angegeben ist.
6. Wählen Sie **OK**, um das Gatewaysubnetz zu erstellen.

### <a name="create-virtual-network-gateway"></a>Erstellen einer Gateway für virtuelle Netzwerke

1. Wählen Sie im Azure-Portal die Option **+ Ressource erstellen**.  
2. Wechseln Sie zu **Marketplace**, und wählen Sie dann **Netzwerk** aus.
3. Wählen Sie in der Liste mit den Netzwerkressourcen den Eintrag **Gateway des virtuellen Netzwerks** aus.
4. Geben Sie **GW2** als **Name** ein.
5. Klicken Sie auf **Virtuelles Netzwerk**, um ein virtuelles Netzwerk auszuwählen. Wählen Sie dann in der Liste die Option **VNET-02** aus.
6. Wählen Sie **Öffentliche IP-Adresse**. Wählen Sie auf dem Blatt **Öffentliche IP-Adresse auswählen** die Option **Neu erstellen** aus.
7. Geben Sie **GW2-PiP** als **Name** ein, und wählen Sie dann **OK**.
8. Als **VPN-Typ** ist standardmäßig **Routenbasiert** ausgewählt. Behalten Sie **Routenbasiert** als VPN-Typ bei.
9. Vergewissern Sie sich, dass **Abonnement** und **Speicherort** korrekt sind. Die Ressource kann an das Dashboard angeheftet werden. Klicken Sie auf **Erstellen**.

### <a name="create-local-network-gateway-resource"></a>Erstellen der Ressource des lokalen Netzwerkgateways

1. Klicken Sie im POC2-Benutzerportal auf **+ Ressource erstellen**.
2. Wechseln Sie zu **Marketplace**, und wählen Sie dann **Netzwerk** aus.
3. Wählen Sie in der Ressourcenliste den Eintrag für das **lokale Netzwerkgateway** aus.
4. Geben Sie **POC1-GW** als **Name** ein.
5. Geben Sie in **IP-Adresse** die Adresse der externen BGPNAT für POC1 ein, die weiter oben in der Netzwerk-Konfigurationstabelle aufgeführt ist.
6. Geben Sie in **Adressraum** für POC1 den Adressraum **10.0.10.0/23** von **VNET-01** ein.
7. Vergewissern Sie sich, dass Ihr **Abonnement**, die **Ressourcengruppe** und der **Speicherort** richtig sind, und klicken Sie auf **Erstellen**.

## <a name="create-connection"></a>Erstellen der Verbindung

1. Klicken Sie im Benutzerportal auf **+ Ressource erstellen**.
2. Wechseln Sie zu **Marketplace**, und wählen Sie dann **Netzwerk** aus.
3. Wählen Sie in der Ressourcenliste die Option **Verbindung** aus.
4. Wählen Sie auf dem Blatt **Grundlagen** unter **Verbindungstyp** die Option **Site-to-Site (IPsec)** aus.
5. Wählen Sie Werte für **Abonnement**, **Ressourcengruppe** und **Speicherort** aus, und wählen Sie anschließend **OK**.
6. Wählen Sie auf dem Blatt **Einstellungen** die Option **Gateway des virtuellen Netzwerks** aus, und wählen Sie dann **GW2**.
7. Wählen Sie **Gateway des lokalen Netzwerks** aus, und wählen Sie dann **POC1-GW**.
8. Geben Sie **POC2-POC1** als **Verbindungsname** ein.
9. Geben Sie in **Gemeinsam verwendeter Schlüssel (PSK)** den Wert **12345** ein. Falls Sie einen anderen Wert verwenden, vergessen Sie nicht, dass er dem Wert für den gemeinsam verwendeten Schlüssel entsprechen muss, den Sie für POC1 erstellt haben. Klicken Sie auf **OK**.
10. Überprüfen Sie das Blatt **Einstellungen**, und wählen Sie dann **OK**.

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

Erstellen Sie jetzt einen virtuellen Computer in POC2, und platzieren Sie ihn dann im VM-Subnetz Ihres virtuellen Netzwerks:

1. Wählen Sie im Azure-Portal die Option **+ Ressource erstellen**.
2. Wechseln Sie zu **Marketplace**, und wählen Sie dann **Compute** aus.
3. Wählen Sie in der Liste mit den VM-Images das Image **Windows Server 2016 Datacenter Eval** aus.
4. Geben Sie auf dem Blatt **Grundlagen** **VM02** als **Name** ein.
5. Geben Sie einen gültigen Benutzernamen und das Kennwort ein. Mit diesem Konto melden Sie sich am virtuellen Computer an, nachdem dieser erstellt wurde.
6. Geben Sie Werte für **Abonnement**, **Ressourcengruppe** und **Speicherort** an, und wählen Sie anschließend **OK**.
7. Wählen Sie auf dem Blatt **Größe** für diese Instanz eine Größe für den virtuellen Computer aus, und klicken Sie dann auf **Auswählen**.
8. Auf dem Blatt **Einstellungen** können Sie die Standardeinstellungen übernehmen. Stellen Sie sicher, dass das virtuelle Netzwerk **VNET-02** ausgewählt und das Subnetz auf **10.0.20.0/24** festgelegt ist. Klicken Sie auf **OK**.
9. Überprüfen Sie die Einstellungen auf dem Blatt **Zusammenfassung**, und klicken Sie anschließend auf **OK**.

## <a name="configure-the-nat-virtual-machine-on-each-azure-stack-development-kit-for-gateway-traversal"></a>Konfigurieren des virtuellen NAT-Computers in den einzelnen Azure Stack Development Kits für das Durchlaufen des Gateways

Da das ASDK entwurfsbedingt eigenständig und von dem Netzwerk isoliert ist, in dem der physische Host bereitgestellt wird, handelt es sich bei dem *externen* VIP-Netzwerk, mit dem die Gateways verbunden sind, nicht wirklich um ein externes Netzwerk. Das VIP-Netzwerk befindet sich vielmehr hinter einem Router, der eine Netzwerkadressenübersetzung durchführt.

Der Router ist ein virtueller Windows Server-Computer namens **AzS bgpnat01**, der die RRAS-Rolle (Routing and Remote Access Services; Routing- und RAS-Dienste) in der ASDK-Infrastruktur ausführt. Sie müssen die NAT auf dem virtuellen Computer „AzS-bgpnat01“ so konfigurieren, dass die Site-to-Site-VPN-Verbindung an beiden Enden möglich ist.

Zum Konfigurieren der VPN-Verbindung müssen Sie eine statische NAT-Zuordnung erstellen, die die externe Schnittstelle des virtuellen BGPNAT- Computers der VIP des Edge-Gatewaypools Ports zuordnet. Eine statische NAT-Zuordnung ist für jeden Port in einer VPN-Verbindung erforderlich.

> [!NOTE]
> Diese Konfiguration ist nur für Azure Stack Development Kit-Umgebungen erforderlich.
>
>

### <a name="configure-the-nat"></a>Konfigurieren der NAT

> [!IMPORTANT]
> Sie müssen diese Schritte für beide ASDK-Umgebungen ausführen.

1. Ermitteln Sie die **interne IP-Adresse** für die Verwendung im folgenden PowerShell-Skript. Öffnen Sie das Gateway des virtuellen Netzwerks (GW1 und GW2), und speichern Sie dann auf dem Blatt **Übersicht** den Wert für die **öffentliche IP-Adresse** zur späteren Verwendung.

   ![Interne IP-Adresse](media/azure-stack-create-vpn-connection-one-node-tp2/InternalIP.PNG)

2. Melden Sie sich auf dem physischen Azure Stack-Computer für POC1 an.
3. Kopieren und bearbeiten Sie das folgende PowerShell-Skript: Führen Sie das Skript in einer Windows PowerShell ISE mit erhöhten Rechten aus, um die NAT für jedes Azure Stack Development Kit zu konfigurieren. Fügen Sie im Skript Werte zu den Platzhaltern `External BGPNAT address` und `Internal IP address` hinzu:

   ```powershell
   # Designate the external NAT address for the ports that use the IKE authentication.
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 499 `
      -PortEnd 501}
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 4499 `
      -PortEnd 4501}
   # create a static NAT mapping to map the external address to the Gateway
   # Public IP Address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 500 `
      -InternalPort 500}
   # Finally, configure NAT traversal which uses port 4500 to
   # successfully establish the complete IPSEC tunnel over NAT devices
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 4500 `
      -InternalPort 4500}
   ```

4. Wiederholen Sie dieses Verfahren auf POC2.

## <a name="test-the-connection"></a>Testen der Verbindung

Nach der Einrichtung der Site-to-Site-Verbindung sollten Sie sich vergewissern, dass darüber Datenverkehr abgewickelt werden kann. Melden Sie sich für die Überprüfung bei dem virtuellen Computer an, den Sie in der ASDK-Umgebung erstellt haben. Pingen Sie dann den virtuellen Computer, den Sie in der anderen Umgebung erstellt haben.

Um sicherzustellen, dass der Datenverkehr über die Site-to-Site-Verbindung gesendet wird, müssen Sie anstelle der VIP-Adresse die direkte IP-Adresse (DIP) des virtuellen Computers im Remotesubnetz pingen. Dazu müssen Sie die DIP-Adresse am anderen Ende der Verbindung ermitteln. Speichern Sie die Adresse für die spätere Verwendung.

### <a name="sign-in-to-the-tenant-vm-in-poc1"></a>Anmelden beim virtuellen Mandantencomputer in POC1

1. Melden Sie sich beim physischen Azure Stack-Computer für POC1 an, und verwenden Sie anschließend ein Mandantenkonto für die Anmeldung beim Portal.
2. Klicken Sie in der linken Navigationsleiste auf **Compute**.
3. Suchen Sie in der Liste mit den virtuellen Computern nach dem Computer **VM01**, den Sie zuvor erstellt haben.
4. Klicken Sie auf dem Blatt für den virtuellen Computer auf **Verbinden**, und öffnen Sie dann die Datei „VM01.rdp“.

     ![Schaltfläche „Verbinden“](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)

5. Melden Sie sich mit dem Konto an, das Sie beim Erstellen des virtuellen Computers konfiguriert haben.
6. Öffnen Sie ein **Windows PowerShell**-Fenster mit erhöhten Rechten.
7. Geben Sie **ipconfig /all** ein.
8. Suchen Sie in der Ausgabe die **IPv4-Adresse**, und speichern Sie diese zur späteren Verwendung. Diese Adresse wird später von POC2 aus gepingt. In der Beispielumgebung wird die Adresse **10.0.10.4** verwendet, in Ihrer Umgebung lautet sie aber unter Umständen anders. Sie sollte innerhalb des zuvor erstellten Subnetzes **10.0.10.0/24** liegen.
9. Zum Erstellen einer Firewallregel, zulässt, dass der virtuelle Computer auf Pings reagiert, führen Sie den folgenden PowerShell-Befehl aus:

   ```powershell
   New-NetFirewallRule `
    -DisplayName "Allow ICMPv4-In" `
    -Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-poc2"></a>Anmelden beim virtuellen Mandantencomputer in POC2

1. Melden Sie sich beim physischen Azure Stack-Computer für POC2 an, und verwenden Sie anschließend ein Mandantenkonto für die Anmeldung beim Portal.
2. Klicken Sie in der linken Navigationsleiste auf **Compute**.
3. Suchen Sie in der Liste mit den virtuellen Computern nach dem Computer **VM02**, den Sie zuvor erstellt haben.
4. Klicken Sie auf dem Blatt für den virtuellen Computer auf **Verbinden**.
5. Melden Sie sich mit dem Konto an, das Sie beim Erstellen des virtuellen Computers konfiguriert haben.
6. Öffnen Sie ein **Windows PowerShell**-Fenster mit erhöhten Rechten.
7. Geben Sie **ipconfig /all** ein.
8. Es wird eine IPv4-Adresse aus dem Adressbereich **10.0.20.0/24** angezeigt. In der Beispielumgebung wird die Adresse **10.0.20.4** verwendet, Ihre Adresse lautet aber unter Umständen anders.
9. Zum Erstellen einer Firewallregel, zulässt, dass der virtuelle Computer auf Pings reagiert, führen Sie den folgenden PowerShell-Befehl aus:

   ```powershell
   New-NetFirewallRule `
    -DisplayName "Allow ICMPv4-In" `
    -Protocol ICMPv4
   ```

10. Pingen Sie vom virtuellen Computer in POC2 den virtuellen Computer in POC1 durch den Tunnel. Hierzu pingen Sie die DIP-Adresse, die Sie für „VM01“ erfasst haben. In der Beispielumgebung wird die Adresse **10.0.10.4** verwendet. Achten Sie jedoch darauf, dass Sie die Adresse pingen, die Sie in Ihrem Lab notiert haben. Das Ergebnis sollte beispielsweise in etwa wie folgt aussehen:

    ![Ping erfolgreich](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
11. Wenn Sie eine Antwort vom virtuellen Remotecomputer erhalten, war der Test erfolgreich. Sie können das Fenster für den virtuellen Computer schließen. Zum Testen Ihrer Verbindung können andere Arten von Datenübertragungen wie eine Dateikopie durchführen.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Anzeigen der Datenübertragungsstatistik über die Gatewayverbindung

Auf dem Blatt **Verbindung** können Sie die Datenmenge ermitteln, die über die Site-to-Site-Verbindung übertragen wird. Mit diesem Test können Sie auch überprüfen, ob der soeben gesendete Ping tatsächlich die VPN-Verbindung durchlaufen hat.

1. Verwenden Sie Ihr Mandantenkonto zur Anmeldung beim Benutzerportal, während Sie am virtuellen Mandantencomputer in POC2 angemeldet sind.
2. Wechseln Sie zu **Alle Ressourcen**, und wählen Sie dann die Verbindung **POC2-POC1** aus. **Verbindungen** wird angezeigt.
3. Im Fenster **Verbindung** wird die Statistik für **eingehende Daten** und **ausgehende Daten** angezeigt. Die hohen Zahlen im folgenden Screenshot sind auf zusätzliche Dateiübertragungen zurückzuführen. Dort sollten andere Werte als Null angezeigt werden.

    ![Eingehende und ausgehende Daten](media/azure-stack-create-vpn-connection-one-node-tp2/image20.png)
