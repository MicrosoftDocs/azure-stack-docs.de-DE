---
title: Anwenden von Updates in Azure Stack | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Microsoft-Updates für ein integriertes Azure Stack-System importieren und installieren.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2019
ms.author: mabrigg
ms.reviewer: justini
ms.lastreviewed: 02/11/2019
ms.openlocfilehash: 124dc554e18f6d387a3e41d37809d43276094879
ms.sourcegitcommit: 0d8ccf2a32b08ab9bcbe13d54c7c3dce2379757f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/25/2019
ms.locfileid: "64490124"
---
# <a name="apply-updates-in-azure-stack"></a>Anwenden von Updates in Azure Stack

*Gilt für: Integrierte Azure Stack-Systeme*

Sie können im Verwaltungsportal über die Kachel **Aktualisieren** Microsoft- oder OEM-Updatepakete für Azure Stack anwenden.

Wenn Sie eine integrierte Systemversion 1807 oder höher verwenden, müssen Sie das Updatepaket herunterladen, die Paketdateien in Azure Stack importieren und das Update dann installieren. Anweisungen hierzu finden Sie unter [Aktualisieren von Azure Stack durch Herunterladen des Pakets](#update-azure-stack-by-downloading-the-package).

Diese Upgradeanweisungen funktionieren nur mit integrierten Azure Stack-Systemen. Wenn Sie das Azure Stack-Entwicklungssystem verwenden, müssen Sie das Installationspaket für die aktuelle Version herunterladen. Anleitungen finden Sie unter [Installieren des Azure Stack Development Kits](../asdk/asdk-install.md).

## <a name="update-azure-stack"></a>Aktualisieren von Azure Stack

### <a name="select-and-apply-an-update-package"></a>Auswählen und Anwenden eines Updatepakets

1. Öffnen Sie das Verwaltungsportal.

2. Wählen Sie **Dashboard** aus. Wählen Sie die Kachel **Update** aus.

    ![Azure Stack-Update verfügbar](media/azure-stack-apply-updates/azure-stack-updates-1901-dashboard.png)

3. Notieren Sie sich die aktuelle Version Ihres Azure Stacks. Sie können auf die nächsten Vollversion aktualisieren. Wenn Sie beispielsweise Azure Stack 1811 ausführen, ist die nächste veröffentlichte Version 1901.

    ![Azure Stack-Update anwenden](media/azure-stack-apply-updates/azure-stack-updates-1901-updateavailable.png)

4. Wählen Sie die nächste verfügbare Version in der Liste „Updates“ aus. Sie können in der Spalte mit den Versionshinweisen **Anzeigen** auswählen, um das Thema mit den Versionshinweisen für die Version anzuzeigen, wenn Sie die Versionsänderungen überprüfen möchten.

5. Wählen Sie jetzt das Update aus. Das Update wird gestartet.

### <a name="review-update-history"></a>Prüfen des Updateverlaufs

1. Öffnen Sie das Verwaltungsportal.

2. Wählen Sie **Dashboard** aus. Wählen Sie die Kachel **Update** aus.

3. Wählen Sie **Updateverlauf** aus.

![Azure Stack-Updateverlauf](media/azure-stack-apply-updates/azure-stack-update-history.PNG)

## <a name="update-azure-stack-by-downloading-the-package"></a>Aktualisieren von Azure Stack durch Herunterladen des Pakets

Wenn Sie eine integrierte Systemversion 1807 oder höher verwenden, müssen Sie das Updatepaket herunterladen, die Paketdateien in Azure Stack importieren und das Update dann installieren.

## <a name="download-the-update-package"></a>Herunterladen des Updates

Wenn ein Microsoft- oder OEM-Updatepaket für Azure Stack verfügbar ist, laden Sie das Paket an einen Speicherort herunter, auf den von Azure Stack aus zugegriffen werden kann. Überprüfen Sie den Paketinhalt. Ein Updatepaket besteht in der Regel aus folgenden Dateien:

- Eine selbstextrahierende `<PackageName>.exe`-Datei. Diese Datei enthält die Nutzlast für das Update, beispielsweise das aktuelle kumulative Update für Windows Server.

- Entsprechende `<PackageName>.bin`-Dateien. Diese Dateien dienen zum Komprimieren der Nutzlast, die der Datei *Paketname.exe* zugeordnet ist.

- Eine `Metadata.xml`-Datei. Diese Datei enthält wichtige Informationen zum Update, etwa Herausgeber, Name, Voraussetzungen, Größe und Supportpfad-URL.

> [!IMPORTANT]  
> Nachdem das Updatepaket für Azure Stack 1901 angewendet wurde, ändert sich das Paketformat für Azure Stack-Updatepakete von EXE, BIN(s) und XML in ZIP(s) und XML. Azure Stack-Betreiber, die verbundene Zeitstempel besitzen, sind davon nicht betroffen. Azure Stack-Betreiber, die getrennt sind, importieren einfach die XML- und ZIP-Dateien, indem sie denselben Prozess wie unten beschrieben verwenden.

## <a name="import-and-install-updates"></a>Importieren und Installieren von Updates

Das folgende Verfahren zeigt, wie Updatepakete im Administratorportal importiert und installiert werden.

> [!IMPORTANT]  
> Es wird dringend empfohlen, dass Sie die Benutzer über alle Wartungsvorgänge unterrichten und normale Wartungsfenster so weit wie möglich außerhalb der Geschäftszeiten planen. Wartungsvorgänge können sowohl Benutzerworkloads als auch Portalvorgänge beeinträchtigen.

1. Wählen Sie im Administratorportal **Alle Dienste** aus. Wählen Sie anschließend in der Kategorie **Daten und Speicher** die Option **Speicherkonten** aus. (Oder beginnen Sie im Filterfeld mit der Eingabe von **Speicherkonten**, und wählen Sie dann die entsprechende Option aus.)

    ![Hier sehen Sie, wo Sie Speicherkonten im Portal finden.](media/azure-stack-apply-updates/ApplyUpdates1.png)

2. Geben Sie in das Filterfeld **update** ein, und wählen Sie das Speicherkonto **updateadminaccount**.

3. Klicken Sie in den Speicherkontodetails unter **Dienste** auf **Blobs**.
 
    ![Hier sehen Sie, wie Sie „Blobs“ für das Speicherkonto auswählen.](media/azure-stack-apply-updates/ApplyUpdates3.png) 

4. Klicken Sie unter **Blob-Dienst** auf **+ Container**, um einen Container zu erstellen. Geben Sie einen Namen (etwa *Update-1811*) ein, und wählen Sie dann **OK**.
 
     ![Hier sehen Sie, wie Sie einen Container im Speicherkonto hinzufügen.](media/azure-stack-apply-updates/ApplyUpdates4.png)

5. Klicken Sie nach dem Erstellen des Containers auf den Containernamen und dann auf **Hochladen**, um die Paketdateien in den Container hochzuladen.
 
    ![Hier sehen Sie, wie Sie die Paketdateien hochladen.](media/azure-stack-apply-updates/ApplyUpdates5.png)

6. Klicken Sie unter **Blob hochladen** auf das Ordnersymbol, navigieren Sie zur EXE-Datei des Updatepakets, und klicken Sie dann im Explorer-Fenster auf **Öffnen**.
  
7. Klicken Sie unter **Blob hochladen** auf **Hochladen**.
  
    ![Hier sehen Sie, wo die einzelnen Paketdateien hochgeladen werden.](media/azure-stack-apply-updates/ApplyUpdates6.png)

8. Wiederholen Sie die Schritte 6 und 7 für die Dateien *Paketname.bin* und „Metadata.xml“. Falls die Datei „Supplemental Notice.txt“ enthalten ist, importieren Sie sie nicht.
9. Anschließend können Sie die Benachrichtigungen (Glockensymbol in der oberen rechten Ecke des Portals) überprüfen. In den Benachrichtigungen sollte angegeben sein, dass der Upload abgeschlossen ist.
10. Kehren Sie zur Kachel „Aktualisieren“ auf dem Dashboard zurück. Auf der Kachel sollte angegeben sein, dass ein Update verfügbar ist. Klicken Sie auf die Kachel, um das neu hinzugefügte Updatepaket zu überprüfen.
11. Um das Update zu installieren, wählen Sie das als **Bereit** markierte Paket aus. Klicken Sie dann entweder mit der rechten Maustaste auf das Paket, und wählen Sie **Jetzt aktualisieren**, oder klicken Sie im oberen Bereich auf die Aktion **Jetzt aktualisieren**.
12. Wenn Sie auf „Update wird installiert...“ klicken, können Sie den Status im Bereich **Update run details** (Updateausführungsdetails) anzeigen. Hier können Sie auch auf **Download full logs** (Vollständige Protokolle herunterladen) klicken, um die Protokolldateien herunterzuladen.
13. Wenn das Update abgeschlossen ist, wird auf der Kachel „Aktualisieren“ die aktualisierte Azure Stack-Version angezeigt.

Sie können Updates manuell aus dem Speicherkonto löschen, nachdem sie in Azure Stack installiert wurden. Azure Stack führt in regelmäßigen Abständen eine Überprüfung auf ältere Updatepakete durch und entfernt diese aus dem Speicher. Es kann in Azure Stack bis zu zwei Wochen dauern, bis die alten Pakete entfernt werden.

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht zum Verwalten von Updates in Azure Stack](azure-stack-updates.md)
- [Azure Stack-Wartungsrichtlinie](azure-stack-servicing-policy.md)
