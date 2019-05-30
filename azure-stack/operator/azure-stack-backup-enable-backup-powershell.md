---
title: Aktivieren der Sicherung für Azure Stack mit PowerShell | Microsoft-Dokumentation
description: Aktivieren Sie den Dienst für die Infrastruktursicherung mit Windows PowerShell, sodass Azure Stack bei einem Fehler wiederhergestellt werden kann.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 03/14/2019
ms.openlocfilehash: 08950940510d874b407448bab37c6a43f6965c7f
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66268949"
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>Aktivieren der Sicherung für Azure Stack mit PowerShell

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Aktivieren Sie den Infrastructure Backup-Dienst mit Windows PowerShell, um regelmäßige Sicherungen von Folgendem zu erstellen:
 - Interner Identitätsdienst und Stammzertifikat
 - Benutzertarife, -angebote und -abonnements
 - Compute-, Speicher- und Netzwerkbenutzerkontingente
 - Geheime Schlüssel für Benutzerschlüsseltresor
 - RBAC-Rollen und -Richtlinien für Benutzer
 - Benutzerspeicherkonten

Sie können auf die PowerShell-Cmdlets zugreifen, um über den Endpunkt der Bedienerverwaltung die Sicherung zu aktivieren und zu starten sowie Sicherungsinformationen abzurufen.

## <a name="prepare-powershell-environment"></a>Vorbereiten der PowerShell-Umgebung

Anweisungen zum Konfigurieren der PowerShell-Umgebung finden Sie unter [Installieren von PowerShell für Azure Stack](azure-stack-powershell-install.md). Weitere Informationen zur Anmeldung bei Azure Stack finden Sie unter [Konfigurieren der Betreiberumgebung und Anmelden bei Azure Stack](azure-stack-powershell-configure-admin.md).

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Angeben der Sicherungsfreigabe, der Anmeldeinformationen und des Verschlüsselungsschlüssels zum Aktivieren der Sicherung

Bearbeiten Sie in der gleichen PowerShell-Sitzung das folgende PowerShell-Skript durch Hinzufügen der Variablen für Ihre Umgebung. Führen Sie das aktualisierte Skript aus, um die Sicherungsfreigabe, die Anmeldeinformationen und den Verschlüsselungsschlüssel für den Infrastructure Backup-Dienst anzugeben.

| Variable        | BESCHREIBUNG   |
|---              |---                                        |
| $username       | Geben Sie den **Benutzernamen** ein, und verwenden Sie dabei die Domäne und den Benutzernamen für den Speicherort des freigegebenen Laufwerks mit ausreichendem Zugriff zum Lesen und Schreiben von Dateien. Beispiel: `Contoso\backupshareuser`. |
| $password       | Geben Sie das **Kennwort** für den Benutzer ein. |
| $sharepath      | Geben Sie den Pfad zu dem **Sicherungsspeicherort** ein. Für den Pfad zu einer auf einem separaten Gerät gehosteten Dateifreigabe müssen Sie eine UNC-Zeichenfolge (Universal Naming Convention) verwenden. Eine UNC-Zeichenfolge gibt den Speicherort von Ressourcen (z.B. freigegebene Dateien oder Geräte) an. Zur Sicherstellung der Verfügbarkeit der Sicherungsdaten sollte sich das Gerät an einem gesonderten Speicherort befinden. |
| $frequencyInHours | Die Häufigkeit in Stunden bestimmt, wie oft Sicherungen erstellt werden. Der Standardwert ist 12. Scheduler unterstützt maximal 12 und mindestens 4.|
| $retentionPeriodInDays | Die Vermerkdauer in Tagen bestimmt, wie viele Tage Sicherungen am externen Speicherort beibehalten werden. Der Standardwert ist 7. Scheduler unterstützt maximal 14 und mindestens 2. Nach Ablauf ihrer Vermerkdauer werden Sicherungen automatisch aus dem externen Speicherort gelöscht.|
| $encryptioncertpath | Gilt ab 1901.  Der Parameter ist ab der Azure Stack-Modulversion 1.7 verfügbar. Der Verschlüsselungszertifikatpfad gibt den Dateipfad zu der CER-Datei mit dem öffentlichen Schlüssel an, die für die Datenverschlüsselung verwendet wird. |
| $encryptionkey | Gilt bis zum Build 1811. Der Parameter ist bis zur Azure Stack-Modulversion 1.6 verfügbar. Der für die Datenverschlüsselung verwendete Verschlüsselungsschlüssel. Verwenden Sie das Cmdlet [New-AzsEncryptionKeyBase64](https://docs.microsoft.com/powershell/module/azs.backup.admin/new-azsencryptionkeybase64), um einen neuen Verschlüsselungsschlüssel zu erstellen. |
|     |     |

### <a name="enable-backup-on-1901-and-beyond-using-certificate"></a>Aktivieren der Sicherung unter Verwendung eines Zertifikats (ab 1901)
```powershell
    # Example username:
    $username = "domain\backupadmin"
 
    # Example share path:
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

    $password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString

    # Create a self-signed certificate using New-SelfSignedCertificate, export the public key portion and save it locally.

    $cert = New-SelfSignedCertificate `
        -DnsName "www.contoso.com" `
        -CertStoreLocation "cert:\LocalMachine\My" 

    New-Item -Path "C:\" -Name "Certs" -ItemType "Directory" 

    #make sure to export the PFX format of the certificate with the public and private keys and then delete the certificate from the local certificate store of the machine where you created the certificate
    
    Export-Certificate `
        -Cert $cert `
        -FilePath c:\certs\AzSIBCCert.cer 

    # Set the backup settings with the name, password, share, and CER certificate file.
    Set-AzsBackupConfiguration -BackupShare $sharepath -Username $username -Password $password -EncryptionCertPath "c:\temp\cert.cer"
```
### <a name="enable-backup-on-1811-or-earlier-using-certificate"></a>Aktivieren der Sicherung unter Verwendung eines Zertifikats (bis 1811)
```powershell
    # Example username:
    $username = "domain\backupadmin"
 
    # Example share path:
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

    $password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString

    # Create a self-signed certificate using New-SelfSignedCertificate, export the public key portion and save it locally.

    $key = New-AzsEncryptionKeyBase64
    $Securekey = ConvertTo-SecureString -String ($key) -AsPlainText -Force

    # Set the backup settings with the name, password, share, and CER certificate file.
    Set-AzsBackupConfiguration -BackupShare $sharepath -Username $username -Password $password -EncryptionKey $Securekey
```

   
##  <a name="confirm-backup-settings"></a>Überprüfen der Sicherungseinstellungen

Führen Sie in der gleichen PowerShell-Sitzung die folgenden Befehle aus:

   ```powershell
    Get-AzsBackupConfiguration | Select-Object -Property Path, UserName
   ```

Das Ergebnis sollte der folgenden Beispielausgabe ähneln:

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
   ```

## <a name="update-backup-settings"></a>Aktualisieren der Sicherungseinstellungen
In der gleichen PowerShell-Sitzung können Sie die Standardwerte für die Vermerkdauer und die Häufigkeit von Sicherungen aktualisieren. 

   ```powershell
    #Set the backup frequency and retention period values.
    $frequencyInHours = 10
    $retentionPeriodInDays = 5

    Set-AzsBackupConfiguration -BackupFrequencyInHours $frequencyInHours -BackupRetentionPeriodInDays $retentionPeriodInDays

    Get-AzsBackupConfiguration | Select-Object -Property Path, UserName, AvailableCapacity, BackupFrequencyInHours, BackupRetentionPeriodInDays
   ```

Das Ergebnis sollte der folgenden Beispielausgabe ähneln:

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
    AvailableCapacity           : 60 GB
    BackupFrequencyInHours      : 10
    BackupRetentionPeriodInDays : 5
   ```

### <a name="azure-stack-powershell"></a>Azure Stack PowerShell 
Das PowerShell-Cmdlet zum Konfigurieren der Infrastruktursicherung ist „Set-AzsBackupConfiguration“. In früheren Versionen war dies das Cmdlet „Set-AzsBackupShare“. Dieses Cmdlet erfordert die Bereitstellung eines Zertifikats. Wenn die Infrastruktursicherung mit einem Verschlüsselungsschlüssel konfiguriert ist, können Sie weder den Verschlüsselungsschlüssel aktualisieren noch die Eigenschaft anzeigen. Sie müssen die Version 1.6. der Administrator-PowerShell verwenden. 

Wenn die Infrastruktursicherung vor der Aktualisierung auf 1901 konfiguriert wurde, können Sie die Version 1.6 der Administrator-PowerShell verwenden, um den Verschlüsselungsschlüssel festzulegen und anzuzeigen. Version 1.6 lässt nicht zu, dass Sie vom Verschlüsselungsschlüssel auf eine Zertifikatdatei aktualisieren.
Weitere Informationen zum Installieren der richtigen Version des Moduls finden Sie unter [Installieren von Azure Stack PowerShell](azure-stack-powershell-install.md). 


## <a name="next-steps"></a>Nächste Schritte

Informationen zum Ausführen einer Sicherung finden Sie unter [Sichern von Azure Stack](azure-stack-backup-back-up-azure-stack.md).

Informationen zum Überprüfen, ob eine Sicherung durchgeführt wurde, finden Sie unter [Bestätigen der erfolgreichen Sicherung im Verwaltungsportal](azure-stack-backup-back-up-azure-stack.md).
