---
title: 'Schnellstart: Einrichten eines Azure Kubernetes Service-Hosts für Azure Stack HCI mit Windows PowerShell'
description: Es wird beschrieben, wie Sie einen Azure Kubernetes Service-Host für Azure Stack HCI mit Windows PowerShell einrichten.
author: jessicaguan
ms.topic: quickstart
ms.date: 09/23/2020
ms.author: jeguan
ms.openlocfilehash: 089488e246bdb7c12bbd0808ef2e92a4c83b0fce
ms.sourcegitcommit: be445f183d003106192f039990d1fb8ee151c8d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253959"
---
# <a name="quickstart-set-up-an-azure-kubernetes-service-host-on-azure-stack-hci-using-powershell"></a>Schnellstart: Einrichten eines Azure Kubernetes Service-Hosts für Azure Stack HCI mit PowerShell

> Gilt für: Azure Stack HCI

In dieser Schnellstartanleitung wird beschrieben, wie Sie einen Azure Kubernetes Service-Host für Azure Stack HCI mit PowerShell einrichten. Falls Sie stattdessen Windows Admin Center verwenden möchten, finden Sie weitere Informationen unter [Schnellstart: Einrichten eines Azure Kubernetes Service-Hosts für Azure Stack HCI mit PowerShell](setup.md).

## <a name="before-you-begin"></a>Voraussetzungen

Stellen Sie zunächst sicher, dass Sie über einen Azure Stack HCI-Cluster mit zwei bis vier Knoten oder eine Azure Stack HCI-Instanz mit nur einem Knoten verfügen. **Wir empfehlen Ihnen, einen Azure Stack HCI-Cluster mit zwei bis vier Knoten zu verwenden.** Folgen Sie andernfalls der Anleitung auf der Seite [Registrierung für Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/).

## <a name="step-1-download-and-install-the-akshci-powershell-module"></a>Schritt 1: Herunterladen des PowerShell-Moduls „AksHci“

Laden Sie `AKS-HCI-Public=Preview-Oct-2020` von der [Registrierungsseite für Azure Kubernetes Service in Azure Stack HCI](https://aka.ms/AKS-HCI-Evaluate) herunter. Die ZIP-Datei `AksHci.Powershell.zip` enthält das PowerShell-Modul.

Wenn Sie den Azure Kubernetes Service bereits mit PowerShell oder dem Windows Admin Center in Azure Stack HCI installiert haben, führen Sie den folgenden Befehl aus, bevor Sie fortfahren.

   ```powershell
   Uninstall-AksHci
   ```

**Schließen Sie alle PowerShell-Fenster.** Löschen Sie alle vorhandenen Verzeichnisse für AksHci, AksHci.Day2 und MSK8sDownloadAgent, die sich unter dem Pfad `%systemdrive%\program files\windowspowershell\modules` befinden. Anschließend können Sie den Inhalt der neuen ZIP-Datei extrahieren. Stellen Sie sicher, dass Sie die ZIP-Datei am richtigen Speicherort (`%systemdrive%\program files\windowspowershell\modules`) extrahieren.

   ```powershell
   Import-Module AksHci
   ```

Schließen Sie nach dem Ausführen des obigen Befehls alle PowerShell-Fenster, und öffnen Sie erneut eine Sitzung mit Administratorrechten, um die Befehle in den folgenden Schritten auszuführen.

## <a name="step-2-prepare-your-machines-for-deployment"></a>Schritt 2: Vorbereiten Ihrer Computer für die Bereitstellung

Überprüfen Sie auf allen physischen Knoten, ob alle Anforderungen für die Installation von Azure Kubernetes Service unter Azure Stack HCI erfüllt sind.

Öffnen Sie PowerShell als Administrator, und führen Sie den folgenden Befehl aus.

   ```powershell
   Initialize-AksHciNode
   ```

Nach Abschluss der Überprüfungen wird die Meldung „Fertig“ in grüner Schrift angezeigt.

## <a name="step-3-configure-your-deployment"></a>Schritt 3: Konfigurieren Ihrer Bereitstellung

Legen Sie die Konfigurationseinstellungen für den Azure Kubernetes Service-Host fest. **Für einen Azure Stack HCI-Cluster mit zwei bis vier Knoten müssen Sie `MultiNode` in den Parametern `-deploymentType`, `wssdImageDir` und `cloudConfigLocation` angeben.** Bei einem Azure Stack HCI-Cluster mit nur einem Knoten sind alle Parameter optional und auf die Standardwerte festgelegt. Zur Erzielung einer optimalen Leistung **empfehlen wir Ihnen aber, die Bereitstellung eines Azure Stack HCI-Clusters mit zwei bis vier Knoten zu verwenden** .

Konfigurieren Sie Ihre Bereitstellung mit dem folgenden Befehl.

   ```powershell
   Set-AksHciConfig [-deploymentType {SingleNode, MultiNode}]
                    [-wssdImageDir]
                    [-cloudConfigLocation]
                    [-nodeConfigLocation]
                    [-vnetName]
                    [-controlPlaneVmSize]
                    [-loadBalancerVmSize]
                    [-sshPublicKey]
                    [-vipPoolStartIp]
                    [-vipPoolEndIp]
                    [-macPoolStart]
                    [-macPoolEnd]
                    [-vlanID]
                    [-cloudServiceCidr]
                    [-wssdDir]
                    [-akshciVersion]
                    [-vnetType]
                    [-nodeAgentPort]
                    [-nodeAgentAuthorizerPort]
                    [-clusterRoleName]
                    [-skipHostLimitChecks]
                    [-insecure]
                    [-skipUpdates]
                    [-forceDnsReplication]

   ```

### <a name="optional-parameters"></a>Optionale Parameter

`-deploymentType`

Der Bereitstellungstyp Zulässige Werte: „SingleNode“, „MultiNode“. Die Standardeinstellung ist „SingleNode“.

`-wssdImageDir`

Der Pfad zu dem Verzeichnis, in dem von Azure Kubernetes Service unter Azure Stack HCI die VHD-Images gespeichert werden. Für Bereitstellungen mit einem Knoten lautet er standardmäßig `%systemdrive%\wssdimagestore`. *Bei Bereitstellungen mit mehreren Knoten muss dieser Parameter angegeben werden* . Es muss ein Pfad zu einem freigegebenen Speicher, z. B.  `C:\ClusterStorage\Volume2\ImageStore`, oder zu einer SMB-Freigabe, z. B.  `\\FileShare\ImageStore`, angegeben werden.

`-cloudConfigLocation`

Der Speicherort, an dem vom Cloud-Agent die Konfiguration gespeichert wird. Für Bereitstellungen mit einem Knoten lautet er standardmäßig `%systemdrive%\wssdimagestore`. Der Speicherort kann dem obigen Pfad  `-wssdImageDir` entsprechen. Bei *Bereitstellungen mit mehreren Knoten muss dieser Parameter angegeben werden* .

`-nodeConfigLocation`

Der Speicherort, an dem von den Knoten-Agents die Konfiguration gespeichert wird. Dies muss ein lokaler Pfad sein.

`-vnetName`

Der Name des virtuellen Switches, mit dem die virtuellen Computer verbunden werden sollen. Die Standardeinstellung ist „External“. Falls der Switch nicht vorhanden ist, wird er erstellt.  

`-controlPlaneVmSize`

Die Größe des virtuellen Computers, der für die Steuerungsebene erstellt werden soll. Führen Sie `Get-AksHciVmSize` aus, um eine Liste mit den verfügbaren VM-Größen abzurufen.

`-loadBalancerVmSize`

Die Größe der VM, die für die Lastenausgleichs-VMs erstellt werden soll. Führen Sie `Get-AksHciVmSize` aus, um eine Liste mit den verfügbaren VM-Größen abzurufen.

`-sshPublicKey`

Pfad zu einer Datei mit einem öffentlichen SSH-Schlüssel. Mit diesem öffentlichen Schlüssel können Sie sich bei allen VMs anmelden, die von der Azure Kubernetes Service-Bereitstellung unter Azure Stack HCI erstellt werden. Wenn kein Schlüssel angegeben wird, wird unter  `%systemdrive%\Users\<username>\.ssh\id_rsa.pub` danach gesucht. Falls die Datei nicht vorhanden ist, wird am obigen Speicherort ein SSH-Schlüsselpaar generiert und dann verwendet.  

`-vipPoolStartIp`

Bei Verwendung von VIP-Pools für Ihre Bereitstellung wird mit diesem Parameter der Netzwerkstart des Pools angegeben. Standardwert ist „none“.

`-vipPoolEndIp`

Bei Verwendung von VIP-Pools für Ihre Bereitstellung wird mit diesem Parameter das Netzwerkende des Pools angegeben. Standardwert ist „none“.

`-macPoolStart`

Wird zum Angeben des Starts für die MAC-Adresse des MAC-Pools verwendet, den Sie für die Azure Kubernetes Service-Host-VM verwenden möchten. Bei der Syntax für die MAC-Adresse ist es erforderlich, dass das unwichtigste Bit des ersten Bytes immer „0“ ist, und das erste Byte sollte immer eine gerade Zahl sein (d. h. 00, 02, 04, 06...). Eine typische MAC-Adresse kann wie folgt aussehen: 02:1E:2B:78:00:00. Standardwert ist „none“.

`-macPoolEnd`

Wird zum Angeben des Endes für die MAC-Adresse des MAC-Pools verwendet, den Sie für die Azure Kubernetes Service-Host-VM verwenden möchten. Bei der Syntax für die MAC-Adresse ist es erforderlich, dass das unwichtigste Bit des ersten Bytes immer „0“ ist, und das erste Byte sollte immer eine gerade Zahl sein (d. h. 00, 02, 04, 06...). Das erste Byte der Adresse, die als `-macPoolEnd` übergeben wird, sollte dem ersten Byte der Adresse entsprechen, die als `-macPoolStart` übergeben wird. Standardwert ist „none“.

`-vlandID`

Dies kann verwendet werden, um eine Netzwerk-VLAN-ID anzugeben. Azure Kubernetes Service-Host und VM-Netzwerkadapter des Kubernetes-Clusters werden mit der angegebenen VLAN-ID gekennzeichnet. Standardwert ist „none“.

`cloudServiceCidr`

Dies kann verwendet werden, um statische IP/Netzwerkpräfix anzugeben, die dem MOC-CloudAgent-Dienst zugewiesen werden soll. Dieser Wert sollte im CIDR-Format angegeben werden. (Beispiel: 192.168.1.2/16) Standardwert ist „none“.

`-wssdDir`

Dies ist ein Arbeitsverzeichnis für das Modul, in dem kleine Dateien gespeichert werden. Die Standardeinstellung ist `%PROGRAMFILES%\AksHci`. Sie sollte für die meisten Bereitstellungen nicht geändert werden.  

`-akshciVersion`

Die Version von Azure Kubernetes Service unter Azure Stack HCI, die Sie bereitstellen möchten. Der Standardwert ist die neueste Version.

`-vnetType`

Der Typ des virtuellen Switches, mit dem die Verbindung hergestellt bzw. der erstellt wird. Standardmäßig wird der Switchtyp „External“ verwendet.

`-nodeAgentPort`

Die TCP/IP-Portnummer, über die „nodeagents“ lauschen sollen. Der Standardwert ist „45000“.  

`-nodeAgentAuthorizerPort`

Die TCP/IP-Portnummer, die von „nodeagents“ für ihren Autorisierungsport verwendet werden soll. Der Standardwert ist „45001“.  

`-clusterRoleName`

Hier wird der Name angegeben, der beim Erstellen eines „cloudagent“ als generischer Dienst im Cluster verwendet werden soll. Standardmäßig wird ein eindeutiger Name mit dem Präfix „ca-“ und einem GUID-Suffix (z. B. „ca-9e6eb299-bc0b-4f00-9fd7-942843820c26“) verwendet.

`-skipHostLimitChecks`

Fordert vom Skript das Überspringen aller Überprüfungen, die zum Bestätigen der Verfügbarkeit des Arbeits- und Festplattenspeichers durchgeführt werden, bevor die Bereitstellung zugelassen wird.

`-insecure`

Stellt Azure Kubernetes Service auf Azure Stack HCI-Komponenten, z. B. „cloudagent“ und „nodeagent(s)“, im ungeschützten Modus (ohne geschützte TLS-Verbindungen) bereit.  Wir raten Ihnen davon ab, den ungeschützten Modus in Produktionsumgebungen zu nutzen.

`-skipUpdates`

Verwenden Sie dieses Flag, wenn Sie alle verfügbaren Updates überspringen möchten.

`-forceDnsReplication`

Die DNS-Replikation kann auf einigen Systemen bis zu eine Stunde in Anspruch nehmen. Dies führt zu einer langsamen Bereitstellung. Falls dieses Problem bei Ihnen auftritt, sehen Sie, dass „Install-AksHci“ in einer Schleife ausgeführt wird. Versuchen Sie, dieses Flag zu verwenden, um das Problem zu beheben. Es ist nicht sichergestellt, dass die Behebung mit dem Flag `-forceDnsReplication` erfolgreich ist. Falls für die Logik des Flags ein Fehler auftritt, ist der Fehler nicht sichtbar, und der Befehl wird so weiter ausgeführt, als ob das Flag nicht angegeben worden wäre.

### <a name="reset-the-azure-kubernetes-service-on-azure-stack-hci-configuration"></a>Zurücksetzen von Azure Kubernetes Service in der Azure Stack HCI-Konfiguration

Führen Sie den folgenden Befehl aus, um Azure Kubernetes Service in der Azure Stack HCI-Konfiguration zurückzusetzen. Wenn Sie diesen Befehl allein ausführen, wird die Konfiguration auf die Standardwerte zurückgesetzt.

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

## <a name="step-5-access-your-clusters-using-kubectl"></a>Schritt 5: Zugreifen auf Ihre Cluster mit kubectl

Führen Sie den folgenden Befehl aus, um mit kubectl auf Ihren Azure Kubernetes Service-Host oder Kubernetes-Cluster zuzugreifen. Hierbei wird die kubeconfig-Datei des angegebenen Clusters als kubeconfig-Standarddatei für kubectl verwendet.

```powershell
Get-AksHciCredential -clusterName
                     [-outputLocation]
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

## <a name="reinstall-azure-kubernetes-service-on-azure-stack-hci"></a>Erneutes Installieren von Azure Kubernetes Service unter Azure Stack HCI

Beim erneuten Installieren von Azure Kubernetes Service unter Azure Stack HCI werden Ihre gesamten Kubernetes-Cluster (falls vorhanden) und der Azure Kubernetes Service-Host entfernt. Darüber hinaus werden auf den Knoten auch die Agents und Dienste von Azure Kubernetes Service unter Azure Stack HCI deinstalliert. Anschließend werden die ursprünglichen Schritte des Installationsvorgangs erneut ausgeführt, bis der Host neu erstellt wurde. Die Konfiguration für Azure Kubernetes Service unter Azure Stack HCI, die Sie mit `Set-AksHciConfig` vorgenommen haben, und die heruntergeladenen VHDX-Images werden beibehalten.

Führen Sie den folgenden Befehl aus, um Azure Kubernetes Service unter Azure Stack HCI erneut zu installieren.

```powershell
Restart-AksHci
```

## <a name="remove-azure-kubernetes-service-on-azure-stack-hci"></a>Entfernen von Azure Kubernetes Service unter Azure Stack HCI

Führen Sie den folgenden Befehl aus, um Azure Kubernetes Service unter Azure Stack HCI zu entfernen.

```powershell
Uninstall-AksHci
```

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen eines Kubernetes-Clusters für Ihre Anwendungen](create-kubernetes-cluster-powershell.md)
