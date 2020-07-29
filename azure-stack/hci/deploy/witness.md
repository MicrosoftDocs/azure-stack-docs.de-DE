---
title: Einrichten eines Clusterzeugen
description: Erfahren Sie, wie Sie einen Clusterzeugen einrichten
author: v-dasis
ms.topic: how-to
ms.date: 07/21/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: e660d1cc10e5d4b4e3d1ffbebeee99e421cd6dbb
ms.sourcegitcommit: a15a0f955bac922cebb7bf90a72384fd84ddfe56
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2020
ms.locfileid: "86947468"
---
# <a name="set-up-a-cluster-witness"></a>Einrichten eines Clusterzeugen

> Gilt für Azure Stack HCI, Version 20H2; Windows Server 2019

Das Einrichten einer Zeugenressource ist für alle Cluster obligatorisch und sollte unmittelbar nach dem Erstellen eines Clusters erfolgen. Für Cluster mit zwei Knoten wird ein Zeuge benötigt, damit beim Versetzen eines Servers in den Offlinezustand die Verfügbarkeit des anderen Knotens nicht gefährdet ist. Cluster mit drei oder mehr Knoten müssen einen Zeugen aufweisen, um eine Situation zu überstehen, in der zwei Server ausfallen oder in den Offlinezustand versetzt werden.  

Sie können entweder eine Dateifreigabe oder einen Azure-Cloudzeugen als Zeugen verwenden. Ein Azure-Cloudzeuge wird empfohlen, vorausgesetzt, alle Serverknoten im Cluster verfügen über eine zuverlässige Internetverbindung. Weitere Informationen finden Sie unter [Deploy a cloud witness for a Failover Cluster](/windows-server/failover-clustering/deploy-cloud-witness) (Bereitstellen eines Cloudzeugen für einen Failovercluster).

Bei Dateifreigabezeugen bestehen Anforderungen an den Dateiserver. Weitere Informationen finden Sie unter [Vor dem Bereitstellen von Azure Stack HCI](before-you-start.md).

## <a name="set-up-a-witness-using-windows-admin-center"></a>Einrichten eines Zeugen mithilfe von Windows Admin Center

1. Wählen Sie in der oberen Dropdownliste in Windows Admin Center die Option **Cluster-Manager** aus.
1. Wählen Sie unter **Clusterverbindungen** den Cluster aus.
1. Wählen Sie unter **Extras** die Option **Einstellungen** aus.
1. Wählen Sie im rechten Bereich **Zeuge** aus.
1. Wählen Sie als **Zeugentyp** einen der folgenden Werte aus:
      - **Cloudzeuge**: Geben Sie den Namen, Schlüssel und Endpunkt Ihres Azure Storage-Kontos ein
      - **Dateifreigabezeuge**: Geben Sie den Pfad der Dateifreigabe ein (//server/share)

> [!NOTE]
> Die dritte Option, **Datenträgerzeuge**, eignet sich nicht für die Verwendung in Stretchingclustern.

## <a name="set-up-a-witness-using-windows-powershell"></a>Einrichten eines Zeugen mithilfe von Windows PowerShell

Um einen Clusterzeugen mithilfe von PowerShell einzurichten, führen Sie eins der folgenden Cmdlets aus.

Verwenden Sie das folgende Cmdlet, um einen Azure-Cloudzeugen zu erstellen:

```powershell
Set-ClusterQuorum –Cluster Cluster1 -CloudWitness -AccountName <AzureStorageAccountName> -AccessKey <AzureStorageAccountAccessKey>
```

Verwenden Sie das folgende Cmdlet, um einen Dateifreigabezeugen zu erstellen:

```powershell
Set-ClusterQuorum –Cluster Cluster1 -FileShareWitness \\fileserver\fsw
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Clusterquorum finden Sie unter [Grundlegendes zum Cluster- und Poolquorum in Azure Stack HCI](../concepts/quorum.md).
