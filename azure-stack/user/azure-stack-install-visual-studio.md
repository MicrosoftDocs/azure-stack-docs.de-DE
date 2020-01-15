---
title: Installieren von Visual Studio und Herstellen einer Verbindung mit Azure Stack Hub | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Visual Studio installieren und eine Verbindung mit Azure Stack Hub herstellen.
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
ms.date: 01/07/2020
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: e7254f5fb6a44a268c73ea988d2ba9ecd236d7f0
ms.sourcegitcommit: b9d520f3b7bc441d43d489e3e32f9b89601051e6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75727580"
---
# <a name="install-visual-studio-and-connect-to-azure-stack-hub"></a>Installieren von Visual Studio und Herstellen einer Verbindung mit Azure Stack Hub

*Anwendungsbereich: Integrierte Azure Stack Hub-Systeme und Azure Stack Development Kit*

Sie können Visual Studio zum Schreiben und Bereitstellen von [Azure Resource Manager-Vorlagen](azure-stack-arm-templates.md) für Azure Stack Hub verwenden. Die Schritte in diesem Artikel beschreiben die Installation von Visual Studio in [Azure Stack Hub](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) oder auf einem externen Computer, wenn Sie eine Verbindung mit Azure Stack Hub per [VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) herstellen möchten.

## <a name="install-visual-studio"></a>Installieren von Visual Studio

1. Laden Sie den [Webplattform-Installer](https://www.microsoft.com/web/downloads/platform.aspx) herunter, und führen Sie ihn aus.  

2. Öffnen Sie den **Microsoft-Webplattform-Installer**.

3. Suchen Sie nach **Visual Studio Community 2015 with Microsoft Azure SDK – 2.9.6**. Klicken Sie auf **Hinzufügen** und anschließend auf **Installieren**.

4. Deinstallieren Sie **Microsoft Azure PowerShell**, das als Teil des Azure SDK installiert ist.

    ![Screenshot der WebPI-Installationsschritte](./media/azure-stack-install-visual-studio/image1.png)

5. [Installieren von PowerShell für Azure Stack Hub](../operator/azure-stack-powershell-install.md)

6. Starten Sie nach Abschluss der Installation das Betriebssystem neu.

## <a name="connect-to-azure-stack-hub-with-azure-ad"></a>Herstellen einer Verbindung mit Azure Stack Hub über Azure AD

1. Starten Sie Visual Studio.

2. Wählen Sie im Menü **Ansicht** die Option **Cloud-Explorer** aus.

3. Wählen Sie im neuen Bereich die Option **Konto hinzufügen** aus, und melden Sie sich mit Ihren Azure Active Directory-Anmeldeinformationen (Azure AD) an.  

    ![Screenshot von Cloud-Explorer nach dem Anmelden und dem Herstellen einer Verbindung mit Azure Stack Hub](./media/azure-stack-install-visual-studio/image2.png)

Nach der Anmeldung können Sie [Vorlagen bereitstellen](azure-stack-deploy-template-visual-studio.md) oder die verfügbaren Ressourcentypen und Ressourcengruppen durchsuchen, um Ihre eigenen Vorlagen zu erstellen.  

## <a name="connect-to-azure-stack-hub-with-ad-fs"></a>Herstellen einer Verbindung mit Azure Stack Hub über AD FS

1. Starten Sie Visual Studio.

2. Wählen Sie unter **Tools** den Eintrag **Optionen** aus.

3. Erweitern Sie **Umgebung** im **Navigationsbereich**, und wählen Sie **Konten** aus.

4. Wählen Sie **Hinzufügen** aus, und geben Sie den Azure Resource Manager-Endpunkt des Benutzers ein. Für das Azure Stack Development Kit (ASDK) lautet die URL `https://management.local.azurestack/external`.  Für integrierte Azure Stack Hub-Systeme lautet die URL `https://management.[Region}.[External FQDN]`.

    ![Hinzufügen eines neuen Azure Cloud Discovery-Endpunkts](./media/azure-stack-install-visual-studio/image5.png)

5. Wählen Sie **Hinzufügen**.  

    Visual Studio ruft Azure Resource Manager auf und ermittelt die Endpunkte, einschließlich des Authentifizierungsendpunkts für Active Directory-Verbunddienste (AD FS).

    ![Screenshot von Cloud-Explorer nach dem Anmelden und dem Herstellen einer Verbindung mit Azure Stack Hub](./media/azure-stack-install-visual-studio/image6.png)

6. Wählen Sie im Menü **Ansicht** die Option **Cloud-Explorer** aus.

7. Wählen Sie **Konto hinzufügen** aus, und melden Sie sich mit Ihren AD FS-Anmeldeinformationen an.  

    ![Anmelden bei Visual Studio in Cloud-Explorer](./media/azure-stack-install-visual-studio/image7.png)

    Cloud-Explorer fragt die verfügbaren Abonnements ab. Sie können ein für die Verwaltung verfügbares Abonnement auswählen.

    ![Auswählen zu verwaltender Abonnements in Cloud-Explorer](./media/azure-stack-install-visual-studio/image8.png)

8. Durchsuchen Sie Ihre vorhandenen Ressourcen und Ressourcengruppen, oder stellen Sie Vorlagen bereit.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [parallele Installation mehrerer Visual Studio-Versionen](/visualstudio/install/install-visual-studio-versions-side-by-side).
- [Entwickeln von Vorlagen für Azure Stack Hub](azure-stack-develop-templates.md)
