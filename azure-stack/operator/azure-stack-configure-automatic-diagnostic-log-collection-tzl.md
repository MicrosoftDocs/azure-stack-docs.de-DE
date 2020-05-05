---
title: Proaktives Sammeln von Diagnoseprotokollen im Falle einer Warnung in Azure Stack Hub
description: Hier erfahren Sie, wie Sie die proaktive Protokollsammlung in „Hilfe und Support“ von Azure Stack Hub konfigurieren.
author: justinha
ms.topic: article
ms.date: 04/17/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 04/17/2020
ms.openlocfilehash: 636a3a62b60092e4869197432884533fac57c788
ms.sourcegitcommit: 355e21dd9b8c3f44e14abaae0b4f176443cf7495
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81625008"
---
# <a name="send-azure-stack-hub-diagnostic-logs-proactively"></a>Proaktives Senden von Azure Stack Hub-Diagnoseprotokollen

::: moniker range=">= azs-2002"

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

::: moniker-end
::: moniker range="<= azs-1910"

Es wird empfohlen, die Funktion zur automatischen Sammlung von Diagnoseprotokollen zu konfigurieren, um die Protokollsammlung und den Kundensupport zu optimieren. 
Wenn die Integritätsbedingungen des Systems untersucht werden müssen, können die Protokolle automatisch zur Analyse durch die Microsoft Customer Support Services (CSS) hochgeladen werden. 

## <a name="create-an-azure-blob-container-sas-url"></a>Erstellen einer SAS-URL für Azure-Blobcontainer 

Bevor Sie die automatische Protokollsammlung konfigurieren können, müssen Sie eine SAS (Shared Access Signature) für einen Blobcontainer abrufen. Mit einer SAS können Sie Zugriff auf Ressourcen unter Ihrem Speicherkonto erteilen, ohne dafür Kontoschlüssel weitergeben zu müssen. 
Sie können Azure Stack Hub-Protokolldateien in einem Blobcontainer in Azure speichern und dann die SAS-URL übergeben, über die CSS die Protokolle sammeln kann. 

### <a name="prerequisites"></a>Voraussetzungen

Sie können einen neuen oder vorhandenen Blobcontainer in Azure verwenden. 
Um einen Blobcontainer in Azure zu erstellen, müssen Sie mindestens über die [Rolle „Mitwirkender an Speicher-Blobs“](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) oder die [spezifische Berechtigung](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations) verfügen. 
Globale Administratoren verfügen ebenfalls über die erforderliche Berechtigung. 

Bewährte Methoden zum Wählen von Parametern für das Speicherkonto für die automatische Protokollsammlung finden Sie unter [Bewährte Methoden für die automatische Azure Stack Hub-Protokollsammlung](azure-stack-best-practices-automatic-diagnostic-log-collection.md). Weitere Informationen zu Typen von Speicherkonten finden Sie unter [Azure Storage-Konto – Übersicht](https://docs.microsoft.com/azure/storage/common/storage-account-overview)

### <a name="create-a-blob-storage-account"></a>Erstellen eines Blob Storage-Kontos
 
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Klicken Sie auf **Speicherkonten** > **Hinzufügen**. 
1. Erstellen Sie einen Blobcontainer mit diesen Einstellungen:
   - **Abonnement**: Wählen Sie Ihr Azure-Abonnement aus.
   - **Ressourcengruppe**: Geben Sie eine Ressourcengruppe an.
   - **Speicherkontoname**: Geben Sie einen eindeutigen Speicherkontonamen an.
   - **Standort**: Wählen Sie in Übereinstimmung mit Ihrer Unternehmensrichtlinie ein Rechenzentrum aus.
   - **Leistung**: Standard
   - **Kontoart**: StorageV2 (universell v2) 
   - **Replikation**: Lokal redundanter Speicher (LRS)
   - **Zugriffsebene**: Kalt

   ![Screenshot mit der Darstellung der Blobcontainer-Eigenschaften](media/azure-stack-automatic-log-collection/azure-stack-log-collection-create-storage-account.png)

1. Klicken Sie auf **Überprüfen + erstellen** und dann auf **Erstellen**.  

### <a name="create-a-blob-container"></a>Erstellen eines Blobcontainers 

1. Klicken Sie nach der erfolgreichen Bereitstellung auf **Zu Ressource wechseln**. Sie können das Speicherkonto auch für den bequemen Zugriff am Dashboard anheften. 
1. Klicken Sie auf **Storage-Explorer (Vorschau)** , klicken Sie mit der rechten Maustaste auf **Blobcontainer**, und klicken Sie auf **Blobcontainer erstellen**. 
1. Geben Sie einen Namen für den neuen Container ein, und klicken Sie auf **OK**.

## <a name="create-a-sas-url"></a>Erstellen einer SAS-URL

1. Klicken Sie mit der rechten Maustaste auf den Container, und klicken Sie auf **Shared Access Signature abrufen**. 
   
   ![Screenshot, der das Abrufen der Shared Access Signature eines Blobcontainers darstellt](media/azure-stack-automatic-log-collection/get-sas.png)

1. Wählen Sie diese Eigenschaften aus:
   - Startzeit: Optional können Sie die Startzeit zurück verlegen. 
   - Ablaufzeit: Zwei Jahre
   - Zeitzone: UTC
   - Berechtigungen: Lesen, schreiben und auflisten

   ![Screenshot mit Darstellung der Shared Access Signature-Eigenschaften](media/azure-stack-automatic-log-collection/sas-properties.png) 

1. Klicken Sie auf **Erstellen**.  

Kopieren Sie die URL, und geben Sie sie beim [Konfigurieren der automatischen Protokollsammlung](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md) ein. 
Weitere Informationen zu SAS-URLs finden Sie unter [Verwenden von Shared Access Signatures (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1). 


## <a name="steps-to-configure-automatic-log-collection"></a>Schritte zum Konfigurieren der automatischen Protokollsammlung

Führen Sie diese Schritte aus, um der Benutzeroberfläche der Protokollsammlung die SAS-URL hinzuzufügen: 

1. Melden Sie sich beim Azure Stack Hub-Administratorportal an.
1. Öffnen Sie **Hilfe und Support – Übersicht**.
1. Klicken Sie auf **Einstellungen der automatischen Sammlung**.

   ![Screenshot, der darstellt, wo die Protokollsammlung in „Hilfe und Support“ aktiviert wird](media/azure-stack-automatic-log-collection/azure-stack-automatic-log-collection.png)

1. Legen Sie die automatische Protokollsammlung auf **Aktiviert** fest.
1. Geben Sie die SAS-URL (Shared Access Signature) des Speicherkonto-Blobcontainers ein.

   ![Screenshot mit Darstellung der Blob-SAS-URL](media/azure-stack-automatic-log-collection/azure-stack-enable-automatic-log-collection.png)

>[!NOTE]
>Die automatische Protokollsammlung kann jederzeit deaktiviert und wieder aktiviert werden. Die SAS-URL-Konfiguration ändert sich dadurch nicht. Wenn die automatische Protokollsammlung wieder aktiviert wird, durchläuft die zuvor eingegebene SAS-URL die gleichen Überprüfungen, und eine abgelaufene SAS-URL wird abgelehnt. 


::: moniker-end

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

|Titel der Warnung  |    FaultIdType|    
|-------------|------------|
|Es konnte keine Verbindung mit dem Remotedienst hergestellt werden |    UsageBridge.NetworkError|
|Fehler bei Update |    Urp.UpdateFailure    |          
|Infrastruktur oder Abhängigkeiten für Speicherressourcenanbieter nicht verfügbar |    StorageResourceProviderDependencyUnavailable     |     
|Knoten nicht mit Controller verbunden|    ServerHostNotConnectedToController     |     
|Fehler bei Routenveröffentlichung |    SlbMuxRoutePublicationFailure |    
|Interner Datenspeicher des Speicherressourcenanbieters nicht verfügbar |    StorageResourceProvider. DataStoreConnectionFail     |       
|Speichergerätfehler    | Microsoft.Health.FaultType.VirtualDisks.Detached     |      
|Health Controller kann nicht auf das Speicherkonto zugreifen | Microsoft.Health.FaultType.StorageError |    
|Konnektivität mit einem physischen Datenträger ist verloren gegangen |    Microsoft.Health.FaultType.PhysicalDisk.LostCommunication     |    
|Blobdienst wird auf einem Knoten nicht ausgeführt |    StorageService.The.blob.service.is.not.running.on.a.node-Critical |    
|Infrastrukturrolle fehlerhaft |    Microsoft.Health.FaultType.GenericExceptionFault |        
|Fehler bei Tabellenspeicherdienst |    StorageService.Table.service.errors-Critical |              
|Dateifreigabe ist zu mehr als 80 % belegt |    Microsoft.Health.FaultType.FileShare.Capacity.Warning.Infra |       
|Skalierungseinheitknoten ist offline.    | FRP.Heartbeat.PhysicalNode |    
|Infrastrukturrolleninstanz nicht verfügbar |    FRP.Heartbeat.InfraVM    |    
|Infrastrukturrolleninstanz nicht verfügbar  |    FRP.Heartbeat.NonHaVm     |        
|Infrastrukturrolle „Verzeichnisverwaltung“ hat Fehler bei der Zeitsynchronisierung gemeldet |    DirectoryServiceTimeSynchronizationError |       
|Pending external certificate expiration (Bevorstehender Ablauf eines externen Zertifikats) |    CertificateExpiration.ExternalCert.Warning |
|Pending external certificate expiration (Bevorstehender Ablauf eines externen Zertifikats) |    CertificateExpiration.ExternalCert.Critical |
|Virtuelle Computer können aufgrund einer niedrigen Arbeitsspeicherkapazität für bestimmte Klassen und Größen nicht bereitgestellt werden |    AzureStack.ComputeController.VmCreationFailure.LowMemory |
|Auf Knoten kann für Platzierung des virtuellen Computers nicht zugegriffen werden. |    AzureStack.ComputeController.HostUnresponsive |    
|Fehler bei Sicherung  | AzureStack.BackupController.BackupFailedGeneralFault |    
|Geplante Sicherung wurde aufgrund eines Konflikts mit fehlerhaften Vorgängen übersprungen    | AzureStack.BackupController.BackupSkippedWithFailedOperationFault |    


## <a name="see-also"></a>Weitere Informationen

[Verarbeiten von Azure Stack Hub-Protokoll- und -Kundendaten](azure-stack-data-collection.md)





