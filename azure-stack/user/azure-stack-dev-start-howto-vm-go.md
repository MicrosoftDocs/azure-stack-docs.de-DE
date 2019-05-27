---
title: Bereitstellen einer Go-Web-App auf einem virtuellen Computer in Azure Stack | Microsoft-Dokumentation
description: Bereitstellen einer Go-Web-App auf einer VM in Azure Stack
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 8b1f207929e018b27bb3f20db8d2b7d5abe46088
ms.sourcegitcommit: 05a16552569fae342896b6300514c656c1df3c4e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65838346"
---
# <a name="deploy-a-go-web-app-to-a-vm-in-azure-stack"></a>Bereitstellen einer Go-Web-App auf einer VM in Azure Stack

Sie können einen virtuellen Computer erstellen, um eine Go-Web-App in Azure Stack zu hosten. In diesem Artikel richten Sie einen Server ein, konfigurieren den Server zum Hosten Ihrer Go-Web-App und stellen die App dann unter Azure Stack bereit.

## <a name="create-a-vm"></a>Erstellen einer VM

1. Richten Sie Ihre VM in Azure Stack ein, indem Sie die Anleitung unter [Bereitstellen einer Linux-VM zum Hosten einer Web-App in Azure Stack](azure-stack-dev-start-howto-deploy-linux.md) befolgen.

2. Stellen Sie im Bereich für das VM-Netzwerk sicher, dass auf die folgenden Ports zugegriffen werden kann:

    | Port | Protocol | BESCHREIBUNG |
    | --- | --- | --- |
    | 80 | HTTP | Das Hypertext Transfer-Protokoll (HTTP) wird zum Bereitstellen von Webseiten über Server verwendet. Clients stellen eine Verbindung per HTTP über einen DNS-Namen oder eine IP-Adresse her. |
    | 443 | HTTPS | Das Hypertext Transfer-Protokoll Secure (HTTPS) ist eine sichere Version von HTTP, für die ein Sicherheitszertifikat benötigt wird und die die verschlüsselte Übertragung von Informationen ermöglicht. |
    | 22 | SSH | Secure Shell (SSH) ist ein verschlüsseltes Netzwerkprotokoll für die sichere Kommunikation. Sie verwenden diese Verbindung mit einem SSH-Client, um den virtuellen Computer zu konfigurieren und die App bereitzustellen. |
    | 3389 | RDP | Optional. Über das Remotedesktopprotokoll (RDP) kann für eine Remotedesktopverbindung eine grafische Benutzeroberfläche auf Ihrem Computer verwendet werden.   |
    | 3000 | Benutzerdefiniert | Port 3000 wird während der Entwicklung vom Go-Webframework verwendet. Auf einem Produktionsserver leiten Sie den Datenverkehr über die Ports 80 und 443. |

## <a name="install-go"></a>Installieren von Go

1. Stellen Sie mit Ihrem SSH-Client eine Verbindung mit Ihrem virtuellen Computer her. Anleitungen finden Sie unter [Herstellen einer Verbindung über SSH mit PuTTY](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

1. Geben Sie an der Bash-Eingabeaufforderung auf Ihrer VM folgende Befehle ein:

    ```bash  
    wget https://dl.google.com/go/go1.10.linux-amd64.tar.gz
    sudo tar -xvf go1.10.linux-amd64.tar.gz
    sudo mv go /usr/local
    ```

2. Richten Sie die Go-Umgebung auf Ihrer VM ein. Geben Sie bei bestehender Verbindung mit Ihrer VM in derselben SSH-Sitzung die folgenden Befehle ein:

    ```bash  
    export GOROOT=/usr/local/go
    export GOPATH=$HOME/Projects/ADMFactory/Golang
    export PATH=$GOPATH/bin:$GOROOT/bin:$PATH

    vi ~/.profile
    ```

3. Überprüfen Sie die Installation. Geben Sie bei bestehender Verbindung mit Ihrer VM in derselben SSH-Sitzung den folgenden Befehl ein:

    ```bash  
        go version
    ```

3. Führen Sie die [Installation von Git](https://git-scm.com) durch. Hierbei handelt es sich um ein weit verbreitetes System für die Versionskontrolle und Quellcodeverwaltung (Source Code Management, SCM). Geben Sie bei bestehender Verbindung mit Ihrer VM in derselben SSH-Sitzung den folgenden Befehl ein:

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="deploy-and-run-the-app"></a>Bereitstellen und Ausführen der App

1. Richten Sie Ihr Git-Repository auf der VM ein. Geben Sie bei bestehender Verbindung mit Ihrer VM in derselben SSH-Sitzung die folgenden Befehle ein:

    ```bash  
       git clone https://github.com/appleboy/go-hello
    
       cd go-hello
       go get -d
    ```

2. Starten Sie die App. Geben Sie bei bestehender Verbindung mit Ihrer VM in derselben SSH-Sitzung den folgenden Befehl ein:

    ```bash  
       go run hello-world.go
    ```

3. Navigieren Sie zu Ihrem neuen Server. Die ausgeführte Webanwendung sollte angezeigt werden.

    ```HTTP  
       http://yourhostname.cloudapp.net:3000
    ```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum [Entwickeln für Azure Stack](azure-stack-dev-start.md)
- Weitere Informationen zu [häufigen Bereitstellungen für Azure Stack als IaaS](azure-stack-dev-start-deploy-app.md)
- Besuchen Sie die Website [Golang.org](https://golang.org), um die Programmiersprache Go zu erlernen und auf weitere Go-Ressourcen zuzugreifen.
