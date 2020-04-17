---
title: Proaktives Sammeln von Diagnoseprotokollen im Falle einer Warnung in Azure Stack Hub
description: Hier erfahren Sie, wie Sie die proaktive Protokollsammlung in „Hilfe und Support“ von Azure Stack Hub konfigurieren.
author: justinha
ms.topic: article
ms.date: 02/12/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 02/12/2020
ms.openlocfilehash: 0c6bfe4dba2b3795e1069419d90624c8e3b55cda
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "79520543"
---
# <a name="send-azure-stack-hub-diagnostic-logs-proactively"></a>Proaktives Senden von Azure Stack Hub-Diagnoseprotokollen

Die proaktive Sammlung von Diagnoseprotokollen im Falle einer Warnung in Azure Stack Hub ermöglicht die Beschleunigung des Kundensupports.
Wenn die Integritätsbedingungen des Systems untersucht werden müssen, können die Protokolle automatisch zur Analyse hochgeladen werden, bevor eine Supportanfrage für den Microsoft-Kundensupport (Customer Support Services, CSS) erstellt wird. 

## <a name="steps-to-configure-proactive-log-collection"></a>Schritte zum Konfigurieren der proaktiven Protokollsammlung

Gehen Sie zum Konfigurieren der proaktiven Protokollsammlung wie folgt vor. Die automatische Protokollsammlung kann jederzeit deaktiviert und wieder aktiviert werden.  

1. Melden Sie sich beim Azure Stack Hub-Administratorportal an.
1. Öffnen Sie **Hilfe und Support – Übersicht**.
1. Wenn das Banner angezeigt wird, klicken Sie auf **Enable proactive log collection** (Proaktive Protokollsammlung aktivieren). 

   ![Screenshot, der darstellt, wo die Protokollsammlung in „Hilfe und Support“ aktiviert wird](media/azure-stack-help-and-support/banner-enable-automatic-log-collection.png)


   Sie können auch auf **Einstellungen** klicken, **Proactive log collection** (Proaktive Protokollsammlung) auf **Aktivieren** festlegen und anschließend auf **Speichern** klicken.

   ![Screenshot, der darstellt, wo die Protokollsammlung in „Hilfe und Support“ aktiviert wird](media/azure-stack-help-and-support/settings-enable-automatic-log-collection.png)


## <a name="view-log-collection"></a>Anzeigen der Protokollsammlung

Der Verlauf der von Azure Stack Hub gesammelten Protokolle wird auf der Seite **Protokollsammlung** in Hilfe und Support mit den folgenden Daten und Uhrzeiten angezeigt:

- **Time Collected** (Zeitpunkt der Sammlung): Zeitpunkt, zu dem der Sammlungsvorgang begonnen hat
- **Status:** Entweder „In Bearbeitung“ oder „Abgeschlossen“
- **Logs start** (Beginn der Protokolle): Beginn des Zeitraums, für den Sie die Sammlung ausführen möchten.
- **Logs end** (Ende der Protokolle): Ende des Zeitraums
- **Typ:** Gibt an, ob es sich um eine manuelle oder um eine proaktive Protokollsammlung handelt 


![Screenshot mit Darstellung von Protokollsammlungen](media/azure-stack-help-and-support/azure-stack-log-collection.png)


## <a name="proactive-diagnostic-log-collection-alerts"></a>Warnungen für die proaktive Sammlung von Diagnoseprotokollen 

Bei aktivierter proaktiver Protokollsammlung werden Protokolle hochgeladen, wenn eines der folgenden Ereignisse ausgelöst wird. 

**Fehler bei Update** ist beispielsweise eine Warnung, durch die eine proaktive Sammlung von Diagnoseprotokollen ausgelöst wird. Ist die Funktion aktiviert, werden Diagnoseprotokolle bei einem Updatefehler proaktiv gesammelt, um CSS bei der Behandlung des Problems zu unterstützen. Die Diagnoseprotokolle werden nur erfasst, wenn die Warnung für **Fehler bei Update** ausgelöst wird. 

|Titel der Warnung  | FaultIdType|    
|-------------|------------|
|Es konnte keine Verbindung mit dem Remotedienst hergestellt werden |  UsageBridge.NetworkError|
|Fehler bei Update |    Urp.UpdateFailure   |          
|Infrastruktur oder Abhängigkeiten für Speicherressourcenanbieter nicht verfügbar |  StorageResourceProviderDependencyUnavailable     |     
|Knoten nicht mit Controller verbunden|  ServerHostNotConnectedToController   |     
|Fehler bei Routenveröffentlichung |    SlbMuxRoutePublicationFailure | 
|Interner Datenspeicher des Speicherressourcenanbieters nicht verfügbar |    StorageResourceProvider. DataStoreConnectionFail     |       
|Speichergerätfehler | Microsoft.Health.FaultType.VirtualDisks.Detached   |      
|Health Controller kann nicht auf das Speicherkonto zugreifen | Microsoft.Health.FaultType.StorageError |    
|Konnektivität mit einem physischen Datenträger ist verloren gegangen |    Microsoft.Health.FaultType.PhysicalDisk.LostCommunication    |    
|Blobdienst wird auf einem Knoten nicht ausgeführt | StorageService.The.blob.service.is.not.running.on.a.node-Critical | 
|Infrastrukturrolle fehlerhaft |    Microsoft.Health.FaultType.GenericExceptionFault |        
|Fehler bei Tabellenspeicherdienst | StorageService.Table.service.errors-Critical |              
|Dateifreigabe ist zu mehr als 80 % belegt |    Microsoft.Health.FaultType.FileShare.Capacity.Warning.Infra |       
|Skalierungseinheitknoten ist offline. | FRP.Heartbeat.PhysicalNode |  
|Infrastrukturrolleninstanz nicht verfügbar | FRP.Heartbeat.InfraVM   |    
|Infrastrukturrolleninstanz nicht verfügbar  |    FRP.Heartbeat.NonHaVm     |        
|Infrastrukturrolle „Verzeichnisverwaltung“ hat Fehler bei der Zeitsynchronisierung gemeldet |  DirectoryServiceTimeSynchronizationError |     
|Pending external certificate expiration (Bevorstehender Ablauf eines externen Zertifikats) |  CertificateExpiration.ExternalCert.Warning |
|Pending external certificate expiration (Bevorstehender Ablauf eines externen Zertifikats) |  CertificateExpiration.ExternalCert.Critical |
|Virtuelle Computer können aufgrund einer niedrigen Arbeitsspeicherkapazität für bestimmte Klassen und Größen nicht bereitgestellt werden |  AzureStack.ComputeController.VmCreationFailure.LowMemory |
|Auf Knoten kann für Platzierung des virtuellen Computers nicht zugegriffen werden. |  AzureStack.ComputeController.HostUnresponsive | 
|Fehler bei Sicherung  | AzureStack.BackupController.BackupFailedGeneralFault |    
|Geplante Sicherung wurde aufgrund eines Konflikts mit fehlerhaften Vorgängen übersprungen  | AzureStack.BackupController.BackupSkippedWithFailedOperationFault |   


## <a name="see-also"></a>Weitere Informationen

[Verarbeiten von Azure Stack Hub-Protokoll- und -Kundendaten](azure-stack-data-collection.md)





