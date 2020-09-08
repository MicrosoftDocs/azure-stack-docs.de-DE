---
title: Verbinden von zwei Azure Stack Hub-Instanzen per VNET-Peering
description: Es wird beschrieben, wie Sie zwei Azure Stack Hub-Instanzen per VNET-Peering verbinden.
author: mattbriggs
ms.topic: how-to
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/03/2019
ms.openlocfilehash: 5d8dcba27b1aaed79489b27a2dd6224f6211ac18
ms.sourcegitcommit: 9557a5029cf329599f5b523c68e8305b876108d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2020
ms.locfileid: "88965108"
---
# <a name="connect-two-vnets-through-peering"></a>Verbinden von zwei VNETs per Peering

In diesem Artikel wird beschrieben, wie Sie eine Verbindung zwischen zwei virtuellen Netzwerken in derselben Umgebung herstellen. Im Rahmen der Verbindungseinrichtung werden Sie auch mit der Funktionsweise von VPN-Gateways in Azure Stack Hub vertraut gemacht. Verbinden Sie zwei VNETs in derselben Azure Stack Hub-Umgebung, indem Sie Fortinet FortiGate verwenden. Bei diesem Verfahren werden zwei VNETs mit einer FortiGate NVA (virtuelles Netzwerkgerät, Network Virtual Appliance) in jedem VNET bereitgestellt – jeweils in einer separaten Ressourcengruppe. Darüber hinaus werden die Änderungen beschrieben, die für die Einrichtung eines IPSec-VPN zwischen den beiden VNETs vorgenommen werden müssen. Wiederholen Sie die Schritte in diesem Artikel für jede VNET-Bereitstellung.

## <a name="prerequisites"></a>Voraussetzungen

-   Zugriff auf ein System mit verfügbarer Kapazität, um die Compute-, Netzwerk- und Ressourcenanforderungen für diese Lösung zu erfüllen.

-  Eine Lösung für ein virtuelles Netzwerkgerät (Network Virtual Appliance, NVA), die heruntergeladen und im Azure Stack Hub-Marketplace veröffentlicht wird. Mit einem virtuellen Netzwerkgerät (Network Virtual Appliance, NVA) wird der Fluss des Netzwerkdatenverkehrs aus einem Umkreisnetzwerk in andere Netzwerke oder Subnetze gesteuert. In diesem Verfahren wird die Lösung [FortiGate Next-Generation Firewall – Single VM](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm) genutzt.

-  Mindestens zwei verfügbare FortiGate-Lizenzdateien zum Aktivieren der FortiGate NVA. Informationen zur Beschaffung dieser Lizenzen finden Sie in der Fortinet-Dokumentbibliothek im Artikel zum [Registrieren und Herunterladen Ihrer Lizenz](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license).

    In diesem Verfahren wird die [Bereitstellung einer einzelnen FortiGate-VM](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/632940/single-FortiGate-vm-deployment) durchgeführt. Sie finden die Schritte zum Herstellen einer Verbindung zwischen der FortiGate NVA und dem Azure Stack Hub-VNET in Ihrem lokalen Netzwerk.

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

> [!NOTE]
> \* Wählen Sie andere Adressräume und Subnetzpräfixe aus, falls es für die obigen Angaben zu Überschneidungen mit der lokalen Netzwerkumgebung kommt, einschließlich dem VIP-Pool der beiden Azure Stack Hub-Instanzen. Stellen Sie auch sicher, dass sich die Adressbereiche nicht gegenseitig überschneiden.

## <a name="deploy-the-fortigate-ngfw"></a>Bereitstellen der FortiGate NGFW

1.  Öffnen Sie das Azure Stack Hub-Benutzerportal.

    ![Der Startbildschirm mit der Schaltfläche „+ Ressource erstellen“ wird angezeigt.](./media/azure-stack-network-howto-vnet-to-onprem/image5.png)

2.  Wählen Sie **Ressource erstellen** aus, und suchen Sie nach `FortiGate`.

    !Die Suchergebnisliste zeigt „FortiGate NGFW – Single VM Deployment“ an.](./media/azure-stack-network-howto-vnet-to-onprem/image6.png)

3.  Wählen Sie **FortiGate NGFW** und dann die Option **Erstellen** aus.

4.  Fügen Sie unter **Grundlagen** die Parameter aus der Tabelle unter [Bereitstellungsparameter](#deployment-parameters) ein.

    ![Der Bildschirm „Grundlagen“ enthält Werte aus den Bereitstellungsparametern, die in Listen- und Textfeldern ausgewählt und eingegeben wurden.](./media/azure-stack-network-howto-vnet-to-onprem/image7.png)

5.  Klicken Sie auf **OK**.

6.  Geben Sie die Details zum virtuellen Netzwerk, zu den Subnetzen und zur VM-Größe ein, indem Sie die Tabelle unter [Bereitstellungsparameter](#deployment-parameters) verwenden.

    > [!Warning] 
    > Falls es für das lokale Netzwerk zu einer Überschneidung mit dem IP-Adressbereich `172.16.0.0/16` kommt, müssen Sie einen anderen Netzwerkbereich und entsprechende Subnetze auswählen und einrichten. Wenn Sie andere Namen und Bereiche als in der Tabelle [Bereitstellungsparameter](#deployment-parameters) verwenden möchten, sollten Sie Parameter nutzen, die **nicht** mit dem lokalen Netzwerk in Konflikt stehen. Gehen Sie beim Festlegen des VNET-IP-Bereichs und der Subnetzbereiche im VNET mit Bedacht vor. Der Bereich darf sich nicht mit den IP-Adressbereichen in Ihrem lokalen Netzwerk überschneiden.

7.  Klicken Sie auf **OK**.

8.  Konfigurieren Sie die öffentliche IP-Adresse für die Fortigate NVA:

    ![Das Dialogfeld „IP-Adresszuweisung“ zeigt den Wert „forti1-publicip1“ für „Name der öffentlichen IP-Adresse“ und „Static“ für „Typ der öffentlichen IP-Adresse“ an.](./media/azure-stack-network-howto-vnet-to-onprem/image8.png)

9.  Klicken Sie auf **OK**. Wählen Sie dann **OK**.

10.  Klicken Sie auf **Erstellen**.

Die Bereitstellung dauert ungefähr zehn Minuten.

## <a name="configure-routes-udrs-for-each-vnet"></a>Konfigurieren von Routen (UDRs) für jedes VNET

Führen Sie diese Schritte für beide Bereitstellungen aus („forti1-rg1“ und „forti2-rg1“).

1. Öffnen Sie das Azure Stack Hub-Benutzerportal.

1. Wählen Sie die Option „Ressourcengruppen“ aus. Geben Sie `forti1-rg1` in den Filter ein, und doppelklicken Sie auf die Ressourcengruppe „forti1-rg1“.

    ![Für die Ressourcengruppe „forti1-rg1“ sind zehn Ressourcen aufgeführt.]](./media/azure-stack-network-howto-vnet-to-onprem/image9.png)

1. Wählen Sie die Ressource **forti1-forti1-InsideSubnet-routes-xxxx** aus.

1. Wählen Sie unter **Einstellungen** die Option **Routen** aus.

    ![Die Schaltfläche „Routen“ wird im Dialogfeld „Einstellungen“ ausgewählt.](./media/azure-stack-network-howto-vnet-to-onprem/image10.png)

1. Löschen Sie die Route **to-Internet**.

    ![Die Route „to-Internet“ ist die einzige aufgeführte Route, und sie ist ausgewählt. Es gibt eine Schaltfläche zum Löschen.](./media/azure-stack-network-howto-vnet-to-onprem/image11.png)

1. Wählen Sie *Ja*.

1. Wählen Sie **Hinzufügen** aus, um eine neue Route hinzuzufügen.

1. Geben Sie der Route den Namen `to-onprem`.

1. Geben Sie den IP-Adressbereich für das Netzwerk ein, mit dem der Netzwerkbereich des lokalen Netzwerks definiert wird, mit dem per VPN eine Verbindung hergestellt wird.

1. Wählen Sie unter **Typ des nächsten Hops** die Option **Virtuelles Gerät** und dann `172.16.1.4` aus. Verwenden Sie Ihren IP-Adressbereich, falls er sich von diesem IP-Adressbereich unterscheidet.

    ![Das Dialogfeld „Route hinzufügen“ zeigt die vier Werte an, die ausgewählt und in die Textfelder eingegeben wurden.](./media/azure-stack-network-howto-vnet-to-onprem/image12.png)

1. Wählen Sie **Speichern** aus.

Sie benötigen jeweils eine gültige Lizenzdatei von Fortinet, um eine FortiGate NVA zu aktivieren. Die NVAs funktionieren **erst**, nachdem Sie sie aktiviert haben. Weitere Informationen zum Beschaffen einer Lizenzdatei und die Schritte zum Aktivieren der NVA finden Sie in der Fortinet-Dokumentbibliothek im Artikel zum [Registrieren und Herunterladen Ihrer Lizenz](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license).

Sie müssen zwei Lizenzdateien beschaffen: eine für jede NVA.

## <a name="create-an-ipsec-vpn-between-the-two-nvas"></a>Erstellen eines IPSec-VPN zwischen den beiden NVAs

Führen Sie nach dem Aktivieren der NVAs diese Schritte aus, um ein IPSec-VPN zwischen den beiden NVAs zu erstellen.

Führen Sie die unten angegebenen Schritte jeweils für die forti1-NVA und die forti2-NVA aus:

1. Navigieren Sie zur Seite mit der Übersicht für die fortiX-VM, um die zugewiesene öffentliche IP-Adresse abzurufen:

    ![Die Übersichtsseite der virtuellen Computer „forti1“ zeigt Werte für „forti1“ an, z. B. „Ressourcengruppe“ und „Status“.](./media/azure-stack-network-howto-vnet-to-vnet/image13.png)

1. Kopieren Sie die zugewiesene IP-Adresse, öffnen Sie einen Browser, und fügen Sie die Adresse in die Adressleiste ein. In Ihrem Browser wird ggf. eine Warnung mit dem Hinweis angezeigt, dass das Sicherheitszertifikat nicht vertrauenswürdig ist. Setzen Sie den Vorgang trotzdem fort.

1. Geben Sie den Benutzernamen für den FortiGate-Administrator, den Sie bei der Bereitstellung angegeben haben, und das zugehörige Kennwort ein.

    ![Das Anmeldedialogfeld enthält Textfelder für Benutzer und Kennwort sowie eine Schaltfläche für die Anmeldung.](./media/azure-stack-network-howto-vnet-to-vnet/image14.png)

1. Wählen Sie **System** > **Firmware** aus.

1. Wählen Sie das Feld aus, in dem die aktuelle Firmware angezeigt wird, z. B. `FortiOS v6.2.0 build0866`.

    ![Das Dialogfeld „Firmware“ umfasst den Firmwarebezeichner „FortiOS v6.2.0 build0866“, einen Link zu den Versionshinweisen und zwei Schaltflächen: „Backup config and upgrade“ (Konfiguration sichern und aktualisieren) und „Upgrade“ (Aktualisieren).](./media/azure-stack-network-howto-vnet-to-vnet/image15.png)

1. Wählen Sie **Backup config and upgrade** > **Continue** (Konfiguration sichern und aktualisieren > Weiter) aus.

1. Für die NVA wird die Firmware auf den neuesten Build aktualisiert und ein Neustart durchgeführt. Der Vorgang dauert ungefähr fünf Minuten. Melden Sie sich wieder an der FortiGate-Webkonsole an.

1. Klicken Sie auf **VPN** > **IPSec Wizard** (VPN > IPSec-Assistent).

1. Geben Sie im **VPN Creation Wizard** (Assistent für die VPN-Erstellung) einen Namen für das VPN ein, z. B. `conn1`.

1. Wählen Sie die Option **This site is behind NAT** (Website befindet sich hinter NAT) aus.

    ![Der Screenshot des Assistenten für die VPN-Erstellung zeigt, dass er sich im ersten Schritt, dem VPN-Setup, befindet. Die folgenden Werte sind ausgewählt: „Site to Site“ für den Vorlagentyp, „FortiGate“ für den Remotegerätetyp und „This site is behind NAT“ (Website befindet sich hinter NAT) für die NAT-Konfiguration.](./media/azure-stack-network-howto-vnet-to-vnet/image16.png)

1. Wählen Sie **Weiter** aus.

1. Geben Sie die Remote-IP-Adresse des lokalen VPN-Geräts ein, mit dem Sie eine Verbindung herstellen möchten.

1. Wählen Sie unter **Outgoing Interface** (Ausgangsschnittstelle) die Option **port1** aus.

1. Wählen Sie **Vorinstallierter Schlüssel** aus, und geben Sie einen vorinstallierten Schlüssel ein (und notieren Sie ihn). 

    > [!NOTE]  
    > Sie benötigen diesen Schlüssel zum Einrichten der Verbindung mit dem lokalen VPN-Gerät. Es muss eine *genaue* Übereinstimmung sein.

    ![Der Screenshot des Assistenten für die VPN-Erstellung zeigt, dass er sich im zweiten Schritt (der Authentifizierung) befindet, und die ausgewählten Werte sind hervorgehoben.](./media/azure-stack-network-howto-vnet-to-vnet/image17.png)

1. Wählen Sie **Weiter** aus.

1. Wählen Sie unter **Lokale Schnittstelle** die Option **port2** aus.

1. Geben Sie den Bereich des lokalen Subnetzes ein:
    - forti1: 172.16.0.0/16
    - forti2: 172.17.0.0/16

    Verwenden Sie Ihren IP-Adressbereich, falls er sich von diesem IP-Adressbereich unterscheidet.

1. Geben Sie die entsprechenden Remotesubnetze ein, die das lokale Netzwerk darstellen, mit dem Sie über das lokale VPN-Gerät eine Verbindung herstellen.
    - forti1: 172.16.0.0/16
    - forti2: 172.17.0.0/16

    Verwenden Sie Ihren IP-Adressbereich, falls er sich von diesem IP-Adressbereich unterscheidet.

    ![Der Screenshot des Assistenten für die VPN-Erstellung zeigt, dass er sich im dritten Schritt, Richtlinie und Routing, befindet. Es zeigt die ausgewählten und eingegebenen Werte an.](./media/azure-stack-network-howto-vnet-to-vnet/image18.png)

1. Klicken Sie auf **Erstellen**

1. Wählen Sie **Netzwerk** > **Schnittstellen** aus.

    ![Die Schnittstellenliste zeigt zwei Schnittstellen: „port1“, der konfiguriert wurde, und „port2“, der nicht konfiguriert wurde. Es gibt Schaltflächen zum Erstellen, Bearbeiten und Löschen von Schnittstellen.](./media/azure-stack-network-howto-vnet-to-vnet/image19.png)

1. Doppelklicken Sie auf **port2**.

1. Wählen Sie in der Liste **Rolle** die Option **LAN** und als Adressierungsmodus dann **DHCP** aus.

1. Klicken Sie auf **OK**.

Wiederholen Sie die Schritte für die andere NVA.

## <a name="bring-up-all-phase-2-selectors"></a>Aufrufen aller Phase 2-Selektoren 

Gehen Sie wie folgt vor, nachdem die obigen Vorgänge für *beide* NVAs abgeschlossen wurden:

1.  Wählen Sie auf der FortiGate-Webkonsole „forti2“ **Monitor** > **IPsec Monitor** (Monitor > IPsec-Monitor) aus. 

    ![Der Monitor für die VPN-Verbindung „conn1“ ist aufgelistet. Er wird ebenso wie der entsprechende Phase 2-Selektor als „Ausgefallen“ angezeigt.](./media/azure-stack-network-howto-vnet-to-vnet/image20.png)

2.  Markieren Sie `conn1`, und wählen Sie **Bring Up** > **All Phase 2 Selectors** (Aufrufen > Alle Phase 2-Selektoren) aus.

    ![Der Monitor und der Phase 2-Selektor werden beide als „Aktiv“ angezeigt.](./media/azure-stack-network-howto-vnet-to-vnet/image21.png)

## <a name="test-and-validate-connectivity"></a>Testen und Überprüfen der Konnektivität

Sie sollten jetzt in der Lage sein, zwischen den VNETs über die FortiGate NVAs eine Weiterleitung durchzuführen. Erstellen Sie zum Überprüfen der Verbindung im InsideSubnet jedes VNET eine Azure Stack Hub-VM. Die Erstellung einer Azure Stack Hub-VM kann über das Portal, die CLI oder PowerShell durchgeführt werden. Für die VM-Erstellung gilt Folgendes:

-   Die Azure Stack Hub-VMs sind jeweils im **InsideSubnet** eines VNET angeordnet.

-   Sie wenden während der Erstellung **keine** NSGs auf die VM an. (Entfernen Sie also die NSG, die standardmäßig hinzugefügt wird, falls Sie die VM über das Portal erstellen.)

-   Stellen Sie sicher, dass die VM-Firewallregeln die Kommunikation zulassen, die Sie zum Testen der Konnektivität benötigen. Wir empfehlen Ihnen, zu Testzwecken die Firewall im Betriebssystem vollständig zu deaktivieren, falls dies möglich ist.

## <a name="next-steps"></a>Nächste Schritte

[Azure Stack Hub-Netzwerke: Unterschiede und Überlegungen](azure-stack-network-differences.md)  
[Anbieten einer Netzwerklösung in Azure Stack Hub mit Fortinet FortiGate](../operator/azure-stack-network-solutions-enable.md)  