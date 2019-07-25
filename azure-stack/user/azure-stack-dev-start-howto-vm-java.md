---
title: Bereitstellen einer Java-Webanwendungsressource auf einem virtuellen Computer in Azure Stack | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie eine Java-Webanwendungsressource auf einem virtuellen Computer in Azure Stack bereitstellen.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 28d60e8fc5b575cd2fbefee1298220418e4f59a1
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418235"
---
# <a name="deploy-a-java-web-app-to-a-vm-in-azure-stack"></a>Bereitstellen einer Java-Web-App auf einer VM in Azure Stack

Sie können einen virtuellen Computer erstellen, um Ihre Python-Web-App in Azure Stack zu hosten. In diesem Artikel installieren und konfigurieren Sie einen Apache Tomcat-Server auf einer Linux-VM in Azure Stack. Anschließend laden Sie eine Java-Webanwendungsressource (WAR-Datei) auf den Server. Eine WAR-Datei wird zum Verteilen einer Sammlung mit Java-Archivdateien (JAR), komprimierten Dateien mit Java-Ressourcen, z. B. Klassen, Text, Bildern, XML- und HTML-Code, sowie anderen Ressourcen zum Bereitstellen einer Webanwendung verwendet.

## <a name="create-a-vm"></a>Erstellen einer VM

1. Richten Sie Ihre VM in Azure Stack ein, indem Sie die Anleitung unter [Bereitstellen einer Linux-VM zum Hosten einer Web-App in Azure Stack](azure-stack-dev-start-howto-deploy-linux.md) befolgen.

2. Stellen Sie im Bereich für das VM-Netzwerk sicher, dass auf die folgenden Ports zugegriffen werden kann:

    | Port | Protocol | BESCHREIBUNG |
    | --- | --- | --- |
    | 80 | HTTP | Das Hypertext Transfer-Protokoll (HTTP) wird zum Bereitstellen von Webseiten über Server verwendet. Clients stellen eine Verbindung per HTTP über einen DNS-Namen oder eine IP-Adresse her. |
    | 443 | HTTPS | Das Hypertext Transfer-Protokoll Secure (HTTPS) ist eine sichere Version von HTTP, für die ein Sicherheitszertifikat benötigt wird und die die verschlüsselte Übertragung von Informationen ermöglicht. |
    | 22 | SSH | Secure Shell (SSH) ist ein verschlüsseltes Netzwerkprotokoll für die sichere Kommunikation. Sie verwenden diese Verbindung mit einem SSH-Client, um den virtuellen Computer zu konfigurieren und die App bereitzustellen. |
    | 3389 | RDP | Optional. Über das Remotedesktopprotokoll (RDP) kann für eine Remotedesktopverbindung eine grafische Benutzeroberfläche auf Ihrem Computer verwendet werden.   |
    | 8080 | Benutzerdefiniert | Der Standardport für den Apache Tomcat-Dienst. Auf einem Produktionsserver leiten Sie den Datenverkehr über die Ports 80 und 443. |

## <a name="install-java"></a>Installieren von Java

1. Stellen Sie mit Ihrem SSH-Client eine Verbindung mit Ihrem virtuellen Computer her. Anleitungen finden Sie unter [Herstellen einer Verbindung über SSH mit PuTTY](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

2. Führen Sie an der Bash-Eingabeaufforderung auf Ihrer VM den folgenden Befehl aus:

    ```bash  
        sudo apt-get install default-jdk
    ```

3. Überprüfen Sie die Installation. Führen Sie bei bestehender Verbindung mit Ihrer VM in derselben SSH-Sitzung den folgenden Befehl aus:

    ```bash  
        java -version
    ```

## <a name="install-and-configure-tomcat"></a>Installieren und Konfigurieren von Tomcat

1. Stellen Sie mit Ihrem SSH-Client eine Verbindung mit Ihrem virtuellen Computer her. Anleitungen finden Sie unter [Herstellen einer Verbindung über SSH mit PuTTY](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

1. Erstellen Sie wie folgt einen Tomcat-Benutzer:

    a. Erstellen Sie eine neue Tomcat-Gruppe, indem Sie den folgenden Befehl ausführen:

    ```bash  
        sudo groupadd tomcat
    ```
     
    b. Erstellen Sie einen neuen Tomcat-Benutzer. Fügen Sie diesen Benutzer der Tomcat-Gruppe mit dem Basisverzeichnis */opt/tomcat* hinzu. Sie stellen Tomcat in diesem Verzeichnis bereit:

    ```bash  
        sudo useradd -s /bin/false -g tomcat -d /opt/tomcat tomcat
    ```

1. Installieren Sie Tomcat wie folgt:

    a. Rufen Sie die URL zum tar-Hilfsprogramm für die neueste Version von Tomcat 8 von der [Tomcat 8-Downloadseite](http://tomcat.apache.org/download-80.cgi) ab.

    b. Verwenden Sie cURL, um über diesen Link die neueste Version herunterzuladen. Führen Sie die folgenden Befehle aus:

    ```bash  
        cd /tmp 
        curl -O <URL for the tar for the latest version of Tomcat 8>
    ```

    c. Installieren Sie Tomcat im Verzeichnis */opt/tomcat*. Erstellen Sie den Ordner, und öffnen Sie anschließend das Archiv:

    ```bash  
        sudo mkdir /opt/tomcat
        sudo tar xzvf apache-tomcat-8*tar.gz -C /opt/tomcat --strip-components=1
        sudo chown -R tomcat webapps/ work/ temp/ logs/
    ```

1. Aktualisieren Sie die Berechtigungen für Tomcat, indem Sie die folgenden Befehle ausführen:

    ```bash  
        sudo chgrp -R tomcat /opt/tomcat
        sudo chmod -R g+r conf
        sudo chmod g+x conf
    ```

1. Erstellen Sie die Dienstdatei *systemd*, damit Sie Tomcat als Dienst ausführen können.

   a. Tomcat muss wissen, wo Sie Java installiert haben. Dieser Pfad wird im Allgemeinen als *JAVA_HOME* bezeichnet. Suchen Sie den Speicherort, indem Sie folgenden Befehl ausführen:

    ```bash  
        sudo update-java-alternatives -l
    ```

    Dies führt in etwa zu Folgendem:

    ```Text  
        Output
        java-1.8.0-openjdk-amd64       1081       /usr/lib/jvm/java-1.8.0-openjdk-amd64
    ```

    Sie können den Variablenwert *JAVA_HOME* erstellen, indem Sie den Pfad aus der Ausgabe verwenden und */jre* hinzufügen. Im vorherigen Beispiel ist dies beispielsweise */usr/lib/jvm/java-1.8.0-openjdk-amd64/jre*.

    b. Verwenden Sie den Wert von Ihrem Server, um die systemd-Dienstdatei zu erstellen:

    ```bash  
        sudo nano /etc/systemd/system/tomcat.service
    ```

    c. Kopieren Sie den folgenden Inhalt in Ihre Dienstdatei. Ändern Sie ggf. den Wert von *JAVA_HOME*, damit dieser dem Wert in Ihrem System entspricht. Unter Umständen müssen Sie auch die Einstellungen für die Arbeitsspeicherzuteilung ändern, die in CATALINA_OPTS angegeben sind:

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

    d. Speichern und schließen Sie die Datei.

    e. Laden Sie den systemd-Daemon erneut, damit dieser den Speicherort Ihrer Dienstdatei kennt:

    ```bash  
        sudo systemctl daemon-reload
    ```

    f. Starten Sie den Tomcat-Dienst: 

    ```bash  
        sudo systemctl start tomcat
    ```

    g. Überprüfen Sie, ob der Dienst ohne Fehler gestartet wurde, indem Sie Folgendes eingeben:

    ```bash  
        sudo systemctl status tomcat
    ```

1. Überprüfen Sie den Tomcat-Server. Tomcat verwendet Port 8080, um herkömmliche Anforderungen zu akzeptieren. Lassen Sie Datenverkehr zu diesem Port zu, indem Sie den folgenden Befehl ausführen:

    ```bash  
        sudo ufw allow 8080
    ```

    Wenn Sie die *Regeln für eingehende Ports* für Ihre Azure Stack-VM noch nicht hinzugefügt haben, sollten Sie dies jetzt nachholen. Weitere Informationen finden Sie unter [Erstellen einer VM](#create-a-vm).

1. Öffnen Sie einen Browser in demselben Netzwerk wie Ihre Azure Stack-Instanz, und öffnen Sie dann Ihren Server *yourmachine.local.cloudapp.azurestack.external:8080*.

    ![Apache Tomcat auf einer Azure Stack-VM](media/azure-stack-dev-start-howto-vm-java/apache-tomcat.png)

    Die Apache Tomcat-Seite auf Ihrem Server wird geladen. Als Nächstes konfigurieren Sie den Server so, dass Sie auf „Server Status“, „Manager“ und „Host Manager“ zugreifen können.

1. Aktivieren Sie die Dienstdatei, sodass Tomcat automatisch gestartet wird, wenn Sie den Server neu starten:

    ```bash  
        sudo systemctl enable tomcat
    ```

1. Konfigurieren Sie den Tomcat-Server so, dass Sie auf die Benutzeroberfläche der Webverwaltung zugreifen können. 

   a. Bearbeiten Sie die Datei *tomcat-users.xml*, und definieren Sie eine Rolle und einen Benutzer, damit Sie sich anmelden können. Definieren Sie den Benutzer, der Zugriff auf die `manager-gui` und die `admin-gui` erhalten soll.

    ```bash  
        sudo nano /opt/tomcat/conf/tomcat-users.xml
    ```

   b. Fügen Sie im Abschnitt `<tomcat-users>` folgende Elemente hinzu:

    ```XML  
        <role rolename="tomcat"/>
        <user username="<username>" password="<password>" roles="tomcat,manager-gui,admin-gui"/>
    ```

    Ihre endgültige Datei kann beispielsweise wie folgt aussehen:

    ```XML  
        <tomcat-users xmlns="http://tomcat.apache.org/xml"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://tomcat.apache.org/xml tomcat-users.xsd"
              version="1.0">
        <role rolename="tomcat"/>
        <user username="tomcatuser" password="changemepassword" roles="tomcat,manager-gui,admin-gui"/>
        </tomcat-users>
    ```

    c. Speichern und schließen Sie die Datei.

1. Tomcat beschränkt den Zugriff auf die Apps *Manager* und *Host Manager* auf vom Server eingehende Verbindungen. Da Sie Tomcat auf einer VM in Azure Stack installieren, kann es ratsam sein, diese Einschränkung zu entfernen. Ändern Sie die Einschränkungen in Bezug auf die IP-Adresse für diese Apps, indem Sie die entsprechenden *context.xml*-Dateien bearbeiten.

    a. Aktualisieren Sie *context.xml* in der Manager-App:

    ```bash  
        sudo nano /opt/tomcat/webapps/manager/META-INF/context.xml
    ```

    b. Kommentieren Sie die IP-Adresseinschränkung aus, um Verbindungen von einem beliebigen Ort aus zuzulassen, oder fügen Sie die IP-Adresse des Computers hinzu, den Sie zum Herstellen der Verbindung mit Tomcat verwenden.

    ```XML  
    <Context antiResourceLocking="false" privileged="true" >
        <!--<Valve className="org.apache.catalina.valves.RemoteAddrValve"
                allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />-->
    </Context>
    ```

    c. Speichern und schließen Sie die Datei.

    d. Aktualisieren Sie die Datei *context.xml* der Host Manager-App auf ähnliche Weise:

    ```bash  
        sudo nano /opt/tomcat/webapps/host-manager/META-INF/context.xml
    ```

    e. Speichern und schließen Sie die Datei.

1. Starten Sie den Tomcat-Dienst neu, um den Server mit den Änderungen zu aktualisieren:

    ```bash  
        sudo systemctl restart tomcat
    ```

1. Öffnen Sie einen Browser in demselben Netzwerk wie Ihre Azure Stack-Instanz, und öffnen Sie dann Ihren Server: *yourmachine.local.cloudapp.azurestack.external:8080*.

    a. Wählen Sie **Server Status**, um den Status des Tomcat-Servers zu überprüfen und sicherzustellen, dass Sie Zugriff haben.

    b. Melden Sie sich mit Ihren Tomcat-Anmeldeinformationen an.

    ![Apache Tomcat auf einer Azure Stack-VM](media/azure-stack-dev-start-howto-vm-java/apache-tomcat-management-app.png)

## <a name="create-an-app"></a>Erstellen einer App

Sie müssen eine Webanwendungsressource erstellen, um die Bereitstellung für Tomcat zu ermöglichen. Wenn Sie nur Ihre Umgebung prüfen möchten, können Sie eine WAR-Beispieldatei auf der [Apache Tomcat-Website](https://tomcat.apache.org/tomcat-6.0-doc/appdev/sample/) verwenden.

Anleitungen zum Entwickeln von Java-Apps in Azure finden Sie unter [Erstellen und Bereitstellen von Java-Apps in Azure](https://azure.microsoft.com/develop/java/).

## <a name="deploy-and-run-the-app"></a>Bereitstellen und Ausführen der App

1. Stellen Sie mit Ihrem SSH-Client eine Verbindung mit Ihrem virtuellen Computer her. Anleitungen finden Sie unter [Herstellen einer Verbindung über SSH mit PuTTY](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

1. Beenden Sie den Tomcat-Dienst, um den Server mit Ihrem App-Paket zu aktualisieren:

    ```bash  
        sudo systemctl stop tomcat
    ```

1. Fügen Sie Ihren FTP-Benutzer der Tomcat-Gruppe hinzu, um in den Ordner „webapps“ schreiben zu können. Der FTP-Benutzer ist der Benutzer, den Sie beim Erstellen Ihrer VM in Azure Stack definiert haben.

    ```bash  
        sudo usermod -a -G tomcat <VM-user>
    ```

1. Stellen Sie mit FileZilla eine Verbindung mit Ihrer VM her, um den Ordner „webapps“ zu leeren und dann Ihre neue oder aktualisierte WAR-Datei hochzuladen. Eine Anleitung finden Sie unter [Herstellen einer SFTP-Verbindung über FileZilla](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-sftp-with-filezilla).

    a. Löschen Sie den Inhalt von *TOMCAT_HOME/webapps*.

    b. Fügen Sie Ihre Webanwendungsressource unter *TOMCAT_HOME/webapps* hinzu (z. B. */opt/tomcat/webapps/* ).

1.  Tomcat erweitert die Anwendung automatisch und stellt sie bereit. Sie können die Anwendung mit dem DNS-Namen anzeigen, den Sie zuvor erstellt haben. Beispiel:

    ```HTTP  
       http://yourmachine.local.cloudapp.azurestack.external:8080/sample
    ```
    
## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum [Entwickeln für Azure Stack](azure-stack-dev-start.md)
- Weitere Informationen zu [häufigen Bereitstellungen für Azure Stack als IaaS](azure-stack-dev-start-deploy-app.md)
- Besuchen Sie die Website [Java.com](https://www.java.com), um die Programmiersprache Java zu erlernen und weitere Ressourcen für Java zu finden.
