---
title: Bereitstellen einer Ruby-App auf einem virtuellen Computer in Azure Stack | Microsoft-Dokumentation
description: Bereitstellen einer Ruby-App auf einem virtuellen Computer in Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 7744d1adcdcb1dde53c6ef887498a9a3978f4513
ms.sourcegitcommit: 41927cb812e6a705d8e414c5f605654da1fc6952
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/25/2019
ms.locfileid: "64482296"
---
# <a name="how-to-deploy-a-ruby-web-app-to-a-vm-in-azure-stack"></a>Bereitstellen einer Ruby-Web-App auf einer VM in Azure Stack

Sie können eine VM erstellen, um Ihre Ruby-Web-App in Azure Stack zu hosten. In diesem Artikel finden Sie die Schritte, die Sie ausführen müssen, um einen Server einzurichten, den Server für das Hosten Ihrer Ruby-Web-App zu konfigurieren und dann Ihre App bereitzustellen.

Bei Ruby liegt der Fokus auf Ausgewogenheit. Der Entwickler von Ruby, Yukihiro „Matz“ Matsumoto, mischte Teile seiner bevorzugten Sprachen (Perl, Smalltalk, Eiffel, Ada und Lisp), um eine neue Sprache zu formen, um ein Gleichgewicht zwischen funktionaler und imperativer Programmierung herzustellen. Besuchen Sie die Website [Ruby-lang.org](https://www.ruby-lang.org), um die Programmiersprache Ruby zu erlernen und weitere Ressourcen für Ruby zu finden.

Dieser Artikel verwendet Ruby und ein Ruby on Rails-Framework.

## <a name="create-a-vm"></a>Erstellen einer VM

1. Legen Sie das Setup Ihrer VM in Azure Stack fest. Führen Sie die Schritte unter [Bereitstellen einer Linux-VM zum Hosten einer Web-App in Azure Stack](azure-stack-dev-start-howto-deploy-linux.md) aus.

2. Stellen Sie auf dem Blatt für das VM-Netzwerk sicher, dass auf die folgenden Ports zugegriffen werden kann:

    | Port | Protocol | BESCHREIBUNG |
    | --- | --- | --- |
    | 80 | HTTP | Das Hypertext Transfer-Protokoll (HTTP) ist ein Anwendungsprotokoll für verteilte, zusammenarbeitsorientierte Hypermedia-Informationssysteme. Clients stellen die Verbindung zu Ihrer Web-App entweder über die öffentliche IP-Adresse oder über den DNS-Namen Ihrer VM her. |
    | 443 | HTTPS | Das Hypertext Transfer-Protokoll Secure (HTTPS) ist eine Erweiterung des Hypertext Transfer-Protokolls (HTTP). Es wird zur sicheren Kommunikation in einem Computernetzwerk verwendet. Clients stellen die Verbindung zu Ihrer Web-App entweder über die öffentliche IP-Adresse oder über den DNS-Namen Ihrer VM her. |
    | 22 | SSH | Secure Shell (SSH) ist ein kryptografisches Netzwerkprotokoll für den sicheren Betrieb von Netzwerkdiensten in einem nicht gesicherten Netzwerk. Sie verwenden diese Verbindung mit einem SSH-Client, um die VM zu konfigurieren und die App bereitzustellen. |
    | 3389 | RDP | Optional. Das Remotedesktopprotokoll ermöglicht eine Remotedesktopverbindung, um eine grafische Benutzeroberfläche auf Ihrem Computer zu verwenden.   |
    | 3000 | Benutzerdefiniert | Port 3000 wird während der Entwicklung vom Ruby on Rails-Webframework verwendet. Auf einem Produktionsserver sollten Sie den Datenverkehr über die Ports 80 und 443 leiten. |

## <a name="install-ruby"></a>Installieren von Ruby

1. Stellen Sie mit Ihrem SSH-Client eine Verbindung mit Ihrem virtuellen Computer her. Anleitungen finden Sie unter [Herstellen einer Verbindung über SSH mit PuTTY](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty).
1. Installieren Sie das PPA-Repository. Geben Sie an der Bash-Eingabeaufforderung auf Ihrer VM folgende Befehle ein:

    ```bash  
    sudo apt -y install software-properties-common
    sudo apt-add-repository ppa:brightbox/ruby-ng

    sudo apt update
    ```

2. Installieren Sie Ruby und Ruby on Rails auf Ihrer VM. Geben Sie in der gleichen SSH-Sitzung die folgenden Befehle auf Ihrer VM ein:

    ```bash  
    sudo apt install ruby
    gem install rails -v 4.2.6
    ```

3. Installieren Sie Ruby on Rails-Abhängigkeiten. Geben Sie in der gleichen SSH-Sitzung die folgenden Befehle auf Ihrer VM ein:

    ```bash  
    sudo apt-get install make
    sudo apt-get install gcc
    sudo apt-get install sqlite3
    sudo apt-get install nodejs
    sudo gem install sqlite
    sudo gem install bundler
    ```

    > [!Note]  
    > Bei der Installation müssen Sie möglicherweise `sudo gem install bundler` mehrmals ausführen. Wenn beim Versuch, Abhängigkeiten zu installieren, Fehler auftreten, sehen Sie sich die Fehlerprotokolle an und beheben die Probleme.

4. Überprüfen Sie die Installation. Geben Sie in der gleichen SSH-Sitzung die folgenden Befehle auf Ihrer VM ein:

    ```bash  
        ruby -v
    ```

3. Installieren Sie Git. [Git](https://git-scm.com) ist ein weit verbreitetes System für Versionskontrolle und Quellcodeverwaltung (Source Code Management, SCM). Geben Sie in der gleichen SSH-Sitzung die folgenden Befehle auf Ihrer VM ein:

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="create-and-run-an-app"></a>Erstellen und Ausführen einer App

1. Geben Sie in der gleichen SSH-Sitzung die folgenden Befehle auf Ihrer VM ein:

    ```bash
        rails new myapp
        cd myapp
        rails server -b 0.0.0.0 -p 3000
    ```

2.  Navigieren Sie jetzt zu Ihrem neuen Server – die ausgeführte Webanwendung sollte zu sehen sein.

    ```HTTP  
       http://yourhostname.cloudapp.net:3000
    ```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum [Entwickeln für Azure Stack](azure-stack-dev-start.md)
- Weitere Informationen zu [häufigen Bereitstellungen für Azure Stack als IaaS](azure-stack-dev-start-deploy-app.md)