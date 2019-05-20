---
title: Azure Stack-Schnellstart – Erstellen eines virtuellen Windows-Computers
description: Azure Stack-Schnellstart – Erstellen eines virtuellen Windows-Computers mit dem Portal
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: unknown
ms.custom: mvc
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 47a2e2090a4bc6f4d72f5288f31a6e1cb53bdac7
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985969"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-with-the-azure-stack-portal"></a>Schnellstart: Erstellen eines virtuellen Windows Server-Computers im Azure Stack-Portal

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Sie können einen virtuellen Windows Server 2016-Computer im Azure Stack-Portal erstellen. Befolgen Sie die Schritte in diesem Artikel zum Erstellen und Verwenden eines virtuellen Computers.

> [!NOTE]  
> Die Screenshots in diesem Artikel werden entsprechend der Benutzeroberfläche aktualisiert, die mit der Azure Stack Version 1808 eingeführt wurde. 1808 bietet Unterstützung für die Verwendung von *verwalteten Datenträgern*, zusätzlich zu nicht verwalteten Datenträgern. Wenn Sie eine frühere Version verwenden, unterscheiden sich einige Abbildungen, wie die Datenträgerauswahl, von denen, die in diesem Artikel gezeigt werden.  


## <a name="sign-in-to-the-azure-stack-portal"></a>Anmelden beim Azure Stack-Portal

Melden Sie sich beim Azure Stack-Portal an. Die Adresse des Azure Stack-Portals hängt davon ab, mit welchem Azure Stack-Produkt Sie eine Verbindung herstellen:

* Verwenden Sie für das Azure Stack Development Kit (ASDK) die folgende Adresse: https://portal.local.azurestack.external.
* Rufen Sie bei einem integrierten Azure Stack-System die vom Azure Stack-Operator bereitgestellte URL auf.

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

1. Klicken Sie auf **+ Eine Ressource erstellen** > **Compute** > **Windows Server 2016 Datacenter – nutzungsbasierte Bezahlung** > **Erstellen**. Wenn Sie den Eintrag **Windows Server 2016 Datacenter – nutzungsbasierte Bezahlung** nicht sehen, wenden Sie sich an Ihren Azure Stack-Betreiber. Bitten Sie ihn, diesen dem Marketplace hinzuzufügen, wie im Artikel [Hinzufügen des VM-Images für Windows Server 2016 zum Azure Stack-Marketplace](../operator/azure-stack-create-and-publish-marketplace-item.md) beschrieben.

    ![Schritte zum Erstellen eines virtuellen Windows-Computers im Portal](media/azure-stack-quick-windows-portal/image01.png)
2. Geben Sie unter **Grundlagen** Werte für **Name**, **Benutzername** und **Kennwort** ein. Wählen Sie ein **Abonnement**aus. Erstellen Sie eine **Ressourcengruppe**, oder wählen Sie eine vorhandene aus, wählen Sie einen **Speicherort** aus, und klicken Sie anschließend auf **OK**.

    ![Grundeinstellungen konfigurieren](media/azure-stack-quick-windows-portal/image02.png)
3. Klicken Sie unter **Größe** auf **D1 Standard** und dann auf **Auswählen**.  
    ![Auswählen der Größe des virtuellen Computers](media/azure-stack-quick-windows-portal/image03.png)

4. Nehmen Sie auf der Seite **Einstellungen** sämtliche gewünschten Änderungen an den Standardwerten vor.
   - Ab der Azure Stack Version 1808 können Sie den **Speicher** konfigurieren. An dieser Stelle können Sie auswählen, *verwaltete Datenträger*  zu verwenden. Vor Version 1808 können nur nicht verwaltete Datenträger verwendet werden.  
   ![Konfigurieren der Einstellungen des virtuellen Computers](media/azure-stack-quick-windows-portal/image04.png)  
   Wenn Ihre Konfigurationen abgeschlossen sind, klicken Sie auf **OK**, um den Vorgang fortzusetzen.

5. Klicken Sie unter **Zusammenfassung** auf **OK**, um den virtuellen Computer zu erstellen.
    ![Anzeigen der Zusammenfassung und Erstellen des virtuellen Computers](media/azure-stack-quick-windows-portal/image05.png)

6. Klicken Sie zum Anzeigen Ihres neuen virtuellen Computers auf **Alle Ressourcen**, suchen Sie nach dem Namen des virtuellen Computers, und klicken Sie in den Suchergebnissen auf seinen Namen.
    ![Anzeigen des virtuellen Computers](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die Nutzung des virtuellen Computers abgeschlossen ist, löschen Sie den virtuellen Computer und seine Ressourcen. Wählen Sie hierzu auf der Seite des virtuellen Computers die Ressourcengruppe aus, und klicken Sie auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie einen einfachen virtuellen Windows Server-Computer bereitgestellt. Um weitere Informationen zu virtuellen Computern unter Azure Stack zu erhalten, fahren Sie mit [Considerations for Virtual Machines in Azure Stack](azure-stack-vm-considerations.md) (Überlegungen zu virtuellen Computern in Azure Stack) fort.
