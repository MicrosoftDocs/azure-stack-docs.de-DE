---
title: Verwenden der Rechenzentrumsfirewall für SDN in Azure Stack HCI
description: Dieses Thema gibt den Einstieg in das Verwenden der Rechenzentrumsfirewall für softwaredefinierte Netzwerke in Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 11/17/2020
ms.openlocfilehash: 833780947bd698a0e39709668715372bd8508e90
ms.sourcegitcommit: 40d3f3f0ac088d1590d1fb64ca05ac1dabf4e00c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94881235"
---
# <a name="use-datacenter-firewall-for-software-defined-networking-in-azure-stack-hci"></a>Verwenden der Rechenzentrumsfirewall für softwaredefinierte Netzwerke in Azure Stack HCI

> Gilt für: Azure Stack HCI, Version 20H2; Windows Server 2019

Dieses Thema enthält Anweisungen zum Konfigurieren von Zugriffssteuerungslisten (Access Control Lists, ACLs), um über Windows PowerShell Datenverkehr mit der [Rechenzentrumsfirewall](../concepts/datacenter-firewall-overview.md) für softwaredefinierte Netzwerke (SDN) in Azure Stack HCI zu verwalten. Sie aktivieren und konfigurieren die Rechenzentrumsfirewall, indem Sie ACLs erstellen, die auf ein Subnetz oder eine Netzwerkschnittstelle angewendet werden. In den Beispielskripts in diesem Thema werden Windows PowerShell-Befehle verwendet, die aus dem **NetworkController**-Modul exportiert wurden. Sie können auch Windows Admin Center verwenden, um ACLs zu konfigurieren und zu verwalten.

## <a name="configure-datacenter-firewall-to-allow-all-traffic"></a>Konfigurieren der Rechenzentrumsfirewall so, dass jeglicher Datenverkehr zugelassen wird

Nachdem Sie SDN bereitgestellt haben, sollten Sie die elementare Netzwerkkonnektivität in Ihrer neuen Umgebung testen. Erstellen Sie hierzu eine Regel für die Rechenzentrumsfirewall, die den gesamten Netzwerkdatenverkehr ohne Einschränkung zulässt.

Verwenden Sie die Einträge aus der folgenden Tabelle, um eine Reihe von Regeln zu erstellen, der den gesamten eingehenden und ausgehenden Netzwerkdatenverkehr zulassen.

| Quell-IP | Ziel-IP | Protocol | Quellport | Zielport | Direction | Aktion | Priorität |
|:---------:|:--------------:|:--------:|:-----------:|:----------------:|:---------:|:------:|:--------:|
|    \*     |       \*       |   Alle    |     \*      |        \*        |  Eingehend  | Allow  |   100    |
|    \*     |       \*       |   All    |     \*      |        \*        | Ausgehend  | Allow  |   110    |

---

In diesem Beispiel erstellen Sie eine ACL mit zwei Regeln:

1. **AllowAll_Inbound**: Lässt zu, dass jeglicher Netzwerkdatenverkehr die Netzwerkschnittstelle nach innen passieren kann, für die diese ACL konfiguriert ist.
2. **AllowAll_Outbound**: Lässt zu, dass jeglicher Datenverkehr die Netzwerkschnittstelle nach außen passieren kann. Diese ACL, die durch die Ressourcen-ID „AllowAll-1“ gekennzeichnet ist, kann jetzt in virtuellen Subnetzen und Netzwerkschnittstellen verwendet werden.

Stellen Sie zunächst eine Verbindung mit einem der Clusterknoten her, indem Sie eine PowerShell-Sitzung öffnen:

```PowerShell
Enter-PSSession <server-name>
```

Führen Sie das folgende Skript aus, um die ACL zu erstellen:

```PowerShell
$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Allow"
$ruleproperties.SourceAddressPrefix = "*"
$ruleproperties.DestinationAddressPrefix = "*"
$ruleproperties.Priority = "100"
$ruleproperties.Type = "Inbound"
$ruleproperties.Logging = "Enabled"
$aclrule1 = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule1.Properties = $ruleproperties
$aclrule1.ResourceId = "AllowAll_Inbound"
$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Allow"
$ruleproperties.SourceAddressPrefix = "*"
$ruleproperties.DestinationAddressPrefix = "*"
$ruleproperties.Priority = "110"
$ruleproperties.Type = "Outbound"
$ruleproperties.Logging = "Enabled"
$aclrule2 = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule2.Properties = $ruleproperties
$aclrule2.ResourceId = "AllowAll_Outbound"
$acllistproperties = new-object Microsoft.Windows.NetworkController.AccessControlListProperties
$acllistproperties.AclRules = @($aclrule1, $aclrule2)
New-NetworkControllerAccessControlList -ResourceId "AllowAll" -Properties $acllistproperties -ConnectionUri <NC REST FQDN>
```

>[!NOTE]
>Die Windows PowerShell-Befehlsreferenz für „NetworkController“ befindet sich im Thema [NetworkController-Cmdlets](/powershell/module/networkcontroller/).

## <a name="use-acls-to-limit-traffic-on-a-subnet"></a>Verwenden von ACLs, um Datenverkehr in einem Subnetz einzuschränken
In diesem Beispiel erstellen Sie eine ACL, die verhindert, dass virtuelle Computer (Virtual Machines, VMs), die zum Subnetz 192.168.0.0/24 gehören, miteinander kommunizieren können. Diese Art von ACL ist nützlich, um die Fähigkeit eines Angreifers einzuschränken, sich innerhalb des Subnetzes auszubreiten, während die VMs weiterhin Anforderungen von außerhalb des Subnetzes empfangen und mit anderen Diensten in anderen Subnetzen kommunizieren können.

|   Quell-IP    | Ziel-IP | Protocol | Quellport | Zielport | Direction | Aktion | Priorität |
|:--------------:|:--------------:|:--------:|:-----------:|:----------------:|:---------:|:------:|:--------:|
|  192.168.0.1   |       \*       |   Alle    |     \*      |        \*        |  Eingehend  | Allow  |   100    |
|       \*       |  192.168.0.1   |   Alle    |     \*      |        \*        | Ausgehend  | Allow  |   101    |
| 192.168.0.0/24 |       \*       |   Alle    |     \*      |        \*        |  Eingehend  | Block  |   102    |
|       \*       | 192.168.0.0/24 |   Alle    |     \*      |        \*        | Ausgehend  | Block  |   103    |
|       \*       |       \*       |   Alle    |     \*      |        \*        |  Eingehend  | Allow  |   104    |
|       \*       |       \*       |   Alle    |     \*      |        \*        | Ausgehend  | Allow  |   105    |

---

Die ACL, die mit dem folgenden Beispielskript erstellt wurde und durch die Ressourcen-ID **Subnet-192-168-0-0** gekennzeichnet ist, kann nun auf das Subnetz des virtuellen Netzwerks angewendet werden, das die Subnetzadresse „192.168.0.0/24“ hat. Diese ACL-Regeln werden automatisch auf jede Netzwerkschnittstelle angewendet, die mit diesem Subnetz des virtuellen Netzwerks verbunden ist.

Das folgende Skript ist ein Beispielskript zum Erstellen dieser ACL über die NetworkController-REST-API:

```PowerShell
import-module networkcontroller
$ncURI = "https://mync.contoso.local"
$aclrules = @()

$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Allow"
$ruleproperties.SourceAddressPrefix = "192.168.0.1"
$ruleproperties.DestinationAddressPrefix = "*"
$ruleproperties.Priority = "100"
$ruleproperties.Type = "Inbound"
$ruleproperties.Logging = "Enabled"

$aclrule = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule.Properties = $ruleproperties
$aclrule.ResourceId = "AllowRouter_Inbound"
$aclrules += $aclrule

$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Allow"
$ruleproperties.SourceAddressPrefix = "*"
$ruleproperties.DestinationAddressPrefix = "192.168.0.1"
$ruleproperties.Priority = "101"
$ruleproperties.Type = "Outbound"
$ruleproperties.Logging = "Enabled"

$aclrule = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule.Properties = $ruleproperties
$aclrule.ResourceId = "AllowRouter_Outbound"
$aclrules += $aclrule

$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Deny"
$ruleproperties.SourceAddressPrefix = "192.168.0.0/24"
$ruleproperties.DestinationAddressPrefix = "*"
$ruleproperties.Priority = "102"
$ruleproperties.Type = "Inbound"
$ruleproperties.Logging = "Enabled"

$aclrule = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule.Properties = $ruleproperties
$aclrule.ResourceId = "DenySubnet_Inbound"
$aclrules += $aclrule

$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Deny"
$ruleproperties.SourceAddressPrefix = "*"
$ruleproperties.DestinationAddressPrefix = "192.168.0.0/24"
$ruleproperties.Priority = "103"
$ruleproperties.Type = "Outbound"
$ruleproperties.Logging = "Enabled"

$aclrule = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule.Properties = $ruleproperties
$aclrule.ResourceId = "DenySubnet_Outbound"

$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Allow"
$ruleproperties.SourceAddressPrefix = "*"
$ruleproperties.DestinationAddressPrefix = "*"
$ruleproperties.Priority = "104"
$ruleproperties.Type = "Inbound"
$ruleproperties.Logging = "Enabled"

$aclrule = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule.Properties = $ruleproperties
$aclrule.ResourceId = "AllowAll_Inbound"
$aclrules += $aclrule

$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Allow"
$ruleproperties.SourceAddressPrefix = "*"
$ruleproperties.DestinationAddressPrefix = "*"
$ruleproperties.Priority = "105"
$ruleproperties.Type = "Outbound"
$ruleproperties.Logging = "Enabled"

$aclrule = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule.Properties = $ruleproperties
$aclrule.ResourceId = "AllowAll_Outbound"
$aclrules += $aclrule

$acllistproperties = new-object Microsoft.Windows.NetworkController.AccessControlListProperties
$acllistproperties.AclRules = $aclrules

New-NetworkControllerAccessControlList -ResourceId "Subnet-192-168-0-0" -Properties $acllistproperties -ConnectionUri $ncURI
```

## <a name="add-an-acl-to-a-network-interface"></a>Hinzufügen einer ACL zu einer Netzwerkschnittstelle

Nachdem Sie eine ACL erstellt und einem virtuellen Subnetz zugewiesen haben, können Sie diese Standard-ACL im virtuellen Subnetz mit einer speziellen ACL für eine einzelne Netzwerkschnittstelle außer Kraft setzen. In diesem Fall wenden Sie spezielle ACLs direkt auf Netzwerkschnittstellen an, die nicht mit dem virtuellen Netzwerk, sondern mit VLANs verbunden sind. Wenn Sie ACLs für das virtuelle Subnetz festgelegt haben, das mit der Netzwerkschnittstelle verbunden ist, werden beide ACLs angewendet, wobei die ACLs der Netzwerkschnittstelle Vorrang vor den ACLs des virtuellen Subnetzes haben.

In diesem Beispiel wird gezeigt, wie Sie eine ACL zu einem virtuellen Netzwerk hinzufügen.

>[!TIP]
>Sie können eine ACL auch hinzuzufügen, wenn Sie die Netzwerkschnittstelle erstellen.

1. Rufen Sie die Netzwerkschnittstelle ab, der Sie die ACL hinzufügen möchten, oder erstellen Sie diese Netzwerkschnittstelle.

   ```PowerShell
   $nic = get-networkcontrollernetworkinterface -ConnectionUri $uri -ResourceId "MyVM_Ethernet1"
   ```

2. Rufen Sie die ACL ab, die Sie der Netzwerkschnittstelle hinzufügen möchten, oder erstellen Sie diese ACL.

   ```PowerShell
   $acl = get-networkcontrolleraccesscontrollist -ConnectionUri $uri -ResourceId "AllowAllACL"
   ```

3. Weisen Sie die ACL der „AccessControlList“-Eigenschaft der Netzwerkschnittstelle zu.

   ```PowerShell
    $nic.properties.ipconfigurations[0].properties.AccessControlList = $acl
   ```

4. Fügen Sie die Netzwerkschnittstelle im Netzwerkcontroller hinzu.

   ```PowerShell
   new-networkcontrollernetworkinterface -ConnectionUri $uri -Properties $nic.properties -ResourceId $nic.resourceid
   ```

## <a name="remove-an-acl-from-a-network-interface"></a>Entfernen einer ACL aus einer Netzwerkschnittstelle

In diesem Beispiel wird gezeigt, wie Sie eine ACL aus einer Netzwerkschnittstelle entfernen. Nach dem Entfernen einer ACL wird der Standardsatz von Regeln auf die Netzwerkschnittstelle angewendet. Der Standardsatz von Regeln lässt jeglichen ausgehenden Datenverkehr zu, blockiert jedoch jeglichen eingehenden Datenverkehr. Wenn Sie jeglichen eingehenden Datenverkehr zulassen möchten, müssen Sie gemäß dem vorherigen [Beispiel](#add-an-acl-to-a-network-interface) vorgehen, um eine ACL hinzuzufügen, die jeglichen eingehenden und ausgehenden Datenverkehr zulässt.

1. Rufen Sie die Netzwerkschnittstelle ab, für die Sie die ACL entfernen möchten.
   ```PowerShell
   $nic = get-networkcontrollernetworkinterface -ConnectionUri $uri -ResourceId "MyVM_Ethernet1"
   ```

2. Weisen Sie der IP-Konfigurationseigenschaft „AccessControlList“ den Wert „$null“ zu.
   ```PowerShell
   $nic.properties.ipconfigurations[0].properties.AccessControlList = $null
   ```

3. Fügen Sie das Netzwerkschnittstellenobjekt im Netzwerkcontroller hinzu.
   ```PowerShell
   new-networkcontrollernetworkinterface -ConnectionUri $uri -Properties $nic.properties -ResourceId $nic.resourceid
   ```

## <a name="firewall-auditing"></a>Firewallüberwachung

Firewallüberwachung ist eine neue Funktion für die Rechenzentrumsfirewall, die jeglichen Datenverkehr aufzeichnet, der mit den SDN-Firewallregeln verarbeitet wurde. Alle ACLs, für die Protokollierung aktiviert ist, werden aufgezeichnet. Die Protokolldateien müssen in einer Syntax vorliegen, die mit den [Azure Network Watcher-Datenflussprotokollen](/azure/network-watcher/network-watcher-nsg-flow-logging-overview) übereinstimmt. Diese Protokolle können für Diagnosen verwendet oder zur späteren Analyse archiviert werden.

Das folgende Skript ist ein Beispielskript, mit dem Firewallüberwachung auf den Hostservern aktiviert wird. Aktualisieren Sie die am Anfang aufgeführten Variablen, und führen Sie diese Skript für einen Azure Stack HCI-Cluster aus, für den [Netzwerkcontroller](../concepts/network-controller-overview.md) bereitgestellt ist:

```PowerShell
$logpath = "C:\test\log1"
$servers = @("sa18n22-2", "sa18n22-3", "sa18n22-4")
$uri = "https://sa18n22sdn.sa18.nttest.microsoft.com"

# Create log directories on the hosts
invoke-command -Computername $servers  {
    param(
        $Path
    )
    mkdir $path    -force
} -argumentlist $LogPath

# Set firewall auditing settings on Network Controller
$AuditProperties = new-object Microsoft.Windows.NetworkController.AuditingSettingsProperties
$AuditProperties.OutputDirectory = $logpath
set-networkcontrollerauditingsettingsconfiguration -connectionuri $uri -properties $AuditProperties -force  | out-null

# Enable logging on each server
$servers = get-networkcontrollerserver -connectionuri $uri
foreach ($s in $servers) {
    $s.properties.AuditingEnabled = @("Firewall")
    new-networkcontrollerserver -connectionuri $uri -resourceid $s.resourceid -properties $s.properties -force | out-null
}
```

Nach der Aktivierung wird im angegebenen Verzeichnis auf jedem Host etwa einmal pro Stunde eine neue Datei gespeichert.  Sie sollten diese Dateien regelmäßig verarbeiten und von den Hosts entfernen.  Die aktuelle Datei hat eine Länge von null und ist gesperrt, bis sie bei der nächsten Stundenmarke gefüllt wurde:

```syntax
PS C:\test\log1> dir

    Directory: C:\test\log1

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        7/19/2018   6:28 AM          17055 SdnFirewallAuditing.d8b3b697-5355-40e2-84d2-1bf2f0e0dc4a.20180719TL122803093.json
-a----        7/19/2018   7:28 AM           7880 SdnFirewallAuditing.d8b3b697-5355-40e2-84d2-1bf2f0e0dc4a.20180719TL132803173.json
-a----        7/19/2018   8:28 AM           7867 SdnFirewallAuditing.d8b3b697-5355-40e2-84d2-1bf2f0e0dc4a.20180719TL142803264.json
-a----        7/19/2018   9:28 AM          10949 SdnFirewallAuditing.d8b3b697-5355-40e2-84d2-1bf2f0e0dc4a.20180719TL152803360.json
-a----        7/19/2018   9:28 AM              0 SdnFirewallAuditing.d8b3b697-5355-40e2-84d2-1bf2f0e0dc4a.20180719TL162803464.json
```

Diese Dateien enthalten eine Sequenz von Datenflussereignissen. Beispiel:

```syntax
{
    "records": [
        {
            "properties":{
                "Version":"1.0",
                "flows":[
                    {
                        "flows":[
                            {
                                "flowTuples":["1531963580,192.122.0.22,192.122.255.255,138,138,U,I,A"],
                                "portId":"9",
                                "portName":"7290436D-0422-498A-8EB8-C6CF5115DACE"
                            }
                        ],
                        "rule":"Allow_Inbound"
                    }
                ]
            },
            "operationName":"NetworkSecurityGroupFlowEvents",
            "resourceId":"394f647d-2ed0-4c31-87c5-389b8c0c8132",
            "time":"20180719:L012620622",
            "category":"NetworkSecurityGroupFlowEvent",
            "systemId":"d8b3b697-5355-40e2-84d2-1bf2f0e0dc4a"
            },
```

Protokollierung erfolgt nur für Regeln, für die **Logging** auf **Enabled** festgelegt ist. Beispiel:

```syntax
{
    "Tags":  null,
    "ResourceRef":  "/accessControlLists/AllowAll",
    "InstanceId":  "4a63e1a5-3264-4986-9a59-4e77a8b107fa",
    "Etag":  "W/\"1535a780-0fc8-4bba-a15a-093ecac9b88b\"",
    "ResourceMetadata":  null,
    "ResourceId":  "AllowAll",
    "Properties":  {
                       "ConfigurationState":  null,
                       "ProvisioningState":  "Succeeded",
                       "AclRules":  [
                                        {
                                            "ResourceMetadata":  null,
                                            "ResourceRef":  "/accessControlLists/AllowAll/aclRules/AllowAll_Inbound",
                                            "InstanceId":  "ba8710a8-0f01-422b-9038-d1f2390645d7",
                                            "Etag":  "W/\"1535a780-0fc8-4bba-a15a-093ecac9b88b\"",
                                            "ResourceId":  "AllowAll_Inbound",
                                            "Properties":  {
                                                               "Protocol":  "All",
                                                               "SourcePortRange":  "0-65535",
                                                               "DestinationPortRange":  "0-65535",
                                                               "Action":  "Allow",
                                                               "SourceAddressPrefix":  "*",
                                                               "DestinationAddressPrefix":  "*",
                                                               "Priority":  "101",
                                                               "Description":  null,
                                                               "Type":  "Inbound",
                                                               "Logging":  "Enabled",
                                                               "ProvisioningState":  "Succeeded"
                                                           }
                                        },
                                        {
                                            "ResourceMetadata":  null,
                                            "ResourceRef":  "/accessControlLists/AllowAll/aclRules/AllowAll_Outbound",
                                            "InstanceId":  "068264c6-2186-4dbc-bbe7-f504c6f47fa8",
                                            "Etag":  "W/\"1535a780-0fc8-4bba-a15a-093ecac9b88b\"",
                                            "ResourceId":  "AllowAll_Outbound",
                                            "Properties":  {
                                                               "Protocol":  "All",
                                                               "SourcePortRange":  "0-65535",
                                                               "DestinationPortRange":  "0-65535",
                                                               "Action":  "Allow",
                                                               "SourceAddressPrefix":  "*",
                                                               "DestinationAddressPrefix":  "*",
                                                               "Priority":  "110",
                                                               "Description":  null,
                                                               "Type":  "Outbound",
                                                               "Logging":  "Enabled",
                                                               "ProvisioningState":  "Succeeded"
                                                           }
                                        }
                                    ],
                       "IpConfigurations":  [

                                            ],
                       "Subnets":  [
                                       {
                                           "ResourceMetadata":  null,
                                           "ResourceRef":  "/virtualNetworks/10_0_1_0/subnets/Subnet1",
                                           "InstanceId":  "00000000-0000-0000-0000-000000000000",
                                           "Etag":  null,
                                           "ResourceId":  null,
                                           "Properties":  null
                                       }
                                   ]
                   }
}
```

## <a name="next-steps"></a>Nächste Schritte

Verwandte Informationen finden Sie außerdem unter:

- [Übersicht über die Rechenzentrumsfirewall](../concepts/datacenter-firewall-overview.md)
- [Netzwerkcontroller – Übersicht](../concepts/network-controller-overview.md)
- [SDN in Azure Stack HCI](../concepts/software-defined-networking.md)