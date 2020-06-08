---
title: Verwenden des privilegierten Endpunkts in Azure Stack Hub
description: Erfahren Sie, wie Sie den privilegierten Endpunkt (PEP) in Azure Stack Hub als Operator verwenden.
author: mattbriggs
ms.topic: article
ms.date: 04/28/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/28/2020
ms.openlocfilehash: ff1a4f255f81c10c29c87320bdb71fcbaf9b234b
ms.sourcegitcommit: 804f94f288859027b8249d138b14e8bc1501e009
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2020
ms.locfileid: "84158349"
---
# <a name="use-the-privileged-endpoint-in-azure-stack-hub"></a>Verwenden des privilegierten Endpunkts in Azure Stack Hub

Als Azure Stack Hub-Operator verwenden Sie das Administratorportal, PowerShell oder Azure Resource Manager-APIs für die meisten alltäglichen Verwaltungsaufgaben. Für einige weniger häufig ausgeführten Vorgänge müssen Sie jedoch den *privilegierten Endpunkt* (PEP) verwenden. Bei dem PEP handelt es sich um eine vorkonfigurierte PowerShell-Remotekonsole, die Ihnen gerade so viele Funktionen zur Verfügung stellt, wie Sie zur Ausführung einer erforderlichen Aufgabe benötigen. Der Endpunkt nutzt [PowerShell JEA (Just Enough Administration)](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview), um nur einen eingeschränkten Satz von Cmdlets verfügbar zu machen. Es wird ein Konto mit geringen Berechtigungen verwendet, um auf den PEP zuzugreifen und den eingeschränkten Satz von Cmdlets aufzurufen. Es sind keine Administratorkonten erforderlich. Die Skripterstellung ist nicht zulässig, um die Sicherheit zu erhöhen.

Sie können den PEP nutzen, um diese Aufgaben durchzuführen:

- Aufgaben auf niedriger Ebene, z. B. [Diagnoseprotokolle erfassen](azure-stack-get-azurestacklog.md).
- Viele Aufgaben zur Integration von Rechenzentren für integrierte Systeme nach der Bereitstellung, z. B. Hinzufügen von DNS-Weiterleitungen nach der Bereitstellung, Einrichten der Microsoft Graph-Integration, der Integration von Active Directory-Verbunddiensten (AD FS), der Zertifikatrotation usw.
- Zusammenarbeit mit dem Support zum Erhalt von temporärem, allgemeinem Zugriff für die eingehende Problembehandlung eines integrierten Systems.

Der PEP protokolliert jede Aktion, die Sie in der PowerShell-Sitzung ausführen (sowie die entsprechende Ausgabe). Dies bietet vollständige Transparenz und die komplette Überwachung von Vorgängen. Sie können diese Protokolldateien für zukünftige Überwachungen aufbewahren.

> [!NOTE]
> Im Azure Stack Development Kit (ASDK) können Sie einige der im PEP verfügbaren Befehle direkt über eine PowerShell-Sitzung auf dem Development Kit-Host ausführen. Es empfiehlt sich allerdings, einige Vorgänge (beispielsweise die Protokollsammlung) unter Verwendung des PEPs zu testen, da dies die einzige verfügbare Methode ist, mit der bestimmte Vorgänge in einer Umgebung mit integrierten Systemen ausgeführt werden können.

## <a name="access-the-privileged-endpoint"></a>Zugreifen auf den privilegierten Endpunkt

Der Zugriff auf den PEP erfolgt über eine PowerShell-Remotesitzung auf dem virtuellen Computer (VM), auf dem der PEP gehostet wird. Im ASDK hat diese VM den Namen **AzS-ERCS01**. Wenn Sie ein integriertes System verwenden, gibt es drei Instanzen des PEPs. Aus Resilienzgründen werden diese jeweils auf einer VM („*Präfix*-ERCS01“, „*Präfix*-ERCS02“ und „*Präfix*-ERCS03“) auf unterschiedlichen Hosts ausgeführt.

Bevor Sie dieses Verfahren für das integrierte System beginnen, sollten Sie sich vergewissern, dass Sie anhand der IP-Adresse oder per DNS auf den PEP zugreifen können. Nach der erstmaligen Bereitstellung von Azure Stack Hub können Sie nur über die IP-Adresse auf den PEP zugreifen, da die DNS-Integration noch nicht eingerichtet wurde. Ihr OEM-Hardwarehersteller stellt eine JSON-Datei namens **AzureStackStampDeploymentInfo** bereit, die die IP-Adressen des PEPs enthält.

Sie finden die IP-Adresse auch im Azure Stack Hub-Administratorportal. Öffnen Sie das Portal, z. B. `https://adminportal.local.azurestack.external`. Wählen Sie **Regionsverwaltung** > **Eigenschaften** aus.

Sie müssen die aktuelle Kultureinstellung auf `en-US` festlegen, wenn Sie den privilegierten Endpunkt ausführen. Andernfalls werden Cmdlets wie „Test-AzureStack“ oder „Get-AzureStackLog“ nicht wie erwartet ausgeführt.

> [!NOTE]
> Aus Sicherheitsgründen darf mit dem PEP nur über eine gehärtete VM, die auf dem Hardwarelebenszyklus-Host basiert, oder über einen dedizierten, sicheren Computer (beispielsweise eine [Arbeitsstation mit privilegiertem Zugriff](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)) eine Verbindung hergestellt werden. Die ursprüngliche Konfiguration des Hardwarelebenszyklus-Hosts darf nicht geändert werden (einschließlich der Installation neuer Software), und er darf nicht zum Herstellen einer Verbindung mit dem PEP verwendet werden.

1. Richten Sie die Vertrauensstellung ein.

      - Führen Sie auf einem integrierten System in einer Windows PowerShell-Sitzung mit erhöhten Rechten den folgenden Befehl aus, um den PEP als vertrauenswürdigen Host auf der gehärteten virtuellen VM hinzuzufügen, die auf dem Hardwarelebenszyklus-Host oder der Arbeitsstation mit privilegiertem Zugriff ausgeführt wird.

      ```powershell  
    Set-Item WSMan:\localhost\Client\TrustedHosts -Value '<IP Address of Privileged Endpoint>' -Concatenate
      ```

      - Wenn Sie das ASDK ausführen, melden Sie sich beim Development Kit-Host an.

2. Öffnen Sie auf der gehärteten virtuellen VM, die auf dem Hardwarelebenszyklus-Host oder der Arbeitsstation mit privilegiertem Zugriff ausgeführt wird, eine Windows PowerShell-Sitzung. Führen Sie die folgenden Befehle aus, um eine Remotesitzung auf der VM einzurichten, die den PEP hostet:
 
  - In einem integrierten System:

    ```powershell  
    $cred = Get-Credential

    $pep = New-PSSession -ComputerName <IP_address_of_ERCS> -ConfigurationName PrivilegedEndpoint -Credential $cred -SessionOption (New-PSSessionOption -Culture en-US -UICulture en-US)
    Enter-PSSession $pep
    ```
    
    Der Parameter `ComputerName` kann die IP-Adresse oder der DNS-Name einer VM sein, auf der der PEP gehostet wird.

    > [!NOTE]  
    >Azure Stack Hub führt beim Überprüfen der PEP-Anmeldeinformationen keinen Remoteaufruf durch. Für die Überprüfung wird ein lokal gespeicherter öffentlicher RSA-Schlüssel verwendet.

   - Bei Ausführung des ASDK:

     ```powershell  
      $cred = Get-Credential
    
      $pep = New-PSSession -ComputerName azs-ercs01 -ConfigurationName PrivilegedEndpoint -Credential $cred -SessionOption (New-PSSessionOption -Culture en-US -UICulture en-US)
      Enter-PSSession $pep
     ```
    
   - Wenn Sie zur Eingabe von Anmeldeinformationen aufgefordert werden, geben Sie Folgendes ein:
   
       - **Benutzername**: Geben Sie das Cloudadministratorkonto im Format **&lt;*Azure Stack Hub-Domäne*&gt;\cloudadmin** an. (Für ASDK lautet der Benutzername **Azurestack\cloudadmin**.)
  
        - **Kennwort**: Geben Sie das gleiche Kennwort ein, das während der Installation für das AzureStackAdmin-Domänenadministratorkonto bereitgestellt wurde.

      > [!NOTE]
      > Wenn Sie keine Verbindung mit dem ERCS-Endpunkt herstellen können, wiederholen Sie die Schritte 1 und 2 mit einer anderen ERCS-VM-IP-Adresse.

3. Nachdem Sie eine Verbindung hergestellt haben, ändert sich die Eingabeaufforderung in **[*IP-Adresse oder ERCS-VM-Name*]: PS>** oder zu **[azs-ercs01]: PS>** (abhängig von der Umgebung). Führen Sie `Get-Command` aus, um die Liste der verfügbaren Cmdlets anzuzeigen.

    Eine Referenz zu Cmdlets finden Sie unter [Referenz zu privilegierten Azure Stack Hub-Endpunkten](../reference/pep-2002/index.md).

   Viele dieser Cmdlets sind nur für integrierte Systemumgebungen vorgesehen (z.B. die Cmdlets im Zusammenhang mit der Datacenter-Integration). In der ASDK wurden die folgenden Cmdlets überprüft:

   - Clear-Host
   - Close-PrivilegedEndpoint
   - Exit-PSSession
   - Get-AzureStackLog
   - Get-AzureStackStampInformation
   - Get-Command
   - Get-FormatData
   - Get-Help
   - Get-ThirdPartyNotices
   - Measure-Object
   - New-CloudAdminUser
   - Out-Default
   - Remove-CloudAdminUser
   - Select-Object
   - Set-CloudAdminUserPassword
   - Test-AzureStack
   - Stop-AzureStack
   - Get-ClusterLog

## <a name="how-to-use-the-privileged-endpoint"></a>Verwenden des privilegierten Endpunkts 

Beim PEP handelt es sich wie bereits erwähnt um einen Endpunkt vom Typ [PowerShell JEA](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview). Ein JEA-Endpunkt bietet nicht nur eine Ebene mit hoher Sicherheit, sondern schränkt auch einige der grundlegenden PowerShell-Funktionen wie Skriptverwendung und Vervollständigung mittels TAB-TASTE ein. Wenn Sie einen Skriptvorgangstyp testen, schlägt der Vorgang mit Fehler **ScriptsNotAllowed** fehl. Dieser Fehler ist das erwartete Verhalten.

Führen Sie den folgenden Befehl aus, wenn Sie beispielsweise die Parameterliste für ein bestimmtes Cmdlet erhalten möchten:

```powershell
    Get-Command <cmdlet_name> -Syntax
```

Alternativ können Sie das Cmdlet [**Import-PSSession**](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Import-PSSession?view=powershell-5.1) verwenden, um alle PEP-Cmdlets in die aktuelle Sitzung auf Ihrem lokalen Computer zu importieren. Die Cmdlets und Funktionen des privilegierten Endpunkts sind dann auf Ihrem lokalen Computer verfügbar – zusammen mit der Vervollständigung mittels TAB-TASTE und der Skripterstellung im Allgemeinen. Sie können auch das Modul **[Get-Help](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/get-help)** ausführen, um Anweisungen zu den Cmdlets anzuzeigen.

Wenn Sie die PEP-Sitzung in Ihren lokalen Computer importieren möchten, führen Sie die folgenden Schritte aus:

1. Richten Sie die Vertrauensstellung ein.

    - Führen Sie auf einem integrierten System in einer Windows PowerShell-Sitzung mit erhöhten Rechten den folgenden Befehl aus, um den PEP als vertrauenswürdigen Host auf der gehärteten virtuellen VM hinzuzufügen, die auf dem Hardwarelebenszyklus-Host oder der Arbeitsstation mit privilegiertem Zugriff ausgeführt wird.

    ```powershell
    winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
    ```

    - Wenn Sie das ASDK ausführen, melden Sie sich beim Development Kit-Host an.

2. Öffnen Sie auf der gehärteten virtuellen VM, die auf dem Hardwarelebenszyklus-Host oder der Arbeitsstation mit privilegiertem Zugriff ausgeführt wird, eine Windows PowerShell-Sitzung. Führen Sie die folgenden Befehle aus, um eine Remotesitzung auf dem virtuellen Computer einzurichten, der den PEP hostet:

    - In einem integrierten System:
    
      ```powershell  
        $cred = Get-Credential
      
        $session = New-PSSession -ComputerName <IP_address_of_ERCS> `
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```
    
      Der Parameter `ComputerName` kann die IP-Adresse oder der DNS-Name einer VM sein, auf der der PEP gehostet wird.

    - Bei Ausführung des ASDK:
     
        ```powershell  
          $cred = Get-Credential
    
          $session = New-PSSession -ComputerName azs-ercs01 `
             -ConfigurationName PrivilegedEndpoint -Credential $cred
        ```

     Wenn Sie zur Eingabe von Anmeldeinformationen aufgefordert werden, geben Sie Folgendes ein:

     - **Benutzername**: Geben Sie das Cloudadministratorkonto im Format **&lt;*Azure Stack Hub-Domäne*&gt;\cloudadmin** an. (Für ASDK lautet der Benutzername **Azurestack\cloudadmin**.)
     - **Kennwort**: Geben Sie das gleiche Kennwort ein, das während der Installation für das AzureStackAdmin-Domänenadministratorkonto bereitgestellt wurde.

3. Importieren Sie die PEP-Sitzung in Ihren lokalen Computer:

    ```powershell 
      Import-PSSession $session
    ```

4. Nun können Sie in Ihrer lokalen PowerShell-Sitzung wie gewohnt die Ergänzung mittels TAB-TASTE sowie Skripts verwenden – zusammen mit allen Funktionen und Cmdlets des privilegierten Endpunkts und ohne Beeinträchtigung der Sicherheit von Azure Stack Hub. Viel Spaß!

## <a name="close-the-privileged-endpoint-session"></a>Schließen der Sitzung mit dem privilegierten Endpunkt

 Der PEP protokolliert wie bereits erwähnt jede Aktion, die Sie in der PowerShell-Sitzung durchführen, sowie die entsprechende Ausgabe. Schließen Sie die Sitzung mithilfe des Cmdlets `Close-PrivilegedEndpoint`. Dieses Cmdlet schließt den Endpunkt ordnungsgemäß, und überträgt die Protokolldateien an eine externe Dateifreigabe für die Aufbewahrung.

Schließen der Endpunktsitzung:

1. Erstellen Sie eine externe Dateifreigabe, auf die der PEP zugreifen kann. In einer Development Kit-Umgebung können Sie nur eine Dateifreigabe auf dem Development Kit-Host erstellen.
2. Führen Sie das folgende Cmdlet aus:

  ```powershell  
     Close-PrivilegedEndpoint -TranscriptsPathDestination "\\fileshareIP\SharedFolder" -Credential Get-Credential
  ```

   Das Cmdlet verwendet die Parameter in der folgenden Tabelle:

   | Parameter | BESCHREIBUNG | type | Erforderlich |
   |---------|---------|---------|---------|
   | *TranscriptsPathDestination* | Pfad zur externen Dateifreigabe, die als „fileshareIP\sharefoldername“ definiert ist. | String | Ja|
   | *Credential* | Anmeldeinformationen für den Zugriff auf die Dateifreigabe | SecureString |   Ja |


Nachdem die Aufzeichnungsprotokolldateien erfolgreich in die Dateifreigabe übertragen wurden, werden sie automatisch vom PEP gelöscht. 

> [!NOTE]
> Wenn Sie mithilfe der Cmdlets `Exit-PSSession` oder `Exit` die PEP-Sitzung (oder einfach die PowerShell-Konsole) schließen, werden die Aufzeichnungsprotokolle nicht in eine Dateifreigabe übertragen. Sie verbleiben auf dem PEP. Wenn Sie das nächste Mal `Close-PrivilegedEndpoint` ausführen und eine Dateifreigabe einschließen, werden die Aufzeichnungsprotokolle aus vorherigen Sitzung(en) auch übertragen. Schließen Sie die PEP-Sitzung nicht mit `Exit-PSSession` oder `Exit`, sondern mit `Close-PrivilegedEndpoint`.


## <a name="next-steps"></a>Nächste Schritte

- [Azure Stack Hub-Diagnosetools](azure-stack-diagnostic-log-collection-overview-tzl.md)
- [Referenz zu privilegierten Azure Stack Hub-Endpunkten](../reference/pep-2002/index.md)
