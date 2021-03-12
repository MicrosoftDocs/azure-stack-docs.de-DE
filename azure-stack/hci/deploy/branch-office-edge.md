---
title: Filialen- und Edgebereitstellungsszenarios in der Azure Stack HCI
description: In diesem Artikel erhalten Sie eine Anleitung, wie Sie Filialen- und Edgebereitstellungsszenarios im Azure Stack HCI-Betriebssystem planen, konfigurieren und durchführen.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 01/21/2021
ms.openlocfilehash: ee9fef4a7da2312876eff168573b1a26bea0290b
ms.sourcegitcommit: f194f9ca4297864500e62d8658674a0625b29d1d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102186839"
---
# <a name="deploy-branch-office-and-edge-on-azure-stack-hci"></a>Filialen- und Edgebereitstellungsszenarios in der Azure Stack HCI

>Gilt für: Azure Stack HCI, Version 20H2

In diesem Artikel erhalten Sie eine Anleitung, wie Sie Filialen- und Edgebereitstellungsszenarios im Azure Stack HCI-Betriebssystem planen, konfigurieren und durchführen. In diesem Leitfaden erfährt Ihre Organisation, wie komplexe und hochverfügbare Workloads auf VMs und in Containern in Remotebereitstellungsszenarios in Filialen und am Edge ausführt werden können. Beim Computing am Edge wird der Großteil der Datenverarbeitung aus einem zentralisierten System an den Edge des Netzwerks verlagert, also in die Nähe eines Geräts oder Systems, für das die Daten schnell verfügbar sein müssen.

Mithilfe der Azure Stack HCI können Sie virtualisierte Anwendungen und Workloads mit Hochverfügbarkeit auf empfohlener Hardware ausführen. Die Hardware unterstützt Cluster, die aus zwei Servern bestehen, die mit geschachtelter Resilienz für Speicher, einem einfachen und kostengünstigen Clusterzeugen für einen USB-Stick und Verwaltung über eine browserbasierte Windows Admin Center-Instanz konfiguriert sind. Details zum Erstellen eines Clusterzeugen für ein USB-Gerät finden Sie unter [Bereitstellen eines Dateifreigabenzeugen](/windows-server/failover-clustering/file-share-witness).

Azure IoT Edge verschiebt Cloudanalysen und benutzerdefinierte Geschäftslogik auf Geräte, damit Sie sich auf geschäftliche Erkenntnisse konzentrieren können und sich nicht mit der Datenverwaltung befassen müssen. Azure IoT Edge kombiniert KI, Cloud- und Edgecomputing in containerisierten Cloudworkloads, z. B. Azure Cognitive Services, Machine Learning, Stream Analytics und Azure Functions. Workloads können auf Geräten wie einem Raspberry Pi oder auf einem konvergierten Edgeserver ausgeführt werden. Sie verwenden [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub), um Ihre Edgeanwendungen und -geräte zu verwalten.

Wenn Sie Azure IoT Edge zu Ihren Azure Stack HCI-Filialen- und Edgebereitstellungen hinzufügen, wird Ihre Umgebung so modernisiert, dass [CI-/CD-Pipelines](/azure/iot-edge/how-to-continuous-integration-continuous-deployment) als Anwendungsbereitstellungsframework unterstützt werden. DevOps-Personal in Ihrer Organisation kann containerisierte Anwendungen bereitstellen und durchlaufen, die vom IT-Team erstellt und unterstützt werden. Dafür werden herkömmliche VM-Verwaltungsprozesse und entsprechende Tools verwendet.

Hauptfeatures von Azure IoT Edge:
- Open-Source-Software von Microsoft
- Ausführung unter Windows oder Linux
- Ausführung am Edge für Antworten in Quasi-Echtzeit
- Sichere Software- und Hardwaremethoden
- Verfügbarkeit im [KI-Toolkit für Azure IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge)
- Unterstützung offener Programmierbarkeit für folgende Sprachen: Java, .NET Core 2.0, Node.js, C und Python
- Unterstützung für Offlinekonnektivität und zeitweilige Konnektivität
- Native Verwaltung in Azure IoT Hub

Weitere Informationen finden Sie unter [Was ist Azure IoT Edge?](/azure/iot-edge/about-iot-edge)

## <a name="deploy-branch-office-and-edge"></a>Filialen- und Edgebereitstellungen
In diesem Abschnitt wird allgemein erläutert, wie Sie Hardware für Filialen- und Edgebereitstellungen in der Azure Stack HCI erwerben und Windows Admin Center zum Verwalten verwenden. Außerdem erfahren Sie, wie Azure IoT Edge bereitgestellt wird, um Container in der Cloud zu verwalten.

### <a name="step-1-acquire-hardware-from-the-azure-stack-hci-catalog"></a>Schritt 1: Erwerb von Hardware aus dem Azure Stack HCI-Katalog
Zuerst müssen Sie Hardware beschaffen. Die einfachste Möglichkeit besteht darin, Ihren bevorzugten Microsoft-Hardwarepartner im [Azure Stack HCI-Katalog](https://hcicatalog.azurewebsites.net) zu suchen und ein integriertes System mit vorinstalliertem Azure Stack HCI-Betriebssystem zu kaufen. Im-Katalog können Sie filtern, um Herstellerhardware anzuzeigen, die für diese Art Workload optimiert ist.

Andernfalls müssen Sie das Azure Stack HCI-Betriebssystem auf Ihrer eigenen Hardware bereitstellen. Details zu den Azure Stack HCI-Bereitstellungsoptionen und zum Installieren von Windows Admin Center finden Sie unter [Bereitstellen des Azure Stack HCI-Betriebssystems](./operating-system.md).

Verwenden Sie als nächstes das Windows Admin Center, um [einen Azure Stack HCI-Cluster zu erstellen](./create-cluster.md).

### <a name="step-2-set-up-azure-monitor-in-windows-admin-center"></a>Schritt 2: Einrichten von Azure Monitor in Windows Admin Center
Richten Sie in Windows Admin Center Azure Monitor ein, um Erkenntnisse zur Anwendung, zum Netzwerk und zur Serverintegrität Ihrer Azure Stack HCI-Filialen- und Edgebereitstellung zu erhalten.

Weitere Informationen finden Sie unter [Überwachen der Azure Stack HCI mit Azure Monitor](../manage/azure-monitor.md).

Sie können Windows Admin Center auch verwenden, um zusätzliche Azure-Hybriddienste wie Azure Backup, Azure-Dateisynchronisierung, Site Recovery, ein Point-to-Site-VPN, Updateverwaltung und Security Center einzurichten.

### <a name="step-3-use-container-based-apps-and-iot-data-processing"></a>Schritt 3: Verwenden von containerbasierten Apps und IoT-Datenverarbeitung
Sie sind jetzt bereit dazu, moderne containerbasierte Anwendungsentwicklung und IoT-Datenverarbeitung zu nutzen. Verwenden Sie Windows Admin Center für die Schritte in diesem Abschnitt, um eine VM bereitzustellen, die Azure IoT Edge ausführt.

Weitere Informationen finden Sie unter [Was ist Azure IoT Edge?](/azure/iot-edge/about-iot-edge)

So stellen Sie Azure IoT Edge in der Azure Stack HCI bereit:
1. Verwenden Sie Windows Admin Center, um [eine neue VM in der Azure Stack HCI zu erstellen](/windows-server/manage/windows-admin-center/use/manage-virtual-machines#create-a-new-virtual-machine).

    Weitere Informationen zu unterstützten Betriebssystemversionen, VM-Typen, Prozessorarchitekturen und Systemanforderungen finden Sie unter [Von Azure IoT Edge unterstützte Systeme](/azure/iot-edge/support).

1. Falls Sie noch nicht über ein Azure-Konto verfügen, können Sie sich für ein [kostenloses Konto](https://azure.microsoft.com/free) registrieren.
1. Erstellen Sie im Azure-Portal eine [Azure IoT Hub-Instanz](/azure/iot-edge/quickstart#create-an-iot-hub).
1. Registrieren Sie ein [IoT Edge-Gerät über das Azure-Portal](/azure/iot-edge/quickstart#register-an-iot-edge-device).

    >[!NOTE]
    > Das IoT Edge-Gerät befindet sich auf einer VM, die entweder Windows oder Linux in der Azure Stack HCI ausführt.

1. [Installieren und starten Sie auf der in Schritt 1 erstellten VM die IoT Edge-Runtime.](/azure/iot-edge/quickstart#install-and-start-the-iot-edge-runtime)

   >[!IMPORTANT]
   > Sie benötigen die Gerätezeichenfolge, die Sie in Schritt 4 erstellt haben, um die Runtime mit Azure IoT Hub zu verbinden.

1. [Stellen Sie ein Modul in Azure IoT Edge bereit.](/azure/iot-edge/quickstart#deploy-a-module)

    Im Abschnitt [IoT Edge-Module](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) im Azure Marketplace finden Sie vorgefertigte Module, die Sie bereitstellen können.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Filialen und Edge sowie zu Azure IoT Edge finden Sie in den folgenden Artikeln:
- [Schnellstart: Bereitstellen Ihres ersten IoT Edge-Moduls auf einem virtuellen Linux-Gerät](/azure/iot-edge/quickstart-linux?preserve-view=true&view=iotedge-2018-06)
- [Schnellstart: Deploy your first IoT Edge module to a Windows device (Bereitstellen Ihres ersten IoT Edge-Moduls auf einem Windows-Gerät)](/azure/iot-edge/quickstart?preserve-view=true&view=iotedge-2018-06)