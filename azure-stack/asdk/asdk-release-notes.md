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
ms.date: 06/04/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 06/04/2019
ms.openlocfilehash: 2ca85da5d9fde42fb06eef149e7304ab08bc32ee
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691201"
---
# <a name="asdk-release-notes"></a>Versionshinweise zum ASDK

Dieser Artikel bietet Informationen zu Änderungen, Fehlerbehebungen und bekannten Problemen im Azure Stack Development Kit (ASDK). Wenn Sie nicht sicher sind, welche Version ausgeführt wird, können Sie diese im [Portal überprüfen](../operator/azure-stack-updates.md#determine-the-current-version).

Abonnieren Sie den [![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [RSS-Feed](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#), um in Bezug auf Neuerungen im ASDK auf dem Laufenden zu bleiben.

## <a name="build-11905040"></a>Build 1.1905.0.40

<!-- ### Changes -->

### <a name="new-features"></a>Neue Funktionen

- Eine Liste der neuen Funktionen in diesem Release finden Sie [in diesem Abschnitt](../operator/azure-stack-release-notes-1905.md#whats-in-this-update) der Anmerkungen zu dieser Version für Azure Stack.

### <a name="fixed-and-known-issues"></a>Behobene und bekannte Probleme

- Aufgrund einer Zeitüberschreitung für den Dienstprinzipal beim Ausführen des Registrierungsskripts müssen Sie das PowerShell-Skript **RegisterWithAzure.psm1** bearbeiten, damit die [ASDK-Registrierung](asdk-register.md) erfolgreich ist. Gehen Sie wie folgt vor:

  1. Öffnen Sie auf dem ASDK-Hostcomputer die Datei **C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1** in einem Editor mit erhöhten Rechten.
  2. Fügen Sie in Zeile 1249 am Ende den Parameter `-TimeoutInSeconds 1800` hinzu. Dies ist erforderlich, weil beim Ausführen des Registrierungsskripts eine Zeitüberschreitung für einen Dienstprinzipal auftritt. Zeile 1249 sollte jetzt wie folgt aussehen:

     ```powershell
      $servicePrincipal = Invoke-Command -Session $PSSession -ScriptBlock { New-AzureBridgeServicePrincipal -RefreshToken $using:RefreshToken -AzureEnvironment $using:AzureEnvironmentName -TenantId $using:TenantId -TimeoutInSeconds 1800 }
      ```

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

- Das [hier](#known-issues) erkannte VPN-Verbindungsproblem in Version 1902 wurde behoben.

- Eine Liste anderer in dieser Version behobener Azure Stack-Probleme finden Sie [in diesem Abschnitt](../operator/azure-stack-release-notes-1904.md#fixes) der Azure Stack-Versionshinweise.
- Eine Liste der bekannten Probleme finden Sie [in diesem Abschnitt](../operator/azure-stack-release-notes-known-issues-1904.md).
- Beachten Sie, dass [verfügbare Hotfixes für Azure Stack](../operator/azure-stack-release-notes-1904.md#hotfixes) nicht auf das Azure Stack ASDK anwendbar sind.

## <a name="build-1903"></a>Build 1903

Die Nutzlast 1903 umfasst kein ASDK-Release.

## <a name="build-11902069"></a>Build 1.1902.0.69

### <a name="new-features"></a>Neue Funktionen

- In Build 1902 wird eine neue Benutzeroberfläche im Azure Stack-Administratorportal zum Erstellen von Plänen, Angeboten, Kontingenten und Add-On-Plänen eingeführt. Weitere Informationen (einschließlich Screenshots) finden Sie unter [Erstellen von Plänen, Angeboten und Kontingenten](../operator/azure-stack-create-plan.md).

- Eine Liste der weiteren Änderungen und Verbesserungen in diesem Release finden Sie [in diesem Abschnitt](../operator/azure-stack-update-1902.md#improvements) der Versionshinweise zu Azure Stack.

<!-- ### New features

- For a list of new features in this release, see [this section](../operator/azure-stack-update-1902.md#new-features) of the Azure Stack release notes.

### Fixed and known issues

- For a list of issues fixed in this release, see [this section](../operator/azure-stack-update-1902.md#fixed-issues) of the Azure Stack release notes. For a list of known issues, see [this section](../operator/azure-stack-update-1902.md#known-issues-post-installation).
- Note that [available Azure Stack hotfixes](../operator/azure-stack-update-1902.md#azure-stack-hotfixes) are not applicable to the Azure Stack ASDK. -->

### <a name="known-issues"></a>Bekannte Probleme

- Beim Einrichten einer VPN-Verbindung von einem anderen Host in das ASDK mit den Schritten in [diesem Artikel](asdk-connect.md) tritt ein Problem auf. Wenn Sie versuchen, von Ihrem VPN-Client aus eine Verbindung mit der ASDK-Umgebung herzustellen, wird ein Fehler angezeigt, der besagt, dass **der Benutzername oder das Kennwort falsch ist**, selbst wenn Sie sicher sind, dass Sie das richtige Konto verwendet und das Kennwort korrekt eingegeben haben. Das Problem liegt nicht bei Ihren Anmeldeinformationen, sondern bei einer Änderung des Authentifizierungsprotokolls für die VPN-Verbindung auf der ASDK. Führen Sie die folgenden Schritte aus, um das Problem zu umgehen:

   Ändern Sie zunächst das auf der Serverseite des ASDK verwendete Authentifizierungsprotokoll:

   1. RDP zum ASDK-Host.
   2. Öffnen Sie eine erweiterte PowerShell-Sitzung, und melden Sie sich als „AzureStack\AzureStackAdmin“ mit dem Kennwort an, das Sie zum Zeitpunkt der Bereitstellung angegeben haben.
   3. Führen Sie die folgenden Befehle aus:

      ```powershell
      netsh nps set np name = "Connections to Microsoft Routing and Remote Access server" profileid = "0x100a" profiledata = "1A000000000000000000000000000000" profileid = "0x1009" profiledata = "0x5"
      restart-service remoteaccess -force
      ```

   Als nächstes ändern Sie das clientseitige Verbindungsskript. Die einfachste Möglichkeit hierzu besteht darin, Änderungen direkt am Skriptmodul „C:\AzureStack-Tools-master\connect\azurestack.connect.psm1“ vorzunehmen:

   1. Ändern Sie das Cmdlet **Add-AzsVpnConnection**, um den Parameter `AuthenticationMethod` von `MsChapv2` in `EAP` zu ändern:

      ```powershell
      $connection = Add-VpnConnection -Name $ConnectionName -ServerAddress $ServerAddress -TunnelType L2tp -EncryptionLevel Required -AuthenticationMethod Eap -L2tpPsk $PlainPassword -Force -RememberCredential -PassThru -SplitTunneling
      ```

   2. Ändern Sie das Cmdlet **Connect-AzsVpn** von der Verwendung von `rasdial @ConnectionName $User $PlainPassword` auf `rasphone`, da EAP eine interaktive Anmeldung erfordert:

      ```powershell
      rasphone $ConnectionName
      ```

   3. Speichern Sie Ihre Änderungen, und importieren Sie dann das Modul **azurestack.connect.psm1** erneut.
   4. Befolgen Sie die Anweisungen in [diesem Artikel](asdk-connect.md#set-up-vpn-connectivity).
   5. Wenn Sie die Verbindung mit dem ASDK über VPN herstellen, navigieren Sie in Windows zu **Netzwerk- und Interneteinstellungen** und dann zu **VPN**, anstatt sich über die Taskleiste zu verbinden, um sicherzustellen, dass Sie zur Eingabe von Anmeldeinformationen aufgefordert werden.

- Es wurde ein Problem festgestellt, bei dem an einen internen Lastenausgleich (Internal Load Balancer, ILB) gesendete Pakete mit einer Größe von mehr als 1.450 Bytes gelöscht werden. Dieses Problem ist darauf zurückzuführen, dass die MTU-Einstellung auf dem Host zu niedrig ist, um VXLAN-gekapselte Pakete zu verarbeiten, die die seit Update 1901 auf den Host verschobene Rolle durchlaufen. Das Problem wurde in den folgenden zwei Szenarien beobachtet, kann jedoch auch in weiteren Szenarien auftreten:

  - SQL-Abfragen an SQL AlwaysOn, die hinter einem internen Lastenausgleich ausgeführt werden und größer als 660 Bytes sind.
  - Bei Kubernetes-Bereitstellungen tritt ein Fehler auf, wenn Sie versuchen, mehrere Master zu aktivieren.  

  Das Problem tritt auf, wenn eine VM und ein interner Lastenausgleich, die sich im gleichen virtuellen Netzwerk, aber in unterschiedlichen Subnetzen befinden, miteinander kommunizieren. Sie können das Problem umgehen, indem Sie auf dem ASDK-Host die folgenden Befehle an einer Eingabeaufforderung mit erhöhten Rechten ausführen:

  ```shell
  netsh interface ipv4 set sub "hostnic" mtu=1660
  netsh interface ipv4 set sub "management" mtu=1660
  ```
