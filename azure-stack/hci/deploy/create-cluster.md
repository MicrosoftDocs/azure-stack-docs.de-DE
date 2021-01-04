---
title: Erstellen eines Azure Stack HCI-Clusters mithilfe von Windows Admin Center
description: Hier erfahren Sie, wie Sie mithilfe von Windows Admin Center einen Servercluster für Azure Stack HCI erstellen.
author: v-dasis
ms.topic: how-to
ms.date: 12/11/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: e33096b2667ad9d620e942b66934f341982e619b
ms.sourcegitcommit: 79e8df69b139bfa21eb83aceb824b97e7f418c03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97364216"
---
# <a name="create-an-azure-stack-hci-cluster-using-windows-admin-center"></a>Erstellen eines Azure Stack HCI-Clusters mithilfe von Windows Admin Center

> Gilt für Azure Stack HCI, Version v20H2

In diesem Artikel erfahren Sie, wie Sie Windows Admin Center verwenden, um einen Azure Stack HCI-Cluster zu erstellen, der direkte Speicherplätze verwendet. Der Clustererstellungs-Assistent in Windows Admin Center nimmt Ihnen den größten Teil der schweren Arbeit ab. Wenn Sie das lieber selbst mithilfe von PowerShell erledigen möchten, lesen Sie [Erstellen eines Azure Stack HCI-Clusters mithilfe von PowerShell](create-cluster-powershell.md). Der PowerShell-Artikel enthält auch nützliche Informationen zu den Vorgängen, die im Hintergrund des Assistenten ausgeführt werden, sowie zur Problembehandlung.

Beim Erstellen haben Sie die Wahl zwischen zwei Clustertypen:

- Standardcluster mit mindestens zwei Serverknoten, die an einem einzelnen Standort betrieben werden.
- Stretchingcluster mit mindestens vier Serverknoten, die sich über zwei Standorte erstrecken und pro Standort mindestens zwei Knoten aufweisen.

Weitere Informationen zu Stretchingclustern finden Sie unter [Stretchingcluster: Übersicht](../concepts/stretched-clusters.md).

Wenn Sie Azure Stack HCI testen möchten, aber nur wenig oder keine Ersatzhardware haben, lesen Sie den [Evaluierungsleitfaden für Azure Stack HCI](https://github.com/Azure/AzureStackHCI-EvalGuide/blob/main/README.md). Dort wird Schritt für Schritt erläutert, wie Sie Azure Stack HCI unter Verwendung der geschachtelten Virtualisierung in Azure oder auf einem lokalen physischen System ausprobieren können.

## <a name="before-you-run-the-wizard"></a>Vor dem Ausführen des Assistenten

Stellen Sie vor dem Ausführen des Clustererstellungs-Assistenten sicher, dass die folgenden Punkte erledigt wurden:

- Sie haben die Hardware- und sonstige Anforderungen unter [Systemanforderungen](../concepts/system-requirements.md) gelesen.
- Sie haben die [Anforderungen an das physische Netzwerk](../concepts/physical-network-requirements.md) und [Host Netzwerk Anforderungen](../concepts/host-network-requirements.md) für Azure Stack HCI gelesen.
- Sie haben das Azure Stack HCI-Betriebssystem auf jedem Server im Cluster installiert. Mehr dazu finden Sie unter [Bereitstellen des Azure Stack HCI-Betriebssystems](operating-system.md).
- Auf jedem Server ist ein Konto vorhanden, das Mitglied der lokalen Administratorgruppe ist.
- Sie haben Windows Admin Center auf einem Verwaltungscomputer oder -server installiert. Weitere Informationen finden Sie unter [Installieren von Windows Admin Center](/windows-server/manage/windows-admin-center/deploy/install).
- Sie haben für Stretchingcluster vorab zwei Standorte in Active Directory eingerichtet. Falls nicht, kann der Assistent sie für Sie einrichten.

Wenn Sie Windows Admin Center nicht auf einem lokalen PC, sondern auf einem Server ausführen, verwenden Sie ein Konto, das Mitglied der Gatewayadministratorgruppe oder der lokalen Administratorgruppe auf dem Windows Admin Center-Server ist.

Außerdem muss Ihr Windows Admin Center-Verwaltungscomputer der Active Directory-Domäne, in der Sie den Cluster erstellen, oder einer voll vertrauenswürdigen Domäne hinzugefügt werden. Die Server, die Sie im Cluster gruppieren möchten, müssen noch nicht zur Domäne gehören; sie können der Domäne während der Clustererstellung hinzugefügt werden.

Dies sind die wichtigsten Schritte im Clustererstellungs-Assistenten:

1. **Get Started** (Erste Schritte): Stellt sicher, dass jeder Server die Voraussetzungen erfüllt und die Funktionen besitzt, die für den Beitritt zum Cluster erforderlich sind.
1. **Networking** (Netzwerk): Weist Netzwerkadapter zu, konfiguriert diese und erstellt die virtuellen Switches für jeden Server.
1. **Clustering**: Überprüft, ob der Cluster ordnungsgemäß eingerichtet ist. Richtet bei Stretchingclustern außerdem die zwei Standorte ein.
1. **Storage** (Speicher): Konfiguriert Direkte Speicherplätze.

Nach dem Abschluss des Assistenten richten Sie den Clusterzeugen ein, registrieren sich bei Azure und erstellen Volumes (wodurch auch die Replikation zwischen Standorten eingerichtet wird, wenn Sie einen Stretchingcluster erstellen).

Stellen Sie vor dem Starten des Assistenten sicher, dass die neuesten Windows Admin Center-Erweiterungen installiert sind, insbesondere die Erweiterung für die Clustererstellung. Gehen Sie folgendermaßen vor:

1. Öffnen Sie Windows Admin Center, und klicken Sie oben rechts auf „Einstellungen“ (Zahnradsymbol).
1. Wählen Sie unter **Einstellungen** die Option **Erweiterungen** aus.
1. Wählen Sie **Cluster Creation** (Clustererstellung) aus, und klicken Sie dann auf **Installieren**.
1. Wählen Sie **Cluster Manager** (Cluster-Manager) aus, und klicken Sie dort dann auf **Installieren**.

Jetzt können Sie loslegen:

1. Klicken Sie in Windows Admin Center unter **Alle Verbindungen** auf **Hinzufügen**.
1. Wählen Sie im Bereich **Ressourcen hinzufügen oder erstellen** unter **Windows Server-Cluster** die Option **Neu erstellen** aus.
1. Klicken Sie unter **1. Clustertyp auswählen** auf **Azure Stack HCI**.

    :::image type="content" source="media/cluster/create-cluster-type.png" alt-text="Clustererstellungs-Assistent: HCI-Option" lightbox="media/cluster/create-cluster-type.png":::

1. Wählen Sie unter **Serverstandorte auswählen** eine der folgenden Optionen aus:

    - **Alle Server an einem Standort**
    - **Server an zwei Standorten** (für Stretchingcluster)

1. Klicken Sie auf **Erstellen**, wenn Sie fertig sind. Jetzt wird der Clustererstellungs-Assistent angezeigt, wie unten dargestellt.

    :::image type="content" source="media/cluster/create-cluster-wizard.png" alt-text="Clustererstellungs-Assistent: Erste Schritte" lightbox="media/cluster/create-cluster-wizard.png":::

## <a name="step-1-get-started"></a>Schritt 1: Erste Schritte

In Schritt 1 des Assistenten werden Sie durch die Überprüfung der Voraussetzungen, das Hinzufügen der Serverknoten, das Installieren der erforderlichen Features und bei Bedarf den Neustart der einzelnen Server geführt.

1. Lesen Sie **1.1 Check the prerequisites** (Voraussetzungen überprüfen) im Assistenten, um sicherzustellen, dass alle Serverknoten clusterfähig sind. Klicken Sie abschließend auf **Weiter**.
1. Geben Sie auf der Seite **1.2 Add servers** (Server hinzufügen) den Benutzernamen und das Kennwort Ihres Kontos ein, und klicken Sie dann auf **Weiter**. Bei dem Konto muss es sich um ein Mitglied der lokalen Gruppe „Administratoren“ auf jedem Server handeln.
1. Geben Sie den Namen des ersten hinzuzufügenden Servers ein, und klicken Sie dann auf **Hinzufügen**.
1. Wiederholen Sie Schritt 3 für jeden Server, der Teil des Clusters sein soll. Klicken Sie abschließend auf **Weiter**.
1. Bei Bedarf geben Sie unter **1.3 Join a domain** (Domäne beitreten) die Domäne an, mit der die Server verknüpft werden sollen, sowie das zu verwendende Konto. Optional können Sie die Server umbenennen. Klicken Sie dann auf **Weiter**.
1. Überprüfen Sie unter **1.4 Install features** (Funktionen installieren) die ausgewählten Funktionen, und fügen Sie ggf. weitere hinzu. Klicken Sie abschließend auf **Weiter**.

    Der Assistent installiert die folgenden erforderlichen Features:

    - BitLocker
    - Data Center Bridging (für RoCEv2-Netzwerkadapter)
    - Failoverclustering
    - Dateiserver
    - FS-Datendeduplizierungsmodul
    - Hyper-V
    - RSAT-AD-PowerShell-Modul
    - Speicherreplikat (wird für Stretched Cluster installiert)

1. Klicken Sie unter **1.5 Install updates** (Updates installieren) ggf. auf **Install updates** (Updates installieren), um Betriebssystemupdates zu installieren. Klicken Sie auf **Weiter**, wenn Sie fertig sind.
1. Klicken Sie unter **1.6 Install hardware updates** (Hardwareupdates installieren) ggf. auf **Get updates** (Updates abrufen), um verfügbare Updates des Hardwareherstellers abzurufen.
1. Befolgen Sie die herstellerspezifischen Schritte zum Installieren der Updates auf Ihrer Hardware. Diese Schritte umfassen die Durchführung von Symmetrie- und Konformitätsprüfungen für Ihre Hardware, um sicherzustellen, dass das Update erfolgreich angewendet wird. Möglicherweise müssen Sie einige Schritte erneut ausführen.
1. Klicken Sie bei Bedarf unter **1.7 Server neu starten** auf **Server neu starten**. Überzeugen Sie sich, dass jeder Server erfolgreich gestartet wurde.

## <a name="step-2-networking"></a>Schritt 2: Netzwerk

Schritt 2 des Assistenten führt Sie durch die Konfiguration von virtuellen Switches, Netzwerkadaptern und anderen Netzwerkelementen für Ihren Cluster. RDMA-Netzwerkadapter (iWARP und RoCE) werden unterstützt.

Weitere Informationen zu RDMA- und Hyper-V-Hostnetzwerken für Azure Stack HCI finden Sie unter [Anforderungen für Hostnetzwerke](../concepts/host-network-requirements.md).

> [!NOTE]
> Wenn beim Konfigurieren des Netzwerks oder virtuellen Switches Fehler angezeigt werden, wählen Sie erneut **Apply and test** (Übernehmen und testen) aus.

1. Klicken Sie auf **Weiter: Netzwerk**.
1. Warten Sie unter **2.1 Check network adapters** (Überprüfen der Netzwerkadapter), bis grüne Kontrollkästchen neben den einzelnen Adaptern angezeigt werden, und wählen Sie dann **Weiter** aus.

1. Wählen Sie unter **2.2 Select management adapters** (Verwaltungsadapter auswählen) einen oder zwei Verwaltungsadapter für jeden Server aus. Die Auswahl wenigstens eines der Adapter für Verwaltungszwecke ist obligatorisch, da der Assistent mindestens eine dedizierte physische NIC für die Clusterverwaltung erfordert.  Sobald ein Adapter für die Verwaltung zugewiesen wurde, wird er aus dem restlichen Ablauf des Assistenten ausgeschlossen.

    :::image type="content" source="media/cluster/create-cluster-management-adapters.png" alt-text="Clustererstellungs-Assistent: Verwaltungsadapter auswählen" lightbox="media/cluster/create-cluster-management-adapters.png":::

    Für Verwaltungsadapter gibt es zwei Konfigurationsoptionen:

    - **Ein physischer Netzwerkadapter für die Verwaltung**. Für diese Option werden sowohl DHCP als auch die Zuweisung statischer IP-Adressen unterstützt.

    - **Zwei physische Netzwerkadapter zur gemeinsamen Verwaltung**. Wenn zwei Adapter kombiniert werden, wird nur die Zuweisung statischer IP-Adressen unterstützt. Wenn die ausgewählten Adapter die DHCP-Adressierung verwenden (für einen oder beide Adapter), werden die DHCP-IP-Adressen vor der Erstellung des virtuellen Switches in statische IP-Adressen konvertiert.

    Bei der Verwendung kombinierter Adapter haben Sie eine einzelne Verbindung mit mehreren Switches, verwenden aber nur eine einzelne IP-Adresse. Damit wird Lastenausgleich verfügbar, und es wird sofortige Fehlertoleranz erreicht, statt die Aktualisierung von DNS-Einträgen abwarten zu müssen.

    Führen Sie nun für jeden Server die folgenden Schritte aus:

    - Aktivieren Sie das Kontrollkästchen **Beschreibung**. Beachten Sie, dass alle Adapter ausgewählt sind und der Assistent Ihnen möglicherweise eine Empfehlung vorschlägt.
    - Deaktivieren Sie die Kontrollkästchen der Adapter, die Sie nicht für die Clusterverwaltung verwenden möchten.

    > [!NOTE]
    > Sie können 1 GBit-Adapter als Verwaltungsadapter verwenden, wir empfehlen aber, Adapter mit 10 GBit oder mehr für die Abwicklung des Speicher- und Workloadverkehrs (der VMs) zu verwenden.

1. Wenn die Änderungen vorgenommen wurden, klicken Sie auf **Apply and test** (Übernehmen und testen).
1. Vergewissern Sie sich unter **Netzwerke definieren**, dass jeder Netzwerkadapter für jeden Server über eine eindeutige statische IP-Adresse, eine Subnetzmaske und eine VLAN-ID verfügt. Zeigen Sie mit der Maus auf jedes Tabellenelement, und geben Sie bei Bedarf Werte ein bzw. ändern Sie diese. Wenn Sie den Vorgang abgeschlossen haben, klicken Sie auf **Apply and test** (Übernehmen und testen).

    > [!NOTE]
    > Um die VLAN-ID-Konfiguration für den Cluster zu unterstützen, müssen alle Netzwerkadapter auf allen Servern die VLANID-Eigenschaft unterstützen.

1. Warten Sie, bis in der Spalte **Status** für jeden Server **Passed** (Bestanden) angezeigt wird, und klicken Sie dann auf **Weiter**. In diesem Schritt wird die Netzwerkverbindung zwischen allen Adaptern mit dem gleichen Subnetz und der gleichen VLAN-ID überprüft. Die angegebenen IP-Adressen werden vom physischen Adapter auf die virtuellen Adapter übertragen, sobald die Erstellung der virtuellen Switches im nächsten Schritt abgeschlossen ist. Der Vorgang kann mehrere Minuten in Anspruch nehmen, abhängig von der Anzahl der konfigurierten Adapter.

1. Wählen Sie unter **2.3 Virtual switch** fallweise eine der folgenden Optionen aus. Je nachdem, wie viele Netzwerkadapter vorhanden sind, sind möglicherweise nicht alle Optionen verfügbar:

    - **Skip virtual switch creation** (Erstellung virtueller Switches überspringen): Wählen Sie diese Option aus, wenn Sie die virtuellen Switches später einrichten möchten.
    - **Create one virtual switch for compute and storage together** (Gemeinsamen Switch für Compute und Speicher erstellen): Wählen Sie diese Option aus, wenn Sie den gleichen virtuellen Switch für Ihre VMs und für direkte Speicherplätze verwenden möchten. Dies ist die „zusammengeführte“ Option.
    - **Create one virtual switch for compute only** (Virtuellen Switch nur für Compute erstellen): Wählen Sie diese Option aus, wenn Sie nur für Ihre VMs einen virtuellen Switch verwenden möchten.
    - **Create two virtual switches** (Zwei virtuelle Switches erstellen): Wählen Sie diese Option aus, wenn Sie für VMs und direkte Speicherplätze jeweils einen dedizierten virtuellen Switch verwenden möchten.

        :::image type="content" source="media/cluster/create-cluster-virtual-switches.png" alt-text="Clustererstellungs-Assistent – virtuelle Switches" lightbox="media/cluster/create-cluster-virtual-switches.png":::

    > [!NOTE]
    > Wenn Sie den Netzwerkcontroller für das softwaredefinierte Netzwerk bereitstellen möchten (in **Schritt 5: SDN** des Assistenten), benötigen Sie einen virtuellen Switch. Wenn Sie sich hier also gegen die Erstellung eines virtuellen Switches entscheiden und keinen virtuellen Switch außerhalb des Assistenten erstellen, wird der Assistent den Netzwerkcontroller nicht bereitstellen.

    In der folgenden Tabelle sind die Konfigurationen für virtuelle Switches aufgeführt, die für verschiedene Netzwerkadapterkonfigurationen unterstützt werden und aktiviert sind:

    | Option | 1 –2 Adapter | 3 oder mehr Adapter | Kombinierte Adapter |
    | :------------- | :--------- |:--------| :---------|
    | Einzelner Switch (Compute + Speicher) | enabled | enabled  | Nicht unterstützt |
    | Einzelner Switch (nur Compute) | Nicht unterstützt| enabled | enabled |
    | Zwei Switches | Nicht unterstützt | enabled | enabled |

1. Ändern Sie nach Bedarf den Namen eines Switches und weitere Konfigurationseinstellungen, und klicken Sie dann auf **Apply and test** (Übernehmen und testen). In der Spalte **Status** sollte für jeden Server **Passed** (Bestanden) angezeigt werden, nachdem die virtuellen Switches erstellt wurden.

1. Der Schritt **2.4 RDMA** ist optional. Wenn Sie RDMA verwenden, aktivieren Sie das Kontrollkästchen **Configure RDMA (Recommended)** (RDMA konfigurieren (empfohlen)), und klicken Sie auf **Weiter**.

    :::image type="content" source="media/cluster/create-cluster-rdma.png" alt-text="Clustererstellungs-Assistent – RDMA konfigurieren" lightbox="media/cluster/create-cluster-rdma.png":::

    Informationen zum Zuweisen von Bandbreitenreservierungen finden Sie im Abschnitt [Bandbreitenzuordnung für Datenverkehr](../concepts/host-network-requirements.md#traffic-bandwidth-allocation) in den [Anforderungen für Hostnetzwerke](../concepts/host-network-requirements.md).

1. Wählen Sie **Advanced** (Erweitert) aus, und aktivieren Sie dann das Kontrollkästchen **Data Center Bridging (DCB)** .

1. Weisen Sie unter **Cluster heartbeat** (Clustertakt) eine Prioritätsstufe und den Prozentsatz der Bandbreitenreservierung zu.

1. Weisen Sie unter **Storage** eine Prioritätsstufe und den Prozentsatz der Bandbreitenreservierung zu.

1. Anschließend wählen Sie **Änderungen übernehmen** aus, und klicken Sie auf **Weiter**.

1. Überprüfen und bearbeiten Sie unter **2.5 Define networks** (Netzwerke definieren) für jeden aufgeführten Adapter die Felder für Name, IP-Adresse, Subnetzmaske, VLAN-ID und Standardgateway.

    :::image type="content" source="media/cluster/create-cluster-define-networks.png" alt-text="Clustererstellungs-Assistent – Netzwerke definieren" lightbox="media/cluster/create-cluster-define-networks.png":::

1. Wenn Sie den Vorgang abgeschlossen haben, klicken Sie auf **Apply and test** (Übernehmen und testen). Möglicherweise müssen Sie den **Verbindungstest wiederholen,** , wenn der Status für einen Adapter nicht OK lautet.

## <a name="step-3-clustering"></a>Schritt 3: Clustering

In Schritt 3 des Assistenten wird überprüft, ob bisher alles ordnungsgemäß eingerichtet wurde, und im Fall von Stretchingcluster-Bereitstellungen werden automatisch zwei Standorte eingerichtet. Anschließend wird der Cluster erstellt. Sie können die Standorte auch vorab in Active Directory einrichten.

1. Klicken Sie auf **Weiter: Clustering**.
1. Wählen Sie unter **3.1 Validate the cluster** (Cluster überprüfen) **Validate** (Überprüfen) aus. Die Überprüfung kann einige Minuten dauern.

    Wenn das **Credential Security Service Provider (CredSSP)** -Popup angezeigt wird, wählen Sie **Ja** aus, um CredSSP vorübergehend zu aktivieren, damit der Assistent fortgesetzt werden kann. Nachdem der Cluster erstellt wurde und der Assistent abgeschlossen ist, deaktivieren Sie CredSSP, um die Sicherheit zu erhöhen. Falls Sie Probleme mit CredSSP haben, finden Sie unter [Problembehandlung für CredSSP](../manage/troubleshoot-credssp.md) weitere Informationen.

1. Überprüfen Sie alle Validierungsstatus, laden Sie den Bericht herunter, um detaillierte Informationen zu eventuellen Fehlern zu erhalten, nehmen Sie bei Bedarf Änderungen vor, und klicken Sie auf **Validate again** (Erneut überprüfen). Mit **Download report** können Sie den Bericht auch herunterladen. Wiederholen Sie dies bei Bedarf, bis alle Validierungsprüfungen bestanden werden. Wenn alles in Ordnung ist, klicken Sie auf **Weiter**.
1. Geben Sie unter **3.2 Create the cluster** (Cluster erstellen) einen Namen für Ihren Cluster ein.

    :::image type="content" source="media/cluster/create-cluster.png" alt-text="Clustererstellungs-Assistent – Cluster erstellen" lightbox="media/cluster/create-cluster.png":::

1. Wählen Sie unter **IP addresses** (IP-Adressen) die zu verwendenden statischen oder dynamischen IP-Adressen aus.
1. Wählen Sie **Erweitert** aus. Sie haben mehrere Möglichkeiten:

    - **Registrieren des Clusters bei DNS und Active Directory**
    - **Hinzufügen des geeigneten Speichers zum Cluster (empfohlen)**

1. Wählen Sie unter **Netzwerk** aus, ob Sie **Alle Netzwerke verwenden (empfohlen)** oder **geben Sie mindestens ein Netzwerk an, das nicht verwendet werden soll**.

1. Wenn Sie dies abgeschlossen haben, klicken Sie auf **Create cluster** (Cluster erstellen).

1. Benennen Sie bei Stretched Cluster unter **3.3 Assign servers to sites** (Server zu Standorten zuweisen) die zwei Standorte, die verwendet werden sollen.

1. Weisen Sie als Nächstes jeden Server einem Standort zu. Die Einrichtung der Replikation zwischen den Standorten erfolgt später. Wenn Sie den Vorgang abgeschlossen haben, klicken Sie auf **Änderungen übernehmen**.

## <a name="step-4-storage"></a>Schritt 4: Storage

In Schritt 4 des Assistenten werden Sie durch das Einrichten von Direkte Speicherplätze für Ihren Cluster geführt.

1. Klicken Sie auf **Weiter: Speicher**.
1. Unter **4.1 Clean drives** (Laufwerke bereinigen) können Sie optional **Erase drives** (Laufwerke löschen) auswählen, sofern diese bei Ihrer Bereitstellung sinnvoll ist.
1. Klicken Sie unter **4.2 Check drives** (Laufwerke überprüfen) auf das Symbol **>** neben jedem Server, um zu überprüfen, ob die Datenträger funktionieren und verbunden sind. Wenn alles in Ordnung ist, klicken Sie auf **Weiter**.
1. Klicken Sie unter **4.3 Validate storage** (Speicher überprüfen) auf **Weiter**.
1. Laden Sie den Überprüfungsbericht herunter, und sehen Sie ihn durch. Wenn alles in Ordnung ist, klicken Sie auf **Weiter**. Andernfalls führen Sie **Validate again** (Erneut überprüfen) aus.
1. Klicken Sie unter **4.4 Enable Storage Spaces Direct** (Direkte Speicherplätze aktivieren) auf **Aktivieren**.
1. Laden Sie den Bericht herunter, und überprüfen Sie ihn. Wenn alles in Ordnung ist, klicken Sie auf **Fertigstellen**. 
1. Wählen Sie **Go to connections list** (Verbindungsliste aufrufen) aus.
1. Nach einigen Minuten sollte Ihr Cluster in der Liste angezeigt werden. Wählen Sie ihn aus, um die Seite mit der Clusterübersicht anzuzeigen.

Es kann etwas dauern, bis der Clustername in Ihrer Domäne repliziert wurde. Dies gilt insbesondere dann, wenn Active Directory neue Arbeitsgruppenserver hinzugefügt wurden. Auch wenn der Cluster in Windows Admin Center angezeigt wird, ist er möglicherweise noch nicht zum Herstellen einer Verbindung verfügbar.

Wenn das Auflösen des Clusters nach einiger Zeit nicht erfolgreich ist, können Sie in den meisten Fällen einen Servernamen anstelle des Clusternamens angeben.

## <a name="step-5-sdn-optional"></a>Schritt 5: SDN (optional)

In diesem optionalen Schritt richten Sie die Netzwerkcontrollerkomponente von [Software Defined Networking (SDN)](../concepts/software-defined-networking.md) ein. Nachdem der Netzwerkcontroller eingerichtet wurde, können Sie weitere Komponenten von SDN wie Software Load Balancer (SLB) und RAS Gateway konfigurieren.

> [!NOTE]
> Mit dem Assistenten können SLB und RAS-Gateway derzeit nicht konfiguriert werden. Sie können diese Komponenten mit SDN Express-Skripts konfigurieren. Weitere Informationen hierzu finden Sie im [SDNExpress-GitHub-Repository](https://github.com/microsoft/SDN/tree/master/SDNExpress/scripts).

> [!NOTE]
> SDN wird für Stretched Cluster nicht unterstützt.

1. Klicken Sie auf **Weiter: SDN**.

    :::image type="content" source="media/cluster/create-cluster-network-controller.png" alt-text="Assistent zum Erstellen von Clustern – SDN-Netzwerkcontroller" lightbox="media/cluster/create-cluster-network-controller.png":::

1. Geben Sie unter **5.1 Define the Network Controller cluster**, (Netzwerkcontroller-Cluster definieren) unter **Host** einen Namen für den Netzwerkcontroller ein. Dies ist der DNS-Name, der von Verwaltungsclients (z. B. Windows Admin Center) für die Kommunikation mit dem Netzwerkcontroller verwendet wird.
1. Geben Sie einen Pfad zur Azure Stack HCI-VHD-Datei an. Verwenden Sie **Durchsuchen**, um sie schneller zu finden.
1. Geben Sie die Anzahl der virtuellen Computer an, die für Netzwerkcontroller dediziert werden sollen. Für Hochverfügbarkeit werden mindestens drei virtuelle Computer empfohlen.
1. Geben Sie unter **Netzwerk** die VLAN-ID des Verwaltungsnetzwerks ein. Der Netzwerkcontroller benötigt eine Verbindung mit demselben Verwaltungsnetzwerk wie die Hosts, um mit den Hosts kommunizieren und diese konfigurieren zu können.

    > [!NOTE]
    > Netzwerkcontroller-VMs verwenden den virtuellen Switch, der für die Clusterverwaltung verwendet wird, sofern verfügbar. Andernfalls verwenden Sie den virtuellen Switch „Compute“ wie die anderen Cluster-VMs. Weitere Informationen finden Sie im Abschnitt [Anforderungen an Netzwerkcontroller](../concepts/network-controller.md#network-controller-requirements) unter [Planen der Bereitstellung von Netzwerkcontrollern](../concepts/network-controller.md).

1. Wählen Sie für **VM-Netzwerkadressierung** entweder **DHCP** oder **Statisch** aus.
1. Wenn Sie **DHCP** ausgewählt haben, geben Sie den Namen für die virtuellen Computer des Netzwerkcontrollers ein.
1. Geben Sie Folgendes an, wenn Sie **Statisch** ausgewählt haben:
    1. IP-Adresse.
    1. Subnetzpräfix.
    1. Standardgateway.
    1. Mindestens einen DNS-Server. Klicken Sie auf **Hinzufügen**, um weitere DNS-Server hinzuzufügen.
1. Geben Sie unter **Anmeldeinformationen** den Benutzernamen und das Kennwort ein, mit denen die virtuellen Computer des Netzwerkcontrollers mit der Clusterdomäne verbunden werden.
1. Geben Sie das lokale Administratorkennwort für diese VMs ein.
1. Geben Sie unter **Erweitert** den Pfad zu den virtuellen Computern ein.
1. Geben Sie Werte für den **Anfang des MAC-Adresspools** und das **Ende des MAC-Adresspools** ein.
1. Klicken Sie abschließend auf **Weiter**.
1. Warten Sie unter **Deploy the Network Controller** (Netzwerkcontroller bereitstellen), bis der Assistent den Auftrag ausgeführt hat. Bleiben Sie auf dieser Seite, bis alle Fortschrittsaufgaben abgeschlossen sind. Klicken Sie auf **Fertig stellen**.

1. Konfigurieren Sie nach dem Erstellen von Netzwerkcontroller-VMs dynamische DNS-Updates für den Namen des Netzwerkcontrollerclusters auf dem DNS-Server. Weitere Informationen finden Sie unter [Konfigurieren der dynamischen DNS-Registrierung für den Netzwerkcontroller](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller#step-3-configure-dynamic-dns-registration-for-network-controller).

1. Wenn bei der Bereitstellung des Netzwerkcontrollers ein Fehler auftritt, gehen Sie wie folgt vor, bevor Sie es erneut versuchen:

- Beenden und löschen Sie alle Netzwerkcontroller-VMs, die der Assistent erstellt hat.  

- Bereinigen Sie alle VHD-Bereitstellungspunkte, die der Assistent erstellt hat.  

- Stellen Sie sicher, dass Sie über mindestens 50–100 GB freien Speicherplatz auf Ihren Hyper-V-Hosts verfügen.  

## <a name="after-you-complete-the-wizard"></a>Nach Abschluss des Assistenten

Nachdem der Assistent abgeschlossen wurde, gibt es noch einige wichtige Aufgaben, die Sie ausführen müssen.

Die erste Aufgabe besteht darin, das CredSSP-Protokoll (Credential Security Support Provider) auf jedem Server aus Sicherheitsgründen zu deaktivieren. Denken Sie daran, dass CredSSP zur Ausführung des Assistenten aktiviert sein musste. Falls Sie Probleme mit CredSSP haben, finden Sie unter [Problembehandlung für CredSSP](../manage/troubleshoot-credssp.md) weitere Informationen.

1. Wählen Sie in Windows Admin Center unter **Alle Verbindungen** den Cluster aus, den Sie soeben erstellt haben.
1. Wählen Sie unter **Tools** die Option **Server** aus.
1. Wählen Sie im rechten Bereich den ersten Server im Cluster aus.
1. Wählen Sie unter **Übersicht** die Option **Disable CredSSP** (CredSSP deaktivieren) aus. Sie können dann sehen, dass das rote Banner **CredSSP ENABLED** (CredSSP AKTIVIERT) oben ausgeblendet wird.
1. Wiederholen Sie die Schritte 3 und 4 für jeden Server in Ihrem Cluster.

OK, und das sind die anderen Aufgaben, die Sie ausführen müssen:

- Einrichten eines Clusterzeugen. Mehr dazu finden Sie unter [Einrichten eines Clusterzeugen](witness.md).
- Erstellen Ihrer Volumes. Mehr dazu finden Sie unter [Erstellen von Volumes](../manage/create-volumes.md).
- Für Stretched Cluster erstellen Sie Volumes und richten die Replikation ein. Siehe [Erstellen von Volumes für Stretched Cluster und Einrichten der Replikation](../manage/create-stretched-volumes.md).

## <a name="next-steps"></a>Nächste Schritte

- Registrieren Sie Ihren Cluster bei Azure. Mehr dazu finden Sie unter [Verwalten der Azure-Registrierung](../manage/manage-azure-registration.md).
- Führen Sie eine abschließende Überprüfung des Clusters durch. Mehr dazu finden Sie unter [Überprüfen eines Azure Stack HCI-Clusters](validate.md).
- Stellen Sie Ihre virtuellen Computer bereit. Mehr dazu finden Sie unter [Verwalten von VMs in Azure Stack HCI mithilfe von Windows Admin Center](../manage/vm.md).
- Sie können einen Cluster auch mithilfe von PowerShell bereitstellen. Mehr dazu finden Sie unter [Erstellen eines Azure Stack HCI-Clusters mithilfe von Windows PowerShell](create-cluster-powershell.md).
- Sie können Netzwerkcontroller auch mithilfe von PowerShell bereitstellen. Weitere Informationen finden Sie unter [Bereitstellen eines Netzwerkcontrollers mit PowerShell](network-controller-powershell.md).
