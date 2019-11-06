---
title: Erweitern des Rechenzentrums in Azure Stack | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie das Rechenzentrum in Azure Stack erweitern.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 10/19/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/19/2019
ms.openlocfilehash: 94a9398a68be06d4735e2c082e8dc0a02281b6eb
ms.sourcegitcommit: 58e1911a54ba249a82fa048c7798dadedb95462b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73064967"
---
# <a name="how-to-extend-the-data-center-on-azure-stack"></a>Erweitern des Rechenzentrums in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Dieser Artikel enthält Informationen zur Speicherinfrastruktur von Azure Stack, die Sie bei der Entscheidung unterstützen, wie Sie Azure Stack in Ihre bestehende Netzwerkumgebung integrieren können. Der Speicherplatz auf Azure Stack ist beschränkt. Sie können Speicher mit Speicher außerhalb ihrer Azure Stack-Instanz und in Ihr lokales Rechenzentrum integrieren. Nach allgemeinen Darlegungen zum Erweitern Ihres Rechenzentrums werden im Artikel zwei unterschiedliche Szenarien vorgestellt. Sie können eine Verbindung mit einem Windows File Storage-Server herstellen. Sie können auch eine Verbindung mit einem Windows iSCSI-Server herstellen.

## <a name="overview-of-extending-storage-to-azure-stack"></a>Übersicht über das Erweitern von Speicher auf Azure Stack

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

13. Wählen Sie unter „DNS-Name“ die Option **Konfigurieren** aus, geben Sie die DNS-Namensbezeichnung **vm001** ein, und wählen Sie **Speichern** aus. Wählen Sie anschließend **VM001** im Breadcrumb aus, um zum Blatt *Übersicht* zurückzukehren.

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

4)  Erweitern Sie **Datei- und Speicherdienste** und **Datei- und iSCSI-Dienste**, und aktivieren Sie das Kontrollkästchen **Dateiserver**. Schließen Sie anschließend den **Server-Manager**.

5)  Öffnen Sie den **Server-Manager** erneut, und wählen Sie die Option **Datei- und Speicherdienste** aus.

6)  Wählen Sie **Freigaben** aus.

7)  Wählen Sie im Feld **Freigaben** den Link **Starten Sie den Assistenten für neue Freigaben, um die Dateifreigabe zu erstellen** aus.

8)  Wählen Sie im Feld **Assistent für neue Freigaben** die Option **SMB-Freigabe – Schnell** aus. Wählen Sie anschließend **Weiter**, und navigieren Sie durch die Bildschirme des Assistenten. Wählen Sie das **Volume C:\\\\**  aus.

9)  Benennen Sie die Freigabe **TestStorage**, und wählen Sie **Weiter** aus.

10) Wählen Sie im **Assistenten für neue Freigaben** **Weiter** und anschließend **Erstellen** und **Schließen** aus.

Sie haben nun die Dateifreigabe auf Ihrem Dateiserver erstellt.

### <a name="testing-file-storage-performance-and-connectivity"></a>Testen der von Speicherleistung und -konnektivität

Melden Sie sich zum Prüfen der Kommunikation und Ausführen einiger grundlegender Tests wieder beim Azure Stack-Benutzerportal im **Azure Stack**-System an, und navigieren Sie zum Blatt **Übersicht** für **VM001**.

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

## <a name="next-steps"></a>Nächste Schritte

[Azure Stack-Netzwerke: Unterschiede und Überlegungen](azure-stack-network-differences.md)  