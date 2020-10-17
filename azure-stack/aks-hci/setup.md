---
title: 'Schnellstart: Einrichten von Azure Kubernetes Service unter Azure Stack HCI mit Windows Admin Center'
description: Es wird beschrieben, wie Sie Azure Kubernetes Service unter Azure Stack HCI mit Windows Admin Center einrichten.
author: davannaw-msft
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: dawhite
ms.openlocfilehash: 2f442928c01c59bc85eb672153b2f5b21a653ef1
ms.sourcegitcommit: 362081a8c19e7674c3029c8a44d7ddbe2deb247b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91899719"
---
# <a name="quickstart-set-up-azure-kubernetes-service-on-azure-stack-hci-using-windows-admin-center"></a>Schnellstart: Einrichten von Azure Kubernetes Service unter Azure Stack HCI mit Windows Admin Center

> Gilt für: Azure Stack HCI

In dieser Schnellstartanleitung richten Sie Azure Kubernetes Service unter Azure Stack HCI mit Windows Admin Center ein. Falls Sie hierfür PowerShell verwenden möchten, helfen Ihnen die Informationen unter [Schnellstart: Einrichten eines Azure Kubernetes Service-Hosts für Azure Stack HCI mit PowerShell](setup-powershell.md) weiter.

Die Einrichtung umfasst die folgenden Aufgaben:

* Einrichten von Windows Admin Center, falls Sie dies noch nicht durchgeführt haben
* Installieren der Azure Kubernetes Service-Erweiterung für Azure Stack HCI für Windows Admin Center
* Einrichten eines Azure Kubernetes Service-Hosts auf dem System, auf dem Sie den Kubernetes-Cluster bereitstellen möchten

Stellen Sie auf der Seite [Systemanforderungen](.\system-requirements.md) zunächst sicher, dass alle Voraussetzungen erfüllt sind.

## <a name="setting-up-windows-admin-center"></a>Einrichten von Windows Admin Center

Falls Sie Windows Admin Center noch nicht installiert haben, helfen Ihnen die Informationen unter [Installieren von Windows Admin Center](/windows-server/manage/windows-admin-center/deploy/install) weiter. Um die öffentliche Vorschauversion von Azure Kubernetes Service unter Azure Stack HCI zu erhalten, müssen Sie Windows Admin Center auf einem Windows 10-Computer herunterladen und ausführen. Derzeit ist nur der Desktopmodus von Windows Admin Center mit Azure Kubernetes Service unter Azure Stack HCI kompatibel. Die Funktionalität für Azure Kubernetes Service unter Azure Stack HCI ist nur für Windows Admin Center-Builds der Version 2009 oder höher verfügbar.

## <a name="installing-the-azure-kubernetes-service-extension"></a>Installieren der Azure Kubernetes Service-Erweiterung

Nachdem Sie die Dateien für die öffentliche Vorschauversion von Azure Kubernetes Service unter Azure Stack HCI erhalten haben, müssen Sie die `.nupkg`-Datei lokal oder auf einer SMB-Freigabe speichern und den Dateipfad in Ihrem Windows Admin Center-Manager für Erweiterungen der Liste „Feeds“ hinzufügen. Bei der `.nupkg`-Datei handelt es sich um ein NuGet-Paket, das die Windows Admin Center-Erweiterung enthält.

Öffnen Sie zum Zugreifen auf Ihren vorhandenen Erweiterungsfeed Windows Admin Center, und wählen Sie oben rechts auf dem Bildschirm das Zahnradsymbol aus. Das Menü „Einstellungen“ wird geöffnet. Die Erweiterungsfeeds finden Sie im Abschnitt **Gateway** im Menü **Erweiterungen**. Navigieren Sie zur Registerkarte **Feeds**, und wählen Sie **Hinzufügen** aus. Fügen Sie in diesem Bereich den Dateipfad zu Ihrer Kopie der Azure Kubernetes Service-Erweiterung für Azure Stack HCI ein, und wählen Sie **Hinzufügen** aus. Wenn das Hinzufügen Ihres Dateipfads erfolgreich war, erhalten Sie eine entsprechende Benachrichtigung. 

Nachdem wir nun den Feed hinzugefügt haben, wird die Azure Kubernetes Service-Erweiterung für Azure Stack HCI in der Liste mit den verfügbaren Erweiterungen angezeigt. Wählen Sie nach dem Auswählen der Erweiterung oben in der Tabelle die Option **Installieren** aus, um diese Erweiterung zu installieren. Windows Admin Center wird nach Abschluss der Installation erneut geladen. 

[ ![Liste mit den verfügbaren Erweiterungen im Windows Admin Center-Manager für Erweiterungen](.\media\setup\extension-manager.png) ](.\media\setup\extension-manager.png#lightbox)

## <a name="setting-up-an-azure-kubernetes-service-host"></a>Einrichten eines Azure Kubernetes Service-Hosts

Vor dem Erstellen eines Kubernetes-Clusters müssen Sie noch einen letzten Schritt ausführen. Sie müssen einen Azure Kubernetes Service-Host auf dem System einrichten, auf dem Sie den Kubernetes-Cluster bereitstellen möchten. Bei diesem System muss es sich um einen Azure Stack HCI-Cluster handeln. 

> [!NOTE] 
> Das Einrichten von Azure Kubernetes Service-Hosts auf zwei unabhängigen Systemen, um diese beim Erstellen des Kubernetes-Clusters zusammenzuführen, wird als Szenario nicht unterstützt. 

Sie können diese Einrichtung mit dem neuen Azure Kubernetes Service-Tool durchführen. 

Mit diesem Tool werden die erforderlichen Pakete installiert und heruntergeladen, und es wird ein Verwaltungscluster erstellt, in dem die wichtigen Kubernetes-Dienste bereitgestellt und die Anwendungsworkloads orchestriert werden. 

Bevor Sie dieses Tool verwenden, öffnen Sie PowerShell, und führen Sie den folgenden Befehl auf jedem Knoten aus, um sicherzustellen, dass die Azure-Anmeldung von keinen Computereinstellungen blockiert wird:
```PowerShell
az login
```

Nun, da wir unsere Systemeinstellungen überprüft haben, beginnen wir: 
1. Wählen Sie **Einrichten** aus, um den Assistenten für die Einrichtung zu starten.
2. Überprüfen Sie die Voraussetzungen für den Computer, auf dem Sie Windows Admin Center ausführen, für den Azure Stack HCI-Cluster, mit dem Sie verbunden sind, sowie für das Netzwerk. Vergewissern Sie sich außerdem, dass Sie bei einem Azure-Konto im Windows Admin Center angemeldet sind und dass das Azure-Abonnement, das Sie verwenden möchten, nicht abgelaufen ist. Wählen Sie **Weiter**, wenn Sie fertig sind.
3. Führen Sie auf der Seite **System checks** (Systemüberprüfungen) des Assistenten alle erforderlichen Aktionen durch, z. B. das Herstellen einer Verbindung für Ihr Windows Admin Center-Gateway mit Azure. Bei diesem Schritt wird überprüft, ob Windows Admin Center und das System, auf dem Azure Kubernetes Service gehostet wird, über die richtigen Konfigurationen zur Fortsetzung des Vorgangs verfügen. Wählen Sie **Weiter** aus, nachdem Sie alle Aktionen durchgeführt haben.
4. Konfigurieren Sie den Computer, auf dem Azure Kubernetes Service gehostet wird, im Schritt **Hostkonfiguration**. Wir empfehlen Ihnen, in diesem Abschnitt die Option zum **automatischen Herunterladen von Updates** auszuwählen. Wählen Sie nach Abschluss des Vorgangs **Weiter** aus. In diesem Schritt des Assistenten werden Sie aufgefordert, die folgenden Details zu konfigurieren:
    * Hostdetails, z. B. ein Name für den Verwaltungscluster und ein Ordner zum Speichern von VM-Images
    * VM-Netzwerk, das für alle Linux- und Windows-VMs (Knoten) gilt, die zum Ausführen von Containern und Orchestrieren der Containerverwaltung erstellt werden 
    * Einstellungen für den Lastenausgleich, mit dem der Pool mit den Adressen für externe Dienste definiert wird

    ![Abbildung: Schritt „Hostkonfiguration“ im Assistenten für den Azure Kubernetes Service-Host](.\media\setup\host-configuration.png)

5. Führen Sie die Registrierung bei Azure durch, und wählen Sie im Schritt **Azure-Registrierung** aus, dass Diagnosedaten an Microsoft gesendet werden sollen. Auf dieser Seite wird zwar nach einem Azure-Abonnement und einer Ressourcengruppe gefragt, aber für die Einrichtung und Verwendung von Azure Kubernetes Service fallen in der öffentlichen Vorschauphase keine Kosten an. Die von Ihnen an Microsoft gesendeten Diagnosedaten werden verwendet, um den Dienst sicher und auf dem neuesten Stand zu halten, Probleme zu beheben und Produktverbesserungen durchzuführen. Wählen Sie **Weiter** aus, nachdem Sie Ihre Auswahl getroffen haben.
6. Überprüfen Sie im Schritt **Überprüfen und erstellen** alle Optionen, die Sie bisher ausgewählt haben. Wenn Sie mit Ihrer Auswahl zufrieden sind, wählen Sie **Einrichten** aus, um mit der Einrichtung des Hosts zu beginnen. 
7. Auf der Seite **Setup progress** (Setupstatus) können Sie den Status der Hosteinrichtung verfolgen. Sie können Windows Admin Center nun in einer neuen Registerkarte öffnen und mit der Durchführung Ihrer Verwaltungsaufgaben fortfahren. 
8. Wenn die Bereitstellung erfolgreich ist, werden die nächsten Schritte angezeigt, und die Schaltfläche **Fertig stellen** wird aktiviert. Wenn Sie unter **Nächste Schritte** die Option **Download management cluster kubeconfig** (kubeconfig für Verwaltungscluster herunterladen) auswählen, wird ein Download gestartet. Sie befinden sich aber weiterhin im Assistenten. 

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie Windows Admin Center und die Azure Kubernetes Service-Erweiterung für Azure Stack HCI installiert. Außerdem haben Sie einen Azure Kubernetes Service-Host auf dem System konfiguriert, für das Sie die Bereitstellung eines Kubernetes-Clusters planen.

Nun können Sie das [Erstellen eines Kubernetes-Clusters in Windows Admin Center](create-kubernetes-cluster.md) durchführen.