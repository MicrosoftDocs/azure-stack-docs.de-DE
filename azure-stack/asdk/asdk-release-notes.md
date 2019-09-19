---
title: ASDK-Versionshinweise | Microsoft-Dokumentation
description: Verbesserungen, Fehlerbehebungen und bekannte Probleme im Azure Stack Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 08/30/2019
ms.openlocfilehash: 98e43cfe0226e06ca936484a78da5a61915f5797
ms.sourcegitcommit: c46d913ebfa4cb6c775c5117ac5c9e87d032a271
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71101029"
---
# <a name="asdk-release-notes"></a>Versionshinweise zum ASDK

Dieser Artikel bietet Informationen zu Änderungen, Fehlerbehebungen und bekannten Problemen im Azure Stack Development Kit (ASDK). Wenn Sie nicht sicher sind, welche Version Sie ausführen, [überprüfen Sie sie im Portal](../operator/azure-stack-updates.md).

Abonnieren Sie den [![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [RSS-Feed](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#), um in Bezug auf Neuerungen im ASDK auf dem Laufenden zu bleiben.

::: moniker range="azs-1908"
## <a name="build-11908020"></a>Build 1.1908.0.20

### <a name="new-features"></a>Neue Funktionen

- Eine Liste der neuen Funktionen in diesem Release finden Sie [in diesem Abschnitt](/azure-stack/operator/release-notes?view=azs-1908#whats-new-1908) der Anmerkungen zu dieser Version für Azure Stack.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Behobene und bekannte Probleme

<!-- - For a list of Azure Stack issues fixed in this release, see [this section](/azure-stack/operator/release-notes?view=azs-1908#fixes-1908) of the Azure Stack release notes. -->
- Eine Liste der bekannten Probleme finden Sie [in diesem Abschnitt](/azure-stack/operator/known-issues?view=azs-1908).
- Beachten Sie, dass verfügbare Hotfixes für Azure Stack nicht für das ASDK gelten.
::: moniker-end

::: moniker range="azs-1907"
## <a name="build-11907020"></a>Build 1.1907.0.20

### <a name="new-features"></a>Neue Funktionen

- Eine Liste der neuen Funktionen in diesem Release finden Sie [in diesem Abschnitt](/azure-stack/operator/release-notes?view=azs-1907#whats-in-this-update-1907) der Anmerkungen zu dieser Version für Azure Stack.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Behobene und bekannte Probleme

- Wenn Sie VM-Ressourcen unter Verwendung einiger Marketplace-Images erstellen, können Sie die Bereitstellung eventuell nicht abschließen. Zur Umgehung dieses Problems können Sie auf den Link **Vorlage und Parameter herunterladen** auf der Seite **Zusammenfassung** klicken, und dann klicken Sie auf die Schaltfläche **Bereitstellen** auf dem Blatt **Vorlage**.
- Eine Liste der in dieser Version behobenen Azure Stack-Probleme finden Sie [in diesem Abschnitt](/azure-stack/operator/release-notes?view=azs-1907#fixes-1907) der Azure Stack-Versionshinweise.
- Eine Liste der bekannten Probleme finden Sie [in diesem Abschnitt](/azure-stack/operator/known-issues?view=azs-1907).
- Beachten Sie, dass [verfügbare Hotfixes für Azure Stack](/azure-stack/operator/release-notes?view=azs-1907#hotfixes-1907) nicht auf das Azure Stack ASDK anwendbar sind.
::: moniker-end

::: moniker range="azs-1906"
## <a name="build-11906030"></a>Build 1.1906.0.30

### <a name="new-features"></a>Neue Funktionen

- Eine Liste der neuen Funktionen in diesem Release finden Sie [in diesem Abschnitt](/azure-stack/operator/release-notes?view=azs-1906#whats-in-this-update-1906) der Anmerkungen zu dieser Version für Azure Stack.

### <a name="changes"></a>Änderungen

- Es wurde ein Virtueller Supportring-Computer **AzS-SRNG01** hinzugefügt, der den Protokollsammlungsdienst für Azure Stack hostet. Weitere Informationen finden Sie unter [Rollen virtueller Computer](asdk-architecture.md).

### <a name="fixed-and-known-issues"></a>Behobene und bekannte Probleme

- Wenn Sie VM-Ressourcen unter Verwendung einiger Marketplace-Images erstellen, können Sie die Bereitstellung eventuell nicht abschließen. Zur Umgehung dieses Problems können Sie auf den Link **Vorlage und Parameter herunterladen** auf der Seite **Zusammenfassung** klicken, und dann klicken Sie auf die Schaltfläche **Bereitstellen** auf dem Blatt **Vorlage**.
- Eine Liste der in dieser Version behobenen Azure Stack-Probleme finden Sie [in diesem Abschnitt](/azure-stack/operator/release-notes?view=azs-1906#fixes-1906) der Azure Stack-Versionshinweise.
- Eine Liste der bekannten Probleme finden Sie [in diesem Abschnitt](/azure-stack/operator/known-issues?view=azs-1906).
- Beachten Sie, dass [verfügbare Hotfixes für Azure Stack](/azure-stack/operator/release-notes?view=azs-1906#hotfixes-1906) nicht auf das Azure Stack ASDK anwendbar sind.
::: moniker-end

::: moniker range="azs-1905"
## <a name="build-11905040"></a>Build 1.1905.0.40

<!-- ### Changes -->

### <a name="new-features"></a>Neue Funktionen

- Eine Liste der neuen Funktionen in diesem Release finden Sie [in diesem Abschnitt](/azure-stack/operator/release-notes?view=azs-1905#whats-in-this-update-1905) der Anmerkungen zu dieser Version für Azure Stack.

### <a name="fixed-and-known-issues"></a>Behobene und bekannte Probleme

- Ein Problem wurde behoben, bei dem Sie das PowerShell-Skript **RegisterWithAzure.psm1** bearbeiten mussten, damit [das ASDK erfolgreich registriert](asdk-register.md) werden konnte.
- Eine Liste anderer in dieser Version behobener Azure Stack-Probleme finden Sie [in diesem Abschnitt](/azure-stack/operator/release-notes?view=azs-1905#fixes-1905) der Azure Stack-Versionshinweise.
- Eine Liste der bekannten Probleme finden Sie [in diesem Abschnitt](/azure-stack/operator/known-issues?view=azs-1905).
- Beachten Sie, dass [verfügbare Hotfixes für Azure Stack](/azure-stack/operator/release-notes?view=azs-1905#hotfixes-1905) nicht auf das Azure Stack ASDK anwendbar sind.
::: moniker-end
