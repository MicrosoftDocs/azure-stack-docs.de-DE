---
title: Verwenden eines öffentlichen SSH-Schlüssels mit Azure Stack Hub
description: Verwenden eines öffentlichen SSH-Schlüssels
author: mattbriggs
ms.topic: overview
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: 20e8afdae36f7a182f20aff1f4162d36378ca313
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525454"
---
# <a name="use-an-ssh-public-key"></a>Verwenden eines öffentlichen SSH-Schlüssels

Zur Verwendung einer offenen SSH-Verbindung zwischen Ihrem Entwicklungscomputer und der Server-VM in Ihrer Azure Stack Hub-Instanz, in der Ihre Web-App gehostet wird, müssen Sie unter Umständen ein öffentlich-privates SSH-Schlüsselpaar (Secure Shell) erstellen. 

In diesem Artikel erstellen Sie Ihre Schlüssel und verwenden diese dann, um eine Verbindung mit Ihrem Server herzustellen. Sie können einen SSH-Client verwenden, um eine Bash-Eingabeaufforderung auf dem Linux-Server zu öffnen. Sie können auch einen SFTP-Client (Secure FTP) verwenden, um Dateien auf den und vom Server zu verschieben.

## <a name="create-an-ssh-public-key-on-windows"></a>Erstellen eines öffentlichen SSH-Schlüssels auf Windows

In diesem Abschnitt verwenden Sie den Schlüsselgenerator von PuTTY, um ein öffentlich-privates SSH-Schlüsselpaar zu erstellen, das Sie beim Erstellen einer sicheren Verbindung mit Linux-Computern in Ihrer Azure Stack Hub-Instanz verwenden. Bei PuTTY handelt es sich um einen kostenlosen Terminalemulator, mit dem Sie per SSH und Telnet eine Verbindung mit einem Server herstellen können.

1. [Laden Sie PuTTY für Windows herunter, und installieren Sie es](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

1. Öffnen Sie den PuTTY-Schlüsselgenerator.

    ![PuTTY-Schlüsselgenerator mit leerem Schlüsselfeld](media/azure-stack-dev-start-howto-ssh-public-key/001-putty-key-gen-start.png)

1. Wählen Sie unter **Parameters** (Parameter) die Option **RSA**.

1. Geben Sie im Feld **Number of bits in a generated key** (Anzahl von Bits in einem generierten Schlüssel) den Wert **2048** ein.  

1. Wählen Sie **Generieren** aus.

1. Generieren Sie im Bereich **Schlüssel** einige zufällige Zeichen, indem Sie den Cursor über den leeren Bereich bewegen.

    ![PuTTY-Schlüsselgenerator mit ausgefülltem Schlüsselfeld](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-key-gen-result.png)

1. Geben Sie eine **Schlüsselpassphrase** ein, und bestätigen Sie diese im Feld **Passphrase bestätigen**. Notieren Sie sich die Passphrase zur späteren Verwendung.

1. Wählen Sie **Save public key** (Öffentlichen Schlüssel speichern), und speichern Sie den Schlüssel an einem Speicherort, auf den Sie Zugriff haben.

1. Wählen Sie **Save private key** (Privaten Schlüssel speichern), und speichern Sie den Schlüssel an einem Speicherort, auf den Sie Zugriff haben. Beachten Sie, dass dieser Schlüssel zum öffentlichen Schlüssel gehört.

Ihr öffentlicher Schlüssel befindet sich in der Textdatei, die Sie gespeichert haben. Der Text sieht dann wie folgt aus:

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

Wenn eine Anwendung den Schlüssel anfordert, können Sie den gesamten Inhalt der Textdatei kopieren und einfügen.

## <a name="connect-with-ssh-by-using-putty"></a>Herstellen einer SSH-Verbindung mithilfe von PuTTY

Nach der Installation von PuTTY verfügen Sie sowohl über den PuTTY-Schlüsselgenerator als auch über einen SSH-Client. In diesem Abschnitt öffnen Sie den SSH-Client und PuTTY und konfigurieren Ihre Verbindungswerte und den SSH-Schlüssel. Stellen Sie eine Verbindung mit Ihrem virtuellen Computer her, wenn Sie sich in demselben Netzwerk wie die Azure Stack Hub-Instanz befinden.

Bevor Sie die Verbindung herstellen, benötigen Sie Folgendes:
- PuTTY.
- Die IP-Adresse und der Benutzername für den Linux-Computer in Ihrer Azure Stack Hub-Instanz, die einen öffentlichen SSH-Schlüssel als Authentifizierungstyp verwendet
- Port 22 muss für den Computer geöffnet sein.
- Der öffentliche SSH-Schlüssel, den Sie beim Erstellen des Computers verwendet haben.
- Der Clientcomputer, auf dem PuTTY ausgeführt wird, muss sich in demselben Netzwerk wie Ihre Azure Stack Hub-Instanz befinden.

1. Öffnen Sie PuTTY.

    ![PuTTY-Konfigurationsbereich](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-connect.png)

2. Geben Sie im Feld **Hostname (oder IP-Adresse)** den Benutzernamen und die öffentliche IP-Adresse des Computers ein (z. B. **username@192.XXX.XXX.XX** ). 
3. Vergewissern Sie sich, dass für **Port** die Zahl **22** angegeben und als **Verbindungstyp** die Option **SSH** ausgewählt ist.
4. Erweitern Sie in der Struktur **Kategorie** die Punkte **SSH** und **Auth**.

    ![PuTTY-Konfigurationsbereich: Privater SSH-Schlüssel](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-set-private-key.png)

5. Wählen Sie neben dem Feld **Private key file for authentication** (Datei mit privatem Schlüssel für die Authentifizierung) die Option **Durchsuchen**, und suchen Sie für Ihr öffentlich-privates Schlüsselpaar dann nach der Datei mit dem privaten Schlüssel ( *\<filename>.ppk*).
6. Wählen Sie in der Struktur **Kategorie** die Option **Sitzung**.

    ![PuTTY-Konfigurationsbereich: Feld „Saved Sessions“ (Gespeicherte Sitzungen)](media/azure-stack-dev-start-howto-ssh-public-key/003-puTTY-save-session.png)

7. Geben Sie unter **Saved Sessions** (Gespeicherte Sitzungen) einen Namen für die Sitzung ein, und wählen Sie dann die Option **Speichern**.
8. Wählen Sie in der Liste **Saved Sessions** (Gespeicherte Sitzungen) den Namen Ihrer Sitzung und dann die Option **Laden** aus.
9. Wählen Sie **Open**(Öffnen). Die SSH-Sitzung wird geöffnet.

## <a name="connect-with-sftp-with-filezilla"></a>Herstellen einer SFTP-Verbindung über FileZilla

Zum Verschieben von Dateien für Ihren Linux-Computer können Sie FileZilla nutzen. Dies ist ein FTP-Client, für den Secure FTP (SFTP) unterstützt wird. FileZilla kann unter Windows 10, Linux und macOS ausgeführt werden. Der FileZilla-Client unterstützt FTP, FTP über TLS (FTPS) und SFTP. Es handelt sich um eine Open-Source-Software, die im Rahmen der GNU General Public License kostenlos erhältlich ist.

### <a name="set-your-connection"></a>Einrichten der Verbindung

1. [Laden Sie FileZilla herunter, und installieren Sie es](https://filezilla-project.org/download.php).
1. Öffnen Sie FileZilla.
1. Klicken Sie auf **Datei** > **Servermanager**.

    ![Bereich „FileZilla Site Manager“](media/azure-stack-dev-start-howto-ssh-public-key/005-filezilla-file-manager.png)

1. Wählen Sie in der Dropdownliste **Protokoll** die Option **SFTP – SSH File Transfer Protocol**.
1. Geben Sie im Feld **Host** die öffentliche IP-Adresse für Ihren Computer ein.
1. Wählen Sie im Feld **Anmeldetyp** die Option **Normal**.
1. Geben Sie Ihren Benutzernamen und Ihr Kennwort ein.
1. Klicken Sie auf **OK**.
1. Wählen Sie **Bearbeiten** > **Einstellungen** aus.

    ![FileZilla-Einstellungsbereich](media/azure-stack-dev-start-howto-ssh-public-key/006-filezilla-add-private-key.png)

1. Erweitern Sie in der Struktur **Seite auswählen** die Option **Verbindung**, und wählen Sie **SFTP**.
1. Wählen Sie **Add key file** (Schlüsseldatei hinzufügen), und geben Sie dann Ihre Datei mit dem privaten Schlüssel ein (z. B. *\<filename>.ppk*).
1. Klicken Sie auf **OK**.

### <a name="open-your-connection"></a>Öffnen der Verbindung

1. Öffnen Sie FileZilla.
1. Klicken Sie auf **Datei** > **Servermanager**.
1. Wählen Sie den Namen Ihres Servers aus, und klicken Sie dann auf **Verbinden**.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über das [Einrichten einer Entwicklungsumgebung in Azure Stack Hub](azure-stack-dev-start.md).
