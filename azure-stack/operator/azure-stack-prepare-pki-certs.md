---
title: Vorbereiten von PKI-Zertifikaten für Azure Stack für die Bereitstellung von in Azure Stack integrierten Systemen oder die Geheimnisrotation | Microsoft-Dokumentation
description: Beschreibt die Vorgehensweise zum Vorbereiten von Azure Stack-PKI-Zertifikaten für in Azure Stack integrierte Systeme.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2019
ms.author: justinha
ms.reviewer: ppacent
ms.lastreviewed: 09/16/2019
ms.openlocfilehash: 38175eee8be9b8f678405e0ad1ec6f01bdba8b77
ms.sourcegitcommit: ca5025fb04250271fe0b2b2df8ad0b3b9ed3e604
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/16/2019
ms.locfileid: "71020842"
---
# <a name="prepare-azure-stack-pki-certificates-for-use-in-deployment-or-rotation"></a>Vorbereiten von Azure Stack-PKI-Zertifikaten für die Verwendung bei der Bereitstellung oder Rotation

Die [von der Zertifizierungsstelle Ihrer Wahl bezogenen](azure-stack-get-pki-certs.md) Zertifikatdateien müssen mit Eigenschaften importiert und exportiert werden, die mit den Zertifikatanforderungen von Azure Stack in Einklang stehen.

## <a name="prepare-certificates-for-deployment"></a>Vorbereiten von Zertifikaten für die Bereitstellung

Verwenden Sie die folgenden Schritte zum Vorbereiten und Überprüfen der Azure Stack-PKI-Zertifikate, die für die Bereitstellung einer neuen Azure Stack-Umgebung oder für die Geheimnisrotation in einer vorhandenen Azure Stack-Umgebung verwendet werden. 


### <a name="import-the-certificate"></a>Importieren des Zertifikats

1. Kopieren Sie die ursprünglichen Zertifikatsversionen, [die Sie von der Zertifizierungsstelle Ihrer Wahl bezogen haben](azure-stack-get-pki-certs.md), in ein Verzeichnis auf dem Bereitstellungshost. 
   > [!WARNING]
   > Kopieren Sie keine Dateien, die bereits importiert/exportiert wurden oder aus einem anderen Grund nicht mehr den Dateien entsprechen, die direkt von der Zertifizierungsstelle bereitgestellt wurden.

1. Klicken Sie mit der rechten Maustaste auf das Zertifikat, und wählen Sie **Zertifikat installieren** oder **PFX installieren** aus – je nachdem, wie das Zertifikat von Ihrer Zertifizierungsstelle übermittelt wurde.

1. Wählen Sie im **Zertifikatimport-Assistenten** als Importspeicherort die Option **Lokaler Computer** aus. Klicken Sie auf **Weiter**. Klicken Sie auf dem folgenden Bildschirm erneut auf „Weiter“.

    ![Importspeicherort „Lokaler Computer“](./media/prepare-pki-certs/1.png)

1. Wählen Sie **Alle Zertifikate in folgendem Speicher speichern** und anschließend **Enterprise Trust** als Speicherort aus. Klicken Sie auf **OK**, um das Dialogfeld für die Zertifikatspeicherauswahl zu schließen, und klicken Sie anschließend auf **Weiter**.

   ![Konfigurieren des Zertifikatspeichers](./media/prepare-pki-certs/3.png)

   a. Wenn Sie eine PFX-Datei importieren, wird Ihnen ein zusätzliches Dialogfeld angezeigt. Geben Sie auf der Seite **Schutz für den privaten Schlüssel** das Kennwort für Ihre Zertifikatdateien ein, und aktivieren Sie die Option **Schlüssel als exportierbar markieren. Dadurch können Sie Ihre Schlüssel später sichern oder transportieren**. Klicken Sie auf **Weiter**.

   ![Markieren des Schlüssels als exportierbar](./media/prepare-pki-certs/2.png)

1. Klicken Sie auf „Fertig stellen“, um den Import abzuschließen.

> [!NOTE]
> Nachdem Sie ein Zertifikat für Azure Stack importiert haben, wird der private Schlüssel des Zertifikats als PKCS 12-Datei (PFX) im Clusterspeicher gespeichert.

### <a name="export-the-certificate"></a>Zertifikat exportieren

Öffnen Sie die MMC-Konsole des Zertifikat-Managers, und stellen Sie eine Verbindung mit dem Zertifikatspeicher des lokalen Computers her.

1. Öffnen Sie Microsoft Management Console; klicken Sie in Windows 10 mit der rechten Maustaste auf das Startmenü, und klicken Sie auf „Ausführen“. Geben Sie **mmc** ein, und klicken Sie auf „OK“.

1. Klicken Sie auf „Datei“, „Snap-In hinzufügen/entfernen“, wählen Sie „Zertifikate“ aus, und klicken Sie auf „Hinzufügen“.

    ![Hinzufügen von Zertifikate-Snap-In](./media/prepare-pki-certs/mmc-2.png)
 
1. Wählen Sie „Computerkonto“ aus, klicken Sie auf „Weiter“, wählen Sie „Lokaler Computer“ und dann „Fertig stellen“ aus. Klicken Sie auf „OK“, um die Seite „Snap-In hinzufügen/entfernen“ zu schließen.

    ![Hinzufügen von Zertifikate-Snap-In](./media/prepare-pki-certs/mmc-3.png)

1. Navigieren Sie zu „Zertifikate“ > „Organisationsvertrauen“ > „Zertifikatspeicherort“. Stellen Sie sicher, dass das Zertifikat auf der rechten Seite angezeigt wird.

1. Klicken Sie auf der Taskleiste der Zertifikat-Manager-Konsole auf **Aktionen** > **Alle Aufgaben** > **Exportieren**. Klicken Sie auf **Weiter**.

   > [!NOTE]
   > Je nachdem, über wie viele Azure Stack-Zertifikate Sie verfügen, müssen Sie diesen Vorgang möglicherweise mehrere Male abschließen.

1. Wählen Sie **Ja, privaten Schlüssel exportieren** aus, und klicken Sie dann auf **Weiter**.

1. Gehen Sie im Abschnitt für das Exportdateiformat wie folgt vor:
    
   - Wählen Sie **Wenn möglich, alle Zertifikate im Zertifizierungspfad einbeziehen**.  
   - Wählen Sie **Alle erweiterten Eigenschaften exportieren**.  
   - Wählen Sie **Zertifikatdatenschutz aktivieren**.  
   - Klicken Sie auf **Weiter**.  
    
     ![Zertifikatexport-Assistent mit ausgewählten Optionen](./media/prepare-pki-certs/azure-stack-save-cert.png)

1. Klicken Sie auf **Kennwort**, und geben Sie ein Kennwort für die Zertifikate an. Erstellen Sie ein Kennwort, das die folgenden Anforderungen an die Komplexität von Kennwörtern erfüllt. Mindestlänge von 8 Zeichen. Das Kennwort enthält mindestens drei der folgenden Elemente: Großbuchstaben, Kleinbuchstaben, Zahlen von 0–9, Sonderzeichen, alphabetische Zeichen, die weder Groß- noch Kleinbuchstaben sind. Notieren Sie sich dieses Kennwort. Sie verwenden es noch als Bereitstellungsparameter.

1. Klicken Sie auf **Weiter**.

1. Wählen Sie einen Dateinamen und einen Speicherort für die zu exportierende PFX-Datei aus. Klicken Sie auf **Weiter**.

1. Wählen Sie **Fertig stellen** aus.

## <a name="next-steps"></a>Nächste Schritte

[Überprüfen von Azure Stack-PKI-Zertifikaten](azure-stack-validate-pki-certs.md)