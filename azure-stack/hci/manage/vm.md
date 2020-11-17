---
title: Verwalten virtueller Computer mit Windows Admin Center
description: Hier erfahren Sie, wie Sie virtuelle Computer (Virtual Machines, VMs) in einem Cluster in Azure Stack HCI mit Windows Admin Center erstellen und verwalten.
author: v-dasis
ms.topic: how-to
ms.date: 11/06/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 73d705bf5b36509b3aed31afb09105f2da91862f
ms.sourcegitcommit: 08ef9545316798c9a21c2f9bc1da8c15cb648982
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360268"
---
# <a name="manage-vms-with-windows-admin-center"></a>Verwalten virtueller Computer mit Windows Admin Center

> Gilt für die Azure Stack HCI, Version 20H2; Windows Server 2019

Mit Windows Admin Center können Sie virtuelle Computer (Virtual Machines, VMs) in Azure Stack HCI erstellen und verwalten.

## <a name="create-a-new-vm"></a>Erstellen eines neuen virtuellen Computers

Mit Windows Admin Center können Sie problemlos einen neuen virtuellen Computer (VM) erstellen.

:::image type="content" source="media/manage-vm/new-vm.png" alt-text="Bildschirm „Neue VM“" lightbox="media/manage-vm/new-vm.png":::

1. Wählen Sie auf der Startseite von Windows Admin Center unter **Alle Verbindungen** den Server oder Cluster aus, auf oder in dem Sie die VM erstellen möchten.
1. Scrollen Sie unter **Tools** nach unten, und wählen Sie **Virtuelle Computer** aus.
1. Wählen Sie unter **Virtuelle Computer** die Registerkarte **Inventar** aus, und wählen Sie anschließend **Hinzufügen** und **Neu** aus.
1. Geben Sie in das Feld **Neuer virtueller Computer** einen Namen für Ihre VM ein.
1. Wählen Sie **Generation 2 (empfohlen)** aus.
1. Wählen Sie unter **Host** den Server aus, auf dem sich die VM befinden soll.
1. Wählen Sie unter **Pfad** in der Dropdownliste einen vorab zugewiesenen Dateipfad aus, oder klicken Sie auf **Durchsuchen**, um den Ordner auszuwählen, in dem die VM-Konfigurationsdatei und die VHD-Dateien gespeichert werden sollen. Sie können zu einer beliebigen SMB-Freigabe im Netzwerk navigieren, indem Sie als Pfad *\\server\share* eingeben.

1. Wählen Sie unter **Virtuelle Prozessoren** die Anzahl der virtuellen Prozessoren aus, und geben Sie an, ob geschachtelte Virtualisierung für die VM aktiviert werden soll.
1. Wählen Sie unter **Arbeitsspeicher** die Menge des Arbeitsspeichers beim Start aus (4 GB wird als Minimum empfohlen), sowie einen Minimum- und Maximalbereich von dynamischem Arbeitsspeicher, welcher der VM zugeordnet werden soll.
1. Wählen Sie in der Dropdownliste unter **Netzwerk** einen Netzwerkadapter aus.
1. Klicken Sie unter **Speicher** auf **Hinzufügen**, und wählen Sie aus, ob eine neue leere virtuelle Festplatte erstellt oder eine vorhandene virtuelle Festplatte verwendet werden soll. Wenn Sie eine vorhandene virtuelle Festplatte verwenden, klicken Sie auf **Durchsuchen**, und wählen Sie den entsprechenden Dateipfad aus.  
1. Führen Sie unter **Betriebssystem** einen der folgenden Schritte aus:
   - Aktivieren Sie **Betriebssystem zu einem späteren Zeitpunkt installieren**, wenn Sie später ein Betriebssystem für die VM installieren möchten, nachdem die VM erstellt wurde.
   - Wählen Sie **Install an operating system from an image file (*.iso)** (Betriebssystem von Imagedatei (*.iso) installieren) aus, klicken Sie auf **Durchsuchen**, und wählen Sie dann die gewünschte ISO-Imagedatei aus.
1. Wenn Sie fertig sind, klicken Sie auf **Erstellen**, um die VM zu erstellen.
1. Zeigen Sie zum Starten der VM in der Liste **Virtuelle Computer** mit dem Mauszeiger auf die neue VM, aktivieren Sie links das entsprechende Kontrollkästchen, und wählen Sie **Starten** aus.
1. Vergewissern Sie sich unter **Status**, ob der VM-Status **Wird ausgeführt** ist.

## <a name="get-a-list-of-vms"></a>Abrufen einer Liste von VMs

Sie können auf einfache Weise alle VMs auf einem Server in Ihrem Cluster anzeigen.

:::image type="content" source="media/manage-vm/vm-inventory.png" alt-text="Bildschirm „Virtuelle Computer“" lightbox="media/manage-vm/vm-inventory.png":::

1. Führen Sie in Windows Admin Center unter **Tools** einen Bildlauf nach unten aus, und wählen Sie **Virtuelle Computer** aus.
1. Auf der Registerkarte **Inventar** rechts werden alle auf dem aktuellen Server oder im Cluster verfügbaren VMs aufgelistet; hier finden sich auch Befehle zum Verwalten einzelner VMs. Ihre Möglichkeiten:
    - Zeigen Sie eine Liste der VMs an, die auf dem aktuellen Server oder im Cluster ausgeführt werden.
    - Zeigen Sie den Zustand und den Hostserver der VM an, wenn Sie VMs für einen Cluster untersuchen. Sie können auch die CPU- und Arbeitsspeichernutzung aus Hostperspektive überprüfen, einschließlich von Arbeitsspeicherauslastung, Arbeitsspeicherbedarf und zugewiesenem Arbeitsspeicher sowie von Betriebszeit, Heartbeat-Status und Schutzstatus der VM (mit Azure Site Recovery).
    - Erstellen Sie einen neuen virtuellen Computer.
    - Sie können eine VM löschen, starten, ausschalten, herunterfahren, anhalten, fortsetzen, zurücksetzen oder umbenennen. Außerdem können Sie die VM speichern, einen gespeicherten Zustand löschen oder einen Prüfpunkt erstellen.
    - Ändern Sie die Einstellungen für eine VM.
    - Stellen Sie über den Hyper-V-Host eine Verbindung mit einer VM-Konsole her.
    - Replizieren Sie eine VM mithilfe von Azure Site Recovery.
    - Bei Vorgängen, die für mehrere VMs gleichzeitig ausgeführt werden können (z. B. „Starten“, „Herunterfahren“, „Speichern“, „Anhalten“, „Löschen“ oder „Zurücksetzen“), können Sie mehrere VMs auswählen und den Vorgang einmal ausführen.

## <a name="view-vm-details"></a>Anzeigen von VM-Details

Sie können ausführliche Informationen und Leistungsdiagramme für eine bestimmte VM auf ihrer dedizierten Seite einsehen.

:::image type="content" source="media/manage-vm/vm-details.png" alt-text="Bildschirm mit detaillierten Informationen für VMs" lightbox="media/manage-vm/vm-details.png":::

1. Scrollen Sie unter **Tools** nach unten, und wählen Sie **Virtuelle Computer** aus.
1. Klicken Sie rechts auf die Registerkarte **Inventar**, und wählen Sie anschließend die VM aus. Auf der nächsten Seite können Sie folgende Aufgaben ausführen:

   - Sie können Liniendiagramme mit Live- und Verlaufsdaten für CPU, Arbeitsspeicher, Netzwerk, IOPS und E/A-Durchsatz (Verlaufsdaten sind nur für hyperkonvergente Cluster verfügbar) anzeigen.
   - Sie können Prüfpunkte anzeigen, erstellen, anwenden, umbenennen und löschen.
   - Sie können Details für die VHD-Dateien (Virtual Hard Disk, virtuelle Festplatte), Netzwerkadapter und den Hostserver anzeigen.
   - Sie können den Zustand der VM anzeigen.
   - Sie können die VM speichern, einen gespeicherten Zustand löschen oder die VM exportieren oder klonen.
   - Sie können die Einstellungen für die VM ändern.
   - Sie können mithilfe von VMConnect über den Hyper-V-Host eine Verbindung mit der VM-Konsole herstellen.
   - Replizieren Sie die VM mithilfe von Azure Site Recovery.

## <a name="view-aggregate-vm-metrics"></a>Anzeigen aggregierter VM-Metriken

Sie können Ressourcennutzung und Leistungsmetriken für alle VMs in Ihrem Cluster anzeigen.

:::image type="content" source="media/manage-vm/host-metrics.png" alt-text="Bildschirm mit Hostmetriken" lightbox="media/manage-vm/host-metrics.png":::

1. Scrollen Sie unter **Tools** nach unten, und wählen Sie **Virtuelle Computer** aus.
1. Auf der Registerkarte **Zusammenfassung** rechts findet sich eine holistische Ansicht der Hyper-V-Hostressourcen und -leistung für einen ausgewählten Server oder Cluster. Hierzu zählen folgende Angaben:
    - Anzahl der ausgeführten, beendeten, angehaltenen und gespeicherten VMs
    - Aktuelle Integritätswarnungen oder Ereignisse aus Hyper-V-Ereignisprotokollen für Cluster
    - CPU- und Speicherauslastung mit Aufschlüsselung nach Host und Gast
    - Liniendiagramme mit Live- und Verlaufsdaten für IOPS und E/A-Durchsatz für Cluster

## <a name="change-vm-settings"></a>Ändern von VM-Einstellungen

Es gibt eine Vielzahl von Einstellungen, die Sie für eine VM ändern können.

> [!NOTE]
> Einige Einstellungen können für eine ausgeführte VM nicht geändert werden, und Sie müssen die VM zuerst beenden.

1. Scrollen Sie unter **Tools** nach unten, und wählen Sie **Virtuelle Computer** aus.
1. Klicken Sie rechts auf die Registerkarte **Inventar**, wählen Sie die VM aus, und klicken Sie dann auf **Einstellungen**.

1. Wählen Sie zum Ändern der Aktionen zum Starten/Beenden von VMs **Allgemein** aus, und führen Sie folgende Schritte aus:
    - Geben Sie zum Ändern des VM-Namens den Namen im Feld **Name** ein.
    - Wählen Sie die entsprechenden Einstellungen in den Dropdownfeldern aus, um die Standardaktionen zum Starten/Beenden von VMs zu ändern.
    - Wenn Sie Zeitintervalle für das Anhalten oder Starten einer VM ändern möchten, geben Sie die entsprechenden Werte in die angezeigten Felder ein.

        :::image type="content" source="media/manage-vm/vm-settings-general.png" alt-text="Bildschirm mit allgemeinen Einstellungen für VMs" lightbox="media/manage-vm/vm-settings-general.png":::

1. Wählen Sie **Arbeitsspeicher** aus, um den Arbeitsspeicher beim Starten von VMs, den Bereich des dynamischen Arbeitsspeichers, den Prozentsatz des Arbeitsspeicherpuffers sowie die Arbeitsspeichergewichtung zu ändern.

    :::image type="content" source="media/manage-vm/vm-settings-memory.png" alt-text="Bildschirm zum Ändern von Arbeitsspeichereinstellungen für VMs" lightbox="media/manage-vm/vm-settings-memory.png":::

1. Wählen Sie **Prozessoren** aus, um die Anzahl der virtuellen Prozessoren zu ändern, um die geschachtelte Virtualisierung zu aktivieren oder um das simultane Multithreading (SMT) zu aktivieren.

    :::image type="content" source="media/manage-vm/vm-settings-processor.png" alt-text="Bildschirm zum Ändern von Prozessoreinstellungen für VMs" lightbox="media/manage-vm/vm-settings-processor.png":::

1. Um die Größe eines vorhandenen Datenträgers zu ändern, ändern Sie den Wert in **Größe (GB)** . Wenn Sie einen neuen virtuellen Datenträger hinzufügen möchten, wählen Sie **Datenträger** aus. Wählen Sie anschließend aus, ob Sie einen leeren virtuellen Datenträger erstellen oder eine vorhandene virtuelle Festplatte oder ISO-Imagedatei verwenden möchten. Klicken Sie auf **Durchsuchen**, und wählen Sie den Pfad zum virtuellen Datenträger oder zur Imagedatei aus.

    :::image type="content" source="media/manage-vm/vm-settings-disk.png" alt-text="Bildschirm zum Ändern von Datenträgereinstellungen für VMs" lightbox="media/manage-vm/vm-settings-disk.png":::

1. Um Einstellungen für Netzwerkadapter hinzuzufügen, zu entfernen oder zu ändern, wählen Sie **Netzwerke** aus, und führen Sie die folgenden Schritte aus:
    - Geben Sie den zu verwendenden virtuellen Switch an, und geben Sie an, ob die Erkennung virtueller LANs aktiviert werden soll (Sie müssen auch den VLAN-Bezeichner angeben).
    - Klicken Sie auf **Erweitert**, um weitere Einstellungen für Netzwerkadapter zu ändern. Folgende Aktionen sind möglich:
        - Auswählen des dynamischen oder statischen MAC-Adresstyps
        - Aktivieren des Spoofings von MAC-Adressen
        - Aktivieren der Bandbreitenverwaltung und Angeben des Maximal-/Minimalbereichs

        :::image type="content" source="media/manage-vm/vm-settings-network.png" alt-text="Bildschirm zum Ändern von Netzwerkeinstellungen für VMs" lightbox="media/manage-vm/vm-settings-network.png":::

1. Wählen Sie **Startreihenfolge** aus, um Startgeräte hinzuzufügen oder um die VM-Startsequenz zu ändern.

    :::image type="content" source="media/manage-vm/vm-settings-boot.png" alt-text="Bildschirm zum Ändern der VM-Startreihenfolge" lightbox="media/manage-vm/vm-settings-boot.png":::

1. Wählen Sie **Prüfpunkte** aus, um VM-Prüfpunkte zu aktivieren, wählen Sie den Prüfpunkttyp aus, und geben den Speicherort der Prüfpunktdatei an.

    > [!NOTE]
    > Die Prüfpunkteinstellung **Produktion** wird empfohlen. Bei dieser Einstellung wird Sicherungstechnologie im Gastbetriebssystem verwendet, um datenkonsistente Prüfpunkte zu erstellen. Bei der Einstellung **Standard** werden VHD-Momentaufnahmen verwendet, um Prüfpunkte mit dem Anwendungs- und Dienststatus zu erstellen.

     :::image type="content" source="media/manage-vm/vm-settings-checkpoint.png" alt-text="Bildschirm zum Ändern von VM-Prüfpunkten" lightbox="media/manage-vm/vm-settings-checkpoint.png":::

1. Wählen Sie **Affinitätsregeln** aus, um eine Affinitätsregel für eine VM zu erstellen. Weitere Informationen zum Erstellen von Affinitätsregeln finden Sie unter [Erstellen von Server- und Standortaffinitätsregeln für VMs](vm-affinity.md).

    :::image type="content" source="media/manage-vm/vm-affinity.png" alt-text="Anzeige für Affinitätsregeln" lightbox="media/manage-vm/vm-affinity.png":::

1. Wählen Sie zum Ändern der VM-Sicherheitseinstellungen **Sicherheit** aus, und führen Sie folgende Schritte aus:
    - Wählen Sie **Sicheren Start aktivieren** aus, um zu verhindern, dass nicht autorisierter Code beim Starten ausgeführt wird (empfohlen). Wählen Sie außerdem im Dropdownfeld eine Microsoft-Vorlage oder eine Open-Source-Vorlage aus.
    - Wählen Sie für **Vorlage** die zu verwendende Sicherheitsvorlage aus.

    - Unter **Verschlüsselungsunterstützung** können Sie Folgendes ausführen:

        - Wählen Sie **Trusted Platform Module aktivieren** aus, um ein Hardware-Kryptografiedienstmodul verwenden zu können.

        - Sie können die Verschlüsselung von Status- und VM-Migrationsdatenverkehr aktivieren.

        > [!NOTE]
        > Für die Unterstützung von Verschlüsselung benötigt die VM eine Schlüsselschutzvorrichtung (KP). Sofern noch nicht vorhanden, wird bei Auswahl einer dieser Optionen eine KP generiert, die das Ausführen der VM auf diesem Host zulässt.

    - Wählen Sie unter **Sicherheitsrichtlinie** die Option **Enable Shielding** (Schutz aktivieren) aus, um auf weitere Schutzoptionen für die VM zuzugreifen.

        :::image type="content" source="media/manage-vm/vm-settings-security.png" alt-text="Ändern von VM-Sicherheitseinstellungen" lightbox="media/manage-vm/vm-settings-security.png":::

## <a name="move-a-vm-to-another-server-or-cluster"></a>Verschieben einer VM auf einen anderen Server oder in einen anderen Cluster

Sie können einen virtuellen Computer wie folgt auf einfache Weise auf einen anderen Server oder in einen anderen Cluster verschieben:

1. Scrollen Sie unter **Tools** nach unten, und wählen Sie **Virtuelle Computer** aus.
1. Wählen Sie auf der Registerkarte **Inventar** einen virtuellen Computer in der Liste aus, und wählen Sie **Verwalten > Verschieben** aus.
1. Wählen Sie einen Server in der Liste aus, und klicken Sie auf **Verschieben**.
1. Wenn Sie sowohl die VM als auch deren Speicher verschieben möchten, wählen Sie aus, ob die VM in einen anderen Cluster oder auf einen anderen Server im selben Cluster verschoben werden soll.

    :::image type="content" source="media/manage-vm/vm-more-move.png" alt-text="Bildschirm „VM verschieben“" lightbox="media/manage-vm/vm-more-move.png":::

1. Wenn Sie nur den Speicher der VM verschieben möchten, wählen Sie entweder aus, ihn in denselben Pfad zu verschieben, oder wählen Sie unterschiedliche Pfade für Konfiguration, Prüfpunkte oder Smart Paging aus.

    :::image type="content" source="media/manage-vm/vm-move-storage.png" alt-text="Dialogfeld für Verschieben von VM-Speicher" lightbox="media/manage-vm/vm-move-storage.png":::

## <a name="join-a-vm-to-a-domain"></a>Hinzufügen einer VM zu einer Domäne

Sie können eine VM wie folgt problemlos einer Domäne hinzufügen:

:::image type="content" source="media/manage-vm/vm-domain-join.png" alt-text="Dialogfeld für Hinzufügen einer VM zu einer Domäne" lightbox="media/manage-vm/vm-domain-join.png":::

1. Scrollen Sie unter **Tools** nach unten, und wählen Sie **Virtuelle Computer** aus.
1. Wählen Sie auf der Registerkarte **Inventar** einen virtuellen Computer in der Liste aus, und wählen Sie **Verwalten > Domänenbeitritt** aus.
1. Geben Sie den Namen der Domäne, zu der die VM hinzugefügt werden soll, zusammen mit dem Domänenbenutzernamen und dem Domänenkennwort ein.
1. Geben Sie den Benutzernamen und das Kennwort für die VM ein.
1. Klicken Sie abschließend auf **Beitreten**.

## <a name="clone-a-vm"></a>Klonen einer VM

Sie können eine VM wie folgt problemlos klonen:

1. Scrollen Sie unter **Tools** nach unten, und wählen Sie **Virtuelle Computer** aus.
1. Klicken Sie rechts auf die Registerkarte **Inventar**. Wählen Sie eine VM in der Liste aus, und wählen Sie **Verwalten > Klonen** aus.
1. Geben Sie den Namen und den Pfad für die geklonte VM an.
1. Führen Sie „Sysprep“ auf Ihrer VM aus, wenn Sie dies noch nicht getan haben.

:::image type="content" source="media/manage-vm/vm-clone.png" alt-text="Dialogfeld für Klonen einer VM" lightbox="media/manage-vm/vm-clone.png":::

## <a name="import-or-export-a-vm"></a>Importieren oder Exportieren einer VM

Sie können eine VM auf einfache Weise importieren oder exportieren. In der folgenden Prozedur wird der Importvorgang beschrieben.

:::image type="content" source="media/manage-vm/vm-more-import.png" alt-text="Bildschirm zum Importieren von VMs" lightbox="media/manage-vm/vm-more-import.png":::

1. Scrollen Sie unter **Tools** nach unten, und wählen Sie **Virtuelle Computer** aus.
1. Wählen Sie auf der Registerkarte **Inventar** die Option **Hinzufügen > Importieren** aus.
1. Geben Sie den Namen des Ordners ein, der die VM enthält, oder klicken Sie auf **Durchsuchen**, und wählen Sie einen Ordner aus.
1. Wählen Sie die zu importierende VM aus.
1. Erstellen Sie ggf. eine eindeutige ID für die VM.
1. Klicken Sie abschließend auf **Importieren**.

Die Vorgehensweise für ein Exportieren einer VM ist ähnlich:

1. Scrollen Sie unter **Tools** nach unten, und wählen Sie **Virtuelle Computer** aus.
1. Wählen Sie auf der Registerkarte **Inventar** in der Liste die zu exportierende VM aus.
1. Wählen Sie **Verwalten > Exportieren** aus.
1. Geben Sie den Pfad des Ordners ein, in den die VM exportiert werden soll.

:::image type="content" source="media/manage-vm/vm-export.png" alt-text="Dialogfeld für Exportieren einer VM" lightbox="media/manage-vm/vm-export.png":::

## <a name="view-vm-event-logs"></a>Anzeigen von VM-Ereignisprotokollen

Sie können VM-Ereignisprotokolle wie folgt anzeigen:

1. Scrollen Sie unter **Tools** nach unten, und wählen Sie **Virtuelle Computer** aus.
1. Wählen Sie auf der Registerkarte **Zusammenfassung** rechts die Option **Alle Ereignisse anzeigen** aus.
1. Wählen Sie eine Ereigniskategorie aus, und erweitern Sie die Ansicht.

## <a name="connect-to-a-vm-by-using-remote-desktop"></a>Herstellen einer Verbindung mit einer VM mithilfe von Remotedesktop (RDP)

Anstatt mit Windows Admin Center können Sie Ihre VMs auch über einen Hyper-V-Host mit einer RDP-Verbindung (Remote Desktop Protocol) verwalten.

1. Scrollen Sie unter **Tools** nach unten, und wählen Sie **Virtuelle Computer** aus.
1. Wählen Sie auf der Registerkarte **Inventar** in der Liste die Option „Virtuellen Computer auswählen“ aus, und wählen Sie die Option **Verbinden > Verbinden** oder **Verbinden > RDP-Datei herunterladen** aus. Bei beiden Optionen wird zum Herstellen einer Verbindung mit der Gast-VM über den Hyper-V-Host das VMConnect-Tool verwendet, und Sie müssen Benutzername und Kennwort des Administrators für den Hyper-V-Host eingeben.

    - Mit der Option **Verbinden** wird die VM mithilfe von Remotedesktop in Ihrem Webbrowser verbunden.

    - Mit der Option **RDP-Datei herunterladen** wird eine RDP-Datei heruntergeladen, die Sie öffnen können, um eine Verbindung mit der Remotedesktopverbindungs-App („mstsc.exe“) herzustellen.

## <a name="protect-vms-with-azure-site-recovery"></a>Schützen von VMs mit Azure Site Recovery

Sie können Windows Admin Center verwenden, um Azure Site Recovery zu konfigurieren und Ihre lokalen VMs in Azure zu replizieren. Dies ist ein optionaler Dienst zum Hinzufügen von Werten. Informationen zu den ersten Schritten finden Sie unter [Schützen von VMs mit Azure Site Recovery](azure-site-recovery.md).

:::image type="content" source="media/manage-vm/vm-more-azure.png" alt-text="Bildschirm zum Einrichten von Azure Site Recovery" lightbox="media/manage-vm/vm-more-azure.png":::

## <a name="next-steps"></a>Nächste Schritte

Sie können VMs auch mit der Windows PowerShell erstellen und verwalten. Weitere Informationen hierzu finden Sie unter [Verwalten von VMs in der Azure Stack HCI mithilfe der Windows PowerShell](vm-powershell.md).