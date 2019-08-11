---
title: Installieren von Visual Studio und Herstellen einer Verbindung mit Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Visual Studio installieren und eine Verbindung mit Azure Stack herstellen.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.custom: vs-azure
ms.workload: azure-vs
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/08/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 12f0b8d13ebaa7a31153dd89f65f27fd97294700
ms.sourcegitcommit: 637018771ac016b7d428174e88d4dcb131b54959
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68842944"
---
# <a name="install-visual-studio-and-connect-to-azure-stack"></a>Installieren von Visual Studio und Herstellen einer Verbindung mit Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Sie können Visual Studio zum Schreiben und Bereitstellen von [Azure Resource Manager-Vorlagen](azure-stack-arm-templates.md) für Azure Stack verwenden. Die Schritte in diesem Artikel beschreiben die Installation von Visual Studio in [Azure Stack](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) oder auf einem externen Computer, wenn Sie eine Verbindung mit Azure Stack per [VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) herstellen möchten.

## <a name="install-visual-studio"></a>Installieren von Visual Studio

1. Laden Sie den [Webplattform-Installer](https://www.microsoft.com/web/downloads/platform.aspx) herunter, und führen Sie ihn aus.  

2. Öffnen Sie den **Microsoft-Webplattform-Installer**.

3. Suchen Sie nach **Visual Studio Community 2015 with Microsoft Azure SDK – 2.9.6**. Klicken Sie auf **Hinzufügen** und anschließend auf **Installieren**.

4. Deinstallieren Sie **Microsoft Azure PowerShell**, das als Teil des Azure SDK installiert ist.

    ![Screenshot der WebPI-Installationsschritte](./media/azure-stack-install-visual-studio/image1.png)

5. [Installieren Sie PowerShell für Azure Stack](../operator/azure-stack-powershell-install.md).

6. Starten Sie nach Abschluss der Installation das Betriebssystem neu.

## <a name="connect-to-azure-stack-with-azure-ad"></a>Herstellen einer Verbindung mit Azure Stack über Azure AD

1. Starten Sie Visual Studio.

2. Wählen Sie im Menü **Ansicht** die Option **Cloud-Explorer** aus.

3. Wählen Sie im neuen Bereich die Option **Konto hinzufügen** aus, und melden Sie sich mit Ihren Azure Active Directory-Anmeldeinformationen (Azure AD) an.  

    ![Screenshot von Cloud-Explorer nach dem Anmelden und dem Herstellen einer Verbindung mit Azure Stack](./media/azure-stack-install-visual-studio/image2.png)

Nach der Anmeldung können Sie [Vorlagen bereitstellen](azure-stack-deploy-template-visual-studio.md) oder die verfügbaren Ressourcentypen und Ressourcengruppen durchsuchen, um Ihre eigenen Vorlagen zu erstellen.  

## <a name="connect-to-azure-stack-with-ad-fs"></a>Herstellen einer Verbindung mit Azure Stack über AD FS

1. Starten Sie Visual Studio.

2. Wählen Sie unter **Tools** den Eintrag **Optionen** aus.

3. Erweitern Sie **Umgebung** im **Navigationsbereich**, und wählen Sie **Konten** aus.

4. Wählen Sie **Hinzufügen** aus, und geben Sie den Azure Resource Manager-Endpunkt des Benutzers ein. Für das Azure Stack Development Kit (ASDK) lautet die URL `https://management.local.azurestack/external`.  Für integrierte Azure Stack-Systeme lautet die URL `https://management.[Region}.[External FQDN]`.

    ![Hinzufügen eines neuen Azure Cloud Discovery-Endpunkts](./media/azure-stack-install-visual-studio/image5.png)

5. Wählen Sie **Hinzufügen**.  

    Visual Studio ruft Azure Resource Manager auf und ermittelt die Endpunkte, einschließlich des Authentifizierungsendpunkts für Active Directory-Verbunddienste (AD FS).

    ![Screenshot von Cloud-Explorer nach dem Anmelden und dem Herstellen einer Verbindung mit Azure Stack](./media/azure-stack-install-visual-studio/image6.png)

6. Wählen Sie im Menü **Ansicht** die Option **Cloud-Explorer** aus.

7. Wählen Sie **Konto hinzufügen** aus, und melden Sie sich mit Ihren AD FS-Anmeldeinformationen an.  

    ![Anmelden bei Visual Studio in Cloud-Explorer](./media/azure-stack-install-visual-studio/image7.png)

    Cloud-Explorer fragt die verfügbaren Abonnements ab. Sie können ein für die Verwaltung verfügbares Abonnement auswählen.

    ![Auswählen zu verwaltender Abonnements in Cloud-Explorer](./media/azure-stack-install-visual-studio/image8.png)

8. Durchsuchen Sie Ihre vorhandenen Ressourcen und Ressourcengruppen, oder stellen Sie Vorlagen bereit.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [parallele Installation mehrerer Visual Studio-Versionen](/visualstudio/install/install-visual-studio-versions-side-by-side).
- [Entwickeln Sie Vorlagen für Azure Stack.](azure-stack-develop-templates.md)