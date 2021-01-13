---
title: Installieren von Firmware mithilfe von iDRAC
description: Hier erfahren Sie, wie Sie Firmware mithilfe von iDRAC installieren.
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 3b6a257d5f5f42c951313c2220559306577de15d
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910243"
---
# <a name="installing-firmware-using-the-idrac-interface"></a>Installieren von Firmware mithilfe der iDRAC-Schnittstelle

Die integrierte Dell Remote Access Card-Plattform (iDRAC) ermöglicht es Ihnen, Firmware per Remoteverbindung zu aktualisieren (eine Aktualisierung nach der anderen). Dafür nutzen Sie das **Update and Rollback**-Feature. Dieses funktioniert auch, wenn der Server ausgeführt wird.

Rufen Sie die Dokumentationsseite [Integriertes System für Microsoft Azure Stack Hub 14G](https://www.dell.com/support/home/product-support/product/cloud-for-microsoft-azure-stack14g/docs) auf, um die aktuelle Unterstützungsmatrix herunterzuladen. Darin finden Sie eine Liste unterstützter Firmwareversionen.

Verwenden Sie die folgende Prozedur, um mithilfe der iDRAC-Webschnittstelle die Firmware der einzelnen Geräte zu aktualisieren.

**Schritte**

1.  Navigieren Sie zu **Maintenance** (Wartung) und dann zu \**System Update** (Systemupdate). Die Seite **Firmware Update** (Firmwareupdate) wird angezeigt.

    ![Screenshot mit der Seite „Maintenance“ (Wartung) für den Integrated Dell Remote Access Controller 9 | Enterprise](media/image-85.png)

2.  Klicken Sie auf der Registerkarte **Update** auf **Local** als Speicherort für Dateien.

3.  Klicken Sie auf **Browse** (Durchsuchen), und wählen Sie das Firmwareimage für die erforderliche Komponente aus. Klicken Sie dann auf **Upload** (Hochladen).

4.  Nach Abschluss des Uploads werden im Abschnitt **Update Details** (Updatedetails) die einzelnen Firmwaredateien mit dazugehörigem Status angezeigt, die zu iDRAC hochgeladen wurden. Wenn die Firmwareimagedatei gültig ist und erfolgreich hochgeladen wurde, wird in der Spalte **Contents** (Inhalte) ein +-Symbol neben dem Namen der Firmwareimagedatei angezeigt. Erweitern Sie den Namen, damit die Informationen **Device Name** (Gerätename), **Current** (Aktuell) und **Available firmware version** (Verfügbare Firmwareversion) angezeigt werden.

5.  Wählen Sie die erforderliche Firmwaredatei aus, und führen Sie Folgendes aus:

    -   Wenn Firmwareimages keinen Neustart des Hostsystems erfordern, klicken Sie auf **Install** (Installieren).

    -   Wenn für Firmwareimages ein Neustart des Hostsystems erforderlich ist, klicken Sie auf **Install and Reboot** (Installieren und neu starten) oder auf **Install Next Reboot** (Beim nächsten Neustart installieren).

    -   Wenn Sie dieses Firmwareupdate abbrechen möchten, klicken Sie auf **Cancel** (Abbrechen).

6.  Damit die Seite **Job Queue** (Auftragswarteschlange) angezeigt wird, klicken Sie auf **Job Queue** (Auftragswarteschlange). Verwenden Sie diese Seite, um die bereitgestellten Firmwareupdates anzuzeigen und zu verwalten, oder klicken Sie auf

    **OK**, um die aktuelle Seite zu aktualisieren und den Status der Firmwareupdates anzuzeigen.
    
