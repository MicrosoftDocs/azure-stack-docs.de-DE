---
title: Sicherheitskontrollen für Azure Stack Hub
titleSuffix: Azure Stack Hub
description: Hier erfahren Sie mehr über den Sicherheitsstatus sowie die Sicherheitskontrollen, die auf Azure Stack Hub angewendet werden.
author: JustinHall
ms.topic: article
ms.date: 06/10/2019
ms.author: justinha
ms.reviewer: fiseraci
ms.lastreviewed: 1/16/2019
ms.openlocfilehash: 63d50749d3877209dd81ca7a59a38e634b2d46e7
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77697551"
---
# <a name="azure-stack-hub-infrastructure-security-controls"></a>Sicherheitskontrollen der Azure Stack Hub-Infrastruktur

Sicherheitsüberlegungen und Konformitätsbestimmungen gehören zu den wichtigsten Beweggründen für die Verwendung von Hybrid Clouds. Azure Stack Hub ist für diese Szenarien konzipiert. In diesem Artikel werden die Sicherheitsmaßnahmen für Azure Stack Hub beschrieben.

In Azure Stack Hub sind zwei Sicherheitsstatusebenen vorhanden. Die erste Ebene ist die Azure Stack Hub-Infrastruktur, die sämtliche Komponenten von den Hardwarekomponenten bis hin zum Azure Resource Manager umfasst. Die erste Ebene beinhaltet das Administrator- und Benutzerportal. Die zweite Ebene besteht aus den Workloads, die von Mandanten erstellt, bereitgestellt und verwaltet werden. Die zweite Ebene beinhaltet Elemente wie virtuelle Computer und App Service-Websites.

## <a name="security-approach"></a>Sicherheitsansatz

Der Sicherheitsstatus für Azure Stack Hub wurde für die Abwehr moderner Bedrohungen und die Erfüllung der Anforderungen der wichtigsten Compliancestandards konzipiert. Daraus resultiert, dass der Sicherheitsstatus der Azure Stack Hub-Infrastruktur auf zwei Säulen ruht:

- **Von einer Sicherheitsverletzung ausgehen**  
    Von der Annahme ausgehend, dass bereits eine systemseitige Sicherheitsverletzung vorliegt, konzentrieren wir uns auf das *Erkennen und Beschränken der Auswirkungen von Sicherheitsverletzungen*, anstatt nur zu versuchen, Angriffe zu verhindern.

- **Standardmäßig verstärkt**  
    Da die Infrastruktur auf klar definierter Hardware und Software ausgeführt wird, *aktiviert, konfiguriert und überprüft Azure Stack Hub standardmäßig alle Sicherheitsfeatures*.

Da Azure Stack Hub als integriertes System geliefert wird, wird der Sicherheitsstatus der Azure Stack Hub-Infrastruktur von Microsoft definiert. Genau wie in Azure sind Mandanten dafür verantwortlich, den Sicherheitsstatus ihrer Mandantenworkloads zu definieren. Dieses Dokument vermittelt grundlegende Kenntnisse über den Sicherheitsstatus der Azure Stack Hub-Infrastruktur.

## <a name="data-at-rest-encryption"></a>Verschlüsselung für ruhende Daten

Alle Infrastruktur- und Mandantendaten von Azure Stack Hub sind im Ruhezustand mit BitLocker verschlüsselt. Diese Verschlüsselung bietet Schutz vor physischem Verlust bzw. Diebstahl von Azure Stack Hub-Speicherkomponenten. Weitere Informationen finden Sie unter [Verschlüsselung für ruhende Daten in Azure Stack Hub](azure-stack-security-bitlocker.md).

## <a name="data-in-transit-encryption"></a>Verschlüsselung von Daten während der Übertragung

Die Azure Stack Hub-Infrastrukturkomponenten kommunizieren mithilfe von Kanälen, die mit TLS 1.2 verschlüsselt sind. Verschlüsselungszertifikate werden von der Infrastruktur selbst verwaltet.

Alle externen Infrastrukturendpunkte, z. B. die REST-Endpunkte oder das Azure Stack Hub-Portal, unterstützen TLS 1.2 für die sichere Kommunikation. Verschlüsselungszertifikate, die entweder von einem Drittanbieter oder der Zertifizierungsstelle Ihres Unternehmens ausgestellt werden, müssen für diese Endpunkte bereitgestellt werden.

Für diese externen Endpunkte können zwar selbstsignierte Zertifikate verwendet werden, doch Microsoft rät dringend von deren Verwendung ab.
Weitere Informationen zum Erzwingen von TLS 1.2 auf den externen Endpunkten von Azure Stack Hub finden Sie unter [Konfigurieren von Azure Stack Hub-Sicherheitskontrollen](azure-stack-security-configuration.md).

## <a name="secret-management"></a>Verwaltung von Geheimnissen

In der Azure Stack Hub-Infrastruktur werden zahlreiche Geheimnisse eingesetzt, wie z. B. Kennwörter. Die meisten von ihnen werden automatisch rotiert, da sie gruppenverwaltete Dienstkonten (group Managed Service Account, gMSA) sind, die alle 24 Stunden rotiert werden.

Die verbleibenden Geheimnisse, die keine gruppenverwalteten Dienstkonten sind, können manuell mit einem Skript im privilegierten Endpunkt rotiert werden.

Die Azure Stack Hub-Infrastruktur verwendet für alle internen Zertifikate RSA-Schlüssel mit 4096 Bit. Zertifikate mit der gleichen Schlüssellänge können auch für die externen Endpunkte verwendet werden. Weitere Informationen zu Geheimnissen und zur Zertifikatsrotation finden Sie unter [Rotieren von Geheimnissen in Azure Stack Hub](azure-stack-rotate-secrets.md).

## <a name="windows-defender-application-control"></a>Windows Defender-Anwendungssteuerung

Azure Stack Hub nutzt die neuesten Sicherheitsfeatures von Windows Server. Eines davon ist Windows Defender Application Control (WDAC, früherer Name „Codeintegrität“). Dieses Tool bietet Whitelists ausführbarer Dateien und stellt sicher, dass innerhalb der Azure Stack Hub-Infrastruktur nur autorisierter Code ausgeführt wird.

Autorisierter Code wurde von Microsoft oder dem OEM-Partner signiert. Der signierte autorisierte Code befindet sich in der Liste der zulässigen Software, die in einer von Microsoft definierten Richtlinie angegeben wird. Anders gesagt kann nur Software ausgeführt werden, die zur Ausführung in der Azure Stack Hub-Infrastruktur genehmigt wurde. Jeder Versuch, nicht autorisierten Code auszuführen, wird blockiert, und eine Warnung wird generiert. Azure Stack Hub erzwingt Codeintegrität für den Benutzermodus (User Mode Code Integrity, UMCI) und Hypervisor-Codeintegrität (HVCI).

Die WDAC-Richtlinie verhindert auch die Ausführung von Drittanbieter-Agents oder -Software in der Azure Stack Hub-Infrastruktur.
Weitere Informationen zu WDAD finden Sie unter [Windows Defender-Anwendungssteuerung und virtualisierungsbasierter Schutz der Codeintegrität](https://docs.microsoft.com/windows/security/threat-protection/device-guard/introduction-to-device-guard-virtualization-based-security-and-windows-defender-application-control).

## <a name="credential-guard"></a>Credential Guard

Ein weiteres Windows Server-Sicherheitsfeature in Azure Stack Hub ist Windows Defender Credential Guard. Dieses Tool schützt Anmeldeinformationen der Azure Stack Hub-Infrastruktur vor Pass-the-Hash- und Pass-the-Ticket-Angriffen.

## <a name="antimalware"></a>Antimalware

Jede Komponente in Azure Stack Hub (Hyper-V-Hosts und virtuelle Computer) wird mit Windows Defender Antivirus geschützt.

In Szenarien mit bestehender Verbindung werden mehrmals täglich Updates für Antivirendefinitionen und -programme angewendet. In Szenarien mit getrennter Verbindung werden Updates für Antischadsoftware im Rahmen der monatlichen Azure Stack Hub-Updates angewendet. Wenn in Szenarien mit getrennter Verbindung häufigere Updates  der Definitionen von Windows Defender erforderlich sind, unterstützt Azure Stack Hub auch das Importieren von Windows Defender-Updates. Weitere Informationen finden Sie unter [Aktualisieren von Windows Defender Antivirus in Azure Stack Hub](azure-stack-security-av.md).

## <a name="secure-boot"></a>Sicherer Start

Azure Stack Hub erzwingt den sicheren Start auf allen Hyper-V-Hosts und Infrastruktur-VMs. 

## <a name="constrained-administration-model"></a>Eingeschränktes Verwaltungsmodell

Die Verwaltung in Azure Stack Hub wird mit drei Einstiegspunkten gesteuert, von denen jeder einen bestimmten Zweck hat:

- Das [Administratorportal](azure-stack-manage-portals.md) bietet eine Point-and-Click-Benutzeroberfläche für tägliche Verwaltungsvorgänge.
- Azure Resource Manager macht alle Verwaltungsvorgänge des Administratorportals über eine REST-API verfügbar, die von PowerShell und der Azure CLI verwendet wird.
- Für bestimmte Low-Level-Vorgänge (etwa Rechenzentrumsintegration oder Unterstützungsszenarien) macht Azure Stack Hub einen als [privilegierten Endpunkt](azure-stack-privileged-endpoint.md) bezeichneten PowerShell-Endpunkt verfügbar. Dieser Endpunkt macht nur einen zugelassenen Satz von Cmdlets verfügbar und wird gründlich überwacht.

## <a name="network-controls"></a>Netzwerksteuerungen

Die Azure Stack Hub-Infrastruktur umfasst mehrere Ebenen der Zugriffssteuerungsliste (Access Control List, ACL) für das Netzwerk. Die ACLs verhindern unbefugten Zugriff auf die Infrastrukturkomponenten und begrenzen die Infrastrukturkommunikation auf die Pfade, die für ihr Funktionieren erforderlich sind.

Netzwerk-ACLs werden in drei Ebenen durchgesetzt:

- Ebene 1: Top-of Rack-Switches
- Ebene 2: Softwaredefiniertes Netzwerk
- Ebene 3: Host- und VM-Betriebssystemfirewalls

## <a name="regulatory-compliance"></a>Compliance

Azure Stack Hub hat eine formale Funktionsbewertung durch ein drittanbieterunabhängiges Überwachungsunternehmen durchlaufen. Infolgedessen ist Dokumentation darüber verfügbar, wie die Azure Stack Hub-Infrastruktur die geltenden Kontrollen aus mehreren wichtigen Compliancestandards erfüllt. Die Dokumentation ist keine Zertifizierung von Azure Stack Hub, da die Standards mehrere personal- und prozessbezogene Sicherheitsmaßnahmen beinhalten. Vielmehr können Kunden diese Dokumentation nutzen, um ihren Zertifizierungsprozess zu beschleunigen.

Die Bewertungen umfassen die folgenden Standards:

- [PCI-DSS](https://www.pcisecuritystandards.org/pci_security/) richtet sich an die Zahlungskartenbranche.
- [CSA Cloud Control Matrix](https://cloudsecurityalliance.org/group/cloud-controls-matrix/#_overview) ist eine umfassende Zuordnung über mehrere Standards hinweg, z.B. FedRAMP Moderate, ISO27001, HIPAA, HITRUST, ITAR, NIST SP800-53 und andere.
- [FedRAMP High](https://www.fedramp.gov/fedramp-releases-high-baseline/) für Behörden.

Die Dokumentation zur Compliance finden Sie im [Microsoft Service Trust-Portal](https://servicetrust.microsoft.com/ViewPage/AzureStack). Die Compliancehandbücher sind eine geschützte Ressource. Sie müssen sich daher mit den Anmeldeinformationen Ihres Azure-Clouddiensts anmelden.

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren von Azure Stack Hub-Sicherheitskontrollen](azure-stack-security-configuration.md)
- [Erfahren Sie, wie Sie Ihre Geheimnisse in Azure Stack Hub rotieren](azure-stack-rotate-secrets.md).
- [PCI-DSS- und CSA CCM-Dokumente für Azure Stack Hub](https://servicetrust.microsoft.com/ViewPage/TrustDocuments)
- [DoD- und NIST-Dokumente für Azure Stack Hub](https://servicetrust.microsoft.com/ViewPage/Blueprint)
