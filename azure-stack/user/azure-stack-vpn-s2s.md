---
title: Konfigurieren von Site-to-Site-VPN-Verbindungen mit IPsec/IKE
description: Informationen zur IPsec/IKE-Richtlinie und Ihrer Konfiguration für Site-to-Site-VPN- oder VNET-zu-VNET-Verbindungen in Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 05/07/2020
ms.author: sethm
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 2456bd234b8affaecf061871ca701f45088f17c4
ms.sourcegitcommit: 9894804f31527234d43f4a93a9b7c106c8540435
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967793"
---
# <a name="configure-ipsecike-policy-for-site-to-site-vpn-connections"></a>Konfigurieren einer IPsec/IKE-Richtlinie für Standort-zu-Standort-VPN-Verbindungen

Dieser Artikel enthält die Schritte zum Konfigurieren einer IPsec/IKE-Richtlinie für Site-to-Site-VPN-Verbindungen (S2S) in Azure Stack Hub.

>[!NOTE]
> Sie müssen Azure Stack Hub Build **1809** oder höher ausführen, um diese Funktion zu verwenden.  Wenn Sie aktuell einen niedrigeren Build als 1809 ausführen, aktualisieren Sie Ihr Azure Stack Hub-System auf den neuesten Build, bevor Sie mit den Schritten in diesem Artikel fortfahren.

## <a name="ipsec-and-ike-policy-parameters-for-vpn-gateways"></a>IPsec- und IKE-Richtlinienparameter für VPN-Gateways

Der IPsec- und IKE-Protokollstandard unterstützt ein breites Spektrum von Kryptografiealgorithmen in verschiedenen Kombinationen. Informationen zu den in Azure Stack Hub für die Erfüllung Ihrer Anforderungen an Compliance oder Sicherheit unterstützten Parametern finden Sie unter [IPsec/IKE-Parameter](azure-stack-vpn-gateway-settings.md#ipsecike-parameters).

Dieser Artikel enthält eine Anleitung zum Erstellen und Konfigurieren einer IPsec/IKE-Richtlinie und zu ihrem Anwenden auf eine neue oder vorhandene Verbindung.

## <a name="considerations"></a>Überlegungen

Beachten Sie die folgenden wichtigen Informationen, wenn Sie diese Richtlinien verwenden:

- Die IPsec/IKE-Richtlinie kann nur für die Gateway-SKUs *Standard* und *HighPerformance* (routenbasiert) verwendet werden.

- Pro Verbindung kann jeweils nur eine Richtlinienkombination angegeben werden.

- Sie müssen alle Algorithmen und Parameter für IKE (Hauptmodus) und IPsec (Schnellmodus) angeben. Partielle Richtlinien sind nicht zulässig.

- Vergewissern Sie sich in den Spezifikationen Ihres VPN-Geräteanbieters, dass die Richtlinie von Ihren lokalen VPN-Geräten unterstützt wird. Site-to-Site-Verbindungen können nicht hergestellt werden, wenn die Richtlinien inkompatibel sind.

## <a name="part-1---workflow-to-create-and-set-ipsecike-policy"></a>Teil 1: Workflow zum Erstellen und Festlegen der IPsec/IKE-Richtlinie

In diesem Abschnitt wird der Workflow zum Erstellen und Aktualisieren der IPsec/IKE-Richtlinie für eine Site-to-Site-VPN-Verbindung beschrieben:

1. Erstellen eines virtuellen Netzwerks und eines VPN-Gateways

2. Erstellen eines Gateways für das lokale Netzwerk für standortübergreifende Verbindungen

3. Erstellen einer IPsec/IKE-Richtlinie mit ausgewählten Algorithmen und Parametern

4. Erstellen einer IPsec-Verbindung mit der IPsec/IKE-Richtlinie

5. Hinzufügen/Aktualisieren/Entfernen einer IPsec/IKE-Richtlinie für eine vorhandene Verbindung

Die Anweisungen in diesem Artikel helfen Ihnen beim Einrichten und Konfigurieren von IPsec/IKE-Richtlinien, wie in der folgenden Abbildung dargestellt:

![Einrichten und Konfigurieren von IPsec-/IKE-Richtlinien](media/azure-stack-vpn-s2s/site-to-site.svg)

## <a name="part-2---supported-cryptographic-algorithms-and-key-strengths"></a>Teil 2: Unterstützte Kryptografiealgorithmen und Schlüsselstärken

Die folgende Tabelle gibt Aufschluss über die unterstützten Kryptografiealgorithmen und Schlüsselstärken, die von den Azure Stack Hub-Kunden konfiguriert werden können:

| IPsec/IKEv2                                          | Tastatur                                                                  |
|------------------------------------------------------|--------------------------------------------------------------------------|
| IKEv2-Verschlüsselung                                     | AES256, AES192, AES128, DES3, DES                                        |
| IKEv2-Integrität                                      | SHA384, SHA256, SHA1, MD5                                                |
| DH-Gruppe                                             | ECP384, ECP256, DHGroup24, DHGroup14, DHGroup2, DHGroup1                 |
| IPsec-Verschlüsselung                                     | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, keine |
| IPsec-Integrität                                      | GCMASE256, GCMAES192, GCMAES128                                          |
| PFS-Gruppe                                            | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, PFSMM, Keine                  |
| QM-SA-Gültigkeitsdauer                                       | (Optional: Wenn kein Wert angegeben wird, werden die Standardwerte verwendet.)<br />                         Sekunden (ganze Zahl; min. 300/Standard: 27.000 Sekunden)<br />                         KB (ganze Zahl; min. 1.024/Standard: 102.400.000 KB) |
| Datenverkehrsselektor                                     | Richtlinienbasierte Datenverkehrsselektoren werden in Azure Stack Hub nicht unterstützt.         |

- Ihre lokale VPN-Gerätekonfiguration muss folgenden Algorithmus- und Parameterangaben für die Azure-IPsec-/IKE-Richtlinie entsprechen oder selbige enthalten:

  - IKE-Verschlüsselungsalgorithmus (Hauptmodus/Phase 1)
  - IKE-Integritätsalgorithmus (Hauptmodus/Phase 1)
  - DH-Gruppe (Hauptmodus/Phase 1)
  - IPsec-Verschlüsselungsalgorithmus (Schnellmodus/Phase 2)
  - IPsec-Integritätsalgorithmus (Schnellmodus/Phase 2)
  - PFS-Gruppe (Schnellmodus/Phase 2)
  - Bei der SA-Gültigkeitsdauer handelt es sich lediglich um eine lokale Angabe. Diese muss nicht übereinstimmen.

- Wenn GCMAES als IPsec-Verschlüsselungsalgorithmus verwendet wird, müssen Sie denselben GCMAES-Algorithmus und dieselbe Schlüssellänge für die IPsec-Integrität auswählen. Beispiel: Verwendung von GCMAES128 für beide.

- Für die obige Tabelle gilt Folgendes:

  - IKEv2 entspricht Hauptmodus oder Phase 1
  - IPsec entspricht Hauptmodus oder Phase 2
  - Die DH-Gruppe gibt die im Hauptmodus oder in Phase 1 verwendete Diffie-Hellmen-Gruppe an
  - Die PFS-Gruppe gibt die im Schnellmodus oder in Phase 2 verwendete Diffie-Hellmen-Gruppe an

- Die SA-Gültigkeitsdauer von IKEv2 (Hauptmodus) ist für die Azure Stack Hub-VPN-Gateways auf 28.800 Sekunden festgelegt.

Die folgende Tabelle enthält die entsprechenden Diffie-Hellman-Gruppen, die von der benutzerdefinierten Richtlinie unterstützt werden:

| Diffie-Hellman-Gruppe | DHGroup   | PFSGroup      | Schlüssellänge    |
|----------------------|-----------|---------------|---------------|
| 1                    | DHGroup1  | PFS1          | 768-Bit-MODP  |
| 2                    | DHGroup2  | PFS2          | 1024-Bit-MODP |
| 14                   | DHGroup14<br/>DHGroup2048 | PFS2048       | 2048-Bit-MODP |
| 19                   | ECP256    | ECP256        | 256-Bit-ECP   |
| 20                   | ECP384    | ECP384        | 384-Bit-ECP   |
| 24                   | DHGroup24 | PFS24         | 2048-Bit-MODP |

Weitere Informationen finden Sie unter [RFC3526](https://tools.ietf.org/html/rfc3526) und [RFC5114](https://tools.ietf.org/html/rfc5114).

## <a name="part-3---create-a-new-site-to-site-vpn-connection-with-ipsecike-policy"></a>Teil 3: Erstellen einer neuen Site-to-Site-VPN-Verbindung mit einer IPsec/IKE-Richtlinie

In diesem Abschnitt werden die Schritte zum Erstellen einer Site-to-Site-VPN-Verbindung mit einer IPsec/IKE-Richtlinie beschrieben. Mit den folgenden Schritten wird die Verbindung erstellt, wie in der folgenden Abbildung dargestellt:

![site-to-site-policy](media/azure-stack-vpn-s2s/site-to-site.svg)

Eine ausführlichere Anleitung zum Erstellen einer Site-to-Site-VPN-Verbindung finden Sie unter [Erstellen einer Site-to-Site-VPN-Verbindung](/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell).

### <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen, stellen Sie sicher, dass die folgenden Voraussetzungen erfüllt sind:

- Ein Azure-Abonnement. Wenn Sie noch kein Azure-Abonnement besitzen, können Sie Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) aktivieren oder sich für ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) registrieren.

- Die Azure Resource Manager-PowerShell-Cmdlets. Weitere Informationen zum Installieren der PowerShell-Cmdlets finden Sie unter [Installieren von PowerShell für Azure Stack Hub](../operator/azure-stack-powershell-install.md).

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Schritt 1: Erstellen des virtuellen Netzwerks, VPN-Gateways und Gateways des lokalen Netzwerks

#### <a name="1-declare-variables"></a>1. Deklarieren von Variablen

Bei dieser Übung beginnen Sie mit dem Deklarieren der folgenden Variablen. Achten Sie darauf, die Platzhalter durch Ihre eigenen Werte zu ersetzen, wenn Sie die Konfiguration für die Produktion durchführen:

```powershell
$Sub1 = "<YourSubscriptionName>"
$RG1 = "TestPolicyRG1"
$Location1 = "East US 2"
$VNetName1 = "TestVNet1"
$FESubName1 = "FrontEnd"
$BESubName1 = "Backend"
$GWSubName1 = "GatewaySubnet"
$VNetPrefix11 = "10.11.0.0/16"
$VNetPrefix12 = "10.12.0.0/16"
$FESubPrefix1 = "10.11.0.0/24"
$BESubPrefix1 = "10.12.0.0/24"
$GWSubPrefix1 = "10.12.255.0/27"
$DNS1 = "8.8.8.8"
$GWName1 = "VNet1GW"
$GW1IPName1 = "VNet1GWIP1"
$GW1IPconf1 = "gw1ipconf1"
$Connection16 = "VNet1toSite6"
$LNGName6 = "Site6"
$LNGPrefix61 = "10.61.0.0/16"
$LNGPrefix62 = "10.62.0.0/16"
$LNGIP6 = "131.107.72.22"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Herstellen einer Verbindung mit Ihrem Abonnement und Erstellen einer neuen Ressourcengruppe

Stellen Sie sicher, dass Sie in den PowerShell-Modus wechseln, um die Ressourcen-Manager-Cmdlets zu verwenden. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit Azure Stack Hub über PowerShell als Benutzer](azure-stack-powershell-configure-user.md).

Öffnen Sie die PowerShell-Konsole, und stellen Sie eine Verbindung mit Ihrem Konto her. Beispiel:

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3. Erstellen des virtuellen Netzwerks, VPN-Gateways und Gateways des lokalen Netzwerks

Im folgenden Beispiel wird das virtuelle Netzwerk **TestVNet1** zusammen mit drei Subnetzen und dem VPN-Gateway erstellt. Beim Ersetzen der Werte ist es wichtig, dass Sie Ihrem Gatewaysubnetz den Namen **GatewaySubnet** geben. Wenn Sie einen anderen Namen verwenden, tritt beim Erstellen des Gateways ein Fehler auf.

```powershell
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1 = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1

$subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" `
-VirtualNetwork $vnet1

$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 `
-Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1

New-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 `
-Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix `
$LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-site-to-site-vpn-connection-with-an-ipsecike-policy"></a>Schritt 2: Erstellen einer Site-to-Site-VPN-Verbindung mit einer IPsec/IKE-Richtlinie

#### <a name="1-create-an-ipsecike-policy"></a>1. Erstellen Sie eine IPsec/IKE-Richtlinie.

Das Beispielskript erstellt eine IPsec/IKE-Richtlinie mit den folgenden Algorithmen und Parametern:

- IKEv2: AES128, SHA1, DHGroup14
- IPsec: AES256, SHA256, keine, SA-Gültigkeitsdauer 14.400 Sekunden und 102.400.000 KB

```powershell
$ipsecpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup none -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

Wenn Sie GCMAES für IPsec verwenden, müssen für die IPsec-Verschlüsselung und -Integrität derselbe GCMAES-Algorithmus und dieselbe Schlüssellänge verwendet werden.

#### <a name="2-create-the-site-to-site-vpn-connection-with-the-ipsecike-policy"></a>2. Erstellen der Site-to-Site-VPN-Verbindung mit der IPsec/IKE-Richtlinie

Erstellen Sie eine Site-to-Site-VPN-Verbindung, und wenden Sie die zuvor erstellte IPsec/IKE-Richtlinie an:

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$lng6 = Get-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'Azs123'
```

> [!IMPORTANT]
> Nachdem für eine Verbindung eine IPsec/IKE-Richtlinie angegeben wurde, sendet bzw. akzeptiert das Azure-VPN-Gateway den IPsec/IKE-Vorschlag mit angegebenen Kryptografiealgorithmen und Schlüsselstärken nur für die jeweilige Verbindung. Achten Sie darauf, dass Ihr lokales VPN-Gerät für die Verbindung die exakte Richtlinienkombination nutzt bzw. akzeptiert, da der Site-to-Site-VPN-Tunnel andernfalls nicht hergestellt werden kann.

## <a name="part-4---update-ipsecike-policy-for-a-connection"></a>Teil 4: Aktualisieren der IPsec/IKE-Richtlinie für eine Verbindung

Im vorherigen Abschnitt wurde gezeigt, wie Sie eine IPsec/IKE-Richtlinie für eine vorhandene Site-to-Site-Verbindung verwalten. In diesem Abschnitt werden die folgenden Vorgänge für eine Verbindung beschrieben:

1. Anzeigen der IPsec/IKE-Richtlinie einer Verbindung
2. Hinzufügen oder Aktualisieren der IPsec/IKE-Richtlinie für eine Verbindung
3. Entfernen der IPsec/IKE-Richtlinie aus einer Verbindung

> [!NOTE]
> Die IPsec-/IKE-Richtlinie wird nur für routenbasierte VPN-Gateways vom Typ *Standard* und *HighPerformance* unterstützt. Sie funktioniert nicht bei Gateways mit der SKU *Basic*.

### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1. Anzeigen der IPsec/IKE-Richtlinie einer Verbindung

Im folgenden Beispiel wird veranschaulicht, wie Sie die für eine Verbindung konfigurierte IPsec/IKE-Richtlinie abrufen. Die Skripts bauen auf den obigen Übungen auf:

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Mit dem letzten Befehl wird die aktuelle IPsec/IKE-Richtlinie, die für die Verbindung konfiguriert wurde (sofern vorhanden), aufgelistet. Das folgende Beispiel zeigt eine Beispielausgabe für die Verbindung:

```shell
SALifeTimeSeconds : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption : AES256
IpsecIntegrity : SHA256
IkeEncryption : AES128
IkeIntegrity : SHA1
DhGroup : DHGroup14
PfsGroup : None
```

Wurde keine IPsec/IKE-Richtlinie konfiguriert, generiert der Befehl `$connection6.policy` eine leere Rückgabe. Dies bedeutet nicht, dass IPsec/IKE nicht für die Verbindung konfiguriert wurde, sondern dass keine benutzerdefinierte IPsec/IKE-Richtlinie vorhanden ist. Für die eigentliche Verbindung wird die Standardrichtlinie verwendet, die zwischen Ihrem lokalen VPN-Gerät und dem Azure-VPN-Gateway ausgehandelt wurde.

### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2. Hinzufügen oder Aktualisieren einer IPsec/IKE-Richtlinie für eine Verbindung

Die Schritte zum Hinzufügen einer neuen Richtlinie oder Aktualisieren einer vorhandenen Richtlinie für eine Verbindung sind identisch: Erstellen Sie eine neue Richtlinie, und wenden Sie sie anschließend auf die Verbindung an:

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$newpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

$connection6.SharedKey = "AzS123"

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

Sie können die Verbindung erneut abrufen, um zu überprüfen, ob die Richtlinie aktualisiert wird:

```powershell
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Die Ausgabe der letzten Zeile sollte wie im folgenden Beispiel aussehen:

```shell
SALifeTimeSeconds : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption : AES256
IpsecIntegrity : SHA256
IkeEncryption : AES128
IkeIntegrity : SHA1
DhGroup : DHGroup14
PfsGroup : None
```

### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3. Entfernen einer IPsec/IKE-Richtlinie aus einer Verbindung

Wenn Sie die benutzerdefinierte Richtlinie für eine Verbindung entfernen, verwendet das Azure-VPN-Gateway wieder die [IPsec/IKE-Standardvorschläge](azure-stack-vpn-gateway-settings.md#ipsecike-parameters) und beginnt eine neue Aushandlung mit Ihrem lokalen VPN-Gerät.

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.SharedKey = "AzS123"
$currentpolicy = $connection6.IpsecPolicies[0]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

Mit dem gleichen Skript können Sie prüfen, ob die Richtlinie für die Verbindung entfernt wurde.

## <a name="next-steps"></a>Nächste Schritte

- [VPN-Gatewaykonfigurationseinstellungen für Azure Stack Hub](azure-stack-vpn-gateway-settings.md)
