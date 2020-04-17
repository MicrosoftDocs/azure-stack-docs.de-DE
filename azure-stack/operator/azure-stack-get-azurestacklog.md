---
title: Verwenden des privilegierten Endpunkts (PEP) zum Erfassen von Diagnoseprotokollen
description: Hier erfahren Sie, wie Sie in Azure Stack Hub mithilfe des Administratorportals oder eines PowerShell-Skripts Diagnoseprotokolle bedarfsgesteuert sammeln.
author: justinha
ms.topic: article
ms.date: 03/05/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 03/05/2020
ms.openlocfilehash: df5a98e8526181a84d8b214fbdf82eb1dba00088
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "79520651"
---
# <a name="send-azure-stack-hub-diagnostic-logs-by-using-the-privileged-endpoint-pep"></a>Senden von Azure Stack Hub-Diagnoseprotokollen mithilfe des privilegierten Endpunkts (PEP)

<!--how do you look up the PEP IP address. You look up the azurestackstampinfo.json--->


Um „Get-AzureStackLog“ in einem integrierten System ausführen zu können, benötigen Sie Zugriff auf den privilegierten Endpunkt (PEP). Im Anschluss finden Sie ein Beispielskript, das Sie unter Verwendung des PEP ausführen können, um Protokolle zu sammeln. Wenn Sie eine laufende Protokollsammlung abbrechen, um eine neue Sammlung zu starten, warten Sie fünf Minuten, bevor Sie die neue Protokollsammlung starten, und geben Sie `Remove-PSSession -Session $session` ein.


```powershell
$ipAddress = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.

$password = ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object -TypeName System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $password)

$shareCred = Get-Credential

$session = New-PSSession -ComputerName $ipAddress -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2) # Provide the time that includes the period for your issue

Invoke-Command -Session $session { Get-AzureStackLog -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if ($session) {
    Remove-PSSession -Session $session
}
```

### <a name="examples"></a>Beispiele

* Sammeln Sie alle Protokolle für alle Rollen:

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred
  ```

* Sammeln Sie Protokolle für die Rollen VirtualMachines und BareMetal:

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal
  ```

* Sammeln Sie Protokolle für die Rollen VirtualMachines und BareMetal, und verwenden Sie die Datumsfilterung für Protokolldateien für die letzten acht Stunden:

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

* Sammeln Sie Protokolle für die Rollen VirtualMachines und BareMetal, und verwenden Sie für die Datumsfilterung der Protokolldateien den Zeitraum zwischen acht und zwei Stunden vor dem aktuellen Zeitpunkt:

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```

* Sammeln Sie Protokolle von Mandantenbereitstellungen, die auf selbstverwalteten Kubernetes-Clustern (AKS-Engine) in Azure Stack ausgeführt werden. Kubernetes-Protokolle sollten in einem Mandantenspeicherkonto in einem Format gespeichert werden, das erlaubt, dass der Sammlungszeitraum auch auf sie angewendet wird. 

  ```powershell
  Get-AzureStackLog -OutputPath <Path> -InputSasUri "<Blob Service Sas URI>" -FromDate "<Beginning of the time range>" -ToDate "<End of the time range>"
  ```

  Beispiel:

  ```powershell
  Get-AzureStackLog -OutputPath C:\KubernetesLogs -InputSasUri "https://<storageAccountName>.blob.core.windows.net/<ContainerName><SAS token>" -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2) 
  ```

* Sammeln Sie Protokolle für die Mehrwert-Ressourcenanbieter. Die allgemeine Syntax sieht wie folgt aus:
 
  ```powershell
  Get-AzureStackLogs -FilterByResourceProvider <<value-add RP name>>
  ```
 
  Protokollsammlung für IoT Hub: 

  ```powershell
  Get-AzureStackLogs -FilterByResourceProvider IotHub
  ```
 
  Protokollsammlung für Event Hubs:

  ```powershell
  Get-AzureStackLogs -FilterByResourceProvider eventhub
  ```
 
  Protokollsammlung für Azure Stack Edge:

  ```powershell
  Get-AzureStackLogs -FilterByResourceProvide databoxedge
  ```

* Erfassen Sie Protokolle, und speichern Sie sie im angegebenen Azure Storage-Blobcontainer. Die allgemeine Syntax für diesen Vorgang lautet wie folgt:

  ```powershell
  Get-AzureStackLog -OutputSasUri "<Blob service SAS Uri>"
  ```

  Beispiel:

  ```powershell
  Get-AzureStackLog -OutputSasUri "https://<storageAccountName>.blob.core.windows.net/<ContainerName><SAS token>"
  ```

  > [!NOTE]
  > Diese Vorgehensweise eignet sich zum Hochladen von Protokollen. Auch wenn Sie nicht über eine SMB-Freigabe oder Internetzugriff verfügen, können Sie in Ihrer Azure Stack Hub-Instanz ein Blobspeicherkonto erstellen, um die Protokolle zu übertragen. Anschließend können Sie Ihren Client nutzen, um diese Protokolle abzurufen.  

  Um das SAS-Token für das Speicherkonto zu generieren, sind folgende Berechtigungen erforderlich:

  * Zugriff auf den Blob Storage-Dienst
  * Zugriff auf den Containerressourcentyp

  Führen Sie diese Schritte aus, um einen SAS-URI-Wert für den Parameter `-OutputSasUri` zu generieren:

  1. Erstellen Sie ein Speicherkonto, indem Sie die Schritte in [diesem Artikel](/azure/storage/common/storage-quickstart-create-account) ausführen.
  2. Öffnen Sie eine Instanz des Azure Storage-Explorers.
  3. Stellen Sie eine Verbindung mit dem in Schritt 1 erstellten Speicherkonto her.
  4. Navigieren Sie zu **Blobcontainer** in **Speicherdienste**.
  5. Wählen Sie **Neuen Container erstellen**  aus.
  6. Klicken Sie mit der rechten Maustaste auf den neuen Container und dann auf **Shared Access Signature abrufen**.
  7. Wählen Sie eine gültige **Startzeit** und **Endzeit** entsprechend Ihren Anforderungen aus.
  8. Wählen Sie die erforderlichen Berechtigungen **Lesen**, **Schreiben** und **Auflisten** aus.
  9. Klicken Sie auf **Erstellen**.
  10. Sie erhalten eine Shared Access Signature. Kopieren Sie den URL-Teil, und übergeben Sie ihn an den Parameter `-OutputSasUri`.

### <a name="parameter-considerations"></a>Überlegungen zu Parametern 

* Die Parameter **OutputSharePath** und **OutputShareCredential** werden verwendet, um Protokolle an einem benutzerdefinierten Speicherort zu speichern.

* Die Parameter **FromDate** und **ToDate** können zum Sammeln von Protokollen für einen bestimmten Zeitraum verwendet werden. Ohne Angabe dieser Parameter werden standardmäßig Protokolle für die letzten vier Stunden gesammelt.

* Verwenden Sie den Parameter **FilterByNode**, um Protokolle nach Computername zu filtern. Beispiel:

    ```powershell
    Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByNode azs-xrp01
    ```

* Verwenden Sie den Parameter **FilterByLogType**, um Protokolle nach Typ zu filtern. Sie können nach Datei (File), Freigabe (Share) oder Windows-Ereignis (WindowsEvent) filtern. Beispiel:

    ```powershell
    Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByLogType File
    ```

* Sie können den **TimeOutInMinutes**-Parameter verwenden, um das Timeout für die Sammlung von Protokollen festzulegen. Er ist standardmäßig auf 150 (2,5 Stunden) festgelegt.
* Die Protokollsammlung für Dumpdateien ist standardmäßig deaktiviert. Sie können sie mithilfe des Switch-Parameters **IncludeDumpFile** aktivieren.
* Derzeit können Sie den **FilterByRole**-Parameter verwenden, um die Protokollsammlung nach den folgenden Rollen zu filtern:

  |   |   |   |    |     |
  | - | - | - | -  |  -  |
  |ACS                   |CA                             |HRP                            |OboService                |VirtualMachines|
  |ACSBlob               |CacheService                   |IBC                            |OEM                       |WAS            |
  |ACSDownloadService    |Compute                        |InfraServiceController         |OnboardRP                 |WASPUBLIC|
  |ACSFabric             |CPI                            |KeyVaultAdminResourceProvider  |PXE                       |         |
  |ACSFrontEnd           |CRP                            |KeyVaultControlPlane           |QueryServiceCoordinator   |         | 
  |ACSMetrics            |DeploymentMachine              |KeyVaultDataPlane              |QueryServiceWorker        |         |
  |ACSMigrationService   |DiskRP                         |KeyVaultInternalControlPlane   |SeedRing                  |         |
  |ACSMonitoringService  |Domain                         |KeyVaultInternalDataPlane      |SeedRingServices          |         |
  |ACSSettingsService    |ECE                            |KeyVaultNamingService          |SLB                       |         |
  |ACSTableMaster        |EventAdminRP                   |MDM                            |SQL                       |         |
  |ACSTableServer        |EventRP                        |MetricsAdminRP                 |SRP                       |         |
  |ACSWac                |ExternalDNS                    |MetricsRP                      |Storage                   |         |
  |ADFS                  |FabricRing                     |MetricsServer                  |StorageController         |         |
  |ApplicationController |FabricRingServices             |MetricsStoreService            |URP                       |         |
  |ASAppGateway          |FirstTierAggregationService    |MonAdminRP                     |SupportBridgeController   |         |
  |AzureBridge           |FRP                            |MonRP                          |SupportRing               |         |
  |AzureMonitor          |Gateway                        |NC                             |SupportRingServices       |         |
  |BareMetal             |HealthMonitoring               |NonPrivilegedAppGateway        |SupportBridgeRP           |         |
  |BRP                   |HintingServiceV2               |NRP                            |UsageBridge               |         |
  |   |   |   |    |     | 

### <a name="additional-considerations-on-diagnostic-logs"></a>Weitere Aspekte zu Diagnoseprotokollen

* Je nachdem, welche Rollen in den Protokollen gesammelt werden, kann dieser Befehl mehr Zeit beanspruchen. Zu den entscheidenden Faktoren gehören auch der Zeitraum, der für die Protokollsammlung angegeben wurde, und die Anzahl von Knoten in der Azure Stack Hub-Umgebung.
* Überprüfen Sie während der Protokollsammlung den neuen Ordner, der mit dem **OutputSharePath**-Parameter des Befehls erstellt wird.
* Für jede Rolle sind die Protokolle in separaten ZIP-Dateien enthalten. Abhängig von der Größe der gesammelten Protokolle kann das Protokoll einer Rolle möglicherweise in mehrere ZIP-Dateien aufgeteilt werden. Gehen Sie für eine Rolle dieser Art wie folgt vor, wenn Sie alle Protokolldateien in demselben Ordner entzippen möchten: Verwenden Sie ein Tool, mit dem das Entzippen als Massenvorgang durchgeführt werden kann. Wählen Sie alle ZIP-Dateien für die Rolle und dann **Hier entpacken** aus. Alle Protokolldateien für diese Rolle werden in einen einzelnen zusammengeführten Ordner entzippt.
* Eine Datei namens **Get-AzureStackLog_Output.log** wird ebenfalls in dem Ordner mit den gezippten Protokolldateien erstellt. Diese Datei ist ein Protokoll der Befehlsausgabe, die Sie zum Beheben von Problemen während der Protokollsammlung verwenden können. In einigen Fällen enthält die Protokolldatei `PS>TerminatingError`-Einträge, die ignoriert werden können, es sei denn, nach der Protokollsammlung fehlen erwartete Protokolldateien.
* Zum Untersuchen eines bestimmten Fehlers werden unter Umständen die Protokolle von mehr als einer Komponente benötigt.

  * System- und Ereignisprotokolle für alle Infrastruktur-VMs werden unter der Rolle **VirtualMachines** gesammelt.
  * System- und Ereignisprotokolle für alle Hosts werden unter der Rolle **BareMetal** gesammelt.
  * Failovercluster- und Hyper-V-Ereignisprotokolle werden unter der Rolle **Storage** gesammelt.
  * ACS-Protokolle werden unter den Rollen **Storage** und **ACS** gesammelt.

> [!NOTE]
> Größen- und Altersgrenzwerte für die gesammelten Protokolle werden erzwungen, da es sehr wichtig ist, für eine effiziente Nutzung Ihres Speicherplatzes zu sorgen und eine übermäßig hohe Anzahl von Protokollen zu verhindern. Allerdings werden beim Diagnostizieren von Problemen manchmal Protokolle benötigt, die aufgrund dieser Grenzwerte nicht mehr vorhanden sind. Daher empfehlen wir Ihnen**dringend**, Ihre Protokolle alle acht bis zwölf Stunden in einen externen Speicher zu verlagern (Speicherkonto in Azure, zusätzliches lokales Speichergerät oder Ähnliches) und je nach Ihren Anforderungen darin einen bis drei Monate lang aufzubewahren. Außerdem sollten Sie sicherstellen, dass der Speicherort verschlüsselt ist.

### <a name="invoke-azurestackondemandlog"></a>Invoke-AzureStackOnDemandLog

Sie können das Cmdlet **Invoke-AzureStackOnDemandLog** verwenden, um bedarfsgesteuerte Protokolle für bestimmte Rollen zu generieren (siehe Liste am Ende dieses Abschnitts). Die von diesem Cmdlet generierten Protokolle sind standardmäßig nicht im Protokollpaket enthalten, das Sie beim Ausführen des Cmdlets **Get-AzureStackLog** erhalten. Außerdem empfehlen wir Ihnen, diese Protokolle nur auf Anforderung des Microsoft-Supportteams zu erfassen.

Derzeit können Sie den Parameter `-FilterByRole` verwenden, um die Protokollsammlung nach den folgenden Rollen zu filtern:

* OEM
* NC
* SLB
* Gateway

#### <a name="example-of-collecting-on-demand-diagnostic-logs"></a>Beispiel für das Sammeln von bedarfsgesteuerten Diagnoseprotokollen

```powershell
$ipAddress = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.

$password = ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object -TypeName System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $password)

$shareCred = Get-Credential

$session = New-PSSession -ComputerName $ipAddress -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2) # Provide the time that includes the period for your issue

Invoke-Command -Session $session {
   Invoke-AzureStackOnDemandLog -Generate -FilterByRole "<on-demand role name>" # Provide the supported on-demand role name e.g. OEM, NC, SLB, Gateway
   Get-AzureStackLog -OutputSharePath "<external share address>" -OutputShareCredential $using:shareCred -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate
}

if ($session) {
   Remove-PSSession -Session $session
}
```

### <a name="how-diagnostic-log-collection-using-the-pep-works"></a>Funktionsweise der Diagnoseprotokollerfassung mit dem PEP

Die Azure Stack Hub-Diagnosetools helfen dabei, die Erfassung von Protokollen einfach und effizient zu machen. Im folgenden Diagramm ist dargestellt, wie die Diagnosetools funktionieren:

![Azure Stack Hub-Diagnosetools: Workflowdiagramm](media/azure-stack-diagnostics/get-azslogs.png)


#### <a name="trace-collector"></a>Collector der Ablaufverfolgung

Der Collector der Ablaufverfolgung ist standardmäßig aktiviert und wird dauerhaft im Hintergrund ausgeführt, um alle ETW-Protokolle (Ereignisablaufverfolgung für Windows) von Azure Stack Hub-Komponentendiensten zu sammeln. ETW-Protokolle werden maximal fünf Tage lang auf einer gemeinsam genutzten lokalen Freigabe gespeichert. Nachdem dieser Grenzwert erreicht wurde, werden die ältesten Dateien gelöscht, wenn neue Dateien erstellt werden. Die maximal zulässige Standardgröße pro Datei beträgt 200 MB. Die Größe wird alle zwei Minuten überprüft. Ab einer Größe von 200 MB wird die aktuelle Datei gespeichert und eine neue Datei generiert. Für die Gesamtdateigröße, die pro Ereignissitzung generiert werden kann, gilt außerdem eine Obergrenze von 8 GB.

#### <a name="get-azurestacklog"></a>Get-AzureStackLog

Das PowerShell-Cmdlet „Get-AzureStackLog“ kann verwendet werden, um Protokolle für alle Komponenten einer Azure Stack Hub-Umgebung zu sammeln. Sie werden an einem vom Benutzer angegebenen Ort in ZIP-Dateien gespeichert. Wenn das Team für den technischen Support für Azure Stack Hub Ihre Protokolle für die Behandlung eines Problems benötigt, werden Sie unter Umständen gebeten, „Get-AzureStackLog“ auszuführen.

> [!CAUTION]
> Es kann sein, dass diese Protokolldateien personenbezogene Informationen enthalten. Berücksichtigen Sie dies, bevor Sie Protokolldateien öffentlich bereitstellen.

Es folgen einige Beispiele für die gesammelten Protokolltypen:

* **Azure Stack Hub-Bereitstellungsprotokolle**
* **Windows-Ereignisprotokolle**
* **Panther-Protokolle**
* **Clusterprotokolle**
* **Speicherdiagnoseprotokolle**
* **ETW-Protokolle**

Diese Dateien werden vom Collector der Ablaufverfolgung gesammelt und in einer Freigabe gespeichert. „Get-AzureStackLog“ kann dann verwendet werden, um sie bei Bedarf zu sammeln.

