---
title: Erstellen eines virtuellen Computers mit einer Communityvorlage
description: Hier erfahren Sie, wie Sie das ASDK (Azure Stack Development Kit) zum Erstellen eines virtuellen Computers mit einer vordefinierten Vorlage und einer benutzerdefinierten GitHub-Vorlage verwenden.
author: sethmanheim
ms.topic: tutorial
ms.date: 01/23/2020
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/13/2019
ms.openlocfilehash: 9e8df158444b0cbb2b6efaea6f99766f36b26241
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77704334"
---
# <a name="tutorial-create-a-vm-using-a-community-template"></a>Tutorial: Erstellen eines virtuellen Computers mit einer Communityvorlage

Als Azure Stack Hub-Betreiber oder -Benutzer können Sie einen virtuellen Computer (Virtual Machine, VM) erstellen, indem Sie [benutzerdefinierte GitHub-Schnellstartvorlagen](https://github.com/Azure/AzureStack-QuickStart-Templates) verwenden, anstatt eine Vorlage manuell über den Azure Stack Hub-Marketplace bereitzustellen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Verwenden von Azure Stack Hub-Schnellstartvorlagen
> * Erstellen einer VM mit einer benutzerdefinierten GitHub-Vorlage
> * Starten von Minikube und Installieren einer Anwendung

## <a name="azure-stack-hub-quickstart-templates"></a>Azure Stack Hub-Schnellstartvorlagen

Azure Stack Hub-Schnellstartvorlagen werden auf GitHub im [globalen Repository für Azure Stack Hub-Schnellstartvorlagen](https://github.com/Azure/AzureStack-QuickStart-Templates) gespeichert. Dieses Repository enthält Azure Resource Manager-Bereitstellungsvorlagen, die mit dem Microsoft Azure Stack Development Kit (ASDK) getestet wurden. Sie können sie verwenden, um Ihnen das Auswerten von Azure Stack Hub und das Verwenden der ASDK-Umgebung zu erleichtern.

Im Laufe der Zeit haben viele GitHub-Benutzer einen Beitrag zum Repository geleistet, wodurch eine Sammlung mit mehr als 400 Bereitstellungsvorlagen entstanden ist. Dieses Repository ist ein guter Ausgangspunkt, um ein Verständnis dafür zu entwickeln, wie Sie verschiedene Arten von Umgebungen in Azure Stack Hub bereitstellen können.

>[!IMPORTANT]
> Einige dieser Vorlagen wurden von Mitgliedern der Community und nicht von Microsoft erstellt. Jede Vorlage wird von dessen Besitzer, nicht von Microsoft, unter einem Lizenzvertrag lizenziert. Microsoft ist für diese Vorlagen nicht verantwortlich und führt dafür keine Tests in Bezug auf die Sicherheit, Kompatibilität oder Leistung durch. Vorlagen aus der Community werden von keinem Microsoft-Supportprogramm oder -Dienst unterstützt und „wie besehen“ ohne jegliche Gewährleistung zur Verfügung gestellt.

Wenn Sie Azure Resource Manager-Vorlagen für GitHub beitragen möchten, stellen Sie sie im Repository [AzureStack-QuickStart-Templates](https://github.com/Azure/AzureStack-QuickStart-Templates) bereit. Weitere Informationen zu diesem Repository und zu Beiträgen dafür finden Sie in der [Infodatei](https://aka.ms/aa6zktg).

## <a name="create-a-vm-using-a-custom-github-template"></a>Erstellen einer VM mit einer benutzerdefinierten GitHub-Vorlage

In diesem Beispieltutorial wird die Azure Stack Hub-Schnellstartvorlage [101-vm-linux-minikube](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-linux-minikube) verwendet, um einen virtuellen Ubuntu 16.04-Computer unter Azure Stack Hub bereitzustellen, auf dem Minikube zum Verwalten eines Kubernetes-Clusters ausgeführt wird.

Minikube ist ein Tool, mit dem die lokale Ausführung von Kubernetes leicht möglich ist. Minikube führt einen Kubernetes-Cluster mit nur einem Knoten auf einer VM aus, wodurch Sie Kubernetes ausprobieren oder damit tägliche Entwicklungsarbeiten leisten können. Das Tool unterstützt einen einfachen Kubernetes-Cluster mit einem Knoten, der auf einer Linux-VM ausgeführt wird. Minikube ist die schnellste und einfachste Möglichkeit zum Ausführen eines voll funktionsfähigen Kubernetes-Clusters. Entwickler können hiermit ihre Bereitstellungen von Kubernetes-basierten Anwendungen auf ihren lokalen Computern entwickeln und testen. Im Hinblick auf die Architektur führt der virtuelle Minikube-Computer sowohl Master- als auch Agent-Knotenkomponenten lokal aus:

* Masterknotenkomponenten (etwa API-Server, Scheduler und [etcd-Server](https://coreos.com/etcd/)) werden in einem einzelnen Linux-Prozess namens **LocalKube** ausgeführt.
* Agent-Knotenkomponenten werden in Docker-Containern ausgeführt, wie dies auch auf einem normalen Agent-Knoten der Fall wäre. Aus Sicht der Anwendungsbereitstellung besteht kein Unterschied darin, ob die Anwendung in einem Minikube-Cluster oder einem regulären Kubernetes-Cluster bereitgestellt wird.

Mit dieser Vorlage werden die folgenden Komponenten installiert:

* Ubuntu 16.04 LTS VM
* [Docker-CE](https://download.docker.com/linux/ubuntu)
* [Kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.8.0/bin/linux/amd64/kubectl)
* [Minikube](https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64)
* xFCE4
* xRDP

> [!IMPORTANT]
> Das Ubuntu-VM-Image (hier: Ubuntu Server 16.04 LTS) muss dem Azure Stack Hub-Marketplace bereits hinzugefügt worden sein, bevor Sie diese Schritte ausführen.

1. Wählen Sie **+ Ressource erstellen** > **Benutzerdefiniert** > **Vorlagenbereitstellung** aus.

    ![Erstellen einer Vorlage](media/azure-stack-create-vm-template/1.PNG)

2. Wählen Sie **Vorlage bearbeiten** aus.

    ![Bearbeiten einer Vorlage](media/azure-stack-create-vm-template/2.PNG)

3. Wählen Sie **Schnellstartvorlage** aus.

    ![Schnellstartvorlage](media/azure-stack-create-vm-template/3.PNG)

4. Wählen Sie aus den verfügbaren Vorlagen die Option **101-vm-linux-minikube** aus, indem Sie die Dropdownliste **Vorlage auswählen** verwenden und dann auf **OK** klicken.

    ![Vorlage auswählen](media/azure-stack-create-vm-template/4.PNG)

5. Sie können auch Änderungen an der JSON-Vorlagendatei vornehmen. Wenn Sie die Datei nicht ändern möchten oder mit der Bearbeitung fertig sind, wählen Sie **Speichern**, um das Dialogfeld **Vorlage bearbeiten** zu schließen.

    ![Vorlage speichern](media/azure-stack-create-vm-template/5.PNG)

6. Wählen Sie **Parameter** aus, füllen Sie je nach Bedarf die verfügbaren Felder aus (bzw. ändern Sie sie), und klicken Sie anschließend auf **OK**.

    ![Parameter](media/azure-stack-create-vm-template/6.PNG)

7. Wählen Sie das gewünschte Abonnement aus, und erstellen oder wählen Sie einen vorhandenen Ressourcengruppennamen. Wählen Sie dann **Erstellen** aus, um die Vorlagenbereitstellung zu starten.

    ![Auswählen eines Abonnements](media/azure-stack-create-vm-template/7.PNG)

8. Die Bereitstellung der Ressourcengruppe dauert mehrere Minuten, um die benutzerdefinierte vorlagenbasierte VM zu erstellen. Sie können den Installationsstatus anhand von Benachrichtigungen und über die Ressourcengruppeneigenschaften überwachen.

    ![Bereitstellen](media/azure-stack-create-vm-template/8.PNG)

    >[!NOTE]
    > Die VM wird ausgeführt, wenn die Bereitstellung abgeschlossen ist.

## <a name="start-minikube-and-install-an-application"></a>Starten von Minikube und Installieren einer Anwendung

Nachdem der virtuelle Linux-Computer nun erfolgreich erstellt wurde, können Sie sich anmelden, um Minikube zu starten und eine Anwendung zu installieren.

1. Wählen Sie nach Abschluss der Bereitstellung **Verbinden** aus, um die öffentliche IP-Adresse anzuzeigen, die zum Herstellen der Verbindung mit dem virtuellen Linux-Computer verwendet werden soll.

    ![Verbinden](media/azure-stack-create-vm-template/9.PNG)

2. Führen Sie an einer Eingabeaufforderung mit erhöhten Rechten **mstsc.exe** aus, um die Remotedesktopverbindung zu öffnen und eine Verbindung mit der öffentlichen IP-Adresse des virtuellen Linux-Computers herzustellen, die im vorherigen Schritt ermittelt wurde. Wenn Sie zum Anmelden bei xRDP aufgefordert werden, verwenden Sie die Anmeldeinformationen, die Sie bei der Erstellung der VM angegeben haben.

    ![Remote](media/azure-stack-create-vm-template/10.PNG)

3. Öffnen Sie die Terminalemulation, und geben Sie die folgenden Befehle ein, um Minikube zu starten:

    ```shell
    sudo minikube start --vm-driver=none
    sudo minikube addons enable dashboard
    sudo minikube dashboard --url
    ```

    ![Ausführung von Befehlen](media/azure-stack-create-vm-template/11.PNG)

4. Öffnen Sie einen Browser, und rufen Sie die Adresse des Kubernetes-Dashboards auf. Herzlichen Glückwunsch! Sie verfügen nun über eine voll funktionsfähige Kubernetes-Installation mit Minikube.

    ![Dashboard](media/azure-stack-create-vm-template/12.PNG)

5. Navigieren Sie zum Bereitstellen einer Beispielanwendung zur offiziellen Seite mit der Kubernetes-Dokumentation, und überspringen Sie den Abschnitt „Create Minikube Cluster“ (Minikube-Cluster erstellen), da Sie diesen bereits erstellt haben. Fahren Sie mit dem Abschnitt „Create your Node.js application“ (Node.js-Anwendung erstellen) unter https://kubernetes.io/docs/tutorials/stateless-application/hello-minikube/ fort.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Verwenden von Azure Stack Hub-Schnellstartvorlagen
> * Erstellen einer VM mit einer benutzerdefinierten GitHub-Vorlage
> * Starten von Minikube und Installieren einer Anwendung
