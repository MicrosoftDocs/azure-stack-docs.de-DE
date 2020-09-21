---
title: Einrichten einer VNET-zu-VNET-Verbindung in Azure Stack Hub per Fortinet FortiGate NVA
description: Es wird beschrieben, wie Sie in Azure Stack Hub eine VNET-zu-VNET-Verbindung per Fortinet FortiGate NVA einrichten.
author: mattbriggs
ms.topic: how-to
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/03/2019
ms.openlocfilehash: 4a952fe100f29cf8f5478afb73507b2e8bc79ca9
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/15/2020
ms.locfileid: "90574124"
---
# <a name="establish-a-vnet-to-vnet-connection-in-azure-stack-hub-with-fortinet-fortigate-nva"></a>Einrichten einer VNET-zu-VNET-Verbindung in Azure Stack Hub per Fortinet FortiGate NVA

In diesem Artikel verbinden Sie ein VNET einer Azure Stack Hub-Instanz mit einem VNET in einer anderen Azure Stack Hub-Instanz, indem Sie eine Fortinet FortiGate NVA (virtuelles Netzwerkgerät (Network Virtual Appliance, NVA)) verwenden.

In diesem Artikel wird die derzeit geltende Azure Stack Hub-Einschränkung beschrieben, nach der Mandanten nur eine VPN-Verbindung für zwei Umgebungen einrichten dürfen. Benutzer erfahren, wie sie auf einem virtuellen Linux-Computer ein benutzerdefiniertes Gateway einrichten, mit dem mehrere VPN-Verbindungen für unterschiedliche Azure Stack Hub-Instanzen möglich sind. Mit dem Verfahren in diesem Artikel werden zwei VNETs mit einer FortiGate NVA pro VNET bereitgestellt, also eine Bereitstellung pro Azure Stack Hub-Umgebung. Darüber hinaus werden die Änderungen beschrieben, die für die Einrichtung eines IPSec-VPN zwischen den beiden VNETs vorgenommen werden müssen. Die Schritte in diesem Artikel sollten jeweils für jedes VNET auf allen Azure Stack Hub-Instanzen ausgeführt werden. 

## <a name="prerequisites"></a>Voraussetzungen

-  Zugriff auf ein integriertes Azure Stack Hub-System mit verfügbarer Kapazität, um die erforderlichen Compute-, Netzwerk- und Ressourcenanforderungen für diese Lösung zu erfüllen. 

    > [!NOTE]  
    > Diese Anleitung funktioniert aufgrund der Netzwerkeinschränkungen des Azure Stack Development Kit (ASDK) **nicht** für das ASDK. Weitere Informationen finden Sie unter [Anforderungen und Überlegungen zu ASDK](../asdk/asdk-deploy-considerations.md).

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
> \* Wählen Sie andere Adressräume und Subnetzpräfixe aus, falls es für die obigen Angaben zu Überschneidungen mit der lokalen Netzwerkumgebung kommt, einschließlich dem VIP-Pool der beiden Azure Stack Hub-Instanzen. Stellen Sie auch sicher, dass sich die Adressbereiche nicht gegenseitig überschneiden.**

## <a name="deploy-the-fortigate-ngfw-marketplace-items"></a>Bereitstellen der FortiGate NGFW-Marketplace-Elemente

Wiederholen Sie diese Schritte für beide Azure Stack Hub-Umgebungen. 

1. Öffnen Sie das Azure Stack Hub-Benutzerportal. Stellen Sie sicher, dass Sie Anmeldeinformationen verwenden, die mindestens über Rechte vom Typ „Mitwirkender“ für ein Abonnement verfügen.

    ![Der Screenshot zeigt das Portal.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image5.png)

1. Wählen Sie **Ressource erstellen** aus, und suchen Sie nach `FortiGate`.

    ![Der Screenshot zeigt eine einzelne Zeile mit den Ergebnissen der Suche nach „fortigate“. Der Name des gefundenen Elements lautet „FortiGate NGFW – Single VM Deployment (BYOL)“.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image6.png)

1. Wählen Sie **FortiGate NGFW** und dann die Option **Erstellen** aus.

1. Fügen Sie unter **Grundlagen** die Parameter aus der Tabelle unter [Bereitstellungsparameter](#deployment-parameters) ein.

    Ihr Formular sollte die folgenden Informationen enthalten:

    ![Die Textfelder (wie Instanzname und BYOL-Lizenz) des Dialogfelds „Grundlagen“ wurden mit Werten aus der Bereitstellungstabelle ausgefüllt.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image7.png)

1. Klicken Sie auf **OK**.

1. Geben Sie die Details zum virtuellen Netzwerk, zu den Subnetzen und zur VM-Größe ein, indem Sie die Angaben unter [Bereitstellungsparameter](#deployment-parameters) verwenden.

    Falls Sie andere Namen und Bereiche verwenden möchten, sollten Sie keine Parameter verwenden, die mit den anderen VNET- und FortiGate-Ressourcen in der anderen Azure Stack Hub-Umgebung in Konflikt stehen. Dies gilt besonders beim Festlegen des VNET-IP-Bereichs und der Subnetzbereiche im VNET. Vergewissern Sie sich, dass es nicht zu einer Überschneidung mit den IP-Adressbereichen für das andere VNET kommt, das Sie erstellen.

1. Klicken Sie auf **OK**.

1. Konfigurieren Sie die öffentliche IP-Adresse, die für die FortiGate NVA verwendet wird:

    ![Das Textfeld „Name der öffentlichen IP-Adresse“ des Dialogfelds „IP-Adresszuweisung“ zeigt den Wert „forti1-publicip1“ (aus der Bereitstellungstabelle) an.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image8.png)

1. Wählen Sie **OK** und dann noch einmal **OK** aus.

1. Klicken Sie auf **Erstellen**.

Die Bereitstellung dauert ungefähr zehn Minuten. Sie können nun die Schritte zum Erstellen der anderen FortiGate NVA- und VNET-Bereitstellung in der anderen Azure Stack Hub-Umgebung wiederholen.

## <a name="configure-routes-udrs-for-each-vnet"></a>Konfigurieren von Routen (UDRs) für jedes VNET

Führen Sie diese Schritte für beide Bereitstellungen aus („forti1-rg1“ und „forti2-rg1“).

1. Navigieren Sie im Azure Stack Hub-Portal zur Ressourcengruppe „forti1-rg1“.

    ![Dies ist ein Screenshot der Liste der Ressourcen in der Ressourcengruppe „forti1-rg1“.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image9.png)

2. Wählen Sie die Ressource „forti1-forti1-InsideSubnet-routes-xxxx“ aus.

3. Wählen Sie unter **Einstellungen** die Option **Routen** aus.

    ![Der Screenshot zeigt den unter Einstellungen markierten Eintrag für Routen.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image10.png)

4. Löschen Sie die Route **to-Internet**.

    ![Der Screenshot zeigt die hervorgehobene Route „to-Internet“ an. Es gibt eine Schaltfläche zum Löschen.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image11.png)

5. Wählen Sie **Ja**.

6. Wählen Sie **Hinzufügen**.

7. Geben Sie der **Route den Namen ** `to-forti1` oder `to-forti2`. Verwenden Sie Ihren IP-Adressbereich, falls er sich von diesem IP-Adressbereich unterscheidet.

8. Eingeben:
    - forti1: `172.17.0.0/16`  
    - forti2: `172.16.0.0/16`  

    Verwenden Sie Ihren IP-Adressbereich, falls er sich von diesem IP-Adressbereich unterscheidet.

9. Wählen Sie unter **Typ des nächsten Hops** die Option **Virtuelles Gerät** aus.
    - forti1: `172.16.1.4`  
    - forti2: `172.17.0.4`  

    Verwenden Sie Ihren IP-Adressbereich, falls er sich von diesem IP-Adressbereich unterscheidet.

    ![Das Dialogfeld „Route bearbeiten“ für „to-forti2“ enthält Textfelder mit Werten. „Adresspräfix“ ist 172.17.0.0/16, „Typ des nächsten Hops“ ist „Virtuelles Gerät“ und „Adresse des nächsten Hops“ ist 172.16.1.4.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image12.png)

10. Wählen Sie **Speichern** aus.

Wiederholen Sie die Schritte für jede **InsideSubnet**-Route der einzelnen Ressourcengruppen.

## <a name="activate-the-fortigate-nvas-and-configure-an-ipsec-vpn-connection-on-each-nva"></a>Aktivieren der FortiGate NVAs und Konfigurieren einer IPSec-VPN-Verbindung auf jeder NVA

 Sie benötigen jeweils eine gültige Lizenzdatei von Fortinet, um eine FortiGate NVA zu aktivieren. Die NVAs funktionieren **erst**, nachdem Sie sie aktiviert haben. Weitere Informationen zum Beschaffen einer Lizenzdatei und die Schritte zum Aktivieren der NVA finden Sie in der Fortinet-Dokumentbibliothek im Artikel zum [Registrieren und Herunterladen Ihrer Lizenz](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license).

Sie müssen zwei Lizenzdateien beschaffen: eine für jede NVA.

## <a name="create-an-ipsec-vpn-between-the-two-nvas"></a>Erstellen eines IPSec-VPN zwischen den beiden NVAs

Führen Sie nach dem Aktivieren der NVAs diese Schritte aus, um ein IPSec-VPN zwischen den beiden NVAs zu erstellen.

Führen Sie die unten angegebenen Schritte jeweils für die forti1-NVA und die forti2-NVA aus:

1. Navigieren Sie zur Seite mit der Übersicht für die fortiX-VM, um die zugewiesene öffentliche IP-Adresse abzurufen:

    ![Die Übersichtsseite für „forti1“ zeigt die Ressourcengruppe, den Status usw. an.](./media/azure-stack-network-howto-vnet-to-vnet/image13.png)

1. Kopieren Sie die zugewiesene IP-Adresse, öffnen Sie einen Browser, und fügen Sie die Adresse in die Adressleiste ein. In Ihrem Browser wird ggf. eine Warnung mit dem Hinweis angezeigt, dass das Sicherheitszertifikat nicht vertrauenswürdig ist. Setzen Sie den Vorgang trotzdem fort.

1. Geben Sie den Benutzernamen für den FortiGate-Administrator, den Sie bei der Bereitstellung angegeben haben, und das zugehörige Kennwort ein.

    ![Der Screenshot zeigt den Anmeldebildschirm, der eine Anmeldeschaltfläche und Textfelder für Benutzername und Kennwort enthält.](./media/azure-stack-network-howto-vnet-to-vnet/image14.png)

1. Wählen Sie **System** > **Firmware** aus.

1. Wählen Sie das Feld aus, in dem die aktuelle Firmware angezeigt wird, z. B. `FortiOS v6.2.0 build0866`.

    ![Der Screenshot für die Firmware „FortiOS v6.2.0 build0866“ enthält einen Link zu den Versionshinweisen und zwei Schaltflächen: „Backup config and upgrade“ (Konfiguration sichern und aktualisieren) und „Upgrade“ (Aktualisieren).](./media/azure-stack-network-howto-vnet-to-vnet/image15.png)

1. Wählen Sie **Backup config and upgrade** (Konfiguration sichern und aktualisieren) und dann „Continue“ (Weiter) aus, wenn die entsprechende Aufforderung angezeigt wird.

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

    ![Der Screenshot des Assistenten für die VPN-Erstellung zeigt, dass er sich im dritten Schritt, Richtlinie und Routing, befindet und die ausgewählten und eingegebenen Werte anzeigt.](./media/azure-stack-network-howto-vnet-to-vnet/image18.png)

1. Klicken Sie auf **Erstellen**

1. Wählen Sie **Netzwerk** > **Schnittstellen** aus.  

    ![Die Schnittstellenliste zeigt zwei Schnittstellen: „port1“, der konfiguriert wurde, und „port2“, der nicht konfiguriert wurde. Es gibt Schaltflächen zum Erstellen, Bearbeiten und Löschen von Schnittstellen.](./media/azure-stack-network-howto-vnet-to-vnet/image19.png)

1. Doppelklicken Sie auf **port2**.

1. Wählen Sie in der Liste **Rolle** die Option **LAN** und als Adressierungsmodus dann **DHCP** aus.

1. Klicken Sie auf **OK**.

Wiederholen Sie die Schritte für die andere NVA.


## <a name="bring-up-all-phase-2-selectors"></a>Aufrufen aller Phase 2-Selektoren 

Gehen Sie wie folgt vor, nachdem die obigen Vorgänge für **beide** NVAs abgeschlossen wurden:

1.  Wählen Sie auf der FortiGate-Webkonsole „forti2“ **Monitor** > **IPsec Monitor** (Monitor > IPsec-Monitor) aus. 

    ![Der Monitor für die VPN-Verbindung „conn1“ ist aufgelistet. Er wird ebenso wie der entsprechende Phase 2-Selektor als „Ausgefallen“ angezeigt.](./media/azure-stack-network-howto-vnet-to-vnet/image20.png)

2.  Markieren Sie `conn1`, und wählen Sie **Bring Up** > **All Phase 2 Selectors** (Aufrufen > Alle Phase 2-Selektoren) aus.

    ![Der Monitor und der Phase 2-Selektor werden beide als „Aktiv“ angezeigt.](./media/azure-stack-network-howto-vnet-to-vnet/image21.png)


## <a name="test-and-validate-connectivity"></a>Testen und Überprüfen der Konnektivität

Sie sollten jetzt in der Lage sein, zwischen den VNETs über die FortiGate NVAs eine Weiterleitung durchzuführen. Erstellen Sie zum Überprüfen der Verbindung im InsideSubnet jedes VNET eine Azure Stack Hub-VM. Die Erstellung einer Azure Stack Hub-VM kann über das Portal, die CLI oder PowerShell durchgeführt werden. Für die VM-Erstellung gilt Folgendes:

-   Die Azure Stack Hub-VMs sind jeweils im **InsideSubnet** eines VNET angeordnet.

-   Sie wenden während der Erstellung **keine** NSGs auf die VM an. (Entfernen Sie beim Erstellen der VM über das Portal also die NSG, die standardmäßig hinzugefügt wird.)

-   Stellen Sie sicher, dass die VM-Firewallregeln die Kommunikation zulassen, die Sie zum Testen der Konnektivität benötigen. Wir empfehlen Ihnen, zu Testzwecken die Firewall im Betriebssystem vollständig zu deaktivieren, falls dies möglich ist.

## <a name="next-steps"></a>Nächste Schritte

[Azure Stack Hub-Netzwerke: Unterschiede und Überlegungen](azure-stack-network-differences.md)  
[Anbieten einer Netzwerklösung in Azure Stack Hub mit Fortinet FortiGate](../operator/azure-stack-network-solutions-enable.md)  
