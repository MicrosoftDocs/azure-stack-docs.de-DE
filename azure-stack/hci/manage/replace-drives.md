---
title: Ersetzen fehlerhafter Laufwerke in Azure Stack HCI
description: Hier erfahren Sie, wie Sie fehlerhafte Laufwerke in Azure Stack HCI ersetzen.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 02/27/2020
ms.openlocfilehash: f4e67545519f54d630c8a83e100d2b4918025f0e
ms.sourcegitcommit: 76af742a42e807c400474a337e29d088ede8a60d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/22/2020
ms.locfileid: "85196374"
---
# <a name="replace-failed-drives-on-azure-stack-hci"></a>Ersetzen fehlerhafter Laufwerke in Azure Stack HCI

Azure Stack HCI arbeitet mit direkt verbundenen SATA-, SAS- oder NVME-Laufwerken, die physisch an nur jeweils einen Server angeschlossen sind. Im Falle eines Laufwerksausfalls benötigen Sie Zugang zur physischen Serverhardware, um das Laufwerk auszutauschen.

## <a name="find-the-alert"></a>Anzeigen der Warnung
Im Falle eines Laufwerksausfalls wird links oben im Bereich **Warnungen** des Dashboards **Windows Admin Center** eine Warnung angezeigt. Alternativ können Sie im Navigationsbereich auf der linken Seite die Option **Laufwerke** auswählen oder auf der Kachel in der rechten unteren Ecke auf den Link **VIEW DRIVES >** (LAUFWERKE ANZEIGEN >) klicken, um die Laufwerke zu durchsuchen und sich über deren Status zu informieren. Das Raster auf der Registerkarte **Ansicht** kann sortiert, gruppiert und nach Schlüsselwörtern durchsucht werden.

1. Klicken Sie auf dem Dashboard auf die Warnung, um Details wie etwa den physischen Standort des Laufwerks anzuzeigen.
1. Weitere Informationen erhalten Sie, indem Sie auf die Verknüpfung **Go to drive** (Zum Laufwerk) klicken, um zur Detailseite **Laufwerk** zu gelangen.
1. Sofern dies von Ihrer Hardware unterstützt wird, können Sie auf **Turn light on/off** (Licht ein-/ausschalten) klicken, um die Kontrollleuchte des Laufwerks zu steuern.
   Fehlerhafte Laufwerke werden von „Direkte Speicherplätze“ automatisch außer Betrieb genommen und entfernt. In diesem Fall hat das Laufwerk den Status „Zurückgezogen“, und die Leiste für die Speicherkapazität ist leer.
1. Entfernen Sie das fehlerhafte Laufwerk, und installieren Sie ein Ersatzlaufwerk.

## <a name="wait-for-the-alert-to-clear"></a>Warten, bis die Warnung entfernt wird
Das neue Laufwerk wird unter **Laufwerke > Bestand** angezeigt. Im Laufe der Zeit wird die Warnung entfernt, die Volumes werden repariert und kehren zum Status „OK“ zurück, und der Speicher wird mit dem neuen Laufwerk ausgeglichen – alles ganz ohne Benutzeraktion.

## <a name="next-steps"></a>Nächste Schritte
-  Informationen zur Nachverfolgung der Speicherintegrität auf unterschiedlichen Ebenen (einschließlich der Laufwerksebene) finden Sie unter [Behandeln von Problemen im Zusammenhang mit Integritäts- und Betriebszuständen von Speicherplätzen und „Direkte Speicherplätze“](/windows-server/storage/storage-spaces/storage-spaces-states).
