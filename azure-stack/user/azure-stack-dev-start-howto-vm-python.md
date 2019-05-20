---
title: Bereitstellen einer Python-Web-App auf einem virtuellen Computer in Azure Stack | Microsoft-Dokumentation
description: Bereitstellen einer Python-Web-App auf einem virtuellen Computer in Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: b1a588dd084962c095a534f6569333b34e694bc6
ms.sourcegitcommit: 41927cb812e6a705d8e414c5f605654da1fc6952
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/25/2019
ms.locfileid: "64482254"
---
# <a name="how-to-deploy-a-python-web-app-to-a-vm-in-azure-stack"></a>Bereitstellen einer Python-Web-App auf einer VM in Azure Stack

Sie können eine VM erstellen, um Ihre Python-Web-App in Azure Stack zu hosten. In diesem Artikel finden Sie die Schritte, die Sie ausführen müssen, um einen Server einzurichten, den Server für das Hosten Ihrer Python-Web-App zu konfigurieren und dann Ihre App bereitzustellen.

Python ist eine interpretierte höhere Programmiersprache für allgemeine Zwecke. Python wurde von Guido van Rossum entwickelt und 1991 erstmals veröffentlicht. Python folgt der Entwurfsphilosophie, das Code lesbar sein muss. Dies wird insbesondere durch Verwendung von Leerräumen erreicht. Die Sprache bietet Konstrukte, die eine klar strukturierte Programmierung ermöglichen, unabhängig von der Menge an Code, die geschrieben wird. Besuchen Sie die Website [Python.org](https://www.python.org), um die Programmiersprache Python zu erlernen und weitere Ressourcen für Python zu finden.

In diesem Artikel wird Python 3.x mit Flask in einer virtuellen Umgebung auf einem Ngnix-Server verwendet.

## <a name="create-a-vm"></a>Erstellen einer VM

1. Legen Sie das Setup Ihrer VM in Azure Stack fest. Führen Sie die Schritte unter [Bereitstellen einer Linux-VM zum Hosten einer Web-App in Azure Stack](azure-stack-dev-start-howto-deploy-linux.md) aus.

2. Stellen Sie auf dem Blatt für das VM-Netzwerk sicher, dass auf die folgenden Ports zugegriffen werden kann:

    | Port | Protocol | BESCHREIBUNG |
    | --- | --- | --- |
    | 80 | HTTP | Das Hypertext Transfer-Protokoll (HTTP) ist ein Anwendungsprotokoll für verteilte, zusammenarbeitsorientierte Hypermedia-Informationssysteme. Clients stellen die Verbindung zu Ihrer Web-App entweder über die öffentliche IP-Adresse oder über den DNS-Namen Ihrer VM her. |
    | 443 | HTTPS | Das Hypertext Transfer-Protokoll Secure (HTTPS) ist eine Erweiterung des Hypertext Transfer-Protokolls (HTTP). Es wird zur sicheren Kommunikation in einem Computernetzwerk verwendet. Clients stellen die Verbindung zu Ihrer Web-App entweder über die öffentliche IP-Adresse oder über den DNS-Namen Ihrer VM her. |
    | 22 | SSH | Secure Shell (SSH) ist ein kryptografisches Netzwerkprotokoll für den sicheren Betrieb von Netzwerkdiensten in einem nicht gesicherten Netzwerk. Sie verwenden diese Verbindung mit einem SSH-Client, um die VM zu konfigurieren und die App bereitzustellen. |
    | 3389 | RDP | Optional. Das Remotedesktopprotokoll ermöglicht eine Remotedesktopverbindung, um eine grafische Benutzeroberfläche auf Ihrem Computer zu verwenden.   |
    | 5000, 8000 | Benutzerdefiniert | Die Ports 5000 und 8000 werden während der Entwicklung vom Flask-Webframework verwendet. Auf einem Produktionsserver sollten Sie den Datenverkehr über die Ports 80 und 443 leiten. |

## <a name="install-python"></a>Installieren von Python

1. Stellen Sie mit Ihrem SSH-Client eine Verbindung mit Ihrem virtuellen Computer her. Anleitungen finden Sie unter [Herstellen einer Verbindung über SSH mit PuTTY](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty).
2. Geben Sie an der Bash-Eingabeaufforderung auf Ihrer VM folgende Befehle ein:

    ```bash  
    sudo apt-get -y install python3 python3-venv python3-dev
    ```

3. Überprüfen Sie die Installation. Geben Sie in der gleichen SSH-Sitzung die folgenden Befehle auf Ihrer VM ein:

    ```bash  
        python -version
    ```


3. Installieren Sie NGINX. [NGINX](https://www.nginx.com/resources/wiki/) ist ein Webserver, der auch als Reverseproxy, Lastenausgleich, Mailproxy und HTTP-Cache verwendet werden kann. Geben Sie in der gleichen SSH-Sitzung die folgenden Befehle auf Ihrer VM ein:

    ```bash  
       sudo apt-get -y install nginx git
    ```

4. Installieren Sie Git. [Git](https://git-scm.com) ist ein weit verbreitetes System für Versionskontrolle und Quellcodeverwaltung (Source Code Management, SCM). Geben Sie in der gleichen SSH-Sitzung die folgenden Befehle auf Ihrer VM ein:

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="deploy-and-run-the-app"></a>Bereitstellen und Ausführen der App

1. Richten Sie Ihr Git-Repository auf der VM ein. Geben Sie in der gleichen SSH-Sitzung die folgenden Befehle auf Ihrer VM ein:

    ```bash  
       git clone https://github.com/mattbriggs/flask-hello-world.git
    
       cd flask-hello-world
    ```

2. Erstellen Sie eine virtuelle Umgebung, und füllen Sie sie mit allen Paketabhängigkeiten auf.  Geben Sie in der gleichen SSH-Sitzung die folgenden Befehle auf Ihrer VM ein:

    ```bash  
    python3 -m venv venv
    source venv/bin/activate
    pip install -r requirements.txt
    
    export FLASK_APP=application.py
    # export FLASK_DEBUG=1 
    flask run -h 0.0.0.0
    ```

3.  Navigieren Sie jetzt zu Ihrem neuen Server – die ausgeführte Webanwendung sollte zu sehen sein.

    ```HTTP  
       http://yourhostname.cloudapp.net:5000
    ```

## <a name="update-your-server"></a>Aktualisieren des Servers

1. Stellen Sie in der SSH-Sitzung eine Verbindung mit Ihrer VM her. Halten Sie den Server an, indem Sie `CTRL+C` eingeben.
2. Geben Sie die folgenden Befehle ein:

    ```bash  
    deactivate
    open the git repo
    git pull
    ```

3. Aktivieren Sie die virtuelle Umgebung, und starten Sie die App:

    ```bash  
    source venv/bin/activate
    export FLASK_APP=application.py
    flask run -h 0.0.0.0
    ```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum [Entwickeln für Azure Stack](azure-stack-dev-start.md)
- Weitere Informationen zu [häufigen Bereitstellungen für Azure Stack als IaaS](azure-stack-dev-start-deploy-app.md)
