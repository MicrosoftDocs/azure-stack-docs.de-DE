---
title: Schützen von Azure Stack HCI-VMs mithilfe von Azure Site Recovery
description: Verwenden von Windows Admin Center, um Azure Stack HCI-VMs mit Azure Site Recovery zu schützen.
ms.topic: how-to
author: davannaw-msft
ms.author: dawhite
ms.date: 04/30/2020
ms.localizationpriority: low
ms.openlocfilehash: 0465666549e0ae8801c9bab0be6d8a3d6dad8891
ms.sourcegitcommit: 76af742a42e807c400474a337e29d088ede8a60d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/22/2020
ms.locfileid: "85196459"
---
# <a name="protect-azure-stack-hci-vms-using-azure-site-recovery"></a>Schützen von Azure Stack HCI-VMs mithilfe von Azure Site Recovery

>Gilt für: Windows Server 2019

Windows Admin Center optimiert den Prozess der Replikation virtueller Computer (VMs) auf Ihren Servern oder Clustern und bietet Ihnen die Möglichkeit, Azure aus Ihrem eigenen Rechenzentrum zu nutzen. Zum Automatisieren des Setups verbinden Sie Windows Admin Center mit Azure.

In diesem Artikel erfahren Sie, wie Sie mit Azure Site Recovery Replikationseinstellungen konfigurieren und einen Wiederherstellungsplan im Azure-Portal erstellen. Durch Ausführen dieser Aufgaben kann Windows Admin Center die VM-Replikation starten, um ihre VMs zu schützen. Azure Site Recovery schützt auch Ihre physischen Server und Clusterknoten.

Weitere Informationen finden Sie unter [Verbinden von Windows Server mit Azure-Hybriddiensten](/windows-server/manage/windows-admin-center/azure/).

## <a name="how-azure-site-recovery-works-with-windows-admin-center"></a>Funktionsweise von Azure Site Recovery mit Windows Admin Center
*Azure Site Recovery* ist ein Azure-Dienst, der Workloads auf VMs repliziert, damit Ihre unternehmenskritische Infrastruktur vor einem Notfall geschützt wird. Weitere Informationen finden Sie unter [Informationen zu Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview).

Azure Site Recovery besteht aus zwei Komponenten: *Replikation* und *Failover*. Der Replikationsteil schützt Ihre VMs vor einem Notfall, indem die VHD der Ziel-VM in ein Azure-Speicherkonto repliziert wird. Anschließend können Sie für die VMs ein Failover durchführen und sie im Falle eines Notfalls in Azure ausführen. Sie können auch ein Testfailover durchführen, ohne dass Ihre primären VMs beeinträchtigt werden, um den Wiederherstellungsprozess in Azure zu testen.

Das Abschließen des Setups für die Replikationskomponente ist allein ausreichend, um ihre VMs vor einem Notfall zu schützen. Allerdings können Sie die VMs in Azure erst starten, wenn Sie den Failoverteil des Prozesses konfiguriert haben.

Sie können den Failoverteil einrichten, wenn Sie ein Failover auf eine Azure-VM ausführen möchten. Dies ist während der ersten Einrichtung nicht erforderlich. Wenn der Hostserver ausfällt, können Sie die Failoverkomponente zu diesem Zeitpunkt konfigurieren und auf die Workloads der geschützten VM zugreifen. Es wird jedoch empfohlen, die failoverbezogenen Einstellungen vor einem Notfall zu konfigurieren.

## <a name="prerequisites-and-planning"></a>Voraussetzungen und Planung
Zum Ausführen der Schritte in diesem Artikel ist Folgendes erforderlich:

- Die Zielserver, auf denen die VMs gehostet werden, die Sie schützen möchten, müssen über Internetzugriff verfügen, um die Replikation in Azure auszuführen.
- Eine Verbindung von Windows Admin Center mit Azure. Weitere Informationen finden Sie unter [Konfigurieren der Azure-Integration](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/azure-integration).
- Überprüfen Sie das Kapazitätsplanungstool, um die Anforderungen für eine erfolgreiche Replikation und ein Failover auszuwerten. Weitere Informationen finden Sie unter [Informationen zum Azure Site Recovery-Bereitstellungsplaner für die Hyper-V-Notfallwiederherstellung in Azure](https://docs.microsoft.com/azure/site-recovery/hyper-v-site-walkthrough-capacity).

## <a name="step-1-set-up-vm-protection-on-your-target-host"></a>Schritt 1: Einrichten des VM-Schutzes auf dem Zielhost
Führen Sie die folgenden Schritte pro Hostserver oder Cluster mit den VMs aus, die Sie schützen möchten:
1. Stellen Sie in Windows Admin Center auf der Seite **Alle Verbindungen** eine Verbindung mit dem Server oder Cluster her, der die VMs hostet, die Sie schützen möchten.
1. Wählen Sie unter **Extras** die Option **Virtuelle Computer**aus, und wählen Sie dann die Registerkarte **Bestand** aus.
1. Wählen Sie eine beliebige VM aus (es muss sich nicht um die VM handeln, die Sie schützen möchten).
1. Erweitern Sie das Untermenü **Weitere**, und wählen Sie dann **VM-Schutz einrichten** aus.

    :::image type="content" source="media/azure-site-recovery/set-up-vm-protection.png" alt-text="Das Untermenü „VM-Schutz einrichten“ in Windows Admin Center.":::

1. Melden Sie sich bei Ihrem Azure-Konto an.
1. Geben Sie auf der Seite „Host mit Azure Site Recovery einrichten“ die erforderlichen Informationen ein, und wählen Sie dann **Einrichten** aus:

   - **Abonnement:** Das Azure-Abonnement, das Sie für die VM-Replikation auf diesem Host verwenden möchten.
   - **Ressourcengruppe:** Ein neuer Ressourcengruppenname.
   - **Recovery Services-Tresor**: Ein Name für den Azure Site Recovery-Tresor für die geschützten VMs auf diesem Host.
   - **Standort:** Die Azure-Region, in der die Azure Site Recovery-Ressourcen erstellt werden sollen.

    :::image type="content" source="media/azure-site-recovery/set-up-host-with-asr.png" alt-text="Die Seite „Host mit Azure Site Recovery einrichten“ in Windows Admin Center.":::

1. Warten Sie, bis die folgende Benachrichtigung angezeigt wird: **Site Recovery-Einstellung wurde abgeschlossen**.

Dieser Vorgang kann bis zu 10 Minuten dauern. Sie können den Fortschritt überwachen, indem Sie zu **Benachrichtigungen** wechseln (das Glockensymbol in der oberen rechten Ecke von Windows Admin Center).

>[!NOTE]
> Bei diesem Vorgang wird der Azure Site Recovery-Agent automatisch auf dem Zielserver oder den Zielknoten installiert (wenn Sie einen Cluster konfigurieren), und es wird eine **Ressourcengruppe** mit dem angegebenen **Speicherkonto** und **Tresor** im angegebenen **Speicherort** erstellt. Außerdem wird der Zielhost beim Azure Site Recovery-Dienst registriert und eine Standardreplikationsrichtlinie konfiguriert.

## <a name="step-2-select-vms-to-protect"></a>Schritt 2: Auswählen der zu schützenden VMs
Führen Sie die folgenden Schritte aus, um Ihre VMs zu schützen:
1. Kehren Sie in Windows Admin Center zu dem Server oder Cluster zurück, den Sie in der vorherigen Aufgabe konfiguriert haben.
1. Wählen Sie unter **Extras** die Option **Virtuelle Computer**aus, und wählen Sie dann die Registerkarte **Bestand** aus.
1. Wählen Sie die VM aus, die Sie schützen möchten, erweitern Sie das Untermenü **Weitere**, und wählen Sie dann **VM schützen** aus.

    :::image type="content" source="media/azure-site-recovery/protect-vm-setting.png" alt-text="Die Einstellung „VM schützen“ in Windows Admin Center.":::

1. Überprüfen Sie die Kapazitätsanforderungen für den Schutz der VM. Weitere Informationen finden Sie unter [Planen der Kapazität für die Notfallwiederherstellung von Hyper-V-VMs](https://docs.microsoft.com/azure/site-recovery/site-recovery-capacity-planner).

    Wenn Sie ein Storage Premium-Konto verwenden möchten, erstellen Sie dieses im Azure-Portal. Weitere Informationen finden Sie im Abschnitt **SSD Premium** unter [Welche Datenträgertypen sind in Azure verfügbar?](https://docs.microsoft.com/azure/storage/common/storage-premium-storage) Die Option **Neu erstellen**, die in Windows Admin Center bereitgestellt wird, erstellt ein Standardspeicherkonto.

1. Geben Sie den Namen des **Speicherkontos** ein, das für die Replikation dieser VM verwendet werden soll, und wählen Sie dann **VM schützen** aus, um die Replikation für die VM zu aktivieren.

    :::image type="content" source="media/azure-site-recovery/protect-vm-setting-asr.png" alt-text="Definieren des Speicherkontos für Azure Site Recovery, um eine VM in Windows Admin Center zu schützen.":::

    Azure Site Recovery startet den Replikationsprozess. Die VM wird geschützt, wenn der Wert in der Spalte **Geschützt** im Raster **VM-Bestand** in **Ja** geändert wird. Dieser Vorgang kann mehrere Minuten dauern.

## <a name="step-3-configure-and-run-a-test-failover-in-the-azure-portal"></a>Schritt 3: Konfigurieren und Ausführen eines Testfailovers im Azure-Portal
Vor dem Starten der VM-Replikation ist es nicht erforderlich, diesen Schritt abzuschließen. Die VM ist bereits ausschließlich mit Replikation geschützt. Es empfiehlt sich jedoch, beim Einrichten von Azure Site Recovery Failovereinstellungen zu konfigurieren.

Führen Sie die folgenden Schritte aus, um ein Failover auf eine Azure-VM vorzubereiten:
1. Richten Sie ein Azure-Netzwerk ein, das von der VM mit Failover an dieses VNET angefügt wird. Weitere Informationen finden Sie unter[Einrichten der Notfallwiederherstellung von lokalen Hyper-V-VMs in Azure](https://docs.microsoft.com/azure/site-recovery/hyper-v-site-walkthrough-prepare-azure).

    >[!NOTE]
    > Die Schritte in dieser Ressource werden von Windows Admin Center automatisch abgeschlossen. Sie müssen nur das Azure-Netzwerk einrichten.

1. Führen Sie ein Testfailover durch. Weitere Informationen finden Sie unter [Durchführen eines Notfallwiederherstellungsverfahrens in Azure](https://docs.microsoft.com/azure/site-recovery/hyper-v-site-walkthrough-test-failover).

## <a name="step-4-create-recovery-plans"></a>Schritt 4: Erstellen von Wiederherstellungsplänen
Mithilfe von Wiederherstellungsplänen in Azure Site Recovery können Sie ein Failover und die Wiederherstellung der VM-Sammlung für die gesamte Anwendung ausführen. Es ist möglich, geschützte VMs einzeln wiederherzustellen. Ein besserer Weg ist es jedoch, die VMs einer Anwendung Ihrem Wiederherstellungsplan hinzuzufügen. Anschließend können Sie ein Failover der gesamten Anwendung über den Wiederherstellungsplan durchführen. Sie können auch das Feature „Testfailover“ eines Wiederherstellungsplans verwenden, um die Wiederherstellung der Anwendung zu testen.

Mit Wiederherstellungsplänen können Sie VMs gruppieren, die Reihenfolge angeben, in der sie während eines Failovers gestartet werden sollen, und zusätzliche Wiederherstellungsschritte automatisieren. Nachdem Sie Ihre VMs geschützt haben, können Sie zum Azure Site Recovery-Tresor im Azure-Portal navigieren, um Wiederherstellungspläne für sie zu erstellen. Weitere Informationen finden Sie unter [Erstellen und Anpassen von Wiederherstellungsplänen](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans).

## <a name="step-5-monitor-replicated-vms-in-azure"></a>Schritt 5: Überwachen von replizierten VMs in Azure
Um zu überprüfen, dass bei der Serverregistrierung keine Fehler aufgetreten sind, navigieren Sie zum **Azure-Portal**, und wählen Sie **Alle Ressourcen**, **Recovery Services-Tresor**, **Aufträge** und dann **Site Recovery Aufträge** aus. Der Name des **Recovery Services-Tresors** ist der Name, den Sie in Schritt 6 der ersten Aufgabe in diesem Artikel angegeben haben.

Wechseln Sie zum Überwachen der VM-Replikation zum **Recovery Services-Tresor** und dann zu **Replizierte Elemente**.

Wenn Sie alle Server anzeigen möchten, die beim Tresor registriert sind, navigieren Sie zu **Recovery Services-Tresor**, **Site Recovery-Infrastruktur** und dann zu **Hyper-V-Hosts** (im Abschnitt Hyper-V-Standorte).

## <a name="known-issue"></a>Bekanntes Problem ##
Wenn Sie Azure Site Recovery bei einem Cluster registrieren und ein Knoten entweder Azure Site Recovery nicht installieren oder den Azure Site Recovery-Dienst nicht registrieren kann, sind die VMs möglicherweise nicht geschützt. Um zu bestätigen, dass alle Knoten im Cluster im Azure-Portal registriert sind, navigieren Sie zu **Recovery Services-Tresor**, **Aufträge** und dann zu **Site Recovery-Aufträge**.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Azure Site Recovery finden Sie auch hier:

- [Allgemeine Fragen zu Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-faq)
