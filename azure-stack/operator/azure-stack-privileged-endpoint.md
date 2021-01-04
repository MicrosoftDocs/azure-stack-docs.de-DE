---
title: Verwenden des privilegierten Endpunkts in Azure Stack Hub
description: Erfahren Sie, wie Sie den privilegierten Endpunkt (PEP) in Azure Stack Hub als Operator verwenden.
author: mattbriggs
ms.topic: article
ms.date: 04/28/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/28/2020
ms.custom: conteperfq4
ms.openlocfilehash: a20e7bf62cf65d1fd7c5ffb0f4f4ddc7313c55b2
ms.sourcegitcommit: 5fbc60b65d27c916ded7a95ba4102328d550c7e5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97598248"
---
# <a name="use-the-privileged-endpoint-in-azure-stack-hub"></a>Verwenden des privilegierten Endpunkts in Azure Stack Hub

Als Azure Stack Hub-Operator verwenden Sie das Administratorportal, PowerShell oder Azure Resource Manager-APIs für die meisten alltäglichen Verwaltungsaufgaben. Für einige weniger häufig ausgeführten Vorgänge müssen Sie jedoch den *privilegierten Endpunkt* (PEP) verwenden. Bei dem PEP handelt es sich um eine vorkonfigurierte PowerShell-Remotekonsole, die Ihnen gerade so viele Funktionen zur Verfügung stellt, wie Sie zur Ausführung einer erforderlichen Aufgabe benötigen. Der Endpunkt nutzt [PowerShell JEA (Just Enough Administration)](/powershell/scripting/learn/remoting/jea/overview), um nur einen eingeschränkten Satz von Cmdlets verfügbar zu machen. Es wird ein Konto mit geringen Berechtigungen verwendet, um auf den PEP zuzugreifen und den eingeschränkten Satz von Cmdlets aufzurufen. Es sind keine Administratorkonten erforderlich. Die Skripterstellung ist nicht zulässig, um die Sicherheit zu erhöhen.

Sie können den PEP nutzen, um diese Aufgaben durchzuführen:

- Aufgaben auf niedriger Ebene, z. B. [Diagnoseprotokolle erfassen](azure-stack-get-azurestacklog.md).
- Viele Aufgaben zur Integration von Rechenzentren für integrierte Systeme nach der Bereitstellung, z. B. Hinzufügen von DNS-Weiterleitungen nach der Bereitstellung, Einrichten der Microsoft Graph-Integration, der Integration von Active Directory-Verbunddiensten (AD FS), der Zertifikatrotation usw.
- Zusammenarbeit mit dem Support zum Erhalt von temporärem, allgemeinem Zugriff für die eingehende Problembehandlung eines integrierten Systems.

Der PEP protokolliert jede Aktion, die Sie in der PowerShell-Sitzung ausführen (sowie die entsprechende Ausgabe). Dies bietet vollständige Transparenz und die komplette Überwachung von Vorgängen. Sie können diese Protokolldateien für zukünftige Überwachungen aufbewahren.

> [!NOTE]
> Im Azure Stack Development Kit (ASDK) können Sie einige der im PEP verfügbaren Befehle direkt über eine PowerShell-Sitzung auf dem Development Kit-Host ausführen. Es empfiehlt sich allerdings, einige Vorgänge (beispielsweise die Protokollsammlung) unter Verwendung des PEPs zu testen, da dies die einzige verfügbare Methode ist, mit der bestimmte Vorgänge in einer Umgebung mit integrierten Systemen ausgeführt werden können.

[!INCLUDE [Azure Stack Hub Operator Access Workstation](../includes/operator-note-owa.md)]

## <a name="access-the-privileged-endpoint"></a>Zugreifen auf den privilegierten Endpunkt

Der Zugriff auf den PEP erfolgt über eine PowerShell-Remotesitzung auf dem virtuellen Computer (VM), auf dem der PEP gehostet wird. Im ASDK hat diese VM den Namen **AzS-ERCS01**. Wenn Sie ein integriertes System verwenden, gibt es drei Instanzen des PEPs. Aus Resilienzgründen werden diese jeweils auf einer VM („*Präfix*-ERCS01“, „*Präfix*-ERCS02“ und „*Präfix*-ERCS03“) auf unterschiedlichen Hosts ausgeführt.

Bevor Sie dieses Verfahren für das integrierte System beginnen, sollten Sie sich vergewissern, dass Sie anhand der IP-Adresse oder per DNS auf den PEP zugreifen können. Nach der erstmaligen Bereitstellung von Azure Stack Hub können Sie nur über die IP-Adresse auf den PEP zugreifen, da die DNS-Integration noch nicht eingerichtet wurde. Ihr OEM-Hardwarehersteller stellt eine JSON-Datei namens **AzureStackStampDeploymentInfo** bereit, die die IP-Adressen des PEPs enthält.

Sie finden die IP-Adresse auch im Azure Stack Hub-Administratorportal. Öffnen Sie das Portal, z. B. `https://adminportal.local.azurestack.external`. Wählen Sie **Regionsverwaltung** > **Eigenschaften** aus.

Sie müssen die aktuelle Kultureinstellung auf `en-US` festlegen, wenn Sie den privilegierten Endpunkt ausführen. Andernfalls werden Cmdlets wie „Test-AzureStack“ oder „Get-AzureStackLog“ nicht wie erwartet ausgeführt.

> [!NOTE]
> Aus Sicherheitsgründen darf mit dem PEP nur über eine gehärtete VM, die auf dem Hardwarelebenszyklus-Host basiert, oder über einen dedizierten, sicheren Computer (beispielsweise eine [Arbeitsstation mit privilegiertem Zugriff](/windows-server/identity/securing-privileged-access/privileged-access-workstations)) eine Verbindung hergestellt werden. Die ursprüngliche Konfiguration des Hardwarelebenszyklus-Hosts darf nicht geändert werden (einschließlich der Installation neuer Software), und er darf nicht zum Herstellen einer Verbindung mit dem PEP verwendet werden.

1. Richten Sie die Vertrauensstellung ein.

   - Führen Sie auf einem integrierten System in einer Windows PowerShell-Sitzung mit erhöhten Rechten den folgenden Befehl aus, um den PEP als vertrauenswürdigen Host auf der gehärteten virtuellen VM hinzuzufügen, die auf dem Hardwarelebenszyklus-Host oder der Arbeitsstation mit privilegiertem Zugriff ausgeführt wird.

      ```powershell  
      Set-Item WSMan:\localhost\Client\TrustedHosts -Value '<IP Address of Privileged Endpoint>' -Concatenate
      ```
      
   - Wenn Sie das ASDK ausführen, melden Sie sich beim Development Kit-Host an.

1. Öffnen Sie auf der gehärteten virtuellen VM, die auf dem Hardwarelebenszyklus-Host oder der Arbeitsstation mit privilegiertem Zugriff ausgeführt wird, eine Windows PowerShell-Sitzung. Führen Sie die folgenden Befehle aus, um eine Remotesitzung auf der VM einzurichten, die den PEP hostet:
 
   - In einem integrierten System:

      ```powershell  
      $cred = Get-Credential

      $pep = New-PSSession -ComputerName <IP_address_of_ERCS> -ConfigurationName PrivilegedEndpoint -Credential $cred -SessionOption (New-PSSessionOption -Culture en-US -UICulture en-US)
      Enter-PSSession $pep
      ```
    
      Der Parameter `ComputerName` kann die IP-Adresse oder der DNS-Name einer VM sein, auf der der PEP gehostet wird.

      > [!NOTE]  
      > Azure Stack Hub führt beim Überprüfen der PEP-Anmeldeinformationen keinen Remoteaufruf durch. Für die Überprüfung wird ein lokal gespeicherter öffentlicher RSA-Schlüssel verwendet.

   - Bei Ausführung des ASDK:

      ```powershell  
      $cred = Get-Credential
    
      $pep = New-PSSession -ComputerName azs-ercs01 -ConfigurationName PrivilegedEndpoint -Credential $cred -SessionOption (New-PSSessionOption -Culture en-US -UICulture en-US)
      Enter-PSSession $pep
      ```
    
   Wenn Sie zur Eingabe von Anmeldeinformationen aufgefordert werden, geben Sie Folgendes ein:
   
   -  **Benutzername**: Geben Sie das Cloudadministratorkonto im Format **&lt;*Azure Stack Hub-Domäne*&gt;\cloudadmin** an. (Für ASDK lautet der Benutzername **azurestack\cloudadmin**.)
   - **Kennwort**: Geben Sie das gleiche Kennwort ein, das während der Installation für das AzureStackAdmin-Domänenadministratorkonto bereitgestellt wurde.

   > [!NOTE]
   > Wenn Sie keine Verbindung mit dem ERCS-Endpunkt herstellen können, wiederholen Sie die Schritte 1 und 2 mit einer anderen ERCS-VM-IP-Adresse.

1. Nachdem Sie eine Verbindung hergestellt haben, ändert sich die Eingabeaufforderung in **[*IP-Adresse oder ERCS-VM-Name*]: PS>** oder zu **[azs-ercs01]: PS>** (abhängig von der Umgebung). Führen Sie `Get-Command` aus, um die Liste der verfügbaren Cmdlets anzuzeigen.

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

Beim PEP handelt es sich wie bereits erwähnt um einen Endpunkt vom Typ [PowerShell JEA](/powershell/scripting/learn/remoting/jea/overview). Ein JEA-Endpunkt bietet nicht nur eine Ebene mit hoher Sicherheit, sondern schränkt auch einige der grundlegenden PowerShell-Funktionen wie Skriptverwendung und Vervollständigung mittels TAB-TASTE ein. Wenn Sie einen Skriptvorgangstyp testen, schlägt der Vorgang mit Fehler **ScriptsNotAllowed** fehl. Dieser Fehler ist das erwartete Verhalten.

Führen Sie den folgenden Befehl aus, wenn Sie beispielsweise die Parameterliste für ein bestimmtes Cmdlet erhalten möchten:

```powershell
    Get-Command <cmdlet_name> -Syntax
```

Alternativ können Sie das Cmdlet [**Import-PSSession**](/powershell/module/microsoft.powershell.utility/import-pssession?view=powershell-5.1) verwenden, um alle PEP-Cmdlets in die aktuelle Sitzung auf Ihrem lokalen Computer zu importieren. Die Cmdlets und Funktionen des privilegierten Endpunkts sind dann auf Ihrem lokalen Computer verfügbar – zusammen mit der Vervollständigung mittels TAB-TASTE und der Skripterstellung im Allgemeinen. Sie können auch das Modul **[Get-Help](/powershell/module/microsoft.powershell.core/get-help)** ausführen, um Anweisungen zu den Cmdlets anzuzeigen.

Wenn Sie die PEP-Sitzung in Ihren lokalen Computer importieren möchten, führen Sie die folgenden Schritte aus:

1. Richten Sie die Vertrauensstellung ein.

   - Führen Sie auf einem integrierten System in einer Windows PowerShell-Sitzung mit erhöhten Rechten den folgenden Befehl aus, um den PEP als vertrauenswürdigen Host auf der gehärteten virtuellen VM hinzuzufügen, die auf dem Hardwarelebenszyklus-Host oder der Arbeitsstation mit privilegiertem Zugriff ausgeführt wird.

      ```powershell
      winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ```

   - Wenn Sie das ASDK ausführen, melden Sie sich beim Development Kit-Host an.

1. Öffnen Sie auf der gehärteten virtuellen VM, die auf dem Hardwarelebenszyklus-Host oder der Arbeitsstation mit privilegiertem Zugriff ausgeführt wird, eine Windows PowerShell-Sitzung. Führen Sie die folgenden Befehle aus, um eine Remotesitzung auf dem virtuellen Computer einzurichten, der den PEP hostet:

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

1. Importieren Sie die PEP-Sitzung in Ihren lokalen Computer:

   ```powershell 
   Import-PSSession $session
   ```

1. Nun können Sie in Ihrer lokalen PowerShell-Sitzung wie gewohnt die Ergänzung mittels TAB-TASTE sowie Skripts verwenden – zusammen mit allen Funktionen und Cmdlets des privilegierten Endpunkts und ohne Beeinträchtigung der Sicherheit von Azure Stack Hub. Viel Spaß!


## <a name="close-the-privileged-endpoint-session"></a>Schließen der Sitzung mit dem privilegierten Endpunkt

Der PEP protokolliert wie bereits erwähnt jede Aktion, die Sie in der PowerShell-Sitzung durchführen, sowie die entsprechende Ausgabe. Schließen Sie die Sitzung mithilfe des Cmdlets `Close-PrivilegedEndpoint`. Dieses Cmdlet schließt den Endpunkt ordnungsgemäß, und überträgt die Protokolldateien an eine externe Dateifreigabe für die Aufbewahrung.

Schließen der Endpunktsitzung:

1. Erstellen Sie eine externe Dateifreigabe, auf die der PEP zugreifen kann. In einer Development Kit-Umgebung können Sie nur eine Dateifreigabe auf dem Development Kit-Host erstellen.
1. Führen Sie das folgende Cmdlet aus:

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

## <a name="unlocking-the-privileged-endpoint-for-support-scenarios"></a>Entsperren des privilegierten Endpunkts für Supportszenarien

Während eines Supportszenarios muss der Microsoft-Supporttechniker möglicherweise die Rechte der PowerShell-Sitzung des privilegierten Endpunkts für den Zugriff auf die Interna der Azure Stack Hub-Infrastruktur erhöhen. Dieser Prozess wird gelegentlich informell als „Scheibe einschlagen“ (break the glass) oder „PEP entsperren“ (unlock the PEP) bezeichnet. Der Rechteerhöhungsprozess für die PEP-Sitzung ist ein zweistufiger Authentifizierungsprozess, der zwei Personen und zwei Organisationen umfasst. Das Verfahren zum Entsperren wird vom Azure Stack Hub-Operator initiiert, der jederzeit die Kontrolle über seine Umgebung behält. Der Operator greift auf den PEP zu und führt das folgende Cmdlet aus:
 
 ```powershell  
      Get-SupportSessionToken
 ```

Das Cmdlet gibt das Anforderungstoken der Supportsitzung zurück, wobei es sich um eine sehr lange alphanumerische Zeichenfolge handelt. Der Operator gibt das Anforderungstoken dann über ein Medium seiner Wahl (z. B. Chat oder E-Mail) an den Microsoft-Supporttechniker weiter. Der Microsoft-Supporttechniker verwendet das Anforderungstoken, um, falls gültig, ein Autorisierungstoken für die Supportsitzung zu generieren, das er dann zurück an den Azure Stack Hub-Operator sendet. In derselben PowerShell-Sitzung des PEP übergibt der Operator dann das Autorisierungstoken als Eingabe an dieses Cmdlet:

```powershell  
      unlock-supportsession
      cmdlet Unlock-SupportSession at command pipeline position 1
      Supply values for the following parameters:
      ResponseToken:
 ```

Wenn das Autorisierungstoken gültig ist, werden die Rechte der PowerShell-Sitzung des PEP erhöht, indem vollständige Administratorfunktionen und vollständiger Zugriff auf die Infrastruktur bereitgestellt werden. 

> [!NOTE]
> Alle Vorgänge und Cmdlets, die in einer PEP-Sitzung mit erhöhten Rechten ausgeführt werden, müssen unter der strengen Aufsicht des Microsoft-Supporttechnikers erfolgen. Wenn dies nicht der Fall ist, kann dies zu schwerwiegenden Ausfallzeiten und Datenverlusten führen sowie eine vollständige erneute Bereitstellung der Azure Stack Hub-Umgebung erfordern.

Nachdem die Supportsitzung beendet wurde, ist es sehr wichtig, die PEP-Sitzung mit erhöhten Rechten mithilfe des Cmdlets **Close-PrivilegedEndpoint-** wieder zu schließen, wie im obigen Abschnitt erläutert. Sobald eine PEP-Sitzung beendet wurde, ist das Entsperrungstoken nicht mehr gültig und kann nicht noch mal verwendet werden, um die PEP-Sitzung erneut zu entsperren.
Eine PEP-Sitzung mit erhöhten Rechten hat eine Gültigkeitsdauer von 8 Stunden, nach deren Ablauf die PEP-Sitzung mit erhöhten Rechten, falls sie nicht beendet wurde, automatisch wider auf eine reguläre PEP-Sitzung reduziert wird.

## <a name="content-of-the-privileged-endpoint-tokens"></a>Inhalt des Tokens des privilegierten Endpunkts

Die PEP-Supportsitzungs-Anforderungs- und -Autorisierungstoken nutzen Kryptografie, um den Zugriff zu schützen und sicherzustellen, dass nur autorisierte Token die PEP-Sitzung entsperren können. Die Token sind darauf ausgelegt, kryptografisch sicherzustellen, dass ein Antworttoken nur von der PEP-Sitzung akzeptiert werden kann, die das Anforderungstoken generiert hat. PEP-Token enthalten keine Informationen, die eine Azure Stack Hub-Umgebung oder einen zugehörigen Kunden eindeutig identifizieren könnten. Sie sind vollständig anonym. Im Folgenden werden die Details des Inhalts jedes einzelnen Tokens bereitgestellt.
 
### <a name="support-session-request-token"></a>Supportsitzungs-Anforderungstoken

Das Anforderungstoken für die PEP-Supportsitzung besteht aus drei Objekten:

- Eine zufällig generierte Sitzungs-ID
- Ein selbst signiertes Zertifikat, das für ein einmaliges Schlüsselpaar aus einem öffentlichen und einem privaten Schlüssel generiert wird. Das Zertifikat enthält keine Informationen zur Umgebung.
- Ein Zeitstempel, der den Ablauf des Anforderungstokens angibt

Das Anforderungstoken wird dann mit dem öffentlichen Schlüssel der Azure Cloud verschlüsselt, bei der die Azure Stack Hub-Umgebung registriert ist.
 
### <a name="support-session-authorization-response-token"></a>Supportsitzungs-Autorisierungsantworttoken

Das Antworttoken für die PEP-Supportsitzungsanforderung besteht aus drei Objekten:

- Die zufällig generierte Sitzungs-ID, die aus dem Anforderungstoken extrahiert wurde
- Ein Zeitstempel, der den Ablauf des Antworttokens angibt
      
Das Antworttoken wird dann mit dem selbstsignierten Zertifikat verschlüsselt, das im Anforderungstoken enthalten ist. Das selbstsignierte Zertifikat wurde mit dem privaten Schlüssel entschlüsselt, der der Azure Cloud zugeordnet ist, bei der die Azure Stack Hub-Umgebung registriert ist.


## <a name="next-steps"></a>Nächste Schritte

- [Azure Stack Hub-Diagnosetools](./azure-stack-diagnostic-log-collection-overview.md?view=azs-2002)
- [Referenz zu privilegierten Azure Stack Hub-Endpunkten](../reference/pep-2002/index.md)
