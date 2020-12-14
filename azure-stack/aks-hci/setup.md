---
title: 'Schnellstart: Einrichten von Azure Kubernetes Service unter Azure Stack HCI mit Windows Admin Center'
description: Es wird beschrieben, wie Sie Azure Kubernetes Service unter Azure Stack HCI mit Windows Admin Center einrichten.
author: davannaw-msft
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: dawhite
ms.openlocfilehash: 99f3e98ff24044c74c9065bb98965dcf640c4032
ms.sourcegitcommit: 0efffe1d04a54062a26d5c6ce31a417f511b9dbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96612538"
---
# <a name="quickstart-set-up-azure-kubernetes-service-on-azure-stack-hci-using-windows-admin-center"></a>Schnellstart: Einrichten von Azure Kubernetes Service unter Azure Stack HCI mit Windows Admin Center

> Gilt für: Azure Stack HCI, Windows Server 2019 Datacenter

In dieser Schnellstartanleitung richten Sie Azure Kubernetes Service unter Azure Stack HCI mit Windows Admin Center ein. Falls Sie hierfür PowerShell verwenden möchten, helfen Ihnen die Informationen unter [Schnellstart: Einrichten eines Azure Kubernetes Service-Hosts für Azure Stack HCI mit PowerShell](setup-powershell.md) weiter.

Die Einrichtung umfasst die folgenden Aufgaben:

* Herunterladen von Azure Kubernetes Service unter Azure Stack HCI
* Einrichten von Windows Admin Center, falls Sie dies noch nicht durchgeführt haben
* Installieren der Azure Kubernetes Service-Erweiterung für Windows Admin Center
* Einrichten eines Azure Kubernetes Service-Hosts auf dem System, auf dem Sie den Kubernetes-Cluster bereitstellen möchten

Stellen Sie auf der Seite [Systemanforderungen](.\system-requirements.md) zunächst sicher, dass alle Voraussetzungen erfüllt sind.

## <a name="download-azure-kubernetes-service-on-azure-stack-hci"></a>Herunterladen von Azure Kubernetes Service unter Azure Stack HCI

Wenn Sie die Vorschausoftware noch nicht heruntergeladen haben, finden Sie weitere Informationen unter [Evaluieren von AKS in Azure Stack HCI](https://aka.ms/AKS-HCI-Evaluate). Sie werden aufgefordert, AKS in Azure Stack HCI sowie Windows Admin Center herunterzuladen.

## <a name="setting-up-windows-admin-center"></a>Einrichten von Windows Admin Center

Falls Sie Windows Admin Center noch nicht installiert haben, helfen Ihnen die Informationen unter [Installieren von Windows Admin Center](/windows-server/manage/windows-admin-center/deploy/install) weiter. Um die öffentliche Vorschauversion von Azure Kubernetes Service unter Azure Stack HCI zu erhalten, müssen Sie Windows Admin Center auf einem Windows 10-Computer herunterladen und ausführen. Die Funktionalität für Azure Kubernetes Service unter Azure Stack HCI ist nur für Windows Admin Center-Builds der Version 2009 oder höher verfügbar.

## <a name="installing-the-azure-kubernetes-service-extension"></a>Installieren der Azure Kubernetes Service-Erweiterung

Nachdem Sie die Dateien für die öffentliche Vorschauversion von Azure Kubernetes Service unter Azure Stack HCI erhalten haben, müssen Sie die `.nupkg`-Datei lokal oder auf einer SMB-Freigabe speichern und den Dateipfad in Ihrem Windows Admin Center-Manager für Erweiterungen der Liste „Feeds“ hinzufügen. Bei der `.nupkg`-Datei handelt es sich um ein NuGet-Paket, das die Windows Admin Center-Erweiterung enthält.

Öffnen Sie zum Zugreifen auf Ihren vorhandenen Erweiterungsfeed Windows Admin Center, und wählen Sie oben rechts auf dem Bildschirm das Zahnradsymbol aus. Das Menü „Einstellungen“ wird geöffnet. Die Erweiterungsfeeds finden Sie im Abschnitt **Gateway** im Menü **Erweiterungen**. Navigieren Sie zur Registerkarte **Feeds**, und wählen Sie **Hinzufügen** aus. Fügen Sie in diesem Bereich den Dateipfad zu Ihrer Kopie der Azure Kubernetes Service-Erweiterung ein, und wählen Sie **Hinzufügen** aus. Wenn das Hinzufügen Ihres Dateipfads erfolgreich war, erhalten Sie eine entsprechende Benachrichtigung. 

Nachdem wir nun den Feed hinzugefügt haben, wird die Azure Kubernetes Service-Erweiterung in der Liste mit den verfügbaren Erweiterungen angezeigt. Wählen Sie nach dem Auswählen der Erweiterung oben in der Tabelle die Option **Installieren** aus, um diese Erweiterung zu installieren. Windows Admin Center wird nach Abschluss der Installation erneut geladen. 

[ ![Liste mit den verfügbaren Erweiterungen im Windows Admin Center-Manager für Erweiterungen](.\media\setup\extension-manager.png) ](.\media\setup\extension-manager.png#lightbox)

## <a name="setting-up-an-azure-kubernetes-service-host"></a>Einrichten eines Azure Kubernetes Service-Hosts

Vor dem Erstellen eines Kubernetes-Clusters müssen Sie noch einen letzten Schritt ausführen. Sie müssen einen Azure Kubernetes Service-Host auf dem System einrichten, auf dem Sie den Kubernetes-Cluster bereitstellen möchten. Dieses System kann ein Windows Server 2019 Datacenter-Cluster, ein Windows Server 2019 Datacenter-Cluster mit einem Knoten oder ein Azure Stack HCI-Cluster mit zwei bis vier Knoten sein. 

> [!NOTE] 
> Das Einrichten von Azure Kubernetes Service-Hosts auf zwei unabhängigen Systemen, um diese beim Erstellen des Kubernetes-Clusters zusammenzuführen, wird als Szenario nicht unterstützt. 

Sie können diese Einrichtung mit dem neuen Azure Kubernetes Service-Tool durchführen. 

Mit diesem Tool werden die erforderlichen Pakete installiert und heruntergeladen, und es wird ein Verwaltungscluster erstellt, in dem die wichtigen Kubernetes-Dienste bereitgestellt und die Anwendungsworkloads orchestriert werden. 


Nun, da wir unsere Systemeinstellungen überprüft haben, beginnen wir: 
1. Wählen Sie **Einrichten** aus, um den Assistenten für die Einrichtung zu starten.
2. Überprüfen Sie die Voraussetzungen für den Computer, auf dem Sie Windows Admin Center ausführen, für den Cluster, mit dem Sie verbunden sind, sowie für das Netzwerk. Vergewissern Sie sich außerdem, dass Sie bei einem Azure-Konto im Windows Admin Center angemeldet sind und dass das Azure-Abonnement, das Sie verwenden möchten, nicht abgelaufen ist. Wählen Sie **Weiter**, wenn Sie fertig sind.
3. Führen Sie auf der Seite **System checks** (Systemüberprüfungen) des Assistenten alle erforderlichen Aktionen durch, z. B. das Herstellen einer Verbindung für Ihr Windows Admin Center-Gateway mit Azure. In diesem Schritt wird überprüft, ob Windows Admin Center und das System, auf dem Azure Kubernetes Service gehostet wird, über die richtigen Konfigurationen zur Fortsetzung des Vorgangs verfügen. Wählen Sie **Weiter** aus, nachdem Sie alle Aktionen durchgeführt haben.
4. Konfigurieren Sie den Computer, auf dem Azure Kubernetes Service gehostet wird, im Schritt **Hostkonfiguration**. Wir empfehlen Ihnen, in diesem Abschnitt die Option zum **automatischen Herunterladen von Updates** auszuwählen. Wählen Sie nach Abschluss des Vorgangs **Weiter** aus. In diesem Schritt des Assistenten werden Sie aufgefordert, die folgenden Details zu konfigurieren:
    * Hostdetails, z. B. ein Name für den Verwaltungscluster und ein Ordner zum Speichern von VM-Images
    * VM-Netzwerk, das für alle Linux- und Windows-VMs (Knoten) gilt, die zum Ausführen von Containern und Orchestrieren der Containerverwaltung erstellt werden 
    * Einstellungen für den Lastenausgleich, mit dem der Pool mit den Adressen für externe Dienste definiert wird

    ![Abbildung: Schritt „Hostkonfiguration“ im Assistenten für den Azure Kubernetes Service-Host](.\media\setup\host-configuration.png)

5. Überprüfen Sie im Schritt **Überprüfen und erstellen** alle Optionen, die Sie ausgewählt haben. Wenn Sie mit Ihrer Auswahl zufrieden sind, wählen Sie **Weiter** aus, um mit der Einrichtung des Hosts zu beginnen. 
6. Auf der Seite **Setup progress** (Setupstatus) können Sie den Status der Hosteinrichtung verfolgen. Sie können Windows Admin Center nun in einer neuen Registerkarte öffnen und mit der Durchführung Ihrer Verwaltungsaufgaben fortfahren. 
7. Wenn die Bereitstellung erfolgreich ist, wird ein Verwaltungsdashboard angezeigt, in dem Sie Ihre Kubernetes-Cluster erstellen und verwalten können. Dieses Dashboard ist wie der Rest von Azure Kubernetes Services auf Azure Stack HCI eine Vorschauversion und wird in zukünftigen Releases mit zusätzlichen Funktionen aktualisiert. 
 
  ![Zeigt das Verwaltungsdashboard von Azure Kubernetes Service auf Azure Stack HCI.](.\media\setup\dashboard.png)
 
## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie Windows Admin Center und die Azure Kubernetes Service-Erweiterung installiert. Außerdem haben Sie einen Azure Kubernetes Service-Host auf dem System konfiguriert, für das Sie die Bereitstellung eines Kubernetes-Clusters planen.

Nun können Sie das [Erstellen eines Kubernetes-Clusters in Windows Admin Center](create-kubernetes-cluster.md) durchführen.
