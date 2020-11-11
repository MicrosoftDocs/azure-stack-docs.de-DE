---
title: Aktivieren der Sicherung für Azure Stack über das Administratorportal | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den Infrastructure Backup-Dienst über das Administratorportal aktivieren, sodass Azure Stack bei einem Fehler wiederhergestellt werden kann.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 12/16/2019
ms.openlocfilehash: 3864183ecda856500db1fcbfe38df84d70f3bae9
ms.sourcegitcommit: 9ecf9c58fbcc4bc42c1fdc688f370c643c761a29
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93330224"
---
# <a name="configure-backup-for-azure-stack-hub-from-the-administrator-portal"></a>Konfigurieren der Sicherung für Azure Stack Hub über das Administratorportal

*Anwendungsbereich: Modular Data Center, Azure Stack Hub Ruggedized*

Sie können den Infrastructure Backup-Dienst über das Administratorportal so konfigurieren, dass Infrastruktursicherungen an einen externen Speicherort exportiert werden. Infrastruktursicherungen können vom Support verwendet werden, um Probleme bei beeinträchtigten Diensten zu beheben.

## <a name="configure-backup"></a>Konfigurieren der Sicherung

1. Öffnen Sie das [Azure Stack-Administratorportal](../../operator/azure-stack-manage-portals.md).

2. Klicken Sie auf **Alle Dienste** und dann in der Kategorie **Verwaltung** auf **Infrastructure Backup**. Wählen Sie auf dem Blatt **Infrastructure Backup** die Option **Konfiguration** aus.

3. Geben Sie den Pfad zu dem **Sicherungsspeicherort** ein. Verwenden Sie für den Pfad zu einer auf einem separaten Gerät gehosteten Dateifreigabe eine UNC-Zeichenfolge (Universal Naming Convention). Eine UNC-Zeichenfolge gibt den Speicherort von Ressourcen (z.B. freigegebene Dateien oder Geräte) an. Für den Dienst können Sie eine IP-Adresse verwenden. Zur Sicherstellung der Verfügbarkeit der Sicherungsdaten nach einem Notfall sollte sich das Gerät an einem gesonderten Ort befinden.

    > [!NOTE]  
    > Anstelle der IP-Adresse können Sie einen vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) verwenden, wenn in Ihrer Umgebung die Namensauflösung vom Azure Stack-Infrastrukturnetzwerk in die Unternehmensumgebung unterstützt wird.

4. Geben Sie den **Benutzernamen** ein, und verwenden Sie dabei die Domäne und den Benutzernamen mit ausreichendem Zugriff zum Lesen und Schreiben von Dateien, z. B. **Contoso\backupshareuser**.

5. Geben Sie das **Kennwort** für den Benutzer ein.

6. Geben Sie das Kennwort erneut ein, um das **Kennwort zu bestätigen**.

7. Die **Häufigkeit in Stunden** bestimmt, wie oft Sicherungen erstellt werden. Der Standardwert ist 12. Der Scheduler unterstützt mindestens 4 und maximal 12.

8. Die **Vermerkdauer in Tagen** bestimmt, wie viele Tage Sicherungen am externen Speicherort beibehalten werden. Der Standardwert ist 7. Der Scheduler unterstützt mindestens 2 und maximal 14. Nach Ablauf ihrer Vermerkdauer werden Sicherungen automatisch aus dem externen Speicherort gelöscht.

   > [!NOTE]
   > Wenn Sie Sicherungen nach Ablauf ihrer Vermerkdauer archivieren möchten, achten Sie darauf, die Dateien zu sichern, bevor der Scheduler die Sicherungen löscht. Wenn Sie den Aufbewahrungszeitraum für Sicherungen verkürzen (z. B. von 7 auf 5 Tage), löscht der Scheduler alle Sicherungen, die älter als die Dauer des neuen Aufbewahrungszeitraums sind. Stellen Sie sicher, dass das Löschen von Sicherungen keine Probleme verursacht, bevor Sie diesen Wert aktualisieren.

9. In den **Verschlüsselungseinstellungen** ist der öffentliche Zertifikatfingerabdruck für das bei der Bereitstellung zur Verfügung gestellte Zertifikat angegeben. Eine Aktualisierung des vorhandenen Zertifikats ist nicht erforderlich.

10. Wählen Sie **OK** aus, um die Backup Controller-Einstellungen zu speichern.

    ![Azure Stack – Backup Controller-Einstellungen](media/azure-stack-backup-enable-backup-console-tzl/backup-controller-settings-certificate.png)

## <a name="enable-or-disable-automatic-backups"></a>Aktivieren oder Deaktivieren von automatischen Sicherungen

Sicherungen werden bei der Bereitstellung automatisch aktiviert. Nachdem Sie den Speicherort konfiguriert haben, exportieren automatische Sicherungen die Sicherungsdateien basierend auf der Häufigkeitseinstellung einem Zeitplan entsprechend an einen externen Speicherort. Den Zeitpunkt der nächsten geplanten Sicherung finden Sie auf dem Blatt **Zusammenfassung**.

![Azure Stack: Bedarfsgesteuerte Sicherung](media/azure-stack-backup-enable-backup-console-tzl/on-demand-backup.png)

Wenn Sie künftige geplante Sicherungen deaktivieren müssen, klicken Sie auf **Disable Automatic Backups** (Automatische Sicherungen deaktivieren). Beim Deaktivieren automatischer Sicherungen bleiben die Sicherungseinstellungen einschließlich des Sicherungszeitplans konfiguriert. Durch diese Aktion wird der Scheduler angewiesen, künftige Sicherungen zu überspringen.

![Azure Stack: Geplante Sicherungen deaktivieren](media/azure-stack-backup-enable-backup-console-tzl/disable-auto-backup.png)

Stellen Sie in der **Zusammenfassung** sicher, dass künftige geplante Sicherungen deaktiviert wurden:

![Azure Stack: Sicherstellen, dass Sicherungen deaktiviert wurden](media/azure-stack-backup-enable-backup-console-tzl/confirm-disable.png)

Klicken Sie auf **Enable Automatic Backups** (Automatische Sicherungen aktivieren), um den Scheduler anzuweisen, zur geplanten Zeit mit künftigen Sicherungen zu beginnen.

![Azure Stack: Geplante Sicherungen aktivieren](media/azure-stack-backup-enable-backup-console-tzl/enable-auto-backup.png)

## <a name="update-backup-settings"></a>Aktualisieren der Sicherungseinstellungen

Um das zum Verschlüsseln von Sicherungsdaten verwendete Zertifikat zu aktualisieren, laden Sie eine neue CER-Datei mit dem öffentlichen Schlüsselteil hoch und wählen „OK“ aus, um die Einstellungen zu speichern.

Neue Sicherungen beginnen mit der Verwendung des öffentlichen Schlüssels in dem neuen Zertifikat. Dies hat keine Auswirkungen auf alle vorhandenen Sicherungen, die mit dem vorherigen Zertifikat erstellt wurden. Stellen Sie sicher, dass Sie das ältere Zertifikat an einem sicheren Ort aufbewahren, falls Sie es für die Cloudwiederherstellung benötigen.

![Azure Stack: Zertifikatfingerabdruck anzeigen](media/azure-stack-backup-enable-backup-console-tzl/encryption-settings-thumbprint.png)

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Überprüfen, ob eine Sicherung durchgeführt wurde, finden Sie unter [Sicherstellen im Administratorportal, dass die Sicherung abgeschlossen wurde](../../operator/azure-stack-backup-back-up-azure-stack.md).
