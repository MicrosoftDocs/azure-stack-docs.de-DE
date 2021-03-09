---
title: Anforderungen an Azure Kubernetes Service in Azure Stack HCI
description: Voraussetzungen von Azure Kubernetes Service in Azure Stack HCI
ms.topic: conceptual
author: abhilashaagarwala
ms.author: abha
ms.date: 02/02/2021
ms.openlocfilehash: 16d4e7b1de239ee1b08aa696696796fa6f12dff7
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "101839741"
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

 - Es wird empfohlen, für die Server und Dienste, denen Sie Ihren Azure Kubernetes Service auf Azure Stack HCI- oder Windows Server 2019 Datacenter-Clustern hinzufügen, eine gesonderte Organisationseinheit zu verwenden. Durch die Verwendung einer gesonderten Organisationseinheit können Sie den Zugriff und die Berechtigungen differenzierter steuern.

 - Wenn Sie GPO-Vorlagen für Container in Active Directory verwenden, stellen Sie sicher, dass die Bereitstellung von AKS-HCI von der Richtlinie ausgenommen ist. Die Serverhärtung wird in einer späteren Vorschauversion verfügbar sein.

## <a name="compute-requirements"></a>Computeanforderungen

 - Ein Azure Stack HCI-Cluster oder Windows Server 2019 Datacenter-Failovercluster mit maximal vier Servern im Cluster. Es wird empfohlen, dass jeder Server im Cluster über mindestens 24 CPU-Kerne und mindestens 256 GB RAM verfügt.

 - Obwohl Sie Azure Kubernetes Service technisch gesehen in Windows Server 2019 Datacenter mit einem einzelnen Knoten ausführen können, wird von dieser Vorgehensweise abgeraten. Sie können jedoch Azure Kubernetes Service zu Evaluierungszwecken in Windows Server 2019 Datacenter mit einem einzelnen Knoten ausführen.

 - Andere Computeanforderungen für Azure Kubernetes Service in Azure Stack HCI entsprechen den Anforderungen von Azure Stack HCI. Ausführliche Informationen zu den Azure Stack HCI-Serveranforderungen finden Sie unter [Azure Stack HCI-Systemanforderungen](../hci/concepts/system-requirements.md#server-requirements).

 - Diese Vorschauversion erfordert, dass Sie das Azure Stack HCI-Betriebssystem auf jedem Server im Cluster unter Verwendung der EN-US-Region und der Sprachauswahl installieren. Eine Änderung nach der Installation ist zu diesem Zeitpunkt nicht ausreichend.

## <a name="general-network-requirements"></a>Allgemeine Netzwerkanforderungen 

Für Azure Stack HCI-Cluster und für Windows Server 2019 Datacenter-Cluster gelten die folgenden Anforderungen: 

 - Stellen Sie sicher, dass ein vorhandener, externer virtueller Switch konfiguriert ist, wenn Sie das Windows Admin Center verwenden. Bei Azure Stack HCI-Clustern müssen dieser Switch und sein Name auf allen Clusterknoten identisch sein. 

 - Vergewissern Sie sich, dass Sie IPv6 auf allen Netzwerkadaptern deaktiviert haben. 

 - Für eine erfolgreiche Bereitstellung müssen die Azure Stack HCI-Clusterknoten und die virtuellen Kubernetes-Clustercomputer über externe Internetkonnektivität verfügen.
 
 - Stellen Sie sicher, dass alle Subnetze, die Sie für den Cluster definieren, untereinander und in das Internet routingfähig sind.
  
 - Vergewissern Sie sich, dass Netzwerkkonnektivität zwischen Azure Stack HCI-Hosts und den Mandanten-VMs besteht.

 - Die DNS-Namensauflösung ist erforderlich, damit alle Knoten miteinander kommunizieren können. 

## <a name="ip-address-assignment"></a>IP-Adresszuweisung  

In AKS auf Azure Stack HCI werden virtuelle Netzwerke verwendet, um den Kubernetes-Ressourcen, die diese benötigen, wie beschrieben IP-Adressen zuzuordnen. Es gibt zwei Netzwerkmodelle, unter denen Sie wählen können, je nach der gewünschten AKS in Azure Stack HCI-Netzwerkarchitektur. 

> [!NOTE]
 > Die hier definierte virtuelle Netzwerkarchitektur für Ihre AKS auf Azure Stack HCI-Bereitstellungen unterscheidet sich von der zugrunde liegenden physischen Netzwerkarchitektur in Ihrem Rechenzentrum.

- Statisches IP-Netzwerk: Das virtuelle Netzwerk ordnet dem API-Server des Kubernetes-Clusters, den Kubernetes-Knoten, den zugrunde liegenden VMs, Lastenausgleichsmodulen und allen Kubernetes-Diensten, die Sie auf der Grundlage Ihres Clusters ausführen, statische IP-Adressen zu.

- DHCP-Netzwerk: Das virtuelle Netzwerk ordnet den Kubernetes-Knoten, zugrunde liegenden VMs und Lastenausgleichsmodulen mithilfe eines DHCP-Servers dynamische IP-Adressen zu. Dem API-Server des Kubernetes-Clusters und allen Kubernetes-Diensten, die Sie auf der Grundlage Ihres Clusters ausführen, werden immer noch statische IP-Adressen zugeordnet.

### <a name="minimum-ip-address-reservation"></a>Mindestens zu reservierende IP-Adressen

Sie sollten mindestens die folgende Anzahl von IP-Adressen für Ihre Bereitstellung reservieren:

| Clustertyp  | Knoten der Steuerungsebene | Workerknoten | Für Aktualisierungsvorgänge | Load Balancer  |
| ------------- | ------------------ | ---------- | ----------| -------------|
| AKS-Host |  1 IP-Adresse |  Nicht verfügbar  |  2 IP-Adressen |  Nicht verfügbar  |
| Workloadcluster  |  1 IP-Adresse pro Knoten  | 1 IP-Adresse pro Knoten |  5 IP-Adressen  |  1 IP-Adresse |

Zusätzlich sollten Sie die folgende Anzahl von IP-Adressen für Ihren VIP-Pool reservieren:

| Ressourcentyp  | Anzahl von IP-Adressen 
| ------------- | ------------------
| Cluster-API-Server |  1 pro Cluster 
| Kubernetes-Dienste  |  1 pro Dienst  

Wie Sie sehen können, ist die Anzahl der erforderlichen IP-Adressen variabel, je nach AKS auf Azure Stack HCI-Architektur und der Anzahl der Dienste, die Sie auf Ihrem Kubernetes-Cluster ausführen. Es wird empfohlen, für Ihre Bereitstellung insgesamt 256 IP-Adressen (/24-Subnetz) zu reservieren.

Weitere Informationen zu den Netzwerk Anforderungen finden Sie unter [Netzwerk Konzepte in AKS auf Azure Stack HCI](./concepts-networking.md).

### <a name="network-port-and-url-requirements"></a>Anforderungen an Netzwerkport und URL 

Beim Erstellen eines Azure Kubernetes-Clusters in Azure Stack HCI werden die folgenden Firewallports auf jedem Server im Cluster automatisch geöffnet. 


| Firewallport               | Beschreibung     | 
| ---------------------------- | ------------ | 
| 45000           | wssdagent GPRC – Serverport     |
| 45001             | wssdagent GPRC – Authentifizierungsport  | 
| 55000           | wssdcloudagent GPRC – Serverport      |
| 65000            | wssdcloudagent GPRC – Authentifizierungsport  | 


Firewall-URL-Ausnahmen sind für den Windows Admin Center-Computer und alle Knoten im Azure Stack HCI-Cluster erforderlich. 

| URL        | Port | Dienst | Notizen |
| ---------- | ---- | --- | ---- |
https://helm.sh/blog/get-helm-sh/  | 443 | Download Agent, WAC | Wird zum Herunterladen der Helm-Binärdateien verwendet 
https://storage.googleapis.com/  | 443 | Cloud-Init | Kubernetes-Binärdateien werden heruntergeladen 
https://azurecliprod.blob.core.windows.net/ | 443 | Cloud-Init | Binärdateien und Container werden heruntergeladen 
https://aka.ms/installazurecliwindows | 443 | WAC | Azure CLI wird heruntergeladen 
https://:443 | 443 | TCP | Dient zur Unterstützung von Azure Arc-Agents  
*.blob.core.windows.net | 443 | TCP | Für Downloads erforderlich
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

 - Windows 10- oder Windows Server-Computer
 - [Registrierung bei Azure](/windows-server/manage/windows-admin-center/azure/azure-integration)
 - In derselben Domäne wie der Azure Stack HCI- oder Windows Server 2019 Datacenter-Cluster

## <a name="next-steps"></a>Nächste Schritte 

Nachdem Sie alle oben genannten Voraussetzungen erfüllt haben, können Sie mit Folgendem einen Azure Kubernetes Service-Host in Azure Stack HCI einrichten:
 - [Windows Admin Center](setup.md)
 - [PowerShell](setup-powershell.md)
