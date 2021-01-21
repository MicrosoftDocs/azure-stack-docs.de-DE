---
title: Bereitstellen von Trusted Enterprise Virtualization auf Azure Stack HCI
description: Dieses Thema enthält Anleitungen zum Planen, Konfigurieren und Bereitstellen einer hochgradig sicheren Infrastruktur, die mit einer vertrauenswürdigen Virtualisierung für Unternehmen unter dem Azure Stack HCI-Betriebssystem arbeitet.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 01/07/2021
ms.openlocfilehash: bd9675efc87929c020f3c1514d220fece54099a7
ms.sourcegitcommit: 0983c1f90734b7ea5e23ae614eeaed38f9cb3c9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/19/2021
ms.locfileid: "98571593"
---
# <a name="deploy-trusted-enterprise-virtualization-on-azure-stack-hci"></a>Bereitstellen von Trusted Enterprise Virtualization auf Azure Stack HCI

>Gilt für: Azure Stack HCI, Version 20H2

Dieses Thema enthält Anleitungen zum Planen, Konfigurieren und Bereitstellen einer hochgradig sicheren Infrastruktur, die mit einer vertrauenswürdigen Virtualisierung für Unternehmen unter dem Azure Stack HCI-Betriebssystem arbeitet. Nutzen Sie Ihre Investition in Azure Stack HCI, um sichere Workloads auf Hardware auszuführen, die [virtualisierungsbasierte Sicherheit (VBS)](/windows-hardware/design/device-experiences/oem-vbs) und Hybrid Cloud-Dienste über das Windows Admin Center und das Azure-Portal verwendet.

## <a name="overview"></a>Übersicht
VBS ist eine Kernkomponente der [Sicherheitsinvestitionen in Azure Stack HCI](/windows-server/get-started-19/whats-new-19#security) zum Schutz von Hosts und virtuellen Computern (VMs) vor Sicherheitsbedrohungen. Beispielsweise führt der [Security Technical Implementation Guide (STIG)](https://nvd.nist.gov/ncp/checklist/914), der als Tool zur Verbesserung der Sicherheit von Informationssystemen des US-Verteidigungsministeriums veröffentlicht wird, VBS und [durch Hypervisor geschützte Codeintegrität (Hypervisor-Protected Code Integrity, HVCI)](/windows-hardware/drivers/bringup/device-guard-and-credential-guard) als allgemeine Sicherheitsanforderungen auf. Es ist zwingend erforderlich, Hosthardware zu verwenden, die für VBS und HVCI geeignet ist, um Workloads auf virtuellen Computern zu schützen, da ein gefährdeter Host keinen VM-Schutz sicherstellen kann.

VBS verwendet Funktionen zur Hardwarevirtualisierung, um einen sicheren Speicherbereich zu erstellen und vom Betriebssystem zu isolieren. Sie können den [Virtual Secure Mode (VSM)](/virtualization/hyper-v-on-windows/tlfs/vsm) in Windows zum Hosten einer Reihe von Sicherheitslösungen verwenden, mit denen der Schutz vor Betriebssystem-Sicherheitsrisiken und schädlichen Exploits erheblich erhöht werden kann.

VBS verwendet den Windows-Hypervisor zum Erstellen und Verwalten von Sicherheitsgrenzen in Betriebssystemsoftware, zum Erzwingen von Einschränkungen zum Schutz wichtiger Systemressourcen und zum Schützen von Sicherheitsressourcen, wie etwa authentifizierten Benutzeranmeldeinformationen. Mit VBS können Sie mögliche Exploits selbst dann stark einschränken und eindämmen, wenn Schadsoftware Zugang zum Betriebssystemkernel erhalten hat, da der Hypervisor Schadsoftware an der Ausführung von Code oder dem Zugriff auf Plattformgeheimnisse hindert.

Der Hypervisor, die Systemsoftware mit der höchsten Berechtigungsstufe, legt Seitenberechtigungen für den gesamten Arbeitsspeicher des Systems fest und setzt diese durch. In VSM können Seiten nur nach der Übergabe von Codeintegritätsprüfungen ausgeführt werden. Selbst wenn ein Sicherheitsrisiko eintritt, wie z. B. ein Pufferüberlauf, der es Malware ermöglichen könnte, den Speicher zu verändern, können Codeseiten nicht verändert werden, und veränderter Speicher kann nicht ausgeführt werden. VSB und HVCI verstärken die Durchsetzung der Codeintegritätsrichtlinie erheblich. Alle Kernelmodustreiber und Binärdateien werden vor dem Starten überprüft, und das Laden von nicht signierten Treibern oder Systemdateien in den Systemspeicher wird verhindert.

## <a name="deploy-trusted-enterprise-virtualization"></a>Bereitstellen von Trusted Enterprise Virtualization
Dieser Abschnitt erläutert allgemein, wie Sie Hardware zum Bereitstellen einer hochgradig sicheren Infrastruktur erwerben, die vertrauenswürdige Virtualisierung für Unternehmen unter Azure Stack HCI und Windows Admin Center zur Verwaltung verwendet.

### <a name="step-1-acquire-hardware-for-trusted-enterprise-virtualization-on-azure-stack-hci"></a>Schritt 1: Erwerb von Hardware für Trusted Enterprise Virtualization unter Azure Stack HCI
Zuerst müssen Sie Hardware beschaffen. Die einfachste Möglichkeit besteht darin, Ihren bevorzugten Microsoft-Hardwarepartner im [Azure Stack HCI-Katalog](https://hcicatalog.azurewebsites.net) zu suchen und ein integriertes System mit vorinstalliertem Azure Stack HCI-Betriebssystem zu kaufen. Im-Katalog können Sie filtern, um Herstellerhardware anzuzeigen, die für diese Art Workload optimiert ist.

Andernfalls müssen Sie das Azure Stack HCI-Betriebssystem auf Ihrer eigenen Hardware bereitstellen. Details zu den Azure Stack HCI-Bereitstellungsoptionen und zum Installieren von Windows Admin Center finden Sie unter [Bereitstellen des Azure Stack HCI-Betriebssystems](./operating-system.md).

Verwenden Sie als nächstes das Windows Admin Center, um [einen Azure Stack HCI-Cluster zu erstellen](./create-cluster.md).

Die gesamte Partnerhardware für Azure Stack HCI ist über die Hardware Assurance Additional Qualification (Zusatzqualifikation zur Hardwareversicherung) zertifiziert. Im Qualifikationsprozess wird die gesamte erforderliche [VBS](/windows-hardware/design/device-experiences/oem-vbs)-Funktionalität getestet. VBS und HVCI sind in Azure Stack HCI jedoch nicht automatisch aktiviert. Weitere Informationen zur Hardware Assurance Additional Qualification finden Sie unter „Hardware Assurance“ (Hardwareversicherung) unter **Systems** (Systeme) im [Windows Server-Katalog](https://www.windowsservercatalog.com/content.aspx?ctf=AQinfo-systems.htm#:~:text=Hardware%20Assurance%20Windows%20Server%20systems%20that%20are%20awarded,of%20Windows%20Server%2C%20starting%20with%20Windows%20Server%202016).

   >[!WARNING]
   > HVCI ist möglicherweise nicht mit Hardwaregeräten kompatibel, die nicht im Azure Stack HCI-Katalog aufgeführt sind. Wir empfehlen dringend, für eine Trusted Enterprise Virtualization-Infrastruktur Azure Stack HCI-validierte Hardware von unseren Partnern zu verwenden.

### <a name="step-2-enable-hvci"></a>Schritt 2: Aktivieren von HVCI
Aktivieren Sie HVCI auf Ihrer Serverhardware und den virtuellen Computern. Ausführliche Informationen dazu finden Sie unter [Enable virtualization-based protection of code integrity](/windows/security/threat-protection/device-guard/enable-virtualization-based-protection-of-code-integrity) (Aktivieren des virtualisierungsbasierten Schutzes der Codeintegrität).

### <a name="step-3-set-up-azure-security-center-in-windows-admin-center"></a>Schritt 3: Einrichten von Azure Security Center in Windows Admin Center
Richten Sie im Windows Admin Center [Azure Security Center](/azure/security-center/security-center-introduction) ein, mit dem Sie Bedrohungsschutz hinzufügen und den Sicherheitsstatus ihrer Workloads schnell bewerten können.

Mehr dazu erfahren Sie unter [Schützen von Windows Admin Center-Ressourcen mit Azure Security Center](/azure/security-center/windows-admin-center-integration).

Erste Schritte mit Security Center:
- Sie benötigen ein Microsoft Azure-Abonnement. Wenn Sie nicht über ein Abonnement verfügen, können Sie sich für ein [kostenloses Testabonnement](https://azure.microsoft.com/free) registrieren.
- Der Free-Tarif von Security Center ist für alle Ihre aktuellen Azure-Abonnements aktiviert, sobald Sie das Azure Security Center-Dashboard im Azure-Portal aufrufen oder es programmgesteuert über die API aktivieren.
Sie müssen Azure Defender aktivieren, um erweiterte Funktionen für die Sicherheitsverwaltung und Bedrohungserkennung nutzen zu können. Sie können Azure Defender 30 Tage lang kostenlos testen. Weitere Informationen finden Sie unter [Security Center – Preise](https://azure.microsoft.com/pricing/details/security-center).
- Wenn Sie Azure Defender jetzt aktivieren möchten, finden Sie unter [Schnellstart: Einrichten von Azure Security Center](/azure/security-center/security-center-get-started) die entsprechenden Schritte.

Sie können das Windows Admin Center ebenfalls verwenden, um zusätzliche Azure-Hybriddienste wie Sicherung, Dateisynchronisierung, Sitewiederherstellung, Punkt-zu-Standort-VPN, Updateverwaltung und Azure Monitor einzurichten.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur Trusted Enterprise Virtualization finden Sie unter:
- [Hyper-V unter Windows Server](/windows-server/virtualization/hyper-v/hyper-v-on-windows-server)