---
title: Verwalten der Speicherkapazität in Azure Stack | Microsoft-Dokumentation
description: Überwachen und Verwalten der Azure Stack-Speicherkapazität und des Verfügbarkeitspeicherplatzes für Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 03/19/2019
ms.openlocfilehash: 48452a9a5c02bca8e99b7769e90495544bd95c0e
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71829505"
---
# <a name="manage-storage-capacity-for-azure-stack"></a>Verwalten der Speicherkapazität für Azure Stack 

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Die Informationen in diesem Artikel helfen Azure Stack-Cloudbetreibern bei der Überwachung und Verwaltung der Speicherkapazität ihrer Azure Stack-Bereitstellung. Die Speicherinfrastruktur von Azure Stack ordnet einen Teil der Gesamtspeicherkapazität der Azure Stack-Bereitstellung für die Verwendung durch **Speicherdienste** zu. Die Speicherdienste speichern Daten eines Mandanten in Freigaben auf Volumes, die den Knoten der Bereitstellung entsprechen.

Cloudbetreibern steht nur eine begrenzte Menge an Speicherplatz zur Verfügung. Die Speicherplatzmenge hängt von der implementierten Lösung ab. Ihre Lösung wird entweder von Ihrem OEM-Anbieter (bei Verwendung einer Lösung mit mehreren Knoten) oder von der Hardware bereitgestellt, auf der Sie das Azure Stack Development Kit installieren.

Da Azure Stack keine Erweiterung der Speicherkapazität unterstützt, ist es wichtig, den verfügbaren Speicherplatz zu [überwachen](#monitor-shares), um einen effizienten Betrieb zu gewährleisten.  

Sollte die freie Kapazität einer Freigabe knapp werden, sollten Sie den [Speicherplatz verwalten](#manage-available-space), um eine Erschöpfung der Kapazität von Freigaben zu verhindern.

Für die Kapazitätsverwaltung stehen unter anderem folgende Optionen zur Verfügung:
- Freigeben von Kapazität
- Migrieren eines Containers

Wenn eine Freigabe vollständig belegt ist, funktioniert der Speicherdienst für diese Freigabe nicht mehr. Wenden Sie sich an den Support von Microsoft, um Unterstützung bei der Wiederherstellung des Betriebs für die Freigabe zu erhalten.

## <a name="understand-volumes-and-shares-containers-and-disks"></a>Grundlegendes zu Volumes und Freigaben, Containern und Datenträgern
### <a name="volumes-and-shares"></a>Volumes und Freigaben
Der *Speicherdienst* partitioniert den verfügbaren Speicher in separate und gleiche Volumes, die zum Speichern von Mandantendaten zugewiesen werden. Die Anzahl von Volumes entspricht der Anzahl von Knoten in der Azure Stack-Bereitstellung:

- Bei einer Bereitstellung mit vier Knoten sind vier Volumes vorhanden. Jedes Volume verfügt über eine einzelne Freigabe. Bei einer Bereitstellung mit mehreren Knoten verringert sich die Anzahl von Freigaben nicht, wenn ein Knoten entfernt wird oder fehlerhaft ist.
- Bei Verwendung des Azure Stack Developer Kits ist ein einzelnes Volume mit einer einzelnen Freigabe vorhanden.

Da die Speicherdienstfreigaben ausschließlich von den Speicherdiensten genutzt werden, müssen Sie keine Dateien auf den Freigaben direkt ändern, hinzufügen oder entfernen. Die auf diesen Volumes gespeicherten Dateien sollten nur von Speicherdiensten verwendet werden.

Freigaben auf Volumes enthalten Mandantendaten. Zu Mandantendaten zählen Seitenblobs, Blockblobs, Anfügeblobs, Tabellen, Warteschlangen, Datenbanken sowie dazugehörige Metadatenspeicher. Da die Speicherobjekte (Blobs usw.) jeweils in einer einzelnen Freigabe enthalten sind, darf die maximale Größe der jeweiligen Objekte die Größe einer Freigabe nicht übersteigen. Die maximale Größe neuer Objekte hängt von der Restkapazität ab, die in einer Freigabe zum Zeitpunkt der Objekterstellung in Form von nicht genutztem Speicherplatz zur Verfügung steht.

Wenn der freie Speicherplatz einer Freigabe knapp wird und Aktionen zum [Freigeben](#reclaim-capacity) von Speicherplatz nicht erfolgreich oder verfügbar sind, kann der Azure Stack-Cloudbetreiber die Blobcontainer zwischen Freigaben migrieren.

- Informationen zur Verwendung von Blobspeicher in Azure Stack durch Mandantenbenutzer finden Sie unter [Azure Stack-Speicherdienste](/azure-stack/user/azure-stack-storage-overview#azure-stack-storage-services).


### <a name="containers"></a>Container
Mandantenbenutzer erstellen Container, die dann zum Speichern von Blobdaten verwendet werden. Der Benutzer entscheidet, in welchem Container Blobs platziert werden sollen, und der Speicherdienst bestimmt mithilfe eines Algorithmus, auf welchem Volume der Container platziert wird. Der Algorithmus wählt in der Regel das Volume mit dem meisten verfügbaren Speicherplatz.  

Nachdem ein Blob in einem Container platziert wurde, kann dieses Blob weiter wachsen und mehr Speicherplatz benötigen. Wenn Sie weitere Blobs hinzufügen und vorhandene Blobs größer werden, verringert sich der verfügbare Speicherplatz des Volumes, das den entsprechenden Container enthält.  

Container sind nicht auf eine einzelne Freigabe beschränkt. Wenn die kombinierten Blobdaten in einem Container 80 Prozent oder mehr des verfügbaren Speicherplatzes beanspruchen, wechselt der Container in den *Überlaufmodus*. Im Überlaufmodus werden alle neuen Blobs, die in diesem Container erstellt werden, einem anderen Volume mit genügend Speicherplatz zugeordnet. Im Laufe der Zeit kann ein Container im Überlaufmodus über Blobs verfügen, die auf mehrere Volumes verteilt sind.

Bei einer Belegung von 80 Prozent und 90 Prozent des verfügbaren Speicherplatzes gibt das System Warnungen im Azure Stack-Administratorportal aus. Cloudbetreiber sollten die verfügbare Speicherkapazität prüfen und eine Neuverteilung der Inhalte planen. Der Speicherdienst funktioniert nicht mehr, wenn ein Datenträger zu 100 Prozent belegt ist, und es werden keine weiteren Benachrichtigungen ausgegeben.

### <a name="disks"></a>Datenträger
VM-Datenträger werden Containern durch Mandanten hinzugefügt und enthalten einen Betriebssystem-Datenträger. Virtuelle Computer können auch über Datenträger für Daten verfügen. Beide Arten von Datenträgern werden als Seitenblobs gespeichert. Für Mandanten empfiehlt es sich, die einzelnen Datenträger in separaten Containern zu platzieren, um die Leistung des virtuellen Computers zu verbessern.
- Jeder Container, der einen Datenträger (Seitenblob) eines virtuellen Computers enthält, wird als angefügter Container des virtuellen Computers betrachtet, zu dem der Datenträger gehört.
- Ein Container, der keinen Datenträger eines virtuellen Computers enthält, gilt als freier Container.

Bei angefügten Containern sind die Optionen für die Speicherplatzfreigabe [eingeschränkt](#move-vm-disks).
> [!TIP]  
> Cloudbetreiber verwalten Datenträger nicht direkt. Diese sind an virtuelle Computer (Virtual Machines, VMs) angefügt, die Mandanten ggf. einem Container hinzufügen können. Zur Planung der Verwaltung von Speicherplatz auf Speicherfreigaben kann es jedoch hilfreich sein, mit der Beziehung zwischen Datenträgern und Containern/Freigaben vertraut zu sein.

## <a name="monitor-shares"></a>Überwachen von Freigaben
Überwachen Sie Freigaben mithilfe von PowerShell oder über das Administratorportal, um informiert zu sein, wenn nur noch wenig Speicherplatz zur Verfügung steht. Bei Verwendung des Portals erhalten Sie Benachrichtigungen zu Freigaben, bei denen der Speicherplatz knapp wird.    

### <a name="use-powershell"></a>Verwenden von PowerShell
Als Cloudbetreiber können Sie die Speicherkapazität einer Freigabe mithilfe des PowerShell-Cmdlets **Get-AzsStorageShare** überwachen. Das Cmdlet "Get-AzsStorageShare" gibt für jede der Freigaben den gesamten, zugeordneten und freien Speicherplatz (in Bytes) zurück.   
![Beispiel: Zurückgeben des freien Speicherplatzes für Freigaben](media/azure-stack-manage-storage-shares/free-space.png)

- Bei der **Gesamtkapazität** handelt es sich um den gesamten Speicherplatz (in Bytes), der auf der Freigabe zur Verfügung steht. Dieser Speicherplatz wird für Daten und Metadaten verwendet, die von den Speicherdiensten verwaltet werden.
- **Verwendete Kapazität** ist die Menge von Daten (in Bytes), die von allen Erweiterungen für die Dateien genutzt wird, die die Mandantendaten und die dazugehörigen Metadaten speichern.

### <a name="use-the-administrator-portal"></a>Verwenden des Administratorportals
Als Cloudbetreiber können Sie die Speicherkapazität für alle Freigaben im Administratorportal anzeigen.

1. Melden Sie sich am [Administratorportal](https://adminportal.local.azurestack.external) an.
2. Wählen Sie **Alle Dienst** > **Speicher** > **Dateifreigaben** aus, um die Dateifreigabeliste mit den Nutzungsinformationen zu öffnen. 

    ![Beispiel: Speicherdateifreigaben](media/azure-stack-manage-storage-shares/storage-file-shares.png)

   - **GESAMT** ist der gesamte Speicherplatz (in Bytes), der auf der Freigabe zur Verfügung steht. Dieser Speicherplatz wird für Daten und Metadaten verwendet, die von den Speicherdiensten verwaltet werden.
   - **VERWENDET** ist die Menge von Daten (in Bytes), die von allen Erweiterungen für die Dateien genutzt wird, die die Mandantendaten und die dazugehörigen Metadaten speichern.

### <a name="storage-space-alerts"></a>Speicherplatzbenachrichtigungen
Bei Verwendung des Administratorportals erhalten Sie Benachrichtigungen zu Freigaben, bei denen der Speicherplatz knapp wird.

> [!IMPORTANT]
> Achten Sie als Cloudbetreiber darauf, dass Freigaben nicht vollständig belegt sind. Wenn eine Freigabe vollständig belegt ist, funktioniert der Speicherdienst für diese Freigabe nicht mehr. Wenden Sie sich an den Support von Microsoft, um Speicherplatz freizugeben und wieder Vorgänge für eine Freigabe zu ermöglichen.

**Warnung**: Wenn eine Dateifreigabe zu mehr als 80 Prozent belegt ist, erhalten Sie im Administratorportal eine Benachrichtigung vom Typ *Warnung*: ![Beispiel: Benachrichtigung vom Typ „Warnung“](media/azure-stack-manage-storage-shares/alert-warning.png)


**Kritisch**: Wenn eine Dateifreigabe zu mehr als 90 Prozent belegt ist, erhalten Sie im Administratorportal eine Benachrichtigung vom Typ *Kritisch*: ![Beispiel: Kritische Warnung](media/azure-stack-manage-storage-shares/alert-critical.png)

**Anzeigen von Details:** Im Administratorportal können Sie Details zu einer Benachrichtigung öffnen, um mögliche Maßnahmen anzuzeigen: ![Beispiel: Anzeigen von Benachrichtigungsdetails](media/azure-stack-manage-storage-shares/alert-details.png)


## <a name="manage-available-space"></a>Verwalten des verfügbaren Speicherplatzes
Wenn Speicherplatz auf einer Freigabe freigegeben werden muss, beginnen Sie mit den am wenigsten invasiven Methoden. Versuchen Sie also beispielsweise zunächst, Speicherplatz freizugeben, bevor Sie sich dafür entscheiden, einen Container zu migrieren.  

### <a name="reclaim-capacity"></a>Freigeben von Kapazität
*Diese Option ist sowohl für Bereitstellungen mit mehreren Knoten als auch für das Azure Stack Development Kit geeignet.*

Sie können die Kapazität freigeben, die von gelöschten Mandantenkonten beansprucht wird. Diese Kapazität wird nach Ablauf des [Aufbewahrungszeitraums](azure-stack-manage-storage-accounts.md#set-the-retention-period) für Daten automatisch freigegeben, kann aber auch sofort manuell freigegeben werden.

Weitere Informationen finden Sie im Artikel „Verwalten von Speicherkonten in Azure Stack“ unter [Freigeben von Kapazität](azure-stack-manage-storage-accounts.md#reclaim).

### <a name="migrate-a-container-between-volumes"></a>Migrieren eines Containers zwischen Volumes
*Diese Option steht nur für Bereitstellungen mit mehreren Knoten zur Verfügung.*

Aufgrund der Verwendungsmuster von Mandanten benötigen einige Mandantenfreigaben mehr Speicherplatz als andere. Dies kann dazu führen, dass bei einigen Freigaben der Speicherplatz knapp wird, während andere Freigaben noch kaum genutzt werden.

Sie können versuchen, Speicherplatz auf einer intensiv genutzten Freigabe freizugeben, indem Sie einige Blobcontainer manuell zu einer anderen Freigabe migrieren. Sie können mehrere kleinere Container zu einer einzelnen Freigabe migrieren, die über genügend Kapazität verfügt, um alle aufzunehmen. Mithilfe der Migration können *freie* Container verschoben werden. Freie Container sind Container, die keinen Datenträger für einen virtuellen Computer enthalten.   

Durch die Migration werden alle Containerblobs in der neuen Freigabe konsolidiert.

- Wenn sich ein Container im Überlaufmodus befindet und Blobs auf zusätzlichen Volumes platziert hat, muss die neue Freigabe über genügend Kapazität verfügen, um alle Blobs für den zu migrierenden Container aufnehmen zu können. Dies schließt die Blobs mit ein, die sich auf zusätzlichen Freigaben befinden.

- Das PowerShell-Cmdlet *Get-AzsStorageContainer* gibt nur den verwendeten Speicherplatz auf dem ursprünglichen Volume für einen Container an. Der Speicherplatz, der von Blobs auf zusätzlichen Volumes verwendet wird, wird von diesem Cmdlet nicht angegeben. Dadurch ist die Gesamtgröße eines Containers unter Umständen nicht ersichtlich. Es kann vorkommen, dass bei der Konsolidierung eines Containers auf einer neuen Freigabe diese neue Freigabe in den Überlaufmodus versetzt wird und Daten auf zusätzlichen Freigaben platziert. In diesem Fall müssten die Freigaben erneut ausgeglichen werden.

- Wenn Ihnen Berechtigungen für eine Ressourcengruppe fehlen und Sie die zusätzlichen Volumes für Überlaufdaten nicht mithilfe von PowerShell abfragen können, ermitteln Sie die Gesamtgröße der Daten in Zusammenarbeit mit dem Besitzer der entsprechenden Ressourcengruppen und Container, bevor Sie die Daten migrieren.  

> [!IMPORTANT]
> Bei der Migration von Blobs für einen Container handelt es sich um einen Offlinevorgang, der die Verwendung von PowerShell erfordert. Bis zum Abschluss der Migration sind alle Blobs für den Container, den Sie migrieren, offline und können nicht verwendet werden. Außerdem sollten Sie es vermeiden, für Azure Stack ein Upgrade durchzuführen, bis alle Migrationsvorgänge abgeschlossen sind.

#### <a name="to-migrate-containers-using-powershell"></a>So migrieren Sie Container mithilfe von PowerShell
1. Vergewissern Sie sich, dass [Azure PowerShell installiert und konfiguriert](https://azure.microsoft.com/documentation/articles/powershell-install-configure/) ist. Weitere Informationen finden Sie unter [Verwenden von Azure PowerShell mit dem Azure-Ressourcen-Manager](https://go.microsoft.com/fwlink/?LinkId=394767).
2. Überprüfen Sie den Container, um zu ermitteln, welche Daten sich auf der Freigabe befinden, die Sie migrieren möchten. Verwenden Sie das Cmdlet **Get-AzsStorageContainer**, um die Container zu ermitteln, die sich auf einem Volume am besten für die Migration eignen:

   ```powershell  
   $farm_name = (Get-AzsStorageFarm)[0].name
   $shares = Get-AzsStorageShare -FarmName $farm_name
   $containers = Get-AzsStorageContainer -ShareName $shares[0].ShareName -FarmName $farm_name
   ```
   Untersuchen Sie dann „$containers“:

   ```powershell
   $containers
   ```

   ![Beispiel: $Containers](media/azure-stack-manage-storage-shares/containers.png)

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

5. Verwenden Sie die Auftrags-ID, um den Status des Migrationsauftrags zu überprüfen. Nach Abschluss der Containermigration wird **MigrationStatus** auf **Abgeschlossen** festgelegt.

   ```powershell 
   Get-AzsStorageContainerMigrationStatus -JobId $job_id -FarmName $farm_name
   ```

   ![Beispiel: Migrationsstatus](media/azure-stack-manage-storage-shares/migration-status1.png)

6. Sie können die Ausführung eines Migrationsauftrags abbrechen. Abgebrochene Migrationsaufträge werden asynchron verarbeitet. Der Abbruch kann anhand von „$jobId“ nachverfolgt werden:

   ```powershell
   Stop-AzsStorageContainerMigration -JobId $job_id -FarmName $farm_name
   ```

   ![Beispiel: Rollbackstatus](media/azure-stack-manage-storage-shares/rollback.png)

7. Sie können den Befehl aus Schritt 6 erneut ausführen, bis der Status des Migrationsauftrags **Angebrochen** lautet:  

    ![Beispiel: Status „Abgebrochen“](media/azure-stack-manage-storage-shares/cancelled.png)

### <a name="move-vm-disks"></a>Verschieben von VM-Datenträgern
*Diese Option steht nur für Bereitstellungen mit mehreren Knoten zur Verfügung.*

Die extremste Methode zur Speicherplatzverwaltung ist die Verschiebung von VM-Datenträgern. Bei der Verschiebung eines angefügten Containers (Container mit einem VM-Datenträger) handelt es sich um einen komplexen Vorgang. Lassen Sie sich daher vom Microsoft-Support dabei unterstützen.

## <a name="next-steps"></a>Nächste Schritte
Informieren Sie sich ausführlicher über das [Verfügbarmachen von virtuellen Computern für Ihre Azure Stack-Benutzer](azure-stack-tutorial-tenant-vm.md).
