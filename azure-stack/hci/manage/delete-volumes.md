---
title: Löschen von Volumes in Azure Stack HCI
description: Hier erfahren Sie, wie Sie Volumes in Azure Stack HCI mithilfe von Windows Admin Center und PowerShell löschen.
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 03/17/2020
ms.openlocfilehash: cf556a9b6c130907e8607d8e5b9436b71756a3d4
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511894"
---
# <a name="deleting-volumes-in-azure-stack-hci"></a>Löschen von Volumes in Azure Stack HCI

> Gilt für: Windows Server 2019

In diesem Thema erfahren Sie, wie Sie Volumes in einem Cluster mit [Direkte Speicherplätze](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) mithilfe von Windows Admin Center löschen.

Sehen Sie sich ein kurzes Anleitungsvideo zum Löschen eines Volumes mit Windows Admin Center an:

> [!VIDEO https://www.youtube-nocookie.com/embed/DbjF8r2F6Jo]

## <a name="use-windows-admin-center-to-delete-a-volume"></a>Löschen eines Volumes mithilfe von Windows Admin Center

1. Stellen Sie in Windows Admin Center eine Verbindung mit einem Cluster mit „Direkte Speicherplätze“ her, und wählen Sie im Bereich **Tools** die Option **Volumes** aus.
2. Wählen Sie auf der Seite **Volumes** die Registerkarte **Bestand** und anschließend das Volume aus, das Sie löschen möchten.
3. Wählen Sie im oberen Bereich der Seite mit den Volumedetails die Option **Löschen** aus.
4. Aktivieren Sie im Bestätigungsdialogfeld das Kontrollkästchen, um zu bestätigen, dass Sie das Volume löschen möchten, und wählen Sie **Löschen** aus.

## <a name="delete-volumes-using-powershell"></a>Löschen von Volumes mithilfe von PowerShell

Verwenden Sie das Cmdlet **Remove-VirtualDisk**, um Volumes in „Direkte Speicherplätze“ zu löschen. Dieses Cmdlet wird verwendet, um das Objekt **VirtualDisk** zu löschen und den beanspruchten Speicherplatz in dem Speicherpool freizugeben, durch den das Objekt **VirtualDisk** verfügbar gemacht wurde.

Starten Sie zunächst PowerShell auf Ihrem Verwaltungs-PC, und führen Sie das Cmdlet **Get-VirtualDisk** mit dem Parameter **CimSession** aus. Hierbei handelt es sich um den Namen eines Clusters oder Serverknotens mit „Direkte Speicherplätze“ (beispielsweise *<Clustername>.microsoft.com*): 

```PowerShell
Get-VirtualDisk -CimSession clustername.microsoft.com
```

Dadurch wird eine Liste möglicher Werte für den Parameter **-FriendlyName** zurückgegeben, die Volumenamen in Ihrem Cluster entsprechen.

### <a name="example"></a>Beispiel

Wenn Sie ein gespiegeltes Volume namens *Volume1* löschen möchten, führen Sie in PowerShell den folgenden Befehl aus:

```PowerShell
Remove-VirtualDisk -FriendlyName "Volume1"
```

Sie werden aufgefordert, zu bestätigen, dass Sie die Aktion ausführen und alle auf dem Volume befindlichen Daten löschen möchten. Wählen Sie „Y“ oder „N“ aus.

   > [!WARNING]
   > Diese Aktion kann nicht rückgängig gemacht werden. In diesem Beispiel wird ein Volume-Objekt vom Typ **VirtualDisk** endgültig gelöscht.

## <a name="next-steps"></a>Nächste Schritte

Schrittanleitungen für weitere wichtige Speicherverwaltungsaufgaben finden Sie unter den folgenden Links:

- [Planen von Volumes in „Direkte Speicherplätze“](../concepts/plan-volumes.md)
- [Erstellen von Volumes in „Direkte Speicherplätze“](create-volumes.md)
- [Erweitern von Volumes in „Direkte Speicherplätze“](extend-volumes.md)