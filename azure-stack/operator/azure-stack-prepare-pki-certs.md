---
title: Vorbereiten von Azure Stack Hub-PKI-Zertifikaten für die Bereitstellung oder Rotation
titleSuffix: Azure Stack Hub
description: Hier erfahren Sie, wie Sie PKI-Zertifikate für die Bereitstellung integrierter Azure Stack Hub-Systeme oder für die Rotation von Geheimnissen in einer vorhandenen Azure Stack Hub-Umgebung vorbereiten.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 09/16/2019
ms.author: inhenkel
ms.reviewer: ppacent
ms.lastreviewed: 09/16/2019
ms.openlocfilehash: 321ceff84ec24997866366c533eb31939acde2dd
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/20/2020
ms.locfileid: "77509720"
---
# <a name="prepare-azure-stack-hub-pki-certificates-for-deployment-or-rotation"></a>Vorbereiten von Azure Stack Hub-PKI-Zertifikaten für die Bereitstellung oder Rotation

Die [von der Zertifizierungsstelle Ihrer Wahl bezogenen](azure-stack-get-pki-certs.md) Zertifikatdateien müssen mit Eigenschaften importiert und exportiert werden, die die Zertifikatanforderungen von Azure Stack Hub erfüllen.

## <a name="prepare-certificates-for-deployment"></a>Vorbereiten von Zertifikaten für die Bereitstellung

Führen Sie die folgenden Schritte aus, um die Azure Stack Hub-PKI-Zertifikate vorzubereiten und zu überprüfen, die für die Bereitstellung einer neuen Azure Stack Hub-Umgebung oder für die Geheimnisrotation in einer vorhandenen Azure Stack Hub-Umgebung verwendet werden.

### <a name="import-the-certificate"></a>Importieren des Zertifikats

1. Kopieren Sie die ursprünglichen Zertifikatsversionen, [die Sie von der Zertifizierungsstelle Ihrer Wahl bezogen haben](azure-stack-get-pki-certs.md), in ein Verzeichnis auf dem Bereitstellungshost. 
   > [!WARNING]
   > Kopieren Sie keine Dateien, die bereits importiert/exportiert wurden oder aus einem anderen Grund nicht mehr den Dateien entsprechen, die direkt von der Zertifizierungsstelle bereitgestellt wurden.

1. Klicken Sie mit der rechten Maustaste auf das Zertifikat, und wählen Sie **Zertifikat installieren** oder **PFX installieren** aus – je nachdem, wie das Zertifikat von Ihrer Zertifizierungsstelle übermittelt wurde.

1. Wählen Sie im **Zertifikatimport-Assistenten** als Importspeicherort die Option **Lokaler Computer** aus. Wählen Sie **Weiter** aus. Wählen Sie auf dem folgenden Bildschirm erneut „Weiter“ aus.

    ![Importspeicherort „Lokaler Computer“ für das Zertifikat](./media/prepare-pki-certs/1.png)

1. Wählen Sie **Alle Zertifikate in folgendem Speicher speichern** und anschließend **Enterprise Trust** als Speicherort aus. Wählen Sie **OK** aus, um das Dialogfeld für die Zertifikatspeicherauswahl zu schließen, und wählen Sie anschließend **Weiter** aus.

   ![Konfigurieren des Zertifikatspeichers für den Zertifikatimport](./media/prepare-pki-certs/3.png)

   a. Wenn Sie eine PFX-Datei importieren, wird Ihnen ein zusätzliches Dialogfeld angezeigt. Geben Sie auf der Seite **Schutz für den privaten Schlüssel** das Kennwort für Ihre Zertifikatdateien ein, und aktivieren Sie die Option **Schlüssel als exportierbar markieren. Dadurch können Sie Ihre Schlüssel später sichern oder transportieren**. Wählen Sie **Weiter** aus.

   ![Markieren des Schlüssels als exportierbar](./media/prepare-pki-certs/2.png)

1. Wählen Sie **Fertig stellen** aus, um den Import abzuschließen.

> [!NOTE]
> Nachdem Sie ein Zertifikat für Azure Stack Hub importiert haben, wird der private Schlüssel des Zertifikats als PKCS 12-Datei (PFX) im Clusterspeicher gespeichert.

### <a name="export-the-certificate"></a>Zertifikat exportieren

Öffnen Sie die MMC-Konsole des Zertifikat-Managers, und stellen Sie eine Verbindung mit dem Zertifikatspeicher des lokalen Computers her.

1. Öffnen Sie die Microsoft Management Console (MMC). Klicken Sie zum Öffnen der Konsole unter Windows 10 mit der rechten Maustaste auf das **Startmenü**, und wählen Sie **Ausführen** aus. Geben Sie dann **mmc** ein, und drücken Sie die EINGABETASTE.

2. Wählen Sie **Datei** > **Snap-In hinzufügen/entfernen** aus. Wählen Sie dann **Zertifikate** > **Hinzufügen** aus.

    ![Hinzufügen des Zertifikat-Snap-Ins in der Microsoft Management Console](./media/prepare-pki-certs/mmc-2.png)

3. Wählen Sie **Computerkonto** und dann **Weiter** aus. Wählen Sie **Lokaler Computer** und dann **Fertig stellen** aus. Wählen Sie **OK** aus, um die Seite „Snap-In hinzufügen/entfernen“ zu schließen.

    ![Auswählen des Kontos für das Hinzufügen des Zertifikat-Snap-Ins in der Microsoft Management Console](./media/prepare-pki-certs/mmc-3.png)

4. Navigieren Sie zu **Zertifikate** > **Organisationsvertrauen** > **Zertifikatspeicherort**. Stellen Sie sicher, dass das Zertifikat auf der rechten Seite angezeigt wird.

5. Wählen Sie auf der Taskleiste der Zertifikat-Manager-Konsole **Aktionen** > **Alle Aufgaben** > **Exportieren** aus. Wählen Sie **Weiter** aus.

   > [!NOTE]
   > Je nachdem, über wie viele Azure Stack Hub-Zertifikate Sie verfügen, müssen Sie diesen Vorgang möglicherweise mehrmals ausführen.

6. Wählen Sie **Ja, privaten Schlüssel exportieren** aus, und klicken Sie dann auf **Weiter**.

7. Gehen Sie im Abschnitt für das Exportdateiformat wie folgt vor:
    
   - Wählen Sie **Wenn möglich, alle Zertifikate im Zertifizierungspfad einbeziehen**.  
   - Wählen Sie **Alle erweiterten Eigenschaften exportieren**.  
   - Wählen Sie **Zertifikatdatenschutz aktivieren**.  
   - Klicken Sie auf **Weiter**.  
    
     ![Zertifikatexport-Assistent mit ausgewählten Optionen](./media/prepare-pki-certs/azure-stack-save-cert.png)

8. Klicken Sie auf **Kennwort**, und geben Sie ein Kennwort für die Zertifikate an. Erstellen Sie ein Kennwort, das die folgenden Anforderungen an die Kennwortkomplexität erfüllt:

    * Mindestlänge von 8 Zeichen.
    * Mindestens drei der folgenden Elemente: Großbuchstaben, Kleinbuchstaben, Zahlen von 0–9, Sonderzeichen, alphabetische Zeichen, die weder Groß- noch Kleinbuchstaben sind.

    Notieren Sie sich dieses Kennwort. Sie verwenden es als Bereitstellungsparameter.

9. Wählen Sie **Weiter** aus.

10. Wählen Sie einen Dateinamen und einen Speicherort für die zu exportierende PFX-Datei aus. Wählen Sie **Weiter** aus.

11. Wählen Sie **Fertig stellen** aus.

## <a name="next-steps"></a>Nächste Schritte

[Überprüfen von Azure Stack-PKI-Zertifikaten](azure-stack-validate-pki-certs.md)