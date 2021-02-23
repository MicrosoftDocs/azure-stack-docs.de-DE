---
title: Einrichten eines Clusterzeugen
description: Erfahren Sie, wie Sie einen Clusterzeugen einrichten
author: v-dasis
ms.topic: how-to
ms.date: 02/17/2021
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 32d0f717e987d757f5315cfe048c75300ae9c776
ms.sourcegitcommit: 4c97ed2caf054ebeefa94da1f07cfb6be5929aac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100647904"
---
# <a name="set-up-a-cluster-witness"></a>Einrichten eines Clusterzeugen

> Gilt für Azure Stack HCI, Version 20H2; Windows Server 2019

Es wird empfohlen, direkt nach der Clustererstellung für alle Cluster eine Zeugenressource einzurichten. Für Cluster mit zwei Knoten wird ein Zeuge benötigt, damit beim Versetzen eines Servers in den Offlinezustand die Verfügbarkeit des anderen Knotens nicht gefährdet ist. Cluster mit drei oder mehr Knoten müssen einen Zeugen aufweisen, um eine Situation zu überstehen, in der zwei Server ausfallen oder in den Offlinezustand versetzt werden.  

Sie können entweder eine SMB-Dateifreigabe als Zeugen oder einen Azure-Cloudzeugen verwenden. Ein Azure-Cloudzeuge wird empfohlen, vorausgesetzt, alle Serverknoten im Cluster verfügen über eine zuverlässige Internetverbindung. In diesem Artikel wird das Erstellen von Cloudzeugen behandelt.

## <a name="before-you-begin"></a>Vorbereitung

Damit Sie einen Cloudzeugen erstellen können, benötigen Sie ein Azure-Konto und -Abonnement und müssen Ihren Azure Stack HCI-Cluster bei Azure registrieren. Weitere Informationen finden Sie in den folgenden Artikeln:

- [Erstellen eines Azure-Kontos](https://docs.microsoft.com/dotnet/azure/create-azure-account)
- Falls erforderlich: [Erstellen eines zusätzlichen Azure-Abonnements](https://docs.microsoft.com/azure/cost-management-billing/manage/create-subscription)
- [Herstellen einer Verbindung von Azure Stack HCI mit Azure](../deploy/register-with-azure.md)

Bei Dateifreigabezeugen gibt es Anforderungen an den Dateiserver. Weitere Informationen finden Sie unter [Systemanforderungen](../concepts/system-requirements.md).

## <a name="create-an-azure-storage-account"></a>Erstellen eines Azure-Speicherkontos

In diesem Abschnitt wird beschrieben, wie Sie ein Azure-Speicherkonto erstellen können. Dieses Konto wird verwendet, um eine Azure-Blobdatei zu speichern, die für die Vermittlung eines bestimmten Clusters verwendet wird. Sie können das gleiche Azure-Speicherkonto verwenden, um einen Cloudzeugen für mehrere Cluster zu konfigurieren.

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com) an.
1. Klicken Sie im Startmenü des Azure-Portals unter **Azure-Dienste** auf die Option **Speicherkonten**. Wenn dieses Symbol fehlt, klicken Sie auf **Ressource erstellen**, um zuerst eine *Speicherkontoressource* zu erstellen.

    :::image type="content" source="media/witness/cloud-witness-home.png" alt-text="Startbildschirm des Azure-Portals" lightbox="media/witness/cloud-witness-home.png":::

1. Klicken Sie auf der Seite **Speicherkonten** auf **Neu**.

    :::image type="content" source="media/witness/cloud-witness-create.png" alt-text="Neues Azure-Speicherkonto" lightbox="media/witness/cloud-witness-create.png":::

1. Führen Sie auf der Seite **Speicherkonto erstellen** die folgenden Schritte aus:
    1. Wählen Sie das **Azure-Abonnement** aus, auf das das Speicherkonto angewendet werden soll.
    1. Wählen Sie die **Ressourcengruppe** aus, auf die das Speicherkonto angewendet werden soll.
    1. Geben Sie den **Speicherkontonamen** ein.
    <br>Speicherkontonamen müssen zwischen 3 und 24 Zeichen lang sein und dürfen nur Zahlen und Kleinbuchstaben enthalten. Ebenso muss der Name innerhalb von Azure eindeutig sein.
    1. Wählen Sie den **Standort** aus, der Ihrem Standort am nächsten ist.
    1. Wählen Sie für **Leistung** die Option **Standard** aus.
    1. Klicken Sie unter **Kontoart** auf **Storage general purpose** (Speicher (universell)).
    1. Wählen Sie für **Replikation** die Option **Lokal redundanter Speicher (LRS)** aus.
    1. Wenn Sie fertig sind, klicken Sie auf **Überprüfen + erstellen**.

    :::image type="content" source="media/witness/cloud-witness-create-storage-account.png" alt-text="Azure-Speicherkonto erstellen" lightbox="media/witness/cloud-witness-create-storage-account.png":::

1. Vergewissern Sie sich, dass das Speicherkonto den Validierungsprozess erfolgreich abschließt, und prüfen Sie anschließend die Kontoeinstellungen. Klicken Sie auf **Erstellen**, wenn Sie fertig sind.

    :::image type="content" source="media/witness/cloud-witness-validation.png" alt-text="Azure-Speicherkonto validieren" lightbox="media/witness/cloud-witness-validation.png":::

1. Es kann einige Sekunden dauern, bis das Konto in Azure bereitgestellt wird. Klicken Sie nach dem Abschluss der Bereitstellung auf **Zu Ressource wechseln**.

    :::image type="content" source="media/witness/cloud-witness-deployment.png" alt-text="Azure-Speicherkonto bereitstellen" lightbox="media/witness/cloud-witness-deployment.png":::

## <a name="copy-the-access-key-and-endpoint-url"></a>Kopieren des Zugriffsschlüssels und der Endpunkt-URL

Wenn Sie ein Azure-Speicherkonto erstellen, werden bei dem Prozess automatisch zwei Zugriffsschlüssel erstellt: ein Primärschlüssel (key1) und ein Sekundärschlüssel (key2). Wenn zum ersten Mal ein Cloudzeuge erstellt wird, wird **key1** verwendet. Die Endpunkt-URL wird ebenfalls automatisch generiert.

Azure-Cloudzeugen verwenden eine Blobdatei als Speicher, für den ein Endpunkt im Format *storage_account_name.blob.core.windows.net* erstellt wird. 

> [!NOTE]  
> Azure-Cloudzeugen verwenden HTTPS (Standardport 443) für die Kommunikation mit dem Azure-Blobdienst. Vergewissern Sie sich, dass der HTTPS-Port zugänglich ist.

### <a name="copy-the-account-name-and-access-key"></a>Kopieren des Kontonamens und des Zugriffsschlüssels

1. Klicken Sie im Azure-Portal unter **Einstellungen** auf **Zugriffsschlüssel**.
1. Klicken Sie auf **Schlüssel anzeigen**, damit die Schlüsselinformationen angezeigt werden.
1. Klicken Sie auf das Symbol zum Kopieren und Einfügen rechts neben den Feldern **Speicherkontoname** und **key1**, und fügen Sie jede Textzeichenfolge in Editor oder einen anderen Text-Editor ein.

    :::image type="content" source="media/witness/cloud-witness-access-keys.png" alt-text="Zugriffsschlüssel für ein Azure-Speicherkonto" lightbox="media/witness/cloud-witness-access-keys.png":::

### <a name="copy-the-endpoint-url-optional"></a>Kopieren der Endpunkt-URL (optional)

Die Endpunkt-URL ist optional und wird für einen Cloudzeugen möglicherweise nicht benötigt.

1. Klicken Sie im Azure-Portal auf **Eigenschaften**.
1. Klicken Sie auf **Schlüssel anzeigen**, damit Endpunktinformationen angezeigt werden.
1. Klicken Sie unter **Blob-Dienst** auf das Symbol zum Kopieren und Einfügen rechts neben dem Feld **Blob-Dienst**, und fügen Sie die Textzeichenfolge in Editor oder einen anderen Text-Editor ein.

    :::image type="content" source="media/witness/cloud-witness-blob-service.png" alt-text="Azure-Blobendpunkt" lightbox="media/witness/cloud-witness-blob-service.png":::

## <a name="create-a-cloud-witness-using-windows-admin-center"></a>Erstellen von Cloudzeugen mithilfe von Windows Admin Center

Nun können Sie mithilfe von Windows Admin Center eine Zeugeninstanz für Ihren Cluster erstellen.

1. Wählen Sie in der oberen Dropdownliste in Windows Admin Center die Option **Cluster-Manager** aus.
1. Wählen Sie unter **Clusterverbindungen** den Cluster aus.
1. Wählen Sie unter **Extras** die Option **Einstellungen** aus.
1. Wählen Sie im rechten Bereich **Zeuge** aus.
1. Wählen Sie als **Zeugentyp** einen der folgenden Werte aus:
      - **Cloudzeuge:** Geben Sie wie zuvor beschrieben den Namen Ihres Azure-Speicherkontos, den Zugriffsschlüssel und die Endpunkt-URL ein.
      - **Dateifreigabezeuge**: Geben Sie den Pfad der Dateifreigabe ein (//server/share).
1. Wenn Sie einen Cloudzeugen erstellen möchten, fügen Sie in die folgenden Felder die bereits kopierten Textzeichenfolgen ein:
    1. **Azure-Speicherkontoname**
    1. **Azure-Speicherzugriffsschlüssel**
    1. **Azure-Dienstendpunkt**

    :::image type="content" source="media/witness/cloud-witness-1.png" alt-text="Cloudzeuge: Zugriffsschlüssel" lightbox="media/witness/cloud-witness-1.png":::

1. Klicken Sie abschließend auf **Speichern**. Es kann einen Moment dauern, bis die Informationen an Azure weitergegeben werden.

> [!NOTE]
> Die dritte Option, **Datenträgerzeuge**, eignet sich nicht für die Verwendung in Stretchingclustern.

## <a name="create-a-cloud-witness-using-windows-powershell"></a>Erstellen eines Cloudzeugen mithilfe von Windows PowerShell

Alternativ können Sie auch mithilfe von PowerShell eine Zeugeninstanz für Ihren Cluster erstellen.

Verwenden Sie das folgende Cmdlet, um einen Azure-Cloudzeugen zu erstellen. Geben Sie wie zuvor beschrieben den Namen des Azure-Speicherkontos und die Informationen des Zugriffsschlüssels ein:

```powershell
Set-ClusterQuorum –Cluster "Cluster1" -CloudWitness -AccountName "AzureStorageAccountName" -AccessKey "AzureStorageAccountAccessKey"
```

Verwenden Sie das folgende Cmdlet, um einen Dateifreigabezeugen zu erstellen. Geben Sie den Pfad zur Dateiserverfreigabe ein:

```powershell
Set-ClusterQuorum -FileShareWitness "\\fileserver\share" -Credential (Get-Credential)
```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Clusterquorum finden Sie unter [Grundlegendes zum Cluster- und Poolquorum in Azure Stack HCI](../concepts/quorum.md).

- Weitere Informationen zum Erstellen und Verwalten von Azure-Speicherkonten finden Sie unter [Erstellen eines Azure-Speicherkontos](https://docs.microsoft.com/azure/storage/common/storage-account-create).