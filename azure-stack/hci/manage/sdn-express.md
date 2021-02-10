---
title: Bereitstellen einer SDN-Infrastruktur mit SDN Express
description: Es wird beschrieben, wie Sie eine SDN-Infrastruktur mit SDN Express bereitstellen.
author: v-dasis
ms.topic: how-to
ms.date: 02/01/2021
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: b8431b7e2cf2cad3d238386619839a760b722042
ms.sourcegitcommit: d74f6d8630783de49667956f39cac67e1968a89d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99473190"
---
# <a name="deploy-an-sdn-infrastructure-using-sdn-express"></a>Bereitstellen einer SDN-Infrastruktur mit SDN Express

> Gilt für Azure Stack HCI, Version 20H2

Unter diesem Thema wird beschrieben, wie Sie eine End-to-End-SDN-Infrastruktur (Softwaredefiniertes Netzwerk) bereitstellen, indem Sie SDN Express-PowerShell-Skripts verwenden. Die Infrastruktur enthält als Komponenten einen Netzwerkcontroller (NC), einen Software Load Balancer (SLB) und ein Gateway, die allesamt hoch verfügbar sind.  Die Skripts unterstützen eine Bereitstellung in Phasen. Sie können beispielsweise zunächst nur den Netzwerkcontroller bereitstellen, um einen Teil der Funktionen bei sehr niedrigen Netzwerkanforderungen zu erhalten. 

Darüber hinaus können Sie mit System Center Virtual Machine Manager (VMM) auch eine SDN-Infrastruktur bereitstellen. Weitere Informationen finden Sie unter [Verwalten von SDN-Ressourcen im VMM-Fabric](/system-center/vmm/network-sdn).

## <a name="before-you-begin"></a>Voraussetzungen

Bevor Sie mit der SDN-Bereitstellung beginnen, sollten Sie Ihre Infrastruktur für physische Netzwerke und Hostnetzwerke planen und konfigurieren. Lesen Sie die folgenden Artikel:

- [Anforderungen für physische Netzwerke](../concepts/physical-network-requirements.md)
- [Anforderungen für Hostnetzwerke](../concepts/host-network-requirements.md)
- [Erstellen eines Azure Stack HCI-Clusters mithilfe von Windows Admin Center](../deploy/create-cluster.md)
- [Erstellen eines Azure Stack HCI-Clusters mithilfe von Windows PowerShell](../deploy/create-cluster-powershell.md)
- [Planen einer softwaredefinierten Netzwerkinfrastruktur](../concepts/plan-software-defined-networking-infrastructure.md)

Sie müssen nicht alle SDN-Komponenten bereitstellen. Anhand der Informationen im Abschnitt [Stufenweise Bereitstellung](../concepts/plan-software-defined-networking-infrastructure.md#phased-deployment) unter [Planen einer softwaredefinierten Netzwerkinfrastruktur](../concepts/plan-software-defined-networking-infrastructure.md) können Sie ermitteln, welche Infrastrukturkomponenten Sie benötigen. Anschließend können Sie die entsprechenden Skripts ausführen.

Stellen Sie sicher, dass auf allen Hostservern das Azure Stack HCI-Betriebssystem installiert ist. Informationen hierzu finden Sie unter [Bereitstellen des Azure Stack HCI-Betriebssystems](../deploy/operating-system.md).

## <a name="requirements"></a>Anforderungen

Damit die SDN-Bereitstellung erfolgreich ist, müssen die folgenden Anforderungen erfüllt sein:

- Für alle Hostserver muss Hyper-V aktiviert sein.
- Alle Hostserver müssen in Active Directory eingebunden sein.
- Ein virtueller Switch muss erstellt werden.
- Das physische Netzwerk muss für die Subnetze und VLANs konfiguriert werden, die in der Konfigurationsdatei definiert sind.
- Die in der Konfigurationsdatei angegebene VHDX-Datei muss von dem Bereitstellungscomputer aus erreichbar sein, auf dem das SDN Express-Skript ausgeführt wird

## <a name="create-the-vdx-file"></a>Erstellen der VDX-Datei

Für SDN wird eine VHDX-Datei mit dem Azure Stack HCI-Betriebssystem als Quelle für die Erstellung der virtuellen SDN-Computer (VMs) genutzt. Die Version des Betriebssystems auf Ihrer VHDX muss mit der Version übereinstimmen, die von den Hyper-V-Hosts verwendet wird.

Wenn Sie das Azure Stack HCI-Betriebssystem über ein ISO-Image heruntergeladen und installiert haben, können Sie diese VHDX mit dem Hilfsprogramm `Convert-WindowsImage` erstellen. Eine Beschreibung finden Sie im [Script Center](https://gallery.technet.microsoft.com/scriptcenter/Convert-WindowsImageps1-0fe23a8f).

Hier ist ein Beispiel für die Verwendung von `Convert-WindowsImage` angegeben:

 ```powershell
$wimpath = "d:\sources\install.wim"
$vhdpath = "c:\temp\WindowsServerDatacenter.vhdx"
$Edition = 4   # 4 = Full Desktop, 3 = Server Core

import-module ".\convert-windowsimage.ps1"

Convert-WindowsImage -SourcePath $wimpath -Edition $Edition -VHDPath $vhdpath -SizeBytes 500GB -DiskLayout UEFI
```

## <a name="download-the-github-repository"></a>Herunterladen des GitHub-Repositorys

Die SDN Express-Skriptdateien befinden sich auf GitHub. Im ersten Schritt werden die benötigten Dateien und Ordner auf Ihren Bereitstellungscomputer heruntergeladen.

1. Navigieren Sie zum GitHub-Repository für [SDN Express](https://github.com/microsoft/SDN).

1. Laden Sie die Dateien aus dem Repository auf Ihren Bereitstellungscomputer herunter. Wählen Sie Option **Clone** (Klonen) oder **Download ZIP** (ZIP herunterladen) aus.

    > [!NOTE]
    > Auf dem Bereitstellungscomputer muss Windows Server 2016 oder höher ausgeführt werden.

1. Extrahieren Sie die ZIP-Datei, und kopieren Sie den Ordner `SDNExpress` in den Ordner `C:\` Ihres Bereitstellungscomputers.

## <a name="edit-the-configuration-file"></a>Bearbeiten der Konfigurationsdatei

Die PowerShell-Datei `MultiNodeSampleConfig.psd1` mit den Konfigurationsdaten enthält alle Parameter und Einstellungen, die für das SDN Express-Skript als Eingabe für die verschiedenen Parameter und Konfigurationseinstellungen benötigt werden. Diese Datei enthält spezifische Informationen dazu, was Sie angeben müssen. Dies hängt davon ab, ob Sie nur die Netzwerkcontroller-Komponente oder auch die Software Load Balancer- und die Gateway-Komponente bereitstellen.

Ausführliche Informationen finden Sie im Thema [Planen einer softwaredefinierten Netzwerkinfrastruktur](../concepts/plan-software-defined-networking-infrastructure.md).

Fangen wir an:

1. Navigieren Sie zum Ordner `C:\SDNExpress\scripts`.

1. Öffnen Sie die Datei `MultiNodeSampleConfig.psd1` in einem Text-Editor Ihrer Wahl.

1. Ändern Sie die spezifischen Parameterwerte, um sie an Ihre Infrastruktur anzupassen.

## <a name="run-the-deployment-script"></a>Ausführen des Bereitstellungsskripts

Mit dem SDN Express-Skript wird Ihre SDN-Infrastruktur bereitgestellt. Nach Abschluss der Skriptausführung können Sie Ihre Infrastruktur für Workloadbereitstellungen nutzen.

1. Die Datei `README.md` enthält die neuesten Informationen zur Ausführung des Bereitstellungsskripts.  

1. Führen Sie den folgenden Befehl über ein Benutzerkonto mit Administratoranmeldeinformationen für die Clusterhostserver aus:

    ```powershell
    SDNExpress\scripts\SDNExpress.ps1 -ConfigurationDataFile MultiNodeSampleConfig.psd1 -Verbose
    ```

1. Konfigurieren Sie nach dem Erstellen der Netzwerkcontroller-VMs dynamische DNS-Updates für den Namen des Netzwerkcontrollerclusters auf dem DNS-Server. Weitere Informationen finden Sie unter [Schritt 3: Konfigurieren der dynamischen DNS-Registrierung für den Netzwerkcontroller](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller#step-3-configure-dynamic-dns-registration-for-network-controller).

## <a name="next-steps"></a>Nächste Schritte

Verwalten Sie Ihre VMs. Weitere Informationen finden Sie unter [Verwalten virtueller Computer mit Windows Admin Center](../manage/vm.md).