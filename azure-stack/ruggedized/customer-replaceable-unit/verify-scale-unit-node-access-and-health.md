---
title: Überprüfen des Zugriffs und der Integrität von Skalierungseinheitknoten
description: Hier erhalten Sie Informationen zum Überprüfen des Zugriffs und der Integrität von Skalierungseinheitknoten.
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: d636b5ebb0d07c2711a17b2b2f10651480c38068
ms.sourcegitcommit: 5ea0e915f24c8bcddbcaf8268e3c963aa8877c9d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100488019"
---
# <a name="verifying-scale-unit-node-access-and-health"></a>Überprüfen des Zugriffs und der Integrität von Skalierungseinheitknoten

Melden Sie sich bei der Privileged Access Workstation an, starten Sie das Verwaltungsportal, überprüfen Sie die Systemintegrität, rufen Sie die IP-Adressen für den privilegierten Endpunkt ab, und ermitteln Sie, welche Knoten entladen oder fortgesetzt werden müssen.

1.  Stellen Sie per Remotedesktop eine Verbindung zur Privileged Access Workstation her.

2.  Greifen Sie auf das Verwaltungsportal in Azure Stack Hub zu.

    Melden Sie sich mit den vom Kunden erhaltenen Anmeldeinformationen beim Verwaltungsportal in Azure Stack Hub an.
        
3.  Rufen Sie die IP-Adressen des privilegierten Endpunkts ab.


    Klicken Sie auf die Kachel **Region Management** (Regionsverwaltung), und klicken Sie auf **Properties** (Eigenschaften). Scrollen Sie nach unten im Bereich, und suchen Sie nach den IP-Adressen im Feld **Privileged endpoint IP addresses** (Privilegierte Endpunkt-IP-Adressen). Notieren Sie sich diese, da Sie sie möglicherweise später in dieser Prozedur benötigen. Möglicherweise benötigt sie auch der Support, falls Probleme auftreten.

    [![Screenshot der Seite „Administration“ (Verwaltung) mit hervorgehobenem Abschnitt „Privileged endpoint IP addresses (Privilegierte Endpunkt-IP-Adressen)](media/image-18-inline.png)](media/image-18-expanded.png#lightbox)
    
4.  Sehen Sie sich aktuelle Warnungen an.

    Klicken Sie unter **Region Management** (Regionsverwaltung) auf **Alerts** (Warnungen), und sehen Sie sich aktuelle Warnungen an. Wenn Sie dabei auf unerwartete Warnungen stoßen, wenden Sie sich an den Microsoft-Support, um zu klären, ob die Probleme gelöst oder sicher ignoriert werden können.
    
    [![Screenshot der Seite „Properties“ (Eigenschaften) mit hervorgehobenem Abschnitt „Name“](media/image-19-inline.png)](media/image-19-expanded.png#lightbox)
    
5.  Ermitteln Sie die Skalierungseinheitknoten.

    Wenn Sie nur das Diensttag erhalten haben und im Verwaltungsportal in Azure Stack Hub nicht ermitteln können, für welchen Knoten ein Problem besteht (d. h. der Energiestatusknoten wurde bereits angehalten), befolgen Sie die folgenden Schritte, um den Skalierungseinheitknoten mit dem Diensttag zu korrelieren:
    
    1.  Klicken Sie unter **Region Management** (Regionsverwaltung) auf **Scale Units** (Skalierungseinheiten), und wählen Sie dann als Cluster **s-cluster** aus. Wählen Sie **Knoten** aus.
    
    1.  Klicken Sie auf den **BMC**-IP-Adresslink, um das Diensttag des Knotens abzurufen. Dadurch wird die iDRAC-Webschnittstelle des Servers in einer neuen Registerkarte oder in einem neuen Fenster geöffnet.

        [![Screenshot der Seite „Knoten“ mit hervorgehobener Spalte „BMC“](media/image-20-inline.png)](media/image-20-expanded.png#lightbox) 
    
    1.  Melden Sie sich bei der iDRAC-Schnittstelle an, und überprüfen Sie das Diensttag des Knotens im Bereich **System Information** (Systeminformationen).
    
    1.  Wiederholen Sie diese Prozedur für jeden Knoten, und korrelieren Sie diese Diensttags mit dem geplanten Hardwareaustausch, um zu bestimmen, welche Knoten gewartet werden müssen.

        [![Screenshot, in dem das Dashboard mit hervorgehobenem Eintrag „Diensttag“ dargestellt wird](media/image-21-inline.png)](media/image-21-expanded.png#lightbox)
    
