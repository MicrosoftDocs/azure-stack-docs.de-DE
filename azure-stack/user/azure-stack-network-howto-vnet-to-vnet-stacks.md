---
title: 'Gewusst wie: Einrichten einer VNET-zu-VNET-Verbindung in Azure Stack per Fortinet FortiGate NVA | Microsoft-Dokumentation'
description: Es wird beschrieben, wie Sie eine VNET-zu-VNET-Verbindung per Fortinet FortiGate NVA einrichten.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 10/03/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/03/2019
ms.openlocfilehash: b68b0119e30d39b126aa43c2c8ed4c859073663e
ms.sourcegitcommit: b2d19e12a50195bb8925879ee75c186c9604f313
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/04/2019
ms.locfileid: "71962574"
---
# <a name="how-to-establish-a-vnet-to-vnet-connection-in-azure-stack-with-fortinet-fortigate-nva"></a>Gewusst wie: Einrichten einer VNET-zu-VNET-Verbindung per Fortinet FortiGate NVA

In diesem Artikel verbinden Sie ein VNET einer Azure Stack-Instanz mit einem VNET in einer anderen Azure Stack-Instanz, indem Sie eine Fortinet FortiGate NVA (virtuelles Netzwerkgerät (Network Virtual Appliance, NVA)) verwenden.

In diesem Artikel wird die derzeit geltende Azure Stack-Einschränkung beschrieben, nach der Mandanten nur eine VPN-Verbindung für zwei Umgebungen einrichten dürfen. Benutzer erfahren, wie sie auf einem virtuellen Linux-Computer ein benutzerdefiniertes Gateway einrichten, mit dem mehrere VPN-Verbindungen für unterschiedliche Azure Stack-Instanzen möglich sind. Mit dem Verfahren in diesem Artikel werden zwei VNETs mit einer FortiGate NVA pro VNET bereitgestellt, also eine Bereitstellung pro Azure Stack-Umgebung. Darüber hinaus werden die Änderungen beschrieben, die für die Einrichtung eines IPSec-VPN zwischen den beiden VNETs vorgenommen werden müssen. Die Schritte in diesem Artikel sollten jeweils für jedes VNET auf allen Azure Stack-Instanzen ausgeführt werden. 

## <a name="prerequisites"></a>Voraussetzungen

-  Zugriff auf ein integriertes Azure Stack-System mit verfügbarer Kapazität, um die erforderlichen Compute-, Netzwerk- und Ressourcenanforderungen für diese Lösung zu erfüllen. 

    > [!Note]  
    > Diese Anleitung funktioniert aufgrund der Netzwerkeinschränkungen des Azure Stack Development Kit (ASDK) **nicht** für das ASDK. Weitere Informationen finden Sie unter [Anforderungen und Überlegungen zu ASDK](https://docs.microsoft.com/azure-stack/asdk/asdk-deploy-considerations).

-  Eine Lösung für ein virtuelles Netzwerkgerät (Network Virtual Appliance, NVA), die heruntergeladen und im Azure Stack-Marketplace veröffentlicht wird. Mit einem virtuellen Netzwerkgerät (Network Virtual Appliance, NVA) wird der Fluss des Netzwerkdatenverkehrs aus einem Umkreisnetzwerk in andere Netzwerke oder Subnetze gesteuert. In diesem Verfahren wird die Lösung [FortiGate Next-Generation Firewall – Single VM](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm) genutzt.

-  Mindestens zwei verfügbare FortiGate-Lizenzdateien zum Aktivieren der FortiGate NVA. Informationen zur Beschaffung dieser Lizenzen finden Sie in der Fortinet-Dokumentbibliothek im Artikel zum [Registrieren und Herunterladen Ihrer Lizenz](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license).

    In diesem Verfahren wird die [Bereitstellung einer einzelnen FortiGate-VM](ttps://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/632940/single-FortiGate-vm-deployment) durchgeführt. Sie finden die Schritte zum Herstellen einer Verbindung zwischen der FortiGate NVA und dem Azure Stack-VNET in Ihrem lokalen Netzwerk.

    Weitere Informationen zur Bereitstellung der FortiGate-Lösung als Aktiv/Passiv-Setup (zur Erzielung von Hochverfügbarkeit) finden Sie in der Fortinet-Dokumentbibliothek im Artikel zur [Hochverfügbarkeit für FortiGate-VM in Azure](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/983245/ha-for-FortiGate-vm-on-azure).

## <a name="deployment-parameters"></a>Bereitstellungsparameter

In der folgenden Tabelle sind als Referenz die Parameter zusammengefasst, die in diesen Bereitstellungen verwendet werden:

### <a name="deployment-one-forti1"></a>Erste Bereitstellung: Forti1

| Name der FortiGate-Instanz | Forti1 |
|-----------------------------------|---------------------------|
| BYOL: Lizenz/Version | 6.0.3 |
| Benutzername des FortiGate-Administrators | fortiadmin |
| Ressourcengruppenname | forti1-rg1 |
| Name des virtuellen Netzwerks | forti1vnet1 |
| VNET-Adressraum | 172.16.0.0/16* |
| Name des öffentlichen VNET-Subnetzes | forti1-PublicFacingSubnet |
| Präfix der öffentlichen VNET-Adresse | 172.16.0.0/24* |
| Name des Subnetzes im VNET | forti1-InsideSubnet |
| Präfix des Subnetzes im VNET | 172.16.1.0/24* |
| VM-Größe der FortiGate NVA | Standard F2s_v2 |
| Name der öffentlichen IP-Adresse | forti1-publicip1 |
| Typ der öffentlichen IP-Adresse | statischen |

### <a name="deployment-two-forti2"></a>Zweite Bereitstellung: Forti2

| Name der FortiGate-Instanz | Forti2 |
|-----------------------------------|---------------------------|
| BYOL: Lizenz/Version | 6.0.3 |
| Benutzername des FortiGate-Administrators | fortiadmin |
| Ressourcengruppenname | forti2-rg1 |
| Name des virtuellen Netzwerks | forti2vnet1 |
| VNET-Adressraum | 172.17.0.0/16* |
| Name des öffentlichen VNET-Subnetzes | forti2-PublicFacingSubnet |
| Präfix der öffentlichen VNET-Adresse | 172.17.0.0/24* |
| Name des Subnetzes im VNET | Forti2-InsideSubnet |
| Präfix des Subnetzes im VNET | 172.17.1.0/24* |
| VM-Größe der FortiGate NVA | Standard F2s_v2 |
| Name der öffentlichen IP-Adresse | Forti2-publicip1 |
| Typ der öffentlichen IP-Adresse | statischen |

> [!Note]
> \* Wählen Sie andere Adressräume und Subnetzpräfixe aus, falls es für die obigen Angaben zu Überschneidungen mit der lokalen Netzwerkumgebung kommt, einschließlich dem VIP-Pool der beiden Azure Stack-Instanzen. Stellen Sie auch sicher, dass sich die Adressbereiche nicht gegenseitig überschneiden.**

## <a name="deploy-the-fortigate-ngfw-marketplace-items"></a>Bereitstellen der FortiGate NGFW-Marketplace-Elemente

Wiederholen Sie diese Schritte für beide Azure Stack-Umgebungen. 

1. Öffnen Sie das Azure Stack-Benutzerportal. Stellen Sie sicher, dass Sie Anmeldeinformationen verwenden, die mindestens über Rechte vom Typ „Mitwirkender“ für ein Abonnement verfügen.

    ![](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image5.png)

1. Wählen Sie **Ressource erstellen** aus, und suchen Sie nach `FortiGate`.

    ![](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image6.png)

2. Wählen Sie **FortiGate NGFW** und dann die Option **Erstellen** aus.

3. Fügen Sie unter **Grundlagen** die Parameter aus der Tabelle unter [Bereitstellungsparameter](#deployment-parameters) ein.

    Ihr Formular sollte die folgenden Informationen enthalten:

    ![](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image7.png)

4. Klicken Sie auf **OK**.

5. Geben Sie die Details zum virtuellen Netzwerk, zu den Subnetzen und zur VM-Größe ein, indem Sie die Angaben unter [Bereitstellungsparameter](#deployment-parameters) verwenden.

    Falls Sie andere Namen und Bereiche verwenden möchten, sollten Sie keine Parameter verwenden, die mit den anderen VNET- und FortiGate-Ressourcen in der anderen Azure Stack-Umgebung in Konflikt stehen. Dies gilt besonders beim Festlegen des VNET-IP-Bereichs und der Subnetzbereiche im VNET. Vergewissern Sie sich, dass es nicht zu einer Überschneidung mit den IP-Adressbereichen für das andere VNET kommt, das Sie erstellen.

6. Klicken Sie auf **OK**.

7. Konfigurieren Sie die öffentliche IP-Adresse, die für die FortiGate NVA verwendet wird:

    ![](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image8.png)

8. Wählen Sie **OK** und dann noch einmal **OK** aus.

9. Klicken Sie auf **Erstellen**.

Die Bereitstellung dauert ungefähr zehn Minuten. Sie können nun die Schritte zum Erstellen der anderen FortiGate NVA- und VNET-Bereitstellung in der anderen Azure Stack-Umgebung wiederholen.

## <a name="configure-routes-udrs-for-each-vnet"></a>Konfigurieren von Routen (UDRs) für jedes VNET

Führen Sie diese Schritte für beide Bereitstellungen aus („forti1-rg1“ und „forti2-rg1“).

1. Navigieren Sie im Azure Stack-Portal zur Ressourcengruppe „forti1-rg1“.

    ![](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image9.png)

2. Wählen Sie die Ressource „forti1-forti1-InsideSubnet-routes-xxxx“ aus.

3. Wählen Sie unter **Einstellungen** die Option **Routen** aus.

    ![](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image10.png)

4. Löschen Sie die Route **to-Internet**.

    ![](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image11.png)

5. Wählen Sie **Ja** aus.

6. Wählen Sie **Hinzufügen**.

7. Geben Sie der **Route** den Namen `to-forti1` oder `to-forti2`. Verwenden Sie Ihren IP-Adressbereich, falls er sich von diesem IP-Adressbereich unterscheidet.

8. Geben Sie Folgendes ein:
    - forti1: `172.17.0.0/16`  
    - forti2: `172.16.0.0/16`  

    Verwenden Sie Ihren IP-Adressbereich, falls er sich von diesem IP-Adressbereich unterscheidet.

9. Wählen Sie unter **Typ des nächsten Hops** die Option **Virtuelles Gerät** aus.
    - forti1: `172.16.1.4`  
    - forti2: `172.17.0.4`  

    Verwenden Sie Ihren IP-Adressbereich, falls er sich von diesem IP-Adressbereich unterscheidet.

    ![](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image12.png)

10. Wählen Sie **Speichern** aus.

Wiederholen Sie die Schritte für jede **InsideSubnet**-Route der einzelnen Ressourcengruppen.

## <a name="activate-the-fortigate-nvas-and-configure-an-ipsec-vpn-connection-on-each-nva"></a>Aktivieren der FortiGate NVAs und Konfigurieren einer IPSec-VPN-Verbindung auf jeder NVA

 Sie benötigen jeweils eine gültige Lizenzdatei von Fortinet, um eine FortiGate NVA zu aktivieren. Die NVAs funktionieren **erst**, nachdem Sie sie aktiviert haben. Weitere Informationen zum Beschaffen einer Lizenzdatei und die Schritte zum Aktivieren der NVA finden Sie in der Fortinet-Dokumentbibliothek im Artikel zum [Registrieren und Herunterladen Ihrer Lizenz](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license).

Sie müssen zwei Lizenzdateien beschaffen: eine für jede NVA.

## <a name="create-an-ipsec-vpn-between-the-two-nvas"></a>Erstellen eines IPSec-VPN zwischen den beiden NVAs

Führen Sie nach dem Aktivieren der NVAs diese Schritte aus, um ein IPSec-VPN zwischen den beiden NVAs zu erstellen.

Führen Sie die unten angegebenen Schritte jeweils für die forti1-NVA und die forti2-NVA aus:

1.  Navigieren Sie zur Seite mit der Übersicht für die fortiX-VM, um die zugewiesene öffentliche IP-Adresse abzurufen:

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image13.png)

2.  Kopieren Sie die zugewiesene IP-Adresse, öffnen Sie einen Browser, und fügen Sie die Adresse in die Adressleiste ein. In Ihrem Browser wird ggf. eine Warnung mit dem Hinweis angezeigt, dass das Sicherheitszertifikat nicht vertrauenswürdig ist. Setzen Sie den Vorgang trotzdem fort.

4.  Geben Sie den Benutzernamen für den FortiGate-Administrator, den Sie bei der Bereitstellung angegeben haben, und das zugehörige Kennwort ein.

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image14.png)

5.  Wählen Sie **System** > **Firmware** aus.

6.  Wählen Sie das Feld aus, in dem die aktuelle Firmware angezeigt wird, z. B. `FortiOS v6.2.0 build0866`.

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image15.png)

7.  Wählen Sie **Backup config and upgrade** (Konfiguration sichern und aktualisieren) und dann „Continue“ (Weiter) aus, wenn die entsprechende Aufforderung angezeigt wird.

8.  Für die NVA wird die Firmware auf den neuesten Build aktualisiert und ein Neustart durchgeführt. Der Vorgang dauert ungefähr fünf Minuten. Melden Sie sich wieder an der FortiGate-Webkonsole an.

10.  Klicken Sie auf **VPN** > **IPSec Wizard** (VPN > IPSec-Assistent).

11. Geben Sie im **VPN Creation Wizard** (Assistent für die VPN-Erstellung) einen Namen für das VPN ein, z. B. `conn1`.

12. Wählen Sie die Option **This site is behind NAT** (Website befindet sich hinter NAT) aus.

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image16.png)

13. Klicken Sie auf **Weiter**.

14. Geben Sie die Remote-IP-Adresse des lokalen VPN-Geräts ein, mit dem Sie eine Verbindung herstellen möchten.

15. Wählen Sie unter **Outgoing Interface** (Ausgangsschnittstelle) die Option **port1** aus.

16. Wählen Sie **Vorinstallierter Schlüssel** aus, und geben Sie einen vorinstallierten Schlüssel ein (und notieren Sie ihn). 

    > [!Note]  
    > Sie benötigen diesen Schlüssel zum Einrichten der Verbindung mit dem lokalen VPN-Gerät. Es muss eine *genaue* Übereinstimmung sein.

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image17.png)

17. Klicken Sie auf **Weiter**.

18. Wählen Sie unter **Lokale Schnittstelle** die Option **port2** aus.

19. Geben Sie den Bereich des lokalen Subnetzes ein:
    - forti1: 172.16.0.0/16
    - forti2: 172.17.0.0/16

    Verwenden Sie Ihren IP-Adressbereich, falls er sich von diesem IP-Adressbereich unterscheidet.

20. Geben Sie die entsprechenden Remotesubnetze ein, die das lokale Netzwerk darstellen, mit dem Sie über das lokale VPN-Gerät eine Verbindung herstellen.
    - forti1: 172.16.0.0/16
    - forti2: 172.17.0.0/16

    Verwenden Sie Ihren IP-Adressbereich, falls er sich von diesem IP-Adressbereich unterscheidet.

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image18.png)

21. Klicken Sie auf **Erstellen**

22. Wählen Sie **Netzwerk** > **Schnittstellen** aus.

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image19.png)

23. Doppelklicken Sie auf **port2**.

24. Wählen Sie in der Liste **Rolle** die Option **LAN** und als Adressierungsmodus dann **DHCP** aus.

25. Klicken Sie auf **OK**.

Wiederholen Sie die Schritte für die andere NVA.


## <a name="bring-up-all-phase-2-selectors"></a>Aufrufen aller Phase 2-Selektoren 

Gehen Sie wie folgt vor, nachdem die obigen Vorgänge für **beide** NVAs abgeschlossen wurden:

1.  Wählen Sie auf der FortiGate-Webkonsole „forti2“ **Monitor** > **IPsec Monitor** (Monitor > IPsec-Monitor) aus. 

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image20.png)

2.  Markieren Sie `conn1`, und wählen Sie **Bring Up** > **All Phase 2 Selectors** (Aufrufen > Alle Phase 2-Selektoren) aus.

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image21.png)


## <a name="test-and-validate-connectivity"></a>Testen und Überprüfen der Konnektivität

Sie sollten jetzt in der Lage sein, zwischen den VNETs über die FortiGate NVAs eine Weiterleitung durchzuführen. Erstellen Sie zum Überprüfen der Verbindung im InsideSubnet jedes VNET eine Azure Stack-VM. Die Erstellung einer Azure Stack-VM kann über das Portal, die CLI oder PowerShell durchgeführt werden. Für die VM-Erstellung gilt Folgendes:

-   Die Azure Stack-VMs sind jeweils im **InsideSubnet** eines VNET angeordnet.

-   Sie wenden während der Erstellung **keine** NSGs auf die VM an. (Entfernen Sie beim Erstellen der VM über das Portal also die NSG, die standardmäßig hinzugefügt wird.)

-   Stellen Sie sicher, dass die VM-Firewallregeln die Kommunikation zulassen, die Sie zum Testen der Konnektivität benötigen. Wir empfehlen Ihnen, zu Testzwecken die Firewall im Betriebssystem vollständig zu deaktivieren, falls dies möglich ist.

## <a name="next-steps"></a>Nächste Schritte

[Azure Stack-Netzwerke: Unterschiede und Überlegungen](azure-stack-network-differences.md)  
[Anbieten einer Netzwerklösung in Azure Stack mit Fortinet FortiGate](../operator/azure-stack-network-solutions-enable.md)  