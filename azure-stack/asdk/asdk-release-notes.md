---
title: Microsoft Azure Stack Development Kit – Versionshinweise | Microsoft-Dokumentation
description: Verbesserungen, Fehlerbehebungen und bekannte Probleme in Azure Stack Development Kit
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
ms.date: 07/25/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 07/25/2019
ms.openlocfilehash: a2b000f60c5867e557ef5b0621f994ea7ec23913
ms.sourcegitcommit: f6ea6daddb92cbf458f9824cd2f8e7e1bda9688e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/25/2019
ms.locfileid: "68493777"
---
# <a name="asdk-release-notes"></a>Versionshinweise zum ASDK

Dieser Artikel bietet Informationen zu Änderungen, Fehlerbehebungen und bekannten Problemen im Azure Stack Development Kit (ASDK). Wenn Sie nicht sicher sind, welche Version ausgeführt wird, können Sie diese im [Portal überprüfen](../operator/azure-stack-updates.md#determine-the-current-version).

Abonnieren Sie den [![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [RSS-Feed](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#), um in Bezug auf Neuerungen im ASDK auf dem Laufenden zu bleiben.

## <a name="build-11907020"></a>Build 1.1907.0.20

### <a name="new-features"></a>Neue Funktionen

- Eine Liste der neuen Funktionen in diesem Release finden Sie [in diesem Abschnitt](../operator/azure-stack-release-notes-1907.md#whats-in-this-update) der Anmerkungen zu dieser Version für Azure Stack.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Behobene und bekannte Probleme

- Wenn Sie VM-Ressourcen unter Verwendung einiger Marketplace-Images erstellen, sind Sie eventuell nicht in der Lage, die Bereitstellung abzuschließen. Zur Umgehung dieses Problems können Sie auf den Link **Vorlage und Parameter herunterladen** auf der Seite **Zusammenfassung** klicken, und dann klicken Sie auf die Schaltfläche **Bereitstellen** auf dem Blatt **Vorlage**.
- Eine Liste der in dieser Version behobenen Azure Stack-Probleme finden Sie [in diesem Abschnitt](../operator/azure-stack-release-notes-1907.md#fixes) der Azure Stack-Versionshinweise.
- Eine Liste der bekannten Probleme finden Sie [in diesem Abschnitt](../operator/azure-stack-release-notes-known-issues-1907.md).
- Beachten Sie, dass [verfügbare Hotfixes für Azure Stack](../operator/azure-stack-release-notes-1907.md#hotfixes) nicht auf das Azure Stack ASDK anwendbar sind.

## <a name="build-11906030"></a>Build 1.1906.0.30

### <a name="new-features"></a>Neue Funktionen

- Eine Liste der neuen Funktionen in diesem Release finden Sie [in diesem Abschnitt](../operator/azure-stack-release-notes-1906.md#whats-in-this-update) der Anmerkungen zu dieser Version für Azure Stack.

### <a name="changes"></a>Änderungen

- Es wurde ein Virtueller Supportring-Computer **AzS-SRNG01** hinzugefügt, der den Protokollsammlungsdienst für Azure Stack hostet. Weitere Informationen finden Sie unter [Rollen virtueller Computer](asdk-architecture.md).

### <a name="fixed-and-known-issues"></a>Behobene und bekannte Probleme

- Wenn Sie VM-Ressourcen unter Verwendung einiger Marketplace-Images erstellen, sind Sie eventuell nicht in der Lage, die Bereitstellung abzuschließen. Zur Umgehung dieses Problems können Sie auf den Link **Vorlage und Parameter herunterladen** auf der Seite **Zusammenfassung** klicken, und dann klicken Sie auf die Schaltfläche **Bereitstellen** auf dem Blatt **Vorlage**.
- Eine Liste der in dieser Version behobenen Azure Stack-Probleme finden Sie [in diesem Abschnitt](../operator/azure-stack-release-notes-1906.md#fixes) der Azure Stack-Versionshinweise.
- Eine Liste der bekannten Probleme finden Sie [in diesem Abschnitt](../operator/azure-stack-release-notes-known-issues-1906.md).
- Beachten Sie, dass [verfügbare Hotfixes für Azure Stack](../operator/azure-stack-release-notes-1906.md#hotfixes) nicht auf das Azure Stack ASDK anwendbar sind.

## <a name="build-11905040"></a>Build 1.1905.0.40

<!-- ### Changes -->

### <a name="new-features"></a>Neue Funktionen

- Eine Liste der neuen Funktionen in diesem Release finden Sie [in diesem Abschnitt](../operator/azure-stack-release-notes-1905.md#whats-in-this-update) der Anmerkungen zu dieser Version für Azure Stack.

### <a name="fixed-and-known-issues"></a>Behobene und bekannte Probleme

- Ein Problem wurde behoben, bei dem Sie das PowerShell-Skript **RegisterWithAzure.psm1** bearbeiten mussten, damit [das ASDK erfolgreich registriert](asdk-register.md) werden konnte.
- Eine Liste anderer in dieser Version behobener Azure Stack-Probleme finden Sie [in diesem Abschnitt](../operator/azure-stack-release-notes-1905.md#fixes) der Azure Stack-Versionshinweise.
- Eine Liste der bekannten Probleme finden Sie [in diesem Abschnitt](../operator/azure-stack-release-notes-known-issues-1905.md).
- Beachten Sie, dass [verfügbare Hotfixes für Azure Stack](../operator/azure-stack-release-notes-1905.md#hotfixes) nicht auf das Azure Stack ASDK anwendbar sind.

## <a name="build-11904036"></a>Build 1.1904.0.36

<!-- ### Changes -->

### <a name="new-features"></a>Neue Funktionen

- Eine Liste der neuen Funktionen in diesem Release finden Sie [in diesem Abschnitt](../operator/azure-stack-release-notes-1904.md#whats-in-this-update) der Anmerkungen zu dieser Version für Azure Stack.

### <a name="fixed-and-known-issues"></a>Behobene und bekannte Probleme

- Aufgrund einer Zeitüberschreitung für den Dienstprinzipal beim Ausführen des Registrierungsskripts müssen Sie das PowerShell-Skript **RegisterWithAzure.psm1** bearbeiten, damit die [ASDK-Registrierung](asdk-register.md) erfolgreich ist. Gehen Sie wie folgt vor:

  1. Öffnen Sie auf dem ASDK-Hostcomputer die Datei **C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1** in einem Editor mit erhöhten Rechten.
  2. Fügen Sie in Zeile 1249 am Ende den Parameter `-TimeoutInSeconds 1800` hinzu. Dies ist erforderlich, weil beim Ausführen des Registrierungsskripts eine Zeitüberschreitung für einen Dienstprinzipal auftritt. Zeile 1249 sollte jetzt wie folgt aussehen:

     ```powershell
      $servicePrincipal = Invoke-Command -Session $PSSession -ScriptBlock { New-AzureBridgeServicePrincipal -RefreshToken $using:RefreshToken -AzureEnvironment $using:AzureEnvironmentName -TenantId $using:TenantId -TimeoutInSeconds 1800 }
      ```

- Das erkannte VPN-Verbindungsproblem in Version 1902 wurde behoben.

- Eine Liste anderer in dieser Version behobener Azure Stack-Probleme finden Sie [in diesem Abschnitt](../operator/azure-stack-release-notes-1904.md#fixes) der Azure Stack-Versionshinweise.
- Eine Liste der bekannten Probleme finden Sie [in diesem Abschnitt](../operator/azure-stack-release-notes-known-issues-1904.md).
- Beachten Sie, dass [verfügbare Hotfixes für Azure Stack](../operator/azure-stack-release-notes-1904.md#hotfixes) nicht auf das Azure Stack ASDK anwendbar sind.

