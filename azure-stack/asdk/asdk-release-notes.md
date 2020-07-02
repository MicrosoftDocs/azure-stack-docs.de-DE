---
title: Versionshinweise zum ASDK
description: Verbesserungen, Fehlerbehebungen und bekannte Probleme im Azure Stack Development Kit (ASDK).
author: sethmanheim
ms.topic: article
ms.date: 04/06/2020
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: bfd3b0821554899b478139e0fe2c8f27b016a242
ms.sourcegitcommit: 2a144c367a17eb1f8db58dd7bf1c820520182015
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85835057"
---
# <a name="asdk-release-notes"></a>Versionshinweise zum ASDK

Dieser Artikel bietet Informationen zu Änderungen, Fehlerbehebungen und bekannten Problemen im Azure Stack Development Kit (ASDK). Wenn Sie nicht sicher sind, welche Version Sie ausführen, [überprüfen Sie sie im Portal](../operator/azure-stack-updates.md).

Abonnieren Sie den ![RSS](./media/asdk-release-notes/feed-icon-14x14.png) [RSS](https://docs.microsoft.com/api/search/rss?search=ASDK+release+notes&locale=en-us#)-Feed, um in Bezug auf Neuerungen im ASDK auf dem Laufenden zu bleiben.

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

::: moniker range="azs-1908"
  
## <a name="build-11908020"></a>Build 1.1908.0.20

### <a name="new-features"></a>Neue Funktionen

- Eine Liste der neuen Funktionen in diesem Release finden Sie [in diesem Abschnitt](/azure-stack/operator/release-notes?view=azs-1908#whats-new-2) der Anmerkungen zu dieser Version für Azure Stack.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Behobene und bekannte Probleme

<!-- - For a list of Azure Stack issues fixed in this release, see [this section](/azure-stack/operator/release-notes?view=azs-1908#fixes-1) of the Azure Stack release notes. -->
- Eine Liste der bekannten Probleme finden Sie [in diesem Abschnitt](/azure-stack/operator/known-issues?view=azs-1908).
- Beachten Sie, dass verfügbare Hotfixes für Azure Stack nicht für das ASDK gelten.
::: moniker-end

::: moniker range="azs-1907"
## <a name="build-11907020"></a>Build 1.1907.0.20

### <a name="new-features"></a>Neue Funktionen

- Eine Liste der neuen Funktionen in diesem Release finden Sie [in diesem Abschnitt](/azure-stack/operator/release-notes?view=azs-1907#whats-in-this-update) der Anmerkungen zu dieser Version für Azure Stack.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Behobene und bekannte Probleme

- Wenn Sie VM-Ressourcen unter Verwendung einiger Marketplace-Images erstellen, können Sie die Bereitstellung eventuell nicht abschließen. Zur Umgehung dieses Problems können Sie auf den Link **Vorlage und Parameter herunterladen** auf der Seite **Zusammenfassung** klicken, und dann klicken Sie auf die Schaltfläche **Bereitstellen** auf dem Blatt **Vorlage**.
- Eine Liste der in dieser Version behobenen Azure Stack-Probleme finden Sie [in diesem Abschnitt](/azure-stack/operator/release-notes?view=azs-1907#fixes-3) der Azure Stack-Versionshinweise.
- Eine Liste der bekannten Probleme finden Sie [in diesem Abschnitt](/azure-stack/operator/known-issues?view=azs-1907).
- Beachten Sie, dass [verfügbare Hotfixes für Azure Stack](/azure-stack/operator/release-notes?view=azs-1907#hotfixes-3) nicht auf das Azure Stack ASDK anwendbar sind.
::: moniker-end
