---
title: Bereitstellen des lokalen Agents | Microsoft-Dokumentation
description: Stellen Sie den lokalen Agent für Validation-as-a-Service in Azure Stack bereit.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: b1a658b428d13cdd12c16b767430f87a80e89fdc
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418368"
---
# <a name="deploy-the-local-agent"></a>Bereitstellen des lokalen Agents

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Hier erfahren Sie, wie Sie mithilfe des lokalen VaaS-Agents (Validation-as-a-Service) Validierungstests ausführen. Der lokale Agent muss vor dem Ausführen von Validierungstests bereitgestellt werden.

> [!Note]  
> Stellen Sie sicher, dass die ausgehende Internetverbindung des Computers, auf dem der lokale Agent ausgeführt wird, nicht unterbrochen wird. Auf diesen Computer dürfen nur Benutzer Zugriff haben, die dazu autorisiert sind, VaaS im Auftrag Ihres Mandanten zu verwenden.

Das Bereitstellen des lokalen Agents umfasst folgende Schritte:

1. Installieren Sie den lokalen Agent.
2. Führen Sie Integritätsprüfungen durch.
3. Führen Sie den lokalen Agent aus.

## <a name="download-and-start-the-local-agent"></a>Herunterladen und Starten des lokalen Agents

Laden Sie den Agent auf einen geeigneten Computer in Ihrem Datencenter herunter, der Zugriff auf alle Azure Stack-Endpunkte hat. Dieser Computer darf nicht Teil des Azure Stack-Systems sein oder in der Azure Stack-Cloud gehostet werden.

### <a name="machine-prerequisites"></a>Voraussetzungen für Computer

Vergewissern Sie sich, dass Ihr Computer die folgenden Kriterien erfüllt:

- Zugriff auf alle Azure Stack-Endpunkte
- Installation von .NET 4.6 und PowerShell 5.0
- Mindestens 8 GB RAM
- Prozessoren mit mindestens acht Kernen
- Mindestens 200 GB Speicherplatz
- Stabile Verbindung mit dem Internet

### <a name="download-and-install-the-agent"></a>Herunterladen und Installieren des Agents

1. Öffnen Sie auf dem Computer, den Sie zum Ausführen der Tests verwenden möchten, Windows PowerShell in einer Eingabeaufforderung mit erhöhten Rechten.
2. Führen Sie den folgenden Befehl aus, um den lokalen Agent herunterzuladen:

    ```powershell
    Invoke-WebRequest -Uri "https://storage.azurestackvalidation.com/packages/Microsoft.VaaSOnPrem.TaskEngineHost.latest.nupkg" -outfile "OnPremAgent.zip"
    Expand-Archive -Path ".\OnPremAgent.zip" -DestinationPath VaaSOnPremAgent -Force
    Set-Location VaaSOnPremAgent\lib\net46
    ```

3. Führen Sie den folgenden Befehl aus, um die Abhängigkeiten des lokalen Agents zu installieren:

    ```powershell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region
    ```

    **Parameter**

    | Parameter | BESCHREIBUNG |
    | --- | --- |
    | aadServiceAdminUser | Der globale Administratorbenutzer für Ihren Azure AD-Mandanten. Beispiel: vaasadmin@contoso.onmicrosoft.com |
    | aadServiceAdminPassword | Das Kennwort für den globalen Administratorbenutzer |
    | AadTenantId | Azure AD-Mandanten-ID für das bei Validation-as-a-Service registrierte Azure-Konto |
    | ExternalFqdn | Sie finden den vollqualifizierten Domänennamen in der Konfigurationsdatei. Anweisungen hierzu finden Sie unter [Allgemeine Workflowparameter für Validation-as-a-Service in Azure Stack](azure-stack-vaas-parameters.md). |
    | Region | Die Region des Azure AD-Mandanten |

Der Befehl lädt ein PIR-Image (Betriebssystem-VHD) und eine Kopie aus einem Azure-Blobspeicher in Ihren Azure Stack-Speicher herunter.

![Herunterladen der erforderlichen Komponenten](media/installingprereqs.png)

> [!Note]
> Wenn die Geschwindigkeit beim Herunterladen dieser Images niedrig ist, laden Sie sie einzeln auf eine lokale Dateifreigabe herunter, und geben Sie den Parameter **-LocalPackagePath** *FileShareOrLocalPath* an. Weitere Informationen zum PIR-Download finden Sie unter [Problembehandlung für Validation-as-a-Service](azure-stack-vaas-troubleshoot.md) im Abschnitt [Behandeln langsamer Netzwerkverbindung](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity).

## <a name="checks-before-starting-the-tests"></a>Prüfungen vor Beginn der Tests

Im Rahmen der Tests werden Remotevorgänge ausgeführt. Der Computer, auf dem die Tests ausgeführt werden, muss Zugriff auf die Azure Stack-Endpunkte haben. Andernfalls funktionieren die Tests nicht. Verwenden Sie bei Verwendung des lokalen VaaS-Agents den Computer, auf dem der Agent ausgeführt wird. Sie können überprüfen, ob Ihr Computer Zugriff auf die Azure Stack-Endpunkte hat. Führen Sie hierzu die folgenden Schritte aus:

1. Prüfen Sie, ob der Basis-URI erreicht werden kann. Öffnen Sie eine CMD-Eingabeaufforderung oder Bash-Shell, und führen Sie den folgenden Befehl aus. Ersetzen Sie dabei `<EXTERNALFQDN>` durch den externen vollqualifizierten Domänennamen Ihrer Umgebung:

    ```bash
    nslookup adminmanagement.<EXTERNALFQDN>
    ```

2. Öffnen Sie einen Webbrowser, und navigieren Sie zu `https://adminportal.<EXTERNALFQDN>`, um zu prüfen, ob das MAS-Portal erreichbar ist.

3. Melden Sie sich mit den Werten für den Azure AD-Dienstadministratornamen und das entsprechende Kennwort an, die beim Erstellen des Testdurchlaufs bereitgestellt wurden.

4. Überprüfen Sie die Systemintegrität mithilfe des PowerShell-Cmdlets **Test-AzureStack**, wie in [Ausführen eines Validierungstests für Azure Stack](../operator/azure-stack-diagnostic-test.md) beschrieben. Beheben Sie vor dem Starten von Tests alle Warnungen und Fehler.

## <a name="run-the-agent"></a>Ausführen des Agents

1. Öffnen Sie Windows PowerShell in einer Eingabeaufforderung mit erhöhten Rechten.

2. Führen Sie den folgenden Befehl aus:

    ```powershell
    .\Microsoft.VaaSOnPrem.TaskEngineHost.exe -u <VaaSUserId> -t <VaaSTenantId>
    ```

      **Parameter**  

    | Parameter | BESCHREIBUNG |
    | --- | --- |
    | VaaSUserId | Benutzer-ID für die Anmeldung beim VaaS-Portal (Beispiel: <Benutzername>\@Contoso.com) |
    | VaaSTenantId | Azure AD-Mandanten-ID für das bei Validation-as-a-Service registrierte Azure-Konto |

    > [!Note]  
    > Beim Ausführen des Agents muss als aktuelles Arbeitsverzeichnis der Speicherort der ausführbaren Datei für den Task-Engine-Host **Microsoft.VaaSOnPrem.TaskEngineHost.exe** verwendet werden.

Werden keine Fehler angezeigt, war der lokale Agent erfolgreich. Der folgende Beispieltext wird im Konsolenfenster angezeigt:

`Heartbeat Callback at 11/8/2016 4:45:38 PM`

![Gestarteter Agent](media/startedagent.png)

Ein Agent wird anhand seines Namens eindeutig identifiziert. Standardmäßig wird der vollqualifizierte Domänenname (Fully Qualified Domain Name, FQDN) des Computers verwendet, auf dem er gestartet wurde. Sie müssen das Fenster minimieren, um versehentliche Klicks im Fenster zu vermeiden, da durch eine Fokusänderung alle anderen Aktionen angehalten werden.

## <a name="next-steps"></a>Nächste Schritte

- [Problembehandlung für Validation-as-a-Service](azure-stack-vaas-troubleshoot.md)
- [Validation-as-a-Service – wichtige Begriffe](azure-stack-vaas-key-concepts.md)
- [Schnellstart: Planen des ersten Tests mithilfe des Validation-as-a-Service-Portals](azure-stack-vaas-schedule-test-pass.md)