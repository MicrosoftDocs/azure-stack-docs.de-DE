---
title: Bekannte Probleme für Azure Kubernetes Service in Azure Stack HCI
description: Bekannte Probleme für Azure Kubernetes Service in Azure Stack HCI
author: abha
ms.topic: troubleshooting
ms.date: 09/22/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: 89324bbfe2c1f28e268a22a9b31c880daaddecd6
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90949315"
---
# <a name="known-issues-for-azure-kubernetes-service-on-azure-stack-hci-public-preview"></a>Bekannte Probleme für Azure Kubernetes Service in Azure Stack HCI Public Preview
In diesem Artikel werden bekannte Probleme mit der öffentlichen Vorschauversion von Azure Kubernetes Service in Azure Stack HCI beschrieben.

## <a name="recovering-from-a-failed-aks-on-azure-stack-hci-deployment"></a>Wiederherstellen nach einer fehlerhaften Bereitstellung von AKS in Azure Stack HCI
Wenn bei Ihnen Bereitstellungsprobleme auftreten oder Sie Ihre Bereitstellung zurücksetzen möchten, stellen Sie sicher, dass Sie alle Windows Admin Center-Instanzen, die mit Azure Kubernetes Service in Azure Stack HCI verbunden sind, schließen, bevor Sie „Uninstall-AksHci“ über ein PowerShell-Verwaltungsfenster ausführen.

## <a name="when-using-kubectl-to-delete-a-node-the-associated-vm-might-not-be-deleted"></a>Beim Löschen eines Knotens mit kubectl wird die zugehörige VM möglicherweise nicht gelöscht.
Sie behandeln dieses Problem, wenn Sie die folgenden Schritte ausführen:
* Erstellen eines Kubernetes-Clusters
* Skalieren des Clusters auf mehr als zwei Knoten
* Verwenden von „kubectl delete node <Knotenname>“ zum Löschen eines Knotens 
* Ausführen von „kubectl get nodes“ Der entfernte Knoten ist in der Ausgabe nicht aufgeführt
* Öffnen eines PowerShell-Administratorfensters
* Ausführen von „get-vm“ Der entfernte Knoten ist weiterhin aufgeführt.

Dies führt dazu, dass das System den fehlenden Knoten nicht erkennt und ein neuer Knoten nicht hochgefahren wird. Dies wird in einem zukünftigen Release behoben.

## <a name="time-synchronization-must-be-configured-across-all-physical-cluster-nodes-and-in-hyper-v"></a>Die Zeitsynchronisierung muss über alle physischen Clusterknoten und in Hyper-V konfiguriert werden.
Um sicherzustellen, dass die gMSA- und AD-Authentifizierung funktioniert, vergewissern Sie sich, dass die Azure Stack HCI-Clusterknoten so konfiguriert sind, dass sie ihre Zeit mit einem Domänencontroller oder einer anderen Zeitquelle synchronisieren, und dass Hyper-V so konfiguriert ist, dass es die Zeit mit allen virtuellen Computern synchronisiert.

## <a name="special-active-directory-permissions-are-needed-for-domain-joined-azure-stack-hci-nodes"></a>Für Azure Stack HCI-Knoten, die in die Domäne eingebunden sind, sind spezielle Active Directory-Berechtigungen erforderlich. 
Benutzer, die Azure Kubernetes Service in Azure Stack HCI bereitstellen und konfigurieren, benötigen die Berechtigung „Vollzugriff“, um AD-Objekte im Active Directory-Container, in dem der Server und die Dienstobjekte erstellt werden, zu erstellen. 

## <a name="get-akshcilogs-command-may-fail"></a>Beim Befehl „Get-AksHciLogs“ tritt möglicherweise ein Fehler auf.
Bei großen Clustern kann der Befehl „Get-AksHciLogs“ eine Ausnahme auslösen, bei der Enumeration von Knoten zu einem Fehler führen oder die Ausgabedatei „c:\wssd\wssdlogs.zip“ nicht generieren.
Dies liegt daran, dass der PowerShell-Befehl zum Komprimieren einer Datei, `Compress-Archive`, eine Begrenzung der Ausgabedateigröße von 2 GB aufweist. Dieses Problem wird in einer künftigen Version behoben.

## <a name="azure-kubernetes-service-powershell-deployment-doesnt-check-for-available-memory-before-creating-a-new-target-cluster"></a>Die PowerShell-Bereitstellung von Azure Kubernetes Service überprüft nicht den verfügbaren Arbeitsspeicher, bevor ein neuer Zielcluster erstellt wird.
Die Aks-Hci-PowerShell-Befehle überprüfen vor dem Erstellen von Kubernetes-Knoten nicht den verfügbaren Arbeitsspeicher auf dem Hostserver. Dies kann dazu führen, dass der Speicher ausgelastet ist und virtuelle Computer nicht starten. Dieser Fehler wird derzeit nicht angemessen behandelt, und die Bereitstellung bleibt ohne eindeutige Fehlermeldung hängen.
Wenn Sie eine Bereitstellung haben, die scheinbar hängt, öffnen Sie `Eventviewer`, und prüfen Sie auf Hyper-V-bezogene Fehlermeldungen, die auf nicht genügend Speicher zum Starten der VM hinweisen.
Dieses Problem wird in einem zukünftigen Release behoben.

## <a name="azure-kubernetes-service-deployment-fails-on-an-azure-stack-hci-configured-with-static-ips-vlans-sdn-or-proxies"></a>Bei der Bereitstellung von Azure Kubernetes Service tritt ein Fehler bei einer Azure Stack HCI auf, die mit statischen IP-Adressen, VLANs, softwaredefiniertem Netzwerk (SDN) oder Proxys konfiguriert ist.
Bei der Bereitstellung von Azure Kubernetes Service in einem Azure Stack HCI-Cluster mit statischen IP-Adressen, VLANs, SDN oder Proxys tritt bei der Clustererstellung ein Fehler auf. Dieses Problem wird in einer künftigen Version behoben.

## <a name="ipv6-must-be-disabled-in-the-hosting-environment"></a>IPv6 muss in der Hostingumgebung deaktiviert werden.
Wenn sowohl IPv4- als auch IPv6-Adressen an die physische Netzwerkkarte (NIC) gebunden sind, verwendet der `cloudagent`-Dienst für das Clustering die IPv6-Adresse für die Kommunikation. Andere Komponenten im Bereitstellungsframework verwenden nur IPv4. Dies führt dazu, dass das Windows Admin Center keine Verbindung zum Cluster herstellen kann und beim Versuch, eine Verbindung mit dem Computer herzustellen, einen Remoteverbindungsfehler meldet.
Problemumgehung: Deaktivieren Sie IPv6 auf den physischen Netzwerkadaptern.
Dieses Problem wird in einem zukünftigen Release behoben.

## <a name="moving-virtual-machines-between-azure-stack-hci-cluster-nodes-quickly-leads-to-vm-startup-failures"></a>Das Verschieben virtueller Computer zwischen Azure Stack HCI-Clusterknoten führt schnell zu Fehlern beim VM-Start.
Wenn Sie das Clusterverwaltungstool verwenden, um eine VM von einem Knoten (Knoten A) auf einen anderen Knoten (Knoten B) im Azure Stack HCI-Cluster zu verschieben, kann die VM möglicherweise nicht auf dem neuen Knoten gestartet werden. Nach dem Verschieben der VM zurück auf den ursprünglichen Knoten kann sie auch dort nicht starten.
Dieses Problem tritt auf, da die Logik zur Bereinigung der ersten Migration asynchron ausgeführt wird. Infolgedessen findet die Logik „VM-Standort aktualisieren“ von Azure Kubernetes Service die VM auf dem ursprünglichen Hyper-V auf Knoten A und löscht sie, anstatt die Registrierung aufzuheben.
Problemumgehung: Stellen Sie sicher, dass die VM erfolgreich auf dem neuen Knoten gestartet wurde, bevor Sie sie wieder auf den ursprünglichen Knoten verschieben.
Dieses Problem wird in einem zukünftigen Release behoben.

## <a name="load-balancer-in-azure-kubernetes-service-requires-dhcp-reservation"></a>Lastenausgleich in Azure Kubernetes Service erfordert DHCP-Reservierung
Die Lastenausgleichslösung in Azure Kubernetes Service in Azure Stack HCI verwendet DHCP zur Zuweisung von IP-Adressen an Dienstendpunkte. Wenn sich die IP-Adresse für den Dienstendpunkt aufgrund eines Neustarts des Diensts ändert, läuft die DHCP-Leasedauer aufgrund einer kurzen Ablaufzeit ab. Aus diesem Grund kann nicht mehr auf den Dienst zugegriffen werden, da die IP-Adresse in der Kubernetes-Konfiguration eine andere ist als am Endpunkt. Dies kann dazu führen, dass der Kubernetes-Cluster nicht mehr verfügbar ist.
Um dieses Problem zu umgehen, verwenden Sie einen MAC-Adresspool für die Dienstendpunkte mit Lastenausgleich, und reservieren Sie bestimmte IP-Adressen für jede MAC-Adresse im Pool.
Dieses Problem wird in einer künftigen Version behoben.

## <a name="cannot-deploy-azure-kubernetes-service-to-an-environment-that-has-separate-storage-and-compute-clusters"></a>Azure Kubernetes Service kann nicht in einer Umgebung mit separaten Speicher- und Computeclustern bereitgestellt werden.
Windows Admin Center stellt Azure Kubernetes Service nicht in einer Umgebung mit separaten Speicher- und Computeclustern bereit, da es erwartet, dass die Compute- und Speicherressourcen vom selben Cluster bereitgestellt werden. In den meisten Fällen findet es keine CSV-Dateien, die vom Computecluster verfügbar gemacht werden, und weigert sich, die Bereitstellung fortzusetzen.
Dieses Problem wird in einer künftigen Version behoben.
