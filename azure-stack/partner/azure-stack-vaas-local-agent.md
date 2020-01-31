---
title: Bereitstellen des lokalen Agents
description: Stellen Sie den lokalen Agent für Validation-as-a-Service in Azure Stack bereit.
author: mattbriggs
ms.topic: quickstart
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a84f2495b0e2195ac143dc8fd9ac4ea4668392b4
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884090"
---
# <a name="deploy-the-local-agent"></a>Bereitstellen des lokalen Agents

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Hier erfahren Sie, wie Sie mithilfe des lokalen VaaS-Agents (Validation-as-a-Service) Validierungstests ausführen. Der lokale Agent muss vor dem Ausführen von Validierungstests bereitgestellt werden.

> [!Note]  
> Stellen Sie sicher, dass die ausgehende Internetverbindung des Computers, auf dem der lokale Agent ausgeführt wird, nicht unterbrochen wird. Auf diesen Computer dürfen nur Benutzer Zugriff haben, die dazu autorisiert sind, VaaS im Auftrag Ihres Mandanten zu verwenden.

Das Bereitstellen des lokalen Agents umfasst folgende Schritte:

1. Laden Sie den lokalen Agent herunter, und installieren Sie ihn.
2. Führen Sie vor Beginn der Tests Integritätsprüfungen durch.
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

### <a name="download-and-install-the-local-agent"></a>Herunterladen und Installieren des lokalen Agents

1. Öffnen Sie auf dem Computer, den Sie zum Ausführen der Tests verwenden möchten, Windows PowerShell in einer Eingabeaufforderung mit erhöhten Rechten.
2. Führen Sie den folgenden Befehl aus, um die Abhängigkeiten des lokalen Agents herunterzuladen und zu installieren und die PIR-Images (Betriebssystem-VHD) in Ihre Azure Stack-Umgebung zu kopieren.

    ```powershell
    # Review and update the following five parameters
    $RootFolder = "c:\VaaS"
    $CloudAdmindUserName = "<Cloud admin user name>"
    $CloudAdminPassword = "<Cloud admin password>"
    $AadServiceAdminUserName = "<AAD service admin user name>"
    $AadServiceAdminPassword = "<AAD service admin password>"

    if (-not(Test-Path($RootFolder))) {
        mkdir $RootFolder
    }
    Set-Location $RootFolder
    Invoke-WebRequest -Uri "https://storage.azurestackvalidation.com/packages/Microsoft.VaaSOnPrem.TaskEngineHost.latest.nupkg" -outfile "$rootFolder\OnPremAgent.zip"
    Expand-Archive -Path "$rootFolder\OnPremAgent.zip" -DestinationPath "$rootFolder\VaaSOnPremAgent" -Force
    Set-Location "$rootFolder\VaaSOnPremAgent\lib\net46"

    $cloudAdminCredential = New-Object System.Management.Automation.PSCredential($cloudAdmindUserName, (ConvertTo-SecureString $cloudAdminPassword -AsPlainText -Force))
    $getStampInfoUri = "https://ASAppGateway:4443/ServiceTypeId/4dde37cc-6ee0-4d75-9444-7061e156507f/CloudDefinition/GetStampInformation" 
    $stampInfo = Invoke-RestMethod -Method Get -Uri $getStampInfoUri -Credential $cloudAdminCredential -ErrorAction Stop
    $serviceAdminCreds = New-Object System.Management.Automation.PSCredential $aadServiceAdminUserName, (ConvertTo-SecureString $aadServiceAdminPassword -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $stampInfo.AADTenantID `
                            -ServiceAdminCreds $serviceAdminCreds `
                            -ArmEndpoint $stampInfo.AdminExternalEndpoints.AdminResourceManager `
                            -Region $stampInfo.RegionName
    ```

> [!Note]  
> Das Cmdlet „Install-VaaSPrerequisites“ lädt große VM-Imagedateien herunter. Ist die Geschwindigkeit niedrig, können Sie Dateien auf Ihren lokalen Dateiserver herunterladen und VM-Images manuell zu Ihrer Testumgebung hinzufügen. Weitere Informationen finden Sie unter [Behandeln langsamer Netzwerkverbindungen](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity).

**Parameter**

| Parameter | Beschreibung |
| --- | --- |
| AadServiceAdminUser | Der globale Administratorbenutzer für Ihren Azure AD-Mandanten. Beispiel: vaasadmin@contoso.onmicrosoft.com |
| AadServiceAdminPassword | Das Kennwort für den globalen Administratorbenutzer |
| CloudAdminUserName | Der Cloudadministratorbenutzer, der auf zulässige Befehle innerhalb des privilegierten Endpunkts zugreifen und sie ausführen kann. Beispiel: AzusreStack\CloudAdmin. Weitere Informationen finden Sie [hier](azure-stack-vaas-parameters.md) . |
| CloudAdminPassword | Das Kennwort für das Cloudadministratorkonto|

![Herunterladen der erforderlichen Komponenten](media/installing-prereqs.png)

## <a name="perform-sanity-checks-before-starting-the-tests"></a>Ausführen von Integritätsprüfungen vor Beginn der Tests

Im Rahmen der Tests werden Remotevorgänge ausgeführt. Der Computer, auf dem die Tests ausgeführt werden, muss Zugriff auf die Azure Stack-Endpunkte haben. Andernfalls funktionieren die Tests nicht. Verwenden Sie bei Verwendung des lokalen VaaS-Agents den Computer, auf dem der Agent ausgeführt wird. Sie können überprüfen, ob Ihr Computer Zugriff auf die Azure Stack-Endpunkte hat. Führen Sie hierzu die folgenden Schritte aus:

1. Prüfen Sie, ob der Basis-URI erreicht werden kann. Öffnen Sie eine CMD-Eingabeaufforderung oder Bash-Shell, und führen Sie den folgenden Befehl aus. Ersetzen Sie dabei `<EXTERNALFQDN>` durch den externen vollqualifizierten Domänennamen Ihrer Umgebung:

    ```bash
    nslookup adminmanagement.<EXTERNALFQDN>
    ```

2. Öffnen Sie einen Webbrowser, und navigieren Sie zu `https://adminportal.<EXTERNALFQDN>`, um zu prüfen, ob das MAS-Portal erreichbar ist.

3. Melden Sie sich mit den Werten für den Azure AD-Dienstadministratornamen und das entsprechende Kennwort an, die beim Erstellen des Testdurchlaufs bereitgestellt wurden.

4. Überprüfen Sie die Systemintegrität mithilfe des PowerShell-Cmdlets **Test-AzureStack**, wie in [Ausführen eines Validierungstests für Azure Stack](../operator/azure-stack-diagnostic-test.md) beschrieben. Beheben Sie vor dem Starten von Tests alle Warnungen und Fehler.

## <a name="run-the-local-agent"></a>Führen Sie den lokalen Agent aus.

1. Öffnen Sie Windows PowerShell in einer Eingabeaufforderung mit erhöhten Rechten.

2. Führen Sie den folgenden Befehl aus:

    ```powershell
   # Review and update the following five parameters
    $RootFolder = "c:\VAAS"
    $CloudAdmindUserName = "<Cloud admin user name>"
    $CloudAdminPassword = "<Cloud admin password>"
    $VaaSUserId = "<VaaS user ID>"
    $VaaSTenantId = "<VaaS tenant ID>"

    Set-Location "$rootFolder\VaaSOnPremAgent\lib\net46"
    .\Microsoft.VaaSOnPrem.TaskEngineHost.exe -u $VaaSUserId -t $VaaSTenantId -x $CloudAdmindUserName -y $CloudAdminPassword
    ```

      **Parameter**  

    | Parameter | Beschreibung |
    | --- | --- |
    | CloudAdminUserName | Der Cloudadministratorbenutzer, der auf zulässige Befehle innerhalb des privilegierten Endpunkts zugreifen und sie ausführen kann. Beispiel: AzusreStack\CloudAdmin. Weitere Informationen finden Sie [hier](azure-stack-vaas-parameters.md) . |
    | CloudAdminPassword | Das Kennwort für das Cloudadministratorkonto|
    | VaaSUserId | Benutzer-ID für die Anmeldung beim VaaS-Portal (Beispiel: <Benutzername>\@Contoso.com) |
    | VaaSTenantId | Azure AD-Mandanten-ID für das bei Validation-as-a-Service registrierte Azure-Konto |

    > [!Note]  
    > Beim Ausführen des Agents muss als aktuelles Arbeitsverzeichnis der Speicherort der ausführbaren Datei für den Task-Engine-Host **Microsoft.VaaSOnPrem.TaskEngineHost.exe** verwendet werden.

Werden keine Fehler angezeigt, war der lokale Agent erfolgreich. Der folgende Beispieltext wird im Konsolenfenster angezeigt:

`Heartbeat was sent successfully.`

![Gestarteter Agent](media/started-agent.png)

Ein Agent wird anhand seines Namens eindeutig identifiziert. Standardmäßig wird der vollqualifizierte Domänenname (Fully Qualified Domain Name, FQDN) des Computers verwendet, auf dem er gestartet wurde. Sie müssen das Fenster minimieren, um versehentliche Klicks im Fenster zu vermeiden, da durch eine Fokusänderung alle anderen Aktionen angehalten werden.

## <a name="next-steps"></a>Nächste Schritte

- [Problembehandlung für Validation-as-a-Service](azure-stack-vaas-troubleshoot.md)
- [Validation-as-a-Service – wichtige Begriffe](azure-stack-vaas-key-concepts.md)
- [Schnellstart: Planen des ersten Tests mithilfe des Validation-as-a-Service-Portals](azure-stack-vaas-schedule-test-pass.md)