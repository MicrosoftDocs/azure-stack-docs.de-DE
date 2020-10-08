---
title: Bereitstellen des Netzwerkcontrollers mithilfe von Windows PowerShell
description: Hier erfahren Sie, wie Sie den Netzwerkcontroller mithilfe von Windows PowerShell bereitstellen.
author: v-dasis
ms.topic: how-to
ms.date: 09/22/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: b9f86e715835ac52bbc34782e4f9eb41ca8b4851
ms.sourcegitcommit: 9a3397f703ff9dd7d539372bd8e5fdbe6d6a0725
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/22/2020
ms.locfileid: "91019570"
---
# <a name="deploy-network-controller-using-windows-powershell"></a>Bereitstellen des Netzwerkcontrollers mithilfe von Windows PowerShell

> Gilt für die Azure Stack HCI (Version 20H2) und für Windows Server 2019

In diesem Thema erfahren Sie, wie Sie den Netzwerkcontroller mithilfe von Windows PowerShell auf mindestens einem virtuellen Computer (virtual machine, VM) bereitstellen, der in einem Azure Stack HCI-Cluster ausgeführt wird. Der Netzwerkcontroller ist eine Komponente des softwaredefinierten Netzwerks (SDN).

>[!NOTE]
>Sie können den Netzwerkcontroller auch mithilfe des Assistenten zum Erstellen von Clustern in Windows Admin Center bereitstellen. Weitere Informationen finden Sie unter [Erstellen eines Azure Stack HCI-Clusters mithilfe von Windows Admin Center](create-cluster.md).

## <a name="using-windows-powershell"></a>Verwenden von Windows PowerShell

Sie können PowerShell entweder lokal im Rahmen einer RDP-Sitzung (Remotedesktop) auf einem Hostserver oder remote auf einem Verwaltungscomputer ausführen.

Beim Ausführen von PowerShell von einem Verwaltungscomputer schließen Sie den Parameter `-Name` oder `-Cluster` mit dem Namen des verwalteten Servers oder Clusters ein. Außerdem müssen Sie möglicherweise den vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) angeben, wenn Sie den Parameter `-ComputerName` für einen Server verwenden.

Sie benötigen ferner die RSAT-Cmdlets (Remote Server Administration Tools) und PowerShell-Module für Hyper-V und Failoverclustering. Wenn diese in der PowerShell-Sitzung auf Ihrem Verwaltungscomputer noch nicht verfügbar sind, können Sie sie mit dem folgenden Befehl aktivieren: `Add-WindowsFeature RSAT-Clustering-PowerShell`.

## <a name="install-the-network-controller-server-role"></a>Installieren der Serverrolle „Netzwerkcontroller“

Führen Sie die hier beschriebenen Schritte aus, um die Serverrolle „Netzwerkcontroller“ auf einem virtuellen Computer (virtual machine, VM) zu installieren.

>[!IMPORTANT]
>Stellen Sie die Serverrolle „Netzwerkcontroller“ nicht auf physischen Hosts bereit. Zum Bereitstellen des Netzwerkcontrollers muss die Serverrolle „Netzwerkcontroller“ auf einem virtuellen Hyper-V-Computer installiert werden, der auf einem Hyper-V-Host installiert ist. Nachdem Sie den Netzwerkcontroller für virtuelle Computer auf drei verschiedenen Hyper-V-Hosts installiert haben, müssen Sie für die Hyper-V-Hosts das softwaredefinierte Netzwerk (SDN) aktivieren, indem Sie die Hosts dem Netzwerkcontroller hinzufügen. Dies ermöglicht die Verwendung des SDN-Softwarelastenausgleichs.

Für dieses Verfahren müssen Sie der Gruppe **Administratoren** oder einer entsprechenden Gruppe angehören.  

>[!NOTE]
>Falls Sie den Netzwerkcontroller nicht mithilfe von Windows PowerShell, sondern lieber mithilfe des Server-Managers installieren möchten, lesen Sie [Installieren der Serverrolle „Netzwerkcontroller“ mithilfe des Server-Managers](https://technet.microsoft.com/library/mt403348.aspx).

Geben Sie zum Installieren des Netzwerkcontrollers folgende Befehle ein:

```powershell
Install-WindowsFeature -Name NetworkController -IncludeManagementTools
```

Für die Installation des Netzwerkcontrollers muss der Computer neu gestartet werden. Geben Sie dazu den folgenden Befehl ein:

```powershell
Restart-Computer
```

## <a name="configure-the-network-controller-cluster"></a>Konfigurieren des Netzwerkcontrollerclusters

Der Netzwerkcontrollercluster bietet hohe Verfügbarkeit und Skalierbarkeit für die Netzwerkcontrolleranwendung. Er kann nach der Clustererstellung konfiguriert werden und wird basierend auf dem Cluster gehostet.

>[!NOTE]
>Die Schritte in den folgenden Abschnitten können entweder direkt auf dem virtuellen Computer ausgeführt werden, auf dem Sie den Netzwerkcontroller installiert haben, oder über einen Remotecomputer mit Windows Admin Center. Außerdem müssen Sie für dieses Verfahren der Gruppe **Administratoren** oder einer entsprechenden Gruppe angehören. Wenn der Computer oder virtuelle Computer, auf dem Sie den Netzwerkcontroller installiert haben, in eine Domäne eingebunden wird, muss Ihr Benutzerkonto der Gruppe **Domänenbenutzer** angehören.

Sie können einen Netzwerkcontrollercluster erstellen, indem Sie ein Knotenobjekt erstellen und anschließend den Cluster konfigurieren.

### <a name="create-a-node-object"></a>Erstellen eines Knotenobjekts

Für jeden virtuellen Computer, der dem Netzwerkcontrollercluster angehört, muss ein Knotenobjekt erstellt werden.

Geben Sie den folgenden Befehl ein, um ein Knotenobjekt zu erstellen. Verwenden Sie als Parameter jeweils Werte, die für Ihre Bereitstellung geeignet sind.  

```powershell
New-NetworkControllerNodeObject -Name <string> -Server "ServerName" -FaultDomain "SiteName" -RestInterface "Name" [-NodeCertificate <X509Certificate2>]
```

In der folgenden Tabelle werden die einzelnen Parameter des Befehls `New-NetworkControllerNodeObject` beschrieben:

|Parameter|Beschreibung|
|-------------|---------------|
|Name|Der Parameter **Name** gibt den Anzeigenamen des Servers an, den Sie dem Cluster hinzufügen möchten.|
|Server|Der Parameter **Server** gibt den Hostnamen, den vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) oder die IP-Adresse des Servers an, den Sie dem Cluster hinzufügen möchten. Bei Computern, die einer Domäne angehören, ist der FQDN erforderlich.|
|FaultDomain|Der Parameter **FaultDomain** gibt die Fehlerdomäne für den Server an, den Sie dem Cluster hinzufügen. Dieser Parameter definiert die Server, bei denen unter Umständen zur gleichen Zeit ein Fehler auftritt wie bei dem Server, den Sie dem Cluster hinzufügen. Dieser Fehler kann auf gemeinsame physische Abhängigkeiten wie Stromversorgung und Netzwerkquellen zurückzuführen sein. Fehlerdomänen stellen in der Regel Hierarchien dar, die mit diesen gemeinsamen Abhängigkeiten zusammenhängen. Dabei gilt: Befinden sich die Server an einer höheren Position in der Fehlerdomänenstruktur, tritt wahrscheinlich bei einer höheren Anzahl von Servern gleichzeitig ein Fehler auf. Zur Laufzeit berücksichtigt der Netzwerkcontroller die Fehlerdomänen im Cluster und versucht, die Netzwerkcontrollerdienste für einen bestimmten Dienst so zu verteilen, dass sie sich in separaten Fehlerdomänen befinden. Mit diesem Prozess wird sichergestellt, dass die Verfügbarkeit des Diensts und sein Zustand bei einem Ausfall einer Fehlerdomäne nicht gefährdet werden. Fehlerdomänen werden in einem hierarchischen Format angegeben. Beispiel: „Fd:/DC1/Rack1/Host1“. „DC1“ ist hierbei der Name des Rechenzentrums, „Rack1“ der Name des Racks und „Host1“ der Name des Hosts, auf dem der Knoten platziert wird.|
|RestInterface|Der Parameter **RestInterface** gibt den Namen der Schnittstelle auf dem Knoten an, bei der die REST-Kommunikation (Representational State Transfer) endet. Diese Netzwerkcontrollerschnittstelle empfängt Northbound-API-Anforderungen von der Verwaltungsebene des Netzwerks.|
|NodeCertificate|Der Parameter **NodeCertificate** gibt das Zertifikat an, das vom Netzwerkcontroller für die Computerauthentifizierung verwendet wird. Das Zertifikat ist erforderlich, wenn Sie für die Kommunikation innerhalb des Clusters die zertifikatbasierte Authentifizierung verwenden. Darüber hinaus wird das Zertifikat auch für die Verschlüsselung des Datenverkehrs zwischen den Netzwerkcontrollerdiensten verwendet. Der Name des Zertifikatantragstellers muss dem DNS-Namen des Knotens entsprechen.|

### <a name="configure-the-cluster"></a>Konfigurieren des Clusters

Geben Sie den folgenden Befehl ein, um den Cluster zu konfigurieren. Verwenden Sie als Parameter jeweils Werte, die für Ihre Bereitstellung geeignet sind.

```powershell
Install-NetworkControllerCluster -Node "NetworkControllerNodeName" -ClusterAuthentication "ClusterAuthenticationType" [-ManagementSecurityGroup <string>][-DiagnosticLogLocation <string>][-LogLocationCredential <PSCredential>] [-CredentialEncryptionCertificate <X509Certificate2>][-Credential <PSCredential>][-CertificateThumbprint <String>] [-UseSSL][-ComputerName <string>][-LogSizeLimitInMBs<UInt32>] [-LogTimeLimitInDays<UInt32>]
```

In der folgenden Tabelle werden die einzelnen Parameter des Befehls `Install-NetworkControllerCluster` beschrieben:
  
|Parameter|BESCHREIBUNG|
|-------------|---------------|
|ClusterAuthentication|Der Parameter **ClusterAuthentication** gibt den Authentifizierungstyp an, der zum Schutz der Kommunikation zwischen Knoten sowie für die Verschlüsselung von Datenverkehr zwischen den Netzwerkcontrollerdiensten verwendet wird. Unterstützte Werte sind **Kerberos**, **X509** und **Keine**. Bei der Kerberos-Authentifizierung werden Domänenkonten verwendet. Sie kann daher nur genutzt werden, wenn die Netzwerkcontrollerknoten einer Domäne angehören. Bei Verwendung der X509-basierten Authentifizierung muss ein Zertifikat im NetworkControllerNode-Objekt bereitgestellt werden. Darüber hinaus muss das Zertifikat vor dem Ausführen dieses Befehls manuell bereitgestellt werden.|
|ManagementSecurityGroup|Der Parameter **ManagementSecurityGroup** gibt den Namen der Sicherheitsgruppe mit Benutzern an, die berechtigt sind, die Verwaltungs-Cmdlets über einen Remotecomputer auszuführen. Dies ist nur relevant, wenn die Clusterauthentifizierung auf „Kerberos“ festgelegt ist. Geben Sie keine Sicherheitsgruppe auf dem lokalen Computer, sondern eine Domänensicherheitsgruppe an.|
|Node|Der Parameter **Node** gibt die Liste der Netzwerkcontrollerknoten an, die Sie mithilfe des Befehls **New-NetworkControllerNodeObject** erstellt haben.|
|DiagnosticLogLocation|Der Parameter **DiagnosticLogLocation** gibt den Freigabespeicherort an, an den die Diagnoseprotokolle in regelmäßigen Abständen hochgeladen werden. Wenn Sie für diesen Parameter keinen Wert angeben, werden die Protokolle lokal auf dem jeweiligen Knoten gespeichert. Protokolle werden lokal im Ordner „%systemdrive%\Windows\tracing\SDNDiagnostics“ gespeichert. Clusterprotokolle werden lokal im Ordner „%systemdrive%\ProgramData\Microsoft\Service Fabric\log\Traces“ gespeichert.|
|LogLocationCredential|Der Parameter **LogLocationCredential** gibt die Anmeldeinformationen an, die für den Zugriff auf den Freigabespeicherort erforderlich sind, an dem die Protokolle gespeichert werden.|
|CredentialEncryptionCertificate|Der Parameter **CredentialEncryptionCertificate** gibt das Zertifikat an, das der Netzwerkcontroller zur Verschlüsselung der Anmeldeinformationen verwendet, die für den Zugriff auf Binärdateien des Netzwerkcontrollers sowie für den Zugriff auf **LogLocationCredential** verwendet werden (sofern angegeben). Das Zertifikat muss auf allen Netzwerkcontrollerknoten bereitgestellt werden, bevor Sie diesen Befehl ausführen, und das gleiche Zertifikat muss auf allen Clusterknoten registriert werden. Dieser Parameter sollte in Produktionsumgebungen zum Schutz der Binärdateien und Protokolle des Netzwerkcontrollers verwendet werden. Ohne diesen Parameter werden die Anmeldeinformationen als Klartext gespeichert und können von nicht autorisierten Benutzern missbraucht werden.|
|Anmeldeinformationen|Dieser Parameter ist nur erforderlich, wenn Sie den Befehl über einen Remotecomputer ausführen. Der Parameter **Credential** gibt ein Benutzerkonto an, das dazu berechtigt ist, den Befehl auf dem Zielcomputer auszuführen.|
|CertificateThumbprint|Dieser Parameter ist nur erforderlich, wenn Sie den Befehl über einen Remotecomputer ausführen. Der Parameter **CertificateThumbprint** gibt das digitale Zertifikat für öffentliche Schlüssel (X509) eines Benutzerkontos an, das dazu berechtigt ist, den Befehl über einen Remotecomputer ausführen.|
|UseSSL|Dieser Parameter ist nur erforderlich, wenn Sie den Befehl über einen Remotecomputer ausführen. Der Parameter **UseSSL** gibt das SSL-Protokoll (Secure Sockets Layer) an, das für die Verbindungsherstellung mit dem Remotecomputer verwendet wird. Standardmäßig wird SSL nicht verwendet.|
|Computername|Der Parameter **ComputerName** gibt den Netzwerkcontrollerknoten an, auf dem der Befehl ausgeführt wird. Wenn Sie für diesen Parameter keinen Wert angeben, wird standardmäßig der lokale Computer verwendet.|
|LogSizeLimitInMBs|Dieser Parameter gibt die maximale Protokollgröße (in MB) an, die vom Netzwerkcontroller gespeichert werden kann. Protokolle werden zirkulär gespeichert. Wenn Sie „DiagnosticLogLocation“ angeben, liegt der Standardwert des Parameters bei 40 GB. Ohne Angabe von „DiagnosticLogLocation“ werden die Protokolle auf den Netzwerkcontrollerknoten gespeichert, und der Standardwert des Parameters liegt bei 15 GB.|
|LogTimeLimitInDays|Dieser Parameter gibt den Grenzwert für die Dauer (in Tagen) an, für die Protokolle gespeichert werden. Protokolle werden zirkulär gespeichert. Der Standardwert dieses Parameters liegt bei drei Tagen.|

## <a name="configure-the-network-controller-application"></a>Konfigurieren der Netzwerkcontrolleranwendung

Geben Sie den folgenden Befehl ein, um die Netzwerkcontrolleranwendung zu konfigurieren. Verwenden Sie als Parameter jeweils Werte, die für Ihre Bereitstellung geeignet sind.

```powershell
Install-NetworkController -Node <NetworkControllerNode[]> -ClientAuthentication <ClientAuthentication>  [-ClientCertificateThumbprint <string[]>]  [-ClientSecurityGroup <string>] -ServerCertificate <X509Certificate2> [-RESTIPAddress <String>] [-RESTName <String>] [-Credential <PSCredential>][-CertificateThumbprint <String> ] [-UseSSL]
```

In der folgenden Tabelle werden die einzelnen Parameter des Befehls `Install-NetworkController` beschrieben:

|Parameter|BESCHREIBUNG|
|-------------|---------------|
|ClientAuthentication|Der Parameter **ClientAuthentication** gibt den Authentifizierungstyp an, der zum Schutz der Kommunikation zwischen REST und Netzwerkcontroller verwendet wird. Unterstützte Werte sind **Kerberos**, **X509** und **Keine**. Bei der Kerberos-Authentifizierung werden Domänenkonten verwendet. Sie kann daher nur genutzt werden, wenn die Netzwerkcontrollerknoten einer Domäne angehören. Bei Verwendung der X509-basierten Authentifizierung muss ein Zertifikat im NetworkControllerNode-Objekt bereitgestellt werden. Darüber hinaus muss das Zertifikat vor dem Ausführen dieses Befehls manuell bereitgestellt werden.|
|Node|Der Parameter **Node** gibt die Liste der Netzwerkcontrollerknoten an, die Sie mithilfe des Befehls **New-NetworkControllerNodeObject** erstellt haben.|
|ClientCertificateThumbprint|Dieser Parameter ist nur erforderlich, wenn für Netzwerkcontrollerclients die zertifikatbasierte Authentifizierung verwendet wird. Der Parameter **ClientCertificateThumbprint** gibt den Fingerabdruck des Zertifikats an, das für Clients auf der Northbound-Ebene registriert ist.|
|ServerCertificate|Der Parameter **ServerCertificate** gibt das Zertifikat an, das vom Netzwerkcontroller verwendet wird, um gegenüber Clients die eigene Identität nachzuweisen. Das Serverzertifikat muss den Zweck der Serverauthentifizierung in Erweiterungen für die erweiterte Schlüsselverwendung enthalten und für den Netzwerkcontroller von einer Zertifizierungsstelle ausgegeben werden, die von Clients als vertrauenswürdig eingestuft wird.|
|RESTIPAddress|Bei einer Netzwerkcontrollerbereitstellung mit nur einem Knoten muss für **RESTIPAddress** kein Wert angegeben werden. Bei Bereitstellungen mit mehreren Knoten gibt der Parameter **RESTIPAddress** die IP-Adresse des REST-Endpunkts in CIDR-Notation an. Beispiel: 192.168.1.10/24. Der Wert des Antragstellernamens von **ServerCertificate** muss zum Wert des Parameters **RESTIPAddress** aufgelöst werden. Dieser Parameter muss für alle Netzwerkcontrollerbereitstellungen mit mehreren Knoten angegeben werden, wenn sich alle Knoten im gleichen Subnetz befinden. Wenn sich Knoten in unterschiedlichen Subnetzen befinden, muss anstelle von **RESTIPAddress** der Parameter **RestName** verwendet werden.|
|RESTName|Bei einer Netzwerkcontrollerbereitstellung mit nur einem Knoten muss für **RestName** kein Wert angegeben werden. Ein Wert für **RestName** ist nur bei Bereitstellungen mit mehreren Knoten erforderlich, bei denen sich Knoten in unterschiedlichen Subnetzen befinden. Bei Bereitstellungen mit mehreren Knoten gibt der Parameter **RestName** den FQDN für den Netzwerkcontrollercluster an.|
|ClientSecurityGroup|Der Parameter **ClientSecurityGroup** gibt den Namen der Active Directory-Sicherheitsgruppe an, bei deren Mitgliedern es sich um Netzwerkcontrollerclients handelt. Dieser Parameter ist nur erforderlich, wenn für **ClientAuthentication** die Kerberos-Authentifizierung verwendet wird. Die Sicherheitsgruppe muss die Konten enthalten, von denen aus auf die REST-APIs zugegriffen wird, und Sie müssen die Sicherheitsgruppe erstellen und Mitglieder hinzufügen, bevor Sie den Befehl ausführen.|
|Anmeldeinformationen|Dieser Parameter ist nur erforderlich, wenn Sie den Befehl über einen Remotecomputer ausführen. Der Parameter **Credential** gibt ein Benutzerkonto an, das dazu berechtigt ist, den Befehl auf dem Zielcomputer auszuführen.|
|CertificateThumbprint|Dieser Parameter ist nur erforderlich, wenn Sie den Befehl über einen Remotecomputer ausführen. Der Parameter **CertificateThumbprint** gibt das digitale Zertifikat für öffentliche Schlüssel (X509) eines Benutzerkontos an, das dazu berechtigt ist, den Befehl über einen Remotecomputer ausführen.|
|UseSSL|Dieser Parameter ist nur erforderlich, wenn Sie den Befehl über einen Remotecomputer ausführen. Der Parameter **UseSSL** gibt das SSL-Protokoll (Secure Sockets Layer) an, das für die Verbindungsherstellung mit dem Remotecomputer verwendet wird. Standardmäßig wird SSL nicht verwendet.|

Nachdem Sie die Netzwerkcontrolleranwendung konfiguriert haben, ist die Bereitstellung des Netzwerkcontrollers abgeschlossen.

## <a name="network-controller-deployment-validation"></a>Überprüfung der Netzwerkcontrollerbereitstellung

Zur Überprüfung Ihrer Netzwerkcontrollerbereitstellung können Sie dem Netzwerkcontroller Anmeldeinformationen hinzufügen und diese anschließend abrufen.

Wenn Sie eine Clientauthentifizierung (`ClientAuthentication`) vom Typ „Kerberos“ verwenden, müssen Sie der von Ihnen erstellten Gruppe **ClientSecurityGroup** angehören, um diese Schritte ausführen zu können.

1. Melden Sie sich bei Verwendung einer Clientauthentifizierung (`ClientAuthentication`) vom Typ „Kerberos“ auf einem Clientcomputer mit einem Benutzerkonto an, das der Gruppe **ClientSecurityGroup** angehört.

1. Geben Sie in PowerShell die folgenden Befehle ein: Verwenden Sie als Parameter jeweils Werte, die für Ihre Bereitstellung geeignet sind.

    ```powershell
    $cred=New-Object Microsoft.Windows.Networkcontroller.credentialproperties
    $cred.type="usernamepassword"
    $cred.username="admin"
    $cred.value="abcd"

    New-NetworkControllerCredential -ConnectionUri "https://networkcontroller"-Properties $cred -ResourceId "cred1"
    ```

1. Geben Sie den folgenden Befehl ein, um die Anmeldeinformationen abzurufen, die Sie dem Netzwerkcontroller hinzugefügt haben. Verwenden Sie als Parameter jeweils Werte, die für Ihre Bereitstellung geeignet sind.

    ```powershell
    Get-NetworkControllerCredential -ConnectionUri https://networkcontroller -ResourceId cred1  
    ```

1. Sehen Sie sich die Befehlsausgabe an. Diese sollte in etwa wie im folgenden Beispiel aussehen:

    ```powershell
    Tags                   :
    ResourceRef     : /credentials/cred1
    CreatedTime    : 1/1/0001 12:00:00 AM
    InstanceId        : e16ffe62-a701-4d31-915e-7234d4bc5a18
    Etag                  : W/"1ec59631-607f-4d3e-ac78-94b0822f3a9d"
    ResourceMetadata :
    ResourceId       : cred1
    Properties       : Microsoft.Windows.NetworkController.CredentialProperties
    ```

    > [!NOTE]
    > Wenn Sie den Befehl `Get-NetworkControllerCredential` ausführen, können Sie die Ausgabe des Befehls einer Variablen zuweisen. Verwenden Sie dazu den Punkt-Operator, um die Eigenschaften der Anmeldeinformationen aufzulisten. Beispiel: `$cred.Properties`.

## <a name="additional-powershell-commands-for-network-controller"></a>Weitere PowerShell-Befehle für den Netzwerkcontroller

Nachdem Sie den Netzwerkcontroller bereitgestellt haben, können Sie Ihre Bereitstellung mithilfe von PowerShell-Befehlen verwalten und ändern. Im Anschluss finden Sie einige der Änderungsmöglichkeiten für Ihre Bereitstellung:

- Ändern von Einstellungen für Netzwerkcontrollerknoten, -cluster und -anwendung

- Entfernen des Netzwerkcontrollerclusters und der -anwendung

- Verwalten von Netzwerkcontroller-Clusterknoten (einschließlich Hinzufügen, Entfernen, Aktivieren und Deaktivieren von Knoten)

Die folgende Tabelle enthält die Syntax für PowerShell-Befehle, die Sie für diese Aufgaben verwenden können:

|Aufgabe|Get-Help|Syntax|
|--------|-------|----------|
|Ändern der Einstellungen des Netzwerkcontrollerclusters|Set-NetworkControllerCluster|`Set-NetworkControllerCluster [-ManagementSecurityGroup <string>][-Credential <PSCredential>] [-computerName <string>][-CertificateThumbprint <String> ] [-UseSSL]`
|Ändern der Einstellungen der Netzwerkcontrolleranwendung|Set-NetworkController|`Set-NetworkController [-ClientAuthentication <ClientAuthentication>] [-Credential <PSCredential>] [-ClientCertificateThumbprint <string[]>] [-ClientSecurityGroup <string>] [-ServerCertificate <X509Certificate2>] [-RestIPAddress <String>] [-ComputerName <String>][-CertificateThumbprint <String> ] [-UseSSL]`
|Ändern der Einstellungen von Netzwerkcontrollerknoten|Set-NetworkControllerNode|`Set-NetworkControllerNode -Name <string> > [-RestInterface <string>] [-NodeCertificate <X509Certificate2>] [-Credential <PSCredential>] [-ComputerName <string>][-CertificateThumbprint <String> ] [-UseSSL]`
|Ändern der Diagnoseeinstellungen für Netzwerkcontrollerknoten|Set-NetworkControllerDiagnostic|`Set-NetworkControllerDiagnostic [-LogScope <string>] [-DiagnosticLogLocation <string>] [-LogLocationCredential <PSCredential>] [-UseLocalLogLocation] >] [-LogLevel <loglevel>][-LogSizeLimitInMBs <uint32>] [-LogTimeLimitInDays <uint32>] [-Credential <PSCredential>] [-ComputerName <string>][-CertificateThumbprint <String> ] [-UseSSL]`
|Entfernen der Netzwerkcontrolleranwendung|Uninstall-NetworkController|`Uninstall-NetworkController [-Credential <PSCredential>][-ComputerName <string>] [-CertificateThumbprint <String> ] [-UseSSL]`
|Entfernen des Netzwerkcontrollerclusters|Uninstall-NetworkControllerCluster|`Uninstall-NetworkControllerCluster [-Credential <PSCredential>][-ComputerName <string>][-CertificateThumbprint <String> ] [-UseSSL]`
|Hinzufügen eines Knotens zum Netzwerkcontrollercluster|Add-NetworkControllerNode|`Add-NetworkControllerNode -FaultDomain <String> -Name <String> -RestInterface <String> -Server <String> [-CertificateThumbprint <String> ] [-ComputerName <String> ] [-Credential <PSCredential> ] [-Force] [-NodeCertificate <X509Certificate2> ] [-PassThru] [-UseSsl]`
|Deaktivieren eines Knotens des Netzwerkcontrollerclusters|Disable-NetworkControllerNode|`Disable-NetworkControllerNode -Name <String> [-CertificateThumbprint <String> ] [-ComputerName <String> ] [-Credential <PSCredential> ] [-PassThru] [-UseSsl]`
|Aktivieren eines Knotens des Netzwerkcontrollerclusters|Enable-NetworkControllerNode|`Enable-NetworkControllerNode -Name <String> [-CertificateThumbprint <String> ] [-ComputerName <String> ] [-Credential <PSCredential> ] [-PassThru] [-UseSsl]`
|Entfernen eines Netzwerkcontrollerknotens aus einem Cluster|Remove-NetworkControllerNode|`Remove-NetworkControllerNode [-CertificateThumbprint <String> ] [-ComputerName <String> ] [-Credential <PSCredential> ] [-Force] [-Name <String> ] [-PassThru] [-UseSsl]`

Weitere Informationen finden Sie in der Windows PowerShell-Referenzdokumentation für den Netzwerkcontroller unter [NetworkController](/powershell/module/networkcontroller/?view=win10-ps).

## <a name="sample-network-controller-configuration-script"></a>Beispielkonfigurationsskript für den Netzwerkcontroller

Das folgende Beispielkonfigurationsskript zeigt die Erstellung eines Netzwerkcontrollerclusters mit mehreren Knoten sowie die Installation der Netzwerkcontrolleranwendung. Darüber hinaus wird mithilfe der Variablen `$cert` ein Zertifikat aus dem Zertifikatspeicher des lokalen Computers ausgewählt, das der Zeichenfolge des Antragstellernamens (networkController.contoso.com) entspricht.

```powershell
$a = New-NetworkControllerNodeObject -Name "Node1" -Server "NCNode1.contoso.com" -FaultDomain "fd:/rack1/host1" -RestInterface Internal
$b = New-NetworkControllerNodeObject -Name "Node2" -Server "NCNode2.contoso.com" -FaultDomain "fd:/rack1/host2" -RestInterface Internal
$c = New-NetworkControllerNodeObject -Name "Node3" -Server "NCNode3.contoso.com" -FaultDomain "fd:/rack1/host3" -RestInterface Internal

$cert= get-item Cert:\LocalMachine\My | get-ChildItem | where {$_.Subject -imatch "networkController.contoso.com" }

Install-NetworkControllerCluster -Node @($a,$b,$c)  -ClusterAuthentication Kerberos -DiagnosticLogLocation \\share\Diagnostics - ManagementSecurityGroup Contoso\NCManagementAdmins -CredentialEncryptionCertificate $cert  
Install-NetworkController -Node @($a,$b,$c) -ClientAuthentication Kerberos -ClientSecurityGroup Contoso\NCRESTClients -ServerCertificate $cert -RestIpAddress 10.0.0.1/24
```

## <a name="next-steps"></a>Nächste Schritte

Bei Netzwerkcontrollerbereitstellungen ohne Kerberos müssen Zertifikate bereitgestellt werden. Weitere Informationen finden Sie in den [Schritten nach der Netzwerkcontrollerbereitstellung](https://docs.microsoft.com/windows-server/networking/sdn/technologies/network-controller/post-deploy-steps-nc).
