---
title: Bereitstellen einer C#-Web-App (ASP.NET) auf einem virtuellen Computer in Azure Stack | Microsoft-Dokumentation
description: Bereitstellen einer C#-Web-App (ASP.NET) auf einer VM in Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 3f925d7c6a7f08257dbcb054044403e1488d38cb
ms.sourcegitcommit: 41927cb812e6a705d8e414c5f605654da1fc6952
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/25/2019
ms.locfileid: "64482398"
---
# <a name="how-to-deploy-a-c-aspnet-web-app-to-a-vm-in-azure-stack"></a>Bereitstellen einer C#-Web-App (ASP.NET) auf einer VM in Azure Stack

Sie können eine VM erstellen, um Ihre C#-Web-App (ASP.NET) in Azure Stack zu hosten. In diesem Artikel finden Sie die Schritte, die Sie ausführen müssen, um einen Server einzurichten, den Server für das Hosten Ihrer C#-Web-App (ASP.NET) zu konfigurieren und dann Ihre App direkt aus Visual Studio bereitzustellen.

C# ist eine Allzweck-Programmiersprache, die auf mehreren Paradigmen basiert und verschiedene Programmierungsdisziplinen in sich vereint: starke Typisierung, lexikalische Struktur, imperativ, deklarativ, funktional, generisch, objektorientiert und komponentenorientiert. Sehen Sie sich den [Leitfaden für C#](https://docs.microsoft.com/dotnet/csharp/) an, um die Programmiersprache C# zu erlernen und weitere Ressourcen für C# zu finden.

In diesem Artikel wird eine C# 6.0-App mit ASP.NET Core 2.2 auf einem Windows 2016-Server verwendet.

## <a name="create-a-vm"></a>Erstellen einer VM

1. Erstellen Sie eine [Windows Server-VM](azure-stack-quick-windows-portal.md).

2. Führen Sie das folgende Skript aus, um die Komponenten auf Ihrer VM zu installieren. Mit dem Skript wird Folgendes durchgeführt:
      - Installieren Sie IIS (mit Verwaltungskonsole).
      - Installieren Sie ASP.NET 4.6.

        ```PowerShell  
        # Install IIS (with Management Console)
        Install-WindowsFeature -name Web-Server -IncludeManagementTools
        
        # Install ASP.NET 4.6
        Install-WindowsFeature Web-Asp-Net45
        
        # Install Web Management Service
        Install-WindowsFeature -Name Web-Mgmt-Service
        ```

3. Laden Sie die [MSI-Datei für Web Deploy 3.6](https://www.microsoft.com/download/details.aspx?id=43717) herunter. Führen Sie die Installation über die MSI-Datei durch, und aktivieren Sie dann alle Features.

4. Installieren Sie das .NET Core Hosting Bundle für 2.2 auf Ihrem Server. Die erforderlichen Schritte finden Sie unter [.NET Core Installer](https://dotnet.microsoft.com/download/dotnet-core/2.2). Stellen Sie sicher, dass Sie auf dem Entwicklungscomputer und dem Zielserver dieselbe Version von .NET Core verwenden.

5. Kehren Sie zum Azure Stack-Portal zurück, und öffnen Sie die Ports in den Netzwerkeinstellungen für Ihre VM.

    1. Öffnen Sie das Azure Stack-Portal für Ihren Mandanten.
    2. Suchen Sie Ihre VM. Möglicherweise haben Sie die VM an Ihr Dashboard angeheftet. Sie können auch über das Feld **Ressourcen suchen** danach suchen.
    3. Wählen Sie **Netzwerk** aus.
    4. Wählen Sie bei der VM die Option **Regel für eingehenden Port hinzufügen** aus.
    1. Fügen Sie eine Eingangssicherheitsregel für die folgenden Ports hinzu:

    | Port | Protocol | BESCHREIBUNG |
    | --- | --- | --- |
    | 80 | HTTP | Das Hypertext Transfer-Protokoll (HTTP) ist ein Anwendungsprotokoll für verteilte, zusammenarbeitsorientierte Hypermedia-Informationssysteme. Clients stellen die Verbindung zu Ihrer Web-App entweder über die öffentliche IP-Adresse oder über den DNS-Namen Ihrer VM her. |
    | 443 | HTTPS | Das Hypertext Transfer-Protokoll Secure (HTTPS) ist eine Erweiterung des Hypertext Transfer-Protokolls (HTTP). Es wird zur sicheren Kommunikation in einem Computernetzwerk verwendet. Clients stellen die Verbindung zu Ihrer Web-App entweder über die öffentliche IP-Adresse oder über den DNS-Namen Ihrer VM her. |
    | 22 | SSH | Secure Shell (SSH) ist ein kryptografisches Netzwerkprotokoll für den sicheren Betrieb von Netzwerkdiensten in einem nicht gesicherten Netzwerk. Sie verwenden diese Verbindung mit einem SSH-Client, um die VM zu konfigurieren und die App bereitzustellen. |
    | 3389 | RDP | Optional. Das Remotedesktopprotokoll ermöglicht eine Remotedesktopverbindung, um eine grafische Benutzeroberfläche auf Ihrem Computer zu verwenden.   |

    Führen Sie für jeden Port Folgendes aus:

    1. Wählen Sie **Alle** für die Quelle aus.
    1. Geben Sie `*` als Quellportbereich ein.
    1. Wählen Sie **Alle** für das **Ziel** aus.
    1. Fügen Sie unter **Zielportbereich** den Port hinzu, den Sie öffnen möchten.
    1. Wählen Sie **Alle** für das **Protokoll** aus.
    1. Wählen Sie **Zulassen** für die **Aktion** aus.
    1. Übernehmen Sie den Standardwert für **Priorität**.
    1. Geben Sie einen **Namen** und eine **Beschreibung** ein, und fügen Sie einen Hinweis dazu ein, warum Sie diesen Port geöffnet haben.
    1. Klicken Sie auf „Hinzufügen“.

5.  Erstellen Sie in den Einstellungen für **Netzwerk** für Ihre VM in Azure Stack einen DNS-Namen für Ihren Server. Benutzer können über die URL eine Verbindung mit Ihrer Website herstellen.

    1. Öffnen Sie das Azure Stack-Portal für Ihren Mandanten.
    1. Suchen Sie Ihre VM. Möglicherweise haben Sie die VM an Ihr Dashboard angeheftet. Sie können auch über das Feld **Ressourcen suchen** danach suchen.
    1. Wählen Sie **Übersicht**.
    1. Wählen Sie unter „VM“ die Option **Konfigurieren** aus.
    1. Wählen Sie **Dynamisch** für die **Zuweisung** aus.
    1. Geben Sie eine Bezeichnung für den DNS-Namen ein, z.B. `mywebapp`, sodass Ihre vollständige URL in etwa so aussieht: `mywebapp.local.cloudapp.azurestack.external`.

## <a name="create-an-app"></a>Erstellen einer App 

Sie können entweder eine eigene Web-App oder das Beispiel unter [Veröffentlichen einer ASP.NET Core-App in Azure mit Visual Studio](https://docs.microsoft.com/aspnet/core/tutorials/razor-pages/razor-pages-start?view=aspnetcore-2.2&tabs=visual-studio
) verwenden.

Dieser Artikel beschreibt das Erstellen und Veröffentlichen einer ASP.NET-Webanwendung auf einem virtuellen Azure-Computer (VM) mithilfe des Veröffentlichungsfeatures für Microsoft Azure Virtual Machines in Visual Studio 2017. Nachdem Sie Ihre App installiert und sichergestellt haben, dass sie lokal ausgeführt wird, aktualisieren Sie das Veröffentlichungsziel in Ihre Windows-VM in Azure Stack.

## <a name="deploy-and-run-the-app"></a>Bereitstellen und Ausführen der App

Erstellen Sie ein Veröffentlichungsziel für Ihre VM in Azure Stack.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus.

    ![Bereitstellen einer ASP.NET-Web-App zur Veröffentlichung in Azure Stack](media/azure-stack-dev-start-howto-vm-dotnet/deploy-app-to-azure-stack.png)

2.  Wählen Sie im Fenster „Veröffentlichen“ die Option „Neues Profil“ aus.
3. Wählen Sie die gewünschten Werte für IIS, FTP, usw.
4. Klicken Sie auf „Veröffentlichen“.

5.  Wählen Sie **Web Deploy** als **Veröffentlichungsmethode** aus.
6.  Geben Sie unter **Server** den von Ihnen zuvor definierten DNS-Namen ein, z.B. `w21902.local.cloudapp.azurestack.external`.
7.  Geben Sie `Default Web Site` als **Websitenamen** ein.
8.  Geben Sie unter **Benutzername** den Benutzernamen für den Computer ein.
9.  Geben Sie unter **Kennwort** das Kennwort für den Computer ein.
10. Geben Sie unter **Ziel-URL** die URL für die Website ein, z.B. `mywebapp.local.cloudapp.azurestack.external`.

    ![Bereitstellen der ASP.NET-Web-App – Konfigurieren von Web Deploy](media/azure-stack-dev-start-howto-vm-dotnet/configure-web-deploy.png)

9. Wählen Sie **Verbindung überprüfen** aus, um Ihre Web Deploy-Bereitstellung zu überprüfen. Klicken Sie anschließend auf **Weiter**.
10. Legen Sie Ihre **Konfiguration** auf **Release** fest.
11. Legen Sie das **Zielframework** auf  **netcoreapp2.2** fest.
12. Legen Sie die **Zielruntime** als **Portierbar** fest.
13. Wählen Sie **Speichern** aus.
14. Wählen Sie **Veröffentlichen**.
15. Navigieren Sie zu Ihrem neuen Server. Die ausgeführte Webanwendung sollte angezeigt werden.

```HTTP  
    mywebapp.local.cloudapp.azurestack.external
```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum [Entwickeln für Azure Stack](azure-stack-dev-start.md)
- Weitere Informationen zu [häufigen Bereitstellungen für Azure Stack als IaaS](azure-stack-dev-start-deploy-app.md)