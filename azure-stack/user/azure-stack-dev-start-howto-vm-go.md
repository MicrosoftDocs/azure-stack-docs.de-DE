---
title: Bereitstellen einer Go-Web-App auf einem virtuellen Computer in Azure Stack Hub
description: Bereitstellen einer Go-Web-App auf einer VM in Azure Stack Hub
author: mattbriggs
ms.topic: overview
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: a43dd6ccb766dfb7b67dad09c0fc9bba37d524b4
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525318"
---
# <a name="deploy-a-go-web-app-to-a-vm-in-azure-stack-hub"></a>Bereitstellen einer Go-Web-App auf einer VM in Azure Stack Hub

Sie können einen virtuellen Computer erstellen, um eine Go-Web-App in Azure Stack Hub zu hosten. In diesem Artikel richten Sie einen Server ein, konfigurieren den Server zum Hosten Ihrer Go-Web-App und stellen die App dann unter Azure Stack Hub bereit.

## <a name="create-a-vm"></a>Erstellen einer VM

1. Richten Sie Ihre VM in Azure Stack Hub ein, indem Sie die Anleitung unter [Bereitstellen einer Linux-VM zum Hosten einer Web-App in Azure Stack Hub](azure-stack-dev-start-howto-deploy-linux.md) befolgen.

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

- Weitere Informationen zum [Entwickeln für Azure Stack Hub](azure-stack-dev-start.md)
- Weitere Informationen zu [häufigen Bereitstellungen für Azure Stack Hub als IaaS](azure-stack-dev-start-deploy-app.md)
- Besuchen Sie die Website [Golang.org](https://golang.org), um die Programmiersprache Go zu erlernen und auf weitere Go-Ressourcen zuzugreifen.
