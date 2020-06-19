---
title: Einrichten eines VPN-Gateways für Azure Stack Hub
description: Es wird beschrieben, wie Sie ein VPN-Gateway für Azure Stack Hub einrichten.
author: mattbriggs
ms.topic: how-to
ms.date: 04/20/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/03/2019
ms.openlocfilehash: 17ba031e6aa1c9e7c0699309d98183ed38cbd4e7
ms.sourcegitcommit: 6306e0c2506106ad01ff50010f36466f3325d0a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/09/2020
ms.locfileid: "84631369"
---
# <a name="set-up-vpn-gateway-for-azure-stack-hub-using-fortigate-nva"></a>Einrichten eines VPN-Gateways für Azure Stack Hub per FortiGate NVA

In diesem Artikel wird beschrieben, wie Sie eine VPN-Verbindung mit Ihrer Azure Stack Hub-Instanz herstellen. Ein VPN-Gateway ist eine Art von Gateway für virtuelle Netzwerke, mit dem verschlüsselter Datenverkehr zwischen Ihrem virtuellen Netzwerk in Azure Stack Hub und einem Remote-VPN-Gateway gesendet wird. Im unten angegebenen Verfahren wird ein VNET mit einer FortiGate NVA (Network Virtual Appliance) in einer Ressourcengruppe bereitgestellt. Außerdem werden Schritte zum Einrichten eines IPSec-VPN auf der FortiGate NVA beschrieben.

## <a name="prerequisites"></a>Voraussetzungen

-  Zugriff auf ein integriertes Azure Stack Hub-System mit verfügbarer Kapazität, um die erforderlichen Compute-, Netzwerk- und Ressourcenanforderungen für diese Lösung zu erfüllen. 

    > [!Note]  
    > Diese Anleitung funktioniert aufgrund der Netzwerkeinschränkungen des Azure Stack Development Kit (ASDK) **nicht** für das ASDK. Weitere Informationen finden Sie unter [Anforderungen und Überlegungen zu ASDK](https://docs.microsoft.com/azure-stack/asdk/asdk-deploy-considerations).

-  Zugriff auf ein VPN-Gerät im lokalen Netzwerk, auf dem das integrierte Azure Stack Hub-System gehostet wird. Das Gerät muss einen IPSec-Tunnel erstellen, für den die unter [Bereitstellungsparameter](#deployment-parameters) beschriebenen Parameter erfüllt werden.

-  Eine NVA-Lösung (Network Virtual Appliance) in Ihrem Azure Stack Hub-Marketplace. Mit einem virtuellen Netzwerkgerät (Network Virtual Appliance, NVA) wird der Fluss des Netzwerkdatenverkehrs aus einem Umkreisnetzwerk in andere Netzwerke oder Subnetze gesteuert. In diesem Verfahren wird die Lösung [FortiGate Next-Generation Firewall – Single VM](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm) genutzt.

    > [!Note]  
    > Wenden Sie sich an Ihren Cloudbetreiber, falls **Fortinet FortiGate-VM For Azure BYOL** und **FortiGate NGFW – Single VM Deployment (BYOL)** in Ihrem Azure Stack Hub-Marketplace nicht verfügbar sind.

-  Zum Aktivieren der FortiGate-NVA benötigen Sie mindestens eine verfügbare FortiGate-Lizenzdatei. Informationen zur Beschaffung dieser Lizenzen finden Sie in der Fortinet-Dokumentbibliothek im Artikel zum [Registrieren und Herunterladen Ihrer Lizenz](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license).

    In diesem Verfahren wird die [Bereitstellung einer einzelnen FortiGate-VM](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/632940/single-FortiGate-vm-deployment) durchgeführt. Sie finden die Schritte zum Herstellen einer Verbindung zwischen der FortiGate NVA und dem Azure Stack Hub-VNET in Ihrem lokalen Netzwerk.

    Weitere Informationen zur Bereitstellung der FortiGate-Lösung als Aktiv/Passiv-Setup (zur Erzielung von Hochverfügbarkeit) finden Sie in der Fortinet-Dokumentbibliothek im Artikel zur [Hochverfügbarkeit für FortiGate-VM in Azure](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/983245/ha-for-FortiGate-vm-on-azure).

## <a name="deployment-parameters"></a>Bereitstellungsparameter

In der folgenden Tabelle sind als Referenz die Parameter zusammengefasst, die in diesen Bereitstellungen verwendet werden.

| Parameter | Wert |
|-----------------------------------|---------------------------|
| Name der FortiGate-Instanz | forti1 |
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

> [!Note]
> \* Wählen Sie einen anderen Adressraum und andere Subnetzpräfixe aus, wenn es für `172.16.0.0/16` zu einer Überschneidung mit dem lokalen Netzwerk oder dem Azure Stack Hub-VIP-Pool kommt.

## <a name="deploy-the-fortigate-ngfw-marketplace-items"></a>Bereitstellen der FortiGate NGFW-Marketplace-Elemente

1. Öffnen Sie das Azure Stack Hub-Benutzerportal.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image5.png)

1. Wählen Sie **Ressource erstellen** aus, und suchen Sie nach `FortiGate`.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image6.png)

2. Wählen Sie **FortiGate NGFW** und dann die Option **Erstellen** aus.

3. Fügen Sie unter **Grundlagen** die Parameter aus der Tabelle unter [Bereitstellungsparameter](#deployment-parameters) ein.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image7.png)

1. Klicken Sie auf **OK**.

2. Geben Sie die Details zum virtuellen Netzwerk, zu den Subnetzen und zur VM-Größe ein, indem Sie die Tabelle unter [Bereitstellungsparameter](#deployment-parameters) verwenden.

    > [!Warning] 
    > Falls es für das lokale Netzwerk zu einer Überschneidung mit dem IP-Adressbereich `172.16.0.0/16` kommt, müssen Sie einen anderen Netzwerkbereich und entsprechende Subnetze auswählen und einrichten. Wenn Sie andere Namen und Bereiche als in der Tabelle [Bereitstellungsparameter](#deployment-parameters) verwenden möchten, sollten Sie Parameter nutzen, die **nicht** mit dem lokalen Netzwerk in Konflikt stehen. Gehen Sie beim Festlegen des VNET-IP-Bereichs und der Subnetzbereiche im VNET mit Bedacht vor. Der Bereich darf sich nicht mit den IP-Adressbereichen in Ihrem lokalen Netzwerk überschneiden.

3. Klicken Sie auf **OK**.

4. Konfigurieren Sie die öffentliche IP-Adresse für die FortiGate-NVA:

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image8.png)

5. Klicken Sie auf **OK**. Wählen Sie dann **OK**.

6. Klicken Sie auf **Erstellen**.

    Die Bereitstellung dauert ungefähr zehn Minuten.

## <a name="configure-routes-udr-for-the-vnet"></a>Konfigurieren von Routen (UDR) für das VNET

1. Öffnen Sie das Azure Stack Hub-Benutzerportal.

2. Wählen Sie die Option „Ressourcengruppen“ aus. Geben Sie `forti1-rg1` in den Filter ein, und doppelklicken Sie auf die Ressourcengruppe „forti1-rg1“.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image9.png)

2. Wählen Sie die Ressource „forti1-forti1-InsideSubnet-routes-xxxx“ aus.

3. Wählen Sie unter **Einstellungen** die Option **Routen** aus.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image10.png)

4. Löschen Sie die Route **to-Internet**.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image11.png)

5. Wählen Sie *Ja* aus.

6. Wählen Sie **Hinzufügen** aus, um eine neue Route hinzuzufügen.

7. Geben Sie der Route den Namen `to-onprem`.

8. Geben Sie den IP-Adressbereich für das Netzwerk ein, mit dem der Netzwerkbereich des lokalen Netzwerks definiert wird, mit dem per VPN eine Verbindung hergestellt wird.

9. Wählen Sie unter **Typ des nächsten Hops** die Option **Virtuelles Gerät** und dann `172.16.1.4` aus. Verwenden Sie Ihren IP-Adressbereich, falls er sich von diesem IP-Adressbereich unterscheidet.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image12.png)

10. Wählen Sie **Speichern** aus.

## <a name="activate-the-fortigate-nva"></a>Aktivieren der FortiGate NVA

Aktivieren Sie die FortiGate NVA, und richten Sie auf jeder NVA eine IPSec-VPN-Verbindung ein.

Sie benötigen jeweils eine gültige Lizenzdatei von Fortinet, um eine FortiGate NVA zu aktivieren. Die NVAs funktionieren **erst**, nachdem Sie sie aktiviert haben. Weitere Informationen zum Beschaffen einer Lizenzdatei und die Schritte zum Aktivieren der NVA finden Sie in der Fortinet-Dokumentbibliothek im Artikel zum [Registrieren und Herunterladen Ihrer Lizenz](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license).

Erstellen Sie nach dem Aktivieren der NVAs jeweils einen IPSec-VPN-Tunnel auf der NVA.

1. Öffnen Sie das Azure Stack Hub-Benutzerportal.

2. Wählen Sie die Option „Ressourcengruppen“ aus. Geben Sie `forti1` in den Filter ein, und doppelklicken Sie auf die Ressourcengruppe „forti1“.

3. Doppelklicken Sie auf dem Ressourcengruppenblatt in der Liste mit den Ressourcentypen auf den virtuellen Computer **forti1**.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image13.png)

4. Kopieren Sie die zugewiesene IP-Adresse, öffnen Sie einen Browser, und fügen Sie die IP-Adresse in die Adressleiste ein. Unter Umständen löst die Website eine Warnung mit dem Hinweis aus, dass das Sicherheitszertifikat nicht vertrauenswürdig ist. Setzen Sie den Vorgang trotzdem fort.

5. Geben Sie den Benutzernamen für den FortiGate-Administrator, den Sie bei der Bereitstellung angegeben haben, und das zugehörige Kennwort ein.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image14.png)

6. Wählen Sie **System** > **Firmware** aus.

7. Wählen Sie das Feld aus, in dem die aktuelle Firmware angezeigt wird, z. B. `FortiOS v6.2.0 build0866`.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image15.png)

8. Wählen Sie **Backup config and upgrade** > **Continue** (Konfiguration sichern und aktualisieren > Weiter) aus.

9. Für die NVA wird die Firmware auf den neuesten Build aktualisiert und ein Neustart durchgeführt. Der Vorgang dauert ungefähr fünf Minuten. Melden Sie sich wieder an der FortiGate-Webkonsole an.

10. Klicken Sie auf **VPN** > **IPSec Wizard** (VPN > IPSec-Assistent).

11. Geben Sie im **VPN Creation Wizard** (Assistent für die VPN-Erstellung) einen Namen für das VPN ein, z. B. `conn1`.

12. Wählen Sie die Option **This site is behind NAT** (Website befindet sich hinter NAT) aus.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image16.png)

13. Wählen Sie **Weiter** aus.

14. Geben Sie die Remote-IP-Adresse des lokalen VPN-Geräts ein, mit dem Sie eine Verbindung herstellen möchten.

15. Wählen Sie unter **Outgoing Interface** (Ausgangsschnittstelle) die Option **port1** aus.

16. Wählen Sie **Vorinstallierter Schlüssel** aus, und geben Sie einen vorinstallierten Schlüssel ein (und notieren Sie ihn). 

    > [!Note]  
    > Sie benötigen diesen Schlüssel zum Einrichten der Verbindung mit dem lokalen VPN-Gerät. Es muss eine *genaue* Übereinstimmung sein.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image17.png)

17. Wählen Sie **Weiter** aus.

18. Wählen Sie unter **Lokale Schnittstelle** die Option **port2** aus.

19. Geben Sie den Bereich des lokalen Subnetzes ein:
    - forti1: 172.16.0.0/16
    - forti2: 172.17.0.0/16

    Verwenden Sie Ihren IP-Adressbereich, falls er sich von diesem IP-Adressbereich unterscheidet.

20. Geben Sie die entsprechenden Remotesubnetze ein, die das lokale Netzwerk darstellen, mit dem Sie über das lokale VPN-Gerät eine Verbindung herstellen.

    [](./media/azure-stack-network-howto-vnet-to-onprem/image18.png)

21. Klicken Sie auf **Erstellen**

22. Wählen Sie **Netzwerk** > **Schnittstellen** aus.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image19.png)

23. Doppelklicken Sie auf **port2**.

24. Wählen Sie in der Liste **Rolle** die Option **LAN** und als Adressierungsmodus dann **DHCP** aus.

25. Klicken Sie auf **OK**.

## <a name="configure-the-on-premises-vpn"></a>Konfigurieren des lokalen VPN

Das lokale VPN-Gerät muss entsprechend konfiguriert werden, damit der IPSec-VPN-Tunnel erstellt werden kann. Die folgende Tabelle enthält die Parameter, die Sie zum Einrichten des lokalen VPN-Geräts benötigen. Informationen zum Konfigurieren des lokalen VPN-Geräts finden Sie in der Dokumentation Ihres Geräts.

| Parameter | Wert |
| --- | --- |
| Remotegateway-IP | Öffentliche IP-Adresse, die „forti1“ zugewiesen ist: siehe [Aktivieren der FortiGate NVA](#activate-the-fortigate-nva). |
| Remote-IP-Netzwerk | 172.16.0.0/16 (bei Verwendung des IP-Adressbereichs, der in dieser Anleitung für das VNET angegeben ist). |
| Authentifizierungsmethode = Vorinstallierter Schlüssel (PSK) | Aus Schritt 16.
| IKE-Version | 1 |
| IKE-Modus | Main (ID-Schutz) |
| Phase 1: Vorschlagsalgorithmen | AES128-SHA256, AES256-SHA256, AES128-SHA1, AES256-SHA1 |
| Diffie-Hellman-Gruppe | 14, 5 |

## <a name="create-the-vpn-tunnel"></a>Erstellen des VPN-Tunnels

Nachdem das lokale VPN-Gerät entsprechend konfiguriert wurde, kann der VPN-Tunnel eingerichtet werden.

Von der FortiGate NVA:

1. Navigieren Sie auf der FortiGate-Webkonsole „forti1“ zu **Monitor** > **IPsec Monitor** (Monitor > IPsec-Monitor).

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image20.png)

2. Markieren Sie **conn1**, und wählen Sie **Bring Up** > **All Phase 2 Selectors** (Aufrufen > Alle Phase 2-Selektoren) aus.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image21.png)

## <a name="test-and-validate-connectivity"></a>Testen und Überprüfen der Konnektivität

Sie können über das lokale VPN-Gerät die Weiterleitung zwischen dem VNET-Netzwerk und dem lokalen Netzwerk einrichten.

Überprüfen Sie die Verbindung wie folgt:

1. Erstellen Sie in den Azure Stack Hub-VNETs einen virtuellen Computer und im lokalen Netzwerk ein System. Sie können die Anleitung zum Erstellen eines virtuellen Computers unter [Schnellstart: Erstellen eines virtuellen Windows Server-Computers mit dem Azure Stack Hub-Portal](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-windows-portal) befolgen.

2. Überprüfen Sie beim Erstellen der Azure Stack Hub-VM und beim Vorbereiten des lokalen Systems Folgendes:

-  Die Azure Stack Hub-VM wird im **InsideSubnet** des VNET angeordnet.

-  Das lokale System wird im lokalen Netzwerk innerhalb des definierten IP-Adressbereichs angeordnet, wie dies in der IPSec-Konfiguration definiert ist. Stellen Sie außerdem sicher, dass die IP-Adresse der lokalen Schnittstelle des lokalen VPN-Geräts für das lokale System als Route bereitgestellt wird, über die das Azure Stack Hub-VNET erreichbar ist, z. B. `172.16.0.0/16`.

-  Wenden Sie **keine** NSGs auf den zu erstellenden virtuellen Azure Stack Hub-Computer an. Unter Umständen müssen Sie die NSG entfernen, die standardmäßig hinzugefügt wird, wenn die VM über das Portal erstellt wird.

-  Stellen Sie sicher, dass die Betriebssysteme des lokalen Systems und der Azure Stack Hub-VM keine Firewallregeln für Betriebssysteme aufweisen, durch die die Kommunikation verhindert wird, die Sie zum Testen der Konnektivität verwenden. Wir empfehlen Ihnen, zu Testzwecken die Firewall im Betriebssystem beider Systeme vollständig zu deaktivieren.

## <a name="next-steps"></a>Nächste Schritte

[Azure Stack Hub-Netzwerke: Unterschiede und Überlegungen](azure-stack-network-differences.md)  
[Anbieten einer Netzwerklösung in Azure Stack Hub mit Fortinet FortiGate](../operator/azure-stack-network-solutions-enable.md)  
