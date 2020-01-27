---
title: Aktualisieren von Windows Defender Antivirus
titleSuffix: Azure Stack Hub
description: Hier erfahren Sie, wie Sie Windows Defender Antivirus in Azure Stack Hub aktualisieren.
services: azure-stack
author: justinha
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 12/04/2019
ms.author: justinha
ms.reviewer: fiseraci
ms.lastreviewed: 12/04/2019
ms.openlocfilehash: a631d700bc83e4fabeca4472f5d2a81c411ddc9b
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75821085"
---
# <a name="update-windows-defender-antivirus-on-azure-stack-hub"></a>Aktualisieren von Windows Defender Antivirus in Azure Stack Hub

[Windows Defender Antivirus](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10) ist eine Antischadsoftwarelösung, die Sicherheit und Virenschutz bereitstellt. Jede Azure Stack Hub-Infrastrukturkomponente (Hyper-V-Hosts und virtuelle Computer) wird mit Windows Defender Antivirus geschützt. Für einen aktuellen Schutz benötigen Sie regelmäßige Updates für die Windows Defender Antivirus-Definitionen, -Engine und -Plattform. Wie Updates angewendet werden, hängt von Ihrer Konfiguration ab.

## <a name="connected-scenario"></a>Verbundenes Szenario

Der Azure Stack Hub-[Updateressourcenanbieter](azure-stack-updates.md#the-update-resource-provider) lädt mehrmals täglich Antischadsoftwaredefinitionen und Engineupdates herunter. Jede Azure Stack Hub-Infrastrukturkomponente erhält das Update vom Updateressourcenanbieter und wendet das Update automatisch an.

Wenden Sie für die Azure Stack Hub-Bereitstellungen, die mit dem öffentlichen Internet verbunden sind, das [monatliche Azure Stack Hub-Update](azure-stack-apply-updates.md) an. Das monatliche Azure Stack Hub-Update umfasst Windows Defender Antivirus-Plattformupdates für den Monat.

## <a name="disconnected-scenario"></a>Nicht verbundenes Szenario

Bei Azure Stack Hub-Bereitstellungen, die nicht mit dem öffentlichen Internet verbunden sind (etwa Rechenzentren mit Air Gap), haben Kunden ab Release 1910 die Möglichkeit, die Antischadsoftwaredefinitionen und Engineupdates bei ihrer Veröffentlichung anzuwenden. 

Wenn Sie die Updates auf Ihre Azure Stack Hub-Lösung anwenden möchten, müssen Sie sie zuerst von der Microsoft-Website (Links siehe unten) herunterladen und anschließend unter *updateadminaccount* in einen Speicherblobcontainer importieren. Eine geplante Aufgabe überprüft den Blobcontainer alle 30 Minuten. Werden neue Defender-Definitionen und Engineupdates gefunden, werden diese auf die Azure Stack Hub-Infrastruktur angewendet. 

Bei getrennten Bereitstellungen, die noch nicht unter 1910 oder einer höheren Version ausgeführt werden oder bei denen Defender-Definitionen und Engineupdates nicht täglich heruntergeladen werden können, beinhaltet das monatliche Azure Stack Hub-Update die Updates für die Windows Defender Antivirus-Definitionen, -Engine und Plattform für den jeweiligen Monat. 


### <a name="set-up-windows-defender-for-manual-updates"></a>Einrichten von Windows Defender für manuelle Updates 

Im Release 1910 wurden zwei neue Cmdlets zum privilegierten Endpunkt hinzugefügt, um die manuelle Aktualisierung von Windows Defender in Azure Stack Hub zu konfigurieren. 

```powershell 
### cmdlet to configure the storage blob container for the Defender updates 
Set-AzsDefenderManualUpdate [-Container <string>] [-Remove]  
### cmdlet to retrieve the configuration of the Defender manual update settings 
Get-AzsDefenderManualUpdate  
``` 

Das folgende Verfahren zeigt, wie Sie die manuelle Aktualisierung von Windows Defender einrichten. 

1. Stellen Sie eine Verbindung mit dem privilegierten Endpunkt her, und führen Sie das folgende Cmdlet aus, um den Namen des Speicherblobcontainers anzugeben, in den Defender-Updates hochgeladen werden. 

   > [!NOTE] 
   > Das nachfolgend beschriebene Verfahren für die manuelle Aktualisierung funktioniert nur in getrennten Umgebungen, in denen der Zugriff auf „go.microsoft.com“ nicht zulässig ist. Bei der Ausführung des Cmdlets „Set-AzsDefenderManualUpdate“ in verbundenen Umgebungen tritt ein Fehler auf. 

   ```powershell 
   ### Configure the storage blob container for the Defender updates 
   Set-AzsDefenderManualUpdate -Container <yourContainerName>
   ``` 

2. Laden Sie die zwei Windows Defender-Updatepakete herunter, und speichern Sie sie an einem Speicherort, auf den über das Azure Stack Hub-Verwaltungsportal zugegriffen werden kann.  

   * mpam-fe.exe von [https://go.microsoft.com/fwlink/?LinkId=121721&arch=x64](https://go.microsoft.com/fwlink/?LinkId=121721&arch=x64) 
   * nis_full.exe von [https://go.microsoft.com/fwlink/?LinkId=197094](https://go.microsoft.com/fwlink/?LinkId=197094) 

   > [!NOTE] 
   > Sie müssen diese beiden Dateien **jedes Mal** herunterladen, wenn Sie die Defender-Signaturen aktualisieren möchten. 

3. Wählen Sie im Verwaltungsportal **Alle Dienste** aus. Wählen Sie anschließend in der Kategorie **Daten und Speicher** die Option **Speicherkonten** aus. (Oder beginnen Sie im Filterfeld mit der Eingabe von **Speicherkonten**, und wählen Sie dann die entsprechende Option aus.) 

   ![Azure Stack Hub Defender: Alle Dienste](./media/azure-stack-security-av/image1.png)  

4. Geben Sie in das Filterfeld **update** ein, und wählen Sie das Speicherkonto **updateadminaccount**. 

5. Klicken Sie in den Speicherkontodetails unter **Dienste** auf **Blobs**. 

   ![Azure Stack Hub Defender: Alle Dienste](./media/azure-stack-security-av/image2.png) 

6. Wählen Sie unter **Blob-Dienst** die Option **+ Container** aus, um einen Container zu erstellen. Geben Sie den Namen (in diesem Beispiel *defenderupdates*) ein, der mit „Set-AzsDefenderManualUpdate“ angegeben wurde, und wählen Sie anschließend **OK** aus. 

   ![Azure Stack Hub Defender: Container](./media/azure-stack-security-av/image3.png) 

7. Klicken Sie nach dem Erstellen des Containers auf den Containernamen und dann auf **Hochladen**, um die Paketdateien in den Container hochzuladen. 

   ![Azure Stack Hub Defender: Hochladen](./media/azure-stack-security-av/image4.png) 

8. Klicken Sie unter **Blob hochladen** auf das Ordnersymbol, navigieren Sie zur Updatedatei *mpam-fe.exe*, und klicken Sie dann im Explorer-Fenster auf **Öffnen**. 

9. Klicken Sie unter **Blob hochladen** auf **Hochladen**. 

   ![Azure Stack Hub Defender: Blob1 hochladen](./media/azure-stack-security-av/image5.png) 

1. Wiederholen Sie die Schritte 8 und 9 für die Datei *nis_full.exe*. 

   ![Azure Stack Hub Defender: Blob2 hochladen](./media/azure-stack-security-av/image6.png)

Eine geplante Aufgabe überprüft den Blobcontainer alle 30 Minuten und wendet alle neuen Windows Defender-Pakete an.  

## <a name="next-steps"></a>Nächste Schritte

[Weitere Informationen zur Sicherheit von Azure Stack Hub](azure-stack-security-foundations.md)
