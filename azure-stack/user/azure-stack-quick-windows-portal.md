---
title: Erstellen eines virtuellen Windows-Computers mit dem Azure Stack-Portal | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen virtuellen Windows Server 2016-Computer (VM) mit dem Azure Stack-Portal erstellen.
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 10/02/2019
ms.author: mabrigg
ms.custom: mvc
ms.reviewer: kivenkat
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 95fef782ca7efe09f7c93fbf0e28e81ed34d8166
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71823923"
---
# <a name="quickstart-create-a-windows-server-vm-with-the-azure-stack-portal"></a>Schnellstart: Erstellen eines virtuellen Windows Server-Computers mit dem Azure Stack-Portal

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Erfahren Sie, wie Sie einen virtuellen Windows Server 2016-Computer (VM) mithilfe des Azure Stack-Portals erstellen.

> [!NOTE]  
> Die Screenshots in diesem Artikel werden entsprechend der Benutzeroberfläche aktualisiert, die mit der Azure Stack Version 1808 eingeführt wurde. 1808 bietet Unterstützung für die Verwendung von *verwalteten Datenträgern*, zusätzlich zu nicht verwalteten Datenträgern. Wenn Sie eine frühere Version verwenden, unterscheiden sich einige Abbildungen, wie die Datenträgerauswahl, von denen, die in diesem Artikel gezeigt werden.  


## <a name="sign-in-to-the-azure-stack-portal"></a>Anmelden beim Azure Stack-Portal

Melden Sie sich beim Azure Stack-Portal an. Die Adresse des Azure Stack-Portals hängt davon ab, mit welchem Azure Stack-Produkt Sie eine Verbindung herstellen:

* Verwenden Sie für das Azure Stack Development Kit (ASDK) die folgende Adresse: https://portal.local.azurestack.external.
* Rufen Sie bei einem integrierten Azure Stack-System die vom Azure Stack-Operator bereitgestellte URL auf.

## <a name="create-a-vm"></a>Erstellen einer VM

1. Klicken Sie auf **+ Eine Ressource erstellen** > **Compute** > **Windows Server 2016 Datacenter – nutzungsbasierte Bezahlung** > **Erstellen**. <br> Wenn Ihnen der Eintrag **Windows Server 2016 Datacenter – Pay-as-you-use** nicht angezeigt wird, können Sie sich an Ihren Azure Stack-Operator wenden und ihn bitten, diesen Eintrag (wie im Artikel [Hinzufügen eines virtuellen Windows Server 2016-Computerimages zum Azure Stack-Marketplace](../operator/azure-stack-create-and-publish-marketplace-item.md) beschrieben) zum Marketplace hinzuzufügen.

    ![Schritte zum Erstellen eines virtuellen Windows-Computers im Portal](media/azure-stack-quick-windows-portal/image01.png)

2. Geben Sie unter **Grundlagen** Werte für **Name**, **Benutzername** und **Kennwort** ein. Wählen Sie ein **Abonnement**aus. Erstellen Sie eine **Ressourcengruppe**, oder wählen Sie eine vorhandene aus, wählen Sie einen **Speicherort** aus, und klicken Sie anschließend auf **OK**.

    ![Grundeinstellungen konfigurieren](media/azure-stack-quick-windows-portal/image02.png)

3. Klicken Sie unter **Größe** auf **D1 Standard** und dann auf **Auswählen**.  

    ![Größe des virtuellen Computers auswählen](media/azure-stack-quick-windows-portal/image03.png)

4. Nehmen Sie auf der Seite **Einstellungen** sämtliche gewünschten Änderungen an den Standardwerten vor.
   - Ab der Azure Stack Version 1808 können Sie den **Speicher** konfigurieren. An dieser Stelle können Sie auswählen, *verwaltete Datenträger*  zu verwenden. Bei Versionen vor Version 1808 können nur nicht verwaltete Datenträger verwendet werden.  

   ![Konfigurieren der Einstellungen für den virtuellen Computer](media/azure-stack-quick-windows-portal/image04.png)  

   Wenn Ihre Konfigurationen abgeschlossen sind, klicken Sie auf **OK**, um den Vorgang fortzusetzen.

5. Klicken Sie zum Erstellen des virtuellen Computers unter **Zusammenfassung** auf **OK**.
    ![Anzeigen der Zusammenfassung und Erstellen des virtuellen Computers](media/azure-stack-quick-windows-portal/image05.png)

6. Klicken Sie zum Anzeigen des neuen virtuellen Computers auf **Alle Ressourcen**, suchen Sie nach dessen Namen, und wählen Sie ihn dann aus den Suchergebnissen aus.

    ![Anzeigen des virtuellen Computers](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie den virtuellen Computer nicht mehr benötigen, können Sie ihn und die Ressourcen löschen. Wählen Sie hierzu die Ressourcengruppe auf der Seite des virtuellen Computers aus, und klicken Sie auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen einfachen virtuellen Windows Server-Computer bereitgestellt. Weitere Informationen zu Azure Stack-VMs finden Sie unter [Überlegungen zur Verwendung von virtuellen Computern in Azure Stack](azure-stack-vm-considerations.md).
