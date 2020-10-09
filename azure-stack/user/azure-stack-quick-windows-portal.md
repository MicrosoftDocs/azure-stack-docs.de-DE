---
title: Erstellen eines virtuellen Windows-Computers mit dem Azure Stack Hub-Portal
description: Hier erfahren Sie, wie Sie einen virtuellen Windows Server 2016-Computer (VM) mit dem Azure Stack Hub-Portal erstellen.
author: mattbriggs
ms.topic: quickstart
ms.date: 10/06/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 10/06/2020
ms.openlocfilehash: 77c7e3484186a0bea1f6220330338128aad72fad
ms.sourcegitcommit: 2d2ae0b6db2e4f43f8496b184f30cddbb08b2cbd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2020
ms.locfileid: "91815417"
---
# <a name="quickstart-create-a-windows-server-vm-with-the-azure-stack-hub-portal"></a>Schnellstart: Erstellen eines virtuellen Windows Server-Computers mit dem Azure Stack Hub-Portal

Hier erfahren Sie, wie Sie einen virtuellen Windows Server 2016-Computer (VM) mit dem Azure Stack Hub-Portal erstellen.

> [!NOTE]  
> Die Screenshots in diesem Artikel werden entsprechend der Benutzeroberfläche aktualisiert, die mit der Azure Stack Hub-Version 1808 eingeführt wurde. 1808 bietet Unterstützung für die Verwendung von *verwalteten Datenträgern*, zusätzlich zu nicht verwalteten Datenträgern. Wenn Sie eine frühere Version verwenden, unterscheiden sich einige Abbildungen, wie die Datenträgerauswahl, von denen, die in diesem Artikel gezeigt werden.  


## <a name="sign-in-to-the-azure-stack-hub-portal"></a>Anmelden beim Azure Stack Hub-Portal

Melden Sie sich beim Azure Stack Hub-Portal an. Die Adresse des Azure Stack Hub-Portals hängt davon ab, mit welchem Azure Stack Hub-Produkt Sie eine Verbindung herstellen:

* Verwenden Sie für das Azure Stack Development Kit (ASDK) die folgende Adresse: `https://portal.local.azurestack.external`.
* Rufen Sie bei einem integrierten Azure Stack Hub-System die vom Azure Stack Hub-Betreiber bereitgestellte URL auf.

## <a name="create-a-vm"></a>Erstellen einer VM

1. Wählen Sie **Ressource erstellen** > **Compute** aus. Suchen Sie nach ` Windows Server 2016 Datacenter - Pay as you use`.
    Wird der Eintrag **Windows Server 2016 Datacenter – nutzungsbasierte Bezahlung** nicht angezeigt, wenden Sie sich an Ihren Azure Stack Hub-Cloudbetreiber, und bitten Sie ihn, das Image zum Azure Stack Hub-Marketplace hinzuzufügen. Eine entsprechende Anleitung findet Ihr Cloudbetreiber unter [Erstellen und Veröffentlichen eines benutzerdefinierten Azure Stack Hub-Marketplace-Elements](../operator/azure-stack-create-and-publish-marketplace-item.md).

    ![Windows Server 2016 Datacenter – nutzungsbasierte Bezahlung](./media/azure-stack-quick-windows-portal/image1.png)

1. Klicken Sie auf **Erstellen**.

    ![Erstellen einer Ressource](./media/azure-stack-quick-windows-portal/image2.png)

1. Geben Sie unter **Grundlagen** Werte für **Name**, **Datenträgertyp**, **Benutzername** und **Kennwort** ein. Wählen Sie ein **Abonnement**aus. Erstellen Sie eine **Ressourcengruppe**, oder wählen Sie eine vorhandene aus, und wählen Sie einen **Speicherort** und anschließend **OK** aus.

    ![Erstellen eines virtuellen Computers: Grundlagen](./media/azure-stack-quick-windows-portal/image3.png)

1. Wählen Sie **D1_v2** unter **Größe** und dann **Auswählen** aus.

    ![Erstellen eines virtuellen Computers: Größe](./media/azure-stack-quick-windows-portal/image4.png)

1. Nehmen Sie auf der Seite **Einstellungen** sämtliche gewünschten Änderungen an den Standardwerten vor. Sie müssen die gewünschten öffentlichen Eingangsports über das zugehörige Dropdownmenü konfigurieren. Wählen Sie **OK**, wenn Sie fertig sind.

    ![Erstellen eines virtuellen Computers: Einstellungen](./media/azure-stack-quick-windows-portal/image5.png)

1. Wählen Sie zum Erstellen des virtuellen Computers unter **Zusammenfassung** die Option **OK** aus.

    ![Erstellen eines virtuellen Computers: Zusammenfassung](./media/azure-stack-quick-windows-portal/image6.png)

1. Wählen Sie zum Überprüfen des neuen virtuellen Computers **Virtuelle Computer** aus. Suchen Sie nach dem Namen des virtuellen Computers, und wählen Sie ihn dann in den Suchergebnissen aus.

![Erstellen eines virtuellen Computers: Suchen nach dem virtuellen Computer](./media/azure-stack-quick-windows-portal/image7.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie den virtuellen Computer nicht mehr benötigen, können Sie ihn und die Ressourcen löschen. Wählen Sie hierzu die Ressourcengruppe auf der Seite des virtuellen Computers und dann **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen einfachen virtuellen Windows Server-Computer bereitgestellt. Weitere Informationen zu Azure Stack Hub-VMs finden Sie unter [Features von Azure Stack Hub-VMs](azure-stack-vm-considerations.md).
