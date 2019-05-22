---
title: Azure Stack-Datencenterintegration Vorgehensweise | Microsoft-Dokumentation
description: Erfahren Sie, was Sie bei einer erfolgreichen lokalen Bereitstellung von Azure Stack in Ihrem Datencenter erwarten können.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: mabrigg
ms.reviewer: asganesh
ms.lastreviewed: 12/10/2018
ms.openlocfilehash: db75a99f9b8e4b3c0db272ccd0e855ea944b7129
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65617892"
---
# <a name="azure-stack-datacenter-integration"></a>Azure Stack-Rechenzentrumsintegration

In diesem Artikel wird die Azure Stack-End-to-End-Kundenerfahrung vom Kauf eines integrierten Systems bis zu einer erfolgreichen lokalen Bereitstellung durch einen Lösungsanbieter beschrieben. Nutzen Sie diese Informationen zur Vereinfachung Ihres Wegs und als Hilfe bei der Festlegung Ihrer Erwartungen als Azure Stack-Kunde.

Als Azure Stack-Kunde sollten Sie die folgenden Phasen erwarten:

|     |Planungsphase|Auftragsprozess|Vor der Bereitstellung|Factoryprozess|Hardwarelieferung|Lokale Bereitstellung|
|-----|-----|-----|-----|-----|-----|-----|
|**Microsoft**|Interagieren Sie mit dem Partner, um Presales-Support bereitzustellen.|Bereiten Sie Softwarelizenzierung und Verträge nach Bedarf vor.|Stellen Sie dem Kunden die erforderlichen Tools zum Sammeln von Anforderungen an die Datencenterintegration und Dokumentation bereit.|Stellen Sie die neuesten Baselinebuilds und Toolkettenaktualisierungen in einem monatlichen Rhythmus bereit.|–|Die Supporttechniker von Microsoft unterstützen Sie bei eventuellen Bereitstellungsproblemen.|
|**Partner**|Empfehlen Sie Lösungsoptionen anhand von Kundenanforderungen.<br><br>Schlagen Sie bei Bedarf Proof of Concept (POC) vor.<br><br>Richten Sie eine Geschäftsbeziehung ein.<br><br>Entscheiden Sie über die Supportebene.|Bereiten Sie erforderliche Verträge mit dem Kunden vor.<br><br>Erstellen Sie eine Kundenbestellung.<br><br>Legen Sie die Übermittlungszeitachse fest.<br><br>Stellen Sie bei Bedarf eine Verbindung zwischen dem Kunden und Microsoft her.|Bieten Sie dem Kunden die erforderliche Schulung, um sicherzustellen, dass er sämtliche Bereitstellungsvoraussetzungen und Optionen für die Datencenterintegration versteht.<br><br>Helfen Sie dem Kunden bei der Prüfung der gesammelten Daten, um deren Vollständigkeit und Genauigkeit sicherzustellen.|Wenden Sie den zuletzt überprüften Baselinebuild an.<br><br>Wenden Sie das erforderliche Microsoft Deployment Toolkit an.|Versenden Sie Hardware an den Kundenstandort.|Die Bereitstellung wird durch einen Vor-Ort-Techniker erledigt.<br><br>Rack und Stack.<br><br>HLH-Bereitstellung (Hardware Lifecycle Host, Hardwarelebenszyklus-Host).<br><br>Azure Stack-Bereitstellung.<br><br>Übergabe an den Kunden.|
|**Kunde**|Beschreiben Sie vorgesehene Anwendungsfälle, und geben Sie die Anforderungen an.|Bestimmen Sie das zu verwendende Abrechnungsmodell; überprüfen und genehmigen Sie Verträge.|Füllen Sie das [Arbeitsblatt für die Bereitstellung](azure-stack-deployment-worksheet.md) aus, und stellen Sie sicher, dass alle Bereitstellungsvoraussetzungen erfüllt und fertig für die Bereitstellung sind.|–|Bereiten Sie das Datencenter vor, indem Sie sicherstellen, dass die gesamte erforderliche Stromversorgung und Kühlung, die Grenzkonnektivität und andere Anforderungen an die Datencenterintegration vorhanden sind.|Stehen Sie während der Bereitstellung zur Verfügung, um Anmeldeinformationen und Support zum Abonnement zu bieten, sofern es Fragen zu den bereitgestellten Daten gibt.|
| | | | | | | |


## <a name="planning-phase"></a>Planungsphase
Die Planungsphase liegt vor, wenn Microsoft oder der Azure Stack-Lösungspartner mit Ihnen zusammenarbeiten, um Ihre Anforderungen auszuwerten, zu verstehen und so zu ermitteln, ob Azure Stack die richtige Lösung für Sie ist:

Sie helfen Ihnen bei der Entscheidung über die folgenden Punkte:

-   Ist Azure Stack die richtige Lösung für Ihre Organisation?

-   Welche Größenlösung benötigen Sie?

-   Welche Art von Abrechnungs- und Lizenzierungsmodell wird bei Ihrer Organisation funktionieren?

-   Wie sind die Anforderungen an Stromversorgung und Kühlung?

Um sicherzustellen, dass die Hardwarelösung Ihren Anforderungen am besten entspricht, kann der [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) als Unterstützung bei der Planung vor dem Kauf helfen, um die geeignete Kapazität und Konfiguration für Ihre Azure Stack-Hardwarelösung zu ermitteln.

Das Arbeitsblatt ist *nicht* als Ersatz für Ihre eigenen Untersuchungen und die Analyse von Hardwarelösungen gedacht, die Ihren Anforderungen am besten entsprechen. Bei der Planung einer Azure Stack-Bereitstellung sollten Sie auch die [allgemeinen Überlegungen zur Datencenterintegration](azure-stack-datacenter-integration.md) für integrierte Azure Stack-Systeme überprüfen.

## <a name="order-process-phase"></a>Auftragsprozessphase
In dieser Phase sollten viele Ihrer Fragen im Hinblick auf die Durchführbarkeit beantwortet worden sein. Jetzt, wo Sie zum Ausführen eines Commits für den Kauf von Azure Stack bereit sind, und nachdem Sie alle erforderlichen Verträge und Bestellungen unterzeichnet haben, werden Sie aufgefordert, Ihrem Lösungsanbieter die Integrationsanforderungsdaten bereitzustellen.

## <a name="pre-deployment-phase"></a>Phase vor der Bereitstellung
Während dieser Phase müssen Sie entscheiden, wie Sie Azure Stack in Ihr Datencenter integrieren möchten. Zur Erleichterung dieses Prozesses hat Microsoft in Zusammenarbeit mit den Lösungsanbietern eine Vorlage für Anforderungen zusammengestellt, damit Sie die erforderlichen Informationen zur Planung einer integrierten Systembereitstellung innerhalb Ihrer Umgebung zusammenstellen können.

Der Artikel [General datacenter integration considerations](azure-stack-datacenter-integration.md) (Allgemeine Überlegungen zur Datencenterintegration) enthält Informationen, die Ihnen beim Ausfüllen der Vorlage, dem so genannten Arbeitsblatt „Deployment Worksheet“ (Arbeitsblatt für die Bereitstellung), helfen sollen. 

> [!IMPORTANT]
> Während dieser Phase ist es wichtig, dass alle vorausgesetzten Informationen untersucht werden und darüber entschieden wird, bevor die Lösung bestellt wird. Denken Sie daran, dass dieser Schritt zeitaufwändig ist und dafür die Koordination und das Sammeln von Daten aus mehreren Fachrichtungen innerhalb Ihrer Organisation erforderlich ist. Falsche oder unvollständige Informationen können zu einer längeren Dauer der Bereitstellung führen. 

In der Phase vor der Bereitstellung müssen Sie über Folgendes entscheiden:

- **Azure Stack-Verbindungsmodell und Identitätsanbieter**. Sie können Azure Stack entweder bei [vorhandener Verbindung mit dem Internet (und mit Azure) oder offline bereitstellen](azure-stack-connection-models.md). Um von Azure Stack, einschließlich Hybridszenarien, am meisten zu profitieren, empfiehlt es sich, die Bereitstellung durchzuführen, wenn eine Verbindung mit Azure besteht. Die Wahl von Active Directory-Verbunddienste (AD FS) oder Azure Active Directory (Azure AD) ist eine Entscheidung, die Sie bei der Bereitstellung einmalig treffen müssen. **Sie können diese Einstellung später nicht mehr ändern, ohne dass Sie das gesamte System erneut bereitstellen müssen**.

- **Lizenzierungsmodell**. Die Lizenzierungsmodelloptionen, aus denen Sie auswählen können, hängen von der Art der verwendeten Bereitstellung ab. Die Wahl des Identitätsanbieters hat keinen Einfluss auf virtuelle Mandantencomputer oder das von ihnen verwendete Identitätssystem und die von ihnen verwendeten Konten.
    - Kunden in einer [nicht verbundenen Bereitstellung](azure-stack-disconnected-deployment.md) haben nur eine Option: kapazitätsbasierte Abrechnung.

    - Kunden in einer [verbundenen Bereitstellung](azure-stack-connected-deployment.md) können zwischen kapazitätsbasierter Abrechnung und nutzungsbasierter Abrechnung wählen. Für die kapazitätsbasierte Abrechnung ist bei der Registrierung ein Azure-Abonnement des Typs EA (Enterprise Agreement) erforderlich. Dies wird für die Registrierung benötigt, die für die Verfügbarkeit von Elementen im Marketplace über ein Azure-Abonnement sorgt.

- **Netzwerkintegration**. [Netzwerkintegration](azure-stack-network.md) ist äußerst wichtig für Bereitstellung, Betrieb und Verwaltung von Azure Stack-Systemen. Es gibt mehrere Überlegungen, um sicherzustellen, dass die Azure Stack-Lösung resilient ist und eine hoch verfügbare physische Infrastruktur zur Unterstützung der Vorgänge aufweist.

- **Firewallintegration**. Es empfiehlt sich, Azure Stack [mit einer Firewall](azure-stack-firewall.md) zu schützen. Firewalls können bei der Verhinderung von DDoS-Angriffen, bei Angriffserkennung und Inhaltsuntersuchung helfen. Allerdings muss daran gedacht werden, dass sie zu einem Durchsatzengpass für Azure-Speicherdienste werden können.


- **Zertifikatanforderungen**. Es ist sehr wichtig, dass [alle erforderlichen Zertifikate](azure-stack-pki-certs.md) zur Verfügung stehen, *bevor* ein Vor-Ort-Techniker in Ihrem Datencenter zur Bereitstellung eintrifft.

Sobald alle vorausgesetzten Informationen über das Arbeitsblatt für die Bereitstellung erfasst wurden, startet der Lösungsanbieter den Factoryprozess anhand der gesammelten Daten, um eine erfolgreiche Integration von Azure Stack in Ihr Datencenter sicherzustellen.

## <a name="hardware-delivery-phase"></a>Phase der Hardwarelieferung
Ihr Lösungsanbieter arbeitet mit Ihnen bei der Planung zusammen, wann die Lösung in Ihrer Einrichtung eintrifft. Nachdem alles empfangen und eingerichtet wurde, müssen Sie mit dem Lösungsanbieter planen, wann ein Techniker vor Ort kommt, um die Azure Stack-Bereitstellung durchzuführen.

Es ist **äußerst wichtig**, dass alle als vorausgesetzten Daten gesperrt und verfügbar sind, *bevor der Vor-Ort-Techniker eintrifft, um die Lösung bereitzustellen*.

-   Alle Zertifikate müssen erworben werden und einsatzbereit sein.

-   Der Regionsname muss festgelegt werden.

-   Alle Parameter für die Netzwerkintegration sind abgeschlossen und stimmen mit dem überein, was Sie für Ihren Lösungsanbieter freigegeben haben.

> [!TIP]
> Wenn eine dieser Informationen geändert wurde, teilen Sie diese Änderung dem Lösungsanbieter unbedingt mit, bevor Sie die tatsächliche Bereitstellung planen.

## <a name="onsite-deployment-phase"></a>Phase der lokalen Bereitstellung
Zum Bereitstellen von Azure Stack muss ein Vor-Ort-Techniker vom Lösungsanbieter für Ihre Hardware anwesend sein, um die Bereitstellung zu starten. Um für eine erfolgreiche Bereitstellung zu sorgen, stellen Sie sicher, dass keine der über das Arbeitsblatt für die Bereitstellung gelieferten Informationen geändert wurden. 

Im Folgenden wird beschrieben, was Sie vom Vor-Ort-Techniker während des Bereitstellungsverfahrens erwarten sollten:

- Überprüfen der gesamten Verkabelung und Grenzkonnektivität, um sicherzustellen, dass die Lösung ordnungsgemäß zusammengesetzt wird und Ihre Anforderungen erfüllt.
- Konfigurieren des Lösungs-HLH (Hardware Lifecycle Host, Hardwarelebenszyklus-Host), falls vorhanden.
- Überprüfen, ob alle BMC-, BIOS- und Netzwerkeinstellungen korrekt sind
- Sicherstellen, dass die Firmware aller Komponenten die neueste durch die Lösung genehmigte Version aufweist.
- Starten der Bereitstellung.

> [!NOTE]
> Die Durchführung des Bereitstellungsverfahrens durch den Vor-Ort-Techniker kann ungefähr eine Geschäftswoche dauern.

## <a name="post-deployment-phase"></a>Phase nach der Bereitstellung
Vom Partner müssen mehrere Schritte ausgeführt werden, bevor die Lösung in der Phase nach der Integration an den Kunden übergeben wird. In dieser Phase ist die Validierung wichtig, um sicherzustellen, dass das System korrekt bereitgestellt wird und einwandfrei funktioniert. 

Folgende Aktionen sollten vom OEM-Partner ausgeführt werden:

- [Ausführen von „Test-AzureStack“](azure-stack-diagnostic-test.md)

- [Registrierung bei Azure](azure-stack-registration.md)

- [Marketplace-Syndikation](azure-stack-download-azure-marketplace-item.md#use-the-marketplace-syndication-tool-to-download-marketplace-items)

- Sichern der Switchkonfigurations- und HLH-Konfigurationsdateien

- Entfernen des DVM

- Vorbereiten einer Kundenzusammenfassung zur Bereitstellung

- [Überprüfen von Updates, um sicherzustellen, dass die Lösungssoftware auf die neueste Version aktualisiert wurde](./azure-stack-updates.md)

Es gibt mehrere Schritte, die – je nach Installationstyp – entweder erforderlich oder optional sind.

- Wenn die Bereitstellung mit [AD FS](azure-stack-integrate-identity.md) durchgeführt wurde, muss der Azure Stack-Stempel in die eigene AD FS des Kunden integriert werden.

  > [!NOTE]
  > Für diesen Schritt ist der Kunde zuständig, obwohl der Partner optional wählen kann, dass er hierfür Dienste anbietet.

- Integration in ein vorhandenes Überwachungssystem vom jeweiligen Partner.

  -   [System Center Operations Manager-Integration](azure-stack-integrate-monitor.md) unterstützt auch Flottenmanagement-Funktionen.

  -   [Nagios-Integration](azure-stack-integrate-monitor.md#integrate-with-nagios)

## <a name="overall-timeline"></a>Gesamte Zeitachse

![](./media/azure-stack-datacenter-integration-walkthrough/image1.png)

## <a name="support"></a>Support
Azure Stack ermöglicht eine mit Azure konsistente, integrierte Supporterfahrung, die den vollständigen Lebenszyklus umfasst. Zur vollständigen Unterstützung von integrierten Azure Stack-Systemen benötigen Kunden zwei Supportverträge: einen mit Microsoft (oder deren Cloud-Lösungsanbieter) für Azure Services-Support und einen mit dem Hardwareanbieter für Systemsupport. Das integrierte Supportsystem bietet koordinierte Eskalation und Auflösung, damit Kunden eine einheitliche Supporterfahrung machen – ganz gleich, wen sie zuerst anrufen. Für Kunden, die nicht bereits über Premier-, Azure -Standard / ProDirect- oder Partner-Support bei Microsoft verfügen, ist der Azure Stack-Softwaresupport eingeschlossen.

Die integrierte Supporterfahrung nutzt einen Fallaustauschmechanismus für bidirektionale Übermittlung von Supportfällen und Fallaktualisierungen zwischen Microsoft und dem Hardwarepartner. Microsoft Azure Stack hält die [Modern Lifecycle-Richtlinie](https://support.microsoft.com/help/30881) ein.

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie mehr zu [allgemeine Überlegungen zur Datencenterintegration](azure-stack-datacenter-integration.md).
