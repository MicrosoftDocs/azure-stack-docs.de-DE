---
title: Erweitern des Rechenzentrums in Azure Stack Hub
description: In diesem Artikel erfahren Sie, wie Sie das Rechenzentrum in Azure Stack Hub erweitern.
author: mattbriggs
ms.topic: how-to
ms.date: 12/13/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/13/2019
ms.openlocfilehash: d3884f877511c3a0d285b62ecf3136673e24a116
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77702447"
---
# <a name="extending-storage-to-azure-stack-hub"></a>Erweitern von Speicher auf Azure Stack Hub

Dieser Artikel enthält Informationen zur Speicherinfrastruktur von Azure Stack Hub, die Sie bei der Entscheidung unterstützen, wie Sie Azure Stack Hub in Ihre bestehende Netzwerkumgebung integrieren. Nach allgemeinen Darlegungen zum Erweitern Ihres Rechenzentrums werden im Artikel zwei unterschiedliche Szenarien vorgestellt. Sie können eine Verbindung mit einem Windows File Storage-Server herstellen. Sie können auch eine Verbindung mit einem Windows iSCSI-Server herstellen.

## <a name="overview-of-extending-storage-to-azure-stack-hub"></a>Übersicht über das Erweitern von Speicher auf Azure Stack Hub

Es gibt Szenarien, in denen es nicht ausreicht, wenn sich die Daten in der öffentlichen Cloud befinden. Vielleicht besitzen Sie eine rechenintensive virtualisierte Datenbankworkload, die empfindlich auf Wartezeiten reagiert, und die Roundtripzeit zur öffentlichen Cloud könnte sich auf die Leistung der Datenbankworkload auswirken. Oder es sind unter Umständen auf einem Dateiserver, einem NAS-Gerät oder in einem iSCSI-Speicherarray gespeicherte lokale Daten vorhanden, auf die von lokalen Workloads zugegriffen werden muss und die sich in der lokalen Umgebung befinden müssen, um gesetzliche Bestimmungen einzuhalten oder Complianceziele zu erreichen. Dies sind nur zwei Szenarien, in denen es für viele Organisationen wichtig ist, dass sich die Daten lokal befinden.

Warum hosten Sie also diese Daten nicht einfach in Speicherkonten in Azure Stack Hub oder auf virtualisierten Dateiservern, die im Azure Stack Hub-System ausgeführt werden? Im Gegensatz zu Azure ist der Azure Stack Hub-Speicher endlich. Die für die Nutzung verfügbare Kapazität hängt gänzlich von der von Ihnen erworbenen Kapazität pro Knoten sowie der Anzahl Ihrer Knoten ab. Und da es sich bei Azure Stack Hub um eine hyperkonvergierte Lösung handelt, müssen Sie zur Erweiterung Ihrer Speicherkapazität auch Ihre Computekapazität erhöhen, indem Sie weitere Knoten hinzufügen, um den Nutzungsanforderungen gerecht zu werden. Dies scheitert unter Umständen an den Kosten, insbesondere, wenn Bedarf an zusätzlicher Kapazität für kalten Speicher oder Archivspeicher besteht, der zu geringen Kosten außerhalb des Azure Stack Hub-Systems hinzugefügt werden könnte.

Und das bringt uns zum folgenden Szenario: Wie können Sie Azure Stack Hub-Systeme und ihre in Azure Stack Hub ausgeführten virtualisierten Workloads einfach und effizient mit Speichersystemen außerhalb von Azure Stack Hub verbinden, auf die über das Netzwerk zugegriffen wird?

### <a name="design-for-extending-storage"></a>Entwurf für die Erweiterung des Speichers

In der Abbildung wird ein Szenario veranschaulicht, in dem eine einzelne VM mit einer Workload zum Lesen/Schreiben von Daten und andere Zwecke eine Verbindung mit externem Speicher herstellt (extern für die VM und die Azure Stack Hub-Instanz selbst). In diesem Artikel konzentrieren wir uns auf das einfache Abrufen von Dateien. Sie können dieses Beispiel jedoch auch auf komplexere Szenarien ausweiten, z. B. die Remotespeicherung von Datenbankdateien.

![](./media/azure-stack-network-howto-extend-datacenter/image1.png)

In der Abbildung ist ersichtlich, dass die VM im Azure Stack Hub-System mit mehreren Netzwerkadaptern bereitgestellt wurde. Sowohl wegen der Redundanz als auch der bewährten Methode hinsichtlich des Speichers ist es unerlässlich, dass zwischen Quelle und Ziel mehrere Pfade vorhanden sind. Komplexer werden die Dinge jedoch, wenn VMs in Azure Stack Hub über öffentliche und auch über private IP-Adressen verfügen, wie z. B. in Azure. Wenn eine Verbindung zwischen externem Speicher und VM erforderlich ist, ist dies nur über die öffentliche IP-Adresse möglich, da die privaten IP-Adressen hauptsächlich in den Azure Stack Hub-Systemen, in VNETs und den Subnetzen verwendet werden. Der externe Speicher wäre nicht in der Lage, mit dem privaten IP-Adressbereich der VM zu kommunizieren, sofern er kein Site-to-Site-VPN durchläuft, um eine Verbindung mit dem VNET selbst herzustellen. Daher konzentrieren wir uns in diesem Beispiel auf die Kommunikation mit dem öffentlichen IP-Adressraum. Ein bemerkenswerter Aspekt des öffentlichen IP-Adressraums in der Abbildung ist, dass zwei unterschiedliche Subnetze für öffentliche IP-Adresspools vorhanden sind. Standardmäßig ist in Azure Stack Hub nur ein Pool für öffentliche IP-Adressen erforderlich, aber für das redundante Routing empfiehlt es sich möglicherweise, einen zweiten hinzuzufügen. Es ist derzeit jedoch nicht möglich, eine IP-Adresse aus einem bestimmten Pool auszuwählen, und sie erhalten unter Umständen tatsächlich VMs mit öffentlichen IP-Adressen aus demselben Pool für mehrere virtuelle Netzwerkkarten.

Hier wird davon ausgegangen, dass das Routing zwischen den Peripheriegeräten und dem externen Speicher erfolgt und der Datenverkehr das Netzwerk entsprechend durchlaufen kann. Für dieses Beispiel ist es unerheblich, ob der Backbone eine Geschwindigkeit von 1 GbE, 10 GbE, 25 GbE oder sogar mehr hat. Dies sollte jedoch bedacht werden, wenn Sie die Integration planen, damit die Leistungsanforderungen von Anwendungen berücksichtigt werden, die auf diesen externen Speicher zugreifen.

## <a name="connect-to-a-windows-server-iscsi-target"></a>Herstellen einer Verbindung mit einem iSCSI-Ziel von Windows Server

In diesem Szenario wird eine Windows Server 2019-VM in Azure Stack Hub bereitgestellt, konfiguriert und für das Herstellen einer Verbindung mit einem externen iSCSI-Ziel vorbereitet. Dadurch wird auch Windows Server 2019 ausgeführt. Aktivieren Sie ggf. Schlüsselfunktionen wie MPIO, um Leistung und Konnektivität zwischen der VM und dem externen Speicher zu optimieren.

### <a name="deploy-the-windows-server-2019-vm-on-azure-stack-hub"></a>Bereitstellen der Windows Server 2019-VM in Azure Stack Hub

1.  Es wird davon ausgegangen, dass das System ordnungsgemäß registriert wurde und mit dem Marketplace verbunden ist. Wählen Sie im **Azure Stack Hub-Verwaltungsportal** die Option **Marketplace Management** (Marketplace-Verwaltung) und – da Sie noch nicht über ein Windows Server 2019-Image verfügen – die Option **Add from Azure** (Aus Azure hinzufügen) aus. Suchen Sie dann nach **Windows Server 2019**, und fügen Sie das Image **Windows Server 2019 Datacenter** hinzu.

    ![](./media/azure-stack-network-howto-extend-datacenter/image2.png)

    Das Herunterladen eines Windows Server 2019-Images kann eine gewisse Zeit dauern.

2.  Wenn Sie über ein Windows Server 2019-Image in Ihrer Azure Stack Hub-Umgebung verfügen, **melden Sie sich beim Azure Stack Hub-Benutzerportal an**.

3.  Stellen Sie nach der Anmeldung beim Azure Stack Hub-Benutzerportal sicher, dass Sie über ein [Abonnement eines Angebots](https://docs.microsoft.com/azure-stack/operator/azure-stack-subscribe-plan-provision-vm?view=azs-1908) verfügen, das Ihnen die Bereitstellung von IaaS-Ressourcen (Compute, Speicher und Netzwerk) ermöglicht.

4.  Wenn Sie über ein Abonnement verfügen, wählen Sie wiederum auf dem **Dashboard** im Azure Stack Hub-Benutzerportal die Option **Ressource erstellen** aus. Wählen Sie **Compute** und anschließend das Katalogelement **Windows Server 2019 Datacenter** aus.

5.  Geben Sie auf dem Blatt **Grundlagen** die folgenden Informationen ein:

    a.  **Name**: VM001

    b.  **Benutzername**: localadmin

    c.  **Kennwort** und **Kennwort bestätigen**: \<Kennwort Ihrer Wahl>

    d.  **Abonnement**: \<Abonnement Ihrer Wahl, mit Compute-/Speicher-/Netzwerkressourcen>

    e.  **Ressourcengruppe**: storagetesting (Neu erstellen)

    f.  Klicken Sie auf **OK**.

6.  Wählen Sie auf dem Blatt **Größe auswählen** die Größe **Standard_F8s_v2** und anschließend **Auswählen** aus.

7.  Wählen Sie auf dem Blatt **Einstellungen** die Option **Virtuelles Netzwerk** aus, ändern Sie auf dem Blatt **Virtuelles Netzwerk erstellen** den Adressraum in **10.10.10.0/23**, und aktualisieren Sie den Subnetz-Adressraum auf **10.10.10.0/24**. Wählen Sie dann **OK** aus.

8.  Wählen Sie die **öffentliche IP-Adresse** und auf dem Blatt **Öffentliche IP-Adresse erstellen** das Optionsfeld **Statisch** aus.

9.  Wählen Sie im Dropdownmenü **Öffentliche Eingangsports hinzufügen** die Option **RDP (3389)** aus.

10. Übernehmen Sie übrigen Standardwerte, und wählen Sie **OK** aus.

    ![](./media/azure-stack-network-howto-extend-datacenter/image3.png)

11. Lesen Sie die Zusammenfassung, warten Sie auf die Validierung, und wählen Sie **OK** aus, um den Bereitstellungsvorgang zu starten. Die Bereitstellung sollte in ca. 10 Minuten abgeschlossen sein.

12. Wählen Sie nach Abschluss der Bereitstellung unter **Ressource** den VM-Namen **VM001** aus, um die **Übersicht** zu öffnen.

    ![](./media/azure-stack-network-howto-extend-datacenter/image4.png)

13. Wählen Sie unter „DNS-Name“ die Option **Konfigurieren** aus, geben Sie die DNS-Namensbezeichnung **vm001** ein, und wählen Sie **Speichern** und anschließend **VM001** aus.

14. Wählen Sie auf der rechten Seite des Blatts „Übersicht“ unter dem Text „Virtuelles Netzwerk/Subnetz“ **storagetesting-vnet/default** aus.

15. Wählen Sie auf dem Blatt „storagetesting-vnet“ die Option **Subnetze** und anschließend **+Subnetz** aus. Geben Sie dann auf dem neuen Blatt „Subnetz hinzufügen“ die folgenden Informationen ein, und wählen Sie **OK** aus:

    a.  **Name**: subnet2

    b.  **Adressbereich (CIDR-Block)** : 10.10.11.0/24

    c.  **Netzwerksicherheitsgruppe**: Keine

    d.  **Routingtabelle**: Keine

16. Wählen Sie nach dem Speichern **VM001** aus.

17. Wählen Sie auf der linken Seite des Blatts „Übersicht“ die Option **Netzwerk** aus.

18. Wählen Sie **Netzwerkschnittstelle anfügen** und anschließend **Netzwerkschnittstelle erstellen** aus.

19. Geben Sie auf dem Blatt **Netzwerkschnittstelle erstellen** die folgenden Informationen ein:

    a.  **Name**: vm001nic2

    b.  **Subnetz**: Stellen Sie sicher, dass das Subnetz 10.10.11.0/24 ist.

    c.  **Netzwerksicherheitsgruppe**: VM001-nsg

    d. **Ressourcengruppe**: storagetesting

20. Wählen Sie nach dem erfolgreichen Anfügen **VM001** und anschließend **Beenden** aus, um die VM herunterzufahren.

21. Nachdem die VM beendet (Zuordnung aufgehoben) wurde, wählen Sie auf der linken Seite des Blatts „Übersicht“ die Option **Netzwerk** und dann **Netzwerkschnittstelle anfügen** aus. Wählen Sie anschließend **vm001nic2** und dann **OK** aus. Der zusätzliche Netzwerkadapter wird der VM in wenigen Augenblicken hinzugefügt.

22. Wählen Sie auf dem Blatt **Netzwerk** die Registerkarte **vm001nic2** und dann die Option **Netzwerkschnittstelle:vm001nic2** aus.

23. Wählen Sie auf dem Blatt für die Netzwerkschnittstelle vm001nic die Option **IP-Konfigurationen** und in der Mitte des Blatts **ipconfig1** aus.

24. Wählen Sie auf dem Blatt mit den Einstellungen für ipconfig1 für „Öffentliche IP-Adresse“ **Aktiviert** aus, und wählen Sie **Erforderliche Einstellungen konfigurieren** und **Neu erstellen** aus. Geben Sie „vm001nic2pip“ als Name ein, und wählen Sie **Statisch** aus. Wählen Sie anschließend **OK** und dann **Speichern** aus.

25. Wechseln Sie nach dem Speichern zurück zum Blatt mit der Übersicht für VM001, und wählen Sie **Starten** aus, um die konfigurierte Windows Server 2019-VM zu starten.

26. Nach dem Start müssen Sie **eine RDP-Sitzung mit VM001 herstellen**.

27. Sobald die Verbindung auf der VM hergestellt wurde, öffnen Sie **CMD** (als Administrator) und geben **hostname** ein, um den Computernamen des Betriebssystems abzurufen. **Er sollte mit VM001 übereinstimmen.** Notieren Sie sich diesen Namen zur späteren Verwendung.

### <a name="configure-second-network-adapter-on-windows-server-2019-vm-on-azure-stack-hub"></a>Konfigurieren eines zweiten Netzwerkadapters auf der Windows Server 2019-VM in Azure Stack Hub

Azure Stack Hub weist der ersten (primären) Netzwerkschnittstelle, die an den virtuellen Computer angefügt ist, standardmäßig ein Standardgateway zu. Azure Stack Hub weist zusätzlichen (sekundären) Netzwerkschnittstellen, die an einen virtuellen Computer angefügt sind, kein Standardgateway zu. Daher können Sie standardmäßig nicht mit Ressourcen außerhalb des Subnetzes kommunizieren, in dem sich eine sekundäre Netzwerkschnittstelle befindet. Sekundäre Netzwerkschnittstellen können jedoch mit Ressourcen außerhalb ihres Subnetzes kommunizieren. Die Schritte zum Aktivieren der Kommunikation unterscheiden sich allerdings je nach Betriebssystem.

1.  Ist noch keine Verbindung geöffnet, stellen Sie eine RDP-Verbindung mit **VM001** her.

2.  Öffnen Sie **CMD** als Administrator, und führen Sie **route print** aus. Damit sollten die beiden Schnittstellen (Hyper-V-Netzwerkadapter) auf dieser VM zurückgegeben werden.

    ![](./media/azure-stack-network-howto-extend-datacenter/image5.png)

3.  Führen Sie nun **ipconfig** aus, um zu prüfen, welche IP-Adresse der sekundären Netzwerkschnittstelle zugewiesen ist. In diesem Beispiel ist 10.10.11.4 der Schnittstelle 6 zugewiesen. Für die sekundäre Netzwerkschnittstelle wird keine Standardgateway-Adresse zurückgegeben.

    ![](./media/azure-stack-network-howto-extend-datacenter/image6.png)

4.  Um den gesamten Datenverkehr für Adressen außerhalb des Subnetzes der sekundären Netzwerkschnittstelle an das Gateway des Subnetzes zu leiten, führen Sie den folgenden Befehl über die **Befehlszeile** aus:

    ```CMD  
    route add -p 0.0.0.0 MASK 0.0.0.0 <ipaddress> METRIC 5015 IF <interface>
    ```

    `<ipaddress>` ist die .1-Adresse des aktuellen Subnetzes, und `<interface>` ist die Schnittstellennummer.

    ![](./media/azure-stack-network-howto-extend-datacenter/image7.png)

5.  Geben Sie den Befehl **route print** ein, um zu überprüfen, ob die hinzugefügte Route in der Routingtabelle enthalten ist.

    ![](./media/azure-stack-network-howto-extend-datacenter/image8.png)

6.  Sie können die ausgehende Kommunikation auch durch Ausführen eines Pingbefehls überprüfen:  
    `ping 8.8.8.8 -S 10.10.11.4`  
    Das Flag `-S` ermöglicht die Angabe einer Quelladresse. In diesem Fall ist 10.10.11.4 die IP-Adresse des Netzwerkadapters, der nun über ein Standardgateway verfügt.

7.  Schließen Sie die **Befehlszeile**.

### <a name="configure-the-windows-server-2019-iscsi-target"></a>Konfigurieren des iSCSI-Ziels von Windows Server 2019

Überprüfen Sie für dieses Szenario eine Konfiguration, bei der das iSCSI-Ziel von Windows Server 2019 eine VM ist, die außerhalb der Azure Stack Hub-Umgebung unter Hyper-V ausgeführt wird. Diese VM wird mit acht virtuellen Prozessoren und einer einzelnen VHDX-Datei und vor allem mit zwei virtuellen Netzwerkadaptern konfiguriert. In einem idealen Szenario verfügen diese Netzwerkadapter über unterschiedliche routingfähige Subnetze, in dieser Validierung haben sie jedoch Netzwerkadapter im gleichen Subnetz.

![](./media/azure-stack-network-howto-extend-datacenter/image9.png)

Für Ihren iSCSI-Zielserver könnte dies Windows Server 2016 oder 2019 sein, physisch oder virtuell, ausgeführt unter Hyper-V, VMware oder einer alternativen Appliance Ihrer Wahl, etwa einem dedizierten physischen iSCSI-SAN. Der Schwerpunkt sind ein- und ausgehende Verbindungen mit dem Azure Stack Hub-System. Mehrere vorhandene Pfade zwischen Quelle und Ziel wären jedoch von Vorteil, da damit zusätzliche Redundanz gegeben ist und fortgeschrittenere Funktionen zum Optimieren der Leistung verwendet werden können, z. B. MPIO.

Es empfiehlt sich, das iSCSI-Ziel von Windows Server 2019 mit den neuesten kumulativen Updates und Fixes zu aktualisieren und ggf. einen Neustart auszuführen, bevor Sie mit dem Konfigurieren von Dateifreigaben fortfahren.

Nach dem Aktualisieren und Neustarten können Sie diesen Server als iSCSI-Ziel konfigurieren.

1.  Öffnen Sie den **Server-Manager**, und wählen Sie **Verwalten** und anschließend **Rollen und Features hinzufügen** aus.

2.  Wählen Sie anschließend **Weiter** und **Rollenbasierte oder featurebasierte Installation** aus, und navigieren Sie durch die Auswahlbildschirme bis zur Seite **Serverrollen auswählen**.

3.  Erweitern Sie **Datei- und Speicherdienste** und **Datei- und iSCSI-Dienste**, und aktivieren Sie das Kontrollkästchen **iSCSI-Zielserver**. Akzeptieren Sie die Aufforderungen zum Hinzufügen neuer Features, und schließen Sie den Vorgang ab.

    ![](./media/azure-stack-network-howto-extend-datacenter/image10.png)

    Schließen Sie anschließend den **Server-Manager**.

4.  Öffnen Sie den **Datei-Explorer,** navigieren Sie zu C:\\, und **erstellen Sie einen neuen Ordner** namens **iSCSI**.

5.  Öffnen Sie erneut **Server-Manager**, und wählen Sie im linken Menü **Datei- und Speicherdienste** aus.

6.  Wählen Sie **iSCSI** und anschließend im rechten Bereich den Link **Starten Sie den Assistenten für neue virtuelle iSCSI-Datenträger, um einen virtuellen iSCSI-Datenträger zu erstellen.** aus. Wählen Sie dies aus. Ein Assistent wird geöffnet.

7.  Wählen Sie auf der Seite **Speicherort des virtuellen iSCSI-Datenträgers auswählen** das Optionsfeld **Type a custom path** (Benutzerdefinierten Pfad eingeben) aus, navigieren Sie zu **C:\\iSCSI**, und wählen Sie **Weiter** aus.

8.  Geben Sie dem virtuellen iSCSI-Datenträger den Namen **iSCSIdisk1**, geben Sie optional eine Beschreibung ein, und wählen Sie dann **Weiter** aus.

9.  Legen Sie die Größe des virtuellen Datenträgers auf **10 GB** fest, und wählen Sie **Feste Größe** und **Weiter** aus.

    ![](./media/azure-stack-network-howto-extend-datacenter/image11.png)

10) Da es sich um ein neues Ziel handelt, wählen Sie **New iSCSI target** (Neues iSCSI-Ziel) und dann **Weiter** aus.

11) Geben Sie auf der Seite **Zielname angeben** den Namen **TARGET1** ein, und wählen Sie **Weiter** aus.

12) Wählen Sie auf der Seite **Zugriffsserver angeben** die Option **Hinzufügen** aus. Dadurch wird ein Dialogfeld zur Eingabe bestimmter **Initiatoren** geöffnet, die zum Herstellen einer Verbindung mit dem iSCSI-Ziel autorisiert werden.

13) Wählen Sie im Fenster **Initiator-ID hinzufügen** die Option **Enter a value for the selected type** (Wert für den ausgewählten Typ eingeben) aus, und vergewissern Sie sich unter **Typ**, dass im Dropdownmenü der Eintrag „IQN“ ausgewählt ist. Geben Sie **iqn.1991-05.com.microsoft:\<computername>** ein. \<computername> ist dabei der **Computername** **VM001**. Wählen Sie anschließend **Weiter** aus.

    ![](./media/azure-stack-network-howto-extend-datacenter/image12.png)

14) Lassen Sie auf der Seite **Authentifizierung aktivieren** die Felder leer, und wählen Sie **Weiter** aus.

15) Bestätigen Sie Ihre Auswahl, wählen Sie **Erstellen** aus, und schließen Sie dann das Fenster. Ihr virtueller iSCSI-Datenträger sollte im Server-Manager erstellt werden.

    ![](./media/azure-stack-network-howto-extend-datacenter/image13.png)

### <a name="configure-the-windows-server-2019-iscsi-initiator-and-mpio"></a>Konfigurieren des iSCSI-Initiators und von Multipfad-E/A unter Windows Server 2019

Melden Sie sich zum Einrichten des iSCSI-Initiators zunächst in Ihrem **Azure Stack Hub**-System erneut beim **Azure Stack Hub-Benutzerportal** an, und navigieren Sie zum Blatt **Übersicht** für **VM001**.

1.  Stellen Sie eine RDP-Verbindung mit VM001 her. Wenn die Verbindung hergestellt wurde, öffnen Sie **Server-Manager**.

2.  Wählen Sie **Rollen und Features hinzufügen** aus, und übernehmen Sie die Standardwerte, bis Sie zur Seite **Features** gelangen.

3.  Fügen Sie auf der Seite **Features** die Option **Multipfad-E/A** hinzu, und wählen Sie **Weiter** aus.

    ![](./media/azure-stack-network-howto-extend-datacenter/image14.png)

4.  Aktivieren Sie das Kontrollkästchen **Restart the destination server automatically if necessary** (Zielserver ggf. automatisch neu starten), und wählen Sie **Installieren** und dann **Schließen** aus. Höchstwahrscheinlich ist ein Neustart erforderlich, stellen Sie daher anschließend wieder eine Verbindung mit VM001 her.

5.  Warten Sie im **Server-Manager** auf den **Abschluss der Multipfad-E/A-Installation**, und wählen Sie **Schließen** und anschließend **Tools** > **Multipfad-E/A** aus.

6.  Wählen Sie die Registerkarte **Multipfade suchen** aus, und aktivieren Sie das Kontrollkästchen **Unterstützung für iSCSI-Geräte hinzufügen**. Wählen Sie anschließend **Hinzufügen** und **Ja** aus, um VM001 **neu zu starten**. Wird kein Fenster angezeigt, wählen Sie **OK** aus, und führen Sie dann manuell einen Neustart aus.

    ![](./media/azure-stack-network-howto-extend-datacenter/image15.png)

7.  Stellen Sie nach dem Neustart eine **neue RDP-Verbindung mit VM001** her.

8.  Wenn die Verbindung hergestellt wurde, öffnen Sie **Server-Manager** und wählen **Tools** > **iSCSI-Initiator** aus.

9.  Wählen Sie im geöffneten Microsoft iSCSI-Fenster **Ja** aus, um die standardmäßige Ausführung des iSCSI-Diensts zuzulassen.

    ![](./media/azure-stack-network-howto-extend-datacenter/image16.png)

10. Wählen Sie im Fenster „Eigenschaften des iSCSI-Initiators“ die Registerkarte **Erkennung** aus.

11. Sie fügen nun zwei Ziele hinzu. Wählen Sie daher zunächst die Schaltfläche**Portal ermitteln** aus.

12. Geben Sie die erste IP-Adresse des iSCSI-Zielservers ein, und wählen Sie dann **Erweitert** aus.

    ![](./media/azure-stack-network-howto-extend-datacenter/image17.png)

13. Wählen Sie im Fenster **Erweiterte Einstellungen** Folgendes und dann **OK** aus.

    a.  **Lokaler Adapter**: Microsoft iSCSI-Initiator

    b.  **Initiator-IP**: 10.10.10.4

14. Wählen Sie im Fenster **Zielportal ermitteln** die Option **OK** aus.

15. Wiederholen Sie den Prozess mit den folgenden Angaben:

    a. **IP-Adresse**: Zweite IP-Adresse des iSCSI-Ziels

    b.  **Lokaler Adapter**: Microsoft iSCSI-Initiator

    c.  **Initiator-IP**: 10.10.11.4

16. Ihr Zielportal sollte wie folgt aussehen (mit Ihren eigenen iSCSI-Ziel-IPs in der Spalte **Adresse**):

    ![](./media/azure-stack-network-howto-extend-datacenter/image18.png)

17. Wählen Sie auf der Registerkarte **Ziele** in der Mitte des Fensters Ihr iSCSI-Ziel und anschließend **Verbinden** aus.

18. Aktivieren Sie im Fenster **Mit Ziel verbinden** das Kontrollkästchen **Multipfad aktivieren**, und wählen Sie dann **Erweitert** aus.

    ![](./media/azure-stack-network-howto-extend-datacenter/image19.png)

19. Geben Sie die folgenden Informationen ein, und wählen Sie **OK** und dann im Fenster **Mit Ziel verbinden** die Option **OK** aus.

    a.  **Lokaler Adapter**: Microsoft iSCSI-Initiator

    b.  **Initiator-IP**: 10.10.10.4

    c.  **Zielportal-IP**: \<Ihre erste iSCSI-Ziel-IP/3260>

![](./media/azure-stack-network-howto-extend-datacenter/image20.png)

1.  Wiederholen Sie den Vorgang für die zweite Kombination aus Initiator und Ziel.

    a. **Lokaler Adapter**: Microsoft iSCSI-Initiator

    b.  **Initiator-IP**: 10.10.11.4

    c.  **Zielportal-IP**: \<Ihre zweite iSCSI-Ziel-IP/3260>

        ![](./media/azure-stack-network-howto-extend-datacenter/image21.png)

2.  Wählen Sie die Registerkarte **Volumes und Geräte** und anschließend **Automatisch konfigurieren** aus. Nun sollte ein Multipfad-E/A-Volume angezeigt werden:

    ![](./media/azure-stack-network-howto-extend-datacenter/image22.png)

3.  Wählen Sie auf der Registerkarte **Ziele** die Option **Geräte** aus, und es sollten zwei Verbindungen mit der zuvor erstellten einzelnen iSCSI-VHD angezeigt werden.

    ![](./media/azure-stack-network-howto-extend-datacenter/image23.png)

4.  Wählen Sie die Schaltfläche **Multipfad-E/A** aus, um weitere Informationen zur Richtlinie für den Lastenausgleich und zu den Pfaden anzuzeigen.

    ![](./media/azure-stack-network-howto-extend-datacenter/image24.png)

5.  Wählen Sie dreimal **OK** aus, um die Fenster und den iSCSI-Initiator zu schließen.

6.  Öffnen Sie die Datenträgerverwaltung (diskmgmt.msc), und das Fenster **Datenträger initialisieren** sollte angezeigt werden.

    ![](./media/azure-stack-network-howto-extend-datacenter/image25.png)

7.  Wählen Sie **OK** aus, um die Standardeinstellungen zu übernehmen. Scrollen Sie dann nach unten zu dem neuen Datenträger, klicken Sie mit der rechten Maustaste, und wählen Sie **Neues einfaches Volume** aus.

8.  Navigieren Sie unter Verwendung der angegebenen Standardwerte durch den Assistenten. Ändern Sie die Volumebezeichnung in **iSCSIdisk1**, und wählen Sie dann **Fertig stellen** aus.

    ![](./media/azure-stack-network-howto-extend-datacenter/image26.png)

9.  Das Laufwerk sollte formatiert und mit einem Laufwerkbuchstaben versehen werden.

10. Öffnen Sie den **Datei-Explorer** > **Dieser PC**, um das an VM001 angefügte neue Laufwerk anzuzeigen.

### <a name="testing-external-storage-connectivity"></a>Testen der Konnektivität des externen Speichers

Melden Sie sich zum Überprüfen der Kommunikation und Ausführen eines grundlegenden Dateikopiertests zunächst im **Azure Stack Hub**-System erneut beim **Azure Stack Hub-Benutzerportal** an, und navigieren Sie zum Blatt **Übersicht** für **VM001**.

1.  Wählen Sie **Verbinden** aus, um eine RDP-Verbindung mit **VM001** herzustellen.

2.  Öffnen Sie **Task-Manager**, wählen Sie die Registerkarte **Leistung** aus, und docken Sie dann das Fenster an der rechten Seite der RDP-Sitzung an.

3.  Öffnen Sie **Windows PowerShell ISE** als Administrator, und docken Sie das Fenster an der linken Seite der RDP-Sitzung an. Schließen Sie auf der rechten Seite der ISE den Bereich **Befehle**, und wählen Sie die Schaltfläche **Skript** aus, um den weißen Skriptbereich oben im ISE-Fenster zu öffnen.

4.  Auf dieser VM sind keine nativen PowerShell-Module zum Erstellen einer VHD vorhanden, die als große Datei zum Testen der Dateiübertragung auf das iSCSI-Ziel verwendet wird. In diesem Fall führen Sie DiskPart zum Erstellen einer VHD-Datei aus. Führen Sie in der ISE Folgendes aus:

    1. `Start-Process Diskpart`

    2. Ein neues CMD-Fenster wird geöffnet. Geben Sie Folgendes ein:  
        `**Create vdisk file="c:\\test.vhd" type=fixed maximum=5120**`
    
    ![](./media/azure-stack-network-howto-extend-datacenter/image27.png)
    
    3.  Die Erstellung kann einen Moment dauern. Öffnen Sie anschließend zur Überprüfung der Erstellung den **Datei-Explorer**, und navigieren Sie zu C:\\. Die neue Datei „test.vhd“ mit einer Größe von 5 GB sollte angezeigt werden.

    ![](./media/azure-stack-network-howto-extend-datacenter/image28.png)

    4. Schließen Sie das CMD-Fenster, kehren Sie zur ISE zurück, und geben Sie dann den folgenden Befehl in das Skriptfenster ein. Ersetzen Sie F:\\ durch den zuvor angewendeten Laufwerkbuchstabe des iSCSI-Ziels.

    5. `Copy-Item "C:\\test.vhd" -Destination "F:\\"`

    6. Wählen Sie die Zeile im Skriptfenster aus, und drücken Sie für die Ausführung F8.

    7. Beobachten Sie während der Befehlsausführung die zwei Netzwerkadapter. Sie sehen, dass die Daten auf beide Netzwerkadapter auf VM001 übertragen werden. Darüber hinaus sollte Ihnen auffallen, dass die Last gleichmäßig auf beide Netzwerkadapter verteilt wird.

    ![](./media/azure-stack-network-howto-extend-datacenter/image29.png)

Mit diesem Szenario soll die Konnektivität zwischen einer in Azure Stack Hub ausgeführten Workload und einem externen Speicherarray (in diesem Fall ein Windows Server-basiertes iSCSI-Ziel) veranschaulicht werden. Es ist weder als Leistungstest ausgelegt noch spiegelt es die Schritte wider, die Sie bei Verwendung einer alternativen iSCSI-basierten Appliance ausführen müssen. Es werden jedoch einige der wichtigsten Aspekte behandelt, die beim Bereitstellen von Workloads in Azure Stack Hub und beim Verbinden dieser Workloads mit Speichersystemen außerhalb der Azure Stack Hub-Umgebung berücksichtigt werden müssen.

## <a name="next-steps"></a>Nächste Schritte

[Azure Stack Hub-Netzwerke: Unterschiede und Überlegungen](azure-stack-network-differences.md)
