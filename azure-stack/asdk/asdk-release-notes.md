---
title: Versionshinweise zum ASDK
description: Verbesserungen, Fehlerbehebungen und bekannte Probleme im Azure Stack Development Kit (ASDK).
author: sethmanheim
ms.topic: article
ms.date: 11/21/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: 608374ce5e788a82378aaf9c62414b05d871c983
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76873480"
---
# <a name="asdk-release-notes"></a>Versionshinweise zum ASDK

Dieser Artikel bietet Informationen zu Änderungen, Fehlerbehebungen und bekannten Problemen im Azure Stack Development Kit (ASDK). Wenn Sie nicht sicher sind, welche Version Sie ausführen, [überprüfen Sie sie im Portal](../operator/azure-stack-updates.md).

Abonnieren Sie den [![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [RSS-Feed](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#), um in Bezug auf Neuerungen im ASDK auf dem Laufenden zu bleiben.

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

- Eine Liste der neuen Funktionen in diesem Release finden Sie [in diesem Abschnitt](/azure-stack/operator/release-notes?view=azs-1908#whats-new-1) der Anmerkungen zu dieser Version für Azure Stack.

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
- Eine Liste der in dieser Version behobenen Azure Stack-Probleme finden Sie [in diesem Abschnitt](/azure-stack/operator/release-notes?view=azs-1907#fixes-2) der Azure Stack-Versionshinweise.
- Eine Liste der bekannten Probleme finden Sie [in diesem Abschnitt](/azure-stack/operator/known-issues?view=azs-1907).
- Beachten Sie, dass [verfügbare Hotfixes für Azure Stack](/azure-stack/operator/release-notes?view=azs-1907#hotfixes-2) nicht auf das Azure Stack ASDK anwendbar sind.
::: moniker-end

::: moniker range="azs-1906"
## <a name="build-11906030"></a>Build 1.1906.0.30

### <a name="new-features"></a>Neue Funktionen

- Eine Liste der neuen Funktionen in diesem Release finden Sie [in diesem Abschnitt](/azure-stack/operator/release-notes?view=azs-1906#whats-in-this-update-1) der Anmerkungen zu dieser Version für Azure Stack.

### <a name="changes"></a>Änderungen

- Es wurde ein Virtueller Supportring-Computer **AzS-SRNG01** hinzugefügt, der den Protokollsammlungsdienst für Azure Stack hostet. Weitere Informationen finden Sie unter [Rollen virtueller Computer](asdk-architecture.md).

### <a name="fixed-and-known-issues"></a>Behobene und bekannte Probleme

- Wenn Sie VM-Ressourcen unter Verwendung einiger Marketplace-Images erstellen, können Sie die Bereitstellung eventuell nicht abschließen. Zur Umgehung dieses Problems können Sie auf den Link **Vorlage und Parameter herunterladen** auf der Seite **Zusammenfassung** klicken, und dann klicken Sie auf die Schaltfläche **Bereitstellen** auf dem Blatt **Vorlage**.
- Eine Liste der in dieser Version behobenen Azure Stack-Probleme finden Sie [in diesem Abschnitt](/azure-stack/operator/release-notes?view=azs-1906#fixes-3) der Azure Stack-Versionshinweise.
- Eine Liste der bekannten Probleme finden Sie [in diesem Abschnitt](/azure-stack/operator/known-issues?view=azs-1906).
- Beachten Sie, dass [verfügbare Hotfixes für Azure Stack](/azure-stack/operator/release-notes?view=azs-1906#hotfixes-3) nicht auf das Azure Stack ASDK anwendbar sind.
::: moniker-end
