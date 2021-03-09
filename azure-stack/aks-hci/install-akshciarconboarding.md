---
title: Install-AksHciArcOnboarding
author: jessicaguan
description: Der PowerShell-Befehl Install-AksHciArcOnboarding verbindet einen AKS auf Azure Stack HCI-Workloadcluster mit Azure Arc für Kubernetes.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: a366e553ac76882a7f45a9f25424cabbb4402319
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "101874454"
---
# <a name="install-akshciarconboarding"></a>Install-AksHciArcOnboarding

## <a name="synopsis"></a>Übersicht
Verbindet einen AKS auf Azure Stack HCI-Workloadcluster mit Azure Arc für Kubernetes.

## <a name="syntax"></a>Syntax

```powershell
Install-AksHciArcOnboarding -Name <String> 
                            -tenantId <String>
                            -subscriptionId <String> 
                            -resourceGroup <String>
                            -clientId <String>
                            -clientSecret <String>
                            [-location <String>]
```

## <a name="description"></a>BESCHREIBUNG
Verbindet einen AKS auf Azure Stack HCI-Workloadcluster mit Azure Arc für Kubernetes.

## <a name="examples"></a>Beispiele

### <a name="connect-an-aks-on-azure-stack-hci-cluster-to-azure-arc-for-kubernetes-with-required-parameters"></a>Verbinden eines AKS auf Azure Stack HCI-Clusters mit Azure Arc für Kubernetes mit erforderlichen Parametern

```PowerShell
Install-AksHciArcOnboarding -name "myCluster" -resourcegroup "myResourceGroup" -subscriptionid "57ac26cf-a9f0-4908-b300-9a4e9a0fb205"  -clientid "22cc2695-54b9-49c1-9a73-2269592103d8" -clientsecret "09d3a928-b223-4dfe-80e8-fed13baa3b3d" -tenantid "72f988bf-86f1-41af-91ab-2d7cd011db47"
```

### <a name="connect-an-aks-on-azure-stack-hci-cluster-to-azure-arc-for-kubernetes-with-all-parameters"></a>Verbinden eines AKS auf Azure Stack HCI-Clusters mit Azure Arc für Kubernetes mit allen Parametern

```PowerShell
Install-AksHciArcOnboarding -name "myCluster" -resourcegroup "myResourceGroup" -location "eastus" -subscriptionid "57ac26cf-a9f0-4908-b300-9a4e9a0fb205"  -clientid "22cc2695-54b9-49c1-9a73-2269592103d8" -clientsecret "09d3a928-b223-4dfe-80e8-fed13baa3b3d" -tenantid "72f988bf-86f1-41af-91ab-2d7cd011db47"
```

## <a name="parameters"></a>Parameter

### <a name="-name"></a>-Name
Der alphanumerische Name Ihres Kubernetes-Clusters.

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

### <a name="-tenantid"></a>-tenantId
Die Mandanten-ID Ihres Azure-Dienstprinzipals.

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

### <a name="-subscriptionid"></a>-subscriptionId
Die Abonnement-ID Ihres Azure-Kontos.

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

### <a name="-resourcegroup"></a>-resourceGroup
Der Name der Azure-Ressourcengruppe.

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

### <a name="-clientid"></a>-clientId
Die Client-ID oder App-ID Ihres Azure-Dienstprinzipals.

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

### <a name="-clientsecret"></a>-clientSecret
Das Clientgeheimnis oder Kennwort Ihres Azure-Dienstprinzipals.

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

### <a name="-location"></a>-location
Der Speicherort oder die Azure-Region Ihrer Azure-Ressource. Der Standardwert ist der Speicherort Ihrer Azure-Ressourcengruppe. Der Speicherort kann nur „eastus“ oder „westeurope“ für Azure Arc für Kubernetes sein.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: Azure resource group's location
Accept pipeline input: False
Accept wildcard characters: False
```
