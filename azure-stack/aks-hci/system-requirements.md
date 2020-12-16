---
title: Anforderungen an Azure Kubernetes Service in Azure Stack HCI
description: Voraussetzungen von Azure Kubernetes Service in Azure Stack HCI
ms.topic: conceptual
author: abhilashaagarwala
ms.author: abha
ms.date: 12/02/2020
ms.openlocfilehash: 2bbd32a8117955a18c525a4a0483d152c5a3ed0c
ms.sourcegitcommit: 0efffe1d04a54062a26d5c6ce31a417f511b9dbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96612470"
---
# <a name="system-requirements-for-azure-kubernetes-service-on-azure-stack-hci"></a>Systemanforderungen für Azure Kubernetes Service in Azure Stack HCI

> Gilt für: Azure Stack HCI, Windows Server 2019 Datacenter

In diesem Artikel werden die Voraussetzungen für die Einrichtung von Azure Kubernetes Service in Azure Stack HCI oder in Windows Server 2019 Datacenter und dessen Verwendung zum Erstellen von Kubernetes-Clustern behandelt. Eine Übersicht über Azure Kubernetes Service in Azure Stack HCI finden Sie unter [Übersicht über AKS in Azure Stack HCI](overview.md).

## <a name="determine-hardware-requirements"></a>Ermitteln der Hardwareanforderungen

Microsoft empfiehlt den Erwerb einer validierten Azure Stack HCI-Hardware-/Softwarelösung von unseren Partnern. Diese Lösungen wurden anhand unserer Referenzarchitektur entworfen, zusammengestellt und validiert und garantieren Kompatibilität und Zuverlässigkeit, sodass Sie sofort loslegen können. Überprüfen Sie, ob die von Ihnen verwendeten Systeme, Komponenten, Geräte und Treiber gemäß Windows Server Catalog für Windows Server 2019 zertifiziert sind. Auf der Website [Azure Stack HCI](https://azure.microsoft.com/overview/azure-stack/hci) finden Sie validierte Lösungen.

## <a name="general-requirements"></a>Allgemeine Anforderungen

Damit Azure Kubernetes Service in Azure Stack HCI oder Windows Server 2019 Datacenter in einer Active Directory-Umgebung optimal funktioniert, müssen die folgenden Anforderungen erfüllt sein: 

 - Stellen Sie sicher, dass die Zeitsynchronisierung eingerichtet ist und die Abweichung zwischen allen Clusterknoten und dem Domänencontroller nicht mehr als zwei Minuten beträgt. Informationen zum Festlegen der Zeitsynchronisierung finden Sie unter [Windows-Zeitdienst](/windows-server/networking/windows-time-service/windows-time-service-top). 

 - Stellen Sie sicher, dass die Benutzerkonten, die Azure Kubernetes Service auf Azure Stack HCI- oder Windows Server 2019 Datacenter-Clustern hinzufügen, aktualisieren und verwalten, über die richtigen Berechtigungen in Active Directory verfügen. Wenn Sie Organisationseinheiten verwenden, um Gruppenrichtlinien für Server und Dienste zu verwalten, benötigen die Benutzerkonten Berechtigungen zum Auflisten, Lesen, Ändern und Löschen aller Objekte in der Organisationseinheit. 

 - Es wird empfohlen, für die Server und Dienste, zu denen Sie Ihren Azure Kubernetes Service auf Azure Stack HCI- oder Windows Server 2019 Datacenter-Cluster hinzufügen, eine separate Organisationseinheit zu verwenden. Dadurch können Sie den Zugriff und die Berechtigungen differenzierter steuern.

 - Wenn Sie GPO-Vorlagen für Container in Active Directory verwenden, stellen Sie sicher, dass die Bereitstellung von AKS-HCI von dieser Richtlinie ausgenommen ist. Die Serverhärtung wird in einer späteren Vorschauversion verfügbar sein.

## <a name="compute-requirements"></a>Computeanforderungen

 - Ein Azure Stack HCI-Cluster oder Windows Server 2019 Datacenter-Failovercluster mit maximal vier Servern im Cluster. Es wird empfohlen, dass jeder Server im Cluster über mindestens 24 CPU-Kerne und mindestens 256 GB RAM verfügt.

 - Obwohl Sie Azure Kubernetes Service technisch gesehen in Windows Server 2019 Datacenter mit einem einzelnen Knoten ausführen können, wird von dieser Vorgehensweise abgeraten. Sie können jedoch Azure Kubernetes Service zu Evaluierungszwecken in Windows Server 2019 Datacenter mit einem einzelnen Knoten ausführen.

 - Andere Computeanforderungen für Azure Kubernetes Service in Azure Stack HCI entsprechen den Anforderungen von Azure Stack HCI. Ausführliche Informationen zu den Azure Stack HCI-Serveranforderungen finden Sie unter [Azure Stack HCI-Systemanforderungen](../hci/concepts/system-requirements.md#server-requirements).

 - Diese Vorschauversion erfordert, dass Sie das Azure Stack HCI-Betriebssystem auf jedem Server im Cluster unter Verwendung der EN-US-Region und der Sprachauswahl installieren. Eine Änderung nach der Installation ist zu diesem Zeitpunkt nicht ausreichend.

## <a name="network-requirements"></a>Netzwerkanforderungen 

Für Azure Stack HCI-Cluster und für Windows Server 2019 Datacenter-Failovercluster gelten die folgenden Anforderungen: 

 - Stellen Sie sicher, dass ein vorhandener, externer virtueller Switch konfiguriert ist, wenn Sie das Windows Admin Center verwenden. Bei Azure Stack HCI-Clustern müssen dieser Switch und sein Name auf allen Clusterknoten identisch sein. 

 - Vergewissern Sie sich, dass Sie IPv6 auf allen Netzwerkadaptern deaktiviert haben. 

 - Das Netzwerk muss über einen verfügbaren DHCP-Server verfügen, um den VMs und VM-Hosts entsprechende TCP/IP-Adressen bereitzustellen. Der DHCP-Server sollte außerdem NTP- und DNS-Hostinformationen enthalten. 

 - Außerdem wird empfohlen, einen DHCP-Server mit einem dedizierten Bereich von IPv4-Adressen einzurichten, auf den über den Azure Stack HCI-Cluster zugegriffen werden kann. Sie können z. B. 10.0.1.1 für das Standardgateway, 10.0.1.2 bis 10.0.1.102 für Kubernetes-Dienste reservieren (mit „-vipPoolStartIp“ und „-vipPoolEndIp“ in „Set-AksHciConfig“) und 10.0.1.103 bis 10.0.1.254 für virtuelle Kubernetes-Clustercomputer verwenden. 

 - Für eine erfolgreiche Bereitstellung müssen die Azure Stack HCI-Clusterknoten und die virtuellen Kubernetes-Clustercomputer über externe Internetkonnektivität verfügen.

 - Die vom DHCP-Server zur Verfügung gestellten IPv4-Adressen sollten routingfähig sein und einen Leaseablaufzeitraum von 30 Tagen aufweisen, um den Verlust der IP-Konnektivität im Falle einer VM-Aktualisierung oder einer erneuten Bereitstellung zu vermeiden.  

 - Die DNS-Namensauflösung ist erforderlich, damit alle Knoten miteinander kommunizieren können. Verwenden Sie für die externe Namensauflösung von Kubernetes die DNS-Server, die vom DHCP-Server bereitgestellt werden, wenn die IP-Adresse abgerufen wird. Verwenden Sie für die interne Namensauflösung von Kubernetes die standardmäßige Core DNS-basierte Lösung von Kubernetes. 
 
 - In dieser Vorschauversion ist für die gesamte Bereitstellung nur ein einzelnes VLAN verfügbar.

 - Diese Vorschauversion verfügt über eingeschränkte Proxyunterstützung für Kubernetes-Cluster, die über PowerShell erstellt wurden.

### <a name="network-port-and-url-requirements"></a>Anforderungen an Netzwerkport und URL 

Beim Erstellen eines Azure Kubernetes-Clusters in Azure Stack HCI werden die folgenden Firewallports auf jedem Server im Cluster automatisch geöffnet. 


| Firewallport               | BESCHREIBUNG         | 
| ---------------------------- | ------------ | 
| 45000           | wssdagent GPRC – Serverport           |
| 45001             | wssdagent GPRC – Authentifizierungsport  | 
| 55000           | wssdcloudagent GPRC – Serverport           |
| 55001             | wssdcloudagent GPRC – Authentifizierungsport  | 


Firewall-URL-Ausnahmen sind für den Windows Admin Center-Computer und alle Knoten im Azure Stack HCI-Cluster erforderlich. 

| URL        | Port | Dienst | Notizen |
| ---------- | ---- | --- | ---- |
https://helm.sh/blog/get-helm-sh/  | 443 | Download Agent, WAC | Wird zum Herunterladen der Helm-Binärdateien verwendet 
https://storage.googleapis.com/  | 443 | Cloud-Init | Kubernetes-Binärdateien werden heruntergeladen 
https://azurecliprod.blob.core.windows.net/ | 443 | Cloud-Init | Binärdateien und Container werden heruntergeladen 
https://aka.ms/installazurecliwindows | 443 | WAC | Azure CLI wird heruntergeladen 
https://:443 | 443 | TCP | Dient zur Unterstützung von Azure Arc-Agents 
*.api.cdp.microsoft.com, *.dl.delivery.mp.microsoft.com, *.emdl.ws.microsoft.com | 80, 443 | Agent herunterladen | Metadaten werden heruntergeladen 
*.dl.delivery.mp.microsoft.com, *.do.dsp.mp.microsoft.com. | 80, 443 | Agent herunterladen | VHD-Images werden heruntergeladen 
ecpacr.azurecr.io | 443 | Kubernetes | Containerimages werden heruntergeladen 
git://:9418 | 9418 | TCP | Dient zur Unterstützung von Azure Arc-Agents 

## <a name="storage-requirements"></a>Speicheranforderungen 

Die folgenden Speicherimplementierungen werden von Azure Kubernetes Service in Azure Stack HCI unterstützt: 

|  Name                         | Speichertyp | Erforderliche Kapazität |
| ---------------------------- | ------------ | ----------------- |
| Azure Stack HCI-Cluster          | CSV          | 1 TB              |
| Windows Server 2019 Datacenter-Failovercluster          | CSV          | 1 TB              |
| Windows Server 2019 Datacenter mit einem einzelnen Knoten | Direkt angeschlossener Speicher | 500 GB|

## <a name="review-maximum-supported-hardware-specifications"></a>Überprüfen der maximal unterstützten Hardwarespezifikationen 

Bereitstellungen von Azure Kubernetes Service in Azure Stack HCI, die folgende Spezifikationen überschreiten, werden nicht unterstützt: 

| Ressource                     | Maximum |
| ---------------------------- | --------|
| Physische Server pro Cluster | 4       |
| Kubernetes-Cluster            | 4       |
| Gesamtzahl der virtuellen Maschinen          | 200     |

## <a name="windows-admin-center-requirements"></a>Anforderungen für Windows Admin Center

Windows Admin Center ist die Benutzerschnittstelle zum Erstellen und Verwalten von Azure Kubernetes Service in Azure Stack HCI. Sie müssen alle Kriterien in der folgenden Liste erfüllen, um Windows Admin Center mithilfe von Azure Kubernetes Service in Azure Stack HCI zu verwenden. 

Für den Computer, auf dem das Windows Admin Center-Gateway ausgeführt wird, gelten folgende Anforderungen: 

 - Ein Computer mit Windows 10 oder Windows Server (das Ausführen von Windows Admin Center auf dem Azure Stack HCI- oder Windows Server 2019 Datacenter-Cluster wird derzeit nicht unterstützt)
 - 60 GB freier Speicherplatz
 - Registrierung bei Azure
 - In derselben Domäne wie der Azure Stack HCI- oder Windows Server 2019 Datacenter-Cluster

## <a name="next-steps"></a>Nächste Schritte 

Nachdem Sie alle oben genannten Voraussetzungen erfüllt haben, können Sie mit Folgendem einen Azure Kubernetes Service-Host in Azure Stack HCI einrichten:
 - [Windows Admin Center](setup.md)
 - [PowerShell](setup-powershell.md)
