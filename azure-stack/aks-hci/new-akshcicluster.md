---
title: New-AksHciCluster
author: jessicaguan
description: Der PowerShell-Befehl New-AksHciCluster erstellt einen neuen Managed Kubernetes-Cluster.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: d4b374ca093c60018d1dc68c3d5a39db68456298
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "101874452"
---
# <a name="new-akshcicluster"></a>New-AksHciCluster

## <a name="synopsis"></a>Übersicht
Erstellen eines neuen Managed Kubernetes-Clusters.

## <a name="syntax"></a>Syntax

```powershell
New-AksHciCluster -name <String>
                 [-kubernetesVersion <String>]
                 [-controlPlaneNodeCount <int>]
                 [-linuxNodeCount <int>]
                 [-windowsNodeCount <int>]
                 [-controlPlaneVmSize <VmSize>]
                 [-loadBalancerVmSize <VmSize>]
                 [-linuxNodeVmSize <VmSize>]
                 [-windowsNodeVmSize <VmSize>]
                 [-vnet <Virtual Network>]
                 [-primaryNetworkPlugin <Network Plugin>]   
                 [-enableAdAuth]
                 [-enableSecretsEncryption]          
```

## <a name="description"></a>BESCHREIBUNG

Erstellen eines neuen Azure Kubernetes Service auf Azure Stack HCI-Clusters.

## <a name="examples"></a>Beispiele

### <a name="new-aks-hci-cluster-with-required-params"></a>Neuer AKS-HCI-Cluster mit erforderlichen Parametern.

```powershell
PS C:\> New-AksHciCluster -name myCluster
```

## <a name="parameters"></a>Parameter

### <a name="-name"></a>-name
Der Name Ihres Kubernetes-Clusters.

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

### <a name="-kubernetesversion"></a>-kubernetesVersion
Die Version von Kubernetes, die Sie bereitstellen möchten. Der Standardwert ist die neueste Version. Zum Abrufen einer Liste der verfügbaren Versionen führen Sie `Get-AksHciKubernetesVersion` aus.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: v1.18.8 or later
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-controlplanenodecount"></a>-controlPlaneNodeCount
Die Anzahl der Knoten in Ihrer Steuerungsebene. Der Standardwert ist 1.

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: 1
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-linuxnodecount"></a>-linuxNodeCount
Die Anzahl der Linux-Knoten in Ihrem Kubernetes-Cluster. Der Standardwert ist 1.

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases: 

Required: False
Position: Named
Default value: 1
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-windowsnodecount"></a>-windowsNodeCount
Die Anzahl der Windows-Knoten in Ihrem Kubernetes-Cluster. Standard ist "0". Wenn Sie Kubernetes v1.18.8 oder höher ausführen, können Sie nur Windows-Knoten bereitstellen.

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: 0
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-controlplanevmsize"></a>-controlPlaneVmSize
Die Größe der VM Ihrer Steuerungsebene. Der Standard ist „Standard_A4_V2“. Führen Sie `Get-AksHciVmSize` aus, um eine Liste mit den verfügbaren VM-Größen abzurufen.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: Standard_A4_V2
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-loadbalancervmsize"></a>-loadBalancerVmSize
Die Größe Ihrer Lastenausgleichs-VM. Der Standard ist „Standard_A4_V2“. Führen Sie `Get-AksHciVmSize` aus, um eine Liste mit den verfügbaren VM-Größen abzurufen.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: Standard_A4_V2
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-linuxnodevmsize"></a>-linuxNodeVmSize
Die Größe Ihrer Linux-Knoten-VM. Der Standard ist „Standard_K8S3_v1“. Führen Sie `Get-AksHciVmSize` aus, um eine Liste mit den verfügbaren VM-Größen abzurufen.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: Standard_K8S3_v1
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-windowsvmsize"></a>-windowsVmSize
Die Größe Ihrer Windows-Knoten-VM. Der Standard ist „Standard_K8S3_v1“. Führen Sie `Get-AksHciVmSize` aus, um eine Liste mit den verfügbaren VM-Größen abzurufen.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: Standard_K8S3_v1
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-vnet"></a>-vnet
Der Name des AksHciNetworkSetting-Objekts, das mit dem Befehl New-AksHciNetworkSetting erstellt wurde.

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

### <a name="-primarynetworkplugin"></a>-primaryNetworkPlugin
Das für die Bereitstellung zu verwendende Netzwerk-Plug-In. Dieser Parameter nimmt `flannel` oder `calico` an. Calico ist nur für Nur-Linux-Cluster verfügbar. Wenn Sie Calico für Ihren Cluster auswählen, ist das Hinzufügen von Windows-Knoten unzulässig.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: flannel
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-enableadauth"></a>-enableADAuth
Verwenden Sie dieses Flag, um Active Directory in Ihrem Kubernetes-Cluster zu aktivieren.

```yaml
Type: System.Management.Automation.SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-enablesecretsencryption"></a>-enableSecretsEncryption
Verwenden Sie dieses Flag, um Verschlüsselung in Ihren Kubernetes-Geheimnissen zu aktivieren.

```yaml
Type: System.Management.Automation.SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
