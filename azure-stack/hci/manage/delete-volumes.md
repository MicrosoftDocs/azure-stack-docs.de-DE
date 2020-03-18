---
title: Löschen von Volumes in Azure Stack HCI
description: Hier erfahren Sie, wie Sie Volumes in Azure Stack HCI mithilfe von Windows Admin Center löschen.
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 03/05/2020
ms.openlocfilehash: ab99ed7a49fe2bf003245f139451895a85c4edbf
ms.sourcegitcommit: a77dea675af6500bdad529106f5782d86bec6a34
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/10/2020
ms.locfileid: "79025978"
---
# <a name="deleting-volumes-in-azure-stack-hci"></a>Löschen von Volumes in Azure Stack HCI

> Gilt für: Windows Server 2019

In diesem Thema erfahren Sie, wie Sie Volumes in einem Cluster mit [Direkte Speicherplätze](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) mithilfe von Windows Admin Center löschen.

Sehen Sie sich ein kurzes Anleitungsvideo zum Löschen eines Volumes an:

> [!VIDEO https://www.youtube-nocookie.com/embed/DbjF8r2F6Jo]

## <a name="use-windows-admin-center-to-delete-a-volume"></a>Löschen eines Volumes mithilfe von Windows Admin Center

1. Stellen Sie in Windows Admin Center eine Verbindung mit einem Cluster mit „Direkte Speicherplätze“ her, und wählen Sie im Bereich **Tools** die Option **Volumes** aus.
2. Wählen Sie auf der Seite **Volumes** die Registerkarte **Bestand** und anschließend das Volume aus, das Sie löschen möchten.
3. Wählen Sie im oberen Bereich der Seite mit den Volumedetails die Option **Löschen** aus.
4. Aktivieren Sie im Bestätigungsdialogfeld das Kontrollkästchen, um zu bestätigen, dass Sie das Volume löschen möchten, und wählen Sie **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

Schrittanleitungen für weitere wichtige Speicherverwaltungsaufgaben finden Sie unter den folgenden Links:

- [Planen von Volumes in „Direkte Speicherplätze“](/windows-server/storage/storage-spaces/plan-volumes)
- [Erstellen von Volumes in „Direkte Speicherplätze“](/windows-server/storage/storage-spaces/create-volumes)
- [Erweitern von Volumes in „Direkte Speicherplätze“](/windows-server/storage/storage-spaces/resize-volumes)