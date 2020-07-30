---
title: Bereitstellen einer Python-Web-App auf einem virtuellen Computer in Azure Stack Hub
description: Stellen Sie eine Python-Web-App auf einem virtuellen Computer in Azure Stack Hub bereit.
author: mattbriggs
ms.topic: overview
ms.date: 07/24/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 3/12/2020
ms.openlocfilehash: c72b08ca3e847a44a1270fb2b8e670a0b30a0929
ms.sourcegitcommit: b2337a9309c52aac9f5a1ffd89f1426d6c178ad5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87250927"
---
# <a name="deploy-a-python-web-app-to-a-vm-in-azure-stack-hub"></a>Bereitstellen einer Python-Web-App auf einer VM in Azure Stack Hub

Sie können eine VM erstellen, um Ihre Python-Web-App in Azure Stack Hub zu hosten. In diesem Artikel richten Sie einen Server ein, konfigurieren den Server zum Hosten Ihrer Python-Web-App und stellen die App dann unter Azure Stack Hub bereit.

In diesem Artikel wird Python 3.x mit Flask in einer virtuellen Umgebung auf einem Ngnix-Server verwendet.

## <a name="create-a-vm"></a>Erstellen einer VM

1. Richten Sie Ihre VM in Azure Stack Hub ein, indem Sie die Anleitung unter [Bereitstellen einer Linux-VM zum Hosten einer Web-App in Azure Stack Hub](azure-stack-dev-start-howto-deploy-linux.md) befolgen.

2. Stellen Sie im Bereich für das VM-Netzwerk sicher, dass auf die folgenden Ports zugegriffen werden kann:

    | Port | Protocol | BESCHREIBUNG |
    | --- | --- | --- |
    | 80 | HTTP | Das Hypertext Transfer-Protokoll (HTTP) wird zum Bereitstellen von Webseiten über Server verwendet. Clients stellen eine Verbindung per HTTP über einen DNS-Namen oder eine IP-Adresse her. |
    | 443 | HTTPS | Das Hypertext Transfer-Protokoll Secure (HTTPS) ist eine sichere Version von HTTP, für die ein Sicherheitszertifikat benötigt wird und die die verschlüsselte Übertragung von Informationen ermöglicht. |
    | 22 | SSH | Secure Shell (SSH) ist ein verschlüsseltes Netzwerkprotokoll für die sichere Kommunikation. Sie verwenden diese Verbindung mit einem SSH-Client, um den virtuellen Computer zu konfigurieren und die App bereitzustellen. |
    | 3389 | RDP | Optional. Über das Remotedesktopprotokoll (RDP) kann für eine Remotedesktopverbindung eine grafische Benutzeroberfläche auf Ihrem Computer verwendet werden.   |
    | 5000, 8000 | Benutzerdefiniert | Die Ports, die während der Entwicklung vom Flask-Webframework verwendet werden. Auf einem Produktionsserver leiten Sie den Datenverkehr über die Ports 80 und 443. |

## <a name="install-python"></a>Installieren von Python

1. Stellen Sie mit Ihrem SSH-Client eine Verbindung mit Ihrem virtuellen Computer her. Anleitungen finden Sie unter [Herstellen einer Verbindung über SSH mit PuTTY](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).
2. Geben Sie an Ihrer Bash-Eingabeaufforderung auf Ihrer VM den folgenden Befehl ein:

    ```bash  
    sudo apt-get -y install python3 python3-venv python3-dev
    ```

3. Überprüfen Sie die Installation. Geben Sie bei bestehender Verbindung mit Ihrer VM in derselben SSH-Sitzung den folgenden Befehl ein:

    ```bash  
        python -version
    ```

3. Führen Sie die [Installation von Nginx](https://www.nginx.com/resources/wiki/) durch. Hierbei handelt es sich um einen einfachen Webserver. Geben Sie bei bestehender Verbindung mit Ihrer VM in derselben SSH-Sitzung den folgenden Befehl ein:

    ```bash  
       sudo apt-get -y install nginx git
    ```

4. Führen Sie die [Installation von Git](https://git-scm.com) durch. Hierbei handelt es sich um ein weit verbreitetes System für die Versionskontrolle und Quellcodeverwaltung (Source Code Management, SCM). Geben Sie bei bestehender Verbindung mit Ihrer VM in derselben SSH-Sitzung den folgenden Befehl ein:

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="deploy-and-run-the-app"></a>Bereitstellen und Ausführen der App

1. Richten Sie Ihr Git-Repository auf der VM ein. Geben Sie bei bestehender Verbindung mit Ihrer VM in derselben SSH-Sitzung die folgenden Befehle ein:

    ```bash  
       git clone https://github.com/Azure-Samples/azure-stack-hub-flask-hello-world.git
    
       cd azure-stack-hub-flask-hello-world
    ```

2. Erstellen Sie eine virtuelle Umgebung, und füllen Sie sie mit allen Paketabhängigkeiten auf. Geben Sie bei bestehender Verbindung mit Ihrer VM in derselben SSH-Sitzung die folgenden Befehle ein:

    ```bash  
    python3 -m venv venv
    source venv/bin/activate
    pip install -r requirements.txt
    
    export FLASK_APP=application.py
    # export FLASK_DEBUG=1 
    flask run -h 0.0.0.0
    ```

3. Navigieren Sie zu Ihrem neuen Server. Die ausgeführte Webanwendung sollte angezeigt werden.

    ```HTTP  
       http://yourhostname.cloudapp.net:5000
    ```

## <a name="update-your-server"></a>Aktualisieren des Servers

1. Stellen Sie in der SSH-Sitzung eine Verbindung mit Ihrer VM her. Halten Sie den Server an, indem Sie STRG+C eingeben.

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

- Weitere Informationen zum [Entwickeln für Azure Stack Hub](azure-stack-dev-start.md)
- Weitere Informationen zu [häufigen Bereitstellungen für Azure Stack Hub als IaaS](azure-stack-dev-start-deploy-app.md)
- Besuchen Sie die Website [Python.org](https://www.python.org), um die Programmiersprache Python zu erlernen und weitere Ressourcen für Python zu finden.
