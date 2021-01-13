---
title: Überprüfen des Zugriffs und der Integrität des Hardwarelebenszyklus-Hosts
description: Hier erfahren Sie, wie Sie Zugriff und Integrität für den Hardwarelebenszyklushost überprüfen.
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: d4dd073e3ec9ec47110df916517ef0f5e3273a3a
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910668"
---
# <a name="verifying-hardware-lifecycle-host-access-and-health"></a>Überprüfen von Zugriff und Integrität für den Hardwarelebenszyklushost

Melden Sie sich beim Betriebssystem für iDRAC und Hardwarelebenszyklushost (HLH) an, und überprüfen Sie die Systemintegrität.

1.  Stellen Sie eine Verbindung mit iDRAC her.

    1.  Navigieren Sie mithilfe eines Webbrowsers zur iDRAC-Webschnittstelle, und melden Sie sich mit den vom Kunden bereitgestellten Anmeldeinformationen an.

        ![Screenshot der iDRAC-Anmeldeseite](media/image-3.png) 
    
    1.  Klicken Sie im oberen Navigationsmenü auf **System**.

        ![Screenshot, in dem das Dashboard mit hervorgehobenem Menü „System“ dargestellt wird](media/image-4.png)
        
    1.  Überprüfen Sie auf der Registerkarte **Overview** (Übersicht), ob das System vollständig fehlerfrei ist oder ein erwartetes Problem anzeigt, das während dem Hardwareaustausch gelöst werden soll.
    
        ![Screenshot der Seite „System“ mit hervorgehobenen Kacheln der Übersicht](media/image-5.png)
    
2.  Stellen Sie mithilfe der virtuellen iDRAC-Konsole eine Verbindung zum HLH-Betriebssystem her.

    > [!NOTE]
    > Sie können diesen Schritt überspringen, wenn Sie sich mit einem Notfallwagen über VGA- und USB-Verbindungen anmelden.
    
    1.  Klicken Sie in der iDRAC-Webschnittstelle auf **Dashboard**.

        ![Screenshot mit ausgewählter Schaltfläche „Dashboard“](media/image-6.png)
    
    1.  Klicken Sie im Bereich **Virtual Console** (Virtuelle Konsole) auf **Settings** (Einstellungen).
    
        ![Screenshot mit ausgewählter Schaltfläche „Settings“ (Einstellungen)](media/image-7.png)
        
    1.  Überprüfen Sie, ob **Plug-in Type** (Plug-In-Typ) auf **HTML 5** festgelegt ist. Ist dies nicht der Fall, ändern Sie dies, klicken Sie auf **Apply** (Anwenden) und dann bei Aufforderung auf **OK**.
    
        ![Screenshot der Seite „Konfiguration“. „Plug-in Type“ (Plug-In-Typ) ist auf „HTML 5“ festgelegt, und die Schaltfläche „Anwenden“ ist ausgewählt.](media/image-8.png)
        
    1.  Klicken Sie auf **Launch Virtual Console** (Virtuelle Konsole starten).

        ![Screenshot der Seite „Configuration“ (Konfiguration) mit hervorgehobener Option „Launch Virtual Console“ (Virtuelle Konsole starten)](media/image-9.png)
    
    1.  Wenn eine Popupwarnung angezeigt wird, ändern Sie die Browsereinstellungen in „Always allow“ (Immer zulassen). Klicken Sie im Internet Explorer beispielsweise auf **Optionen für diese Site** und dann auf **Immer zulassen**. Wenn es erforderlich ist, wiederholen Sie den vorherigen Schritt nach der Änderung der Browsereinstellung, um die virtuelle Konsole zu starten.
    
        ![Screenshot des Popups „Warning“ (Warnung)](media/image-10.png)
        
    1.  Die virtuelle Konsole sollte nun angezeigt werden. Klicken Sie im oberen Menü auf **Console Controls** (Konsolensteuerelemente), um sich beim Betriebssystem anzumelden.
    
        ![Screenshot der virtuellen Konsole, auf der die Schaltfläche „Console Controls“ (Konsolensteuerelemente) ausgewählt ist](media/image-11.png)
        
    1.  Klicken Sie auf **Keyboard Macro** (Tastaturmakro), drücken Sie dann **STRG+ALT+ENTF**, und klicken Sie dann auf **Apply** (Anwenden) und dann auf **Close** (Schließen).
    
        ![Screenshot des Bildschirms „Console Controls“ (Konsolensteuerelemente), in dem „Keyboard Macros“ (Tastaturmakros) und die Schaltfläche „Close“ (Schließen) hervorgehoben sind](media/image-12.png)
        
    1.  Wählen Sie basierend auf den vom Kunden bereitgestellten Anmeldeinformationen den **Benutzer** aus, geben Sie das Kennwort ein, und klicken Sie dann auf den **Pfeil**, um sich anzumelden.
    
        ![Screenshot mit den eingegebenen Benutzeranmeldeinformationen](media/image-13.png)
        
        Sie sind nun beim HLH angemeldet.
        
3.  Überprüfen Sie die Integrität.

    1.  Starten Sie den **Server-Manager**.

        ![Screenshot mit ausgewählter Option „Server-Manager“](media/image-14.png)
        
    1.  Klicken Sie an der Eingabeaufforderung der **Benutzerkontensteuerung** auf **Ja**.
    
        ![Screenshot der Eingabeaufforderung „Benutzerkontensteuerung“ mit ausgewählter Schaltfläche „Ja“](media/image-15.png)
        
    1.  Wählen Sie im Menü **Tools** (Extras) die Option **Hyper-V Manager** (Hyper-V-Manager).
    
        ![Screenshot, in dem das Menü „Extras“ geöffnet und „Hyper-V-Manager“ ausgewählt ist](media/image-16.png)
        
    1.  Wählen Sie im **Hyper-V-Manager** im linken Menü den obersten Knoten aus, und überprüfen Sie dann, ob die VMs wie **Privileged Access Workstation** wenn vorhanden den Status **Wird ausgeführt** aufweisen.