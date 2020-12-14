---
title: 'Schnellstart: Einrichten eines Azure Kubernetes Service-Hosts für Azure Stack HCI mit Windows PowerShell'
description: Es wird beschrieben, wie Sie einen Azure Kubernetes Service-Host für Azure Stack HCI mit Windows PowerShell einrichten.
author: jessicaguan
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: jeguan
ms.openlocfilehash: 4211ec50ef0ea24ffb55f14791101c5d266ede2e
ms.sourcegitcommit: 0efffe1d04a54062a26d5c6ce31a417f511b9dbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96612555"
---
# <a name="quickstart-set-up-an-azure-kubernetes-service-host-on-azure-stack-hci-using-powershell"></a>Schnellstart: Einrichten eines Azure Kubernetes Service-Hosts für Azure Stack HCI mit PowerShell

> Gilt für: Azure Stack HCI, Windows Server 2019 Datacenter

In dieser Schnellstartanleitung wird beschrieben, wie Sie einen Azure Kubernetes Service-Host mit PowerShell einrichten. Falls Sie stattdessen Windows Admin Center verwenden möchten, finden Sie weitere Informationen unter [Schnellstart: Einrichten eines Azure Kubernetes Service-Hosts für Azure Stack HCI mit PowerShell](setup.md).

## <a name="before-you-begin"></a>Voraussetzungen

Stellen Sie sicher, dass Sie über eine der folgenden Installationen verfügen:
 - Azure Stack HCI-Cluster mit 2 bis 4 Knoten
 - Windows Server 2019 Datacenter-Failovercluster
 - Windows Server 2019 Datacenter mit einem einzelnen Knoten 
 
Stellen Sie auf der Seite [Systemanforderungen](.\system-requirements.md) zunächst sicher, dass alle Voraussetzungen erfüllt sind. 
**Wir empfehlen Ihnen, einen Azure Stack HCI-Cluster mit zwei bis vier Knoten zu verwenden.** Wenn Sie über keine der oben genannten Voraussetzungen verfügen, befolgen Sie die Anleitungen auf der Seite [Registrierung für Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/).    

   > [!IMPORTANT]
   > Wenn Sie Azure Kubernetes Service auf Azure Stack HCI entfernen, finden Sie weitere Informationen unter [Entfernen von Azure Kubernetes Service auf Azure Stack HCI](#remove-azure-kubernetes-service-on-azure-stack-hci). Befolgen Sie die dort genannten Anweisungen sorgfältig. 

## <a name="step-1-download-and-install-the-akshci-powershell-module"></a>Schritt 1: Herunterladen des PowerShell-Moduls „AksHci“

Laden Sie `AKS-HCI-Public-Preview-Dec-2020` von der [Registrierungsseite für Azure Kubernetes Service in Azure Stack HCI](https://aka.ms/AKS-HCI-Evaluate) herunter. Die ZIP-Datei `AksHci.Powershell.zip` enthält das PowerShell-Modul.

Wenn Sie Azure Kubernetes Service bereits mit PowerShell oder Windows Admin Center auf Azure Stack HCI installiert haben, gibt es zwei Installationsabläufe für das neue PowerShell-Modul:
 - Führen Sie eine Neuinstallation des PowerShell-Moduls durch, sodass Sie mit einem sauberen System beginnen und die zuvor bereitgestellten Workloads entfernt werden. Fahren Sie mit Schritt 1.1 fort, um eine Neuinstallation auszuführen.
 - Führen Sie ein Upgrade des PowerShell-Moduls aus, wenn Sie Ihr System und Ihre Workloads beibehalten möchten. Um ein Upgrade des PowerShell-Moduls auszuführen, fahren Sie mit Schritt 1.2 fort.

### <a name="step-11-clean-install-of-the-akshci-powershell-module"></a>Schritt 1.1: Neuinstallation des PowerShell-Moduls „AksHci“

Führen Sie den folgenden Befehl aus, bevor Sie fortfahren.
   ```powershell
   Uninstall-AksHci
   ```

**Schließen Sie alle PowerShell-Fenster.** Löschen Sie alle vorhandenen Verzeichnisse für AksHci.UI, MOC und MSK8sDownloadAgent, die sich im Pfad `%systemdrive%\program files\windowspowershell\modules` befinden. Nachdem die vorhandenen Verzeichnisse gelöscht wurden, können Sie den Inhalt der neuen ZIP-Datei extrahieren. Stellen Sie sicher, dass Sie die ZIP-Datei am richtigen Speicherort (`%systemdrive%\program files\windowspowershell\modules`) extrahieren. Führen Sie anschließend die folgenden Befehle aus.

   ```powershell
   Import-Module AksHci
   ```

Schließen Sie alle PowerShell-Fenster erneut, öffnen Sie eine administrative Sitzung erneut, und fahren Sie mit Schritt 1.3 fort: Überprüfen des aktualisierten PowerShell-Moduls,

### <a name="step-12-upgrade-the-akshci-powershell-module"></a>Schritt 1.2: Ausführen des Upgrades des PowerShell-Moduls „AksHci“

**Schließen Sie alle PowerShell-Fenster.** Löschen Sie alle vorhandenen Verzeichnisse für AksHci.UI, MOC und MSK8sDownloadAgent, die sich im Pfad `%systemdrive%\program files\windowspowershell\modules` befinden. Nachdem diese Verzeichnisse entfernt wurden, können Sie den Inhalt der neuen ZIP-Datei extrahieren. Stellen Sie sicher, dass Sie die ZIP-Datei am richtigen Speicherort (`%systemdrive%\program files\windowspowershell\modules`) extrahieren. Führen Sie anschließend die folgenden Befehle aus.

   ```powershell
   Import-Module AksHci
   ```
  
Schließen Sie nach dem Ausführen der obigen Befehle alle PowerShell-Fenster, und öffnen Sie erneut eine administrative Sitzung, um das Upgrade des PowerShell-Moduls unten beschrieben zu überprüfen, und führen Sie dann den Befehl `Update-AksHci` wie später im Dokument beschrieben aus.

## <a name="step-13-validate-upgraded-powershell-module"></a>Schritt 1.3: Überprüfen des aktualisierten PowerShell-Moduls

**Schließen Sie alle PowerShell-Fenster**, und öffnen Sie eine neue administrative Sitzung erneut, um zu überprüfen, ob Sie über die neueste Version des PowerShell-Moduls verfügen.  

   ```powershell
   Get-Command -Module AksHci
   ```
 
**Ausgabe:**
```
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Alias           Initialize-AksHciNode                              0.2.12     AksHci
Function        Get-AksHciCluster                                  0.2.12     AksHci
Function        Get-AksHciConfig                                   0.2.12     AksHci
Function        Get-AksHciCredential                               0.2.12     AksHci
Function        Get-AksHciKubernetesVersion                        0.2.12     AksHci
Function        Get-AksHciLogs                                     0.2.12     AksHci
Function        Get-AksHciUpdates                                  0.2.12     AksHci
Function        Get-AksHciVersion                                  0.2.12     AksHci
Function        Get-AksHciVmSize                                   0.2.12     AksHci
Function        Install-AksHci                                     0.2.12     AksHci
Function        Install-AksHciAdAuth                               0.2.12     AksHci
Function        Install-AksHciArcOnboarding                        0.2.12     AksHci
Function        New-AksHciCluster                                  0.2.12     AksHci
Function        Remove-AksHciCluster                               0.2.12     AksHci
Function        Restart-AksHci                                     0.2.12     AksHci
Function        Set-AksHciClusterNodeCount                         0.2.12     AksHci
Function        Set-AksHciConfig                                   0.2.12     AksHci
Function        Uninstall-AksHci                                   0.2.12     AksHci
Function        Uninstall-AksHciAdAuth                             0.2.12     AksHci
Function        Uninstall-AksHciArcOnboarding                      0.2.12     AksHci
Function        Update-AksHci                                      0.2.12     AksHci
Function        Update-AksHciCluster                               0.2.12     AksHci
```

## <a name="step-2-prepare-your-machines-for-deployment"></a>Schritt 2: Vorbereiten Ihrer Computer für die Bereitstellung

Überprüfen Sie auf allen physischen Knoten, ob alle Anforderungen für die Installation von Azure Kubernetes Service unter Azure Stack HCI erfüllt sind.

Öffnen Sie PowerShell als Administrator, und führen Sie den folgenden Befehl aus.

   ```powershell
   Initialize-AksHciNode
   ```

Nach Abschluss der Überprüfungen wird die Meldung „Fertig“ in grüner Schrift angezeigt.

## <a name="step-3-configure-your-deployment"></a>Schritt 3: Konfigurieren Ihrer Bereitstellung

Legen Sie die Konfigurationseinstellungen für den Azure Kubernetes Service-Host fest. **Wenn Sie die Bereitstellung auf einem Azure Stack HCI-Cluster mit 2 bis 4 Knoten oder einem Windows Server 2019 Datacenter Failovercluster vornehmen, müssen Sie die Parameter `imageDir` und `cloudConfigLocation` angeben.** Unter Windows Server 2019 Datacenter mit einem einzelnen Knoten sind alle Parameter optional und auf die Standardwerte festgelegt. Zur Erzielung einer optimalen Leistung **empfehlen wir Ihnen aber, die Bereitstellung eines Azure Stack HCI-Clusters mit zwei bis vier Knoten zu verwenden**.

Konfigurieren Sie Ihre Bereitstellung mit dem folgenden Befehl.

   ```powershell
   Set-AksHciConfig [-imageDir <String>]
                    [-cloudConfigLocation <String>]
                    [-nodeConfigLocation <String>]
                    [-vnetName <String>]
                    [-controlPlaneVmSize <VmSize>]
                    [-loadBalancerVmSize <VmSize>]
                    [-sshPublicKey <String>]
                    [-vipPoolStartIp <String>]
                    [-vipPoolEndIp <String>]
                    [-macPoolStart <String>]
                    [-macPoolEnd <String>]
                    [-vlanID <int>]
                    [-kvaLoadBalancerType {unstacked_haproxy, stacked_kube_vip}]
                    [-kvaControlPlaneEndpoint <String>]
                    [-proxyServerHTTP <String>]
                    [-proxyServerHTTP <String>]
                    [-proxyServerNoProxy <String>]
                    [-proxyServerCredential <PSCredential>]
                    [-cloudServiceCidr <String>]
                    [-workingDir <String>]
                    [-version <String>]
                    [-vnetType <String>]
                    [-nodeAgentPort <int>]
                    [-nodeAgentAuthorizerPort <int>]
                    [-clusterRoleName <String>]
                    [-cloudLocation <String>]
                    [-skipHostLimitChecks]
                    [-insecure]
                    [-skipUpdates]
                    [-forceDnsReplication]

   ```

### <a name="example"></a>Beispiel

So stellen Sie auf einem Cluster mit 2 bis 4 Knoten mit einem DHCP-Netzwerk bereit:

   ```powershell
   Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config
   ```

So stellen Sie mit einem virtuellen IP-Pool bereit:

   ```powershell
   Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -vipPoolStartIp 10.0.0.20 -vipPoolEndIp 10.0.0.80
   ```

So stellen Sie mit einem `stacked_kube_vip`-Lastenausgleich bereit:

   ```powershell
   Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -kvaLoadBalancerType stacked_kube_vip -kvaControlPlaneEndpoint 10.0.1.10
   ```

So stellen Sie mit einem Proxyserver bereit:

   ```powershell
   Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -proxyServerHttp "http://proxy.contoso.com:8888" -proxyServerHttps "http://proxy.contoso.com:8888" -proxyServerNoProxy "localhost,127.0.0.1,.svc,10.96.0.0/12,10.244.0.0/16,10.231.110.0/24,10.68.237.0/24" -proxyServerCredential $credential
   ```  

### <a name="optional-parameters"></a>Optionale Parameter

`-imageDir`

Der Pfad zu dem Verzeichnis, in dem von Azure Kubernetes Service unter Azure Stack HCI die VHD-Images gespeichert werden. Für Bereitstellungen mit einem Knoten lautet er standardmäßig `%systemdrive%\AksHciImageStore`. *Bei Bereitstellungen mit mehreren Knoten muss dieser Parameter angegeben werden*. Es muss ein Pfad zu einem freigegebenen Speicher, z. B.  `C:\ClusterStorage\Volume2\ImageStore`, oder zu einer SMB-Freigabe, z. B.  `\\FileShare\ImageStore`, angegeben werden.

`-cloudConfigLocation`

Der Speicherort, an dem vom Cloud-Agent die Konfiguration gespeichert wird. Für Bereitstellungen mit einem Knoten lautet er standardmäßig `%systemdrive%\wssdcloudagent`. Der Speicherort kann dem obigen Pfad  `-imageDir` entsprechen. Bei *Bereitstellungen mit mehreren Knoten muss dieser Parameter angegeben werden*. Es muss ein Pfad zu einem freigegebenen Speicher, z. B.  `C:\ClusterStorage\Volume2\ImageStore`, oder zu einer SMB-Freigabe, z. B.  `\\FileShare\ImageStore`, angegeben werden. Der Speicherort muss sich auf einer hochverfügbaren Freigabe befinden, damit der Speicher immer zugänglich ist.

`-nodeConfigLocation`

Der Speicherort, an dem von den Knoten-Agents die Konfiguration gespeichert wird. Jeder Knoten verfügt über einen Knoten-Agent, sodass seine Konfiguration lokal ist. Dieser Speicherort muss ein lokaler Pfad sein. Der Standardwert ist für alle Bereitstellungen `%systemdrive%\programdata\wssdagent`.

`-vnetName`

Der Name des virtuellen Switches, mit dem die virtuellen Computer verbunden werden sollen. Wenn Sie bereits über einen externen Switch auf dem Host verfügen, sollten Sie hier den Namen des Switches übergeben. Falls der Switch nicht vorhanden ist, wird er erstellt.Die Standardeinstellung ist „External“.  

`-controlPlaneVmSize`

Die Größe des virtuellen Computers, der für die Steuerungsebene erstellt werden soll. Führen Sie `Get-AksHciVmSize` aus, um eine Liste mit den verfügbaren VM-Größen abzurufen.

`-loadBalancerVmSize`

Die Größe der VM, die für die Lastenausgleichs-VMs erstellt werden soll. Führen Sie `Get-AksHciVmSize` aus, um eine Liste mit den verfügbaren VM-Größen abzurufen.

`-sshPublicKey`

Pfad zu einer Datei mit einem öffentlichen SSH-Schlüssel. Mit diesem öffentlichen Schlüssel können Sie sich bei allen VMs anmelden, die von der Azure Kubernetes Service-Bereitstellung unter Azure Stack HCI erstellt werden. Wenn Sie über einen eigenen öffentlichen SSH-Schlüssel verfügen, übergeben Sie seinen Speicherort hier. Wenn kein Schlüssel angegeben wird, wird unter `%systemdrive%\akshci\.ssh\akshci_rsa.pub` danach gesucht. Wenn die Datei nicht vorhanden ist, wird am obigen Speicherort ein SSH-Schlüsselpaar generiert und dann verwendet.

`-vipPoolStartIp`

Bei Verwendung von VIP-Pools für Ihre Bereitstellung wird mit diesem Parameter der Netzwerkstart des Pools angegeben. Sie sollten VIP-Pools für langlebige Bereitstellungen verwenden, um sicherzustellen, dass ein Pool von IP-Adressen konsistent bleibt. Dies ist nützlich, wenn Sie über Workloads verfügen, die immer erreichbar sein müssen. Standardwert ist „none“.

`-vipPoolEndIp`

Bei Verwendung von VIP-Pools für Ihre Bereitstellung wird mit diesem Parameter das Netzwerkende des Pools angegeben. Sie sollten VIP-Pools für langlebige Bereitstellungen verwenden, um sicherzustellen, dass ein Pool von IP-Adressen konsistent bleibt. Dies ist nützlich, wenn Sie über Workloads verfügen, die immer erreichbar sein müssen. Standardwert ist „none“.

`-macPoolStart` 

Wird zum Angeben des Starts für die MAC-Adresse des MAC-Pools verwendet, den Sie für die Azure Kubernetes Service-Host-VM verwenden möchten. Bei der Syntax für die MAC-Adresse ist es erforderlich, dass das unwichtigste Bit des ersten Bytes immer „0“ ist, und das erste Byte sollte immer eine gerade Zahl sein (d. h. 00, 02, 04, 06...). Eine typische MAC-Adresse kann wie folgt aussehen: 02:1E:2B:78:00:00. Verwenden Sie MAC-Pools für langlebige Bereitstellungen, damit die zugewiesenen MAC-Adressen konsistent sind. Dies ist hilfreich, wenn eine Anforderung vorhanden ist, dass die VMs über bestimmte MAC-Adressen verfügen. Standardwert ist „none“.

`-macPoolEnd`

Wird zum Angeben des Endes für die MAC-Adresse des MAC-Pools verwendet, den Sie für die Azure Kubernetes Service-Host-VM verwenden möchten. Bei der Syntax für die MAC-Adresse ist es erforderlich, dass das unwichtigste Bit des ersten Bytes immer „0“ ist, und das erste Byte sollte immer eine gerade Zahl sein (d. h. 00, 02, 04, 06...). Das erste Byte der Adresse, die als `-macPoolEnd` übergeben wird, sollte dem ersten Byte der Adresse entsprechen, die als `-macPoolStart` übergeben wird. Verwenden Sie MAC-Pools für langlebige Bereitstellungen, damit die zugewiesenen MAC-Adressen konsistent sind. Dies ist hilfreich, wenn eine Anforderung vorhanden ist, dass die VMs über bestimmte MAC-Adressen verfügen. Standardwert ist „none“.

`-vlandID`

Dies kann verwendet werden, um eine Netzwerk-VLAN-ID anzugeben. Azure Kubernetes Service-Host und VM-Netzwerkadapter des Kubernetes-Clusters werden mit der angegebenen VLAN-ID gekennzeichnet. Diese sollte verwendet werden, wenn eine bestimmte VLAN-ID für die richtige Konnektivität gekennzeichnet werden muss. Standardwert ist „none“.

`-kvaLoadBalancerType`

Dies nimmt `unstacked_haproxy` oder `stacked_kube_vip` an. `unstacked_haproxy` ist die Standardeinstellung, wenn eine separate VM für den Lastenausgleich mit HAProxy als Endpunkt des API-Servers des Azure Kubernetes Service-Hosts bereitgestellt wird. `stacked_kube_vip` ist eine Lastenausgleichslösung ([Kubevip](https://kube-vip.io/)) für den Azure Kubernetes Service-Host. Hiermit können Sie eine statische IP-Adresse auf dem Host als Floating-IP über die Knoten der Steuerungsebene hinweg festlegen, um den API-Server über die IP hochverfügbar zu halten. Wenn diese Option ausgewählt wird, müssen sie die statische IP-Adresse im Parameter `kvaControlPlaneEndpoint` angeben, und es wird keine separate Lastenausgleichs-VM bereitgestellt.

`stacked_kube_vip` erfordert eine IP-Adresse und ist ressourcenfreundlicher, indem Arbeitsspeicher, CPU und Bereitstellungszeit gespart werden. Wenn Sie nicht über eine IP-Adresse verfügen, die als Floating-IP verwendet werden kann, sollten Sie `unstacked_haproxy` verwenden. Die letztgenannte Option erfordert eine Lastenausgleichs-VM. 

`-kvaControlPlaneEndpoint`

Hiermit wird die statische IP-Adresse angegeben, die als Adresse des API-Servers des Azure Kubernetes Service-Hosts verwendet werden soll, wenn der Parameter `kvaLoadBalancerType` auf `stacked_kube_vip` festgelegt ist. Wenn `stacked_kube_vip` verwendet wird, muss dieser Parameter angegeben werden.

`-proxyServerHTTP`

Dadurch wird ein Proxyserver-URI bereitgestellt, der von allen Komponenten verwendet werden muss, die HTTP-Endpunkte erreichen müssen. Das URI-Format enthält das URI-Schema, die Serveradresse und den Port (d. h. https://server.com:8888) ). Standardwert ist „none“.

`-proxyServerHTTPS`

Dadurch wird ein Proxyserver-URI bereitgestellt, der von allen Komponenten verwendet werden muss, die HTTPS-Endpunkte erreichen müssen. Das URI-Format enthält das URI-Schema, die Serveradresse und den Port (d. h. https://server.com:8888) ). Standardwert ist „none“.

`-proxyServerNoProxy`

Dies ist eine durch Trennzeichen getrennte Zeichenfolge von Adressen, die vom Proxy ausgenommen werden. Der Standardwert ist `localhost,127.0.0.1,.svc,10.96.0.0/12,10.244.0.0/16`. Dies schließt den localhost-Datenverkehr (localhost, 127.0.0.1), den internen Kubernetes Service-Datenverkehr (.svc), die Kubernetes Service-CIDR (10.96.0.0/12) und die Kubernetes POD-CIDR (10.244.0.0/16) vom Proxyserver aus. Mit diesem Parameter können Sie weitere Subnetzbereiche oder Namensausnahmen hinzufügen. **Die Einstellungen für diesen Parameter sind sehr wichtig, denn wenn er nicht ordnungsgemäß konfiguriert ist, können Sie ggf. den internen Datenverkehr des Kubernetes-Clusters unerwarteterweise an Ihren Proxy weiterleiten. Dies kann zu verschiedenen Fehlern bei der Netzwerkkommunikation führen.**


`-proxyServerCredential`

Hiermit werden der Benutzername und das Kennwort für die Authentifizierung bei den HTTP-/HTTPS-Proxyservern bereitstellt. Sie können `Get-Credential` verwenden, um ein `PSCredential`-Objekt zu generieren, das an diesen Parameter übergeben wird. Standardwert ist „none“.

`-cloudServiceCidr`

Dies kann verwendet werden, um statische IP/Netzwerkpräfix anzugeben, die dem MOC-CloudAgent-Dienst zugewiesen werden soll. Dieser Wert sollte im CIDR-Format angegeben werden. (Beispiel: 192.168.1.2/16) Sie können dies angeben, um sicherzustellen, dass alles Wichtige im Netzwerk immer zugänglich ist, da sich die IP-Adresse nicht ändert. Standardwert ist „none“.

`-workingDir`

Dies ist ein Arbeitsverzeichnis für das Modul, in dem kleine Dateien gespeichert werden. Die Standardeinstellung ist `%PROGRAMFILES%\AksHci`. Sie sollte für die meisten Bereitstellungen nicht geändert werden. Es wird nicht empfohlen, den Standardwert zu ändern. 

`-version`

Die Version von Azure Kubernetes Service unter Azure Stack HCI, die Sie bereitstellen möchten. Der Standardwert ist die neueste Version. Es wird nicht empfohlen, den Standardwert zu ändern.

`-vnetType`

Der Typ des virtuellen Switches, mit dem die Verbindung hergestellt bzw. der erstellt wird. Standardmäßig wird der Switchtyp „External“ verwendet. Es wird nicht empfohlen, den Standardwert zu ändern.

`-nodeAgentPort`

Die TCP/IP-Portnummer, über die Knoten-Agents lauschen sollen. Der Standardwert ist „45000“. Es wird nicht empfohlen, den Standardwert zu ändern. 

`-nodeAgentAuthorizerPort`

Die TCP/IP-Portnummer, die von Knoten-Agents für ihren Autorisierungsport verwendet werden soll. Der Standardwert ist „45001“. Es wird nicht empfohlen, den Standardwert zu ändern.  

`-clusterRoleName`

Hier wird der Name angegeben, der beim Erstellen eines Knoten-Agents als generischer Dienst im Cluster verwendet werden soll. Standardmäßig wird ein eindeutiger Name mit dem Präfix „ca-“ und einem GUID-Suffix (z. B. „ca-9e6eb299-bc0b-4f00-9fd7-942843820c26“) verwendet. Es wird nicht empfohlen, den Standardwert zu ändern.

`-cloudLocation` 

Dieser Parameter stellt einen benutzerdefinierten, von Microsoft betriebenen Cloudspeicherortnamen bereit. Der Standardname lautet „MocLocation“. Es wird nicht empfohlen, den Standardwert zu ändern.

`-skipHostLimitChecks`

Fordert vom Skript das Überspringen aller Überprüfungen, die zum Bestätigen der Verfügbarkeit des Arbeits- und Festplattenspeichers durchgeführt werden, bevor die Bereitstellung zugelassen wird. Die Verwendung dieser Einstellung wird nicht empfohlen.

`-insecure`

Stellt Azure Kubernetes Service auf Azure Stack HCI-Komponenten (z. B. Cloud-Agent und Knoten-Agent) im ungeschützten Modus (keine durch TLS geschützte Verbindungen) bereit.  Wir raten davon ab, den ungeschützten Modus in Produktionsumgebungen zu nutzen.

`-skipUpdates`

Verwenden Sie dieses Flag, wenn Sie alle verfügbaren Updates überspringen möchten. Die Verwendung dieser Einstellung wird nicht empfohlen.

`-forceDnsReplication`

Die DNS-Replikation kann auf einigen Systemen bis zu eine Stunde in Anspruch nehmen. Dies führt zu einer langsamen Bereitstellung. Falls dieses Problem bei Ihnen auftritt, sehen Sie, dass „Install-AksHci“ in einer Schleife ausgeführt wird. Versuchen Sie, dieses Flag zu verwenden, um das Problem zu beheben. Es ist nicht sichergestellt, dass die Behebung mit dem Flag `-forceDnsReplication` erfolgreich ist. Falls für die Logik des Flags ein Fehler auftritt, ist der Fehler nicht sichtbar, und der Befehl wird so weiter ausgeführt, als ob das Flag nicht angegeben worden wäre. 

### <a name="reset-the-azure-kubernetes-service-on-azure-stack-hci-configuration"></a>Zurücksetzen von Azure Kubernetes Service in der Azure Stack HCI-Konfiguration

Führen Sie die folgenden Befehle aus, um Azure Kubernetes Service in der Azure Stack HCI-Konfiguration zurückzusetzen. Wenn Sie diesen Befehl allein ausführen, wird die Konfiguration auf die Standardwerte zurückgesetzt.

```powershell
Set-AksHciConfig
```

## <a name="step-4-start-a-new-deployment"></a>Schritt 4: Starten einer neuen Bereitstellung

Nachdem Sie Ihre Bereitstellung konfiguriert haben, müssen Sie sie starten. Azure Kubernetes Service wird für die Azure Stack HCI-Agents/-Dienste und auf dem Azure Kubernetes Service-Host installiert.

Führen Sie den folgenden Befehl aus, um die Bereitstellung zu starten.

```powershell
Install-AksHci
```

### <a name="verify-your-deployed-azure-kubernetes-service-host"></a>Überprüfen des bereitgestellten Azure Kubernetes Service-Hosts

Führen Sie den folgenden Befehl aus, um sicherzustellen, dass der Azure Kubernetes Service-Host bereitgestellt wurde. Mit diesem Befehl können Sie Kubernetes-Cluster auch nach deren Bereitstellung abrufen.

```powershell
Get-AksHciCluster
```

**Ausgabe:**
```

Name            : clustergroup-management
Version         : v1.18.8
Control Planes  : 1
Linux Workers   : 0
Windows Workers : 0
Phase           : provisioned
Ready           : True
```

## <a name="step-5-access-your-clusters-using-kubectl"></a>Schritt 5: Zugreifen auf Ihre Cluster mit kubectl

Führen Sie den folgenden Befehl aus, um mit kubectl auf Ihren Azure Kubernetes Service-Host oder Kubernetes-Cluster zuzugreifen. Hierbei wird die kubeconfig-Datei des angegebenen Clusters als kubeconfig-Standarddatei für kubectl verwendet.

```powershell
Get-AksHciCredential -clusterName <String>
                     [-outputLocation <String>]
```

### <a name="example"></a>Beispiel

```powershell
Get-AksHciCredential -clusterName clustergroup-management
```

### <a name="required-parameters"></a>Erforderliche Parameter

`clusterName`

Der Name des Clusters.

### <a name="optional-parameters"></a>Optionale Parameter

`outputLocation`

Der Speicherort, an den kubeconfig heruntergeladen werden soll. Der Standardwert ist `%USERPROFILE%\.kube`.

## <a name="get-logs"></a>Abrufen von Protokollen

Führen Sie den folgenden Befehl aus, um die Protokolle Ihrer gesamten Pods zu erhalten. Mit diesem Befehl wird der gezippte Ausgabeordner `akshcilogs` unter dem Pfad `C:\wssd\akshcilogs` erstellt.

```powershell
Get-AksHciLogs
```

## <a name="update-to-the-latest-version-of-azure-kubernetes-service-on-azure-stack-hci"></a>Update auf die neueste Version von Azure Kubernetes Service auf Azure Stack HCI

Um ein Update auf die neueste Version von Azure Kubernetes Service auf Azure Stack HCI auszuführen, führen Sie den folgenden Befehl aus. Der Updatebefehl funktioniert nur, wenn das Release aus Oktober installiert ist. Er funktioniert nicht für Releases, die älter als das Release aus Oktober sind. Dieser Updatebefehl aktualisiert den Azure Kubernetes Service-Host und die lokale von Microsoft betriebene Cloudplattform. Für diese Vorschauversion bleiben die Kubernetes-Version und die Betriebssystemversion des AKS-Hosts unverändert. Dieser Befehl führt kein Upgrade für vorhandene Workloadcluster durch. Neue, nach dem Aktualisieren des AKS-Hosts erstellte Workloadcluster unterscheiden sich in Bezug auf die Betriebssystemversion des Windows-Knotens und die Kubernetes-Version von vorhandenen Workloadclustern.

   ```powershell
   Update-AksHci
   ```
   
Es wird empfohlen, Workloadcluster sofort nach dem Aktualisieren des Verwaltungsclusters zu aktualisieren, um zu verhindern, dass nicht unterstützte Windows Server-Betriebssystemversionen in Ihren Kubernetes-Clustern mit Windows-Knoten ausgeführt werden. Informationen zum Aktualisieren des Workloadclusters finden Sie unter [Aktualisieren Ihres Workloadclusters](create-kubernetes-cluster-powershell.md).

## <a name="restart-azure-kubernetes-service-on-azure-stack-hci"></a>Neustarten von Azure Kubernetes Service auf Azure Stack HCI

Beim Neustarten von Azure Kubernetes Service auf Azure Stack HCI werden Ihre gesamten Kubernetes-Cluster (falls vorhanden) und der Azure Kubernetes Service-Host entfernt. Darüber hinaus werden auf den Knoten auch die Agents und Dienste von Azure Kubernetes Service unter Azure Stack HCI deinstalliert. Anschließend werden die ursprünglichen Schritte des Installationsvorgangs erneut ausgeführt, bis der Host neu erstellt wurde. Die Konfiguration für Azure Kubernetes Service unter Azure Stack HCI, die Sie mit `Set-AksHciConfig` vorgenommen haben, und die heruntergeladenen VHDX-Images werden beibehalten.

Führen Sie den folgenden Befehl aus, um Azure Kubernetes Service auf Azure Stack HCI mit den gleichen Konfigurationseinstellungen neu zu starten.

```powershell
Restart-AksHci
```

## <a name="reset-configuration-settings-and-reinstall-azure-kubernetes-service-on-azure-stack-hci"></a>Zurücksetzen der Konfigurationseinstellungen und erneutes Installieren von Azure Kubernetes Service auf Azure Stack HCI

Führen Sie zunächst den folgenden Befehl aus, um Azure Kubernetes Service auf Azure Stack HCI mit anderen Konfigurationseinstellungen erneut zu installieren.

```powershell
Uninstall-AksHci
```

Nachdem Sie den obigen Befehl ausgeführt haben, können Sie die Konfigurationseinstellungen mit dem folgenden Befehl ändern. Die Parameter bleiben wie in Schritt 3 beschrieben unverändert. Wenn Sie diesen Befehl ohne angegebene Parameter ausführen, werden die Parameter auf ihre Standardwerte zurückgesetzt.

```powershell
Set-AksHciConfig
```

Nachdem Sie die Konfiguration in die gewünschten Einstellungen geändert haben, führen Sie den folgenden Befehl aus, um Azure Stack Kubernetes unter Azure Stack HCI erneut zu installieren.

```powershell
Install-AksHci
```

## <a name="remove-azure-kubernetes-service-on-azure-stack-hci"></a>Entfernen von Azure Kubernetes Service unter Azure Stack HCI

Führen Sie den folgenden Befehl aus, um Azure Kubernetes Service unter Azure Stack HCI zu entfernen. **Wenn Sie PowerShell verwenden, um eine Windows Admin Center-Bereitstellung zu deinstallieren, müssen Sie den Befehl mit dem Flag `-Force` ausführen.**

```powershell
Uninstall-AksHci
```

Nachdem Sie den obigen Befehl ausgeführt haben, können Sie den `Install-AksHci`-Befehl ausführen, um den Azure Kubernetes Service-Host mit der gleichen Konfiguration wie zuvor zu installieren. Wenn Sie die Konfiguration ändern möchten, führen Sie `Set-AksHciConfig` mit den Änderungen aus, die Sie vornehmen möchten, bevor Sie den Installationsbefehl ausführen.

Wenn Sie die alte Konfiguration nicht beibehalten möchten, führen Sie den folgenden Befehl aus.

```powershell
Uninstall-AksHci -Force
```

Wenn PowerShell-Befehle in einem Cluster ausgeführt werden, auf dem zuvor Windows Admin Center für die Bereitstellung verwendet wurde, überprüft das PowerShell-Modul, ob die Windows Admin Center-Konfigurationsdatei vorhanden ist. Windows Admin Center platziert die Windows Admin Center-Konfigurationsdatei knotenübergreifend. **Wenn Sie den Deinstallationsbefehl verwenden und zurück zu Windows Admin Center wechseln, führen Sie den obigen Deinstallationsbefehl mit dem Flag `-Force` aus. Wenn dies nicht geschieht, sind PowerShell und Windows Admin Center nicht mehr synchron.**

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen eines Kubernetes-Clusters für Ihre Anwendungen](create-kubernetes-cluster-powershell.md)
