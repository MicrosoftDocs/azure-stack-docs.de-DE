---
title: Anforderungen an Azure Kubernetes Service in Azure Stack HCI
description: Voraussetzungen von Azure Kubernetes Service in Azure Stack HCI
ms.topic: conceptual
author: abhilashaagarwala
ms.author: abha
ms.date: 09/22/2020
ms.openlocfilehash: 62b1b2983b2e41b5c6d991b4d53338a67bc5aeab
ms.sourcegitcommit: 296c95cad20ed62bdad0d27f1f5246bfc1c81d5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93064666"
---
# <a name="system-requirements-for-azure-kubernetes-service-on-azure-stack-hci"></a>Systemanforderungen für Azure Kubernetes Service in Azure Stack HCI

> Gilt für: Azure Stack HCI

In diesem Artikel werden die Voraussetzungen für die Einrichtung von Azure Kubernetes Service in Azure Stack HCI und dessen Verwendung zum Erstellen von Kubernetes-Cluster behandelt. Eine Übersicht über Azure Kubernetes Service in Azure Stack HCI finden Sie unter [Übersicht über AKS in Azure Stack HCI](overview.md).

## <a name="determine-hardware-requirements"></a>Ermitteln der Hardwareanforderungen

Microsoft empfiehlt den Erwerb einer validierten Azure Stack HCI-Hardware-/Softwarelösung von unseren Partnern. Diese Lösungen wurden anhand unserer Referenzarchitektur entworfen, zusammengestellt und validiert und garantieren Kompatibilität und Zuverlässigkeit, sodass Sie sofort loslegen können. Überprüfen Sie, ob die von Ihnen verwendeten Systeme, Komponenten, Geräte und Treiber gemäß Windows Server Catalog für Windows Server 2019 zertifiziert sind. Auf der Website [Azure Stack HCI](https://azure.microsoft.com/overview/azure-stack/hci) finden Sie validierte Lösungen.

### <a name="general-requirements"></a>Allgemeine Anforderungen

Damit Azure Kubernetes Service in Azure Stack HCI in einer Active Directory-Umgebung optimal funktioniert, müssen die folgenden Anforderungen erfüllt sein: 

 - Stellen Sie sicher, dass die Zeitsynchronisierung eingerichtet ist und die Abweichung zwischen allen Clusterknoten und dem Domänencontroller nicht mehr als zwei Minuten beträgt. Informationen zum Festlegen der Zeitsynchronisierung finden Sie unter [Windows-Zeitdienst](/windows-server/networking/windows-time-service/windows-time-service-top). 

 - Stellen Sie sicher, dass die Benutzerkonten, die Azure Kubernetes Service auf Azure Stack HCI-Clustern hinzufügen, aktualisieren und verwalten, über die richtigen Berechtigungen in Active Directory verfügen. Wenn Sie Organisationseinheiten verwenden, um Gruppenrichtlinien für Server und Dienste zu verwalten, benötigen die Benutzerkonten Berechtigungen zum Auflisten, Lesen, Ändern und Löschen aller Objekte in der Organisationseinheit. 

 - Es wird empfohlen, für die Server und Dienste, zu denen Sie Ihren Azure Kubernetes Service auf Azure Stack HCI-Clustern hinzufügen, eine separate Organisationseinheit zu verwenden. Dadurch können Sie den Zugriff und die Berechtigungen differenzierter steuern.

 - Wenn Sie GPO-Vorlagen für Container in Active Directory verwenden, stellen Sie sicher, dass die Bereitstellung von AKS-HCI von dieser Richtlinie ausgenommen ist. Die Serverhärtung wird in einer späteren Vorschauversion verfügbar sein.

### <a name="compute-requirements"></a>Computeanforderungen

 - Ein Azure Stack HCI-Cluster mit maximal vier Servern im Cluster. Es wird empfohlen, dass jeder Server im Cluster über mindestens 24 CPU-Kerne und mindestens 512 GB RAM verfügt.

 - Obwohl Sie Azure Kubernetes Service technisch gesehen auf einem Azure Stack HCI-Server mit einem einzelnen Knoten ausführen können, wird von dieser Vorgehensweise abgeraten.

 - Andere Computeanforderungen für Azure Kubernetes Service in Azure Stack HCI entsprechen den Anforderungen von Azure Stack HCI. Ausführliche Informationen zu den Azure Stack HCI-Serveranforderungen finden Sie unter [Azure Stack HCI-Systemanforderungen](../hci/concepts/system-requirements.md#server-requirements).

 - Diese Vorschauversion erfordert, dass Sie das Azure Stack HCI-Betriebssystem auf jedem Server im Cluster unter Verwendung der EN-US-Region und der Sprachauswahl installieren. Eine Änderung nach der Installation ist zu diesem Zeitpunkt nicht ausreichend.

### <a name="network-requirements"></a>Netzwerkanforderungen 

Azure Kubernetes Service in Azure Stack HCI erfordert eine Netzwerkverbindung mit hoher und zuverlässiger Bandbreite sowie geringer Latenz zwischen den einzelnen Serverknoten. Überprüfen Sie Folgendes: 

 - Stellen Sie sicher, dass ein vorhandener, externer virtueller Switch konfiguriert ist, wenn Sie das Windows Admin Center verwenden. Bei Azure Stack HCI-Clustern muss dieser Switch auf allen Clusterknoten identisch sein. 

 - Vergewissern Sie sich, dass Sie IPv6 auf allen Netzwerkadaptern deaktiviert haben. 

 - Das Netzwerk muss über einen verfügbaren DHCP-Server verfügen, um den VMs und VM-Hosts entsprechende TCP/IP-Adressen bereitzustellen. Der DHCP-Server sollte außerdem NTP- und DNS-Hostinformationen enthalten. 

 - Außerdem wird empfohlen, einen DHCP-Server mit einem dedizierten Bereich von IPv4-Adressen einzurichten, auf den über den Azure Stack HCI-Cluster zugegriffen werden kann. Sie können z. B. 10.0.1.1 für das Standardgateway, 10.0.1.2 bis 10.0.1.102 für Kubernetes-Dienste reservieren und 10.0.1.103-10.0.1.254 für virtuelle Kubernetes-Clustercomputer verwenden. 

 - Die vom DHCP-Server zur Verfügung gestellten IPv4-Adressen sollten routingfähig sein und einen Leseablaufzeitraum von sieben Tagen aufweisen, um den Verlust der IP-Konnektivität im Falle einer VM-Aktualisierung oder einer erneuten Bereitstellung zu vermeiden.  

 - Die Verwendung von VLAN-Tags wird nicht empfohlen. Verwenden Sie Zugriffsports oder Ports ohne Tags für Ihre Azure Stack HCI-Clusternetzwerkswitches. 

 - Es wird nicht empfohlen, während der Einrichtung einen dedizierten statischen virtuellen IP-Pool für den virtuellen IP-Pool des Lastenausgleichs zu verwenden. Der DHCP-IP-Pool wird für die virtuellen Computer verwendet, während der virtuelle IP-Pool für den Lastenausgleich verwendet wird und routingfähig sein muss. Der DHCP-IP-Pool muss nicht in das externe Internet weitergeleitet werden können.

 - Die DNS-Namensauflösung ist erforderlich, damit alle Knoten miteinander kommunizieren können. Für die externe Namensauflösung von Kubernetes verwenden wir die DNS-Server, die vom DHCP-Server bereitgestellt werden, wenn die IP-Adresse abgerufen wird. Für die interne Namensauflösung von Kubernetes verwenden wir die standardmäßige Core DNS-basierte Lösung von Kubernetes. 

 - Für diese Vorschauversion unterstützen wir nicht die Verwendung eines Proxyservers, um eine Verbindung von Windows Admin Center, Azure Stack HCI-Clusterknoten und Azure Kubernetes Service auf Azure Stack HCI-Clusterknoten mit dem Internet herzustellen.

### <a name="network-port-and-url-requirements"></a>Anforderungen an Netzwerkport und URL 

Beim Erstellen eines Azure Kubernetes-Clusters in Azure Stack HCI öffnen wir automatisch die folgenden Firewallports auf jedem Server im Cluster. 


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
*.api.cdp.microsoft.com, *.dl.delivery.mp.microsoft.com, *.emdl.ws.microsoft.com | 80, 443 | Agent herunterladen | Metadaten werden heruntergeladen 
*.dl.delivery.mp.microsoft.com, *.do.dsp.mp.microsoft.com. | 80, 443 | Agent herunterladen | VHD-Images werden heruntergeladen 
ecpacr.azurecr.io | 443 | Kubernetes | Containerimages werden heruntergeladen 

### <a name="storage-requirements"></a>Speicheranforderungen 

Die folgenden Speicherimplementierungen werden von Azure Kubernetes Service in Azure Stack HCI unterstützt: 

|  Name                         | Speichertyp | Erforderliche Kapazität |
| ---------------------------- | ------------ | ----------------- |
| Azure Stack HCI-Cluster          | CSV          | 1 TB              |
| Azure Stack HCI für einzelnen Knoten | Direkt angeschlossener Speicher | 500 GB|

### <a name="review-maximum-supported-hardware-specifications"></a>Überprüfen der maximal unterstützten Hardwarespezifikationen 

Bereitstellungen von Azure Kubernetes Service in Azure Stack HCI, die folgende Spezifikationen überschreiten, werden nicht unterstützt: 

| Ressource                     | Maximum |
| ---------------------------- | --------|
| Physische Server pro Cluster | 4       |
| Kubernetes-Cluster            | 4       |
| Gesamtzahl der virtuellen Maschinen          | 200     |

### <a name="windows-admin-center"></a>Windows Admin Center 

Windows Admin Center ist die Benutzerschnittstelle zum Erstellen und Verwalten von Azure Kubernetes Service in Azure Stack HCI. Sie müssen alle Kriterien in der folgenden Liste erfüllen, um Windows Admin Center mithilfe von Azure Kubernetes Service in Azure Stack HCI zu verwenden. 

#### <a name="on-your-windows-admin-center-system"></a>Auf Ihrem Windows Admin Center-System

Auf den Computer, auf dem das Windows Admin Center-Gateway ausgeführt wird, muss Folgendes zutreffen: 

 - Windows 10 (wir unterstützen zurzeit keine Windows Admin Center-Server)
 - 60 GB freier Speicherplatz
 - Registrierung bei Azure
 - In derselben Domäne wie der Azure Stack HCI-Cluster

## <a name="next-steps"></a>Nächste Schritte 

Nachdem Sie alle oben genannten Voraussetzungen erfüllt haben, können Sie mit Folgendem einen Azure Kubernetes Service-Host in Azure Stack HCI einrichten:
 - [Windows Admin Center](setup.md)
 - [PowerShell](setup-powershell.md)
