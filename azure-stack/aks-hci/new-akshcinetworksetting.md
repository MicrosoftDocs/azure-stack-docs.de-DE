---
title: New-AksHciNetworkSetting
author: jessicaguan
description: Mit dem PowerShell-Befehl New-AksHciNetworkSetting wird ein Objekt für ein neues virtuelles Netzwerk erstellt.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 5babbe99254b23d642696b7ce7c5b865105bd906
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "101874435"
---
# <a name="new-akshcinetworksetting"></a>New-AksHciNetworkSetting

## <a name="synopsis"></a>Übersicht
Erstellen eines Objekts für ein neues virtuelles Netzwerk.

## <a name="syntax"></a>Syntax
```powershell
New-AksHciNetworkSetting -vnetName <String>
                         -gateway <String>
                         -dnsServers <String[]>
                         -ipAddressPrefix <String>
                         -vipPoolStart <IP address>
                         -vipPoolEnd <IP address>
                         -k8sNodeIpPoolStart <IP address>
                         -k8sNodeIpPoolEnd <IP address>
                        [-vlanID <int>]
                    
```

## <a name="description"></a>BESCHREIBUNG
Erstellen eines virtuellen Netzwerks, um die DHCP- oder statische IP-Adresse für die Steuerungsebene, den Lastenausgleich, Agent-Endpunkte und einen statischen IP-Bereich für Knoten in allen Kubernetes-Clustern festzulegen. Dieses Cmdlet gibt ein VirtualNetwork-Objekt zurück, das später in den Konfigurationsschritten verwendet werden kann.

## <a name="examples"></a>Beispiele

### <a name="deploy-with-a-static-ip-environment"></a>Bereitstellen mit einer statischen IP-Umgebung

```powershell
PS C:\> $vnet = New-AksHciNetworkSetting -vnetName "External" -k8sNodeIpPoolStart "172.16.10.0" -k8sNodeIpPoolEnd "172.16.10.255" -vipPoolStart "172.16.255.0" -vipPoolEnd "172.16.255.254" -ipAddressPrefix "172.16.0.0/16" -gateway "172.16.0.1" -dnsServers "172.16.0.1" 
PS C:\> Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -vnet $vnet -enableDiagnosticData -cloudservicecidr "172.16.10.10/16"
```

> [!NOTE]
> Die in diesem Beispielbefehl angegebenen Werte müssen für Ihre Umgebung angepasst werden.

### <a name="deploy-with-a-dhcp-environment"></a>Bereitstellen mit einer DHCP-Umgebung

```powershell
PS C:\> $vnet = New-AksHciNetworkSetting -vnetName "External" -vipPoolStart "172.16.255.0" -vipPoolEnd "172.16.255.254" 
```

```powershell
PS C:\> Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -vnet $vnet -enableDiagnosticData"
```

## <a name="parameters"></a>Parameter

### <a name="-vnetname"></a>-vnetName
Der Name des externen Switches. Führen Sie den Befehl `Get-VMSwitch` aus, um eine Liste mit den Namen der verfügbaren Switches abzurufen.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-gateway"></a>-gateway
Die IP-Adresse des Standardgateways des Subnetzes.

```yaml
Type: System.String
Parameter Sets: (StaticIP)
Aliases:

Required: False (This is required when creating a network with a static IP.)
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-dnsservers"></a>-dnsServers
Erforderlich, wenn ein Netzwerk mit einer statischen IP-Adresse erstellt wird. Ein Array von IP-Adressen, die auf die DNS-Server verweisen, die für das Subnetz verwendet werden sollen. Mindestens ein und maximal drei Server können bereitgestellt werden. Das heißt: "8.8.8.8","192.168.1.1".

```yaml
Type: System.String[]
Parameter Sets: (StaticIP)
Aliases:

Required: False (This is required when creating a network with a static IP.)
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-ipaddressprefix"></a>-ipAddressPrefix
Das für die statische IP-Zuweisung zu verwendende Adresspräfix.

```yaml
Type: System.String
Parameter Sets: (StaticIP)
Aliases:

Required: True
Position: Named
Default value: external
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-vippoolstart"></a>-vipPoolStart
Die IP-Startadresse des VIP-Pools. Die Adresse muss innerhalb des Bereichs liegen, der vom DHCP-Server bereitgestellt wird, oder innerhalb des Bereichs, der im Subnetz-CIDR bereitgestellt wird. Die IP-Adressen im VIP-Pool werden für den API-Server und für Kubernetes-Dienste verwendet. Wenn Sie DHCP verwenden, achten Sie außerdem darauf, dass Ihre virtuellen IP-Adressen nicht Teil der DHCP-IP-Reserve sind. Wenn Sie statische IP-Adressen verwenden, stellen Sie sicher, dass Ihre virtuellen IPs aus demselben Subnetz stammen.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-vippoolend"></a>-vipPoolEnd
Die IP-Endadresse des VIP-Pools. Die Adresse muss innerhalb des Bereichs liegen, der vom DHCP-Server bereitgestellt wird, oder innerhalb des Bereichs, der im Subnetz-CIDR bereitgestellt wird. Die IP-Adressen im VIP-Pool werden für den API-Server und für Kubernetes-Dienste verwendet. Wenn Sie DHCP verwenden, achten Sie außerdem darauf, dass Ihre virtuellen IP-Adressen nicht Teil der DHCP-IP-Reserve sind. Wenn Sie statische IP-Adressen verwenden, stellen Sie sicher, dass Ihre virtuellen IPs aus demselben Subnetz stammen.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-k8snodeippoolstart"></a>-k8sNodeIpPoolStart
Die IP-Startadresse eines VM-Pools. Die Adresse muss sich im Bereich des Subnetzes befinden. Dies ist für statische IP-Bereitstellungen erforderlich.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-k8snodeippoolend"></a>-k8sNodeIpPoolEnd
Die IP-Endadresse eines VM-Pools. Die Adresse muss sich im Bereich des Subnetzes befinden. Dies ist für statische IP-Bereitstellungen erforderlich.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-vlanid"></a>-vlanID
Die vLAN-ID für das angegebene Netzwerk. Wenn keine Angabe erfolgt, wird das virtuelle Netzwerk nicht markiert.

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

<!--- ### -macPoolName
The name of the MAC address pool that you wish to use for the Azure Kubernetes Service host VM. The pool will be created with the New-AksHciMacPoolSetting command.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
--->
