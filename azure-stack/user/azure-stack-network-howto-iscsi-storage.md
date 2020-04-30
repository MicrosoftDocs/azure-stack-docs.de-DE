---
title: Herstellen einer Verbindung mit dem iSCSI-Speicher mit Azure Stack Hub
description: Erfahren Sie, wie Sie mit Azure Stack Hub eine Verbindung mit dem iSCSI-Speicher herstellen.
author: mattbriggs
ms.topic: how-to
ms.date: 04/20/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/28/2019
ms.openlocfilehash: f691ba0cfeadae0d359473db881601e90478276c
ms.sourcegitcommit: 32834e69ef7a804c873fd1de4377d4fa3cc60fb6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81660892"
---
# <a name="connect-to-iscsi-storage-with-azure-stack-hub"></a>Herstellen einer Verbindung mit dem iSCSI-Speicher mit Azure Stack Hub

Mithilfe der Vorlage in diesem Artikel können Sie einen virtuellen Azure Stack Hub-Computer mit einem lokalen iSCSI-Ziel verbinden und den virtuellen Computer so einrichten, dass Speicher außerhalb von Azure Stack Hub und an anderer Stelle in Ihrem Rechenzentrum verwendet werden. Dieser Artikel befasst sich mit der Verwendung eines Windows-Computers als iSCSI-Ziel.

Die Vorlage finden Sie im Fork **lucidqdreams** des GitHub-Repositorys [Azure Intelligent Edge Patterns](https://github.com/lucidqdreams/azure-intelligent-edge-patterns). Die Vorlage befindet sich im Ordner **storage-iSCSI**. Die Vorlage ist so ausgelegt, dass die in Azure Stack Hub erforderliche Infrastruktur für die Herstellung einer Verbindung mit einem iSCSI-Ziel eingerichtet wird. Dies umfasst einen virtuellen Computer, der als iSCSI-Initiator agiert, zusammen mit den zugehörigen Komponenten: VNET, Netzwerksicherheitsgruppe, PIP und Speicher. Nach dem Bereitstellen der Vorlage müssen zwei PowerShell-Skripts ausgeführt werden, um die Konfiguration abzuschließen. Ein Skript wird auf dem lokalen virtuellen Computer (Ziel) ausgeführt und das andere auf dem virtuellen Azure Stack Hub-Computer (Initiator). Nach dem Ausführen dieser Skripts wird dem virtuellen Azure Stack Hub-Computer der lokale Speicher hinzugefügt. 

## <a name="overview"></a>Übersicht

In der Abbildung ist ein virtueller Computer dargestellt, der über einen (virtuellen oder physischen) Windows-Computer lokal mit einem eingebundenen iSCSI-Datenträger in Azure Stack Hub gehostet wird, sodass Speicher außerhalb von Azure Stack Hub über das iSCSI-Protokoll innerhalb Ihres in Azure Stack Hub gehosteten virtuellen Computers eingebunden werden kann.

![alt text](./media/azure-stack-network-howto-iscsi-storage/overview.png)

### <a name="requirements"></a>Requirements (Anforderungen)

- Ein lokaler (physischer oder virtueller) Computer, auf dem Windows Server 2016 Datacenter oder Windows Server 2019 Datacenter ausgeführt wird
- Erforderliche Azure Stack Hub-Marketplace-Elemente:
    -  Windows Server 2016 Datacenter oder Windows Server 2019 Datacenter (neuester Build empfohlen)
    -  PowerShell DSC-Erweiterung
    -  Benutzerdefinierte Skripterweiterung
    -  Ein vorhandener virtueller oder physischer Computer. Im Idealfall weist dieser Computer zwei Netzwerkadapter auf. Dies kann auch ein anderes iSCSI-Ziel sein. z. B. ein SAN.

### <a name="things-to-consider"></a>Zu beachtende Aspekte

- Eine Netzwerksicherheitsgruppe wird auf das Subnetz der Vorlage angewandt. Überprüfen Sie dies, und nehmen Sie bei Bedarf zusätzliche Berichtigungen vor.
- Eine Regel zum Ablehnen von RDP wird auf die Netzwerksicherheitsgruppe des Tunnels angewandt und muss auf „Zulassen“ festgelegt werden, wenn Sie über die öffentliche IP-Adresse auf die virtuellen Computer zugreifen möchten.
- Bei dieser Lösung wird die DNS-Auflösung nicht berücksichtigt.
- Sie sollten die Werte für „Chapusername“ und „Chappassword“ ändern. Der Wert für „Chappassword“ muss 12 bis 16 Zeichen umfassen.
- In dieser Vorlage wird eine statische IP-Adresse für den virtuellen Computer verwendet, da bei der iSCSI-Verbindung die lokale Adresse in der Konfiguration verwendet wird.
- In dieser Vorlage wird die Lizenz für BYOL-Fenster verwendet.
- Sie können außerdem Linux-basierte Systeme mit den iSCSI-Zielen verbinden. Anweisungen finden Sie im Artikel [iSCSI Initiator](https://help.ubuntu.com/lts/serverguide/iscsi-initiator.html) in der Ubuntu-Dokumentation.

### <a name="options"></a>Tastatur

- Mit den Parametern **_artifactsLocation** und **_artifactsLocationSasToken** können Sie Ihr eigenes Blob Storage-Konto mit SAS-Token verwenden.
- Diese Vorlage enthält Standardwerte für die VNET-Benennung und die IP-Adressierung.
- Diese Konfiguration umfasst nur einen iSCSI-Netzwerkadapter vom iSCSI-Client. Wir haben eine Reihe von Konfigurationen getestet, um separate Subnetze und Netzwerkadapter zu verwenden, stießen jedoch auf Probleme mit mehreren Gateways und dem Versuch, ein separates Speichersubnetz zu erstellen, um den Datenverkehr zu isolieren und tatsächlich wirkliche Redundanz zu gewährleisten. 
- Achten Sie darauf, dass diese Werte innerhalb der zulässigen Subnetz- und Adressbereiche liegen, da bei der Bereitstellung Fehler auftreten können. 
- Hauptzweck der PowerShell DSC-Pakete ist die Überprüfung auf ausstehende Neustarts. Diese DSC-Pakete können bei Bedarf weiter angepasst werden. Weitere Informationen finden Sie unter [ComputerManagementDsc](https://github.com/PowerShell/ComputerManagementDsc/).

### <a name="resource-group-template-iscsi-client"></a>Ressourcengruppenvorlage (iSCSI-Client)

In der Abbildung sind die mithilfe der Vorlage zum Erstellen des iSCSI-Clients bereitgestellten Vorlagen dargestellt, über die Sie eine Verbindung mit dem iSCSI-Ziel herstellen können. Mit dieser Vorlage werden der virtuelle Computer und andere Ressourcen bereitgestellt. Außerdem wird die Datei „prepare-iSCSIClient.ps1“ ausgeführt und der virtuelle Computer neu gestartet.

![alt text](./media/azure-stack-network-howto-iscsi-storage/iscsi-file-server.png)

### <a name="the-deployment-process"></a>Bereitstellungsprozess

Mit der Ressourcengruppenvorlage wird eine Ausgabe generiert, die als Eingabe für den nächsten Schritt dienen soll. Sie ist hauptsächlich auf den Servernamen und die öffentliche IP-Adresse für Azure Stack Hub konzentriert, von denen der iSCSI-Datenverkehr stammt. In diesem Beispiel:

1. Stellen Sie die Infrastrukturvorlage bereit.
2. Stellen Sie einen virtuellen Azure Stack Hub-Computer auf einem virtuellen Computer bereit, der an anderer Stelle in Ihrem Rechenzentrum gehostet wird. 
3. Führen Sie `Create-iSCSITarget.ps1` mit den Ausgaben für die IP-Adresse und den Servernamen aus der Vorlage als Ein-/Ausgabeparameter für das Skript auf dem iSCSI-Ziel aus, bei dem es sich um einen virtuellen Computer oder einen physischen Server handeln kann.
4. Verwenden Sie die externen IP-Adressen des iSCSI-Zielservers als Eingaben, um das Skript `Connect-toiSCSITarget.ps1` auszuführen. 

![alt text](./media/azure-stack-network-howto-iscsi-storage/process.png)

### <a name="inputs-for-azuredeployjson"></a>Eingaben für „azuredeploy.json“

|**Parameter**|**Standardwert**|**description**|
|------------------|---------------|------------------------------|
|WindowsImageSKU         |2019-Datacenter   |Wählen Sie das Windows-VM-Basisimage aus.
|VMSize                  |Standard_D2_v2    |Geben Sie die VM-Größe ein.
|VMName                  |FileServer        |Name des virtuellen Computers
|adminUsername           |storageadmin      |Name des Administrators des neuen virtuellen Computers
|adminPassword           |                  |Kennwort für das Administratorkonto des neuen virtuellen Computers. Standardwert ist die Abonnement-ID.
|VNetName                |Storage           |Name des VNET. Dieser wird verwendet, um die Ressourcen zu bezeichnen.
|VNetAddressSpace        |10.10.0.0/23      |Adressraum für das VNET
|VNetInternalSubnetName  |Intern          |Name des internen Subnetzes des VNET
|VNetInternalSubnetRange |10.10.1.0/24      |Adressbereich für das interne Subnetz des VNET
|InternalVNetIP          |10.10.1.4         |Statische Adresse für die interne IP-Adresse des Dateiservers
|_artifactsLocation      ||
|_artifactsLocationSasToken||

### <a name="deployment-steps"></a>Bereitstellungsschritte

1. Stellen Sie die iSCSI-Clientinfrastruktur mithilfe von `azuredeploy.json` bereit.
2. Führen Sie `Create-iSCSITarget.ps1` auf dem iSCSI-Ziel des lokalen Servers aus. Nach dem Ausführen der Vorlage müssen Sie „Create-iSCSITarget.ps1“ auf dem iSCSI-Ziel des lokalen Servers mit den Ausgaben aus dem ersten Schritt ausführen.
3. Führen Sie `Connect-toiSCSITarget.ps1` auf dem iSCSI-Client aus. Führen Sie „Connect-toiSCSITarget.ps1“ auf dem iSCSI-Client mit den Details des iSCSI-Ziels aus.

## <a name="adding-iscsi-storage-to-existing-vms"></a>Hinzufügen von iSCSI-Speicher zu vorhandenen virtuellen Computern

Sie können die Skripts auch auf einem vorhandenen virtuellen Computer ausführen, um eine Verbindung zwischen dem iSCSI-Client und einem iSCSI-Ziel herzustellen. Bei diesem Ablauf gehen Sie so vor, als würden Sie das iSCSI-Ziel selbst erstellen. In der folgenden Abbildung ist die Ausführung der PowerShell-Skripts dargestellt. Die Skripts befinden sich im Skriptverzeichnis.

![alt text](./media/azure-stack-network-howto-iscsi-storage/script-flow.png)

### <a name="prepare-iscsiclientps1"></a>Prepare-iSCSIClient.ps1

Mit dem Skript `Prepare-iSCSIClient.ps1` werden die erforderlichen Komponenten auf dem iSCSI-Client installiert. Dies umfasst Folgendes:
- Installation von Multipfad E/A-Diensten
- Festlegen des Starts des iSCSI-Initiator-Diensts auf „Automatisch“
- Aktivieren der Unterstützung für Multipfad I/O für iSCSI
- Aktivieren der automatischen Beanspruchung aller iSCSI-Volumes
- Festlegen des Timeouts für den Datenträger auf 60 Sekunden

Das System muss nach der Installation dieser erforderlichen Komponenten zwingend neu gestartet werden. Zum Festlegen der Richtlinie für den MPIO-Lastenausgleich ist ein Neustart erforderlich.

### <a name="create-iscsitargetps1"></a>Create-iSCSITarget.ps1

Das Skript `Create-iSCSITarget.ps1 ` muss auf dem System ausgeführt werden, das den Speicher bereitstellt. Sie können mehrere durch Initiatoren eingeschränkte Datenträger und Ziele erstellen. Sie können dieses Skript mehrmals ausführen, um viele virtuelle Datenträger zu erstellen, die Sie an verschiedene Ziele anfügen können. Sie können mehrere Datenträger mit einem Ziel verbinden. 

|**Input** (Eingabe)|**Standardwert**|**description**|
|------------------|---------------|------------------------------|
|RemoteServer         |FileServer               |Name des Servers, über den die Verbindung mit dem iSCSI-Ziel hergestellt wird
|RemoteServerIPs      |1.1.1.1                  |IP-Adresse, von der der iSCSI-Datenverkehr stammt
|DiskFolder           |C:\iSCSIVirtualDisks     |Ordner und Laufwerk, unter denen die virtuellen Datenträger gespeichert werden
|DiskName             |DiskName                 |Name der VHDX-Datei des Datenträgers
|DiskSize             |5 GB                      |VHDX-Datenträgergröße
|TargetName           |RemoteTarget01           |Zielname zum Definieren der Zielkonfiguration für den iSCSI-Client 
|ChapUsername         |username                 |Benutzername für die Chap-Authentifizierung
|ChapPassword         |userP@ssw0rd!            |Kennwortname für die Chap-Authentifizierung. Muss 12 bis 16 Zeichen umfassen.

### <a name="connect-toiscsitargetps1"></a>Connect-toiSCSITarget.ps1

Das Skript `Connect-toiSCSITarget.ps1` wird als abschließendes Skript auf dem iSCSI-Client ausgeführt. Mit diesem Skript wird der Datenträger eingebunden, der auf dem iSCSI-Ziel für den iSCSI-Client bereitgestellt wird.

|**Input** (Eingabe)|**Standardwert**|**description**|
|------------------|---------------|------------------------------|
|TargetiSCSIAddresses   |2\.2.2.2, 2.2.2.3    |IP-Adressen des iSCSI-Ziels
|LocalIPAddresses       |10.10.1.4            |Interne IP-Adresse, von der der iSCSI-Datenverkehr stammt
|LoadBalancePolicy      |C:\iSCSIVirtualDisks   |IP-Adresse, von der der iSCSI-Datenverkehr stammt
|ChapUsername           |username               |Benutzername für die Chap-Authentifizierung
|ChapPassword           |userP@ssw0rd!          |Kennwortname für die Chap-Authentifizierung. Muss 12 bis 16 Zeichen umfassen.

## <a name="next-steps"></a>Nächste Schritte

[Azure Stack Hub-Netzwerke: Unterschiede und Überlegungen](azure-stack-network-differences.md)  