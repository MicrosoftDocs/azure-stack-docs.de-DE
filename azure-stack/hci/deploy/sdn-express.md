---
title: Bereitstellen einer SDN-Infrastruktur mit SDN Express
description: Es wird beschrieben, wie Sie eine SDN-Infrastruktur mit SDN Express bereitstellen.
author: v-dasis
ms.topic: how-to
ms.date: 12/16/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: ba28d5904e58d84a913777fe80c5aa05a8ecdffe
ms.sourcegitcommit: 6a99a188bbad491e7d2817de0b9500a27797107e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97658297"
---
# <a name="deploy-an-sdn-infrastructure-using-sdn-express"></a>Bereitstellen einer SDN-Infrastruktur mit SDN Express

> Gilt für Azure Stack HCI, Version 20H2

Unter diesem Thema wird beschrieben, wie Sie eine End-to-End-SDN-Infrastruktur (Softwaredefiniertes Netzwerk) bereitstellen, indem Sie SDN Express-PowerShell-Skripts verwenden. Die Infrastruktur enthält als Komponenten einen Netzwerkcontroller, einen Software Load Balancer (SLB) und ein Gateway, die allesamt hoch verfügbar sind.  

Die Skripts unterstützen eine Bereitstellung in Phasen. Sie können beispielsweise zunächst nur den Netzwerkcontroller bereitstellen, um einen Teil der Funktionen bei sehr niedrigen Netzwerkanforderungen zu erhalten. Sie können den Netzwerkcontroller auch mithilfe des Assistenten zum Erstellen von Clustern in Windows Admin Center bereitstellen. Zum Bereitstellen von anderen SDN-Komponenten, z. B. SLB und Gateway, müssen Sie dann aber die SDN Express-Skripts verwenden.

Darüber hinaus können Sie mit System Center Virtual Machine Manager (VMM) auch eine SDN-Infrastruktur bereitstellen. Weitere Informationen finden Sie unter [Verwalten von SDN-Ressourcen im VMM-Fabric](https://docs.microsoft.com/system-center/vmm/network-sdn).

## <a name="before-you-begin"></a>Voraussetzungen

Bevor Sie mit der SDN-Bereitstellung beginnen, sollten Sie Ihre Infrastruktur für physische Netzwerke und Hostnetzwerke planen und konfigurieren. Lesen Sie die folgenden Artikel:

- [Anforderungen für physische Netzwerke](../concepts/physical-network-requirements.md)
- [Anforderungen für Hostnetzwerke](../concepts/host-network-requirements.md)
- [Erstellen eines Azure Stack HCI-Clusters mithilfe von Windows Admin Center](create-cluster.md)
- [Erstellen eines Azure Stack HCI-Clusters mithilfe von Windows PowerShell](create-cluster-powershell.md)
- [Planen einer softwaredefinierten Netzwerkinfrastruktur](../concepts/plan-software-defined-networking-infrastructure.md)

Sie müssen nicht alle SDN-Komponenten bereitstellen. Anhand der Informationen im Abschnitt [Stufenweise Bereitstellung](../concepts/plan-software-defined-networking-infrastructure.md#phased-deployment) unter [Planen einer softwaredefinierten Netzwerkinfrastruktur](../concepts/plan-software-defined-networking-infrastructure.md) können Sie ermitteln, welche Infrastrukturkomponenten Sie benötigen. Anschließend können Sie die entsprechenden Skripts ausführen.

Stellen Sie sicher, dass auf allen Hostservern das Azure Stack HCI-Betriebssystem installiert ist. Informationen hierzu finden Sie unter [Bereitstellen des Azure Stack HCI-Betriebssystems](operating-system.md).

## <a name="run-the-sdn-express-scripts"></a>Ausführen der SDN Express-Skripts

1. Navigieren Sie zum GitHub-Repository für [SDN Express](https://github.com/microsoft/SDN).

1. Laden Sie die Dateien aus dem Repository auf Ihren Bereitstellungscomputer herunter. Wählen Sie Option **Clone** (Klonen) oder **Download ZIP** (ZIP herunterladen) aus.

    > [!NOTE]
    > Auf dem Bereitstellungscomputer muss Windows Server 2016 oder höher ausgeführt werden.

1. Extrahieren Sie die ZIP-Datei, und kopieren Sie den Ordner `SDNExpress` in den Ordner `C:\` Ihres Bereitstellungscomputers.

1. Navigieren Sie zum Ordner `C:\SDNExpress\scripts`.

1. Führen Sie die Skriptdatei `SDNExpress.ps1` aus. Bei diesem Skript wird eine PowerShell-Bereitstellungsdatei (PSD) als Eingabe für die verschiedenen Konfigurationseinstellungen genutzt. Die Datei `README.md` enthält eine Anleitung zum Ausführen des Skripts.  

1. Verwenden Sie eine VHDX mit dem Azure Stack HCI-Betriebssystem als Quelle für die Erstellung der SDN-VMs. Wenn Sie das Azure Stack HCI-Betriebssystem über ein ISO-Image heruntergeladen und installiert haben, können Sie diese VHDX mit dem Hilfsprogramm `convert-windowsimage` erstellen. Eine Beschreibung finden Sie in der Dokumentation.

1. Passen Sie die Datei `SDNExpress\scripts\MultiNodeSampleConfig.psd1` an, indem Sie die spezifischen Werte für Ihre Infrastruktur entsprechend ändern, z. B. Hostnamen, Domänennamen, Benutzernamen/Kennwörter und Netzwerkinformationen für die aufgeführten Netzwerke. Dies ist unter dem Thema [Planen einer softwaredefinierten Netzwerkinfrastruktur](../concepts/plan-software-defined-networking-infrastructure.md) beschrieben. Diese Datei enthält spezifische Informationen dazu, was Sie angeben müssen. Dies hängt davon ab, ob Sie nur die Netzwerkcontroller-Komponente oder auch die Software Load Balancer- und die Gateway-Komponente bereitstellen.

1. Führen Sie das folgende Skript auf den Hyper-V-Hosts über ein Benutzerkonto mit Administratoranmeldeinformationen aus:

    ```powershell
    SDNExpress\scripts\SDNExpress.ps1 -ConfigurationDataFile MultiNodeSampleConfig.psd1 -Verbose
    ```

1. Konfigurieren Sie nach dem Erstellen der Netzwerkcontroller-VMs dynamische DNS-Updates für den Namen des Netzwerkcontrollerclusters auf dem DNS-Server. Weitere Informationen finden Sie unter [Schritt 3: Konfigurieren der dynamischen DNS-Registrierung für den Netzwerkcontroller](https://docs.microsoft.com/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller#step-3-configure-dynamic-dns-registration-for-network-controller).

## <a name="next-steps"></a>Nächste Schritte

Verwalten Sie Ihre VMs. Weitere Informationen finden Sie unter [Verwalten virtueller Computer mit Windows Admin Center](../manage/vm.md).