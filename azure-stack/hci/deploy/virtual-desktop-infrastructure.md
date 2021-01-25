---
title: Bereitstellen einer virtuellen Desktopinfrastruktur (VDI) auf Azure Stack HCI
description: Dieses Thema enthält Anleitungen zum Planen, Konfigurieren und Bereitstellen von virtueller Desktopinfrastruktur (VDI) auf dem Azure Stack HCI-Betriebssystem.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 01/15/2021
ms.openlocfilehash: dfe22eb768b5bf661760c31c913d8c93caf590a4
ms.sourcegitcommit: 0983c1f90734b7ea5e23ae614eeaed38f9cb3c9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/19/2021
ms.locfileid: "98571596"
---
# <a name="deploy-virtual-desktop-infrastructure-vdi-on-azure-stack-hci"></a>Bereitstellen einer virtuellen Desktopinfrastruktur (VDI) auf Azure Stack HCI

>Gilt für: Azure Stack HCI, Version 20H2

Dieses Thema enthält Anleitungen zum Planen, Konfigurieren und Bereitstellen von virtueller Desktopinfrastruktur (VDI) auf dem Azure Stack HCI-Betriebssystem. Nutzen Sie Ihre Investition in Azure Stack HCI, um eine zentralisierte, hochverfügbare, vereinfachte und sichere Verwaltung für die Benutzer in Ihrer Organisation bereitzustellen. Verwenden Sie diesen Leitfaden, um Szenarien wie Bring-your-own-Device (BYOD) für Ihre Benutzer zu ermöglichen und ihnen gleichzeitig eine konsistente und zuverlässige benutzerfreundliche Lösung für unternehmenskritische Anwendungen zu bieten, ohne die Sicherheit zu beeinträchtigen.

## <a name="overview"></a>Übersicht
VDI verwendet Serverhardware, um Desktopbetriebssysteme und Softwareprogramme auf einem virtuellen Computer (VM) auszuführen. Auf diese Weise können Sie mithilfe von VDI herkömmliche Desktopworkloads auf zentralisierten Servern ausführen. Zu den Vorteilen von VDI im Geschäftsumfeld gehört die Aufbewahrung sensibler Unternehmensanwendungen und -daten in einem sicheren Rechenzentrum und die Bereitstellung einer BYOD-Richtlinie, ohne sich Sorgen wegen einer Vermischung personenbezogener Daten mit Unternehmensressourcen machen zu müssen. VDI hat sich darüber hinaus zum Standard beim Unterstützen von Telearbeit und Arbeitsplätzen in Filialbüros sowie dem Erteilen von Zugriff für Vertragsnehmer und Partner entwickelt.

Azure Stack HCI bietet die optimale Plattform für VDI. Mit einer validierten Azure Stack HCI-Lösung in Kombination mit Microsoft [Remotedesktopdienste (RDS)](/windows-server/remote/remote-desktop-services/welcome-to-rds) können Sie eine hochverfügbare und hochgradig skalierbare Architektur erzielen.

Darüber hinaus bietet Azure Stack HCI-VDI die folgenden, einzigartigen cloudbasierten Funktionen zum Schutz von VDI-Workloads und -Clients:
- Zentral verwaltete Updates mithilfe der Azure-Updateverwaltung
- Vereinheitlichte Sicherheitsverwaltung und Advanced Threat Protection für VDI-Clients

## <a name="deploy-vdi"></a>Bereitstellen einer VDI
In diesem Abschnitt wird allgemein erläutert, wie Sie Hardware für die Bereitstellung von VDI auf Azure Stack HCI erwerben und Windows Admin Center zum Verwalten verwenden. Außerdem wird die Bereitstellung von RDS zur Unterstützung von VDI behandelt.

### <a name="step-1-acquire-hardware-for-vdi-on-azure-stack-hci"></a>Schritt 1: Erwerben von Hardware für VDI auf Azure Stack HCI
Zuerst müssen Sie Hardware beschaffen. Die einfachste Möglichkeit besteht darin, Ihren bevorzugten Microsoft-Hardwarepartner im [Azure Stack HCI-Katalog](https://hcicatalog.azurewebsites.net) zu suchen und ein integriertes System mit vorinstalliertem Azure Stack HCI-Betriebssystem zu kaufen. Im-Katalog können Sie filtern, um Herstellerhardware anzuzeigen, die für diese Art Workload optimiert ist. Wenden Sie sich unbedingt an Ihren Hardwarepartner, um sicherzustellen, dass die Hardware die Anzahl der virtuellen Desktops unterstützen kann, die Sie auf Ihrem Cluster hosten möchten.

Andernfalls müssen Sie das Azure Stack HCI-Betriebssystem auf Ihrer eigenen Hardware bereitstellen. Details zu den Azure Stack HCI-Bereitstellungsoptionen und zum Installieren von Windows Admin Center finden Sie unter [Bereitstellen des Azure Stack HCI-Betriebssystems](./operating-system.md).

Verwenden Sie als nächstes das Windows Admin Center, um [einen Azure Stack HCI-Cluster zu erstellen](./create-cluster.md).

### <a name="step-2-set-up-azure-update-management-in-windows-admin-center"></a>Schritt 2: Einrichten der Azure-Updateverwaltung in Windows Admin Center
Richten Sie In Windows Admin Center die [Azure-Updateverwaltung](/windows-server/manage/windows-admin-center/azure/azure-update-management) ein, um den Status verfügbarer Updates schnell zu beurteilen, erforderliche Updates zu planen und Bereitstellungsergebnisse zu überprüfen, um die angewendeten Updates zu bestätigen.

Für den Einstieg in die Azure-Updateverwaltung benötigen Sie ein Microsoft Azure-Abonnement. Wenn Sie nicht über ein Abonnement verfügen, können Sie sich für ein [kostenloses Testabonnement](https://azure.microsoft.com/free) registrieren.

Sie können das Windows Admin Center ebenfalls verwenden, um zusätzliche Azure-Hybriddienste wie Sicherung, Dateisynchronisierung, Sitewiederherstellung, Punkt-zu-Standort-VPN und Azure Security Center einzurichten.

### <a name="step-3-deploy-remote-desktop-services-rds-for-vdi-support"></a>Schritt 3: Bereitstellen von Remotedesktopdienste (RDS) für die VDI-Unterstützung
Nachdem Sie Ihre Azure Stack HCI-Bereitstellung abgeschlossen und sich bei Azure registriert haben, um die Updateverwaltung zu nutzen, sind Sie bereit, die Anleitungen in diesem Abschnitt zum Erstellen und Bereitstellen von RDS zur Unterstützung von VDI zu verwenden.

So erstellen Sie RDS und stellen sie bereit:
1. [Bereitstellen einer Remotedesktop-Umgebung](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)
1. [Erstellen einer RDS-Sitzungssammlung](/windows-server/remote/remote-desktop-services/rds-create-collection) zum Freigeben von Apps und Ressourcen
1. [License your RDS deployment with client access licenses (CALs)](/windows-server/remote/remote-desktop-services/rds-client-access-license) (Lizenzieren deiner RDS-Bereitstellung mit Clientzugriffslizenzen (CALs))
1. Anweisen Ihrer Benutzer, einen  [Remotedesktopclient](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients) zu installieren, um auf Apps und Ressourcen zuzugreifen
1. Hinzufügen von Verbindungsbrokern und Sitzungshosts zum Aktivieren von Hochverfügbarkeit:
    - [Horizontales Skalieren einer vorhandenen RDS-Sammlung mit einer RD-Sitzungshostfarm](/windows-server/remote/remote-desktop-services/rds-scale-rdsh-farm)
    - [Hinzufügen von hoher Verfügbarkeit zur RD Connection Broker-Infrastruktur](/windows-server/remote/remote-desktop-services/rds-connection-broker-cluster)
    - [Hinzufügen von hoher Verfügbarkeit zur RD Web- und RD Gateway-Webfront](/windows-server/remote/remote-desktop-services/rds-rdweb-gateway-ha)
    - [Bereitstellen eines auf zwei Knoten und „Direkte Speicherplätze“ basierenden Dateisystems für die UPD-Speicherung](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment)

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen im Zusammenhang mit VDI finden Sie unter [Unterstützte Konfigurationen für Remotedesktopdienste](/windows-server/remote/remote-desktop-services/rds-supported-config)
