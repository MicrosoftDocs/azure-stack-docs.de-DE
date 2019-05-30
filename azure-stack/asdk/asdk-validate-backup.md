---
title: Überprüfen einer Azure Stack-Sicherung mit dem ASDK | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine Azure Stack-Sicherung für integrierte Systeme mit dem ASDK überprüfen.
services: azure-stack
author: justinha
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 02/15/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 02/15/2019
ms.openlocfilehash: 38c4de35b4d2b5eac16b8586aa6933b18c62b14a
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66267332"
---
# <a name="use-the-asdk-to-validate-an-azure-stack-backup"></a>Verwenden des ASDK zum Überprüfen der Azure Stack-Sicherung
Nach der Bereitstellung von Azure Stack und von Benutzerressourcen wie Angeboten, Plänen, Kontingenten und Abonnements sollten Sie [Azure Stack-Infrastruktursicherung aktivieren](../operator/azure-stack-backup-enable-backup-console.md). Das Planen und Ausführen regelmäßiger Infrastruktursicherungen stellt sicher, dass Daten zur Infrastrukturverwaltung nicht verloren gehen, wenn ein Hardware- oder Dienstausfall auftritt.

> [!TIP]
> Es wird empfohlen, [eine bedarfsgesteuerte Sicherung auszuführen](../operator/azure-stack-backup-back-up-azure-stack.md), bevor Sie dieses Verfahren beginnen, um sicherzustellen, dass Sie über eine Kopie der neuesten Infrastrukturdaten verfügen. Stellen Sie sicher, die Sicherungs-ID zu erfassen, nachdem eine Sicherung erfolgreich abgeschlossen wurde. Diese ID wird bei der Cloudwiederherstellung benötigt. 

Azure Stack-Infrastruktursicherungen enthalten wichtige Daten zu Ihrer Cloud, die während einer erneuten Bereitstellung von Azure Stack wiederhergestellt werden können. Sie können das ASDK verwenden, um diese Sicherungen ohne Auswirkungen auf den Schutz Ihrer Produktionscloud zu überprüfen. 

Das Überprüfen von Sicherungen im ASDK wird für die folgenden Szenarien unterstützt:

|Szenario|Zweck|
|-----|-----|
|Überprüfen von Infrastruktursicherungen einer integrierten Lösung|Kurze Überprüfung, ob die Daten in der Sicherung gültig sind.|
|Informieren über den vollständigen Wiederherstellungsworkflow|Verwenden Sie das ASDK zum Überprüfen der gesamten Sicherung und Wiederherstellung.|
|     |     |

Das folgende Szenario wird beim Überprüfen von Sicherungen auf dem ASDK **nicht** unterstützt:

|Szenario|Zweck|
|-----|-----|
|ASDK zum Erstellen von Sicherung und Wiederherstellung|Stellen Sie Daten aus einer früheren Version des ASDK in einer neueren Version wieder her.|
|     |     |


## <a name="cloud-recovery-deployment"></a>Bereitstellung von Cloudwiederherstellungen
Infrastruktursicherungen aus der Bereitstellung integrierter Systeme können durch Ausführen einer Bereitstellung von Cloudwiederherstellungen des ASDK überprüft werden. Bei dieser Art der Bereitstellung werden bestimmte Dienstdaten aus einer Sicherung wiederhergestellt, nachdem das ASDK auf dem Hostcomputer installiert wurde.

### <a name="prereqs"></a>Voraussetzungen für die Cloudwiederherstellung
Vor dem Starten einer Bereitstellung für die Cloudwiederherstellung des ASDK benötigen Sie die folgenden Informationen:

**Anforderungen an das Benutzeroberflächen-Installationsprogramm**

*Aktuelles Benutzeroberflächen-Installationsprogramm unterstützt nur Verschlüsselungsschlüssel*

|Voraussetzung|BESCHREIBUNG|
|-----|-----|
|Freigabepfad der Sicherung|Der UNC-Dateifreigabepfad der neuesten Azure Stack-Sicherung, die zum Wiederherstellen von Informationen zur Azure Stack-Infrastruktur verwendet werden. Diese lokale Freigabe wird während des Bereitstellungsprozesses für Cloudwiederherstellungen erstellt.|
|Sicherungs-ID für die Wiederherstellung|Die Sicherungs-ID im alphanumerischen Format „xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx“, die die Sicherung bezeichnet, für die eine Cloudwiederherstellung durchgeführt werden soll.|
|Zeitserver-IP-Adresse|Eine gültige Zeitserver IP-Adresse (z.B. 132.163.97.2) ist für Azure Stack-Bereitstellungen erforderlich.|
|Kennwort für externes Zertifikat|Das Kennwort für das externe Zertifikat, das von Azure Stack verwendet werden soll. Die Sicherung der Zertifizierungsstelle enthält externe Zertifikate, die mit diesem Kennwort wiederhergestellt werden müssen.|
|Verschlüsselungsschlüssel der Sicherung|Erforderlich, wenn Sie ein Upgrade auf Azure Stack Version 1901 oder höher vorgenommen haben und die Sicherungseinstellungen noch mit einem Verschlüsselungsschlüssel konfiguriert sind. Ab 1901 sind Verschlüsselungsschlüssel veraltet. Das Installationsprogramm unterstützt Verschlüsselungsschlüssel im Abwärtskompatibilitätsmodus für mindestens 3 Versionen. Nachdem Sie die Einstellungen auf die Verwendung eines Zertifikats aktualisiert haben, finden Sie die erforderlichen Informationen in der nächsten Tabelle.|

|     |     | 

**Anforderungen an das PowerShell-Installationsprogramm**

*Das aktuelle PowerShell-Installationsprogramm unterstützt Verschlüsselungsschlüssel oder Entschlüsselungszertifikate*

|Voraussetzung|BESCHREIBUNG|
|-----|-----|
|Freigabepfad der Sicherung|Der UNC-Dateifreigabepfad der neuesten Azure Stack-Sicherung, die zum Wiederherstellen von Informationen zur Azure Stack-Infrastruktur verwendet werden. Diese lokale Freigabe wird während des Bereitstellungsprozesses für Cloudwiederherstellungen erstellt.|
|Sicherungs-ID für die Wiederherstellung|Die Sicherungs-ID im alphanumerischen Format „xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx“, die die Sicherung bezeichnet, für die eine Cloudwiederherstellung durchgeführt werden soll.|
|Zeitserver-IP-Adresse|Eine gültige Zeitserver IP-Adresse (z.B. 132.163.97.2) ist für Azure Stack-Bereitstellungen erforderlich.|
|Kennwort für externes Zertifikat|Das Kennwort für das externe Zertifikat, das von Azure Stack verwendet werden soll. Die Sicherung der Zertifizierungsstelle enthält externe Zertifikate, die mit diesem Kennwort wiederhergestellt werden müssen.|
|Kennwort für Entschlüsselungszertifikat|Optional. Nur erforderlich, wenn die Sicherung mit einem Zertifikat verschlüsselt ist. Das Kennwort ist für die PFX-Datei des selbstsignierten Zertifikats, die den privaten Schlüssel enthält, der zum Entschlüsseln der Sicherungsdaten notwendig ist.|
|Verschlüsselungsschlüssel der Sicherung|Optional. Erforderlich, wenn Sie ein Upgrade auf Azure Stack Version 1901 oder höher vorgenommen haben und die Sicherungseinstellungen noch mit einem Verschlüsselungsschlüssel konfiguriert sind. Das Installationsprogramm unterstützt Verschlüsselungsschlüssel im Abwärtskompatibilitätsmodus für mindestens 3 Versionen. Nachdem Sie die Sicherungseinstellungen auf die Verwendung eines Zertifikats aktualisiert haben, müssen Sie das Kennwort für das Entschlüsselungszertifikat angeben.|
|     |     | 

## <a name="prepare-the-host-computer"></a>Vorbereiten des Hostcomputers 
Wie bei einer normalen ASDK-Bereitstellung muss die ASDK-Hostsystemumgebung für die Installation vorbereitet werden. Nach der Vorbereitung des Development Kit-Hostcomputers startet der Computer von der Festplatte des virtuellen Computers (CloudBuilder.vhdx), sodass die ASDK-Bereitstellung beginnen kann.

Laden Sie auf dem ASDK-Hostcomputer die zur gesicherten Version von Azure Stack gehörige Datei „cloudbuilder.vhdx“ herunter, und befolgen Sie die Anweisungen für das [Vorbereiten des ASDK-Hostcomputers](asdk-prepare-host.md).

Nach dem Neustart des Hostservers aus der Datei „cloudbuilder.vhdx“ müssen Sie eine Dateifreigabe erstellen und Ihre Sicherungsdaten in diese kopieren. Die Dateifreigabe muss für das Konto, unter dem das Setup ausgeführt wird, zugänglich sein. In diesen PowerShell-Beispielbefehlen ist dies das Administratorkonto: 

```powershell
$shares = New-Item -Path "c:\" -Name "Shares" -ItemType "directory"
$azsbackupshare = New-Item -Path $shares.FullName -Name "AzSBackups" -ItemType "directory"
New-SmbShare -Path $azsbackupshare.FullName -FullAccess ($env:computername + "\Administrator")  -Name "AzSBackups"
```

Kopieren Sie als Nächstes Ihre aktuellsten Azure Stack-Sicherungsdateien in die neu erstellte Freigabe. Die Ordnerstruktur in der Freigabe sollte `\\<ComputerName>\AzSBackups\MASBackup\<BackupID>\` lauten.

Schließlich kopieren Sie das Entschlüsselungszertifikat (.pfx) in das Zertifikatsverzeichnis: `C:\CloudDeployment\Setup\Certificates\` und benennen die Datei in `BackupDecryptionCert.pfx` um.

## <a name="deploy-the-asdk-in-cloud-recovery-mode"></a>Bereitstellen des ASDK im Cloudwiederherstellungsmodus

> [!IMPORTANT]
> 1. Das aktuelle Benutzeroberflächen-Installationsprogramm unterstützt nur Verschlüsselungsschlüssel. Sie können nur Sicherungen von Systemen überprüfen, die weiterhin Verschlüsselungsschlüssel verwenden. Wenn die Sicherung auf einem integrierten System oder ASDK mit einem Zertifikat verschlüsselt wurde, müssen Sie das PowerShell-Installationsprogramm verwenden (**InstallAzureStackPOC.ps1**). 
> 2. Das PowerShell-Installationsprogramm (**InstallAzureStackPOC.ps1**) unterstützt Verschlüsselungsschlüssel oder Zertifikate.
> 3. Für die ASDK-Installation wird genau eine Netzwerkschnittstellenkarte (NIC) für das Netzwerk unterstützt. Wenn Sie mehrere Netzwerkadapter nutzen, sollten Sie vor dem Ausführen des Bereitstellungsskripts sicherstellen, dass nur einer aktiviert ist (und alle anderen deaktiviert).

### <a name="use-the-installer-ui-to-deploy-the-asdk-in-recovery-mode"></a>Verwenden des Benutzeroberflächen-Installationsprogramms zum Bereitstellen des ASDK im Wiederherstellungsmodus
Die Schritte in diesem Abschnitt zeigen, wie das ASDK mit einer grafischen Benutzeroberfläche (GUI) bereitgestellt werden kann, die durch Herunterladen und Ausführen des PowerShell-Skripts **asdk-installer.ps1** bereitgestellt werden kann.

> [!NOTE]
> Die Benutzeroberfläche des Installers für das Azure Stack Development Kit ist ein Open Source-Skript, das auf WCF und PowerShell basiert.

> [!IMPORTANT]
> Das aktuelle Benutzeroberflächen-Installationsprogramm unterstützt nur Verschlüsselungsschlüssel.

1. Nachdem der Hostcomputer im Image „CloudBuilder.vhdx“ erfolgreich gestartet wurde, können Sie sich mit den Administratoranmeldeinformationen anmelden, die Sie bei der [Vorbereitung des Development Kit-Hostcomputers](asdk-prepare-host.md) für die ASDK-Installation angegeben haben. Diese sollten mit den Anmeldeinformationen für den lokalen Administrator des Development Kit-Hosts identisch sein.
2. Öffnen Sie eine PowerShell-Konsole mit erhöhten Rechten, und führen Sie das PowerShell-Skript „ **&lt;Laufwerkbuchstabe>\AzureStack_Installer\asdk-installer.ps1**“ aus. Das Skript kann sich jetzt eventuell auf einem anderen Laufwerk als „C:\“ im Image „CloudBuilder.vhdx“ befindet. Klicken Sie auf **Wiederherstellen**.

    ![ASDK-Installationsskript](media/asdk-validate-backup/1.PNG) 

3. Geben Sie Ihre Azure AD-Verzeichnisinformationen (optional) sowie das lokale Administratorkennwort für den ASDK-Hostcomputer auf der Seite für Identitätsanbieter und Anmeldeinformationen ein. Klicken Sie auf **Weiter**.

    ![Seite „Identität und Anmeldeinformationen“](media/asdk-validate-backup/2.PNG) 

4. Wählen Sie den Netzwerkadapter aus, der vom ASDK-Hostcomputer verwendet werden soll, und klicken Sie auf **Weiter**. Alle anderen Netzwerkschnittstellen werden während der ASDK-Installation deaktiviert. 

    ![Netzwerkadapterschnittstelle](media/asdk-validate-backup/3.PNG) 

5. Geben Sie auf der Seite „Netzwerkkonfiguration“ gültige IP-Adressen für einen Zeitserver und die DNS-Weiterleitung an. Klicken Sie auf **Weiter**.

    ![Seite „Netzwerkkonfiguration“](media/asdk-validate-backup/4.PNG) 

6. Wenn Netzwerkschnittstellenkarten-Eigenschaften überprüft wurden, klicken Sie auf **Weiter**. 

    ![Überprüfung der Netzwerkkarteneinstellungen](media/asdk-validate-backup/5.PNG) 

7. Geben Sie die weiter oben im [Abschnitt „Voraussetzungen“](#prereqs) beschriebenen, erforderlichen Informationen auf der Seite „Sicherungseinstellungen“ an sowie den Benutzernamen und das Kennwort, die für den Zugriff auf die Dateifreigabe verwendet werden sollen. Klicken Sie auf **Weiter**: 

   ![Seite „Sicherungseinstellungen“](media/asdk-validate-backup/6.PNG) 

8. Überprüfen Sie für die Bereitstellung des ASDK zu verwendende Bereitstellungsskript auf der Seite „Zusammenfassung“. Klicken Sie auf **Bereitstellen**, um die Bereitstellung zu starten. 

    ![Seite „Zusammenfassung“](media/asdk-validate-backup/7.PNG) 


### <a name="use-powershell-to-deploy-the-asdk-in-recovery-mode"></a>Verwenden der PowerShell zum Bereitstellen des ASDK im Wiederherstellungsmodus

Passen Sie die folgenden PowerShell-Befehle an Ihre Umgebung an, und führen Sie sie aus, um das ASDK im Cloudwiederherstellungsmodus bereitzustellen:

**Verwenden Sie das Skript „InstallAzureStackPOC.ps1“, um die Cloudwiederherstellung mit Verschlüsselungsschlüssel zu initiieren.**

```powershell
cd C:\CloudDeployment\Setup     
$adminpass = Read-Host -AsSecureString -Prompt "Local Administrator password"
$certPass = Read-Host -AsSecureString -Prompt "Password for the external certificate"
$backupstorecredential = Read-Host -AsSecureString -Prompt "Credential for backup share"
$key = Read-Host -AsSecureString -Prompt "Your backup encryption key"

.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass `
 -BackupStorePath ("\\" + $env:COMPUTERNAME + "\AzSBackups") `
 -BackupEncryptionKeyBase64 $key `
 -BackupStoreCredential $backupstorecredential `
 -BackupId "<Backup ID to restore>" `
 -TimeServer "<Valid time server IP>" -ExternalCertPassword $certPass
```

**Verwenden Sie das Skript „InstallAzureStackPOC.ps1“, um die Cloudwiederherstellung mit Entschlüsselungszertifikat zu initiieren.**

```powershell
cd C:\CloudDeployment\Setup     
$adminpass = Read-Host -AsSecureString -Prompt "Local Administrator password"
$certPass = Read-Host -AsSecureString -Prompt "Password for the external certificate"
$backupstorecredential = Read-Host -AsSecureString -Prompt "Credential for backup share"
$decryptioncertpassword  = Read-Host -AsSecureString -Prompt "Password for the decryption certificate"

.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass `
 -BackupStorePath ("\\" + $env:COMPUTERNAME + "\AzSBackups") `
 -BackupDecryptionCertPassword $decryptioncertpassword `
 -BackupStoreCredential $backupstorecredential `
 -BackupId "<Backup ID to restore>" `
 -TimeServer "<Valid time server IP>" -ExternalCertPassword $certPass
```

## <a name="complete-cloud-recovery"></a>Abschließen der Cloudwiederherstellung 
Nach einer erfolgreichen Bereitstellung für die Cloudwiederherstellung müssen Sie die Wiederherstellung mit dem Cmdlet **Restore-AzureStack** abschließen. 

Nach der Anmeldung als Azure Stack-Operator [installieren Sie Azure Stack PowerShell](asdk-post-deploy.md#install-azure-stack-powershell) und führen dann die folgenden Befehle aus, um das Zertifikat und Kennwort anzugeben, die beim Wiederherstellen aus der Sicherung verwendet werden sollen:

**Wiederherstellungsmodus mit Zertifikatsdatei**

> [!NOTE] 
> Eine Azure Stack-Bereitstellung behält das Entschlüsselungszertifikat aus Sicherheitsgründen nicht. Sie müssen das Entschlüsselungszertifikat mit dem zugehörigen Kennwort erneut bereitstellen.

```powershell
$decryptioncertpassword = Read-Host -AsSecureString -Prompt "Password for the decryption certificate"
Restore-AzsBackup -ResourceId "<BackupID>" `
 -DecryptionCertPath "<path to decryption certificate with file name (.pfx)>" `
 -DecryptionCertPassword $decryptioncertpassword
```

**Wiederherstellungsmodus mit Verschlüsselungsschlüssel**
```powershell
$decryptioncertpassword = Read-Host -AsSecureString -Prompt "Password for the decryption certificate"
Restore-AzsBackup -ResourceId "<BackupID>"
```

Warten Sie nach dem Aufrufen dieses Cmdlets 60 Minuten, bevor Sie die Überprüfung der Sicherungsdaten im ASDK, das in der Cloud wiederhergestellt wurde, starten.

## <a name="next-steps"></a>Nächste Schritte
[Registrieren von Azure Stack](asdk-register.md)

