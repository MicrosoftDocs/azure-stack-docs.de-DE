---
title: Erweitern des Rechenzentrums in Azure Stack Hub | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie das Rechenzentrum in Azure Stack erweitern.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/07/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 11/07/2019
ms.openlocfilehash: 92e82f549cddf51b1cbd6764cc122acc3ca8fdfc
ms.sourcegitcommit: ed44d477b9fd11573d1e0d1ed3a3c0ef4512df53
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73845978"
---
# <a name="how-to-extend-the-data-center-on--azure-stack-hub"></a>Erweitern des Rechenzentrums in Azure Stack Hub

*Anwendungsbereich: Integrierte Azure Stack Hub-Systeme und Azure Stack Development Kit*

Dieser Artikel enthält Informationen zur Speicherinfrastruktur von Azure Stack Hub, die Sie bei der Entscheidung unterstützen, wie Sie Azure Stack in Ihre bestehende Netzwerkumgebung integrieren. Nach allgemeinen Darlegungen zum Erweitern Ihres Rechenzentrums werden im Artikel zwei unterschiedliche Szenarien vorgestellt. Sie können eine Verbindung mit einem Windows File Storage-Server herstellen. Sie können auch eine Verbindung mit einem Windows iSCSI-Server herstellen.

## <a name="overview-of-extending-storage-to-azure-stack-hub"></a>Übersicht über das Erweitern von Speicher auf Azure Stack Hub

Es gibt Szenarien, in denen es nicht ausreicht, wenn sich die Daten in der öffentlichen Cloud befinden. Vielleicht besitzen Sie eine rechenintensive virtualisierte Datenbankworkload, die empfindlich auf Wartezeiten reagiert, und die Roundtripzeit zur öffentlichen Cloud könnte sich auf die Leistung der Datenbankworkload auswirken. Oder es sind unter Umständen auf einem Dateiserver, einem NAS-Gerät oder in einem iSCSI-Speicherarray gespeicherte lokale Daten vorhanden, auf die von lokalen Workloads zugegriffen werden muss und die sich in der lokalen Umgebung befinden müssen, um gesetzliche Bestimmungen einzuhalten oder Complianceziele zu erreichen.  Dies sind nur zwei Szenarien, in denen es für viele Organisationen wichtig ist, dass sich die Daten lokal befinden.

Warum hosten Sie also diese Daten nicht einfach in Speicherkonten in Azure Stack oder auf virtualisierten Dateiservern, die im Azure Stack-System ausgeführt werden? Im Gegensatz zu Azure ist der Azure Stack-Speicher endlich. Die für die Nutzung verfügbare Kapazität hängt gänzlich von der von Ihnen erworbenen Kapazität pro Knoten sowie der Anzahl Ihrer Knoten ab. Und da es sich bei Azure Stack um eine hyperkonvergierte Lösung handelt, müssen Sie zur Erweiterung Ihrer Speicherkapazität auch Ihre Computekapazität erhöhen, indem Sie weitere Knoten hinzufügen, um den Nutzungsanforderungen gerecht zu werden.  Dies scheitert unter Umständen an den Kosten, insbesondere, wenn Bedarf an zusätzlicher Kapazität für kalten Speicher oder Archivspeicher besteht, der zu geringen Kosten außerhalb des Azure Stack-Systems hinzugefügt werden könnte.

Und das bringt uns zum folgenden Szenario: Wie können Sie Azure Stack-Systeme und ihre in Azure Stack ausgeführten virtualisierten Workloads einfach und effizient mit Speichersystemen außerhalb von Azure Stack verbinden, auf die über das Netzwerk zugegriffen wird?

## <a name="design-for-extending-storage"></a>Entwurf für die Erweiterung des Speichers

In der Abbildung wird ein Szenario veranschaulicht, in dem eine einzelne VM mit einer Workload zum Lesen/Schreiben von Daten und andere Zwecke eine Verbindung mit externem Speicher herstellt (extern für die VM und die Azure Stack-Instanz selbst). In diesem Artikel konzentrieren wir uns auf das einfache Abrufen von Dateien. Sie können dieses Beispiel jedoch auch auf komplexere Szenarien ausweiten, z. B. die Remotespeicherung von Datenbankdateien.

![Erweitern von Speicher auf Azure Stack](./media/azure-stack-network-howto-extend-datacenter/image1.png)

In der Abbildung ist ersichtlich, dass die VM im Azure Stack-System mit mehreren Netzwerkkarten bereitgestellt wurde. Sowohl wegen der Redundanz als auch der bewährten Methode hinsichtlich des Speichers ist es unerlässlich, dass zwischen Quelle und Ziel mehrere Pfade vorhanden sind. Komplexer werden die Dinge jedoch, wenn VMs in Azure Stack über öffentliche und auch über private IP-Adressen verfügen, wie z. B. in Azure. Wenn eine Verbindung zwischen externem Speicher und VM erforderlich ist, ist dies nur über die öffentliche IP-Adresse möglich, da die privaten IP-Adressen hauptsächlich in den Azure Stack-Systemen, in VNETs und den Subnetzen verwendet werden. Der externe Speicher wäre nicht in der Lage, mit dem privaten IP-Adressbereich der VM zu kommunizieren, sofern er kein Site-to-Site-VPN durchläuft, um eine Verbindung mit dem VNET selbst herzustellen. Daher konzentrieren wir uns in diesem Beispiel auf die Kommunikation mit dem öffentlichen IP-Adressraum. Ein bemerkenswerter Aspekt des öffentlichen IP-Adressraums in der Abbildung ist, dass zwei unterschiedliche Subnetze für öffentliche IP-Adresspools vorhanden sind. Standardmäßig ist in Azure Stack nur ein Pool für öffentliche IP-Adressen erforderlich, aber für das redundante Routing empfiehlt es sich möglicherweise, einen zweiten hinzuzufügen. Es ist jedoch nicht möglich, eine IP-Adresse aus einem bestimmten Pool auszuwählen, und sie erhalten VMs mit öffentlichen IP-Adressen aus demselben Pool für mehrere virtuelle Netzwerkkarten.

In diesem Beispiel können Sie davon ausgehen, dass das Routing zwischen den Peripheriegeräten und dem externen Speicher erfolgt und der Datenverkehr das Netzwerk entsprechend durchlaufen kann. Hier ist es unerheblich, ob der Backbone eine Geschwindigkeit von 1 GbE, 10 GbE, 25 GbE oder sogar mehr hat. Dies sollte jedoch bedacht werden, wenn Sie die Integration planen, damit die Leistungsanforderungen von Anwendungen berücksichtigt werden, die auf diesen externen Speicher zugreifen.

## <a name="connect-to-a-windows-server-file-server-storage"></a>Herstellen einer Verbindung mit einem Windows Server-Dateiserverspeicher

In diesem Beispiel wird eine Windows Server 2019-VM in Azure Stack bereitgestellt, konfiguriert und für Verbindungen mit einem externen Dateiserver vorbereitet, auf dem ebenfalls Windows Server 2019 ausgeführt werden kann. Legen Sie ggf. Schlüsselfunktionen wie SMB Multichannel fest, um Leistung und Konnektivität zwischen der VM und dem externen Speicher zu optimieren.

### <a name="deploy-the-windows-server-2019-vm-on-azure-stack"></a>Bereitstellen der Windows Server 2019-VM in Azure Stack

1.  Es wird davon ausgegangen, dass das System ordnungsgemäß konfiguriert und mit dem Marketplace verbunden ist. Wählen Sie im **Azure Stack-Administratorportal** die Option **Marketplace Management** (Marketplace-Verwaltung) und, da sie noch nicht über ein Windows Server 2019-Image verfügen,die Option **Add from Azure** (Aus Azure hinzufügen) aus. Suchen Sie dann nach **Windows Server 2019**, und fügen Sie das Image **Windows Server 2019 Datacenter** hinzu.

    ![](./media/azure-stack-network-howto-extend-datacenter/image2.png)

    Das Herunterladen eines Windows Server 2019-Images kann eine gewisse Zeit dauern.

2.  Wenn Sie über ein Windows Server 2019-Image in Ihrer Azure Stack-Umgebung verfügen, melden Sie sich beim Azure Stack-Benutzerportal an**.

3.  Stellen Sie nach der Anmeldung beim Azure Stack-Benutzerportal sicher, dass Sie über ein [Abonnement eines Angebots](https://docs.microsoft.com/azure-stack/operator/azure-stack-subscribe-plan-provision-vm?view=azs-1908) verfügen, das Ihnen die Bereitstellung von IaaS-Ressourcen (Compute, Speicher und Netzwerk) ermöglicht.

4.  Wenn Sie über ein Abonnement verfügen, wählen Sie wiederum im **Dashboard** im Azure Stack-Benutzerportal die Option **Ressource erstellen** aus. Wählen Sie **Compute** und anschließend das Katalogelement **Windows Server 2019 Datacenter** aus.

5.  Gehen Sie auf dem Blatt **Grundlagen** folgendermaßen vor:

    a.  **Name**: VM001

    b.  **Benutzername**: localadmin

    c.  **Kennwort** und **Kennwort bestätigen**: \<Kennwort Ihrer Wahl>

    d.  **Abonnement**: \<Abonnement Ihrer Wahl, mit Compute-/Speicher-/Netzwerkressourcen>

    e. **Ressourcengruppe**: Neu erstellen: storagetesting

    f.  Wählen Sie anschließend **OK** aus.

6.  Wählen Sie auf dem Blatt **Größe auswählen** die Größe **Standard_F8s_v2** aus.

7.  Wählen Sie auf dem Blatt **Einstellungen** die Option **Virtuelles Netzwerk** aus, ändern Sie auf dem Blatt **Virtuelles Netzwerk erstellen** den Adressraum in **10.10.10.0/23**, und aktualisieren Sie den Subnetz-Adressraum auf **10.10.10.0/24**. Wählen Sie dann **OK** aus.

8.  Wählen Sie die **öffentliche IP-Adresse** aus, und wählen Sie auf dem Blatt **Öffentliche IP-Adresse erstellen** die Option **Statisch** aus.

9.  Wählen Sie unter **Öffentliche Eingangsports hinzufügen** die Option **RDP (3389)** aus.

10. Übernehmen Sie übrigen Standardwerte, und wählen Sie **OK** aus.
    
    ![](./media/azure-stack-network-howto-extend-datacenter/image3.png)

11. Lesen Sie die Zusammenfassung, warten Sie auf die Validierung, und wählen Sie **OK** aus, um den Bereitstellungsvorgang zu starten. Die Bereitstellung sollte in ca. 10 Minuten abgeschlossen sein.

12. Wählen Sie nach Abschluss der Bereitstellung unter **Ressource** den VM-Namen **VM001** aus, um zum Blatt *Übersicht* zu wechseln.
    
    ![](./media/azure-stack-network-howto-extend-datacenter/image4.png)

13. Wählen Sie unter „DNS-Name“ die Option **Konfigurieren** aus, geben Sie die DNS-Namensbezeichnung „**vm001“ ein, und wählen Sie **Speichern** aus. Wählen Sie anschließend **VM001** im Breadcrumb aus, um zum Blatt *Übersicht* zurückzukehren.

14. Wählen Sie auf der rechten Seite des Blatts „Übersicht“ unter dem Text „Virtuelles Netzwerk/Subnetz“ **storagetesting-vnet/default** aus.

15. Wählen Sie auf dem Blatt „storagetesting-vnet“ die Option **Subnetze** und anschließend **+Subnetz** aus. Geben Sie dann auf dem neuen Blatt „Subnetz hinzufügen“ die folgenden Informationen ein: 

    a.  **Name**: subnet2

    b.  **Adressbereich (CIDR-Block)** : 10.10.11.0/24

    c. **Netzwerksicherheitsgruppe**: Keine

    d.  **Routingtabelle**: Keine

    e. Wählen Sie **OK** aus:

16. Wählen Sie nach dem Speichern **VM001** im Breadcrumb aus, um zum Blatt mit der Übersicht zurückzukehren.

17. Wählen Sie **Netzwerk** aus.

18. Wählen Sie **Netzwerkschnittstelle anfügen** und anschließend **Netzwerkschnittstelle erstellen** aus.

19. Auf dem Blatt **Netzwerkschnittstelle erstellen**:

    a.  **Name**: vm001nic2

    b.  **Subnetz**: Stellen Sie sicher, dass das Subnetz 10.10.11.0/24 ist.

    c. **Netzwerksicherheitsgruppe**: VM001-nsg

    d.  **Ressourcengruppe**: storagetesting

20. Wählen Sie nach der erfolgreichen Erstellung **VM001** im Breadcrumb aus, und wählen Sie anschließend **Beenden** aus, um die VM herunterzufahren.

21. Nachdem die VM beendet (Zuordnung aufgehoben) wurde, wählen Sie **Netzwerk** und **Netzwerkschnittstelle anfügen** aus. Wählen Sie anschließend **vm001nic2** und dann **OK** aus. Dadurch fügen Sie der VM in wenigen Augenblicken eine zusätzliche Netzwerkschnittstelle hinzu.

22. Wählen Sie auf dem Blatt **Netzwerk** die Registerkarte **vm001nic2** aus, und wählen Sie **Netzwerkschnittstelle:vm001nic2** aus.

23. Wählen Sie auf dem Blatt für die Netzwerkschnittstelle vm001nic die Option **IP-Konfigurationen** und in der Mitte des Blatts **ipconfig1** aus.

24. Wählen Sie auf dem Blatt mit den Einstellungen für ipconfig1 für „Öffentliche IP-Adresse“ **Aktiviert** aus, und wählen Sie **Erforderliche Einstellungen konfigurieren** und **Neu erstellen** aus. Geben Sie „vm001nic2pip“ als Name ein, und wählen Sie **Statisch** aus. Wählen Sie anschließend **OK** und dann **Speichern** aus.

25. Wechseln Sie nach dem Speichern zurück zum Blatt mit der Übersicht für VM001, und wählen Sie **Starten** aus, um die konfigurierte Windows Server 2019-VM zu starten.

### <a name="configure-the-windows-server-2019-file-server-storage"></a>Konfigurieren des Windows Server 2019-Dateiserverspeichers

Für dieses erste Beispiel prüfen Sie eine Konfiguration, bei welcher der Windows Server 2019-Dateiserver eine unter Hyper-V ausgeführte VM ist. Diese VM wird mit acht virtuellen Prozessoren und einer einzelnen VHDX-Datei und vor allem mit zwei virtuellen Netzwerkadaptern konfiguriert. In einem idealen Szenario verfügen diese Netzwerkadapter über unterschiedliche routingfähige Subnetze, in diesem Test haben sie jedoch Netzwerkadapter im gleichen Subnetz.

![](./media/azure-stack-network-howto-extend-datacenter/image5.png)

Für Ihren Dateiserver könnte dies Windows Server 2016 oder 2019 sein, physisch oder virtuell, ausgeführt unter Hyper-V, VMware oder einer alternativen Plattform Ihrer Wahl. Der Schwerpunkt sind ein- und ausgehende Verbindungen mit dem Azure Stack-System. Mehrere vorhandene Pfade zwischen Quelle und Ziel wären jedoch von Vorteil, da damit zusätzliche Redundanz gegeben ist und fortgeschrittenere Funktionen zum Optimieren der Leistung verwendet werden können, z. B. SMB Multichannel.

Aktualisieren Sie den Dateiserver mit den neuesten kumulativen Updates und Fixes, und führen Sie einen Neustart aus, bevor Sie mit dem Konfigurieren von Dateifreigaben fortfahren.

Nach dem Aktualisieren und Neustarten können Sie diesen Server als Dateiserver konfigurieren.

1) Führen Sie auf dem Dateiservercomputer **ipconfig /all** unter **CMD** aus, und notieren Sie sich den **DNS-Server**, den Ihr Dateiserver verwendet.

2)  Öffnen Sie den **Server-Manager**, und wählen Sie **Verwalten** und anschließend **Rollen und Features hinzufügen** aus.

3)  Wählen Sie **Weiter** und **Rollenbasierte oder featurebasierte Installation** aus, und navigieren Sie durch die Auswahlbildschirme bis zur Seite **Serverrollen auswählen**.

4)  Erweitern Sie **Datei- und Speicherdienste** und **Datei- und iSCSI-Dienste**, und wählen Sie **Dateiserver** aus. Schließen Sie anschließend den **Server-Manager**.

5)  Öffnen Sie den **Server-Manager** erneut, und wählen Sie die Option **Datei- und Speicherdienste** aus.

6)  Wählen Sie **Freigaben** aus.

7)  Wählen Sie im Feld **Freigaben** den Link **Starten Sie den Assistenten für neue Freigaben, um die Dateifreigabe zu erstellen** aus.

8)  Wählen Sie im Feld **Assistent für neue Freigaben** die Option **SMB-Freigabe – Schnell** aus. Wählen Sie anschließend **Weiter**, und navigieren Sie durch die Bildschirme des Assistenten. Wählen Sie das **Volume C:\\\\**  aus.

9)  Benennen Sie die Freigabe **TestStorage**, und wählen Sie **Weiter** aus.

10) Wählen Sie im **Assistenten für neue Freigaben** **Weiter** und anschließend **Erstellen** und **Schließen** aus.

Sie haben nun die Dateifreigabe auf Ihrem Dateiserver erstellt.

### <a name="testing-file-storage-performance-and-connectivity"></a>Testen der von Speicherleistung und -konnektivität

Melden Sie sich zum Prüfen der Kommunikation und Ausführen einiger grundlegender Tests beim Azure Stack-Benutzerportal im **Azure Stack**-System an, und navigieren Sie zum Blatt **Übersicht** für **VM001**.

1)  Wählen Sie **Verbinden** aus, um eine RDP-Verbindung mit VM001 herzustellen.

2)  Zum Kommunizieren über den Hostnamen müssen Sie die Datei **Hosts** bearbeiten; in einer Produktionsumgebung ist DNS jedoch optimalerweise so konfiguriert, dass derartige Namen automatisch aufgelöst werden. Sie könnten auch IP-Adressen verwenden. In diesem Beispiel bearbeiten Sie jedoch die Datei **Hosts**.

3)  Öffnen Sie **Editor** und wählen Sie **Als Administrator ausführen** aus.

4)  Wenn Editor geöffnet ist, wählen Sie **Datei**, **Öffnen** aus, und navigieren Sie zu C:\\Windows\System32\Drivers\etc\,, und wählen Sie unten rechts im Dialogfeld „Öffnen“ die Option **Alle Dateien** aus. Wählen Sie die Datei **Hosts** aus, und wählen Sie **Öffnen** aus.

5)  Bearbeiten Sie die Datei „Hosts“, indem Sie eine IP-Adresse und einen DNS-Namen für den Dateiserver hinzufügen.

    ![](./media/azure-stack-network-howto-extend-datacenter/image6.png)

6)  Speichern Sie die Datei, und schließen Sie Editor.

7)  Öffnen Sie **CMD**, senden Sie eine Ping-Anforderung mit dem Namen des Dateiservers, den Sie in der Datei „Hosts“ eingegeben haben. Dadurch wird die IP-Adresse eines der Netzwerkadapter auf dem Dateiserver zurückgegeben. Prüfen Sie daher den anderen Adapter manuell, indem Sie ein Ping-Signal mit der IP-Adresse senden.

## <a name="connect-to-a-windows-server-iscsi-target-server"></a>Herstellen einer Verbindung mit einem iSCSI-Zielserver von Windows Server

In diesem Beispiel wird eine Windows Server 2019-VM in Azure Stack bereitgestellt, konfiguriert und für das Herstellen einer Verbindung mit einem externen iSCSI-Zielserver hergestellt. Dadurch wird auch Windows Server 2019 ausgeführt.

> [!Note]  
> Wenn Sie bereits das Szenario 1 abgeschlossen haben, springen Sie vor, und passen Sie Ihre Computer an.

### <a name="deploy-the-windows-server-2019-vm-on-azure-stack"></a>Bereitstellen der Windows Server 2019-VM in Azure Stack

Wenn Sie die Windows Server 2019-VM noch nicht in Azure Stack bereitgestellt haben, führen Sie die Schritte unter [Bereitstellen der Windows Server 2019-VM in Azure Stack](#deploy-the-windows-server-2019-vm-on-azure-stack) aus. Anschließend können Sie das iSCSI-Ziel von Windows Server 2019 konfigurieren.

### <a name="configure-the-windows-server-2019-iscsi-target"></a>Konfigurieren des iSCSI-Ziels von Windows Server 2019

Überprüfen Sie für dieses zweite Szenario eine Konfiguration, bei der das iSCSI-Ziel von Windows Server 2019 eine VM ist, die unter Hyper-V ausgeführt wird. Diese VM wird mit acht virtuellen Prozessoren und einer einzelnen VHDX-Datei und vor allem mit zwei virtuellen Netzwerkadaptern konfiguriert. In einem idealen Szenario verfügen diese Netzwerkadapter über unterschiedliche routingfähige Subnetze, in diesem Test haben Sie jedoch Netzwerkadapter im gleichen Subnetz.

![](./media/azure-stack-network-howto-extend-datacenter/image5.png)

Für Ihr iSCSI-Ziel könnte dies Windows Server 2016 oder 2019 sein, physisch oder virtuell, ausgeführt unter Hyper-V, VMware oder einer alternativen Plattform Ihrer Wahl. Der Schwerpunkt sind ein- und ausgehende Verbindungen mit dem Azure Stack-System. Mehrere vorhandene Pfade zwischen Quelle und Ziel wären jedoch von Vorteil, da damit zusätzliche Redundanz und Durchsatz gegeben sind.

Aktualisieren Sie den Dateiserver mit den neuesten kumulativen Updates und Fixes, und führen Sie einen Neustart aus, bevor Sie mit dem Konfigurieren des iSCSI-Zielservers fortfahren.

Nach dem Aktualisieren und Neustarten können Sie diesen Server als iSCSI-Zielserver konfigurieren.

1. Öffnen Sie **Server-Manager** > **Verwalten** > **Rollen und Features hinzufügen**.

2. Wählen Sie anschließend **Weiter** und **Rollenbasierte oder featurebasierte Installation** aus, und navigieren Sie durch die Auswahlbildschirme bis zur Seite **Serverrollen auswählen**.

3. Erweitern Sie **Datei- und Speicherdienste** und **Datei- und iSCSI-Dienste**, und wählen Sie **iSCSI-Zielserver** aus. Akzeptieren Sie die Aufforderungen zum Hinzufügen neuer Features, und schließen Sie den Vorgang ab.

    ![](./media/azure-stack-network-howto-extend-datacenter/image8.png)
    
    Schließen Sie anschließend den **Server-Manager**.

4. Öffnen Sie den **Datei-Explorer,** navigieren Sie zu `C:\\`, und **erstellen Sie einen neuen Ordner** namens `iSCSI`.

5. Öffnen Sie erneut im linken Menü **Server-Manager** > **Datei- und Speicherdienste**.

6. Wählen Sie **iSCSI** und anschließend **Starten Sie den Assistenten für neue virtuelle iSCSI-Datenträger, um einen virtuellen iSCSI-Datenträger zu erstellen.** aus.

7. Wählen Sie auf der Seite **Speicherort des virtuellen iSCSI-Datenträgers auswählen** die Option **Type a custom path** (Benutzerdefinierten Pfad eingeben) aus, und navigieren Sie zum Ordner `C:\\iSCSI`. Klicken Sie auf **Weiter**.

8. Geben Sie dem virtuellen iSCSI-Datenträger den Namen `iSCSIdisk1`, geben Sie optional eine Beschreibung ein, und wählen Sie dann **Weiter** aus.

9. Legen Sie die Größe des virtuellen Datenträgers auf `10GB` fest, und wählen Sie **Feste Größe** und **Weiter** aus.

    ![](./media/azure-stack-network-howto-extend-datacenter/image9.png)

10. Da es sich um ein neues Ziel handelt, wählen Sie **New iSCSI target** (Neues iSCSI-Ziel) und dann **Weiter** aus.

11. Geben Sie auf der Seite **Zielname angeben** den Namen **TARGET1** ein, und wählen Sie **Weiter** aus.

12. Wählen Sie auf der Seite **Zugriffsserver angeben** die Option **Hinzufügen** aus. Dadurch wird ein Dialogfeld zur Eingabe bestimmter **Initiatoren** geöffnet, die zum Herstellen einer Verbindung mit dem iSCSI-Ziel autorisiert werden.

13. Wählen Sie im Fenster **Initiator-ID hinzufügen** die Option **Enter a value for the selected type** (Wert für den ausgewählten Typ eingeben) aus, und vergewissern Sie sich unter **Typ**, dass im Dropdownmenü der Eintrag „IQN“ ausgewählt ist. Geben Sie `iqn.1991-05.com.microsoft:<computername>` ein. Dabei ist `<computername>` der **Computername** **VM001**. Klicken Sie auf **Weiter**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image10.png)

14. Lassen Sie auf der Seite **Authentifizierung aktivieren** die Felder leer, und wählen Sie **Weiter** aus.

15. Bestätigen Sie Ihre Auswahl, wählen Sie **Erstellen** aus, und schließen Sie dann das Fenster.

    ![](./media/azure-stack-network-howto-extend-datacenter/image11.png)

Ihr virtueller iSCSI-Datenträger sollte im Server-Manager erstellt werden.

### <a name="configure-the-windows-server-2019-iscsi-initiator-and-mpio"></a>Konfigurieren des iSCSI-Initiators und von Multipfad-E/A unter Windows Server 2019

Melden Sie sich zum Einrichten des iSCSI-Initiators in Ihrem **Azure Stack**-System beim **Benutzerportal** an, und navigieren Sie zum Blatt **Übersicht** für **VM001**.

1.  Stellen Sie eine RDP-Verbindung mit VM001 her. Wenn die Verbindung hergestellt wurde, öffnen Sie **Server-Manager**.

2.  Wählen Sie **Rollen und Features hinzufügen** aus, und übernehmen Sie die Standardwerte, bis Sie zur Seite **Features** gelangen.

3.  Fügen Sie auf der Seite **Features** die Option **Multipfad-E/A** hinzu, und wählen Sie **Weiter** aus.

    ![](./media/azure-stack-network-howto-extend-datacenter/image12.png)

4.  Aktivieren Sie das Kontrollkästchen **Restart the destination server automatically if necessary** (Zielserver ggf. automatisch neu starten), und wählen Sie **Installieren** und dann **Schließen** aus. Höchstwahrscheinlich ist ein Neustart erforderlich, stellen Sie daher anschließend wieder eine Verbindung mit VM001 her.

5.  Warten Sie im **Server-Manager** auf den **Abschluss der Multipfad-E/A-Installation**, und wählen Sie **Schließen** und anschließend **Tools** > **Multipfad-E/A** aus.

6.  Wählen Sie die Registerkarte **Multipfade suchen** aus, und aktivieren Sie das Kontrollkästchen **Unterstützung für iSCSI-Geräte hinzufügen**. Wählen Sie anschließend  > **Hinzufügen** und **Ja** aus, um VM001 **neu zu starten**. Wählen Sie **OK** aus, und führen Sie anschließend manuell einen Neustart aus.

    ![](./media/azure-stack-network-howto-extend-datacenter/image13.png)

1.  Stellen Sie nach dem Neustart eine **neue RDP-Verbindung mit VM001** her.

2.  Wenn die Verbindung hergestellt wurde, öffnen Sie **Server-Manager** und wählen **Tools** > **iSCSI-Initiator** aus.

3.  Wählen Sie **Ja** aus, wenn Microsoft iSCSI geöffnet wird, um die standardmäßige Ausführung des iSCSI-Diensts zuzulassen.

    ![](./media/azure-stack-network-howto-extend-datacenter/image17.png)

4.  Wählen Sie die Registerkarte **Ermittlung** aus.

5.  Wählen Sie die Schaltfläche **Portal ermitteln** aus. Sie fügen nun zwei Ziele hinzu.

6.  Geben Sie die erste IP-Adresse des iSCSI-Zielservers ein, und wählen Sie dann **Erweitert** aus.

    ![](./media/azure-stack-network-howto-extend-datacenter/image15.png)

7. Wählen Sie unter **Erweiterte Einstellungen** Folgendes aus:

    - Lokaler Adapter: Microsoft iSCSI-Initiator

    - Initiator-IP: 10.10.10.4

    - Wählen Sie **OK** aus, wenn alle Werte festgelegt sind.

8.  Wählen Sie unter **Zielportal ermitteln** die Option **OK** aus.

9.  Wiederholen Sie den Prozess mit den folgenden Angaben:

    - IP-Adresse: Zweite IP-Adresse des iSCSI-Ziels

    - Lokaler Adapter: Microsoft iSCSI-Initiator

    - Initiator-IP: 10.10.11.4

10. Ihr Zielportal sollte wie folgt aussehen (mit Ihren eigenen iSCSI-Ziel-IPs in der Spalte **Adresse**):

    ![](./media/azure-stack-network-howto-extend-datacenter/image16.png)

11. Wählen Sie die Registerkarte **Ziele** und dann Ihr iSCSI-Ziel aus. Wählen Sie **Verbinden**aus.

12. Wählen Sie unter **Mit Ziel verbinden** die Option **Multipfad aktivieren** und dann **Erweitert** aus.

    ![](./media/azure-stack-network-howto-extend-datacenter/image17.png)

13. Geben Sie unter **Mit Ziel verbinden** die folgenden Informationen ein:

    - Lokaler Adapter: Microsoft iSCSI-Initiator

    - Initiator-IP: 10.10.10.4

    - Zielportal-IP: \<Ihre erste iSCSI-Ziel-IP/3260>

    - Klicken Sie auf **OK**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image18.png)

1.  Wiederholen Sie den Vorgang für die zweite Kombination aus Initiator und Ziel.

    - Lokaler Adapter: Microsoft iSCSI-Initiator

    - Initiator-IP: 10.10.11.4

    - Zielportal-IP: \<Ihre zweite iSCSI-Ziel-IP/3260>

    ![](./media/azure-stack-network-howto-extend-datacenter/image19.png)

1.  Wählen Sie die Registerkarte **Volumes und Geräte** und anschließend **Automatisch konfigurieren** aus. Nun sollte ein Multipfad-E/A-Volume angezeigt werden:

    ![](./media/azure-stack-network-howto-extend-datacenter/image20.png)

2.  Wählen Sie auf der Registerkarte **Ziele** die Option **Geräte** aus, und es sollten zwei Verbindungen mit der zuvor erstellten einzelnen iSCSI-VHD angezeigt werden.

    ![](./media/azure-stack-network-howto-extend-datacenter/image20.png)

3.  Wählen Sie die Schaltfläche **Multipfad-E/A** aus, um weitere Informationen zur Richtlinie für den Lastenausgleich und zu den Pfaden anzuzeigen.

    ![](./media/azure-stack-network-howto-extend-datacenter/image21.png)

4.  Wählen Sie dreimal **OK** aus, um die Fenster und den iSCSI-Initiator zu schließen.

5.  Öffnen Sie die Datenträgerverwaltung (diskmgmt.msc), und das Popup **Datenträger initialisieren** sollte angezeigt werden.

    ![](./media/azure-stack-network-howto-extend-datacenter/image22.png)

6.  Wählen Sie **OK** aus, um die Standardeinstellungen zu übernehmen. Scrollen Sie dann nach unten zu dem neuen Datenträger, klicken Sie mit der rechten Maustaste, und wählen Sie **Neues einfaches Volume** aus.

7.  Navigieren Sie unter Verwendung der angegebenen Standardwerte durch den Assistenten. Ändern Sie die Volumebezeichnung in **iSCSIdisk1**, und wählen Sie dann **Fertig stellen** aus.

    ![](./media/azure-stack-network-howto-extend-datacenter/image23.png)

8.  Das Laufwerk sollte formatiert und mit einem Laufwerkbuchstaben versehen werden.

9.  Öffnen Sie den **Datei-Explorer** > **Dieser PC**, um das an VM001 angefügte neue Laufwerk anzuzeigen.

### <a name="test-external-storage-connectivity"></a>Testen der Konnektivität des externen Speichers

Melden Sie sich zum Überprüfen der Kommunikation und Ausführen eines grundlegenden Dateikopiertests im **Azure Stack**-System beim **Benutzerportal** an, und navigieren Sie zum Blatt **Übersicht** für **VM001**.

1. Wählen Sie **Verbinden** aus, um eine RDP-Verbindung mit **VM001** herzustellen.

2. Öffnen Sie **Task-Manager**, und wählen Sie die Registerkarte **Leistung** aus. Docken Sie das Fenster an der rechten Seite der RDP-Sitzung an.

3. Öffnen Sie **Windows PowerShell ISE** als Administrator, und docken Sie das Fenster an der linken Seite der RDP-Sitzung an. Schließen Sie auf der rechten Seite der ISE den Bereich **Befehle**, und wählen Sie die Schaltfläche **Skript** aus, um den weißen Skriptbereich oben im ISE-Fenster zu öffnen.

4. Auf dieser VM sind keine nativen PowerShell-Module zum Erstellen einer VHD vorhanden, die als große Datei zum Testen der Dateiübertragung auf das iSCSI-Ziel verwendet wird. In diesem Fall führen Sie DiskPart zum Erstellen einer VHD-Datei aus. Führen Sie in der ISE Folgendes aus:

    1. `Start-Process Diskpart`

    2. Eine neue Eingabeaufforderung wird geöffnet. Geben Sie den folgenden Befehl ein:<br>`Create vdisk file="c:\\test.vhd" type=fixed maximum=5120`

    ![](./media/azure-stack-network-howto-extend-datacenter/image24.png)

    Die Erstellung kann einen Moment dauern. Öffnen Sie anschließend zur Überprüfung der Erstellung den **Datei-Explorer**, und navigieren Sie zu C:\\. Die neue Datei „test.vhd“ mit einer Größe von 5 GB sollte angezeigt werden.

    ![](./media/azure-stack-network-howto-extend-datacenter/image25.png)

    Schließen Sie die Eingabeaufforderung. Kehren Sie zurück zur ISE, und geben Sie dort den folgenden Befehl ein. Ersetzen Sie F:\\ durch den zuvor angewendeten Laufwerkbuchstabe des iSCSI-Ziels.

    1. `Copy-Item "C:\\test.vhd" -Destination "F:\\"`

    2. Wählen Sie die Zeile in der ISE aus. Drücken Sie **F8** auf Ihrer Tastatur.

    3. Beobachten Sie während der Befehlsausführung die zwei Netzwerkadapter. Sie sehen, dass die Daten auf beide Netzwerkadapter auf VM001 übertragen werden. Darüber hinaus sollte Ihnen auffallen, dass die Last gleichmäßig auf beide Netzwerkadapter verteilt wird.

        ![](./media/azure-stack-network-howto-extend-datacenter/image26.png)

Mit diesem Szenario soll die Konnektivität zwischen einer in Azure Stack ausgeführten Workload und einem externen Speicherarray (in diesem Fall ein Windows Server-basiertes iSCSI-Ziel) veranschaulicht werden. Es ist weder als Leistungstest ausgelegt noch spiegelt es die Schritte wider, die Sie bei Verwendung einer alternativen iSCSI-basierten Appliance ausführen müssen. Es werden jedoch einige der wichtigsten Aspekte behandelt, die beim Bereitstellen von Workloads in Azure Stack und beim Verbinden dieser Workloads mit Speichersystemen außerhalb der Azure Stack-Umgebung berücksichtigt werden müssen.

## <a name="next-steps"></a>Nächste Schritte

[Azure Stack-Netzwerke: Unterschiede und Überlegungen](azure-stack-network-differences.md)  