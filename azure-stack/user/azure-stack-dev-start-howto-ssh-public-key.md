---
title: Verwenden eines öffentlichen SSH-Schlüssels mit Azure Stack | Microsoft-Dokumentation
description: Verwenden eines öffentlichen SSH-Schlüssels
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/25/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 0f4133052022963e1ed0457dd479a00bcc5bb749
ms.sourcegitcommit: 0d8ccf2a32b08ab9bcbe13d54c7c3dce2379757f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/25/2019
ms.locfileid: "64490060"
---
# <a name="how-to-use-an-ssh-public-key"></a>Verwenden eines öffentlichen SSH-Schlüssels

Möglicherweise müssen Sie ein Paar aus einem öffentlichem SSH-Schlüssel und einem privaten Schlüssel erstellen, um eine offene SSH-Verbindung zwischen Ihrem Entwicklungscomputer und der Server-VM in Azure Stack herzustellen, die Ihre Web-App hostet. In diesem Artikel werden die Schritte erläutert, die erforderlich sind, um Ihre Schlüssel abzurufen und diese Schlüssel zum Herstellen einer Verbindung mit Ihrem Server zu verwenden. Sie können einen SSH-Client verwenden, um eine Bash-Eingabeaufforderung auf dem Linux-Server zu öffnen. Sie können auch einen SFTP-Client verwenden, um Dateien auf den und vom Server zu verschieben.

## <a name="create-an-ssh-public-key-on-windows"></a>Erstellen eines öffentlichen SSH-Schlüssels auf Windows

In diesem Abschnitt verwenden Sie den Schlüsselgenerator von PuTTY, um ein Paar aus einem öffentlichem SSH-Schlüssel und einem privaten Schlüssel zu erstellen, das beim Erstellen einer sicheren Verbindung mit Linux-Computern in Ihrer Azure Stack-Instanz verwendet wird. PuTTY ist eine kostenlose Implementierung von SSH und Telnet für Windows- und Unix-Plattformen und bietet einen `xterm`-Terminalemulator.

1. [Laden Sie PuTTY für Windows herunter, und installieren Sie es](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

1. Öffnen Sie den PuTTY-Schlüsselgenerator.

1. Legen Sie die **Parameter** auf **RSA** fest.

1. Legen Sie die Anzahl von Bits in einem generierten Schlüssel auf `2048` fest.  

    ![Verwenden von PuTTY zum Generieren eines öffentlichen SSH-Schlüssels](media/azure-stack-dev-start-howto-ssh-public-key/001-putty-key-gen-start.png)

1. Wählen Sie **Generieren** aus. Generieren Sie im Bereich **Schlüssel** einige beliebige Zeichen, indem Sie den Cursor über den leeren Bereich bewegen.

1. Fügen Sie eine **Schlüsselpassphrase** hinzu, und bestätigen Sie diese im Feld **Bestätigen**. Notieren Sie sich die Passphrase.
    ![Verwenden von PuTTY zum Generieren eines öffentlichen SSH-Schlüssels](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-key-gen-result.png)

1. Wählen Sie **Öffentlichen Schlüssel speichern** aus, und speichern Sie den Schlüssel an einem Speicherort, an dem Sie darauf zugreifen können.

1. Wählen Sie **Privaten Schlüssel speichern** aus, und speichern Sie den Schlüssel an einem Speicherort, an dem Sie darauf zugreifen können. Denken Sie daran, dass dieser Schlüssel zum öffentlichen Schlüssel gehört.

Ihr öffentlicher Schlüssel befindet sich in der Textdatei, die Sie gespeichert haben. Wenn Sie die Datei öffnen, sieht der Text darin etwa folgendermaßen aus:

```text  
---- BEGIN SSH2 PUBLIC KEY ----
Comment: "rsa-key-20190330"
THISISANEXAMPLEDONOTUSE AAAAB3NzaC1yc2EAAAABJQAAAQEAthW2CinpqhXq
9uSa8/lSH7tLelMXnFljSrJIcpxp3MlHlYVbjHHoKfpvQek8DwKdOUcFIEzuStfT
Z8eUI1s5ZXkACudML68qQT8R0cmcFBGNY20K9ZMz/kZkCEbN80DJ+UnWgjdXKLvD
Dwl9aQwNc7W/WCuZtWPazee95PzAShPefGZ87Jp0OCxKaGYZ7UXMrCethwfVumvU
aj+aPsSThXncgVQUhSf/1IoRtnGOiZoktVvt0TIlhxDrHKHU/aZueaFXYqpxDLIs
BvpmONCSR3YnyUtgWV27N6zC7U1OBdmv7TN6M7g01uOYQKI/GQ==
---- END SSH2 PUBLIC KEY ----
```

Bei der Verwendung des öffentlichen Schlüssels kopieren Sie den gesamten Inhalt des Textfelds und fügen ihn als Wert ein, wenn eine Anwendung zur Eingabe des Schlüssels auffordert.

<!-- 
## Create an SSH public key on Linux

ToDo: I need to write this section.

-->
## <a name="connect-with-ssh-using-putty"></a>Herstellen einer SSH-Verbindung mithilfe von PuTTY

Wenn Sie PuTTY installiert haben, verfügen Sie sowohl über den Schlüsselgenerator als auch über einen SSH-Client. Öffnen Sie den SSH-Client von PuTTY, und konfigurieren Sie Ihre Verbindungswerte und Ihren SSH-Schlüssel. Falls Sie im gleichen Netzwerk arbeiten, in dem sich Ihre Azure Stack-Instanz befindet, stellen Sie eine Verbindung mit Ihrer VM her.

Bevor Sie die Verbindung herstellen, benötigen Sie Folgendes:
- PuTTY.
- Die IP-Adresse und der Benutzername für den Linux-Computer in Ihrer Azure Stack-Instanz, die einen öffentlichen SSH-Schlüssel als Authentifizierungstyp verwendet.
- Port 22 muss für den Computer geöffnet sein.
- Der öffentliche SSH-Schlüssel, den Sie beim Erstellen des Computers verwendet haben.
- Ihr Clientcomputer, auf dem PuTTY ausgeführt wird, muss sich im gleichen Netzwerk befinden wie Ihre Azure Stack-Instanz.

### <a name="connect-via-ssh-with-putty"></a>Herstellen Verbindung über SSH mithilfe von PuTTY

1. Öffnen Sie PuTTY.

    ![Verwenden von PuTTY zum Herstellen einer Verbindung](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-connect.png)

2. Fügen Sie den Benutzernamen und die öffentliche IP-Adresse des Computers hinzu. Beispielsweise `username@192.XXX.XXX.XX` als **Hostname**. 
3. Überprüfen Sie, ob **Port** `22` angegeben und der **Verbindungstyp** auf `SSH` festgelegt ist.
4. Erweitern Sie **SSH** > **Auth** in der Struktur **Kategorie**.

    ![Privater SSH-Schlüssel](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-set-private-key.png)

5. Wählen Sie **Durchsuchen** aus, und suchen Sie die Datei mit Ihrem privaten Schlüssel (Dateiname.ppk) des Paars aus öffentlichem und privatem Schlüssel.
6. Wählen Sie in der Struktur „Kategorie“ den Eintrag „Sitzung“ aus.

    ![Öffentlicher SSH-Schlüssel mit privatem Schlüssel](media/azure-stack-dev-start-howto-ssh-public-key/003-puTTY-save-session.png)

7. Geben Sie unter **Gespeicherte Sitzungen** einen Namen für die Sitzung ein, und klicken Sie auf **Speichern**.
8. Wählen Sie den Namen der Sitzung aus, und klicken Sie auf **Laden**.
9. Wählen Sie **Open**(Öffnen). Die SSH-Sitzung wird geöffnet.

## <a name="connect-with-sftp-with-filezilla"></a>Herstellen einer SFTP-Verbindung über FileZilla

Sie können FileZilla als FTP-Client verwenden, der SFTP unterstützt, um Dateien auf Ihren und aus Ihrem Linux-Computer zu verschieben. FileZilla kann unter Windows 10, Linux und macOS ausgeführt werden. Der FileZilla-Client unterstützt FTP sowie FTP über TLS (FTPS) und SFTP. Es handelt sich um eine Open-Source-Software, die im Rahmen der GNU General Public License kostenlos erhältlich ist.

### <a name="set-your-connection"></a>Einrichten der Verbindung

1. [Laden Sie FileZilla herunter, und installieren Sie es](https://filezilla-project.org/download.php).
1. Öffnen Sie FileZilla.
1. Klicken Sie auf **Datei** > **Servermanager**.

    ![Öffentlicher SSH-Schlüssel mit privatem Schlüssel](media/azure-stack-dev-start-howto-ssh-public-key/005-filezilla-file-manager.png)

1. Wählen Sie **SFTP - SSH File Transfer Protocol** als **Protokoll** aus.
1. Fügen Sie die öffentliche IP-Adresse Ihres Computers in das Feld **Server** ein.
1. Wählen Sie **Normal** als **Verbindungsart** aus.
1. Fügen Sie Ihren Benutzernamen und das zugehörige Kennwort hinzu.
1. Klicken Sie auf **OK**.
1. Wählen Sie **Bearbeiten** > **Einstellungen** aus.

    ![Öffentlicher SSH-Schlüssel mit privatem Schlüssel](media/azure-stack-dev-start-howto-ssh-public-key/006-filezilla-add-private-key.png)

1. Erweitern Sie den Eintrag **Verbindung** in der Struktur **Seite auswählen**. Wählen Sie **SFTP** aus.
1. Wählen Sie **Schlüsseldatei hinzufügen** aus, und fügen Sie Ihren privaten Schlüssel hin, z.B. „Dateiname.ppk“.
1. Klicken Sie auf **OK**.

### <a name="open-your-connection"></a>Öffnen der Verbindung

1. Öffnen Sie FileZilla.
1. Klicken Sie auf **Datei** > **Servermanager**.
1. Wählen Sie den Namen Ihres Servers aus, und klicken Sie auf **Verbinden**.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum [Entwickeln für Azure Stack](azure-stack-dev-start.md)