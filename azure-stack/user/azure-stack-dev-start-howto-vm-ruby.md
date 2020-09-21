---
title: Bereitstellen einer Ruby-App auf einem virtuellen Computer in Azure Stack Hub
description: Hier wird beschrieben, wie Sie eine Ruby-App auf einem virtuellen Computer in Azure Stack Hub bereitstellen.
author: mattbriggs
ms.topic: overview
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: 7be8293180f03d9d82565aa27c74c8e8cbe7c9c1
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/15/2020
ms.locfileid: "90573971"
---
# <a name="deploy-a-ruby-web-app-to-a-vm-in-azure-stack-hub"></a>Bereitstellen einer Ruby-Web-App auf einer VM in Azure Stack Hub

Sie können eine VM erstellen, um Ihre Ruby-Web-App in Azure Stack Hub zu hosten. In diesem Artikel richten Sie einen Server ein, konfigurieren den Server zum Hosten Ihrer Ruby-Web-App und stellen die App dann unter Azure Stack Hub bereit.

In diesem Artikel werden Ruby und ein Ruby on Rails-Framework verwendet.

## <a name="create-a-vm"></a>Erstellen einer VM

1. Richten Sie Ihren virtuellen Computer in Azure Stack Hub ein. Eine Anleitung finden Sie unter [Bereitstellen einer Linux-VM zum Hosten einer Web-App in Azure Stack Hub](azure-stack-dev-start-howto-deploy-linux.md).

2. Stellen Sie auf dem Blatt für das VM-Netzwerk sicher, dass auf die folgenden Ports zugegriffen werden kann:

    | Port | Protocol | BESCHREIBUNG |
    | --- | --- | --- |
    | 80 | HTTP | Das Hypertext Transfer-Protokoll (HTTP) wird zum Bereitstellen von Webseiten über Server verwendet. Clients stellen eine Verbindung per HTTP über einen DNS-Namen oder eine IP-Adresse her. |
    | 443 | HTTPS | Das Hypertext Transfer-Protokoll Secure (HTTPS) ist eine sichere Version von HTTP, für die ein Sicherheitszertifikat benötigt wird und die die verschlüsselte Übertragung von Informationen ermöglicht. |
    | 22 | SSH | Secure Shell (SSH) ist ein verschlüsseltes Netzwerkprotokoll für die sichere Kommunikation. Sie verwenden diese Verbindung mit einem SSH-Client, um den virtuellen Computer zu konfigurieren und die App bereitzustellen. |
    | 3389 | RDP | Optional. Über das Remotedesktopprotokoll (RDP) kann für eine Remotedesktopverbindung eine grafische Benutzeroberfläche auf Ihrem Computer verwendet werden.   |
    | 3000 | Benutzerdefiniert | Der Port, der während der Entwicklung vom Ruby on Rails-Webframework verwendet wird. Auf einem Produktionsserver leiten Sie den Datenverkehr über die Ports 80 und 443. |

## <a name="install-ruby"></a>Installieren von Ruby

1. Stellen Sie mit Ihrem SSH-Client eine Verbindung mit Ihrem virtuellen Computer her. Anleitungen finden Sie unter [Herstellen einer Verbindung über SSH mit PuTTY](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

1. Installieren Sie das PPA-Repository. Geben Sie an der Bash-Eingabeaufforderung auf Ihrer VM folgende Befehle ein:

    ```bash  
    sudo apt -y install software-properties-common
    sudo apt-add-repository ppa:brightbox/ruby-ng

    sudo apt update
    ```

2. Installieren Sie Ruby und Ruby on Rails auf Ihrer VM. Geben Sie bei bestehender Verbindung mit Ihrer VM in derselben SSH-Sitzung die folgenden Befehle ein:

    ```bash  
    sudo apt install ruby
    gem install rails -v 4.2.6
    ```

3. Installieren Sie Ruby on Rails-Abhängigkeiten. Geben Sie bei bestehender Verbindung mit Ihrer VM in derselben SSH-Sitzung die folgenden Befehle ein:

    ```bash  
    sudo apt-get install make
    sudo apt-get install gcc
    sudo apt-get install sqlite3
    sudo apt-get install nodejs
    sudo gem install sqlite
    sudo gem install bundler
    ```

    > [!NOTE]  
    > Beim Installieren der Ruby on Rails-Abhängigkeiten müssen Sie `sudo gem install bundler` unter Umständen mehrmals ausführen. Wenn bei der Installation ein Fehler auftritt, sollten Sie die Fehlerprotokolle prüfen und die Probleme beheben.

4. Überprüfen Sie die Installation. Geben Sie bei bestehender Verbindung mit Ihrer VM in derselben SSH-Sitzung den folgenden Befehl ein:

    ```bash  
        ruby -v
    ```

3. Führen Sie die [Installation von Git](https://git-scm.com) durch. Hierbei handelt es sich um ein weit verbreitetes System für die Versionskontrolle und Quellcodeverwaltung (Source Code Management, SCM). Geben Sie bei bestehender Verbindung mit Ihrer VM in derselben SSH-Sitzung den folgenden Befehl ein:

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="create-and-run-an-app"></a>Erstellen und Ausführen einer App

1. Geben Sie bei bestehender Verbindung mit Ihrer VM in derselben SSH-Sitzung die folgenden Befehle ein:

    ```bash
        rails new myapp
        cd myapp
        rails server -b 0.0.0.0 -p 3000
    ```

2. Navigieren Sie zu Ihrem neuen Server. Die ausgeführte Webanwendung sollte angezeigt werden.

    ```HTTP  
       http://yourhostname.cloudapp.net:3000
    ```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum [Entwickeln für Azure Stack Hub](azure-stack-dev-start.md)
- Weitere Informationen zu [häufigen Bereitstellungen für Azure Stack Hub als IaaS](azure-stack-dev-start-deploy-app.md)
- Besuchen Sie die Website [Ruby-lang.org](https://www.ruby-lang.org), um die Programmiersprache Ruby zu erlernen und weitere Ressourcen für Ruby zu finden.
