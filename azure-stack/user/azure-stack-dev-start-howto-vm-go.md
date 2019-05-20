---
title: Bereitstellen einer GO-Web-App auf einem virtuellen Computer in Azure Stack | Microsoft-Dokumentation
description: Bereitstellen einer GO-Web-App auf einer VM in Azure Stack
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: c0cef076522e77a6d0fdafbd8848d5e9bb8a90a8
ms.sourcegitcommit: 41927cb812e6a705d8e414c5f605654da1fc6952
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/25/2019
ms.locfileid: "64482284"
---
# <a name="how-to-deploy-a-go-web-app-to-a-vm-in-azure-stack"></a>Bereitstellen einer GO-Web-App auf einer VM in Azure Stack

Sie können eine VM erstellen, um Ihre GO-Web-App in Azure Stack zu hosten. In diesem Artikel finden Sie die Schritte, die Sie ausführen müssen, um einen Server einzurichten, den Server für das Hosten Ihrer GO-Web-App zu konfigurieren und dann Ihre App bereitzustellen.

GO ist ausdrucksstark, präzise, klar und effizient. Dank der Parallelitätsmechanismen lassen sich Programme schreiben, die vernetzte Computer mit mehreren Kernen optimal nutzen. Das Typsystem ermöglicht flexible und modulare Programmkonstruktionen. Besuchen Sie die Website [Golang.org](https://golang.org), um die Programmiersprache GO zu erlernen und weitere Ressourcen für GO zu finden.

## <a name="create-a-vm"></a>Erstellen einer VM

1. Legen Sie das Setup Ihrer VM in Azure Stack fest. Führen Sie die Schritte unter [Bereitstellen einer Linux-VM zum Hosten einer Web-App in Azure Stack](azure-stack-dev-start-howto-deploy-linux.md) aus.

2. Stellen Sie auf dem Blatt für das VM-Netzwerk sicher, dass auf die folgenden Ports zugegriffen werden kann:

    | Port | Protocol | BESCHREIBUNG |
    | --- | --- | --- |
    | 80 | HTTP | Das Hypertext Transfer-Protokoll (HTTP) ist ein Anwendungsprotokoll für verteilte, zusammenarbeitsorientierte Hypermedia-Informationssysteme. Clients stellen die Verbindung zu Ihrer Web-App entweder über die öffentliche IP-Adresse oder über den DNS-Namen Ihrer VM her. |
    | 443 | HTTPS | Das Hypertext Transfer-Protokoll Secure (HTTPS) ist eine Erweiterung des Hypertext Transfer-Protokolls (HTTP). Es wird zur sicheren Kommunikation in einem Computernetzwerk verwendet. Clients stellen die Verbindung zu Ihrer Web-App entweder über die öffentliche IP-Adresse oder über den DNS-Namen Ihrer VM her. |
    | 22 | SSH | Secure Shell (SSH) ist ein kryptografisches Netzwerkprotokoll für den sicheren Betrieb von Netzwerkdiensten in einem nicht gesicherten Netzwerk. Sie verwenden diese Verbindung mit einem SSH-Client, um die VM zu konfigurieren und die App bereitzustellen. |
    | 3389 | RDP | Optional. Das Remotedesktopprotokoll ermöglicht eine Remotedesktopverbindung, um eine grafische Benutzeroberfläche auf Ihrem Computer zu verwenden.   |
    | 3000 | Benutzerdefiniert | Port 3000 wird während der Entwicklung vom GO-Webframework verwendet. Auf einem Produktionsserver sollten Sie den Datenverkehr über die Ports 80 und 443 leiten. |

## <a name="install-go"></a>Installieren von GO

1. Stellen Sie mit Ihrem SSH-Client eine Verbindung mit Ihrem virtuellen Computer her. Anleitungen finden Sie unter [Herstellen einer Verbindung über SSH mit PuTTY](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty).
1. Geben Sie an der Bash-Eingabeaufforderung auf Ihrer VM folgende Befehle ein:

    ```bash  
    wget https://dl.google.com/go/go1.10.linux-amd64.tar.gz
    sudo tar -xvf go1.10.linux-amd64.tar.gz
    sudo mv go /usr/local
    ```

2. Richten Sie die GO-Umgebung auf Ihrer VM ein. Geben Sie in der gleichen SSH-Sitzung die folgenden Befehle auf Ihrer VM ein:

    ```bash  
    export GOROOT=/usr/local/go
    export GOPATH=$HOME/Projects/ADMFactory/Golang
    export PATH=$GOPATH/bin:$GOROOT/bin:$PATH

    vi ~/.profile
    ```

3. Überprüfen Sie die Installation. Geben Sie in der gleichen SSH-Sitzung die folgenden Befehle auf Ihrer VM ein:

    ```bash  
        go version
    ```

3. Installieren Sie Git. [Git](https://git-scm.com) ist ein weit verbreitetes System für Versionskontrolle und Quellcodeverwaltung (Source Code Management, SCM). Geben Sie in der gleichen SSH-Sitzung die folgenden Befehle auf Ihrer VM ein:

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="deploy-and-run-the-app"></a>Bereitstellen und Ausführen der App

1. Richten Sie Ihr Git-Repository auf der VM ein. Geben Sie in der gleichen SSH-Sitzung die folgenden Befehle auf Ihrer VM ein:

    ```bash  
       git clone https://github.com/appleboy/go-hello
    
       cd go-hello
       go get -d
    ```

2. Starten Sie die App. Geben Sie in der gleichen SSH-Sitzung den folgenden Befehl auf Ihrer VM ein:

    ```bash  
       go run hello-world.go
    ```

3.  Navigieren Sie jetzt zu Ihrem neuen Server – die ausgeführte Webanwendung sollte zu sehen sein.

    ```HTTP  
       http://yourhostname.cloudapp.net:3000
    ```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum [Entwickeln für Azure Stack](azure-stack-dev-start.md)
- Weitere Informationen zu [häufigen Bereitstellungen für Azure Stack als IaaS](azure-stack-dev-start-deploy-app.md)