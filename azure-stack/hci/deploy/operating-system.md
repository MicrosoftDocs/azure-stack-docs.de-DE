---
title: Bereitstellen des Azure Stack HCI-Betriebssystems
description: Hier erfahren Sie, wie Sie das Azure Stack HCI-Betriebssystem bereitstellen und anschließend Windows Admin Center zum Herstellen einer Verbindung mit Ihren Servern verwenden. Lesen Sie, wie Sie einen Servercluster erstellen und wie Sie die neuesten Windows-Updates und aktuelle Firmware für Ihre Server erhalten.
author: JohnCobb1
ms.author: v-johcob
ms.topic: tutorial
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 12/7/2020
ms.openlocfilehash: 51a4411e95207d2f7b544fdf507fe8bd8fc98f2e
ms.sourcegitcommit: 61556b7b6e029e3a26a4b7ef97f0b13fbe7cd5a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96761710"
---
# <a name="deploy-the-azure-stack-hci-operating-system"></a>Bereitstellen des Azure Stack HCI-Betriebssystems

> Gilt für: Azure Stack HCI, Version 20H2

Der erste Schritt für die Bereitstellung von Azure Stack HCI ist das [Herunterladen von Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) und das Installieren des Betriebssystems auf jedem Server, der Teil des Clusters sein soll. In diesem Artikel werden verschiedene Möglichkeiten zum Bereitstellen des Betriebssystems und zum Verwenden von Windows Admin Center für die Verbindungsherstellung mit den Servern beschrieben.

> [!NOTE]
> Wenn Sie Azure Stack HCI-Lösungshardware für integrierte Systeme über den [Azure Stack HCI-Katalog](https://aka.ms/azurestackhcicatalog) von Ihrem bevorzugten Hardwarepartner erworben haben, sollte das Azure Stack HCI-Betriebssystem vorinstalliert sein. In diesem Fall können Sie diesen Schritt überspringen und mit [Erstellen eines Azure Stack HCI-Clusters](create-cluster.md) fortfahren.

## <a name="prerequisites"></a>Voraussetzungen

Vor dem Bereitstellen des Azure Stack HCI-Betriebssystems müssen Sie folgende Schritte ausführen:

- Ermitteln, ob Ihre Hardware die Systemanforderungen für Azure Stack HCI erfüllt
- Erfassen der erforderlichen Informationen für eine erfolgreiche Bereitstellung
- Installieren von Windows Admin Center auf einem Verwaltungscomputer oder Server

Informationen zu den Anforderungen an Azure Kubernetes Service in Azure Stack HCI finden Sie unter [Was Sie für die ersten Schritte benötigen](../../aks-hci/overview.md#what-you-need-to-get-started).

### <a name="determine-hardware-requirements"></a>Ermitteln der Hardwareanforderungen

Microsoft empfiehlt den Erwerb einer validierten Azure Stack HCI-Hardware-/Softwarelösung von unseren Partnern. Diese Lösungen wurden anhand unserer Referenzarchitektur entworfen, zusammengestellt und validiert und garantieren Kompatibilität und Zuverlässigkeit, sodass Sie sofort loslegen können. Überprüfen Sie, ob die von Ihnen verwendeten Systeme, Komponenten, Geräte und Treiber gemäß Windows Server Catalog für Windows Server 2019 zertifiziert sind. Auf der Website [Azure Stack HCI](https://azure.microsoft.com/overview/azure-stack/hci) finden Sie validierte Lösungen.

Sie benötigen mindestens zwei Server, eine zuverlässige Netzwerkverbindung mit hoher Bandbreite und geringer Wartezeit zwischen Servern sowie SATA-, SAS- oder NVMe-Laufwerke bzw. Laufwerke für persistenten Speicher, die physisch an nur jeweils einen Server angeschlossen sind.

Die Hardwareanforderungen können jedoch je nach Größe und Konfiguration der bereitzustellenden Cluster variieren. Um eine erfolgreiche Bereitstellung sicherzustellen, überprüfen Sie die [Systemanforderungen](../concepts/system-requirements.md) von Azure Stack HCI.

### <a name="gather-information"></a>Sammeln von Informationen

Sammeln Sie zur Vorbereitung der Bereitstellung die folgenden Informationen zu Ihrer Umgebung:

- **Servernamen**: Machen Sie sich mit den Benennungsrichtlinien Ihrer Organisation für Computer, Dateien, Pfade und andere Ressourcen vertraut. Sie werden mehrere Server bereitstellen müssen, von denen jeder einen eindeutigen Namen erhalten muss.
- **Domänenname**: Machen Sie sich mit den Benennungsrichtlinien Ihrer Organisation für die Domänenbenennung und den Domänenbeitritt vertraut. Sie werden die Server in Ihre Domäne einbinden und müssen den Domänennamen angeben.
- **Statische IP-Adressen**: Azure Stack HCI erfordert statische IP-Adressen für den Speicher- und Workloaddatenverkehr (VM) und unterstützt keine dynamische Zuweisung von IP-Adressen durch DHCP für dieses Hochgeschwindigkeitsnetzwerk. Sie können DHCP für den Verwaltungsnetzwerkadapter verwenden. Falls Sie jedoch zwei Adapter in einer Teamkonfiguration einsetzen, müssen Sie wieder statische IP-Adressen verwenden. Fragen Sie Ihren Netzwerkadministrator nach den IP-Adressen, die Sie für die einzelnen Server im Cluster verwenden sollten.
- **RDMA-Netzwerkfunktionen**: Es gibt zwei Arten von RDMA-Protokollen: iWARP und RoCE. Informieren Sie sich darüber, welches Protokoll Ihre Netzwerkadapter verwenden. Wenn es sich um RoCE handelt, beachten Sie auch die Version (v1 oder v2). Im Fall von RoCE müssen Sie auch das Modell Ihres Top-of-Rack-Switchs beachten.
- **VLAN-ID**: Informieren Sie sich über die VLAN-ID, die für die Netzwerkserver auf den Servern verwendet werden soll (sofern vorhanden). Diese Informationen sollten Sie von Ihrem Netzwerkadministrator erhalten.
- **Standortnamen:** Bei Stretchingclustern werden zwei Standorte für die Notfallwiederherstellung verwendet. Standorte können mithilfe von [Active Directory Domain Services](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) oder automatisch vom Clustererstellungs-Assistenten eingerichtet werden. Wenden Sie sich im Zusammenhang mit der Einrichtung von Standorten an Ihren Domänenadministrator.

### <a name="install-windows-admin-center"></a>Installieren von Windows Admin Center

Windows Admin Center ist eine lokal bereitgestellte, browserbasierte App zum Verwalten von Azure Stack HCI. Die einfachste Möglichkeit zum [Installieren von Windows Admin Center](/windows-server/manage/windows-admin-center/deploy/install) ist die Verwendung eines lokalen Verwaltungscomputers (Desktopmodus). Die Installation auf einem Server (Dienstmodus) ist jedoch ebenfalls möglich.

Bei der Installation von Windows Admin Center auf einem Server muss für Aufgaben, die CredSSP erfordern (etwa Clustererstellung sowie Installation von Updates und Erweiterungen), ein Konto verwendet werden, das der Gatewayadministratorgruppe auf dem Windows Admin Center-Server angehört. Weitere Informationen finden Sie in den ersten beiden Abschnitten des Artikels [Konfigurieren der Benutzerzugriffssteuerung und von Berechtigungen](/windows-server/manage/windows-admin-center/configure/user-access-control#gateway-access-role-definitions).

## <a name="prepare-hardware-for-deployment"></a>Vorbereiten der Hardware für die Bereitstellung

Nachdem Sie die Serverhardware für Ihre Azure Stack HCI-Lösung erworben haben, muss sie im Rack angeordnet und verkabelt werden. Führen Sie die folgenden Schritte aus, um die Serverhardware für die Bereitstellung des Betriebssystems vorzubereiten.

1. Ordnen Sie alle Serverknoten, die Teil Ihres Serverclusters sein sollen, in einem Rack an.
1. Verbinden Sie die Serverknoten mit Ihren Netzwerkswitches.
1. Konfigurieren Sie das BIOS bzw. die UEFI (Unified Extensible Firmware Interface) Ihrer Server gemäß der Empfehlung Ihres Azure Stack HCI-Hardwareanbieters, um die Leistung und Zuverlässigkeit zu erhöhen.

## <a name="operating-system-deployment-options"></a>Optionen für die Bereitstellung des Betriebssystems

Sie können das Azure Stack HCI-Betriebssystem genauso bereitstellen, wie Sie dies auch für andere Microsoft-Betriebssysteme tun:

- Vorinstallation des Serverherstellers
- Monitorlose Bereitstellung per Antwortdatei
- System Center Virtual Machine Manager (VMM)
- Netzwerkbereitstellung
- Manuelle Bereitstellung, indem entweder eine Tastatur und ein Monitor direkt an die Serverhardware in Ihrem Rechenzentrum angeschlossen werden, oder indem ein KVM-Hardwaregerät mit der Serverhardware verbunden wird

### <a name="server-manufacturer-pre-installation"></a>Vorinstallation des Serverherstellers

Für die unternehmensweite Bereitstellung des Azure Stack HCI-Betriebssystems empfehlen wir Ihnen die Verwendung von Azure Stack HCI-Lösungshardware für integrierte Systeme von Ihrem bevorzugten Hardwarepartner. Auf der Lösungshardware ist das Betriebssystem bei der Lieferung bereits vorinstalliert, und sie unterstützt die Verwendung von Windows Admin Center, um Treiber und Firmware des Hardwareherstellers bereitstellen und aktualisieren zu können.

Die Lösungshardware kann 2 bis 16 Knoten umfassen und wird von Microsoft und Partneranbietern getestet und überprüft. Informationen zur Azure Stack HCI-Lösungshardware Ihres bevorzugten Hardwarepartners finden Sie im [Azure Stack HCI-Katalog](https://www.microsoft.com/cloud-platform/azure-stack-hci-catalog).

### <a name="headless-deployment"></a>Monitorlose Bereitstellung

Sie können eine Antwortdatei verwenden, um eine monitorlose Bereitstellung des Betriebssystems durchzuführen. Für die Antwortdatei wird ein XML-Format genutzt, um bei einer unbeaufsichtigten Installation des Betriebssystems die Konfigurationseinstellungen und Werte zu definieren.

Für diese Bereitstellungsoption können Sie Windows System Image Manager verwenden, um die Antwortdatei „unattend.xml“ für die Bereitstellung des Betriebssystems auf Ihren Servern zu erstellen. Bei Windows System Image Manager wird Ihre „unattend“-Antwortdatei mit einem grafischen Tool mit Komponentenabschnitten erstellt, um die „Antworten“ auf die Konfigurationsfragen zu definieren und anschließend das richtige Format und die richtige Syntax in der Datei sicherzustellen.
Das Windows System Image Manager-Tool ist über das Windows Assessment and Deployment Kit (Windows ADK) verfügbar. Erste Schritte: [Laden Sie das Windows ADK herunter, und installieren Sie es](/windows-hardware/get-started/adk-install).

### <a name="system-center-virtual-machine-manager-vmm-deployment"></a>Bereitstellung von System Center Virtual Machine Manager (VMM)

Sie können System Center Virtual Machine Manager verwenden, um das Azure Stack HCI-Betriebssystem auf Bare-Metal-Hardware bereitzustellen und die Server in Clustern anzuordnen. Weitere Informationen zu VMM finden Sie unter [Systemanforderungen für System Center Virtual Machine Manager](/system-center/vmm/system-requirements).

Weitere Informationen zur Verwendung von VMM für eine Bare-Metal-Bereitstellung des Betriebssystems finden Sie unter [Bereitstellen eines Hyper-V-Hosts oder -Clusters basierend auf Bare-Metal-Computern](/system-center/vmm/hyper-v-bare-metal).

### <a name="network-deployment"></a>Netzwerkbereitstellung

Eine weitere Möglichkeit ist die Installation des Azure Stack HCI-Betriebssystems über das Netzwerk mit [Windows-Bereitstellungsdiensten](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831764(v=ws.11)).

### <a name="manual-deployment"></a>Manuelle Bereitstellung

Installieren Sie das Betriebssystem mit Ihrem bevorzugten Verfahren, z. B. Starten von einer DVD oder einem USB-Laufwerk, um das Azure Stack HCI-Betriebssystem manuell auf dem Systemlaufwerk der einzelnen Server für den Cluster bereitzustellen. Schließen Sie den Installationsvorgang mit dem Serverkonfigurationstool (Sconfig) ab, um die Server für das Clustering vorzubereiten. Weitere Informationen zu diesem Tool finden Sie unter [Konfigurieren einer Server Core-Installation von Windows Server 2016 oder Windows Server, Version 1709, mit „Sconfig.cmd“](/windows-server/get-started/sconfig-on-ws2016).

Führen Sie die manuelle Installation des Azure Stack HCI-Betriebssystems wie folgt durch:
1. Starten Sie den Assistenten für die Installation von Azure Stack HCI auf dem Systemlaufwerk des Servers, auf dem Sie das Betriebssystem installieren möchten.
1. Wählen Sie die Sprache für die Installation aus, oder übernehmen Sie die Standardeinstellungen für die Sprache. Wählen Sie **Weiter** und dann auf der nächsten Seite des Assistenten die Option **Jetzt installieren** aus.

    :::image type="content" source="../media/operating-system/azure-stack-hci-install-language.png" alt-text="Seite mit Sprachoptionen im Assistenten für die Azure Stack HCI-Installation":::

1. Sehen Sie sich auf der Seite mit den Hinweisen und Lizenzbedingungen die Bedingungen an, aktivieren Sie das Kontrollkästchen **Ich stimme den Lizenzbedingungen zu**, und wählen Sie anschließend **Weiter** aus.
1. Wählen Sie auf der Seite „Welche Installationsart soll gewählt werden?“ die Option **Custom: Install the newer version of Azure Stack HCI only (advanced)** (Benutzerdefiniert: Nur die neuere Version von Azure Stack HCI installieren (erweitert)) aus.

    > [!NOTE]
    > Upgradeinstallationen werden bei diesem Release des Betriebssystems nicht unterstützt.

    :::image type="content" source="../media/operating-system/azure-stack-hci-install-which-type.png" alt-text="Seite für die Auswahl der Installationsart im Assistenten für die Azure Stack HCI-Installation":::

1. Übernehmen bzw. aktualisieren Sie auf der Seite „Where do you want to install Azure Stack HCI?“ (Wo soll Azure Stack HCI installiert werden?) den Laufwerkspeicherort, an dem Sie das Betriebssystem installieren möchten, und wählen Sie dann **Weiter** aus.

    :::image type="content" source="../media/operating-system/azure-stack-hci-install-where.png" alt-text="Seite für den Laufwerkspeicherort im Assistenten für die Azure Stack HCI-Installation":::

1. Die Seite „Installing Azure Stack HCI“ (Azure Stack HCI wird installiert) wird angezeigt, um den Status des Prozesses anzugeben.

    :::image type="content" source="../media/operating-system/azure-stack-hci-installing.png" alt-text="Statusseite im Assistenten für die Azure Stack HCI-Installation":::

    > [!NOTE]
    > Während des Installationsvorgangs wird das Betriebssystem zweimal neu gestartet, um den Prozess abzuschließen, und es werden Hinweise zum Start der Dienste angezeigt, bevor eine Administratoreingabeaufforderung geöffnet wird.

1. Wählen Sie in der Administratoreingabeaufforderung **OK** aus, um das Kennwort des Benutzers zu ändern, bevor Sie sich beim Betriebssystem anmelden. Drücken Sie anschließend die EINGABETASTE.

    :::image type="content" source="../media/operating-system/azure-stack-hci-change-admin-password.png" alt-text="Aufforderung zum Ändern des Kennworts":::

1. Geben Sie an der Eingabeaufforderung „Enter new credential for Administrator“ (Neue Anmeldeinformationen für Administrator eingeben) ein neues Kennwort ein, und geben Sie es zur Bestätigung dann erneut ein. Drücken Sie anschließend die EINGABETASTE.
1. Drücken Sie in der Bestätigungsanzeige „Ihr Kennwort wurde geändert“ die EINGABETASTE.

    :::image type="content" source="../media/operating-system/azure-stack-hci-admin-password-changed.png" alt-text="Bestätigung der Kennwortänderung":::

Sie können das Serverkonfigurationstool (Sconfig) jetzt nutzen, um wichtige Aufgaben durchzuführen. Melden Sie sich zur Verwendung von Sconfig bei dem Server an, auf dem das Azure Stack HCI-Betriebssystem ausgeführt wird. Hierfür können Sie lokal eine Tastatur und einen Monitor verwenden, oder Sie können einen Controller für die Remoteverwaltung (monitorlos oder BMC) oder eine Remotedesktopverbindung nutzen. Das Tool „Sconfig“ wird automatisch geöffnet, wenn Sie sich beim Server anmelden.

:::image type="content" source="../media/operating-system/azure-stack-hci-sconfig-screen.png" alt-text="Benutzeroberfläche des Serverkonfigurationstools" lightbox="../media/operating-system/azure-stack-hci-sconfig-screen.png":::

Auf der Hauptseite des Tools „Sconfig“ können Sie die folgenden ersten Konfigurationsaufgaben durchführen:
- Konfigurieren des Netzwerks bzw. Sicherstellen, dass das Netzwerk per DHCP (Dynamic Host Configuration Protocol) automatisch konfiguriert wurde
- Umbenennen des Servers, falls Sie den automatisch generierten Standardservernamen ändern möchten
- Einbinden des Servers in eine Active Directory-Domäne
- Hinzufügen Ihres Domänenbenutzerkontos oder der festgelegten Domänengruppe zu den lokalen Administratoren
- Aktivieren des Zugriffs auf die Windows-Remoteverwaltung (Windows Remote Management, WinRM), wenn Sie den Server außerhalb des lokalen Subnetzes verwalten und den Domänenbeitritt noch nicht durchführen möchten (Die Firewall-Standardregeln ermöglichen die Verwaltung sowohl über das lokale Subnetz als auch über alle Subnetze Ihrer Active Directory Domain Services-Instanz.)

Nachdem Sie das Betriebssystem mit Sconfig wie gewünscht auf allen Servern konfiguriert haben, können Sie den Assistenten für die Clustererstellung in Windows Admin Center verwenden, um die Server im Cluster anzuordnen.

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Durchführung der nächsten Verwaltungsaufgabe im Anschluss dieses Artikels finden Sie unter:
> [!div class="nextstepaction"]
> [Erstellen eines Azure Stack HCI-Clusters](../deploy/create-cluster.md)
