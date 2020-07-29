---
title: Erstellen eines Azure Stack HCI-Clusters mithilfe von Windows Admin Center
description: Hier erfahren Sie, wie Sie mithilfe von Windows Admin Center einen Servercluster für Azure Stack HCI erstellen.
author: v-dasis
ms.topic: how-to
ms.date: 07/21/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: faca39736eb1ed3410f2f3972765d4d7df6c9d5a
ms.sourcegitcommit: 0e52f460295255b799bac92b40122a22bf994e27
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/21/2020
ms.locfileid: "86868065"
---
# <a name="create-an-azure-stack-hci-cluster-using-windows-admin-center"></a>Erstellen eines Azure Stack HCI-Clusters mithilfe von Windows Admin Center

> Gilt für Azure Stack HCI, Version v20H2

In diesem Artikel erfahren Sie, wie Sie Windows Admin Center verwenden, um einen hyperkonvergenten Azure Stack HCI-Cluster erstellen, der Direkte Speicherplätze verwendet. Der Clustererstellungs-Assistent in Windows Admin Center nimmt Ihnen den größten Teil der schweren Arbeit ab. Wenn Sie das lieber selbst mithilfe von PowerShell erledigen möchten, lesen Sie [Erstellen eines Azure Stack HCI-Clusters mithilfe von PowerShell](create-cluster-powershell.md).

Beim Erstellen haben Sie die Wahl zwischen zwei Clustertypen:

- Standardcluster mit mindestens zwei Serverknoten, die an einem einzelnen Standort betrieben werden.
- Stretchingcluster mit mindestens vier Serverknoten, die sich über zwei Standorte erstrecken und pro Standort mindestens zwei Knoten aufweisen.

Weitere Informationen zu Stretchingclustern finden Sie unter [Stretchingcluster: Übersicht](../concepts/stretched-clusters.md).

## <a name="before-you-run-the-wizard"></a>Vor dem Ausführen des Assistenten

Stellen Sie vor dem Ausführen des Clustererstellungs-Assistenten sicher, dass die folgenden Punkte erledigt wurden:

- Sie haben die Hardware- und sonstigen Anforderungen in [Vor dem Bereitstellen von Azure Stack HCI](before-you-start.md) gelesen.
- Sie haben das Azure Stack HCI-Betriebssystem auf jedem Server im Cluster installiert. Mehr dazu finden Sie unter [Bereitstellen des Azure Stack HCI-Betriebssystems](operating-system.md).
- Sie haben Windows Admin Center auf einem Remotecomputer (Verwaltungscomputer) installiert. Mehr dazu finden Sie unter [Vor dem Bereitstellen von Azure Stack HCI](before-you-start.md). Führen Sie den Assistenten nicht auf einem Server im Cluster aus.
- Sie besitzen ein Konto, das auf jedem Server Mitglied der lokalen Gruppe „Administratoren“ ist.

Außerdem muss Ihr Verwaltungscomputer Mitglied der gleichen Active Directory-Domäne, in der Sie den Cluster erstellen, oder einer Domäne mit voller Vertrauensstellung sein. Die Server, die Sie im Cluster gruppieren möchten, müssen noch nicht zur Domäne gehören; sie können der Domäne während der Clustererstellung hinzugefügt werden.

Dies sind die wichtigsten Schritte im Clustererstellungs-Assistenten:

1. **Get Started** (Erste Schritte): Stellt sicher, dass jeder Server die Voraussetzungen erfüllt und die Funktionen besitzt, die für den Beitritt zum Cluster erforderlich sind.
1. **Networking** (Netzwerk): Weist Netzwerkadapter zu, konfiguriert diese und erstellt die virtuellen Switches für jeden Server.
1. **Clustering**: Überprüft, ob der Cluster ordnungsgemäß eingerichtet ist. Richtet bei Stretchingclustern außerdem die zwei Standorte ein.
1. **Storage** (Speicher): Konfiguriert Direkte Speicherplätze.

Nach dem Abschluss des Assistenten richten Sie den Clusterzeugen ein, registrieren sich bei Azure und erstellen Volumes (wodurch auch die Replikation zwischen Standorten eingerichtet wird, wenn Sie einen Stretchingcluster erstellen).

Gut, fangen wir an:

1. Klicken Sie in Windows Admin Center unter **Alle Verbindungen** auf **Hinzufügen**.
1. Wählen Sie im Bereich **Ressourcen hinzufügen** unter **Windows Server-Cluster** **Neuen erstellen** aus.
1. Wählen Sie unter **Clustertyp auswählen** die Option **Azure Stack HCI** aus.
1. Wählen Sie unter **Serverstandorte auswählen** eine der folgenden Optionen aus:

    - **Alle Server an einem Standort**
    - **Server an zwei Standorten** (für Stretchingcluster)

1. Klicken Sie auf **Erstellen**, wenn Sie fertig sind. Jetzt wird der Clustererstellungs-Assistent angezeigt, wie unten dargestellt.

    :::image type="content" source="media/cluster/create-cluster-wizard.png" alt-text="Szenario mit einem aktiv-aktiven Stretchingcluster" lightbox="media/cluster/create-cluster-wizard.png":::

## <a name="step-1-get-started"></a>Schritt 1: Erste Schritte

In Schritt 1 des Assistenten werden Sie durch die Überprüfung der Voraussetzungen, das Hinzufügen der Serverknoten, das Installieren der erforderlichen Features und bei Bedarf den Neustart der einzelnen Server geführt.

1. Überprüfen Sie die im Assistenten aufgeführten Voraussetzungen, um sicherzustellen, dass jeder Serverknoten clusterfähig ist. Klicken Sie abschließend auf **Weiter**.
1. Geben Sie auf der Seite **Add servers to the cluster** (Hinzufügen von Servern zum Cluster) den Benutzernamen und das Kennwort Ihres Kontos ein, und klicken Sie dann auf **Weiter**. Bei dem Konto muss es sich um ein Mitglied der lokalen Gruppe „Administratoren“ auf jedem Server handeln.
1. Geben Sie den Namen des ersten hinzuzufügenden Servers ein, und klicken Sie dann auf **Hinzufügen**.
1. Wiederholen Sie Schritt 3 für jeden Server, der Teil des Clusters sein soll. Klicken Sie abschließend auf **Weiter**.
1. Geben Sie bei Bedarf auf der Seite **Join the servers to a domain** (Hinzufügen der Server zu einer Domäne) die Domäne und ein Konto an, um die Server zur Domäne hinzuzufügen. Geben Sie den Servern optional aussagekräftigere Namen, und klicken Sie auf **Weiter**.
1. Klicken Sie auf **Install Features** (Features installieren). Klicken Sie abschließend auf **Weiter**.

    Der Assistent installiert die folgenden erforderlichen Features:

    - BitLocker
    - Data Center Bridging (für RoCEv2-Netzwerkadapter)
    - Failoverclustering
    - Dateiserver (für Dateifreigabezeugen oder das Hosten von Dateifreigaben)
    - FS-Datendeduplizierungsmodul
    - Hyper-V
    - RSAT-AD-PowerShell-Modul
    - Speicherreplikation (wird nur für Stretchingcluster installiert)

1. Klicken Sie unter **Updates installieren** gegebenenfalls auf **Updates installieren**. Klicken Sie auf **Weiter**, wenn Sie fertig sind.
1. Klicken Sie unter **Lösungsupdates** gegebenenfalls auf **Erweiterung installieren**. Klicken Sie auf **Weiter**, wenn Sie fertig sind.
1. Klicken Sie bei Bedarf auf **Server neu starten**. Überzeugen Sie sich, dass jeder Server erfolgreich gestartet wurde.

## <a name="step-2-networking"></a>Schritt 2: Netzwerk

In Schritt 2 des Assistenten werden Sie durch das Überprüfen der Netzwerk-Schnittstellenadapter (NICs), das Auswählen eines Verwaltungsadapters, das Zuweisen von IP-Adressen, Subnetzmasken und VLAN-IDs für jeden Server sowie durch das Erstellen der virtuellen Switches geführt.

Die Auswahl wenigstens eines der Adapter für Verwaltungszwecke ist obligatorisch, da der Assistent mindestens eine dedizierte physische NIC für die Clusterverwaltung erfordert.  Sobald ein Adapter für die Verwaltung zugewiesen wurde, wird er aus dem restlichen Ablauf des Assistenten ausgeschlossen.

Für Verwaltungsadapter gibt es zwei Konfigurationsoptionen:

- Physischer Adapter: Wenn der Adapter DHCP-Adressierung verwendet, wird er verwendet.

- Kombinierte Adapter (für die Erstellung virtueller Switches): Wenn die ausgewählten Adapter DHCP-Adressierung verwenden (entweder für einen oder beide), wird die DHCP-IP-Adresse diesen Adaptern als statische IP-Adresse zugewiesen.

Durch Verwendung kombinierter Adapter haben Sie eine einzelne Verbindung mit mehreren physischen Switches, verwenden aber nur eine einzelne IP-Adresse. Damit wird Lastenausgleich verfügbar, und es wird sofortige Fehlertoleranz erreicht, statt die Aktualisierung von DNS-Einträgen abwarten zu müssen.

Lassen Sie uns beginnen:

1. Wählen Sie **Weiter: Netzwerk** aus.
1. Warten Sie unter **Verify the network adapters** (Überprüfen der Netzwerkadapter), bis grüne Kontrollkästchen neben den einzelnen Adaptern angezeigt werden, und wählen Sie dann **Weiter** aus.

1. Wählen Sie unter **Select management adapters** (Verwaltungsadapter auswählen) einen oder zwei Verwaltungsadapter für jeden Server aus, und führen Sie dann für jeden Server Folgendes aus:

    - Aktivieren Sie das Kontrollkästchen **Beschreibung**. Beachten Sie, dass alle Adapter ausgewählt sind und der Assistent Ihnen möglicherweise eine Empfehlung vorschlägt.
    - Deaktivieren Sie die Kontrollkästchen der Adapter, die Sie nicht für die Clusterverwaltung verwenden möchten.

     Sie können 1 GBit-Adapter als Verwaltungsadapter verwenden, wir empfehlen aber, Adapter mit 10 GBit oder mehr für die Abwicklung des Speicher- und Workloadverkehrs (der VMs) zu verwenden.

1. Wenn die Änderungen vorgenommen wurden, klicken Sie auf **Apply and test** (Übernehmen und testen).
1. Vergewissern Sie sich unter **Netzwerke definieren**, dass jeder Netzwerkadapter für jeden Server über eine eindeutige statische IP-Adresse, eine Subnetzmaske und eine VLAN-ID verfügt. Zeigen Sie mit der Maus auf jedes Tabellenelement, und geben Sie bei Bedarf Werte ein bzw. ändern Sie diese. Wenn Sie den Vorgang abgeschlossen haben, klicken Sie auf **Apply and test** (Übernehmen und testen).

    > [!NOTE]
    > Netzwerkadapter, die die erweiterte Eigenschaft `VLANID` nicht unterstützen, akzeptieren keine VLAN-IDs.

1. Warten Sie, bis in der Spalte **Status** für jeden Server **Passed** (Bestanden) angezeigt wird, und klicken Sie dann auf **Weiter**. In diesem Schritt wird die Netzwerkverbindung zwischen allen Adaptern mit dem gleichen Subnetz und der gleichen VLAN-ID überprüft. Die angegebenen IP-Adressen werden vom physischen Adapter auf die virtuellen Adapter übertragen, sobald die Erstellung der virtuellen Switches im nächsten Schritt abgeschlossen ist. Der Vorgang kann mehrere Minuten in Anspruch nehmen, abhängig von der Anzahl der konfigurierten Adapter.

1. Wählen Sie unter **Virtueller Switch** fallweise eine der folgenden Optionen aus. Abhängig von der Anzahl der Adapter werden möglicherweise nicht alle Optionen angezeigt.

    - Erstellen eines virtuellen Switches für die gemeinsame Verwendung durch Hyper-V und Speicher
    - Erstellen eines virtuellen Switches nur für die Verwendung durch Hyper-V
    - Erstellen zweier virtueller Switches, einer für Hyper-V, der andere für Speicher
    - Keinen virtuellen Switch erstellen

1. Ändern Sie nach Bedarf den Namen eines Switches und weitere Konfigurationseinstellungen, und klicken Sie dann auf **Apply and test** (Übernehmen und testen). In der Spalte **Status** sollte für jeden Server **Passed** (Bestanden) angezeigt werden, nachdem die virtuellen Switches erstellt wurden.

## <a name="step-3-clustering"></a>Schritt 3: Clustering

In Schritt 3 des Assistenten wird überprüft, ob bislang alles ordnungsgemäß eingerichtet wurde. Anschließend werden Standorte im Fall von Stretchingcluster-Bereitstellungen und schließlich der eigentliche Cluster erstellt.

1. Klicken Sie auf **Weiter: Clustering**.
1. Klicken Sie unter **Validate the cluster** (Cluster überprüfen) auf **Validate** (Überprüfen). Die Überprüfung kann einige Minuten dauern.

    Wenn das **Credential Security Service Provider (CredSSP)** -Popup angezeigt wird, wählen Sie **Ja** aus, um CredSSP vorübergehend zu aktivieren, damit der Assistent fortgesetzt werden kann. Nachdem der Cluster erstellt wurde und der Assistent abgeschlossen ist, deaktivieren Sie CredSSP, um die Sicherheit zu erhöhen.

1. Überprüfen Sie alle Validierungsstatus, laden Sie den Bericht herunter, um detaillierte Informationen zu eventuellen Fehlern zu erhalten, nehmen Sie bei Bedarf Änderungen vor, und klicken Sie auf **Validate again** (Erneut überprüfen). Wiederholen Sie dies bei Bedarf, bis alle Validierungsprüfungen bestanden werden.
1. Geben Sie unter **Create the cluster** (Cluster erstellen) einen Namen für Ihren Cluster ein.
1. Wählen Sie unter **Networks** (Netzwerke) die bevorzugte Konfiguration aus.
1. Wählen Sie unter **IP addresses** (IP-Adressen) die zu verwendenden dynamischen oder statischen IP-Adressen aus.
1. Wenn Sie dies abgeschlossen haben, klicken Sie auf **Create cluster** (Cluster erstellen).

1. Nennen Sie bei Stretchingclustern unter **Assign servers to sites** (Server an Standorte zuweisen) die zwei Standorte, die verwendet werden sollen.

1. Weisen Sie als Nächstes jeden Server einem Standort zu. Die Einrichtung der Replikation zwischen den Standorten erfolgt später. Wenn Sie dies abgeschlossen haben, klicken Sie auf **Apply** (Übernehmen).

## <a name="step-4-storage"></a>Schritt 4: Storage

In Schritt 4 des Assistenten werden Sie durch das Einrichten von Direkte Speicherplätze für Ihren Cluster geführt.


1. Klicken Sie auf **Weiter: Speicher**.
1. Klicken Sie unter **Verify drives** (Laufwerke überprüfen) auf das Symbol **>** neben jedem Server, um zu überprüfen, ob die Datenträger funktionieren und verbunden sind, und klicken Sie dann auf **Weiter**.
1. Klicken Sie unter **Clean drives** (Laufwerke bereinigen) auf **Clean drives**, um die Laufwerke von Daten zu leeren. Wenn Sie fertig sind, klicken Sie auf **Weiter**.
1. Klicken Sie unter **Validate storage** (Speicher überprüfen) auf **Weiter**.
1. Überprüfen Sie die Validierungsergebnisse. Wenn alles in Ordnung ist, klicken Sie auf **Weiter**.
1. Klicken Sie unter **Enable Storage Spaces Direct** (Direkte Speicherplätze aktivieren) auf **Aktivieren**.
1. Laden Sie den Bericht herunter, und überprüfen Sie ihn. Wenn alles in Ordnung ist, klicken Sie auf **Fertigstellen**.

Herzlichen Glückwunsch, Sie haben jetzt einen Cluster.

Nachdem der Cluster erstellt wurde, kann es eine Weile dauern, bis der Clustername in der Domäne repliziert wird. Wenn das Auflösen des Clusters nach einiger Zeit nicht erfolgreich ist, können Sie in den meisten Fällen den Computernamen eines Serverknotens im Cluster anstelle des Clusternamens angeben.

## <a name="after-you-run-the-wizard"></a>Nach der Ausführung des Assistenten

Nachdem der Assistent abgeschlossen wurde, gibt es noch einige wichtige Aufgaben, die Sie ausführen müssen, um einen voll funktionsfähigen Cluster zu erhalten.

Die erste Aufgabe besteht darin, das CredSSP-Protokoll (Credential Security Support Provider) auf jedem Server aus Sicherheitsgründen zu deaktivieren. Denken Sie daran, dass CredSSP zur Ausführung des Assistenten aktiviert sein musste. Weitere Informationen finden Sie unter [CVE-2018-0886](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2018-0886).

1. Wählen Sie in Windows Admin Center unter **Alle Verbindungen** den Cluster aus, den Sie soeben erstellt haben.
1. Wählen Sie unter **Tools** die Option **Server** aus.
1. Wählen Sie im rechten Bereich den ersten Server im Cluster aus.
1. Wählen Sie unter **Übersicht** die Option **Disable CredSSP** (CredSSP deaktivieren) aus. Sie können dann sehen, dass das rote Banner **CredSSP ENABLED** (CredSSP AKTIVIERT) oben ausgeblendet wird.
1. Wiederholen Sie die Schritte 3 und 4 für jeden Server in Ihrem Cluster.

OK, und das sind die anderen Aufgaben, die Sie ausführen müssen:

- Einrichten eines Clusterzeugen. Mehr dazu finden Sie unter [Einrichten eines Clusterzeugen](witness.md).
- Erstellen Ihrer Volumes. Mehr dazu finden Sie unter [Erstellen von Volumes](../manage/create-volumes.md).
- Erstellen Sie für Stretchingcluster Volumes und die Setupreplikation mithilfe von Speicherreplikation. Mehr dazu finden Sie unter [Erstellen von Volumes und Einrichten von Replikation für Stretchingcluster](../manage/create-stretched-volumes.md).

## <a name="next-steps"></a>Nächste Schritte

- Registrieren Sie Ihren Cluster bei Azure. Mehr dazu finden Sie unter [Verwalten der Azure-Registrierung](../manage/manage-azure-registration.md).
- Führen Sie eine abschließende Überprüfung des Clusters durch. Mehr dazu finden Sie unter [Überprüfen eines Azure Stack HCI-Clusters](validate.md).
- Stellen Sie Ihre virtuellen Computer bereit. Mehr dazu finden Sie unter [Verwalten von VMs in Azure Stack HCI mithilfe von Windows Admin Center](../manage/vm.md).
- Alternativ können Sie einen Cluster auch mithilfe von PowerShell erstellen. Mehr dazu finden Sie unter [Erstellen eines Azure Stack HCI-Clusters mithilfe von Windows PowerShell](create-cluster-powershell.md).
