---
title: Migrieren von Azure PowerShell-Skripts von AzureRM zu Az in Azure Stack Hub
description: Hier lernen Sie die Schritte und Tools kennen, mit denen Sie Skripts vom AzureRM-Modul zum neuen Az-Modul in Azure Stack Hub migrieren können.
author: mattbriggs
ms.author: mabrigg
ms.topic: conceptual
ms.date: 04/14/2020
ms.reviewer: sijuman
ms.lastreviewed: 04/14/2020
ms.openlocfilehash: bcfd4b467013a6f207efa37bc02917d3800e3eb0
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81395182"
---
# <a name="migrate-from-azurerm-to-azure-powershell-az-in-azure-stack-hub"></a>Migrieren von AzureRM zu Azure PowerShell Az in Azure Stack Hub

Das Az-Modul bietet die gleichen Features wie AzureRM, verwendet aber kürzere und konsistentere Cmdlet-Namen.
Für die AzureRM-Cmdlets geschriebene Skripts funktionieren nicht automatisch auch mit dem neuen Modul. Zur Vereinfachung des Übergangs bietet Az Tools, die es Ihnen ermöglichen, Ihre bereits vorhandenen Skripts mit AzureRM auszuführen. Eine Migration zu einem neuen Befehlssatz ist immer unangenehm. Dieser Artikel hilft Ihnen jedoch dabei, den Umstieg auf das neue Modul in die Wege zu leiten.

Die vollständige Liste mit grundlegenden Änderungen zwischen AzureRM und Az finden Sie im [Migrationsleitfaden für Az 1.0.0](https://docs.microsoft.com/powershell/azure/migrate-az-1.0.0).

## <a name="check-for-installed-versions-of-azurerm"></a>Durchführen einer Überprüfung auf installierte Versionen von AzureRM

Das Az-Modul kann parallel zum AzureRM-Modul installiert werden, aber diese Vorgehensweise wird nicht empfohlen. Überprüfen Sie, welche Versionen von AzureRM auf Ihrem System installiert sind, bevor Sie Migrationsschritte ausführen. So können Sie sicherstellen, dass für Skripts bereits das aktuelle Release verwendet wird, und Sie können ermitteln, ob Sie Befehlsaliase ohne Deinstallation von AzureRM aktivieren können.

Führen Sie den folgenden Befehl aus, um zu überprüfen, welche Versionen von AzureRM bei Ihnen installiert sind:

```powershell-interactive
Get-InstalledModule -Name AzureRM -AllVersions
```

## <a name="check-current-scripts-work-with-azurerm"></a>Vergewissern, dass die aktuellen Skripts mit AzureRM funktionieren

Das ist der wichtigste Schritt überhaupt. Führen Sie Ihre vorhandenen Skripts aus, und vergewissern Sie sich, dass diese mit der _aktuellen_ Version von AzureRM (__2.5.0__) funktionieren. Sollten Ihre Skripts nicht funktionieren, lesen Sie das [AzureRM-Migrationshandbuch](/powershell/azure/azurerm/migration-guide.6.0.0).

## <a name="install-the-azure-powershell-az-module"></a>Installieren des Az-Moduls von Azure PowerShell

Der erste Schritt besteht darin, das Az-Modul auf Ihrer Plattform zu installieren. Wenn Sie Az installieren, ist es ratsam, AzureRM zu deinstallieren. In den folgenden Schritten erfahren Sie, wie Sie Ihre bereits vorhandenen Skripts weiterhin ausführen und die Kompatibilität mit älteren Cmdlet-Namen sicherstellen können.

Gehen Sie zum Installieren des Az-Moduls von Azure PowerShell wie folgt vor:

* __EMPFOHLEN__: [Deinstallieren Sie das AzureRM-Modul.](/powershell/azure/uninstall-az-ps#uninstall-the-azurerm-module)
  Achten Sie darauf, _alle_ installierten Versionen von AzureRM zu entfernen, nicht nur die neueste Version.
* [Installieren Sie das Az-Modul.](https://docs.microsoft.com/powershell/azure/install-az-ps)

## <a name="enable-azurerm-compatibility-aliases"></a>Aktivieren von AzureRM-Kompatibilitätsaliasen 

> [!IMPORTANT]
>
> Aktivieren Sie den Kompatibilitätsmodus nur, wenn Sie _alle_ Versionen von AzureRM deinstalliert haben. Wenn Sie den Kompatibilitätsmodus bei noch vorhandenen AzureRM-Cmdlets aktivieren, kann dies zu unvorhersehbarem Verhalten führen. Überspringen Sie diesen Schritt, wenn Sie sich für die Beibehaltung der AzureRM-Installation entschieden haben. Sie müssen sich dann aber darüber im Klaren sein, dass für alle AzureRM-Cmdlets die älteren Module verwendet und keine Az-Cmdlets aufgerufen werden.

Wenn Sie AzureRM deinstalliert haben und Ihre Skripts mit der neuesten Version von AzureRM funktionieren, ist der nächste Schritt das Aktivieren des Kompatibilitätsmodus für das Az-Modul. Die Kompatibilität wird mithilfe des folgenden Befehls aktiviert:

```powershell-interactive
Enable-AzureRmAlias -Scope CurrentUser
```

Aliase ermöglichen die Verwendung alter Cmdlet-Namen bei installiertem Az-Modul. Diese Aliase werden in das Benutzerprofil für den ausgewählten Bereich geschrieben. Sollte kein Benutzerprofil vorhanden sein, wird eines erstellt.

> [!WARNING]
>
> Sie können für diesen Befehl zwar einen anderen Bereich (`-Scope`) verwenden, aber dies wird nicht empfohlen. Da Aliase in das Benutzerprofil für den ausgewählten Bereich geschrieben werden, empfiehlt es sich, sie auf einen möglichst kleinen Bereich zu beschränken. Eine systemweite Aktivierung von Aliasen kann auch zu Problemen für andere Benutzer führen, die AzureRM in ihrem lokalen Bereich installiert haben.

Führen Sie Ihre Skripts nach der Aktivierung des Aliasmodus erneut aus, um sich zu vergewissern, dass sie weiterhin wie erwartet funktionieren. 

## <a name="change-module-and-cmdlet-names"></a>Ändern von Modul- und Cmdlet-Namen

Ganz allgemein haben sich die Modulnamen geändert, sodass `AzureRM` und `Azure` zu `Az` werden. Gleiches gilt für die Cmdlets.
So wurde beispielsweise das Modul `AzureRM.Compute` in `Az.Compute` umbenannt. `New-AzureRMVM` wurde zu `New-AzVM`, und `Get-AzureStorageBlob` ist jetzt `Get-AzStorageBlob`.

Es gibt allerdings Ausnahmen für diese Namensänderung, die Sie kennen sollten. Einige Module wurden umbenannt oder mit vorhandenen Modulen zusammengeführt, ohne dass sich dies auf das Suffix der zugehörigen Cmdlets ausgewirkt hat (bis auf die Änderung von `AzureRM` oder `Azure` in `Az`). Andernfalls wurde das vollständige Suffix des Cmdlets geändert, um den neuen Modulnamen widerzuspiegeln.

| AzureRM-Modul | Az-Modul | Cmdlet-Suffix geändert? |
|----------------|-----------|------------------------|
| AzureRM.Profile | Az.Accounts | Ja |
| AzureRM.Insights | Az.Monitor | Ja |
| AzureRM.Tags | Az.Resources | Nein |
| AzureRM.UsageAggregates | Az.Billing | Nein |
| AzureRM.Consumption | Az.Billing | Nein |

## <a name="summary"></a>Zusammenfassung

Mithilfe dieser Schritte können Sie alle Ihre bereits vorhandenen Skripts für die Verwendung des neuen Moduls aktualisieren. Falls Sie Fragen haben oder bei Ihrer Migration Probleme aufgetreten sind, kommentieren Sie diesen Artikel, damit wir die Anweisungen verbessern können.

## <a name="breaking-changes-for-az-100"></a>Grundlegende Änderungen für Az 1.0.0

Dieses Dokument enthält ausführliche Informationen zu den Änderungen zwischen AzureRM 6.x und dem neuen Az-Modul, Version 1.x und höher. Das Inhaltsverzeichnis führt Sie durch einen vollständigen Migrationspfad, einschließlich modulspezifischer Änderungen, die sich auf Ihre Skripte auswirken können.

## <a name="general-breaking-changes"></a>Allgemeine grundlegende Änderungen

In diesem Abschnitt werden die allgemeinen grundlegenden Änderungen beschrieben, die Teil des neuen Entwurfs des Az-Moduls sind.

### <a name="cmdlet-noun-prefix-changes"></a>Präfixänderungen für Cmdlet-Nomen

Im AzureRM-Modul verwenden Cmdlets entweder `AzureRM` oder `Azure` als Nomenpräfix.  Az vereinfacht und normalisiert Cmdlet-Namen, sodass alle Cmdlets „Az“ als Nomenpräfix verwenden. Beispiel:

```powershell  
Get-AzureRMVM
Get-AzureKeyVaultSecret
```

Geändert in:

```powershell  
Get-AzVM
Get-AzKeyVaultSecret
```

Um die Umstellung auf diese neuen Cmdlet-Namen zu vereinfachen, werden mit Az die beiden neuen Cmdlets [Enable-AzureRmAlias](/powershell/module/az.accounts/enable-azurermalias) und [Disable-AzureRmAlias](/powershell/module/az.accounts/disable-azurermalias) eingeführt.  `Enable-AzureRmAlias` erstellt Aliase für die älteren Cmdlet-Namen in AzureRM, die den neueren Az-Cmdlet-Namen zugeordnet sind. Indem Sie das Argument `-Scope` mit `Enable-AzureRmAlias` verwenden, können Sie auswählen, wo Aliase aktiviert werden.

Ein Beispiel hierfür ist das folgende Skript in AzureRM:

```powershell  
#Requires -Modules AzureRM.Storage
Get-AzureRmStorageAccount | Get-AzureStorageContainer | Get-AzureStorageBlob
```

Es kann mit `Enable-AzureRmAlias` mit minimalen Änderungen ausgeführt werden:

```powershell  
#Requires -Modules Az.Storage
Enable-AzureRmAlias -Scope Process
Get-AzureRmStorageAccount | Get-AzureStorageContainer | Get-AzureStorageBlob
```

Durch das Ausführen von `Enable-AzureRmAlias -Scope CurrentUser` werden die Aliase für alle von Ihnen geöffneten PowerShell-Sitzungen aktiviert. Nach der Ausführung dieses Cmdlets sind für ein Skript dieser Art keinerlei Änderungen erforderlich:

```powershell  
Get-AzureRmStorageAccount | Get-AzureStorageContainer | Get-AzureStorageBlob
```

Ausführliche Informationen zur Nutzung der Alias-Cmdlets finden Sie in der [Enable-AzureRmAlias-Referenz](/powershell/module/az.accounts/enable-azurermalias).

Wenn Sie bereit sind, Aliase zu deaktivieren, entfernt `Disable-AzureRmAlias` die erstellten Aliase. Ausführliche Informationen finden Sie in der [Disable-AzureRmAlias-Referenz](/powershell/module/az.accounts/disable-azurermalias).

> [!IMPORTANT]
> Wenn Sie Aliase deaktivieren, stellen Sie sicher, dass sie für _alle_ Bereiche deaktiviert sind, in denen Aliase aktiviert waren.

### <a name="module-name-changes"></a>Änderungen von Modulnamen

Die Modulnamen wurden von `AzureRM.*` in `Az.*` geändert, mit Ausnahme der folgenden Module:

| AzureRM-Modul | Az-Modul |
|----------------|-----------|
| Azure.Storage | Az.Storage |
| Azure.AnalysisServices | Az.AnalysisServices |
| AzureRM.Profile | Az.Accounts |
| AzureRM.Insights | Az.Monitor |
| AzureRM.RecoveryServices.Backup | Az.RecoveryServices |
| AzureRM.RecoveryServices.SiteRecovery | Az.RecoveryServices |
| AzureRM.Tags | Az.Resources |
| AzureRM.MachineLearningCompute | Az.MachineLearning |
| AzureRM.UsageAggregates | Az.Billing |
| AzureRM.Consumption | Az.Billing |

Die Änderungen der Modulnamen bedeuten, dass alle Skripts, die `#Requires` oder `Import-Module` zum Laden von bestimmten Modulen nutzen, auf die Verwendung des neuen Moduls umgestellt werden müssen. Für Module, bei denen sich das Cmdlet-Suffix nicht geändert hat, bedeutet dies, dass sich das Suffix, das den Operationsbereich angibt, _nicht_ geändert hat, obwohl der Modulname geändert wurde.

#### <a name="migrating-requires-and-import-module-statements"></a>Migrieren von Anweisungen vom Typ „#Requires“ und „Import-Module“

Skripts, für die `#Requires` oder `Import-Module` zum Deklarieren einer Abhängigkeit von AzureRM-Modulen verwendet wird, müssen aktualisiert werden, damit die neuen Modulnamen genutzt werden. Beispiel:

```powershell  
#Requires -Module AzureRM.Compute
```

Änderung erforderlich in:

```powershell  
#Requires -Module Az.Compute
```

Für `Import-Module`:

```powershell  
Import-Module -Name AzureRM.Compute
```

Änderung erforderlich in:

```powershell  
Import-Module -Name Az.Compute
```

### <a name="migrating-fully-qualified-cmdlet-invocations"></a>Migrieren von vollqualifizierten Cmdlet-Aufrufen

Bei Verwendung von Skripts, für die Cmdlet-Aufrufe mit Modulqualifikation verwendet werden, z. B.:

```powershell  
AzureRM.Compute\Get-AzureRmVM
```

Diese müssen geändert werden, damit die neuen Modul- und Cmdlet-Namen verwendet werden:

```powershell  
Az.Compute\Get-AzVM
```

### <a name="migrating-module-manifest-dependencies"></a>Migrieren von Modulmanifestabhängigkeiten

Für Module, die Abhängigkeiten von AzureRM-Modulen anhand einer Modulmanifestdatei (.psd1) ausdrücken, müssen die Modulnamen jeweils im Abschnitt `RequiredModules` aktualisiert werden:

```powershell
RequiredModules = @(@{ModuleName="AzureRM.Profile"; ModuleVersion="5.8.2"})
```

Änderung erforderlich in:

```powershell
RequiredModules = @(@{ModuleName="Az.Profile"; ModuleVersion="1.0.0"})
```

### <a name="removed-modules"></a>Entfernte Module

Folgende Module wurden entfernt:

- `AzureRM.Backup`
- `AzureRM.Compute.ManagedService`
- `AzureRM.Scheduler`

Die Tools für diese Dienste werden nicht mehr aktiv unterstützt.  Kunden wird die zeitnahe Umstellung auf alternative Dienste empfohlen.

### <a name="windows-powershell-51-and-net-472"></a>Windows PowerShell 5.1 und .NET 4.7.2

Für die Verwendung von Az mit PowerShell 5.1 für Windows ist die Installation von .NET Framework 4.7.2 erforderlich. Die Verwendung von PowerShell Core 6.x oder höher erfordert kein .NET Framework.

### <a name="temporary-removal-of-user-login-using-pscredential"></a>Vorübergehende Entfernung der Benutzeranmeldung per PSCredential

Aufgrund von Änderungen am Authentifizierungsablauf für .NET Standard entfernen wir die Benutzeranmeldung per PSCredential vorübergehend. Diese Funktion wird mit dem PowerShell 5.1-Release für Windows vom 15.01.2019 wieder eingeführt. Eine ausführliche Beschreibung finden Sie unter [diesem GitHub-Problem](https://github.com/Azure/azure-powershell/issues/7430).

### <a name="default-device-code-login-instead-of-web-browser-prompt"></a>Anmeldung per Standardgerätecode anstelle einer Webbrowser-Eingabeaufforderung

Aufgrund von Änderungen am Authentifizierungsablauf für .NET Standard nutzen wir die Geräteanmeldung während der interaktiven Anmeldung als Standardanmeldungsablauf. Die Anmeldung per Webbrowser wird im PowerShell 5.1-Release für Windows vom 15.01.2019 wieder als Standard eingeführt. Benutzer können dann die Geräteanmeldung mit einem Switch-Parameter wählen.

## <a name="module-breaking-changes"></a>Grundlegende Änderungen am Modul

In diesem Abschnitt werden bestimmte wichtige Änderungen für einzelne Module und Cmdlets beschrieben.

### <a name="azapimanagement-previously-azurermapimanagement"></a>Az.ApiManagement (bisher AzureRM.ApiManagement)

- Folgende Cmdlets wurden entfernt:
  - New-AzureRmApiManagementHostnameConfiguration
  - Set-AzureRmApiManagementHostnames
  - Update-AzureRmApiManagementDeployment
  - Import-AzureRmApiManagementHostnameCertificate
  - Verwenden Sie stattdessen das Cmdlet **Set-AzApiManagement**, um diese Eigenschaften festzulegen.
- Die folgenden Eigenschaften wurden entfernt:
  - Die Eigenschaften `PortalHostnameConfiguration`, `ProxyHostnameConfiguration`, `ManagementHostnameConfiguration` und `ScmHostnameConfiguration` vom Typ `PsApiManagementHostnameConfiguration` wurden aus `PsApiManagementContext` entfernt. Verwenden Sie stattdessen `PortalCustomHostnameConfiguration`, `ProxyCustomHostnameConfiguration`, `ManagementCustomHostnameConfiguration` und `ScmCustomHostnameConfiguration` vom Typ `PsApiManagementCustomHostNameConfiguration`.
  - Die `StaticIPs`-Eigenschaft wurde aus PsApiManagementContext entfernt. Die Eigenschaft wurde in `PublicIPAddresses` und `PrivateIPAddresses` unterteilt.
  - Die erforderliche `Location`-Eigenschaft wurde aus dem Cmdlet New-AzureApiManagementVirtualNetwork entfernt.

### <a name="azbilling-previously-azurermbilling-azurermconsumption-and-azurermusageaggregates"></a>Az.Billing (bisher AzureRM.Billing, AzureRM.Consumption und AzureRM.UsageAggregates)

- Der Parameter `InvoiceName` wurde aus dem Cmdlet `Get-AzConsumptionUsageDetail` entfernt.  Für Skripts müssen andere Identitätsparameter für die Rechnung genutzt werden.

### <a name="azcompute-previously-azurermcompute"></a>Az.Compute (bisher AzureRM.Compute)

- `IdentityIds` wurden aus der `Identity`-Eigenschaft in den Objekten `PSVirtualMachine` und `PSVirtualMachineScaleSet` entfernt. In Skripts sollte der Wert dieses Felds nicht mehr verwendet werden, um Verarbeitungsentscheidungen zu treffen.
- Der Typ der `InstanceView`-Eigenschaft des `PSVirtualMachineScaleSetVM`-Objekts wurde von `VirtualMachineInstanceView` in `VirtualMachineScaleSetVMInstanceView` geändert.
- Die Eigenschaften `AutoOSUpgradePolicy` und `AutomaticOSUpgrade` wurden aus der `UpgradePolicy`-Eigenschaft entfernt.
- Der Typ der `Sku`-Eigenschaft im `PSSnapshotUpdate`-Objekt wurde von `DiskSku` in `SnapshotSku` geändert.
- `VmScaleSetVMParameterSet` wurde aus dem Cmdlet `Add-AzVMDataDisk` entfernt. Sie können einer ScaleSet-VM nicht mehr einzeln einen Datenträger hinzufügen.

### <a name="azkeyvault-previously-azurermkeyvault"></a>Az.KeyVault (bisher AzureRM.KeyVault)

- Die `PurgeDisabled`-Eigenschaft wurde aus den Objekten `PSKeyVaultKeyAttributes`, `PSKeyVaultKeyIdentityItem` und `PSKeyVaultSecretAttributes` entfernt. In Skripts sollte nicht mehr auf die ```PurgeDisabled```-Eigenschaft verwiesen werden, um Verarbeitungsentscheidungen zu treffen.

### <a name="azmonitor-previously-azurerminsights"></a>Az.Monitor (bisher AzureRM.Insights)

- Die Plural-Parameternamen `Categories` und `Timegrains` wurden in Singular-Parameternamen aus dem Cmdlet `Set-AzDiagnosticSetting` geändert. Skripts mit Verwendung von:
  ```powershell  
  Set-AzureRmDiagnosticSetting -Timegrains PT1M -Categories Category1, Category2
  ```

  Änderung erforderlich in:
  ```powershell  
  Set-AzDiagnosticSetting -Timegrain PT1M -Category Category1, Category2
  ```

### <a name="aznetwork-previously-azurermnetwork"></a>Az.Network (bisher AzureRM.Network)

- Der veraltete Parameter `ResourceId` wurde aus dem Cmdlet `Get-AzServiceEndpointPolicyDefinition` entfernt.
- Die verwaltete `EnableVmProtection`-Eigenschaft wurde aus dem `PSVirtualNetwork`-Objekt entfernt.
- Das veraltete Cmdlet `Set-AzVirtualNetworkGatewayVpnClientConfig` wurde entfernt.

Für Skripts sollten basierend auf den Werten dieser Felder keine Verarbeitungsentscheidungen mehr getroffen werden.

### <a name="azresources-previously-azurermresources"></a>Az.Resources (bisher AzureRM.Resources)

- Der Parameter `Sku` wurde aus dem Cmdlet `New/Set-AzPolicyAssignment` entfernt.
- Der Parameter `Password` wurde aus den Cmdlets `New-AzADServicePrincipal` und `New-AzADSpCredential` entfernt. Die Kennwörter werden automatisch generiert. Bei Verwendung von Skripts mit Bereitstellung des Kennworts:

  ```powershell  
  New-AzAdSpCredential -ObjectId 1f99cf81-0146-4f4e-beae-2007d0668476 -Password $secPassword
  ```

  Änderung erforderlich, damit das Kennwort aus der Ausgabe verwendet wird:

  ```powershell  
  $credential = New-AzAdSpCredential -ObjectId 1f99cf81-0146-4f4e-beae-2007d0668476
  $secPassword = $credential.Secret
  ```


### <a name="azstorage-previously-azurestorage-and-azurermstorage"></a>Az.Storage (bisher Azure.Storage und AzureRM.Storage)

- Der Standardparametersatz wurde in `OAuthParameterSet` geändert, um die Erstellung eines OAuth-Speicherkontexts nur mit dem Speicherkontonamen zu unterstützen.
  - Beispiel: `$ctx = New-AzureStorageContext -StorageAccountName $accountName`
- Der Parameter `Location` ist im Cmdlet `Get-AzStorageUsage` jetzt obligatorisch.
- Für die Methoden der Storage-API wird jetzt anstelle von synchronen API-Aufrufen das aufgabenbasierte asynchrone Muster (Task-based Asynchronous Pattern, TAP) verwendet. Die folgenden Beispiele veranschaulichen die neuen asynchronen Befehle:

#### <a name="blob-snapshot"></a>Momentaufnahme eines Blobs

AzureRM:

```powershell  
$b = Get-AzureStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$b.ICloudBlob.Snapshot()
```

Az:

```powershell  
$b = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$task = $b.ICloudBlob.SnapshotAsync()
$task.Wait()
$snapshot = $task.Result
```

#### <a name="share-snapshot"></a>Momentaufnahme freigeben

AzureRM:

```powershell  
$Share = Get-AzureStorageShare -Name $containerName -Context $ctx
$snapshot = $Share.Snapshot()
```

Az:

```powershell  
$Share = Get-AzStorageShare -Name $containerName -Context $ctx
$task = $Share.SnapshotAsync()
$task.Wait()
$snapshot = $task.Result
```

#### <a name="undelete-soft-deleted-blob"></a>Vorläufig gelöschten Blob wiederherstellen

AzureRM:

```powershell  
$b = Get-AzureStorageBlob -Container $containerName -Blob $blobName -IncludeDeleted -Context $ctx
$b.ICloudBlob.Undelete()
```

Az:

```powershell  
$b = Get-AzStorageBlob -Container $containerName -Blob $blobName -IncludeDeleted -Context $ctx
$task = $b.ICloudBlob.UndeleteAsync()
$task.Wait()
```

#### <a name="set-blob-tier"></a>Festlegen des Blobtarifs

AzureRM:

```powershell  
$blockBlob = Get-AzureStorageBlob -Container $containerName -Blob $blockBlobName -Context $ctx
$blockBlob.ICloudBlob.SetStandardBlobTier("hot")

$pageBlob = Get-AzureStorageBlob -Container $containerName -Blob $pageBlobName -Context $ctx
$pageBlob.ICloudBlob.SetPremiumBlobTier("P4")
```

Az:

```powershell  
$blockBlob = Get-AzStorageBlob -Container $containerName -Blob $blockBlobName -Context $ctx
$task = $blockBlob.ICloudBlob.SetStandardBlobTierAsync("hot")
$task.Wait()

$pageBlob = Get-AzStorageBlob -Container $containerName -Blob $pageBlobName -Context $ctx
$task = $pageBlob.ICloudBlob.SetPremiumBlobTierAsync("P4")
$task.Wait()
```

### <a name="azwebsites-previously-azurermwebsites"></a>Az.Websites (bisher AzureRM.Websites)

- Die veralteten Eigenschaften wurden aus den Objekten `PSAppServicePlan`, `PSCertificate`, `PSCloningInfo` und `PSSite` entfernt.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu PowerShell in Azure Stack Hub finden Sie unter [Erste Schritte mit PowerShell in Azure Stack Hub](../user/azure-stack-powershell-overview.md).
- Informationen zum Installieren des PowerShell Az-Moduls finden Sie unter [Installieren des PowerShell Az-Moduls für Azure Stack Hub](powershell-install-az-module.md).