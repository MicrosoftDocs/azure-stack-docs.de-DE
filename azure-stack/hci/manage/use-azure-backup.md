---
title: Verwenden von Azure Backup zum Sichern von Windows-Servern
description: Dieser Artikel stellt einen Leitfaden zur Verwendung von Azure Backup über Windows Admin Center zum Sichern von Windows-Servern dar.
author: thomasmaurer
ms.author: thmaure
ms.topic: how-to
ms.date: 07/21/2020
ms.openlocfilehash: a4668e4d78a2ea3fb9f94913863d3471313034e5
ms.sourcegitcommit: a15a0f955bac922cebb7bf90a72384fd84ddfe56
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2020
ms.locfileid: "86947502"
---
# <a name="use-azure-backup-to-back-up-windows-servers"></a>Verwenden von Azure Backup zum Sichern von Windows-Servern

>Gilt für: Azure Stack HCI, Version 20H2; Windows Server 2019

Mithilfe von Windows Admin Center lässt sich das Sichern von Windows-Servern auf Microsoft Azure optimieren. Der Prozess schützt Sie darüber hinaus vor versehentlichem oder böswilligem Löschen, Beschädigungen und sogar Ransomware. Zum Automatisieren des Setups stellen Sie eine Verbindung von Windows Admin Center mit Azure her, um den Azure Backup-Dienst zu nutzen.

In diesem Artikel erfahren Sie, wie Sie Azure Backup konfigurieren und im Windows Admin Center eine Sicherungsrichtlinie für Servervolumes und den Windows-Systemstatus erstellen. Dieser Leitfaden ist für das Sichern von Workloads auf Azure vorgesehen, die auf virtuellen Computern (VMs) unter dem Betriebssystem Azure Stack HCI ausgeführt werden.

Weitere Informationen zur Integration von Azure in Windows Admin Center finden Sie unter [Verbinden von Windows Server mit Azure-Hybriddiensten](/windows-server/manage/windows-admin-center/azure/).

## <a name="how-azure-backup-works-with-windows-admin-center"></a>Funktionsweise von Azure Backup mit Windows Admin Center
**Azure Backup** ist ein Dienst, den Sie zum Sichern, Schützen und Wiederherstellen Ihrer Daten in Azure verwenden können. Azure Backup ersetzt entweder vorhandene lokale oder standortexterne Sicherungslösungen durch eine zuverlässige, sichere und wirtschaftliche Cloudlösung.

Mehr dazu erfahren Sie unter [Worum handelt es sich beim Azure Backup-Dienst?](/azure/backup/backup-overview)

Azure Backup verfügt über mehrere Komponenten, die Sie herunterladen und auf dem jeweiligen Computer, Server oder in der Cloud bereitstellen. Die Komponente (der Agent), die Sie bereitstellen, richtet sich danach, was geschützt werden soll. Alle Azure Backup-Komponenten können genutzt werden, um Daten in einem Recovery Services-Tresor in Azure zu sichern, unabhängig davon, ob Daten lokal oder in Azure geschützt werden sollen.

Die Azure Backup-Integration in Windows Admin Center eignet sich ideal für das Sichern von Volumes und des Windows-Systemstatus sowohl für lokale Windows-Server als auch für virtuelle Server. Der umfassende Prozess sichert Dateiserver, Domänencontroller und IIS-Webserver.

Der Zugriff auf Azure Backup in Windows Admin Center erfolgt über das **Backup**-Tool. Mithilfe der Setup-, Verwaltungs- und Überwachungsfunktionen des **Backup**-Tools steigen Sie schnell in das Sichern von Servern ein, vollziehen Sicherungs- und Wiederherstellungsvorgänge und überwachen die Gesamtintegrität der Sicherungen von Windows-Servern.

## <a name="prerequisites"></a>Voraussetzungen
Die folgenden Voraussetzungen müssen erfüllt sein, um Azure Backup zu verwenden:
- Ein Azure-Konto mit mindestens einem aktiven Abonnement
- Internetzugriff auf Azure für die Windows-Zielserver
- Eine Verbindung vom Windows Admin Center-Gateway zu Azure

    Weitere Informationen finden Sie unter [Konfigurieren der Azure-Integration](/windows-server/manage/windows-admin-center/azure/azure-integration).

## <a name="getting-started-with-azure-backup"></a>Erste Schritte mit Azure Backup
Wenn Sie das **Backup**-Tool in Windows Admin Center zum ersten Mal auswählen, um eine Serververbindung mit Azure einzurichten, wird die Seite **Willkommen bei Azure Backup** angezeigt. Wählen Sie **Set up Azure Backup** (Azure Backup einrichten) aus, um den Azure Backup-Setup-Assistenten zu starten. In den folgenden Abschnitten sind die Schritte im Assistenten im Detail dargestellt.

Wenn Azure Backup bereits für eine Serververbindung konfiguriert wurde, wird durch Auswählen des **Backup**-Tools das **Backup-Dashboard** geöffnet. Informationen zu den Vorgängen und Aufgaben, die Sie über das Dashboard ausführen können, finden Sie im Abschnitt [Verwaltung und Überwachung](#management-and-monitoring).

### <a name="step-1-log-on-to-the-microsoft-azure-portal"></a>Schritt 1: Anmelden beim Microsoft Azure-Portal
Melden Sie sich bei Ihrem Azure-Konto an.

> [!NOTE]
> Wenn Sie bereits eine Verbindung des Windows Admin Center-Gateways mit Azure hergestellt haben, sollte Ihre Anmeldung beim Portal automatisch erfolgen. Wählen Sie **Abmelden** aus, um sich anschließend als anderer Benutzer anzumelden.

### <a name="step-2-set-up-azure-backup"></a>Schritt 2: Einrichten von Azure Backup
Wählen Sie für Azure Backup die folgenden Einstellungen aus:
- **Abonnement-ID:** Das Azure-Abonnement, das Sie zum Sichern von Windows-Servern auf Azure verwenden möchten. Alle Azure-Ressourcen, wie die Azure-Ressourcengruppe und der Recovery Services-Tresor, werden erstellt und mit dem ausgewählten Abonnement verknüpft.
- **Tresor:** Der Recovery Services-Tresor ist der Speicherort, an dem die Sicherungen Ihrer Server gespeichert werden. Sie können entweder einen vorhandenen Tresor verwenden oder von Windows Admin Center einen neuen erstellen lassen.  
- **Ressourcengruppe:** Die Azure-Ressourcengruppe ist ein Container für eine Sammlung von Ressourcen. Der Recovery Services-Tresor wird entweder in der angegebenen Ressourcengruppe erstellt oder ist in ihr enthalten. Sie können entweder eine vorhandene Ressourcengruppe verwenden oder von Windows Admin Center eine neue erstellen lassen.
- **Standort:** Die Azure-Region, in der der Recovery Services-Tresor erstellt wird. Wir empfehlen, die Azure-Region auszuwählen, die am nächsten am zu sichernden Windows-Server gelegen ist.

### <a name="step-3-select-backup-items-and-schedule"></a>Schritt 3: Auswählen von Sicherungselementen und Zeitplan
1. Wählen Sie auf dem Server die Elemente aus, die Sie sichern möchten. In Windows Admin Center können Sie eine Kombination aus Volumes und Windows-Systemstatus auswählen; die geschätzte Größe der für die Sicherung ausgewählten Daten wird angezeigt.

    > [!NOTE]
    > Die erste Sicherung ist eine vollständige Sicherung aller ausgewählten Daten. Nachfolgende Sicherungen sind inkrementell und übertragen nur Änderungen an den Daten, die seit der vorhergehenden Sicherung aufgetreten sind.

1. Wählen Sie unter mehreren vordefinierten **Sicherungszeitplänen** für Ihren Windows-Systemstatus und die Volumes.

### <a name="step-4-enter-an-encryption-passphrase"></a>Schritt 4: Eingeben einer Verschlüsselungspassphrase
1. Geben Sie eine **Verschlüsselungspassphrase** mit mindestens 16 Zeichen ein. Azure Backup schützt Ihre Sicherungsdaten mit einer Verschlüsselungspassphrase, die von Ihnen erstellt und verwaltet wird. Die Verschlüsselungspassphrase ist erforderlich, um Daten aus Azure Backup wiederherzustellen.

    > [!NOTE]
    > Speichern Sie die Passphrase an einem sicheren Ort außerhalb des Standorts, wie etwa auf einem anderen Server oder im [Azure Key Vault](/azure/key-vault/quick-create-portal). Microsoft speichert die Passphrase nicht und kann sie nicht abrufen oder zurücksetzen, wenn sie verloren oder vergessen wird.

1. Überprüfen Sie alle Einstellungen auf der Seite des Assistenten, und wählen Sie dann **Übernehmen** aus.

Daraufhin führt Windows Admin Center die folgenden Vorgänge aus:
1. Erstellt eine Azure-Ressourcengruppe, wenn noch keine vorhanden ist.
1. Erstellt einen Azure Recovery Services-Tresor gemäß den Angaben.
1. Installiert den Microsoft Azure Recovery Services Agent und registriert ihn beim Tresor.
1. Erstellt den Sicherungs- und Aufbewahrungszeitplan gemäß den mit dem Windows Server verknüpften ausgewählten Optionen.

## <a name="management-and-monitoring"></a>Verwaltung und Überwachung
Nach dem erfolgreichen Einrichten von Azure Backup wird das **Backup-Dashboard** angezeigt, wenn Sie das **Backup**-Tool für eine vorhandene Serververbindung öffnen. Vom Dashboard aus können Sie die folgenden Aufgaben ausführen:
- **Zugreifen auf den Tresor in Azure:** Wählen Sie auf der Registerkarte **Übersicht** die Option **Recovery Services-Tresor** aus, um auf den Tresor zuzugreifen, in dem Sie viele Verwaltungsvorgänge ausführen können. Weitere Informationen finden Sie unter [Überwachen und Verwalten von Recovery Services-Tresoren](/azure/backup/backup-azure-manage-windows-server).
- **Ausführen einer Ad-hoc-Sicherung:** Wählen Sie **Jetzt sichern** aus, um eine Ad-hoc-Sicherung zu erstellen. 
- **Überwachen von Aufträgen und Konfigurieren von Warnungsbenachrichtigungen:** Navigieren Sie zur Registerkarte **Aufträge**, um aktuelle oder bereits erledigte Aufträge zu überwachen, und [konfigurieren Sie Warnungsbenachrichtigungen](/azure/backup/backup-azure-manage-windows-server#configuring-notifications-for-alerts), um E-Mail zu Fehlern bei Aufträgen und sonstige Warnungen zur Sicherungen zu erhalten.
- **Anzeigen von Wiederherstellungspunkten und Wiederherstellen von Daten:** Wählen Sie die Registerkarte **Wiederherstellungspunkte** aus, um Wiederherstellungspunkte anzuzeigen, und wählen Sie **Daten wiederherstellen** aus, um Schritte zum Wiederherstellen Ihrer Daten von Azure anzuzeigen.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie auch unter:
- [Schützen von Azure Stack HCI-VMs mithilfe von Azure Site Recovery](./azure-site-recovery.md)
