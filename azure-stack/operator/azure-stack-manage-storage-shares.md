---
title: Verwalten der Speicherkapazität in Azure Stack Hub
description: Erfahren Sie, wie Sie die Speicherkapazität und -verfügbarkeit in Azure Stack Hub überwachen und verwalten können.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: inhenkel
ms.reviewer: xiaofmao
ms.lastreviewed: 10/16/2020
ms.openlocfilehash: bbced92ca9eb275ed1599ff7422bde1601be11c0
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94545498"
---
# <a name="manage-storage-capacity-for-azure-stack-hub"></a>Verwalten der Speicherkapazität für Azure Stack Hub

Dieser Artikel hilft Azure Stack Hub-Cloudoperatoren bei der Überwachung und Verwaltung der Speicherkapazität ihrer Azure Stack Hub-Bereitstellung. Die Azure Stack Hub-Speicherinfrastruktur ordnet einen Teil der Gesamtspeicherkapazität der Azure Stack Hub-Bereitstellung als Speicherdienste zu. Speicherdienste speichern Daten eines Mandanten in Freigaben auf Volumes, die den Knoten der Bereitstellung entsprechen.

Cloudbetreibern steht nur eine begrenzte Menge an Speicherplatz zur Verfügung. Die Speicherplatzmenge hängt von der implementierten Lösung ab. Die Lösung wird entweder von Ihrem OEM-Anbieter (bei Verwendung einer Lösung mit mehreren Knoten) oder von der Hardware bereitgestellt, auf der Sie das Azure Stack Development Kit (ASDK) installieren.

Azure Stack Hub unterstützt nur die Erweiterung der Speicherkapazität durch Hinzufügen zusätzlicher Skalierungseinheitenknoten. Weitere Informationen finden Sie unter [Hinzufügen zusätzlicher Knoten einer Skalierungseinheit in Azure Stack Hub](azure-stack-add-scale-node.md). Durch Hinzufügen physischer Datenträger zu den Knoten wird die Speicherkapazität nicht vergrößert.

Es ist wichtig, den verfügbaren Speicher zu [überwachen](#monitor-shares), um sicherzustellen, dass effiziente Vorgänge beibehalten werden. Sollte die freie Kapazität eines Volumes knapp werden, sollten Sie den [verfügbaren Speicherplatz verwalten](#manage-available-space), um eine Erschöpfung der Kapazität von Freigaben zu verhindern.

Zu den Optionen für die Kapazitätsverwaltung gehören folgende:


- Freigeben von Kapazität
- Migrieren von Speicherobjekten

Wenn eine Objektspeichervolume vollständig belegt ist, funktioniert der Speicherdienst für dieses Volume nicht mehr. Wenden Sie sich an den Support von Microsoft, um Unterstützung bei der Wiederherstellung des Betriebs für das Volume zu erhalten.

## <a name="understand-volumes-and-shares-containers-and-disks"></a>Grundlegendes zu Volumes und Freigaben, Containern und Datenträgern

### <a name="volumes-and-shares"></a>Volumes und Freigaben

Der *Speicherdienst* partitioniert den verfügbaren Speicher in separate, gleiche Volumes, die zum Speichern von Mandantendaten zugewiesen werden. Weitere Informationen zu Volumes in Azure Stack Hub finden Sie unter [Verwalten der Speicherinfrastruktur für Azure Stack Hub](azure-stack-manage-storage-infrastructure.md).

Objektspeichervolumes enthalten Mandantendaten. Zu Mandantendaten zählen Seitenblobs, Blockblobs, Anfügeblobs, Tabellen, Warteschlangen, Datenbanken sowie dazugehörige Metadatenspeicher. Die Anzahl von Objektspeichervolumes entspricht der Anzahl von Knoten in der Azure Stack Hub-Bereitstellung:

- Bei einer Bereitstellung mit vier Knoten sind vier Objektspeichervolumes vorhanden. Bei einer Bereitstellung mit mehreren Knoten verringert sich die Anzahl von Volumes nicht, wenn ein Knoten entfernt wird oder fehlerhaft ist.
- Wenn Sie das ASDK verwenden, liegt ein einzelnes Volume mit einer einzelnen Freigabe vor.

Die Objektspeichervolumes sind ausschließlich für die Verwendung von Speicherdiensten vorgesehen. Dateien auf den Volumes dürfen nicht direkt geändert, hinzugefügt oder entfernt werden. Die auf diesen Volumes gespeicherten Dateien sollten nur von Speicherdiensten verwendet werden.

Da die Speicherobjekte (Blobs usw.) jeweils auf einem einzelnen Volume enthalten sind, darf die maximale Größe der jeweiligen Objekte die Größe eines Volumes nicht übersteigen. Die maximale Größe neuer Objekte hängt von der Restkapazität ab, die auf einem Volume zum Zeitpunkt der Objekterstellung in Form von nicht genutztem Speicherplatz zur Verfügung steht.

Wenn der freie Speicherplatz eines Objektspeichervolumes knapp wird und Aktionen zum [Freigeben](#reclaim-capacity) von Speicherplatz nicht erfolgreich oder verfügbar sind, können Azure Stack Hub-Cloudoperatoren die Speicherobjekte zwischen Volumes migrieren.

Informationen zur Verwendung von Blobspeicher in Azure Stack Hub durch Mandantenbenutzer finden Sie unter [Azure Stack Hub-Speicherdienste](../user/azure-stack-storage-overview.md).

### <a name="containers"></a>Container

Mandantenbenutzer erstellen Container, die dann zum Speichern von Blobdaten verwendet werden. Obwohl Benutzer entscheiden, in welchem Container Blobs platziert werden sollen, bestimmt der Speicherdienst mithilfe eines Algorithmus, auf welchem Volume der Container platziert wird. Der Algorithmus wählt in der Regel das Volume mit dem meisten verfügbaren Speicherplatz.  

Nachdem ein Blob in einem Container platziert wurde, kann dieses Blob weiter wachsen und mehr Speicherplatz benötigen. Wenn Sie weitere Blobs hinzufügen und vorhandene Blobs größer werden, verringert sich der verfügbare Speicherplatz des Volumes, das den entsprechenden Container enthält. 

Container sind nicht auf ein einzelnes Volume beschränkt. Wenn die kombinierten Blobdaten in einem Container 80 % oder mehr des verfügbaren Speicherplatzes beanspruchen, wechselt der Container in den *Überlaufmodus*. Im Überlaufmodus werden alle neuen Blobs, die in diesem Container erstellt werden, einem anderen Volume mit genügend Speicherplatz zugeordnet. Im Laufe der Zeit kann ein Container im Überlaufmodus über Blobs verfügen, die auf mehrere Volumes verteilt sind.

Bei einer Belegung von 80 Prozent (und anschließend 90 Prozent) des verfügbaren Speicherplatzes eines Volumes gibt das System [Warnungen](#storage-space-alerts) im Azure Stack Hub-Administratorportal aus. Cloudbetreiber sollten die verfügbare Speicherkapazität prüfen und eine Neuverteilung der Inhalte planen. Der Speicherdienst funktioniert nicht mehr, wenn ein Datenträger zu 100 % belegt ist. Dann werden keine weiteren Benachrichtigungen ausgegeben.

### <a name="disks"></a>Datenträger

In Azure Stack Hub können verwaltete und nicht verwaltete Datenträger auf VMs sowohl als Betriebssystemdatenträger als auch als Datenträger für Daten verwendet werden.

**Verwaltete Datenträger** vereinfachen die Datenträgerverwaltung für Azure-IaaS-VMs durch die Verwaltung der Speicherkonten, die den VM-Datenträgern zugeordnet sind. Sie müssen nur die Größe des von Ihnen benötigten Datenträgers angeben. Azure Stack Hub erstellt und verwaltet den Datenträger dann für Sie. Weitere Informationen finden Sie in der [Übersicht über verwaltete Datenträger](/azure/virtual-machines/windows/managed-disks-overview).

Es wird empfohlen, Managed Disks für virtuelle Computer zu verwenden, um die Verwaltung und den Kapazitätsausgleich zu erleichtern. Sie müssen vor der Verwendung von Managed Disks kein Speicherkonto und keine Container vorbereiten. Beim Erstellen mehrerer verwalteter Datenträger werden die Datenträger auf mehrere Volumes verteilt, wodurch die Kapazität der Volumes ausgeglichen werden kann.  

**Nicht verwaltete Datenträger** sind VHD-Dateien, die als Seitenblobs in Azure-Speicherkonten gespeichert sind. Die von Mandanten erstellten Seitenblobs werden als VM-Datenträger bezeichnet und in Containern in den Speicherkonten gespeichert. Es wird empfohlen, nicht verwaltete Datenträger nur für virtuelle Computer zu verwenden, die mit Drittanbietertools kompatibel sein müssen, die nur nicht verwaltete Azure-Datenträger unterstützen.

Für Mandanten empfiehlt es sich, die einzelnen Datenträger in separaten Containern zu platzieren, um die Leistung des virtuellen Computers zu verbessern.

- Jeder Container, der einen Datenträger oder Seitenblob einer VM enthält, wird als angefügter Container der VM betrachtet, zu der der Datenträger gehört.
- Ein Container, der keine Datenträger einer VM enthält, gilt als freier Container.

Bei angefügten Containern sind die Optionen für die Speicherplatzfreigabe eingeschränkt. Weitere Informationen finden Sie unter [Verteilen nicht verwalteter Datenträger](#distribute-unmanaged-disks).

>[!TIP]  
> Cloudoperatoren betreiben nicht verwaltete Datenträger nicht direkt. Diese sind an VMs angefügt, die Mandanten ggf. einem Container hinzufügen können. Zur Planung der Verwaltung von Speicherplatz auf Speicherfreigaben kann es jedoch hilfreich sein, mit der Beziehung zwischen nicht verwalteten Datenträgern und Containern/Freigaben vertraut zu sein.

::: moniker range="<azs-2002"
## <a name="monitor-shares"></a>Überwachen von Freigaben

Überwachen Sie Freigaben mithilfe von Azure PowerShell oder über das Administratorportal, um informiert zu sein, wenn nur noch wenig Speicherplatz zur Verfügung steht. Bei Verwendung des Portals erhalten Sie Benachrichtigungen zu Freigaben, bei denen der Speicherplatz knapp wird.

### <a name="use-powershell"></a>Verwenden von PowerShell

Als Cloudbetreiber können Sie die Speicherkapazität einer Freigabe mithilfe des PowerShell-Cmdlets `Get-AzsStorageShare` überwachen. Das Cmdlet gibt für jede der Freigaben den gesamten, zugeordneten und freien Speicherplatz (in Bytes) zurück.

![Beispiel: Zurückgeben von freiem Speicherplatz für Freigaben](media/azure-stack-manage-storage-shares/free-space.png)

- **Gesamtkapazität:** Dies ist der gesamte Speicherplatz (in Bytes), der auf der Freigabe zur Verfügung steht. Dieser Speicherplatz wird für Daten und Metadaten verwendet, die von den Speicherdiensten verwaltet werden.
- **Verwendete Kapazität:** Dies ist die Menge von Daten (in Bytes), die von allen Erweiterungen für die Dateien genutzt wird, in denen die Mandantendaten und die dazugehörigen Metadaten gespeichert sind.

### <a name="use-the-administrator-portal"></a>Verwenden des Administratorportals

Als Cloudbetreiber können Sie die Speicherkapazität für alle Freigaben im Administratorportal abrufen.

1. Melden Sie sich beim Administratorportal `https://adminportal.local.azurestack.external` an.
2. Wählen Sie **Alle Dienste** > **Speicher** > **Dateifreigaben** aus, um die Dateifreigabeliste mit den Nutzungsinformationen zu öffnen.

    ![Beispiel: Speicherdateifreigaben im Azure Stack Hub-Administratorportal](media/azure-stack-manage-storage-shares/storage-file-shares.png)

   - **Total**: Dies ist der gesamte Speicherplatz (in Bytes), der auf der Freigabe zur Verfügung steht. Dieser Speicherplatz wird für Daten und Metadaten verwendet, die von den Speicherdiensten verwaltet werden.
   - **Verwendet:** Dies ist die Menge von Daten (in Bytes), die von allen Erweiterungen für die Dateien genutzt wird, in denen die Mandantendaten und die dazugehörigen Metadaten gespeichert sind.

::: moniker-end
::: moniker range=">=azs-2002"

## <a name="monitor-volumes"></a>Überwachen von Volumes

Überwachen Sie Volumes mithilfe von PowerShell oder über das Administratorportal, um informiert zu sein, wenn nur noch wenig Speicherplatz zur Verfügung steht. Bei Verwendung des Portals erhalten Sie Warnungen zu Volumes, bei denen der Speicherplatz knapp wird.

### <a name="use-powershell"></a>Verwenden von PowerShell

Als Cloudoperator können Sie die Speicherkapazität eines Volumes mithilfe des PowerShell-Cmdlets `Get-AzsVolume` überwachen. Das Cmdlet gibt für jedes Volume den gesamten und freien Speicherplatz in Gigabyte (GB) zurück.

![Beispiel: Zurückgeben von freiem Speicherplatz für Volumes](media/azure-stack-manage-storage-shares/listvolumespowershell.png)

- **Gesamtkapazität:** Dies ist der gesamte Speicherplatz in GB, der auf der Freigabe zur Verfügung steht. Dieser Speicherplatz wird für Daten und Metadaten verwendet, die von den Speicherdiensten verwaltet werden.
- **Verbleibende Kapazität:** Der freie Speicherplatz in GB zum Speichern der Mandantendaten und der zugehörigen Metadaten

### <a name="use-the-administrator-portal"></a>Verwenden des Administratorportals

Als Cloudoperator können Sie die Speicherkapazität für alle Volumes im Administratorportal anzeigen.

1. Melden Sie sich beim Azure Stack Hub-Administratorportal (`https://adminportal.local.azurestack.external`) an.
2. Wählen Sie **Alle Dienste** > **Speicher** > **Volumes** aus, um die Volumeliste mit den Nutzungsinformationen zu öffnen.

    ![Beispiel: Speichervolumes im Azure Stack Hub-Administratorportal](media/azure-stack-manage-storage-shares/listvolumes.png)

   - **Total**: Der gesamte verfügbare Speicherplatz auf dem Volume. Dieser Speicherplatz wird für Daten und Metadaten verwendet, die von den Speicherdiensten verwaltet werden.
   - **Verwendet:** Dies ist die Menge von Daten, die von allen Erweiterungen für die Dateien genutzt wird, in denen die Mandantendaten und die dazugehörigen Metadaten gespeichert sind.

::: moniker-end

### <a name="storage-space-alerts"></a>Speicherplatzbenachrichtigungen

Bei Verwendung des Administratorportals erhalten Sie Benachrichtigungen zu Volumes, bei denen der Speicherplatz knapp wird.

> [!IMPORTANT]
> Achten Sie als Cloudbetreiber darauf, dass Freigaben nicht vollständig belegt sind. Wenn eine Freigabe vollständig belegt ist, funktioniert der Speicherdienst für diese Freigabe nicht mehr. Wenden Sie sich an den Support von Microsoft, um Speicherplatz freizugeben und wieder Vorgänge für eine vollständig ausgelastete Freigabe zu ermöglichen.

* **Warnung:** Wenn eine Dateifreigabe zu mehr als 80 % belegt ist, erhalten Sie im Administratorportal eine Benachrichtigung vom Typ *Warnung*:

  ![Beispiel: Benachrichtigung vom Typ „Warnung“ im Azure Stack Hub-Administratorportal](media/azure-stack-manage-storage-shares/alert-warning.png)

* **Kritisch**: Wenn eine Dateifreigabe zu mehr als 90 % belegt ist, erhalten Sie im Administratorportal eine Benachrichtigung vom Typ *Kritisch*:

  ![Beispiel: Benachrichtigung vom Typ „Kritisch“ im Azure Stack Hub-Administratorportal](media/azure-stack-manage-storage-shares/alert-critical.png)

* **Anzeigen von Details:** Im Administratorportal können Sie Details zu einer Benachrichtigung vom Typ „Warnung“ öffnen, um mögliche Maßnahmen anzuzeigen:

  ![Beispiel: Anzeigen von Warnungsdetails im Azure Stack Hub-Administratorportal](media/azure-stack-manage-storage-shares/alert-details.png)

## <a name="manage-available-space"></a>Verwalten des verfügbaren Speicherplatzes

Wenn Speicherplatz auf einem Volume freigegeben werden muss, beginnen Sie mit den am wenigsten invasiven Methoden. Versuchen Sie also beispielsweise zunächst, Speicherplatz freizugeben, bevor Sie sich dafür entscheiden, einen verwalteten Datenträger zu migrieren.  

### <a name="reclaim-capacity"></a>Freigeben von Kapazität

Sie können die Kapazität freigeben, die von gelöschten Mandantenkonten beansprucht wird. Diese Kapazität wird nach Ablauf des [Aufbewahrungszeitraums](azure-stack-manage-storage-accounts.md#set-the-retention-period) für Daten automatisch freigegeben, kann aber auch sofort manuell freigegeben werden.

Weitere Informationen finden Sie unter [Verwalten von Speicherkonten in Azure Stack Hub](azure-stack-manage-storage-accounts.md#reclaim) im Abschnitt „Freigeben von Kapazität“.

::: moniker range="<azs-1910"

### <a name="migrate-a-container-between-volumes"></a>Migrieren eines Containers zwischen Volumes

*Diese Option gilt nur für in Azure Stack Hub integrierte Systeme.*

Aufgrund der Verwendungsmuster von Mandanten benötigen einige Mandantenfreigaben mehr Speicherplatz als andere. Dies kann dazu führen, dass bei einigen Freigaben der Speicherplatz knapp wird, während andere Freigaben noch kaum genutzt werden.

Sie können versuchen, Speicherplatz auf einer intensiv genutzten Freigabe freizugeben, indem Sie einige Blobcontainer manuell zu einer anderen Freigabe migrieren. Sie können mehrere kleinere Container zu einer einzelnen Freigabe migrieren, die über genügend Kapazität verfügt, um alle aufzunehmen. Über eine Migration können *freie* Container verschoben werden. Freie Container sind Container, die keinen Datenträger für eine VM enthalten.

Durch die Migration werden alle Containerblobs in der neuen Freigabe konsolidiert.

- Wenn sich ein Container im Überlaufmodus befindet und Blobs auf zusätzlichen Volumes platziert hat, muss die neue Freigabe über genügend Kapazität verfügen, um alle Blobs für den zu migrierenden Container aufnehmen zu können. Dies schließt die Blobs mit ein, die sich auf zusätzlichen Freigaben befinden.

- Das PowerShell-Cmdlet `Get-AzsStorageContainer` gibt nur den verwendeten Speicherplatz auf dem ursprünglichen Volume für einen Container an. Der Speicherplatz, der von Blobs auf zusätzlichen Volumes verwendet wird, wird von diesem Cmdlet nicht angegeben. Dadurch ist die Gesamtgröße eines Containers unter Umständen nicht ersichtlich. Es kann vorkommen, dass bei der Konsolidierung eines Containers auf einer neuen Freigabe diese neue Freigabe in den Überlaufmodus versetzt wird und Daten auf zusätzlichen Freigaben platziert werden. In diesem Fall müssten die Freigaben ausgeglichen werden.

- Ermitteln Sie die Gesamtmenge der Daten in Zusammenarbeit mit dem Besitzer der entsprechenden Ressourcengruppen und Container, bevor Sie die Daten migrieren, wenn Ihnen Berechtigungen für bestimmte Ressourcengruppen fehlen und Sie die zusätzlichen Volumes für Überlaufdaten nicht mithilfe von PowerShell abfragen können.  

> [!IMPORTANT]
> Bei der Migration von Blobs für einen Container handelt es sich um einen Offlinevorgang, der die Verwendung von PowerShell erfordert. Bis zum Abschluss der Migration sind alle Blobs für den Container, den Sie migrieren, offline und können nicht verwendet werden. Außerdem sollten Sie es vermeiden, ein Upgrade für Azure Stack Hub durchzuführen, bis alle Migrationsvorgänge abgeschlossen sind.

#### <a name="migrate-containers-by-using-powershell"></a>Migrieren von Containern mithilfe von PowerShell

1. Vergewissern Sie sich, dass [Azure PowerShell installiert und konfiguriert](/powershell/azure/) ist. Weitere Informationen finden Sie unter [Verwalten von Azure-Ressourcen mithilfe von Azure PowerShell](/azure/azure-resource-manager/management/manage-resources-powershell).
2. Überprüfen Sie den Container, um zu ermitteln, welche Daten sich auf der Freigabe befinden, die Sie migrieren möchten. Verwenden Sie das Cmdlet `Get-AzsStorageContainer`, um die Container zu ermitteln, die sich auf einem Volume am besten für die Migration eignen:

   ```powershell  
   $farm_name = (Get-AzsStorageFarm)[0].name
   $shares = Get-AzsStorageShare -FarmName $farm_name
   $containers = Get-AzsStorageContainer -ShareName $shares[0].ShareName -FarmName $farm_name
   ```
   Untersuchen Sie dann „$containers“:

   ```powershell
   $containers
   ```

   ![Beispiel: $containers](media/azure-stack-manage-storage-shares/containers.png)

3. Ermitteln Sie die besten Zielfreigaben für den zu migrierenden Container:

   ```powershell
   $destinationshare = ($shares | Sort-Object FreeCapacity -Descending)[0]
   ```

   Untersuchen Sie dann „$destinationshares“:

   ```powershell 
   $destinationshares
   ```

   ![Beispiel: $destinationshares](media/azure-stack-manage-storage-shares/examine-destinationshares.png)

4. Starten Sie die Migration für einen Container. Die Migration erfolgt asynchron. Falls Sie die Migration weiterer Container starten, bevor die erste Migration abgeschlossen ist, können Sie den Status der einzelnen Aufträge anhand der jeweiligen Auftrags-ID nachverfolgen.

   ```powershell
   $job_id = Start-AzsStorageContainerMigration -StorageAccountName $containers[0].Accountname -ContainerName $containers[0].Containername -ShareName $containers[0].Sharename -DestinationShareUncPath $destinationshares[0].UncPath -FarmName $farm_name
   ```

   Untersuchen Sie dann „$jobId“. Ersetzen Sie im folgenden Beispiel *d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0* durch die Auftrags-ID, die Sie untersuchen möchten:

   ```powershell
   $jobId
   d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0
   ```

5. Verwenden Sie die Auftrags-ID, um den Status des Migrationsauftrags zu überprüfen. Nach Abschluss der Containermigration wird **MigrationStatus** auf *Abgeschlossen* festgelegt.

   ```powershell 
   Get-AzsStorageContainerMigrationStatus -JobId $job_id -FarmName $farm_name
   ```

   ![Der Screenshot zeigt den Migrationsstatus.](media/azure-stack-manage-storage-shares/migration-status1.png)

6. Sie können die Ausführung eines Migrationsauftrags abbrechen. Abgebrochene Migrationsaufträge werden asynchron verarbeitet. Der Abbruch kann anhand von „$jobId“ nachverfolgt werden:

   ```powershell
   Stop-AzsStorageContainerMigration -JobId $job_id -FarmName $farm_name
   ```

   ![Beispiel: Rollbackstatus](media/azure-stack-manage-storage-shares/rollback.png)

7. Sie können den Befehl aus Schritt 6 noch mal ausführen, bis der Migrationsstatus *Angebrochen* lautet:  

    ![Der Screenshot zeigt ein Beispiel für einen abgebrochenen Migrationsstatus.](media/azure-stack-manage-storage-shares/cancelled.png)

### <a name="move-vm-disks"></a>Verschieben von VM-Datenträgern

*Diese Option gilt nur für in Azure Stack Hub integrierte Systeme.*

Die extremste Methode zum Verwalten von Speicherplatz ist das Verschieben von VM-Datenträgern. Bei der Verschiebung eines angefügten Containers (Container mit einem VM-Datenträger) handelt es sich um einen komplexen Vorgang. Lassen Sie sich daher vom Microsoft-Support dabei unterstützen.

::: moniker-end
::: moniker range=">=azs-1910"

### <a name="migrate-a-managed-disk-between-volumes"></a>Migrieren eines verwalteten Datenträgers zwischen Volumes

*Diese Option gilt nur für in Azure Stack Hub integrierte Systeme.*

Aufgrund der Verwendungsmuster von Mandanten benötigen einige Mandantenvolumes mehr Speicherplatz als andere. Dies kann dazu führen, dass bei einigen Volumes der Speicherplatz knapp wird, während andere Volumes noch kaum genutzt werden.

Sie können Speicherplatz auf einem intensiv genutzten Volume freigeben, indem Sie einige verwaltete Datenträger manuell zu einem anderen Volume migrieren. Sie können mehrere verwaltete Datenträger zu einem einzelnen Volume migrieren, das über genügend Kapazität verfügt, um alle aufzunehmen. Verwenden Sie die Migration, um verwaltete *Offlinedatenträger* zu verschieben. Verwaltete Offlinedatenträger sind Datenträger, die nicht an eine VM angefügt sind.

> [!IMPORTANT]
> Bei der Migration von verwalteten Datenträgern handelt es sich um einen Offlinevorgang, der die Verwendung von PowerShell erfordert. Vor dem Starten des Migrationsauftrags müssen Sie die Kandidatendatenträger für die Migration von ihrer Besitzer-VM trennen. (Nach Abschluss der Migration können Sie sie wieder anfügen.) Bis zum Abschluss der Migration müssen alle verwalteten Datenträger, die Sie migrieren, offline bleiben und können nicht verwendet werden. Andernfalls wird der Migrationsauftrag abgebrochen, und alle nicht migrierten Datenträger befinden sich weiterhin auf den ursprünglichen Volumes. Außerdem sollten Sie es vermeiden, für Azure Stack Hub ein Upgrade durchzuführen, bis alle Migrationsvorgänge abgeschlossen sind.

#### <a name="to-migrate-managed-disks-using-powershell"></a>So migrieren Sie verwaltete Datenträger mithilfe von PowerShell

1. Vergewissern Sie sich, dass Azure PowerShell installiert und konfiguriert ist. Anweisungen zum Konfigurieren der PowerShell-Umgebung finden Sie unter [Installieren von PowerShell für Azure Stack Hub](azure-stack-powershell-install.md). Weitere Informationen zur Anmeldung bei Azure Stack Hub finden Sie unter [Konfigurieren der Betreiberumgebung und Anmelden bei Azure Stack Hub](azure-stack-powershell-configure-admin.md).
2. Überprüfen Sie die verwalteten Datenträger, um nachzuvollziehen, welche Datenträger sich auf dem zu migrierenden Volume befinden. Verwenden Sie das Cmdlet `Get-AzsDisk`, um die Kandidatendatenträger zu ermitteln, die sich auf einem Volume am besten für die Migration eignen:

   ```powershell  
   $ScaleUnit = (Get-AzsScaleUnit)[0]
   $StorageSubSystem = (Get-AzsStorageSubSystem -ScaleUnit $ScaleUnit.Name)[0]
   $Volumes = (Get-AzsVolume -ScaleUnit $ScaleUnit.Name -StorageSubSystem $StorageSubSystem.Name | Where-Object {$_.VolumeLabel -Like "ObjStore_*"})
   $SourceVolume  = ($Volumes | Sort-Object RemainingCapacityGB)[0]
   $VolumeName = $SourceVolume.Name.Split("/")[2]
   $VolumeName = $VolumeName.Substring($VolumeName.IndexOf(".")+1)
   $MigrationSource = "\\SU1FileServer."+$VolumeName+"\SU1_"+$SourceVolume.VolumeLabel
   $Disks = Get-AzsDisk -Status All -SharePath $MigrationSource | Select-Object -First 10
   ```
   Untersuchen Sie dann „$disks“:

   ```powershell
   $Disks
   ```

   ![Beispiel: $Disks](media/azure-stack-manage-storage-shares/disks.png)

3. Ermitteln Sie das beste Zielvolume für die zu migrierenden Datenträger:

   ```powershell
   $DestinationVolume  = ($Volumes | Sort-Object RemainingCapacityGB -Descending)[0]
   $VolumeName = $DestinationVolume.Name.Split("/")[2]
   $VolumeName = $VolumeName.Substring($VolumeName.IndexOf(".")+1)
   $MigrationTarget = "\\SU1FileServer."+$VolumeName+"\SU1_"+$DestinationVolume.VolumeLabel
   ```

4. Starten Sie die Migration für verwaltete Datenträger. Die Migration erfolgt asynchron. Falls Sie die Migration weiterer Datenträger starten, bevor die erste Migration abgeschlossen ist, können Sie den Status der einzelnen Aufträge anhand des jeweiligen Auftragsnamens nachverfolgen.

   ```powershell
   $jobName = "MigratingDisk"
   Start-AzsDiskMigrationJob -Disks $Disks -TargetShare $MigrationTarget -Name $jobName
   ```

5. Verwenden Sie die Auftragsnamen, um den Status des Migrationsauftrags zu überprüfen. Nach Abschluss der Datenträgermigration wird **MigrationStatus** auf **Abgeschlossen** festgelegt.

   ```powershell 
   $job = Get-AzsDiskMigrationJob -Name $jobName
   ```

   ![Beispiel: Migrationsstatus](media/azure-stack-manage-storage-shares/diskmigrationjob.png)
   
   Wenn Sie mehrere verwaltete Datenträger in einem Migrationsauftrag migrieren, können Sie auch die Unteraufgaben des Auftrags überprüfen.

   ```powershell 
   $job.Subtasks
   ```

   ![Beispiel: Status der Migrationsunteraufgaben](media/azure-stack-manage-storage-shares/diskmigrationsubtask.png)

6. Sie können die Ausführung eines Migrationsauftrags abbrechen. Abgebrochene Migrationsaufträge werden asynchron verarbeitet. Sie können den Abbruch anhand des Auftragsnamens nachverfolgen, bis der Status des Migrationsauftrags **Abgebrochen** lautet:

   ```powershell
   Stop-AzsDiskMigrationJob -Name $jobName
   ```

   ![Beispiel: Status „Abgebrochen“](media/azure-stack-manage-storage-shares/diskmigrationstop.png)

### <a name="distribute-unmanaged-disks"></a>Verteilen nicht verwalteter Datenträger

*Diese Option gilt nur für in Azure Stack Hub integrierte Systeme.*

Die extremste Methode zum Verwalten von Speicherplatz ist das Verschieben von nicht verwalteten Datenträgern. Wenn der Mandant einem Container eine Anzahl nicht verwalteter Datenträger hinzufügt, kann die insgesamt genutzte Kapazität des Containers die verfügbare Kapazität des Volumes mit dem Container übersteigen, bevor der Container in den Modus *Überlauf* wechselt. Um zu vermeiden, dass ein einzelner Container den Speicherplatz eines Volumes ausschöpft, kann der Mandant die vorhandenen nicht verwalteten Datenträger eines Containers auf verschiedene Container verteilen. Bei der Verteilung eines angefügten Containers (Container mit einem VM-Datenträger) handelt es sich um einen komplexen Vorgang. Lassen Sie sich daher vom Microsoft-Support dabei unterstützen.

::: moniker-end

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Anbieten von VMs für Benutzer finden Sie unter [Verwalten der Speicherkapazität für Azure Stack Hub](./tutorial-offer-services.md?view=azs-2002).