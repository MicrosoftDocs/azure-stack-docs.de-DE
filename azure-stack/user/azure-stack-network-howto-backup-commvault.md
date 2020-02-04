---
title: Sichern Ihrer VM in Azure Stack Hub mit Commvault
description: Hier erfahren Sie, wie Sie Ihre VM in Azure Stack Hub mit Commvault sichern.
author: mattbriggs
ms.topic: how-to
ms.date: 10/30/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/30/2019
ms.openlocfilehash: aa4e5d10534f2ffe4c9b6cc948fcb7968213aab0
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884643"
---
# <a name="back-up-your-vm-on-azure-stack-hub-with-commvault"></a>Sichern Ihres virtuellen Computers in Azure Stack Hub mit Commvault

## <a name="overview-of-backing-up-a-vm-with-commvault"></a>Übersicht über das Sichern eines virtuellen Computers mit Commvault

In diesem Artikel wird beschrieben, wie Sie Commvault Live Sync konfigurieren, um einen virtuellen Computer für die Wiederherstellung zu aktualisieren, der sich in einer separaten Azure Stack Hub-Skalierungseinheit befindet. In diesem Artikel wird ausführlich erläutert, wie eine allgemeine Partnerlösung konfiguriert wird, um die Daten und den Systemstatus von in Azure Stack Hub bereitgestellten virtuellen Computern zu schützen und wiederherzustellen.

In der folgenden Abbildung ist die allgemeine Lösung bei der Verwendung von Commvault zum Sichern Ihrer virtuellen Computer dargestellt.

![](./media/azure-stack-network-howto-backup-commvault/bcdr-commvault-overall-arc.png)

In diesem Artikel werden die folgenden Themen behandelt:

1. Erstellen eines virtuellen Computers, auf dem die Commvault-Software ausgeführt wird, in Ihrer Azure Stack Hub-Quellinstanz

2. Erstellen eines Speicherkontos an einem sekundären Speicherort. In diesem Artikel wird davon ausgegangen, dass Sie einen Blobcontainer in einem Speicherkonto in einer separaten Azure Stack Hub-Instanz (Ziel) erstellen und dass die Azure Stack Hub-Zielinstanz über die Azure Stack Hub-Quellinstanz erreichbar ist.

3. Konfigurieren von Commvault für die Azure Stack Hub-Quellinstanz und Hinzufügen der virtuellen Computer in der Azure Stack Hub-Zielinstanz zur VM-Gruppe

4. Konfigurieren von Commvault Live Sync

Sie können auch kompatible Partner-VM-Images herunterladen und bereitstellen, um Ihre virtuellen Azure Stack Hub-Computer in einer Azure-Cloud oder einer anderen Azure Stack Hub-Instanz zu schützen. In diesem Artikel wird der Schutz der virtuellen Computer mithilfe von Commvault Live Sync erläutert.

Die Topologie dieses Ansatzes ist in der folgenden Abbildung dargestellt:

![](./media/azure-stack-network-howto-backup-commvault/backup-vm-commvault-diagram.png)

## <a name="create-the-commvault-vm-form-the-commvault-marketplace-item"></a>Erstellen des virtuellen Commvault-Computers über das Commvault-Marketplace-Element

1. Öffnen Sie das Azure Stack Hub-Benutzerportal.

2. Wählen Sie **Ressource erstellen** > **Compute** > **Commvault** aus.

    > [!Note]  
    > Wenn Commvault nicht zur Verfügung steht, wenden Sie sich an Ihren Cloudbetreiber.

    ![](./media/azure-stack-network-howto-backup-commvault/commvault-create-vm-01.png)

3. Konfigurieren Sie die Grundeinstellungen unter **Virtuellen Computer erstellen, 1 Grundlagen**:

    a. Geben Sie einen **Namen** ein.

    b. Wählen Sie **HDD Standard** aus.
    
    c. Geben Sie einen **Benutzernamen** ein.
    
    d. Geben Sie ein **Kennwort** ein.
    
    e. Bestätigen Sie das Kennwort.
    
    f. Wählen Sie ein **Abonnement** für die Sicherung aus.
    
    g. Wählen Sie eine **Ressourcengruppe** aus.
    
    h. Wählen Sie den **Speicherort** der Azure Stack Hub-Instanz aus. Wählen Sie bei Verwendung des ASDK die Option **Lokal** aus.
    
    i. Klicken Sie auf **OK**.

    ![](./media/azure-stack-network-howto-backup-commvault/commvault-create-vm-02.png)

4. Wählen Sie die Größe des virtuellen Commvault-Computers aus. Die VM-Größe für die Sicherung sollte mindestens 10 GB RAM und 100 GB Speicher betragen.

    ![](./media/azure-stack-network-howto-backup-commvault/commvault-create-vm-03.png).

5. Wählen Sie die Einstellungen für den virtuellen Commvault-Computer aus.

    a. Legen Sie Verfügbarkeit auf **Keine** fest.
    
    b. Wählen Sie **Ja** für „Verwaltete Datenträger verwenden“ aus.
    
    c. Wählen Sie das Standard-VNET als **virtuelles Netzwerk** aus.
    
    d. Wählen Sie das standardmäßige **Subnetz** aus.
    
    e. Wählen Sie die standardmäßige **öffentliche IP-Adresse** aus.
    
    f. Belassen Sie den virtuellen Computer in der Netzwerksicherheitsgruppe **Standard**.
    
    g. Öffnen Sie die Ports HTTP (80), HTTPS (443), SSH (22) und RDP (3389).
    
    h. Wählen Sie **Keine Erweiterungen** aus.
    
    i. Wählen Sie unter **Startdiagnose** die Option **Aktiviert** aus.
    
    j. Behalten Sie unter **Diagnose des Gastbetriebssystems** die Option **Deaktiviert** bei.
    
    k. Behalten Sie das standardmäßige **Diagnosespeicherkonto** bei.
    
    l. Klicken Sie auf **OK**.

6. Überprüfen Sie nach der entsprechenden erfolgreichen Validierung die Zusammenfassung des virtuellen Commvault-Computers. Klicken Sie auf **OK**.

## <a name="get-your-service-principal"></a>Abrufen des Dienstprinzipals

Sie müssen wissen, ob Azure AD oder AD FS als Identity Manager festgelegt ist. In der folgenden Tabelle sind die Informationen aufgeführt, die Sie zum Einrichten von Commvault in Ihrer Azure Stack Hub-Instanz benötigen.

| Element | Beschreibung | `Source` |
|--------------------------|--------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Resource Manager-URL | Der Resource Manager-Endpunkt von Azure Stack Hub. | https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-ruby?view=azs-1908#the-azure-stack-hub-resource-manager-endpoint |
| Anwendungsname |  |  |
| Anwendungs-ID | Die Anwendungs-ID des Dienstprinzipals, die beim Erstellen des Dienstprinzipals im vorherigen Abschnitt dieses Artikels gespeichert wurde. | https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals?view=azs-1908 |
| Abonnement-ID | Sie verwenden die Abonnement-ID, um in Azure Stack Hub auf Angebote zuzugreifen. | https://docs.microsoft.com/azure-stack/operator/service-plan-offer-subscription-overview?view=azs-1908#subscriptions |
| Mandanten-ID (Verzeichnis-ID) | Ihre Mandanten-ID für Azure Stack Hub. | https://docs.microsoft.com/azure-stack/operator/azure-stack-identity-overview?view=azs-1908 |
| Anwendungskennwort | Das App-Geheimnis des Dienstprinzipals, das bei der Erstellung des Dienstprinzipals gespeichert wurde. | https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals?view=azs-1908 |

## <a name="configure-backup-using-the-commvault-console"></a>Konfigurieren der Sicherung über die Commvault-Konsole

1. Öffnen Sie den RDP-Client, und stellen Sie eine Verbindung mit dem virtuellen Commvault-Computer in Ihrer Azure Stack Hub-Instanz her. Geben Sie Ihre Anmeldeinformationen ein.

2. Installieren Sie Azure Stack Hub PowerShell und Azure Stack Hub-Tools auf dem virtuellen Commvault-Computer.

    a. Anweisungen zum Installieren von Azure Stack Hub PowerShell finden Sie unter [Installieren von PowerShell für Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fbreadcrumb%2Ftoc.json).  
    b. Anweisungen zum Installieren von Azure Stack Hub-Tools finden Sie unter [Herunterladen von Azure Stack Hub-Tools von GitHub](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-download?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fuser%2FTOC.json%3Fview%3Dazs-1908&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fbreadcrumb%2Ftoc.json%3Fview%3Dazs-1908&view=azs-1908).

3. Öffnen Sie nach der Installation von Commvault auf dem virtuellen Commvault-Computer die CommCell Console. Wählen Sie unter „Start“ die Option **Commvault** > **Commvault CommCell Console** aus.

    ![](./media/azure-stack-network-howto-backup-commvault/commcell-console.png)

4. Konfigurieren Sie die Sicherungsrepositorys so, dass externer Speicher von Azure Stack Hub in der Commvault CommCell Console verwendet wird. Wählen Sie im CommCell-Browser die Optionen „Storage Resources“ (Speicherressourcen) > „Storage Pools“ (Speicherpools) aus. Klicken Sie mit der rechten Maustaste, und wählen Sie **Add Storage Pool** (Speicherpool hinzufügen) aus. Wählen Sie **Cloud** aus.

5. Geben Sie den Namen des Speicherpools ein. Wählen Sie **Weiter** aus.

6. Wählen Sie **Create** > **Cloud Storage** (Erstellen > Cloudspeicher) aus.

    ![](./media/azure-stack-network-howto-backup-commvault/commcell-storage-add-storage-device.png)

7. Wählen Sie Ihren Cloud-Dienstanbieter aus. In diesem Verfahren wählen Sie eine zweite Azure Stack Hub-Instanz an einem anderen Speicherort aus. Wählen Sie „Microsoft Azure Storage“ aus.

8. Wählen Sie den virtuellen Commvault-Computer als MediaAgent aus.

9. Geben Sie Ihre Zugriffsinformationen für das Speicherkonto ein. Anweisungen zum Einrichten eines Azure Storage-Kontos finden Sie hier. Zugriffsinformationen:

    -  **Diensthost**: Der Name der URL stammt aus den Eigenschaften des Blobcontainers in der Ressource. Die Beispiel-URL lautete https:\//backuptest.blob.westus.stackpoc.com/mybackups and I used, blob.westus.stackpoc.com auf dem Diensthost.
    
    -   **Kontoname:** Verwenden Sie den Namen des Speicherkontos. Diesen finden Sie auf dem Blatt „Zugriffsschlüssel“ in der Speicherressource.
    
    -   **Zugriffsschlüssel**: Den Zugriffsschlüssel finden Sie auf dem Blatt „Zugriffsschlüssel“ in der Speicherressource.
    
    -   **Container**: Der Name des Containers, in diesem Fall „mybackups“.
    
    -   **Speicherklasse:** Behalten Sie die Standardspeicherklasse des Benutzercontainers bei.

10. Erstellen Sie einen Microsoft Azure Stack Hub-Client, indem Sie die Anweisungen unter [Creating a Microsoft Azure Hub Stack Client](https://documentation.commvault.com/commvault/v11_sp13/article?p=86495.htm) (Erstellen eines Microsoft Azure Stack Hub-Clients) befolgen.

    ![](./media/azure-stack-network-howto-backup-commvault/commcell-ceate-client.png)

11. Wählen Sie die zu schützenden virtuellen Computer oder Ressourcengruppen aus, und fügen Sie eine Sicherungsrichtlinie hinzu.

12. Konfigurieren Sie den Sicherungszeitplan entsprechend Ihren RPO-Anforderungen für die Wiederherstellung.

13. Führen Sie die erste vollständige Sicherung aus.

## <a name="configure-commvault-live-sync"></a>Konfigurieren von Commvault Live Sync 

Für die Konfiguration haben Sie zwei Möglichkeiten. Sie können die Änderungen von der primären Kopie der Sicherungen oder von einer sekundären Kopie auf dem virtuellen Computer für die Wiederherstellung replizieren. Durch die Replikation von einem Sicherungssatz werden die Auswirkungen von E/A-Lesevorgängen auf dem Quellcomputer unterbunden.

1. Bei der Konfiguration von Live Sync müssen Sie die Details zur Azure Stack Hub-Quellinstanz (Agent des virtuellen Servers) und zur Azure Stack Hub-Zielinstanz angeben.

2. Die entsprechenden Schritte zum Konfigurieren von Commvault Live Sync finden Sie unter [Live Sync Replication for Microsoft Azure Stack Hub](https://documentation.commvault.com/commvault/v11_sp13/article?p=94386.htm) (Live Sync-Replikation für Microsoft Azure Stack Hub).

    ![](./media/azure-stack-network-howto-backup-commvault/live-sync-1.png)
 
3. Bei der Konfiguration von Live Sync müssen Sie die Details zur Azure Stack Hub-Zielinstanz und zum Agent des virtuellen Servers angeben.

    ![](./media/azure-stack-network-howto-backup-commvault/live-sync-2.png)

4. Setzen Sie die Konfiguration fort, und fügen Sie das Zielspeicherkonto, in dem die Replikatdatenträger gehostet werden, die Ressourcengruppen, in denen die virtuellen Replikatcomputer gespeichert werden, und den Namen, der an die virtuellen Replikatcomputer angefügt werden soll, hinzu.

    ![](./media/azure-stack-network-howto-backup-commvault/live-sync-3.png)

5. Sie können außerdem die VM-Größe ändern und die Netzwerkeinstellungen konfigurieren, indem Sie neben den einzelnen virtuellen Computern **Konfigurieren** auswählen.

6. Legen Sie die Häufigkeit der Replikation auf der Azure Stack Hub-Zielinstanz fest.

    ![](./media/azure-stack-network-howto-backup-commvault/live-sync-5.png)

7. Überprüfen Sie die Einstellungen, und speichern Sie die Konfiguration. Nun wird die Wiederherstellungsumgebung erstellt und die Replikation im ausgewählten Intervall gestartet.


## <a name="set-up-failover-behavior-using-live-sync"></a>Einrichten des Failoververhaltens mithilfe von Live Sync

In Commvault Live Sync können Sie ein Failover für Computer zwischen verschiedenen Azure Stack Hub-Instanzen sowie ein Failback ausführen, um Vorgänge auf der ursprünglichen Azure Stack Hub-Instanz fortzusetzen. Der Workflow wird automatisiert und protokolliert.

![](./media/azure-stack-network-howto-backup-commvault/back-up-live-sync-panel.png)

Wählen Sie die virtuellen Computer aus, für die ein Failover auf der Azure Stack Hub-Instanz für die Wiederherstellung ausgeführt werden soll, und legen Sie fest, ob ein geplantes oder ungeplantes Failover ausgeführt werden soll. Ein geplantes Failover ist angebracht, wenn Zeit bleibt, um die Produktionsumgebung ordnungsgemäß herunterzufahren, bevor der Betrieb am Wiederherstellungsstandort wieder aufgenommen wird. Bei einem geplanten Failover werden die virtuellen Produktionscomputer heruntergefahren, endgültige Änderungen am Wiederherstellungsstandort repliziert sowie die virtuellen Computer für die Wiederherstellung mit den neuesten Daten online geschaltet und die bei der Konfiguration von Live Sync angegebene VM-Größe und Netzwerkkonfiguration angewandt. Bei einem ungeplanten Failover wird versucht, die virtuellen Produktionscomputer herunterzufahren. Wenn die Produktionsumgebung nicht verfügbar ist, wird der Vorgang jedoch fortgesetzt, um lediglich die virtuellen Computer für die Wiederherstellung mit dem zuletzt empfangenen auf den virtuellen Computer angewandten Replikationsdataset sowie der zuvor ausgewählten Größe und Netzwerkkonfiguration online zu schalten. In den folgenden Abbildungen wird ein ungeplantes Failover gezeigt, bei dem die virtuellen Computer für die Wiederherstellung mithilfe von Commvault Live Sync online geschaltet wurden.

![](./media/azure-stack-network-howto-backup-commvault/unplanned-failover.png)

![](./media/azure-stack-network-howto-backup-commvault/fail-over-2.png)

![](./media/azure-stack-network-howto-backup-commvault/fail-over-3.png)

## <a name="next-steps"></a>Nächste Schritte

[Azure Stack Hub-Netzwerke: Unterschiede und Überlegungen](azure-stack-network-differences.md)  