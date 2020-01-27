---
title: 'Azure Stack Hub-Rechenzentrumsintegration: Exemplarische Vorgehensweise | Microsoft-Dokumentation'
description: Erfahren Sie, was Sie bei einer erfolgreichen lokalen Bereitstellung von Azure Stack Hub in Ihrem Rechenzentrum erwarten können – von der Planung bis zu den Aktionen nach der Bereitstellung.
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
ms.date: 11/07/2019
ms.author: mabrigg
ms.reviewer: asganesh
ms.lastreviewed: 11/07/2019
ms.openlocfilehash: e5175c11887d3a9afec2c7887f56b605b159f84f
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75818433"
---
# <a name="azure-stack-hub-datacenter-integration-walkthrough"></a>Azure Stack Hub-Rechenzentrumsintegration: Exemplarische Vorgehensweise

Dieser Artikel beschreibt den End-to-End-Prozess für die Azure Stack Hub-Rechenzentrumsintegration – von der Anschaffung bis zur Unterstützung nach der Bereitstellung. Die Integration ist ein gemeinschaftliches Projekt zwischen dem Kunden, einem Lösungsanbieter und Microsoft. Klicken Sie auf die folgenden Registerkarten, um die jeweiligen Schritte für jedes Projektmitglied anzuzeigen. In den nächsten Abschnitten finden Sie eine Zusammenfassung der verschiedenen Phasen für den Projektzeitplan. 

# <a name="customertabcustomer"></a>[Kunde](#tab/customer)

1. Beschreiben von Anwendungsfällen und Anforderungen
1. Bestimmen des Abrechnungsmodells
1. Überprüfen und Genehmigen von Verträgen
1. Ausfüllen des [Arbeitsblatts für die Bereitstellung](azure-stack-deployment-worksheet.md)
1. Sicherstellen, dass die Voraussetzungen für die Bereitstellung erfüllt sind
1. Vorbereiten des Rechenzentrums 
1. Angeben von Abonnementinformationen während der Bereitstellung
1. Beantworten aller Fragen zu den bereitgestellten Daten

# <a name="partnertabpartner"></a>[Partner](#tab/partner)

1. Empfehlen von Lösungsoptionen auf der Grundlage der Kundenanforderungen
1. Vorschlagen einer Proof of Concept-Strategie 
1. Auswählen der Supportebene
1. Vorbereiten von Verträgen mit dem Kunden
1. Erstellen einer Kundenbestellung
1. Festlegen des Bereitstellungszeitplans
1. Herstellen des Kontakts zwischen dem Kunden und Microsoft 
1. Schulen des Kunden bei der Bereitstellung 
1. Unterstützen des Kunden bei der Überprüfung der gesammelten Daten
1. Installieren und Validieren des Baselinebuilds und des Microsoft Deployment Toolkit
1. Versenden der Hardware an den Kundenstandort
1. Bereitstellen eines Technikers vor Ort
1. Rack und Stack
1. Bereitstellen des Hardwarelebenszyklus-Hosts (Hardware Lifecycle Host, HLH) 
1. Bereitstellen von Azure Stack Hub
1. Übergabe an den Kunden

# <a name="microsofttabmicro"></a>[Microsoft](#tab/micro)

1. Einbinden des Partners für Presales-Support
2. Vorbereiten von Softwarelizenzierung und Verträgen
3. Bereitstellen von Tools zum Erfassen der Anforderungen für die Rechenzentrumsintegration
4. Bereitstellen monatlicher Baselinebuilds und Toolkettenaktualisierungen
5. Unterstützung bei eventuellen Bereitstellungsproblemen durch Supporttechniker von Microsoft

---

## <a name="planning"></a>Planung
Microsoft oder ein Azure Stack Hub-Lösungspartner unterstützt Sie bei der Bewertung Ihrer Ziele. Sie beantworten Ihnen Fragen wie:

-   Ist Azure Stack Hub die richtige Lösung für Ihre Organisation?
-   Welche Art von Abrechnungs- und Lizenzierungsmodell wird bei Ihrer Organisation funktionieren?
-   Welche Größenlösung benötigen Sie?
-   Wie sind die Anforderungen an Stromversorgung und Kühlung?

Verwenden Sie den [Azure Stack Hub Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822), um die beste Hardwarekapazität und -konfiguration für Ihre Anforderungen zu ermitteln und zu analysieren. 

## <a name="ordering"></a>Bestellung
Ihre Organisation entscheidet sich für den Erwerb von Azure Stack Hub, unterzeichnet Verträge und Aufträge und stellt die Daten der Integrationsanforderungen für den Lösungsanbieter bereit.

## <a name="pre-deployment"></a>Vor der Bereitstellung
Sie entscheiden, wie Sie Azure Stack Hub in Ihr Rechenzentrum integrieren. Microsoft hat zusammen mit Lösungsanbietern ein [Arbeitsblatt für die Bereitstellung](azure-stack-deployment-worksheet.md) veröffentlicht, das Sie beim Erfassen der erforderlichen Informationen unterstützt.
Der Artikel [General datacenter integration considerations](azure-stack-datacenter-integration.md) (Allgemeine Überlegungen zur Datencenterintegration) enthält Informationen, die Ihnen beim Ausfüllen der Vorlage, dem so genannten Arbeitsblatt „Deployment Worksheet“ (Arbeitsblatt für die Bereitstellung), helfen sollen.

> [!IMPORTANT]
> Alle Voraussetzungen werden vor der Bestellung der Lösung untersucht, um Verzögerungen bei der Bereitstellung zu vermeiden. Die Überprüfung der Voraussetzungen kann einige Zeit in Anspruch nehmen und die Koordination und Datensammlung aus verschiedenen Abteilungen innerhalb Ihres Unternehmens erfordern. 

Sie wählen Folgendes aus:

- **Azure Stack Hub-Verbindungsmodell und Identitätsanbieter**. Sie können Azure Stack Hub entweder [mit dem Internet (und mit Azure) verbunden oder unverbunden](azure-stack-connection-models.md) bereitstellen. Um optimal von Azure Stack Hub (Hybridszenarien) zu profitieren, empfiehlt es sich, die Bereitstellung mit einer Verbindung mit Azure durchzuführen. Die Wahl von Active Directory-Verbunddienste (AD FS) oder Azure Active Directory (Azure AD) ist eine Entscheidung, die Sie bei der Bereitstellung einmalig treffen müssen. **Sie können Ihren Identitätsanbieter später nicht mehr ändern, ohne das gesamte System neu bereitzustellen**.

- **Lizenzierungsmodell**. Die Lizenzierungsmodelloptionen, zwischen denen Sie auswählen können, hängen von der Art der verwendeten Bereitstellung ab. Die Wahl des Identitätsanbieters hat keinen Einfluss auf virtuelle Mandantencomputer oder das von ihnen verwendete Identitätssystem und die von ihnen verwendeten Konten.
    - Kunden in einer [nicht verbundenen Bereitstellung](azure-stack-disconnected-deployment.md) haben nur eine Option: kapazitätsbasierte Abrechnung.

    - Kunden in einer [verbundenen Bereitstellung](azure-stack-connected-deployment.md) können zwischen kapazitätsbasierter Abrechnung und nutzungsbasierter Abrechnung wählen. Für die kapazitätsbezogene Abrechnung ist für die Registrierung ein Azure-Abonnement des Typs EA (Enterprise Agreement) erforderlich. Dies wird für die Registrierung benötigt, die für die Verfügbarkeit von Elementen auf Azure Marketplace über ein Azure-Abonnement sorgt.

- **Netzwerkintegration**. Die [Netzwerkintegration](azure-stack-network.md) ist äußerst wichtig für Bereitstellung, Betrieb und Verwaltung von Azure Stack Hub-Systemen. Es müssen verschiedene Aspekte berücksichtigt werden, um sicherzustellen, dass die Azure Stack Hub-Lösung resilient ist und über eine hoch verfügbare physische Infrastruktur verfügt, um die zugehörigen Vorgänge zu unterstützen.

- **Firewallintegration**. Wir empfehlen Ihnen, Azure Stack Hub [mit einer Firewall](azure-stack-firewall.md) zu schützen. Firewalls können bei der Verhinderung von DDoS-Angriffen, bei Angriffserkennung und Inhaltsuntersuchung helfen. Allerdings muss daran gedacht werden, dass sie zu einem Durchsatzengpass für Azure-Speicherdienste werden können.

- **Zertifikatanforderungen**. Es ist sehr wichtig, dass [alle erforderlichen Zertifikate](azure-stack-pki-certs.md) zur Verfügung stehen, *bevor* ein Vor-Ort-Techniker in Ihrem Datencenter zur Bereitstellung eintrifft.

Sobald alle erforderlichen Informationen über das Arbeitsblatt für die Bereitstellung erfasst wurden, startet der Lösungsanbieter den Factoryprozess anhand der gesammelten Daten, um eine erfolgreiche Integration von Azure Stack Hub in Ihr Rechenzentrum sicherzustellen.

## <a name="hardware-delivery"></a>Hardwarelieferung 
Ihr Lösungsanbieter arbeitet mit Ihnen bei der Planung zusammen, wann die Lösung in Ihrer Einrichtung eintrifft. Nach Erhalt der Lösung müssen Sie mit dem Lösungsanbieter einen Termin vereinbaren, an dem ein Techniker die Azure Stack Hub-Bereitstellung vor Ort durchführen soll.

Es ist **äußerst wichtig**, dass alle erforderlichen Daten gesperrt und verfügbar sind, *bevor der Vor-Ort-Techniker eintrifft, um die Lösung bereitzustellen*.

-   Alle Zertifikate müssen erworben werden und einsatzbereit sein.

-   Der Regionsname muss festgelegt werden.

-   Alle Parameter für die Netzwerkintegration sind abgeschlossen und stimmen mit dem überein, was Sie für Ihren Lösungsanbieter freigegeben haben.

> [!TIP]
> Wenn eine dieser Informationen geändert wurde, teilen Sie diese Änderung dem Lösungsanbieter unbedingt mit, bevor Sie die tatsächliche Bereitstellung planen.

## <a name="onsite-deployment"></a>Lokale Bereitstellung 
Zum Bereitstellen von Azure Stack Hub muss ein Vor-Ort-Techniker vom Lösungsanbieter für Ihre Hardware anwesend sein, um die Bereitstellung zu starten. Um für eine erfolgreiche Bereitstellung zu sorgen, sollten Sie sicherstellen, dass keine der über das Arbeitsblatt für die Bereitstellung angegebenen Informationen geändert wurden.

Anhand der folgenden Überprüfungen wird beschrieben, was Sie vom Vor-Ort-Techniker während des Bereitstellungsverfahrens erwarten sollten:

- Überprüfen der gesamten Verkabelung und Grenzkonnektivität, um sicherzustellen, dass die Lösung ordnungsgemäß zusammengesetzt wird und Ihre Anforderungen erfüllt.
- Konfigurieren des Lösungs-HLH (Hardware Lifecycle Host, Hardwarelebenszyklus-Host), falls vorhanden.
- Überprüfen, ob alle BMC-, BIOS- und Netzwerkeinstellungen korrekt sind.
- Sicherstellen, dass die Firmware für alle Komponenten die neueste durch die Lösung genehmigte Version aufweist.
- Starten der Bereitstellung.

> [!NOTE]
> Die Durchführung des Bereitstellungsverfahrens durch den Vor-Ort-Techniker kann ungefähr eine Geschäftswoche dauern.

## <a name="post-deployment"></a>Nach der Bereitstellung 
Vom Partner müssen mehrere Schritte ausgeführt werden, bevor die Lösung in der Phase nach der Integration an den Kunden übergeben wird. In dieser Phase ist die Validierung wichtig, um sicherzustellen, dass das System korrekt bereitgestellt wird und einwandfrei funktioniert. 

Folgende Aktionen sollten vom OEM-Partner ausgeführt werden:

- [Ausführen von „Test-AzureStack“](azure-stack-diagnostic-test.md)

- [Registrierung bei Azure](azure-stack-registration.md)

- [Marketplace-Syndikation](azure-stack-download-azure-marketplace-item.md#use-the-marketplace-syndication-tool-to-download-marketplace-items)

- Sichern der Switchkonfigurations- und HLH-Konfigurationsdateien

- Entfernen der DVM

- Vorbereiten einer Kundenzusammenfassung zur Bereitstellung

- [Überprüfen von Updates, um sicherzustellen, dass die Lösungssoftware auf die neueste Version aktualisiert wurde](./azure-stack-updates.md)

Es gibt mehrere Schritte, die – je nach Installationstyp – entweder erforderlich oder optional sind.

- Wenn die Bereitstellung mit [AD FS](azure-stack-integrate-identity.md) durchgeführt wurde, muss der Azure Stack Hub-Stempel in die eigenen AD FS des Kunden integriert werden.

  > [!NOTE]
  > Für diesen Schritt ist der Kunde zuständig, obwohl der Partner optional wählen kann, dass er hierfür Dienste anbietet.

- Integration in ein vorhandenes Überwachungssystem vom jeweiligen Partner.

  -   [System Center Operations Manager-Integration](azure-stack-integrate-monitor.md) unterstützt auch Flottenmanagement-Funktionen.

  -   [Nagios-Integration](azure-stack-integrate-monitor.md#integrate-with-nagios)

## <a name="schedule"></a>Zeitplan

![Gesamtzeitplan für die lokale Azure Stack Hub-Bereitstellung](./media/azure-stack-datacenter-integration-walkthrough/image1.png)

## <a name="support"></a>Support
Azure Stack Hub ermöglicht eine mit Azure konsistente, integrierte Supporterfahrung, die den vollständigen Lebenszyklus umfasst. Zur vollständigen Unterstützung von integrierten Azure Stack Hub-Systemen benötigen Kunden zwei Supportverträge: einen mit Microsoft (oder ihrem Cloudlösungsanbieter) für Azure-Dienstsupport und einen mit dem Hardwareanbieter für Systemsupport. Das integrierte Supportsystem basiert auf koordinierten Eskalations- und Behebungsvorgängen, damit Kunden eine einheitliche Supporterfahrung machen – ganz gleich, wen sie zuerst anrufen. Für Kunden, die nicht bereits über Premier-, Azure -Standard/ProDirect- oder Partner-Supportverträge mit Microsoft verfügen, ist der Azure Stack Hub-Softwaresupport eingeschlossen.

Die integrierte Supporterfahrung nutzt einen Fallaustauschmechanismus für bidirektionale Übermittlung von Supportfällen und Fallaktualisierungen zwischen Microsoft und dem Hardwarepartner. Microsoft Azure Stack Hub entspricht der [Modern Lifecycle-Richtlinie](https://support.microsoft.com/help/30881).

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie mehr zu [allgemeine Überlegungen zur Datencenterintegration](azure-stack-datacenter-integration.md).
