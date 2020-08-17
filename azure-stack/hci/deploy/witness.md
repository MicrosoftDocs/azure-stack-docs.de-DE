---
title: Einrichten eines Clusterzeugen
description: Erfahren Sie, wie Sie einen Clusterzeugen einrichten
author: v-dasis
ms.topic: how-to
ms.date: 08/11/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 43d63e4e565a36c30837e2a8b460e0d73a35c0a5
ms.sourcegitcommit: 7d518629bd55f24e7459404bb19b7db8a54f4b94
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/12/2020
ms.locfileid: "88145533"
---
# <a name="set-up-a-cluster-witness"></a>Einrichten eines Clusterzeugen

> Gilt für Azure Stack HCI, Version 20H2; Windows Server 2019

Das Einrichten einer Zeugenressource ist für alle Cluster obligatorisch und sollte unmittelbar nach dem Erstellen eines Clusters erfolgen. Für Cluster mit zwei Knoten wird ein Zeuge benötigt, damit beim Versetzen eines Servers in den Offlinezustand die Verfügbarkeit des anderen Knotens nicht gefährdet ist. Cluster mit drei oder mehr Knoten müssen einen Zeugen aufweisen, um eine Situation zu überstehen, in der zwei Server ausfallen oder in den Offlinezustand versetzt werden.  

Sie können entweder eine SMB-Dateifreigabe als Zeugen oder einen Azure-Cloudzeugen verwenden. Ein Azure-Cloudzeuge wird empfohlen, vorausgesetzt, alle Serverknoten im Cluster verfügen über eine zuverlässige Internetverbindung. Weitere Informationen finden Sie unter [Deploy a cloud witness for a Failover Cluster](/windows-server/failover-clustering/deploy-cloud-witness) (Bereitstellen eines Cloudzeugen für einen Failovercluster).

Bei Dateifreigabezeugen bestehen Anforderungen an den Dateiserver. Weitere Informationen finden Sie unter [Vor dem Bereitstellen von Azure Stack HCI](before-you-start.md).

## <a name="set-up-a-witness-using-windows-admin-center"></a>Einrichten eines Zeugen mithilfe von Windows Admin Center

1. Wählen Sie in der oberen Dropdownliste in Windows Admin Center die Option **Cluster-Manager** aus.
1. Wählen Sie unter **Clusterverbindungen** den Cluster aus.
1. Wählen Sie unter **Extras** die Option **Einstellungen** aus.
1. Wählen Sie im rechten Bereich **Zeuge** aus.
1. Wählen Sie als **Zeugentyp** einen der folgenden Werte aus:
      - **Cloudzeuge**: Geben Sie den Namen Ihres Azure-Speicherkontos, den Zugriffsschlüssel und die Endpunkt-URL ein, wie weiter unten beschrieben.
      - **Dateifreigabezeuge**: Geben Sie den Pfad der Dateifreigabe ein (//server/share).

> [!NOTE]
> Die dritte Option, **Datenträgerzeuge**, eignet sich nicht für die Verwendung in Stretchingclustern.

## <a name="create-an-azure-storage-account-to-use-as-a-cloud-witness"></a>Erstellen eines Azure-Speicherkontos für die Verwendung als Cloudzeuge

In diesem Abschnitt erfahren Sie, wie Sie ein Speicherkonto erstellen und Endpunkt-URLs und Zugriffsschlüssel für dieses Konto anzeigen und kopieren.

Zum Konfigurieren des Cloudzeugen müssen Sie über ein gültiges Azure-Speicherkonto zum Speichern der (für die Vermittlung verwendeten) Blobdatei verfügen. Der Cloudzeuge erstellt einen bekannten Container (**msft-cloud-witness**) unter dem Microsoft-Speicherkonto. Der Cloudzeuge schreibt eine einzelne Blobdatei und verwendet dabei die eindeutige ID des entsprechenden Clusters als Dateiname der Blobdatei im Container **msft-cloud-witness**. Ein Cloudzeuge kann also mit dem gleichen Microsoft Azure-Speicherkonto für mehrere verschiedene Cluster konfiguriert werden.

Wenn Sie das gleiche Azure-Speicherkonto verwenden, um einen Cloudzeugen für mehrere verschiedene Cluster zu konfigurieren, wird automatisch ein einzelner Container namens **msft-cloud-witness** erstellt. Dieser Container enthält jeweils eine einzelne Blobdatei pro Cluster.

> [!NOTE]  
> Der Cloudzeuge verwendet HTTPS (Standardport 443) für die Kommunikation mit dem Azure-Blobdienst. Stellen Sie sicher, dass per Netzwerkproxy auf den HTTPS-Port zugegriffen werden kann.

### <a name="to-create-an-azure-storage-account"></a>Erstellen eines Azure-Speicherkontos

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im Hubmenu Neu -> Daten und Speicher -> Speicherkonto.
1. Gehen Sie auf der Seite „Speicherkonto erstellen“ wie folgt vor:
    1. Geben Sie einen Namen für Ihr Speicherkonto ein.
    <br>Speicherkontonamen müssen zwischen 3 und 24 Zeichen lang sein und dürfen nur Zahlen und Kleinbuchstaben enthalten. Außerdem muss der Name des Speicherkontos innerhalb von Azure eindeutig sein.
    1. Wählen Sie unter **Kontoart** die Option **Universell** aus.
    <br>Für einen Cloudzeugen kann kein Blobspeicherkonto verwendet werden.
    1. Wählen Sie für **Leistung** die Option **Standard** aus.
    <br>Azure Storage Premium kann für einen Cloudzeugen nicht verwendet werden.
    1. Wählen Sie unter **Replikation** die Option **Lokal redundanter Speicher (LRS)** aus.
    <br>Beim Failoverclustering wird die Blobdatei als Vermittlungspunkt verwendet. Dies erfordert einige Konsistenzgarantien beim Lesen von Daten. Wählen Sie daher unter **Replikation** die Option **Lokal redundanter Speicher (LRS)** aus.

### <a name="view-and-copy-storage-access-keys-for-your-azure-storage-account"></a>Anzeigen und Kopieren von Speicherzugriffsschlüsseln für Ihr Azure-Speicherkonto

Wenn Sie ein Microsoft Azure-Speicherkonto erstellen, werden ihm zwei automatisch generierte Zugriffsschlüssel zugeordnet: ein primärer und ein sekundärer Zugriffsschlüssel. Verwenden Sie bei erstmaliger Erstellung eines Cloudzeugen den **primären Zugriffsschlüssel**. Sie können frei zwischen diesen beiden Schlüsseln wählen.  

#### <a name="to-view-and-copy-storage-access-keys"></a>Anzeigen und Kopieren von Speicherzugriffsschlüsseln

Navigieren Sie im Azure-Portal zu Ihrem Speicherkonto, und klicken Sie auf **Alle Einstellungen** und anschließend auf **Zugriffsschlüssel**, um Ihre Kontozugriffsschlüssel anzuzeigen, zu kopieren und erneut zu generieren. Das Blatt Zugriffsschlüssel enthält auch vorkonfigurierte Verbindungszeichenfolgen, in denen Ihre primären und sekundären Schlüssel verwendet werden, die Sie zur Nutzung in der Anwendung kopieren können.

:::image type="content" source="media/witness/cloud-witness-1.png" alt-text="Cloudzeuge: Zugriffsschlüssel" lightbox="media/witness/cloud-witness-1.png":::

### <a name="view-and-copy-endpoint-url-links"></a>Anzeigen und Kopieren von Endpunkt-URL-Links

Wenn Sie ein Speicherkonto erstellen, werden URLs im folgenden Format generiert: `https://<Storage Account Name>.<Storage Type>.<Endpoint>`  

Von einem Cloudzeugen wird immer **Blob** als Speichertyp verwendet. Von Azure wird **.core.windows.net** als Endpunkt verwendet. Der Cloudzeuge kann im Rahmen der Konfiguration mit einem anderen Endpunkt konfiguriert werden, falls dies für Ihr Szenario erforderlich ist. So hat beispielsweise das Microsoft Azure-Rechenzentrum in China einen anderen Endpunkt.  

> [!NOTE]  
> Die Endpunkt-URL wird automatisch von der Cloudzeugenressource generiert, und für die URL ist kein zusätzlicher Konfigurationsschritt erforderlich.  

#### <a name="to-view-and-copy-endpoint-url-links"></a>Anzeigen und Kopieren von Endpunkt-URL-Links

Navigieren Sie im Azure-Portal zu Ihrem Speicherkonto, und klicken Sie auf **Alle Einstellungen** und anschließend auf **Eigenschaften**, um Ihre Endpunkt-URLs anzuzeigen und zu kopieren.  

:::image type="content" source="media/witness/cloud-witness-2.png" alt-text="Cloudzeuge: Endpunkt-URL" lightbox="media/witness/cloud-witness-2.png":::  

## <a name="set-up-a-witness-using-windows-powershell"></a>Einrichten eines Zeugen mithilfe von Windows PowerShell

Um einen Clusterzeugen mithilfe von PowerShell einzurichten, führen Sie eins der folgenden Cmdlets aus.

Verwenden Sie das folgende Cmdlet, um einen Azure-Cloudzeugen zu erstellen:

```powershell
Set-ClusterQuorum –Cluster "Cluster1" -CloudWitness -AccountName "AzureStorageAccountName" -AccessKey "AzureStorageAccountAccessKey"
```

Verwenden Sie das folgende Cmdlet, um einen Dateifreigabezeugen zu erstellen:

```powershell
Set-ClusterQuorum -FileShareWitness "\\fileserver\share" -Credential (Get-Credential)
```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Clusterquorum finden Sie unter [Grundlegendes zum Cluster- und Poolquorum in Azure Stack HCI](../concepts/quorum.md).

- Weitere Informationen zum Erstellen und Verwalten von Azure-Speicherkonten finden Sie unter [Erstellen eines Azure-Speicherkontos](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/).
