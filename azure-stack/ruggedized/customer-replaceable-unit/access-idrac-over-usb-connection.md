---
title: Zugreifen auf iDRAC über eine USB-Verbindung
description: Hier erfahren Sie, wie Sie über eine USB-Verbindung auf iDRAC zugreifen.
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 5c270de88d78bb8cb7ba1f7b9216c4a160a4aae8
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910056"
---
# <a name="accessing-the-idrac-interface-over-a-direct-usb-connection"></a>Zugreifen auf die iDRAC-Schnittstelle über eine direkte USB-Verbindung

Das direkte iDRAC-Feature ermöglicht es Ihnen, Ihren Laptop direkt mit dem iDRAC-USB-Anschluss zu verbinden. Dieses Feature ermöglicht die direkte Interaktion mit den iDRAC-Schnittstellen wie der Weboberfläche, RACADM und WSMan für die erweiterte Serververwaltung und -wartung.



Führen Sie auf Ihrem Laptop die folgenden Schritte aus, um über den USB-Anschluss auf die iDRAC-Schnittstelle zuzugreifen.

**Schritte**

1.  Deaktivieren Sie auf Ihrem Laptop alle Drahtlosnetzwerke, und trennen Sie die Verbindung mit allen anderen kabelgebundenen Netzwerken.

2.  Verbinden Sie Ihren Laptop mithilfe eines Micro-USB-Kabels mit dem iDRAC-Direktanschluss, der sich auf der Vorderseite des Servers befindet.
    Sehen Sie sich Element 4 in der Abbildung an.

    ![Abbildung, in der ein Netzschalter, USB- und Micro-USB-Anschlüsse dargestellt sind](media/image-67.png)

3.  Warten Sie, bis der Laptop die IP-Adresse 169.254.0.4 erhält.

    Es kann mehrere Sekunden dauern, bis die IP-Adressen abgerufen werden. iDRAC ruft die IP-Adresse 169.254.0.3 ab.

4.  Stellen Sie eine Verbindung mit der iDRAC-Weboberfläche her.

    Öffnen Sie einen Browser, und navigieren Sie zu 169.254.0.3, um auf die iDRAC-Weboberfläche zuzugreifen.

5.  Führen Sie die erforderlichen Schritte aus.

    

    > [!NOTE]
    > Wenn die iDRAC-Schnittstelle den USB-Anschluss verwendet (Element 3 auf der obigen Abbildung), leuchtet die LED und zeigt somit Aktivität an. Die LED blinkt viermal pro Sekunde.
    
6.  Trennen Sie das Micro-USB-Kabel.

    Trennen Sie nach Abschluss der gewünschten Aktionen das USB-Kabel vom System. Die LED blinkt nicht mehr.
    
