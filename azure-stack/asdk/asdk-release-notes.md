---
title: Versionshinweise zum ASDK
description: Verbesserungen, Fehlerbehebungen und bekannte Probleme im Azure Stack Development Kit (ASDK).
author: sethmanheim
ms.topic: article
ms.date: 11/16/2020
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 08/10/2020
ms.openlocfilehash: 6eb124193f477eb4418909f6b8e79781e948d135
ms.sourcegitcommit: 6db48bd8e6ccfaaa897713ad7eb2846a8d506358
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94885749"
---
# <a name="asdk-release-notes"></a>Versionshinweise zum ASDK

Dieser Artikel bietet Informationen zu Änderungen, Fehlerbehebungen und bekannten Problemen im Azure Stack Development Kit (ASDK). Wenn Sie nicht sicher sind, welche Version Sie ausführen, [überprüfen Sie sie im Portal](../operator/azure-stack-updates.md).

Abonnieren Sie den ![RSS](./media/asdk-release-notes/feed-icon-14x14.png) [RSS](https://aka.ms/asdkrss)-Feed, um in Bezug auf Neuerungen im ASDK auf dem Laufenden zu bleiben.

::: moniker range="azs-2008"
## <a name="build-12008059"></a>Build 1.2008.0.59

### <a name="new-features"></a>Neue Funktionen

- Eine Liste der in diesem Release behobenen Probleme, Änderungen und neuen Features finden Sie in den entsprechenden Abschnitten in den [Azure Stack-Versionshinweisen](../operator/release-notes.md).

### <a name="fixed-and-known-issues"></a>Behobene und bekannte Probleme

::: moniker-end

::: moniker range="azs-2005"
## <a name="build-12005040"></a>Build 1.2005.0.40

### <a name="new-features"></a>Neue Funktionen

- Eine Liste der in diesem Release behobenen Probleme, Änderungen und neuen Features finden Sie in den entsprechenden Abschnitten in den [Azure Stack-Versionshinweisen](../operator/release-notes.md).

### <a name="fixed-and-known-issues"></a>Behobene und bekannte Probleme

- Das Kennwort für das Entschlüsselungszertifikat ist eine neue Option, um das Kennwort für selbstsignierte Zertifikat (PFX-Datei) anzugeben, das den privaten Schlüssel enthält, der zum Entschlüsseln der Sicherungsdaten notwendig ist. Dieses Kennwort ist nur erforderlich, wenn die Sicherung mit einem Zertifikat verschlüsselt ist.
- Es wurde ein Problem behoben, das dazu führte, dass die Cloudwiederherstellung nicht erfolgreich war, wenn sich das ursprüngliche Kennwort des externen Zertifikats im Quellsystem mit mehreren Knoten geändert hat. 
- Eine Liste der bekannten Probleme bei Azure Stack in dieser Version finden Sie im Artikel [Bekannte Probleme](../operator/known-issues.md).
- Beachten Sie, dass verfügbare Hotfixes für Azure Stack nicht für das ASDK gelten.

#### <a name="initial-configuration-fails-in-asdk"></a>Anfängliche Konfiguration im ASDK nicht erfolgreich

- Beim Bereitstellen des ASDK werden möglicherweise die Fehlermeldungen **Status of 'Deployment-Phase0-DeployBareMetal' is 'Error'** (Status von „Deployment-Phase0-DeployBareMetal“: Fehler) und **Status of 'Deployment-InitialSteps' is 'Error'** (Status von „Deployment-InitialSteps“: Fehler) angezeigt.

- Problemumgehung:

1. Öffnen Sie die Datei „C:\CloudDeployment\Roles\PhysicalMachines\Tests\BareMetal.Tests.ps1“ in einem beliebigen Editor mit Zeilenzählung (beispielsweise in PowerShell ISE).

2. Ersetzen Sie die Zeile 822 durch Folgendes:

   ```powershell

   PartNumber = if($_.PartNumber) {$_.PartNumber.Trim()} else {""};

   ```  
::: moniker-end

::: moniker range="azs-2002"
## <a name="build-12002035"></a>Build 1.2002.0.35

### <a name="new-features"></a>Neue Funktionen

- Eine Liste der in diesem Release behobenen Probleme, Änderungen und neuen Features finden Sie in den entsprechenden Abschnitten in den [Azure Stack-Versionshinweisen](../operator/release-notes.md).

### <a name="fixed-and-known-issues"></a>Behobene und bekannte Probleme

- Das Kennwort für das Entschlüsselungszertifikat ist eine neue Option, um das Kennwort für selbstsignierte Zertifikat (PFX-Datei) anzugeben, das den privaten Schlüssel enthält, der zum Entschlüsseln der Sicherungsdaten notwendig ist. Dieses Kennwort ist nur erforderlich, wenn die Sicherung mit einem Zertifikat verschlüsselt ist.

- Eine Liste der bekannten Probleme bei Azure Stack in dieser Version finden Sie im Artikel [Bekannte Probleme](../operator/known-issues.md).

- Beachten Sie, dass verfügbare Hotfixes für Azure Stack nicht für das ASDK gelten.

#### <a name="sql-vm-provision-fails-in-asdk"></a>Fehler bei der SQL-VM-Bereitstellung in ASDK

- Geltungsbereich: Dieses Problem betrifft ASDK 2002.
- Ursache: Wenn Sie eine neue SQL-VM in ASDK 2002 erstellen, wird möglicherweise die Fehlermeldung **Erweiterung mit Publisher "Microsoft.SqlServer.Management", Typ "SqlIaaSAgent" und Typhandlerversion "2.0" wurde im Erweiterungsrepository nicht gefunden** angezeigt. **SqlIaaSAgent** 2.0 ist in Azure Stack Hub nicht vorhanden.
::: moniker-end

::: moniker range="azs-1910"
## <a name="build-11910058"></a>Build 1.1910.0.58

### <a name="new-features"></a>Neue Funktionen

- Eine Liste der in diesem Release behobenen Probleme, Änderungen und neuen Features finden Sie in den entsprechenden Abschnitten in den [Azure Stack-Versionshinweisen](../operator/release-notes.md).

### <a name="fixed-and-known-issues"></a>Behobene und bekannte Probleme

- Ein Problem beim Sammeln von Protokollen und dem Speichern dieser Protokolle in einem Azure Storage-Blobcontainer wurde behoben. Die Syntax für diesen Vorgang lautet wie folgt:

  ```powershell
  Get-AzureStackLog -OutputSasUri "<Blob service SAS Uri>"
  ``` 

- Ein Bereitstellungsproblem behoben, bei dem ein langsamer ladender Spoolerdienst das Entfernen einiger Windows-Features verhindert und einen Neustart erfordert hat.
- Eine Liste der bekannten Probleme bei Azure Stack in dieser Version finden Sie im Artikel [Bekannte Probleme](../operator/known-issues.md).
- Beachten Sie, dass verfügbare Hotfixes für Azure Stack nicht für das ASDK gelten.
::: moniker-end
