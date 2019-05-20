---
title: Bereitstellen einer Java-Webanwendungsressource auf einem virtuellen Computer in Azure Stack | Microsoft-Dokumentation
description: Bereitstellen einer Java-Webanwendungsressource auf einem virtuellen Computer in Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: e788be6315078fccee020fefe6ad79a20485c382
ms.sourcegitcommit: 41927cb812e6a705d8e414c5f605654da1fc6952
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/25/2019
ms.locfileid: "64482386"
---
# <a name="how-to-deploy-a-java-web-app-to-a-vm-in-azure-stack"></a>Bereitstellen einer Java-Web-App auf einer VM in Azure Stack

Sie können eine VM erstellen, um Ihre Python-Web-App in Azure Stack zu hosten. In diesem Artikel finden Sie die Schritte, die Sie ausführen müssen, um einen Server einzurichten, den Server für das Hosten Ihrer Python-Web-App zu konfigurieren und dann Ihre App bereitzustellen.

Java ist eine Allzweck-Programmiersprache für parallelen, klassenbasierten und objektorientierten Programmcode, die darauf ausgelegt ist, dass möglichst wenige Implementierungsabhängigkeiten entstehen. Sie folgt dem Prinzip „einmal schreiben, überall ausführen“, was bedeutet, dass kompilierter Java-Code auf allen Plattformen ausgeführt werden kann, die Java unterstützen, ohne dass der Code erneut kompiliert werden muss. Besuchen Sie die Website [Java.com](https://www.java.com), um die Programmiersprache Java zu erlernen und weitere Ressourcen für Java zu finden.

Dieser Artikel erläutert Schritt für Schritt, wie Sie einen Apache Tomcat-Servers auf einer Linux-VM in Azure Stack installieren und konfigurieren und anschließend eine Java-Webanwendungsressource (WAR-Datei) auf den Server laden. Eine WAR-Datei wird verwendet, um eine Sammlung von JAR-Dateien, JavaServer Pages, Java-Servlets, Java-Klassen, XML-Dateien, Tagbibliotheken, statischen Webseiten (HTML und zugehörige Dateien) sowie weitere Ressourcen zu verteilen, die zusammen eine Webanwendung bilden.

Apache Tomcat – häufig als Tomcat Server bezeichnet – ist ein von der Apache Software Foundation entwickelter Open-Source-Container für Java-Servlets. Tomcat implementiert verschiedene Java EE-Spezifikationen wie Java-Servlets, JavaServer Pages, Java EL und WebSocket und bietet eine nur aus Java bestehende HTTP-Webserverumgebung zum Ausführen von Java-Code.

## <a name="create-a-vm"></a>Erstellen einer VM

1. Legen Sie das Setup Ihrer VM in Azure Stack fest. Führen Sie die Schritte unter [Bereitstellen einer Linux-VM zum Hosten einer Web-App in Azure Stack](azure-stack-dev-start-howto-deploy-linux.md) aus.

2. Stellen Sie auf dem Blatt für das VM-Netzwerk sicher, dass auf die folgenden Ports zugegriffen werden kann:

    | Port | Protocol | BESCHREIBUNG |
    | --- | --- | --- |
    | 80 | HTTP | Das Hypertext Transfer-Protokoll (HTTP) ist ein Anwendungsprotokoll für verteilte, zusammenarbeitsorientierte Hypermedia-Informationssysteme. Clients stellen die Verbindung zu Ihrer Web-App entweder über die öffentliche IP-Adresse oder über den DNS-Namen Ihrer VM her. |
    | 443 | HTTPS | Das Hypertext Transfer-Protokoll Secure (HTTPS) ist eine Erweiterung des Hypertext Transfer-Protokolls (HTTP). Es wird zur sicheren Kommunikation in einem Computernetzwerk verwendet. Clients stellen die Verbindung zu Ihrer Web-App entweder über die öffentliche IP-Adresse oder über den DNS-Namen Ihrer VM her. |
    | 22 | SSH | Secure Shell (SSH) ist ein kryptografisches Netzwerkprotokoll für den sicheren Betrieb von Netzwerkdiensten in einem nicht gesicherten Netzwerk. Sie verwenden diese Verbindung mit einem SSH-Client, um die VM zu konfigurieren und die App bereitzustellen. |
    | 3389 | RDP | Optional. Das Remotedesktopprotokoll ermöglicht eine Remotedesktopverbindung, um eine grafische Benutzeroberfläche auf Ihrem Computer zu verwenden.   |
    | 8080 | Benutzerdefiniert | Der Standardport für den Apache Tomcat-Dienst ist 8080. Auf einem Produktionsserver sollten Sie den Datenverkehr über die Ports 80 und 443 leiten. |

## <a name="install-java"></a>Installieren von Java

1. Stellen Sie mit Ihrem SSH-Client eine Verbindung mit Ihrem virtuellen Computer her. Anleitungen finden Sie unter [Herstellen einer Verbindung über SSH mit PuTTY](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty).
2. Geben Sie an der Bash-Eingabeaufforderung auf Ihrer VM folgende Befehle ein:

    ```bash  
        sudo apt-get install default-jdk
    ```

3. Überprüfen Sie die Installation. Geben Sie in der gleichen SSH-Sitzung die folgenden Befehle auf Ihrer VM ein:

    ```bash  
        java -version
    ```

## <a name="install-and-configure-tomcat"></a>Installieren und Konfigurieren von Tomcat

1. Stellen Sie mit Ihrem SSH-Client eine Verbindung mit Ihrem virtuellen Computer her. Anleitungen finden Sie unter [Herstellen einer Verbindung über SSH mit PuTTY](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty).

2. Erstellen Sie einen Tomcat-Benutzer.
    - Erstellen Sie zunächst eine neue Tomcat-Gruppe:
        ```bash  
            sudo groupadd tomcat
        ```
     
    - Erstellen Sie danach einen neuen Tomcat-Benutzer und fügen diesen als Mitglied zur Tomcat-Gruppe hinzu. Verwenden Sie `/opt/tomcat` als Stammverzeichnis – das Verzeichnis, in dem Sie Tomcat installieren möchten – und die Shell `/bin/false` (damit sich niemand beim Konto anmelden kann):
        ```bash  
            sudo useradd -s /bin/false -g tomcat -d /opt/tomcat tomcat
        ```

3. Installieren Sie Tomcat.
    - Rufen Sie zuerst die URL zum tar-Hilfsprogramm für die neueste Version von Tomcat 8 von der Tomcat 8-Downloadseite ab: [http://tomcat.apache.org/download-80.cgi](http://tomcat.apache.org/download-80.cgi).
    - Verwenden Sie dann den Befehl „curl“, um über diesen Link die neueste Version herunterzuladen.

        ```bash  
            cd /tmp 
            curl -O <URL for the tar for the latest version of Tomcat 8>
        ```

    - Danach installieren Sie Tomcat im Verzeichnis `/opt/tomcat`. Erstellen Sie das Verzeichnis, und extrahieren Sie dann das Archiv mithilfe der folgenden Befehle:

        ```bash  
            sudo mkdir /opt/tomcat
            sudo tar xzvf apache-tomcat-8*tar.gz -C /opt/tomcat --strip-components=1
            sudo chown -R tomcat webapps/ work/ temp/ logs/
        ```

4. Aktualisieren Sie die Berechtigungen für Tomcat.

    ```bash  
        sudo chgrp -R tomcat /opt/tomcat
        sudo chmod -R g+r conf
        sudo chmod g+x conf
    ```

5. Erstellen Sie eine `systemd`-Dienstdatei, um Tomcat als Dienst ausführen zu können.

    - Tomcat muss wissen, wo Java installiert ist. Dieser Pfad wird im Allgemeinen als **JAVA_HOME** bezeichnet. Suchen Sie den Speicherort, indem Sie folgenden Befehl ausführen:

        ```bash  
            sudo update-java-alternatives -l
        ```

        Es wird in etwa Folgendes ausgegeben:

        ```Text  
            Output
            java-1.8.0-openjdk-amd64       1081       /usr/lib/jvm/java-1.8.0-openjdk-amd64
        ```

        Um den Wert der Variablen **JAVA_HOME** zu konstruieren, verwenden Sie den Pfad aus der Ausgabe und fügen `/jre` hinzu. Beim oben genannten Beispiel sähe das so aus: `/usr/lib/jvm/java-1.8.0-openjdk-amd64/jre`

    - Verwenden Sie den Wert von Ihrem Server, um die systemd-Dienstdatei zu erstellen.

        ```bash  
            sudo nano /etc/systemd/system/tomcat.service
        ```

    - Kopieren Sie den folgenden Inhalt in Ihre Dienstdatei. Ändern Sie ggf. den Wert von **JAVA_HOME**, damit dieser dem Wert in Ihrem System entspricht. Möglicherweise müssen Sie auch die Einstellungen für die Arbeitsspeicherzuteilung ändern, die in CATALINA_OPTS angegeben sind:

        ```Text  
            [Unit]
            Description=Apache Tomcat Web Application Container
            After=network.target
            
            [Service]
            Type=forking
            
            Environment=JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-amd64/jre
            Environment=CATALINA_PID=/opt/tomcat/temp/tomcat.pid
            Environment=CATALINA_HOME=/opt/tomcat
            Environment=CATALINA_BASE=/opt/tomcat
            Environment='CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC'
            Environment='JAVA_OPTS=-Djava.awt.headless=true -Djava.security.egd=file:/dev/./urandom'
            
            ExecStart=/opt/tomcat/bin/startup.sh
            ExecStop=/opt/tomcat/bin/shutdown.sh
        
            User=tomcat
            Group=tomcat
            UMask=0007
            RestartSec=10
            Restart=always
            
            [Install]
            WantedBy=multi-user.target
        ```

    - Speichern und schließen Sie die Datei.
    - Laden Sie den systemd-Daemon erneut, damit dieser den Speicherort Ihrer Dienstdatei kennt:

        ```bash  
            sudo systemctl daemon-reload
        ```

    - Starten Sie den Tomcat-Dienst: 

        ```bash  
            sudo systemctl start tomcat
        ```

    - Überprüfen Sie, ob der Dienst ohne Fehler gestartet wurde, indem Sie Folgendes eingeben:

        ```bash  
            ssudo systemctl status tomcat
        ```

6. Überprüfen Sie den Tomcat-Server. Tomcat verwendet Port 8080, um herkömmliche Anforderungen zu akzeptieren. Lassen Sie Datenverkehr zu diesem Port zu, indem Sie den folgenden Befehl ausführen:

    ```bash  
        sudo ufw allow 8080
    ```

    Wenn Sie die **Regeln für eingehende Ports** für Ihre Azure Stack-VM noch nicht hinzugefügt haben, holen Sie dies jetzt nach. Weitere Informationen finden Sie unter [Erstellen einer VM](#create-a-vm).

7. Öffnen Sie einen Browser im gleichen Netzwerk wie Ihre Azure Stack-Instanz, und öffnen Sie Ihren Server: `yourmachine.local.cloudapp.azurestack.external:8080`.

    ![Apache Tomcat auf einer Azure Stack-VM](media/azure-stack-dev-start-howto-vm-java/apache-tomcat.png)

    Die Apache Tomcat-Seite auf Ihrem Server wird geladen. Als Nächstes konfigurieren Sie den Server so, dass Sie auf „Server Status“, „Manager“ und „Host Manager“ zugreifen können.

8. Aktivieren Sie die Dienstdatei, sodass Tomcat automatisch gestartet wird, wenn Sie den Server neu starten:

    ```bash  
        sudo systemctl enable tomcat
    ```

9. Konfigurieren Sie den Tomcat-Server so, dass Sie auf die Benutzeroberfläche der Webverwaltung zugreifen können. Bearbeiten Sie die Datei `tomcat-users.xml`, und definieren Sie eine Rolle und einen Benutzer, sodass Sie sich anmelden können. Definieren Sie den Benutzer, der Zugriff auf die `manager-gui` und die `admin-gui` erhalten soll.

    ```bash  
        sudo nano /opt/tomcat/conf/tomcat-users.xml
    ```

    - Fügen Sie im Abschnitt `<tomcat-users>` folgende Elemente hinzu:

    ```XML  
        <role rolename="tomcat"/>
        <user username="<username>" password="<password>" roles="tomcat,manager-gui,admin-gui"/>
    ```

    - Ihre endgültige Datei könnte beispielsweise folgendermaßen aussehen:

    ```XML  
        <tomcat-users xmlns="http://tomcat.apache.org/xml"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://tomcat.apache.org/xml tomcat-users.xsd"
              version="1.0">
         <role rolename="tomcat"/>
        <user username="tomcatuser" password="changemepassword" roles="tomcat,manager-gui,admin-gui"/>
        </tomcat-users>
    ```

    - Speichern und schließen Sie die Datei.


10. Tomcat beschränkt den Zugriff auf die Apps **Manager** und **Host Manager** auf vom Server eingehende Verbindungen. Da Sie Tomcat auf einer VM in Azure Stack installieren, müssen Sie diese Beschränkung entfernen. Ändern Sie die Einschränkungen in diesen Apps hinsichtlich der IP-Adresse, indem Sie die entsprechenden `context.xml`-Dateien bearbeiten.

    - Aktualisieren Sie die `context.xml`-Datei der Manager-App:

        ```bash  
            sudo nano /opt/tomcat/webapps/manager/META-INF/context.xml
        ```

    - Kommentieren Sie die IP-Adresseinschränkung aus, um Verbindungen von einem beliebigen Ort aus zuzulassen, oder fügen Sie die IP-Adresse des Computers hinzu, den Sie zum Herstellen der Verbindung mit Tomcat verwenden.

        ```XML  
        <Context antiResourceLocking="false" privileged="true" >
          <!--<Valve className="org.apache.catalina.valves.RemoteAddrValve"
                 allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />-->
        </Context>
        ```

    - Speichern und schließen Sie die Datei.

    - Aktualisieren Sie die `context.xml`-Datei der Host Manager-App auf ähnliche Weise:

        ```bash  
            sudo nano /opt/tomcat/webapps/host-manager/META-INF/context.xml
        ```

    - Speichern und schließen Sie die Datei.

11. Starten Sie den Tomcat-Dienst neu, um den Server mit den Änderungen zu aktualisieren:

    ```bash  
        sudo systemctl restart tomcat
    ```

12. Öffnen Sie einen Browser im gleichen Netzwerk wie Ihre Azure Stack-Instanz, und öffnen Sie Ihren Server: `yourmachine.local.cloudapp.azurestack.external:8080`.

    - Wählen Sie „Server Status“ aus, um den Status des Tomcat-Servers zu überprüfen und sicherzustellen, dass Sie Zugriff haben.
    - Melden Sie sich mit Ihren Tomcat-Anmeldeinformationen an.

![Apache Tomcat auf einer Azure Stack-VM](media/azure-stack-dev-start-howto-vm-java/apache-tomcat-management-app.png)

## <a name="create-an-app"></a>Erstellen einer App

Sie müssen eine Webanwendungsressource erstellen, um die Bereitstellung auf Tomcat zu ermöglichen. Wenn Sie nur Ihre Umgebung prüfen möchten, finden Sie eine WAR-Beispieldatei auf der Apache Tomcat-Website: [Beispiel-App](https://tomcat.apache.org/tomcat-6.0-doc/appdev/sample/).

Anleitungen zum Entwickeln von Java-Apps in Azure finden Sie unter [Erstellen und Bereitstellen von Java-Apps in Azure](https://azure.microsoft.com/develop/java/).

## <a name="deploy-and-run-the-app"></a>Bereitstellen und Ausführen der App

1. Stellen Sie mit Ihrem SSH-Client eine Verbindung mit Ihrem virtuellen Computer her. Anleitungen finden Sie unter [Herstellen einer Verbindung über SSH mit PuTTY](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty).
1. Starten Sie den Tomcat-Dienst neu, um den Server mit Ihrem App-Paket zu aktualisieren:

    ```bash  
        sudo systemctl stop tomcat
    ```

2.  Fügen Sie Ihren FTP-Benutzer zur Tomcat-Gruppe hinzu, um in den webapps-Ordner schreiben zu können. Der FTP-Benutzer ist der Benutzer, den Sie beim Erstellen der VM in Azure Stack definiert haben.

    ```bash  
        sudo usermod -a -G tomcat <VM-user>
    ```

3. Stellen Sie eine Verbindung zwischen Ihrer VM und FileZilla her, um den webapps-Ordner zu leeren und dann Ihre neue oder aktualisierte WAR-Datei hochzuladen. Anleitungen zum Verwenden von FileZilla finden Sie unter [Herstellen einer Verbindung mit FileZilla über SFTP](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-sftp-with-filezilla).
    - Leeren Sie den Ordner `TOMCAT_HOME/webapps`.
    - Fügen Sie Ihre WAR-Datei zu ` TOMCAT_HOME/webapps` hinzu, z.B. `/opt/tomcat/webapps/`.

4.  Tomcat erweitert die Anwendung automatisch und stellt sie bereit. Sie können die Anwendung mit dem DNS-Namen anzeigen, den Sie zuvor erstellt haben. Beispiel: 

    ```HTTP  
       http://yourmachine.local.cloudapp.azurestack.external:8080/sample

## Next steps

- Learn more about how to [Develop for Azure Stack](azure-stack-dev-start.md)
- Learn about [common deployments for Azure Stack as IaaS](azure-stack-dev-start-deploy-app.md).