---
title: Diagnose in Azure Stack
description: Es wird beschrieben, wie Sie Protokolldateien für die Diagnose in Azure Stack sammeln.
services: azure-stack
author: justinha
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 05/29/2019
ms.author: justinha
ms.reviewer: adshar
ms.lastreviewed: 11/20/2018
ms.openlocfilehash: 98ad556bf1b0b5f0297cb7964cd9911a50145496
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691758"
---
# <a name="azure-stack-diagnostics-tools"></a>Azure Stack-Diagnosetools

Bei Azure Stack handelt es sich um eine große Sammlung von Komponenten, die zusammenarbeiten und miteinander interagieren. Alle diese Komponenten generieren eigene eindeutige Protokolle. Dies kann die Diagnose von Problemen zu einer Herausforderung machen – insbesondere bei Fehlern, die von verschiedenen interagierenden Azure Stack-Komponenten stammen.

Mit unseren Diagnosetools wird sichergestellt, dass der Mechanismus für die Protokollsammlung einfach und effizient ist. Im folgenden Diagramm ist dargestellt, wie Tools zum Sammeln von Protokollen in Azure Stack funktionieren:

![Azure Stack-Diagnosetools](media/azure-stack-diagnostics/get-azslogs.png)

## <a name="trace-collector"></a>Collector der Ablaufverfolgung

Der Collector der Ablaufverfolgung ist standardmäßig aktiviert und wird dauerhaft im Hintergrund ausgeführt, um alle ETW-Protokolle (Ereignisablaufverfolgung für Windows) von Azure Stack-Komponentendiensten zu sammeln. ETW-Protokolle werden für fünf Tage in einer gemeinsamen lokalen Freigabe gespeichert. Nachdem dieser Grenzwert erreicht wurde, werden die ältesten Dateien gelöscht, wenn neue Dateien erstellt werden. Die maximal zulässige Standardgröße pro Datei beträgt 200 MB. Die Größe wird alle zwei Minuten überprüft. Ab einer Größe von 200 MB wird die aktuelle Datei gespeichert und eine neue Datei generiert. Für die Gesamtdateigröße, die pro Ereignissitzung generiert werden kann, gilt außerdem eine Obergrenze von 8 GB.

## <a name="log-collection-tool"></a>Tool für die Protokollsammlung

Das PowerShell-Cmdlet **Get-AzureStackLog** kann verwendet werden, um Protokolle für alle Komponenten einer Azure Stack-Umgebung zu sammeln. Sie werden an einem vom Benutzer angegebenen Ort in ZIP-Dateien gespeichert. Wenn das Team für den technischen Support für Azure Stack Ihre Protokolle für die Behandlung eines Problems benötigt, werden Sie unter Umständen gebeten, dieses Tool auszuführen.

> [!CAUTION]
> Es kann sein, dass diese Protokolldateien personenbezogene Informationen enthalten. Berücksichtigen Sie dies, bevor Sie Protokolldateien öffentlich bereitstellen.

Es folgen einige Beispiele für die gesammelten Protokolltypen:

* **Azure Stack-Bereitstellungsprotokolle**
* **Windows-Ereignisprotokolle**
* **Panther-Protokolle**
* **Clusterprotokolle**
* **Speicherdiagnoseprotokolle**
* **ETW-Protokolle**

Diese Dateien werden vom Collector der Ablaufverfolgung gesammelt und in einer Freigabe gespeichert. Das PowerShell-Cmdlet **Get-AzureStackLog** kann dann verwendet werden, um sie bei Bedarf zu sammeln.

### <a name="to-run-get-azurestacklog-on-azure-stack-integrated-systems"></a>So führen Sie „Get-AzureStackLog“ in integrierten Azure Stack-Systemen aus

Um das Protokollerfassungstool in einem integrierten System auszuführen, benötigen Sie Zugriff auf den privilegierten Endpunkt (PEP). Das folgende Beispielskript können Sie mit dem PEP ausführen, um Protokolle in einem integrierten System zu sammeln:

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.

$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)

$shareCred = Get-Credential

$s = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2)  #provide the time that includes the period for your issue

Invoke-Command -Session $s {    Get-AzureStackLog -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if($s)
{
    Remove-PSSession $s
}
```

### <a name="run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system"></a>Ausführen von Get-AzureStackLog in einem System mit dem Azure Stack Development Kit (ASDK)

Verwenden Sie diese Schritte zum Ausführen von `Get-AzureStackLog` auf einem ASDK-Hostcomputer.

1. Melden Sie sich auf dem ASDK-Hostcomputer als **AzureStack\CloudAdmin** an.
2. Öffnen Sie als Administrator ein neues PowerShell-Fenster.
3. Führen Sie PowerShell-Cmdlet **Get-AzureStackLog**.

#### <a name="examples"></a>Beispiele

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

* Erfassen Sie Protokolle, und speichern Sie sie im angegebenen Azure Storage-Blobcontainer. Die allgemeine Syntax für diesen Vorgang lautet wie folgt:

  ```powershell
  Get-AzureStackLog -OutputSasUri "<Blob service SAS Uri>"
  ```

  Beispiel:

  ```powershell
  Get-AzureStackLog -OutputSasUri "https://<storageAccountName>.blob.core.windows.net/<ContainerName><SAS token>"
  ```

  > [!NOTE]
  > Diese Vorgehensweise ist nützlich, wenn Sie über den Microsoft-Support eine Anfrage erstellen und zum Hochladen von Protokollen aufgefordert werden. Auch wenn Sie nicht über eine SMB-Freigabe verfügen, auf die von der ERCS-VM aus zugegriffen werden kann, und Ihre ERCS-VM nicht über Internetzugriff verfügt, können Sie auf Ihrer Azure Stack-Instanz ein Blobspeicherkonto erstellen, um die Protokolle zu übertragen. Anschließend können Sie Ihren Client nutzen, um diese Protokolle abzurufen und für Microsoft hochzuladen.  

  Um das SAS-Token für das Speicherkonto zu generieren, sind folgende Berechtigungen erforderlich:

  * Zugriff auf Azure Blob Storage-Dienst
  * Zugriff auf den Containerressourcentyp

  Um einen SAS-URI-Wert für den Parameter `-OutputSasUri` verwendet zu generieren, führen Sie die folgenden Schritte aus:

  1. Erstellen Sie ein Speicherkonto, indem Sie den Schritten [in diesem Artikel](/azure/storage/common/storage-quickstart-create-account) ausführen.
  2. Öffnen Sie eine Instanz des Azure Storage-Explorers.
  3. Stellen Sie eine Verbindung mit dem in Schritt 1 erstellten Speicherkonto her.
  4. Navigieren Sie zu **Blobcontainer** in **Speicherdienste**.
  5. Wählen Sie **Neuen Container erstellen**  aus.
  6. Klicken Sie mit der rechten Maustaste auf den neuen Container und dann auf **Shared Access Signature abrufen**.
  7. Wählen Sie eine gültige **Startzeit** und **Endzeit** entsprechend Ihren Anforderungen aus.
  8. Wählen Sie die erforderlichen Berechtigungen **Lesen**, **Schreiben** und **Auflisten** aus.
  9. Klicken Sie auf **Erstellen**.
  10. Sie erhalten eine Shared Access Signature. Kopieren Sie den URL-Teil, und übergeben Sie ihn an den `-OutputSasUri`-Parameter.

### <a name="parameter-considerations-for-both-asdk-and-integrated-systems"></a>Überlegungen zu den Parametern für das ASDK und integrierte Systeme

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

* Sie können den **TimeOutInMinutes**-Parameter verwenden, um das Timeout für die Sammlung von Protokollen festzulegen. Es ist standardmäßig auf 150 (2,5 Stunden) festgelegt.
* Die Protokollsammlung für Dumpdateien ist standardmäßig deaktiviert. Sie können sie mithilfe des Switch-Parameters **IncludeDumpFile** aktivieren.
* Derzeit können Sie den **FilterByRole**-Parameter verwenden, um die Protokollsammlung nach den folgenden Rollen zu filtern:

  |   |   |   |    |
  | - | - | - | -  |
  |ACS                   |CacheService                   |IBC                            |OEM|
  |ACSDownloadService    |Compute                        |InfraServiceController         |OnboardRP|
  |ACSFabric             |CPI                            |KeyVaultAdminResourceProvider  |PXE|
  |ACSFrontEnd           |CRP                            |KeyVaultControlPlane           |QueryServiceCoordinator|
  |ACSMetrics            |DeploymentMachine              |KeyVaultDataPlane              |QueryServiceWorker|
  |ACSMigrationService   |DiskRP                         |KeyVaultInternalControlPlane   |SeedRing|
  |ACSMonitoringService  |Domäne                         |KeyVaultInternalDataPlane      |SeedRingServices|
  |ACSSettingsService    |ECE                            |KeyVaultNamingService          |SLB|
  |ACSTableMaster        |EventAdminRP                   |MDM                            |SQL|
  |ACSTableServer        |EventRP                        |MetricsAdminRP                 |SRP   |
  |ACSWac                |ExternalDNS                    |MetricsRP                      |Storage|
  |ADFS                  |FabricRing                     |MetricsServer                  |StorageController   |
  |ApplicationController |FabricRingServices             |MetricsStoreService            |URP   |
  |ASAppGateway          |FirstTierAggregationService    |MonAdminRP                     |UsageBridge|
  |AzureBridge           |FRP                            |MonRP                          |VirtualMachines   |
  |AzureMonitor          |Gateway                        |NC                             |WAS|
  |BareMetal             |HealthMonitoring               |NonPrivilegedAppGateway        |WASPUBLIC|
  |BRP                   |HintingServiceV2               |NRP                            |   |
  |CA                    |HRP                            |OboService                     |   |
  |   |   |   |    |

### <a name="additional-considerations"></a>Zusätzliche Überlegungen

* Je nachdem, welche Rollen in den Protokollen gesammelt werden, kann dieser Befehl mehr Zeit beanspruchen. Zu den entscheidenden Faktoren gehören auch der Zeitraum, der für die Protokollsammlung angegeben wurde, und die Anzahl von Knoten in der Azure Stack-Umgebung.
* Überprüfen Sie während der Protokollsammlung den neuen Ordner, der mit dem **OutputSharePath**-Parameter des Befehls erstellt wird.
* Für jede Rolle sind die Protokolle in separaten ZIP-Dateien enthalten. Abhängig von der Größe der gesammelten Protokolle kann das Protokoll einer Rolle möglicherweise in mehrere ZIP-Dateien aufgeteilt werden. Wenn Sie alle Protokolldateien in denselben Ordner entzippen möchten, verwenden Sie für eine solche Rolle ein Tool, das den Entzipvorgang in einer Sammeloperation durchführen kann (z.B. 7-Zip). Wählen Sie alle ZIP-Dateien für die Rolle und dann **Hier entpacken** aus. Damit werden die Protokolldateien für diese Rolle in einem zusammengeführten Ordner entzippt.
* Eine Datei namens **Get-AzureStackLog_Output.log** wird ebenfalls in dem Ordner mit den gezippten Protokolldateien erstellt. Diese Datei ist ein Protokoll der Befehlsausgabe, die Sie zum Beheben von Problemen während der Protokollsammlung verwenden können. In einigen Fällen enthält die Protokolldatei `PS>TerminatingError`-Einträge, die ignoriert werden können, es sei denn, nach der Protokollsammlung fehlen erwartete Protokolldateien.
* Zum Untersuchen eines bestimmten Fehlers werden unter Umständen die Protokolle von mehr als einer Komponente benötigt.

  * System- und Ereignisprotokolle für alle Infrastruktur-VMs werden unter der Rolle **VirtualMachines** gesammelt.
  * System- und Ereignisprotokolle für alle Hosts werden unter der Rolle **BareMetal** gesammelt.
  * Failovercluster- und Hyper-V-Ereignisprotokolle werden unter der Rolle **Storage** gesammelt.
  * ACS-Protokolle werden unter den Rollen **Storage** und **ACS** gesammelt.

> [!NOTE]
> Größen- und Altersgrenzwerte für die gesammelten Protokolle werden erzwungen, da es sehr wichtig ist, für eine effiziente Nutzung Ihres Speicherplatzes zu sorgen. So wird sichergestellt, dass keine übermäßig hohe Anzahl von Protokollen gespeichert wird. Allerdings werden beim Diagnostizieren von Problemen manchmal Protokolle benötigt, die aufgrund dieser Grenzwerte möglicherweise nicht mehr vorhanden sind. Daher wird **dringend empfohlen**, Ihre Protokolle alle acht bis zwölf Stunden in einen externen Speicher zu verlagern (Speicherkonto in Azure, zusätzliches lokales Speichergerät oder Ähnliches) und je nach Ihren Anforderungen dort einen bis drei Monate lang aufzubewahren. Stellen Sie außerdem sicher, dass der Speicherort verschlüsselt ist.

### <a name="invoke-azurestackondemandlog"></a>Invoke-AzureStackOnDemandLog

Sie können das Cmdlet **Invoke-AzureStackOnDemandLog** verwenden, um bedarfsgesteuerte Protokolle für bestimmte Rollen zu generieren (siehe Liste am Ende dieses Abschnitts). Die von diesem Cmdlet generierten Protokolle sind standardmäßig nicht im Protokollpaket enthalten, das Sie beim Ausführen des Cmdlets **Get-AzureStackLog** erhalten. Es wird außerdem empfohlen, diese Protokolle nur auf Anforderung des Microsoft-Supportteams zu erfassen.

Derzeit können Sie den Parameter `-FilterByRole` verwenden, um die Protokollsammlung nach den folgenden Rollen zu filtern:

* OEM
* NC
* SLB
* Gateway

#### <a name="example-of-collecting-on-demand-logs"></a>Beispiel für das Erfassen von bedarfsgesteuerten Protokollen

```powershell
$ip = "<IP address of the PEP VM>" # You can also use the machine name instead of IP here.

$pwd= ConvertTo-SecureString "<cloud admin password>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<domain name>\CloudAdmin", $pwd)

$shareCred = Get-Credential

$s = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2)  #provide the time that includes the period for your issue

Invoke-Command -Session $s
{
   Invoke-AzureStackOnDemandLog -Generate -FilterByRole "<on-demand role name>" #Provide the supported on-demand role name : OEM, NC, SLB , Gateway
   Get-AzureStackLog -OutputSharePath "<external share address>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate

}

if($s)
{
   Remove-PSSession $s
}
```

## <a name="next-steps"></a>Nächste Schritte

[Microsoft Azure Stack troubleshooting (Problembehandlung für Microsoft Azure Stack)](azure-stack-troubleshooting.md)
