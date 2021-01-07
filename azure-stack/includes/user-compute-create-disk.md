---
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: include
ms.date: 12/16/2020
ms.reviewer: thoroet
ms.lastreviewed: 08/04/2020
ms.openlocfilehash: 8d92b379a2c3c0e77fc3e682121963afcc8be31d
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97867283"
---
1. Melden Sie sich beim Azure Stack Hub-Benutzerportal an.

    Wenn Sie ein Cloudbetreiber sind, der einen Plattformdatenträger erstellt, befolgen Sie die Anweisungen unter [Hinzufügen eines Plattformimages](../operator/azure-stack-add-vm-image.md#add-a-platform-image), um die VHD über das Administratorportal oder mit den Administratorendpunkten hinzuzufügen.

2. Wählen Sie im Benutzerportal **Alle Dienste** > **Datenträger** > **Hinzufügen** aus.

3. Gehen Sie unter **Verwalteten Datenträger erstellen** so vor:

    1. Geben Sie den **Namen** Ihres Images ein.
    2. Wählen Sie Ihr **Abonnement** aus.
    3. Erstellen Sie das Image, oder fügen Sie es einer **Ressourcengruppe** hinzu.
    4. Wählen Sie den **Speicherort**, auch als Region bezeichnet, Ihres Azure Stack Hub Development Kit (ASDK) aus.
    5. Wählen Sie den **Kontotyp** aus.
        - **Premium-Datenträger (SSD)** basieren auf Solid State Drives und bieten konsistente Leistung mit geringen Wartezeiten. Sie bieten das beste Preis-Leistungs-Verhältnis und eignen sich ideal für E/A-intensive Anwendungen und Produktionsworkloads.  
        - **Standard-Datenträger (HDD)** basieren auf magnetischen Laufwerken und werden für Anwendungen bevorzugt, in denen nur selten auf Daten zugegriffen wird. Zonenredundante Datenträger basieren auf zonenredundantem Speicher, der Ihre Daten in mehreren Zonen repliziert, und sind auch dann verfügbar, wenn eine einzelne Zone ausfällt.

    6. Wählen Sie unter **Quelltyp** die Option **Speicherblob** aus. Sie erstellen einen Datenträger aus einem Blob in einem Speicherkonto.
    7. Wählen Sie für die VHD-Quelle Folgendes aus:
        1. Das Quellabonnement, in dem sich das Speicherkonto befindet.
        1. Wählen Sie **Durchsuchen** aus, und navigieren Sie zu Ihrem Speicherkonto, Container und Ihrer VHD. Klicken Sie auf **Auswählen**.
        1. Wählen Sie den **Betriebssystemtyp** aus, der der VHD entspricht.
    8. Wählen Sie eine **Datenträgergröße (GiB)** aus, die mindestens der Größe Ihrer VHD entspricht.
    9. Klicken Sie auf **Erstellen**.

4. Nachdem der Datenträger erstellt wurde, können Sie ihn nutzen, um eine neue VM zu erstellen.