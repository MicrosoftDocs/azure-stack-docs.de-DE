---
title: Überprüfen des Zugriffs und der Integrität von Skalierungseinheitknoten
description: Hier erhalten Sie Informationen zum Überprüfen des Zugriffs und der Integrität von Skalierungseinheitknoten.
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: cec29fed89fa02f6a5142f9eb179d8fc8437f37d
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97392963"
---
# <a name="verifying-scale-unit-node-access-and-health"></a>Überprüfen des Zugriffs und der Integrität von Skalierungseinheitknoten



Melden Sie sich bei der Privileged Access Workstation an, starten Sie das Verwaltungsportal, überprüfen Sie die Systemintegrität, rufen Sie die IP-Adressen für den privilegierten Endpunkt ab, und ermitteln Sie, welche Knoten entladen oder fortgesetzt werden müssen.

1.  Stellen Sie per Remotedesktop eine Verbindung zur Privileged Access Workstation her.

2.  Greifen Sie auf das Verwaltungsportal in Azure Stack Hub zu.

    Melden Sie sich mit den vom Kunden erhaltenen Anmeldeinformationen beim Verwaltungsportal in Azure Stack Hub an.
        
3.  Rufen Sie die IP-Adressen des privilegierten Endpunkts ab.


    Klicken Sie auf die Kachel **Region Management** (Regionsverwaltung), und klicken Sie auf **Properties** (Eigenschaften). Scrollen Sie nach unten im Bereich, und suchen Sie nach den IP-Adressen im Feld **Privileged endpoint IP addresses** (Privilegierte Endpunkt-IP-Adressen). Notieren Sie sich diese, da Sie sie möglicherweise später in dieser Prozedur benötigen. Möglicherweise benötigt sie auch der Support, falls Probleme auftreten.

    [![](media/image-18-inline.png)](media/image-18-expanded.png)
    
4.  Sehen Sie sich aktuelle Warnungen an.

    Klicken Sie unter **Region Management** (Regionsverwaltung) auf **Alerts** (Warnungen), und sehen Sie sich aktuelle Warnungen an. Wenn Sie dabei auf unerwartete Warnungen stoßen, wenden Sie sich an den Support von Dell Technologies, um zu klären, ob die Probleme gelöst oder sicher ignoriert werden können.
    
    [![](media/image-19-inline.png)](media/image-19-expanded.png)
    
5.  Ermitteln Sie den Skalierungseinheitknoten.

    Wenn Sie nur das Diensttag erhalten haben und im Verwaltungsportal in Azure Stack Hub nicht ermitteln können, für welchen Knoten ein Problem besteht (d. h. der Energiestatusknoten wurde bereits angehalten), befolgen Sie die folgenden Schritte, um den Skalierungseinheitknoten mit dem Diensttag zu korrelieren:
    
    1.  Klicken Sie unter **Region Management** (Regionsverwaltung) auf **Scale Units** (Skalierungseinheiten), und wählen Sie dann als Cluster **s-cluster** aus. Wählen Sie **Knoten** aus.
    
    1.  Klicken Sie auf den **BMC**-IP-Adresslink, um das Diensttag des Knotens abzurufen. Dadurch wird die iDRAC-Webschnittstelle des Servers in einer neuen Registerkarte oder in einem neuen Fenster geöffnet.

        [![](media/image-20-inline.png)](media/image-20-expanded.png) 
    
    1.  Melden Sie sich bei der iDRAC-Schnittstelle an, und überprüfen Sie das Diensttag des Knotens im Bereich **System Information** (Systeminformationen).
    
    1.  Wiederholen Sie diese Prozedur für jeden Knoten, und korrelieren Sie diese Diensttags mit dem geplanten Hardwareaustausch, um zu bestimmen, welche Knoten gewartet werden müssen.

        [![](media/image-21-inline.png)](media/image-21-expanded.png)
    
