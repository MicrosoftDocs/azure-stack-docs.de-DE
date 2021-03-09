---
title: Set-AksHciConfig
author: jessicaguan
description: Der PowerShell-Befehl Set-AksHciConfig aktualisiert die Konfigurationseinstellungen für den Azure Kubernetes Service-Host.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 80a6fc50bbfcaf028d6c810bbef7e2d6b4508cab
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "101874487"
---
# <a name="set-akshciconfig"></a>Set-AksHciConfig

## <a name="synopsis"></a>Übersicht
Festlegen oder Aktualisieren der Konfigurationseinstellungen für den Azure Kubernetes Service-Host.

## <a name="syntax"></a>Syntax

### <a name="set-configuration-for-host"></a>Festlegen der Konfiguration für den Host
```powershell
Set-AksHciConfig [-imageDir <String>]
                 [-cloudConfigLocation <String>]
                 [-nodeConfigLocation <String>]
                 [-vnet <Virtual Network>]
                 [-controlPlaneVmSize <VmSize>]
                 [-sshPublicKey <String>]
                 [-macPoolStart <String>]
                 [-macPoolEnd <String>]       
                 [-proxyServerHTTP <String>]
                 [-proxyServerHTTPS <String>]
                 [-proxyServerNoProxy <String>]
                 [-proxyServerCertFile <String>]
                 [-proxyServerCredential <PSCredential>]
                 [-cloudServiceCidr <String>]
                 [-workingDir <String>]
                 [-version <String>]
                 [-nodeAgentPort <int>]
                 [-nodeAgentAuthorizerPort <int>]
                 [-clusterRoleName <String>]
                 [-cloudLocation <String>]
                 [-skipHostLimitChecks]
                 [-insecure]
                 [-skipUpdates]
                 [-forceDnsReplication]
                 [-enableDiagnosticData]   
```

## <a name="description"></a>BESCHREIBUNG
Legen Sie die Konfigurationseinstellungen für den Azure Kubernetes Service-Host fest. Wenn Sie die Bereitstellung auf einem Azure Stack HCI-Cluster mit 2 bis 4 Knoten oder einem Windows Server 2019 Datacenter-Failovercluster vornehmen, müssen Sie die Parameter imageDir und cloudConfigLocation angeben. Unter Windows Server 2019 Datacenter mit einem einzelnen Knoten sind alle Parameter optional und auf die Standardwerte festgelegt. Zur Erzielung einer optimalen Leistung empfehlen wir Ihnen aber, die Bereitstellung eines Azure Stack HCI-Clusters mit zwei bis vier Knoten zu verwenden.

## <a name="examples"></a>Beispiele

### <a name="to-deploy-on-a-2-4-node-cluster-with-dhcp-networking"></a>Ausführen der Bereitstellung auf einem Cluster mit 2 bis 4 Knoten mit einem DHCP-Netzwerk
```powershell
PS C:\> Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config
```

### <a name="to-deploy-with-a-virtual-ip-pool"></a>Ausführen der Bereitstellung mit einem virtuellen IP-Pool
```powershell
PS C:\> New-AksHciNetworkSetting -name newNetwork -subnetCidr 192.168.2.0/32 -defaultGateway 192.168.2.1 -subnetVSwitch External -vipPoolStart 192.168.2.20 -vipPoolEnd 192.168.2.80   
PS C:\> Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -networkSettings newNetwork
```

### <a name="to-deploy-with-a-proxy-server"></a>Ausführen der Bereitstellung mit einem Proxyserver
```powershell
PS C:\> $credential = Get-Credential
```

```powershell
PS C:\> Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -proxyServerHttp "http://proxy.contoso.com:8888" -proxyServerHttps "http://proxy.contoso.com:8888" -proxyServerNoProxy "localhost,127.0.0.1,.svc,10.96.0.0/12,10.244.0.0/16,10.231.110.0/24,10.68.237.0/24" -proxyServerCredential $credential
```

## <a name="parameters"></a>Parameter

### <a name="-imagedir"></a>-imageDir
Der Pfad zu dem Verzeichnis, in dem von Azure Kubernetes Service unter Azure Stack HCI die VHD-Images gespeichert werden. Für Bereitstellungen mit einem Knoten lautet er standardmäßig `%systemdrive%\AksHciImageStore`. Bei Bereitstellungen mit mehreren Knoten muss dieser Parameter angegeben werden. Es muss ein Pfad zu einem freigegebenen Speicher, z. B.  `C:\ClusterStorage\Volume2\ImageStore`, oder zu einer SMB-Freigabe, z. B.  `\\FileShare\ImageStore`, angegeben werden.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: %systemdrive%\AksHciImageStore
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-cloudconfiglocation"></a>-cloudConfigLocation
Der Speicherort, an dem vom Cloud-Agent die Konfiguration gespeichert wird. Für Bereitstellungen mit einem Knoten lautet er standardmäßig `%systemdrive%\wssdcloudagent`. Der Speicherort kann dem Pfad von -imageDir oben entsprechen. Bei Bereitstellungen mit mehreren Knoten muss dieser Parameter angegeben werden. Es muss ein Pfad zu einem freigegebenen Speicher, z. B.  `C:\ClusterStorage\Volume2\ImageStore`, oder zu einer SMB-Freigabe, z. B.  `\\FileShare\ImageStore`, angegeben werden. Der Speicherort muss sich auf einer hochverfügbaren Freigabe befinden, damit der Speicher immer zugänglich ist.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: %systemdrive%\wssdcloudagent
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-nodeconfiglocation"></a>-nodeConfigLocation
Der Speicherort, an dem von den Knoten-Agents die Konfiguration gespeichert wird. Jeder Knoten verfügt über einen Knoten-Agent, sodass seine Konfiguration lokal ist. Dieser Speicherort muss ein lokaler Pfad sein. Der Standardwert ist für alle Bereitstellungen `%systemdrive%\programdata\wssdagent`.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: %systemdrive%\programdata\wssdagent
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-vnet"></a>-vnet
Der Name des AksHciNetworkSetting-Objekts, das mit dem Befehl New-AksHciNetworkSetting erstellt wurde.
```yaml
Type: VirtualNetwork
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-controlplanevmsize"></a>-controlPlaneVmSize
Die Größe des virtuellen Computers, der für die Steuerungsebene erstellt werden soll. Der Standard ist „Standard_A4_V2“. Führen Sie `Get-AksHciVmSize` aus, um eine Liste mit den verfügbaren VM-Größen abzurufen.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: Standard_A4_V2
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-sshpublickey"></a>-sshPublicKey
Pfad zu einer Datei mit einem öffentlichen SSH-Schlüssel. Mit diesem öffentlichen Schlüssel können Sie sich bei allen VMs anmelden, die von der Azure Kubernetes Service-Bereitstellung unter Azure Stack HCI erstellt werden. Wenn Sie über einen eigenen öffentlichen SSH-Schlüssel verfügen, übergeben Sie seinen Speicherort hier. Wenn kein Schlüssel angegeben wird, wird unter `%systemdrive%\akshci\.ssh\akshci_rsa`.pub danach gesucht. Wenn die Datei nicht vorhanden ist, wird am obigen Speicherort ein SSH-Schlüsselpaar generiert und dann verwendet.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-macpoolstart"></a>-macPoolStart
Wird zum Angeben des Starts für die MAC-Adresse des MAC-Pools verwendet, den Sie für die Azure Kubernetes Service-Host-VM verwenden möchten. Bei der Syntax für die MAC-Adresse ist es erforderlich, dass das unwichtigste Bit des ersten Bytes immer „0“ ist, und das erste Byte sollte immer eine gerade Zahl sein (d. h. 00, 02, 04, 06...). Eine typische MAC-Adresse kann wie folgt aussehen: 02:1E:2B:78:00:00. Verwenden Sie MAC-Pools für langlebige Bereitstellungen, damit die zugewiesenen MAC-Adressen konsistent sind. Dies ist hilfreich, wenn eine Anforderung vorhanden ist, dass die VMs über bestimmte MAC-Adressen verfügen. Standardwert ist „none“.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-macpoolend"></a>-macPoolEnd
Wird zum Angeben des Endes für die MAC-Adresse des MAC-Pools verwendet, den Sie für die Azure Kubernetes Service-Host-VM verwenden möchten. Bei der Syntax für die MAC-Adresse ist es erforderlich, dass das unwichtigste Bit des ersten Bytes immer „0“ ist, und das erste Byte sollte immer eine gerade Zahl sein (d. h. 00, 02, 04, 06...). Das erste Byte der Adresse, die als -macPoolEnd übergeben wird, sollte dem ersten Byte der Adresse entsprechen, die als -macPoolStart übergeben wird. Verwenden Sie MAC-Pools für langlebige Bereitstellungen, damit die zugewiesenen MAC-Adressen konsistent sind. Dies ist hilfreich, wenn eine Anforderung vorhanden ist, dass die VMs über bestimmte MAC-Adressen verfügen. Standardwert ist „none“.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-proxyserverhttp"></a>-proxyServerHTTP
Dadurch wird ein Proxyserver-URI bereitgestellt, der von allen Komponenten verwendet werden muss, die HTTP-Endpunkte erreichen müssen. Das URI-Format enthält das URI-Schema, die Serveradresse und den Port (d. h. https://server.com:8888) ). Standardwert ist „none“.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-proxyserverhttps"></a>-proxyServerHTTPS
Dadurch wird ein Proxyserver-URI bereitgestellt, der von allen Komponenten verwendet werden muss, die HTTPS-Endpunkte erreichen müssen. Das URI-Format enthält das URI-Schema, die Serveradresse und den Port (d. h. https://server.com:8888) ). Standardwert ist „none“.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-proxyservernoproxy"></a>-proxyServerNoProxy
Dies ist eine durch Trennzeichen getrennte Zeichenfolge von Adressen, die vom Proxy ausgenommen werden. Der Standardwert ist localhost,127.0.0.1,.svc,10.96.0.0/12,10.244.0.0/16. Dies schließt den localhost-Datenverkehr (localhost, 127.0.0.1), den internen Kubernetes Service-Datenverkehr (.svc), die Kubernetes Service-CIDR (10.96.0.0/12) und die Kubernetes POD-CIDR (10.244.0.0/16) vom Proxyserver aus. Mit diesem Parameter können Sie weitere Subnetzbereiche oder Namensausnahmen hinzufügen. **Die Einstellungen für diesen Parameter sind sehr wichtig, denn wenn er nicht ordnungsgemäß konfiguriert ist, können Sie ggf. den internen Datenverkehr des Kubernetes-Clusters unerwarteterweise an Ihren Proxy weiterleiten. Dies kann zu verschiedenen Fehlern bei der Netzwerkkommunikation führen.**

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-proxyservercertfile"></a>-proxyServerCertFile
Das Zertifikat, das zum Authentifizieren beim Proxyserver verwendet wird.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:
 
Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-proxyservercredential"></a>-proxyServerCredential
Hiermit werden der Benutzername und das Kennwort für die Authentifizierung bei den HTTP-/HTTPS-Proxyservern bereitstellt. Sie können `Get-Credential` verwenden, um ein PSCredential-Objekt zu generieren, das an diesen Parameter übergeben wird. Standardwert ist „none“.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-cloudservicecidr"></a>-cloudServiceCidr
Dies kann verwendet werden, um statische IP/Netzwerkpräfix anzugeben, die dem MOC-CloudAgent-Dienst zugewiesen werden soll. Dieser Wert sollte im CIDR-Format angegeben werden. (Beispiel: 192.168.1.2/16) Sie können dies angeben, um sicherzustellen, dass alles Wichtige im Netzwerk immer zugänglich ist, da sich die IP-Adresse nicht ändert. Standardwert ist „none“.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-workingdir"></a>-workingDir
Dies ist ein Arbeitsverzeichnis für das Modul, in dem kleine Dateien gespeichert werden. Die Standardeinstellung ist `%PROGRAMFILES%\AksHci`. Sie sollte für die meisten Bereitstellungen nicht geändert werden. Es wird nicht empfohlen, den Standardwert zu ändern.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: %PROGRAMFILES%\AksHci
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-version"></a>-version
Die Version von Azure Kubernetes Service unter Azure Stack HCI, die Sie bereitstellen möchten. Der Standardwert ist die neueste Version. Es wird nicht empfohlen, den Standardwert zu ändern.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: Latest version
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-nodeagentport"></a>-nodeAgentPort
Die TCP/IP-Portnummer, über die Knoten-Agents lauschen sollen. Der Standardwert ist „45000“. Es wird nicht empfohlen, den Standardwert zu ändern.

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: 45000
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-nodeagentauthorizerport"></a>-nodeAgentAuthorizerPort
Die TCP/IP-Portnummer, die von Knoten-Agents für ihren Autorisierungsport verwendet werden soll. Der Standardwert ist „45001“. Es wird nicht empfohlen, den Standardwert zu ändern. 

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: 45001
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-clusterrolename"></a>-clusterRoleName
Hier wird der Name angegeben, der beim Erstellen eines Knoten-Agents als generischer Dienst im Cluster verwendet werden soll. Standardmäßig wird ein eindeutiger Name mit dem Präfix „ca-“ und einem GUID-Suffix (z. B. „ca-9e6eb299-bc0b-4f00-9fd7-942843820c26“) verwendet. Es wird nicht empfohlen, den Standardwert zu ändern.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: A unique name with a prefix of ca- and a guid suffix
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-cloudlocation"></a>-cloudLocation
Dieser Parameter stellt einen benutzerdefinierten, von Microsoft betriebenen Cloudspeicherortnamen bereit. Der Standardname lautet „MocLocation“. Es wird nicht empfohlen, den Standardwert zu ändern.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: MocLocation
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-skiphostlimitchecks"></a>-skipHostLimitChecks
Fordert vom Skript das Überspringen aller Überprüfungen, die zum Bestätigen der Verfügbarkeit des Arbeits- und Festplattenspeichers durchgeführt werden, bevor die Bereitstellung zugelassen wird. Die Verwendung dieser Einstellung wird nicht empfohlen.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-insecure"></a>-insecure
Stellt Azure Kubernetes Service auf Azure Stack HCI-Komponenten (z. B. Cloud-Agent und Knoten-Agent) im ungeschützten Modus (keine durch TLS geschützte Verbindungen) bereit.  Wir raten davon ab, den ungeschützten Modus in Produktionsumgebungen zu nutzen.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-skipupdates"></a>-skipUpdates
Verwenden Sie dieses Flag, wenn Sie alle verfügbaren Updates überspringen möchten. Die Verwendung dieser Einstellung wird nicht empfohlen.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-forcednsreplication"></a>-forceDnsReplication
Die DNS-Replikation kann auf einigen Systemen bis zu eine Stunde in Anspruch nehmen. Dies führt zu einer langsamen Bereitstellung. Falls dieses Problem bei Ihnen auftritt, sehen Sie, dass „Install-AksHci“ in einer Schleife ausgeführt wird. Versuchen Sie, dieses Flag zu verwenden, um das Problem zu beheben. Es ist nicht sichergestellt, dass die Behebung mit dem Flag -forceDnsReplication erfolgreich ist. Falls für die Logik des Flags ein Fehler auftritt, ist der Fehler nicht sichtbar, und der Befehl wird so weiter ausgeführt, als ob das Flag nicht angegeben worden wäre.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-enablediagnosticdata"></a>-enableDiagnosticData`

Verwenden Sie dieses Flag, um einzuwilligen, dass erforderliche Diagnosedaten an Microsoft gesendet werden. Dies ist hilfreich, wenn Sie die Einwilligungsaufforderung überspringen möchten, die `Set-AksHciConfig` andernfalls anzeigt. `Install-AksHci` schlägt fehl, wenn Sie die Einwilligungsaufforderung nicht akzeptieren, wenn sie von `Set-AksHciConfig` angezeigt wird.
