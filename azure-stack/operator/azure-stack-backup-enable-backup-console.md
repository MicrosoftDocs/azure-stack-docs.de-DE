---
title: Aktivieren der Sicherung für Azure Stack über das Verwaltungsportal | Microsoft-Dokumentation
description: Aktivieren Sie den Dienst für die Infrastruktursicherung über das Verwaltungsportal, sodass Azure Stack bei einem Fehler wiederhergestellt werden kann.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: 56C948E7-4523-43B9-A236-1EF906A0304F
ms.service: azure-stack
ms.workload: naS
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 03/14/2019
ms.openlocfilehash: eefd393fa12814260711590f028c9a787811d8af
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66269026"
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>Aktivieren der Sicherung für Azure Stack über das Verwaltungsportal
Aktivieren Sie den Infrastructure Backup-Dienst über das Verwaltungsportal, sodass Azure Stack Infrastruktursicherungen generieren kann. Mit diesen Sicherungen kann der Hardwarepartner bei einem [schwerwiegenden Fehler](./azure-stack-backup-recover-data.md) Ihre Umgebung mittels einer Cloudwiederherstellung wiederherstellen. Die Cloudwiederherstellung soll sicherstellen, dass die Anbieter und Benutzer sich nach Abschluss der Wiederherstellung wieder im Portal anmelden können. Auf diese Weise können Benutzer ihre Abonnements wiederherstellen, einschließlich RBAC-Berechtigungen (Role-Based Access Control) und -Rollen, ursprünglichen Plänen, Angeboten und zuvor definierten Compute-, Speicher-, Netzwerkkontingenten und Key Vault-Geheimnissen.

Allerdings sichert der Infrastructure Backup-Dienst keine IaaS-VMs, Netzwerkkonfigurationen oder Speicherressourcen (z.B. Speicherkonten, Blobs, Tabellen). Daher werden Benutzern zuvor vorhandene Ressourcen nicht angezeigt, wenn sie sich nach einer Cloudwiederherstellung anmelden. PaaS-Ressourcen (Platform as a Service) und -Daten werden ebenfalls nicht vom Dienst gesichert. 

Administratoren und Benutzer sind für die Sicherung und Wiederherstellung von IaaS- und PaaS-Ressourcen verantwortlich, die separat von den Infrastruktursicherungsprozessen erfolgen muss. Informationen zum Sichern von IaaS- und PaaS-Ressourcen finden Sie unter folgenden Links:

- [Virtuelle Computer](../user/azure-stack-manage-vm-protect.md)
- [App Service](https://docs.microsoft.com/azure/app-service/manage-backup)
- [SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview)


## <a name="enable-or-reconfigure-backup"></a>Sicherung aktivieren oder neu konfigurieren

1. Öffnen Sie das [Azure Stack-Verwaltungsportal](azure-stack-manage-portals.md).
2. Wählen Sie **Alle Dienste** aus, und wählen Sie dann unter der Kategorie **VERWALTUNG** die Option **Infrastruktursicherung** aus. Wählen Sie auf dem Blatt **Infrastructure Backup** die Option **Konfiguration** aus.
3. Geben Sie den Pfad zu dem **Sicherungsspeicherort** ein. Verwenden Sie für den Pfad zu einer auf einem separaten Gerät gehosteten Dateifreigabe eine UNC-Zeichenfolge (Universal Naming Convention). Eine UNC-Zeichenfolge gibt den Speicherort von Ressourcen (z.B. freigegebene Dateien oder Geräte) an. Für den Dienst können Sie eine IP-Adresse verwenden. Zur Sicherstellung der Verfügbarkeit der Sicherungsdaten nach einem Notfall sollte sich das Gerät an einem gesonderten Speicherort befinden.

    > [!Note]  
    > Anstelle der IP-Adresse können Sie einen vollqualifizierten Domänennamen (FQDN) verwenden, wenn in Ihrer Umgebung die Namensauflösung vom Azure Stack-Infrastrukturnetzwerk in die Unternehmensumgebung unterstützt wird.

4. Geben Sie den **Benutzernamen** ein, und verwenden Sie dabei die Domäne und den Benutzernamen mit ausreichendem Zugriff zum Lesen und Schreiben von Dateien. Beispiel: `Contoso\backupshareuser`.
5. Geben Sie das **Kennwort** für den Benutzer ein.
6. Geben Sie das Kennwort erneut ein, um das **Kennwort zu bestätigen**.
7. Die **Häufigkeit in Stunden** bestimmt, wie oft Sicherungen erstellt werden. Der Standardwert ist 12. Scheduler unterstützt maximal 12 und mindestens 4. 
8. Die **Vermerkdauer in Tagen** bestimmt, wie viele Tage Sicherungen am externen Speicherort beibehalten werden. Der Standardwert ist 7. Scheduler unterstützt maximal 14 und mindestens 2. Nach Ablauf ihrer Vermerkdauer werden Sicherungen automatisch aus dem externen Speicherort gelöscht.

    > [!Note]  
    > Wenn Sie Sicherungen nach Ablauf ihrer Vermerkdauer archivieren möchten, achten Sie darauf, die Dateien zu sichern, bevor der Scheduler die Sicherungen löscht. Wenn Sie die Vermerkdauer reduzieren (z.B. von 7 auf 5 Tage) löscht der Scheduler alle Sicherungen, die älter als neue Vermerkdauer sind. Stellen Sie sicher, dass das Löschen von Sicherungen keine Probleme verursacht, bevor Sie diesen Wert aktualisieren. 

9. Geben Sie in den Verschlüsselungseinstellungen ein Zertifikat im CER-Dateifeld „Zertifikat“ an. Sicherungsdateien werden mit dem öffentlichen Schlüssel im Zertifikat verschlüsselt. Sie sollten ein Zertifikat bereitstellen, das nur den öffentlichen Schlüsselteil enthält, wenn Sie die Sicherungseinstellungen konfigurieren. Nachdem Sie dieses Zertifikat zum ersten Mal festgelegt haben oder das Zertifikat in der Zukunft rotieren, können Sie nur den Fingerabdruck des Zertifikats anzeigen. Sie können die hochgeladene Zertifikatdatei nicht herunterladen oder anzeigen. Um die Zertifikatdatei zu erstellen, führen Sie den folgenden PowerShell-Befehl aus, um ein selbstsigniertes Zertifikat mit den öffentlichen und privaten Schlüsseln zu erstellen, und exportieren Sie ein Zertifikat mit nur dem öffentlichen Schlüsselteil.

    ```powershell

        $cert = New-SelfSignedCertificate `
            -DnsName "www.contoso.com" `
            -CertStoreLocation "cert:\LocalMachine\My"

        New-Item -Path "C:\" -Name "Certs" -ItemType "Directory" 
        Export-Certificate `
            -Cert $cert `
            -FilePath c:\certs\AzSIBCCert.cer 
    ```

   > [!Note]
   > **1901 und höher**: Azure Stack akzeptiert ein Zertifikat zum Verschlüsseln von Infrastruktursicherungsdaten. Stellen Sie sicher, dass das Zertifikat mit dem öffentlichen und privaten Schlüssel an einem sicheren Ort gespeichert wird. Aus Gründen der Sicherheit wird nicht empfohlen, das Zertifikat mit den öffentlichen und privaten Schlüsseln zum Konfigurieren von Sicherungseinstellungen zu verwenden. Weitere Informationen zum Verwalten des Lebenszyklus dieses Zertifikats finden Sie unter [Bewährte Methoden für den Infrastructure Backup-Dienst](azure-stack-backup-best-practices.md).
   > 
   > **Bis 1811:** Azure Stack akzeptiert einen symmetrischen Schlüssel für die Verschlüsselung von Infrastruktursicherungsdaten. Verwenden Sie das [Cmdlet „New-AzsEncryptionKey64“ zum Erstellen eines Schlüssels](https://docs.microsoft.com/powershell/module/azs.backup.admin/new-azsencryptionkeybase64). Nach dem Upgrade von 1811 auf 1901 wird der Verschlüsselungsschlüssel in den Sicherungseinstellungen gespeichert. Es empfiehlt sich, die Sicherungseinstellungen zu aktualisieren und ein Zertifikat zu verwenden. Die Verschlüsselungsschlüsselunterstützung ist nun veraltet. Sie haben mindestens drei Releases Zeit, um die Einstellungen zu aktualisieren und ein Zertifikat zu verwenden. 

10. Wählen Sie **OK** aus, um die Backup Controller-Einstellungen zu speichern.

![Azure Stack – Backup Controller-Einstellungen](media/azure-stack-backup/backup-controller-settings-certificate.png)


## <a name="start-backup"></a>Sicherung starten
Zum Starten einer Sicherung klicken Sie auf **Jetzt sichern**, um eine bedarfsgesteuerte Sicherung zu starten. Eine bedarfsgesteuerte Sicherung ändert die Zeit für die nächste geplante Sicherung nicht. Nachdem die Aufgabe abgeschlossen ist, können Sie die Einstellungen in der **Zusammenfassung** bestätigen:

![Azure Stack: Bedarfsgesteuerte Sicherung](media/azure-stack-backup/scheduled-backup.png)

Sie können auch das PowerShell-Cmdlet **Start-AzsBackup** auf Ihrem Azure Stack-Verwaltungscomputer ausführen. Weitere Informationen finden Sie unter [Sichern von Azure Stack](azure-stack-backup-back-up-azure-stack.md).

## <a name="enable-or-disable-automatic-backups"></a>Aktivieren oder Deaktivieren von automatischen Sicherungen
Wenn Sie die Sicherung aktivieren, werden Sicherungen automatisch geplant. Den Zeitpunkt der nächsten geplanten Sicherung finden Sie in der **Zusammenfassung**. 

![Azure Stack: Bedarfsgesteuerte Sicherung](media/azure-stack-backup/on-demand-backup.png)

Wenn Sie künftige geplante Sicherungen deaktivieren möchten, klicken Sie auf **Automatische Sicherungen deaktivieren**. Beim Deaktivieren automatischer Sicherungen bleiben die Sicherungseinstellungen einschließlich des Sicherungszeitplans konfiguriert. Mit diese Aktion wird der Scheduler lediglich angewiesen, künftige Sicherungen zu überspringen. 

![Azure Stack: Geplante Sicherungen deaktivieren](media/azure-stack-backup/disable-auto-backup.png)

Stellen Sie in der **Zusammenfassung** sicher, dass künftige geplante Sicherungen deaktiviert wurden:

![Azure Stack: Sicherstellen, dass Sicherungen deaktiviert wurden](media/azure-stack-backup/confirm-disable.png)

Klicken Sie auf **Automatische Sicherungen aktivieren**, um den Scheduler anzuweisen, zur geplanten Zeit mit künftigen Sicherungen zu beginnen. 

![Azure Stack: Geplante Sicherungen aktivieren](media/azure-stack-backup/enable-auto-backup.png)


> [!Note]  
> Wenn Sie die Infrastruktursicherung vor dem Update auf 1807 konfiguriert haben, werden automatische Sicherungen deaktiviert. So stehen die von Azure Stack gestarteten Sicherungen nicht mit Sicherungen in Konflikt, die von einem externen Aufgabenplanungsmodul gestartet wurden. Nachdem Sie alle externen Aufgabenplanungen deaktiviert haben, klicken Sie auf **Automatische Sicherungen aktivieren**.

## <a name="update-backup-settings"></a>Aktualisieren der Sicherungseinstellungen
Seit 1901 ist die Unterstützung für Verschlüsselungsschlüssel veraltet. Wenn Sie die Sicherung zum ersten Mal in 1901 konfigurieren, müssen Sie ein Zertifikat verwenden. Azure Stack unterstützt Verschlüsselungsschlüssel nur dann, wenn der Schlüssel vor dem Aktualisieren auf 1901 konfiguriert wurde. Der Abwärtskompatibilitätsmodus gilt für drei Releases. Danach werden Verschlüsselungsschlüssel nicht mehr unterstützt. 

### <a name="default-mode"></a>Standardmodus
Wenn Sie die Infrastruktursicherung zum ersten Mal nach der Installation von oder Aktualisierung auf 1901 konfigurieren, müssen Sie die Sicherung mit einem Zertifikat konfigurieren. Die Verwendung eines Verschlüsselungsschlüssels wird nicht mehr unterstützt. 

Um das zum Verschlüsseln von Sicherungsdaten verwendete Zertifikat zu aktualisieren, können Sie eine neue CER-Datei mit dem öffentlichen Schlüsselteil hochladen und „OK“ auswählen, um die Einstellungen zu speichern. 

Neue Sicherungen beginnen mit der Verwendung des öffentlichen Schlüssels in dem neuen Zertifikat. Dies hat keine Auswirkungen auf alle vorhandenen Sicherungen, die mit dem vorherigen Zertifikat erstellt wurden. Stellen Sie sicher, dass Sie das ältere Zertifikat an einem sicheren Ort aufbewahren, falls Sie es für die Cloudwiederherstellung benötigen.

![Azure Stack: Zertifikatfingerabdruck anzeigen](media/azure-stack-backup/encryption-settings-thumbprint.png)

### <a name="backwards-compatibility-mode"></a>Abwärtskompatibilitätsmodus
Wenn Sie die Sicherung vor der Aktualisierung auf 1901 konfiguriert haben, werden die Einstellungen ohne Änderungen im Verhalten übernommen. In diesem Fall wird der Verschlüsselungsschlüssel aus Gründen der Abwärtskompatibilität unterstützt. Sie haben die Möglichkeit, den Verschlüsselungsschlüssel zu aktualisieren oder zur Verwendung eines Zertifikats zu wechseln. Sie haben mindestens drei Releases Zeit, um die Aktualisierung des Verschlüsselungsschlüssels fortzusetzen. Nutzen Sie diesen Zeitraum, um zu einem Zertifikat überzugehen. Verwenden Sie zum Erstellen eines neuen Verschlüsselungsschlüssels das [Cmdlet „New-AzsEncryptionKeyBase64“](https://docs.microsoft.com/powershell/module/azs.backup.admin/new-azsencryptionkeybase64).

![Azure Stack: Verwenden des Verschlüsselungsschlüssels im Abwärtskompatibilitätsmodus](media/azure-stack-backup/encryption-settings-backcompat-encryption-key.png)

> [!Note]  
> Das Aktualisieren vom Verschlüsselungsschlüssel auf ein Zertifikat ist ein unidirektionaler Vorgang. Nach dieser Änderung können Sie nicht wieder zum Verschlüsselungsschlüssel zurückwechseln. Alle vorhandene Sicherungen bleibt mit dem vorherigen Verschlüsselungsschlüssel verschlüsselt. 

![Azure Stack: Verwenden des Verschlüsselungszertifikats im Abwärtskompatibilitätsmodus](media/azure-stack-backup/encryption-settings-backcompat-certificate.png)

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie eine Sicherung ausführen. Siehe hierzu [Sichern von Azure Stack](azure-stack-backup-back-up-azure-stack.md).

Erfahren Sie, wie Sie sicherstellen, dass die Sicherung ausgeführt wurde. Siehe hierzu [Bestätigen der erfolgreichen Sicherung im Verwaltungsportal](azure-stack-backup-back-up-azure-stack.md).
