---
title: Restart-AksHci
author: jessicaguan
description: Der PowerShell-Befehl Restart-AksHci startet AKS auf Azure Stack HCI neu und entfernt alle bereitgestellten Kubernetes-Cluster.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 358ca36b085b21c6ecdbea7a024d9e72e0ca0597
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "101874479"
---
# <a name="restart-akshci"></a>Restart-AksHci

## <a name="synopsis"></a>Übersicht
Neustarten von Azure Kubernetes Service auf Azure Stack HCI und Entfernen aller bereitgestellten Kubernetes-Cluster.

## <a name="syntax"></a>Syntax

### <a name="restart-aks-hci"></a>Neustarten von AKS-HCI
```powershell
Restart-AksHci
```

## <a name="description"></a>BESCHREIBUNG
Beim Neustarten von Azure Kubernetes Service auf Azure Stack HCI werden Ihre gesamten Kubernetes-Cluster (falls vorhanden) und der Azure Kubernetes Service-Host entfernt. Darüber hinaus werden auf den Knoten auch die Agents und Dienste von Azure Kubernetes Service unter Azure Stack HCI deinstalliert. Anschließend werden die ursprünglichen Schritte des Installationsvorgangs erneut ausgeführt, bis der Host neu erstellt wurde. Die Konfiguration für Azure Kubernetes Service auf Azure Stack HCI, die Sie mit Set-AksHciConfig vorgenommen haben, und die heruntergeladenen VHDX-Images werden beibehalten.

## <a name="examples"></a>Beispiele

### <a name="example"></a>Beispiel
```powershell
PS C:\> Restart-AksHci
```
