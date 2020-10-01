---
title: 'Azure Stack HCI: Sicherheitsaspekte'
description: Dieses Thema bietet Hilfestellung zu den Sicherheitsaspekten des Azure Stack HCI-Betriebssystems.
author: JohnCobb1
ms.author: v-johcob
ms.topic: conceptual
ms.date: 09/10/2020
ms.openlocfilehash: 181eb53d4b0e5c95065371e6b87e470a5e413d06
ms.sourcegitcommit: 69cfff119ab425d0fbb71e38d1480d051fc91216
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/30/2020
ms.locfileid: "91572669"
---
# <a name="azure-stack-hci-security-considerations"></a>Azure Stack HCI: Sicherheitsaspekte

>Gilt für: Azure Stack HCI, Version 20H2; Windows Server 2019

Dieses Thema behandelt Sicherheitsaspekte und -empfehlungen im Zusammenhang mit dem Azure Stack HCI-Betriebssystem:
- In Teil 1 werden grundlegende Sicherheitstools und -Technologien zum Härten des Betriebssystems und zum Schutz von Daten und Identitäten behandelt, um effizient eine sichere Grundlage für Ihr Unternehmen zu schaffen.
- Teil 2 behandelt Ressourcen, die über das Azure Security Center verfügbar sind.
- Teil 3 befasst sich mit erweiterten Sicherheitsaspekten, um den Sicherheitsstatus Ihrer Organisation in diesen Bereichen weiter zu erhöhen.

## <a name="why-are-security-considerations-important"></a>Warum sind Sicherheitsüberlegungen wichtig?
Sicherheit betrifft jeden in Ihrem Unternehmen, vom leitenden Management bis zum Information Worker. Unzureichende Sicherheit ist ein echtes Risiko für Unternehmen, da eine Sicherheitsverletzung das gesamte normale Geschäft beeinträchtigen und Ihr Unternehmen praktisch zum Stillstand bringen kann. Je eher Sie einen potenziellen Angriff erkennen können, desto schneller können Sie eventuelle Bedrohungen der Sicherheit bekämpfen.

Nach dem Erforschen der Schwachpunkte einer Umgebung, um sie auszunutzen, kann ein Angreifer normalerwiese innerhalb von 24 bis 48 Stunden nach der ersten Gefährdung seine Berechtigungen heraufstufen, um die Kontrolle über Systeme im Netzwerk zu übernehmen. Durch gute Sicherheitsmaßnahmen in der Umgebung wird der Zeitraum, den ein Angreifer benötigt, um die Kontrolle zu übernehmen, von Stunden auf Wochen oder sogar Monate verlängert, indem die Bewegungen des Angreifers blockiert werden. Das Implementieren der Sicherheitsempfehlungen in diesem Thema versetzt Ihr Unternehmen in die Lage, solche Angriffe so schnell wie möglich zu erkennen und auf sie zu reagieren.

## <a name="part-1-build-a-secure-foundation"></a>Teil 1: Aufbau einer sicheren Grundlage
In den folgenden Abschnitten werden Sicherheitstools und-Technologien empfohlen, um in Ihrer Umgebung eine sichere Grundlage für die Server zu schaffen, auf denen das Betriebssystem Azure Stack HCI ausgeführt wird.

### <a name="harden-the-environment"></a>Härten der Umgebung
In diesem Abschnitt wird der Schutz von Diensten und virtuellen Computern (VMs) erörtert, die unter dem Betriebssystem ausgeführt werden:
- Für **Azure Stack HCI zertifizierte Hardware** bietet vordefinierte, konsistente Einstellungen für Sicherer Start, UEFI und TPM. Die Kombination von virtualisierungsbasierter Sicherheit und zertifizierter Hardware unterstützt den Schutz von sicherheitsrelevanten Workloads. Darüber hinaus können Sie diese vertrauenswürdige Infrastruktur mit Azure Security Center verbinden, um Verhaltensanalyse und -berichte zu aktivieren, die Auskunft über schnell wechselnde Workloads und Bedrohungen geben.

    - *Sicherer Start* ist ein von der Computerindustrie entwickelter Sicherheitsstandard, mit dem erreicht werden soll, dass ein Gerät nur mit Software gestartet werden kann, die vom OEM (Original Equipment Manufacturer) als vertrauenswürdig angesehen wird. Weitere Informationen finden Sie unter [Sicherer Start](/windows-hardware/design/device-experiences/oem-secure-boot).
    - *UEFI (United Extensible Firmware Interface)* steuert den Startprozess des Servers und übergibt die Steuerung dann entweder an Windows oder an ein anderes Betriebssystem. Weitere Informationen finden Sie unter [UEFI-Firmware-Anforderungen](/windows-hardware/design/device-experiences/oem-uefi).
    - Die *TPM (Trusted Platform Module)* -Technologie stellt hardwarebasierte sicherheitsbezogene Funktionen zur Verfügung. Bei einem TPM-Chip handelt es sich um einen sicheren Kryptografieprozessor, der Kryptografieschlüssel generiert, speichert und ihre Verwendung einschränkt. Weitere Informationen finden Sie unter [Trusted Platform Module – Technologieübersicht](/windows/security/information-protection/tpm/trusted-platform-module-overview).

    Weitere Informationen zu zertifizierten Hardwareanbietern für Azure Stack HCI finden Sie auf der Website [Azure Stack HCI-Lösungen](https://azure.microsoft.com/products/azure-stack/hci/).

- **Device Guard-** und **Credential Guard**. Device Guard schützt vor Malware ohne bekannte Signatur, mit unsigniertem Code und Malware, die Zugriff auf den Kernel erlangt, um entweder vertrauliche Informationen zu erfassen oder das System zu beschädigen. Windows Defender Credential Guard verwendet auf Virtualisierung basierende Sicherheit, um Geheimnisse zu isolieren, sodass nur privilegierte Systemsoftware auf sie zugreifen kann.

    Weitere Informationen finden Sie unter [Verwalten von Windows Defender Credential Guard](/windows/security/identity-protection/credential-guard/credential-guard-manage) und indem Sie das [Hardware-Bereitschaftstool für Device Guard und Credential Guard](https://www.microsoft.com/download/details.aspx?id=53337) herunterladen.

- **Windows-** und **Firmware**-Updates auf Clustern, Servern (einschließlich Gast-VMs) und PCs sind unverzichtbar, um sicherzustellen, dass sowohl das Betriebssystem als auch die Systemhardware bestmöglich vor Angreifern geschützt sind. Sie können das **Updates**-Tool aus dem Windows Admin Center verwenden, um Updates auf einzelne Systeme anzuwenden. Wenn Ihr Hardwareanbieter Windows Admin Center-Unterstützung für das Abrufen von Treiber-, Firmware- und Lösungsupdates einbezieht, können Sie diese Updates zugleich mit Windows-Updates erhalten. Beziehen Sie sie andernfalls direkt von Ihrem Lieferanten.

    Weitere Informationen finden Sie unter [Aktualisieren des Clusters](../manage/update-cluster.md).

    Um Updates auf mehreren Clustern und Servern zugleich zu verwalten, erwägen Sie ein Abonnement des optionalen Azure-Updateverwaltungsdiensts, der in Windows Admin Center integriert ist. Weitere Informationen finden Sie unter [Azure-Updateverwaltung mithilfe von Windows Admin Center](https://www.thomasmaurer.ch/2018/11/azure-update-management-windows-admin-center).

### <a name="protect-data"></a>Schützen von Daten
In diesem Abschnitt wird erläutert, wie Sie Windows Admin Center verwenden, um Daten und Workloads im Betriebssystem zu schützen:

- **BitLocker für Speicherplätze** schützt ruhende Daten. Sie können BitLocker verwenden, um den Inhalt von Speicherplatz-Datenvolumes im Betriebssystem zu verschlüsseln. Die Verwendung von BitLocker zum Schützen von Daten kann Organisationen dabei helfen, die Einhaltung von behördlichen, regionalen und branchenspezifischen Standards wie z. B. FIPS 140-2 oder HIPAA sicherzustellen.
 
    Weitere Informationen zur Verwendung von BitLocker in Windows Admin Center finden Sie unter [Aktivieren der Volumeverschlüsselung, Deduplizierung und Komprimierung](../manage/volume-encryption-deduplication.md).

- Die **SMB**-Verschlüsselung für Windows-Netzwerke schützt Daten bei der Übertragung. *Server Message Block (SMB)* ist ein Protokoll, das der Freigabe von Netzwerkdateien dient und es Anwendungen auf einem Computer ermöglicht, Dateien zu lesen und in Dateien zu schreiben sowie Dienste von Serverprogrammen in einem Computernetzwerk anzufordern.

    Informationen zum Aktivieren der SMB-Verschlüsselung finden Sie unter [SMB security enhancements](/windows-server/storage/file-server/smb-security) (SMB-Sicherheitsverbesserungen).

- **Windows Defender Antivirus** in Windows Admin Center schützt das Betriebssystem auf Clients und Servern vor Viren, Malware, Spyware und weiteren Bedrohungen. Weitere Informationen finden Sie unter [Microsoft Defender Antivirus in Windows Server 2016 und 2019](/windows/security/threat-protection/microsoft-defender-antivirus/microsoft-defender-antivirus-on-windows-server-2016).

### <a name="protect-identities"></a>Schutz von Identitäten
In diesem Abschnitt wird erläutert, wie Sie das Windows Admin Center zum Schützen privilegierter Identitäten verwenden:

- **Zugriffssteuerung** kann die Sicherheit Ihrer Managementlandschaft verbessern. Wenn Sie einen Windows Admin Center-Server (im Gegensatz zur Ausführung auf einem Windows 10-PC) verwenden, können Sie den Zugriff auf Windows Admin Center auf zwei Ebenen verwalten: für Gatewaybenutzer und für Gatewayadministratoren. Zu den Optionen für den Gatewayadministrator-Identitätsanbieter gehören:
    - Active Directory- oder lokale Computergruppen zum Durchsetzen von Smartcard-Authentifizierung.
    - Azure Active Directory zum Durchsetzen von bedingtem Zugriff und mehrstufiger Authentifizierung.
 
    Weitere Informationen finden Sie unter [User access options with Windows Admin Center](/windows-server/manage/windows-admin-center/plan/user-access-options) (Optionen für den Benutzerzugriff in Windows Admin Center) und [Configure User Access Control and Permissions](/windows-server/manage/windows-admin-center/configure/user-access-control) (Konfigurieren von Benutzerzugriffssteuerung und Berechtigungen).

- **Browserverkehr** mit dem Windows Admin Center verwendet HTTPS. Datenverkehr vom Windows Admin Center mit verwalteten Servern verwendet standardmäßig PowerShell und Windows Management Instrumentation (WMI) anstelle von Windows Remote Management (WinRM). Windows Admin Center unterstützt die Local Administrator Password Solution (LAPS), die ressourcenbasierte eingeschränkte Delegierung, Gateway-Zugriffssteuerung mithilfe von Active Directory (AD) oder Microsoft Azure Active Directory (Azure AD), und rollenbasierte Zugriffssteuerung (RBAC) für die Verwaltung von Zielservern.

    Windows Admin Center unterstützt Microsoft Edge (Windows 10, Version 1709 oder höher), Google Chrome und Microsoft Edge Insider unter Windows 10. Sie können Windows Admin Center wahlweise auf einem Windows 10-PC oder einem Windows-Server installieren.

    Wenn Sie Windows Admin Center auf einem Server installieren, wird es als Gateway ausgeführt, ohne Benutzeroberfläche auf dem Hostserver. In diesem Szenario können sich Administratoren mithilfe einer HTTPS-Sitzung beim Server anmelden, die durch ein selbstsigniertes Sicherheitszertifikat auf dem Host geschützt ist. Es ist jedoch besser, ein angemessenes SSL-Zertifikat von einer vertrauenswürdigen Zertifizierungsstelle für den Anmeldevorgang zu verwenden, da die unterstützten Browser eine selbstsignierte Verbindung als unsicher behandeln, selbst wenn die Verbindung über ein vertrauenswürdiges VPN zu einer lokalen IP-Adresse erfolgt.

    Mehr über Installationsoptionen für Ihre Organisation erfahren Sie unter [What type of installation is right for you?](/windows-server/manage/windows-admin-center/plan/installation-options) (Welche Installationsart ist für Sie die richtige?).

- **CredSSP** ist ein Authentifizierungsanbieter, den Windows Admin Center in einigen Fällen verwendet, um Anmeldeinformationen jenseits des spezifischen Servers, der Ihr Verwaltungsziel darstellt, an Computer zu übergeben. Windows Admin Center schreibt CredSSP aktuell für diese Vorgänge vor:
    - Erstellen eines neuen Clusters.
    - Zugriff auf das Tool **Updates**, um die Funktionen zum Failoverclustering oder zum clusterfähigen Aktualisieren zu verwenden.
    - Verwenden von zerteiltem SMB-Speicher in VMs.

    Weitere Informationen finden Sie unter [Does Windows Admin Center use CredSSP?](/windows-server/manage/windows-admin-center/understand/faq#does-windows-admin-center-use-credssp) (Wird CredSSP von Windows Admin Center verwendet?)

- Die **rollenbasierte Zugriffssteuerung (RBAC)** in Windows Admin Center gestattet Benutzern den eingeschränkten Zugriff auf die Server, die sie verwalten müssen, statt sie zu lokalen Administratoren mit vollen Berechtigungen zu machen. Zum Verwenden von RBAC in Windows Admin Center konfigurieren Sie jeden verwalteten Server mit einem PowerShell Just Enough Administration-Endpunkt.

    Weitere Informationen finden Sie unter [Rollenbasierte Zugriffssteuerung](/windows-server/manage/windows-admin-center/plan/user-access-options#role-based-access-control) und [Just Enough Administration](/powershell/scripting/learn/remoting/jea/overview).

- Zu den **Sicherheitstools** in Windows Admin Center, die Sie zum Verwalten und Schützen von Identitäten verwenden können, zählen Active Directory, Zertifikate, Firewall, Lokale Benutzer und Gruppen und weitere.

    Weitere Informationen dazu finden Sie unter [Verwalten von Servern mit Windows Admin Center](/windows-server/manage/windows-admin-center/use/manage-servers).

## <a name="part-2-use-azure-security-center"></a>Teil 2: Verwenden des Azure Security Centers
*Azure Security Center* ist ein vereinheitlichtes Sicherheitsverwaltungssystem für Infrastrukturen, mit dem der Sicherheitsstatus Ihrer Rechenzentren gestärkt wird und ein erweiterter Schutz vor Bedrohungen für Ihre Hybridworkloads in der Cloud und in der lokalen Umgebung bereitgestellt wird. Security Center stellt Ihnen Tools zum Bewerten des Sicherheitsstatus Ihres Netzwerks, Schützen von Workloads, Auslösen von Sicherheitswarnungen und Befolgen spezifischer Empfehlungen zum Entschärfen von Angriffen und Vorbeugen gegen künftige Bedrohungen zur Verfügung. Security Center führt alle diese Dienste mit hoher Geschwindigkeit in der Cloud aus und verursacht durch automatische Bereitstellung und Schutz mithilfe von Azure-Diensten keinen Mehraufwand für die Bereitstellung.

Security Center schützt VMs durch Installieren der Protokollanalyse auf den betroffenen Ressourcen sowohl für Windows-Server als auch für Linux-Server. Azure korreliert vom Agent erfasste Ereignisse zu Empfehlungen (Härtungsaufgaben), die Sie ausführen, um Ihre Workloads sicher zu machen. Die auf bewährten Sicherheitsmethoden basierenden Härtungsaufgaben umfassen das Verwalten und Durchsetzen von Sicherheitsrichtlinien. Anschließend können Sie die Ergebnisse nachverfolgen und die Compliance und Governance im zeitlichen Verlauf durch Überwachung im Security Center verwalten, während Sie zugleich die Angriffsfläche für alle Ressourcen verringern.

Die Verwaltung der Personen, die auf Ihre Azure-Ressourcen und -Abonnements zugreifen können, ist ein wichtiger Bestandteil Ihrer Azure-Governancestrategie. Die rollenbasierte Zugriffssteuerung in Azure (Role-Based Access Control, RBAC) ist das Hauptverfahren zum Verwalten des Zugriffs in Azure. Weitere Informationen dazu finden Sie unter [Verwalten des Zugriffs auf Ihre Azure-Umgebung per rollenbasierter Zugriffssteuerung](/azure/cloud-adoption-framework/ready/azure-setup-guide/manage-access).

Für das Arbeiten mit Security Center über das Windows Admin Center ist ein Azure-Abonnement erforderlich. Informationen zum Einstieg finden Sie unter [Integrieren von Azure Security Center mit Windows Admin Center](/azure/security-center/windows-admin-center-integration).

Greifen Sie nach der Registrierung in Windows Admin Center auf das Security Center zu: Wählen Sie auf der Seite **Alle Verbindungen** einen Server oder eine VM aus, wählen Sie unter **Extras** **Azure Security Center** und dann **Anmelden bei Azure** aus.

Weitere Informationen finden Sie unter [Was ist Azure Security Center?](/azure/security-center/security-center-intro)

## <a name="part-3-add-advanced-security"></a>Teil 3: Hinzufügen erweiterter Sicherheit
In den folgenden Abschnitten werden erweiterte Sicherheitstools und-Technologien empfohlen, um Server in Ihrer Umgebung, auf denen das Betriebssystem Azure Stack HCI ausgeführt wird, weiter zu härten.

### <a name="harden-the-environment"></a>Härten der Umgebung
- **Microsoft-Sicherheitsbaselinse** beruhen auf Sicherheitsempfehlungen von Microsoft, die aus der Partnerschaft mit Handelsorganisationen und der US-Regierung stammen, beispielsweise dem Verteidigungsministerium. Die Sicherheitsbaselines umfassen empfohlene Sicherheitseinstellungen für Windows Firewall, Windows Defender und viele andere.

    Die Sicherheitsbaselines stehen in Form von Sicherungen von Gruppenrichtlinien-Objekten (Group Policy Object, GPO) zur Verfügung, die Sie in Active Directory Domain Services (AD DS) importieren und dann auf in die Domäne eingebundenen Servern bereitstellen können, um die Umgebung zu härten. Darüber hinaus können Sie Tools für lokale Skripts verwenden, um eigenständige (nicht mit der Domäne verbundene) Server mit Sicherheitsbaselines zu konfigurieren. Zum Einstieg in die Sicherheitsbaselines laden Sie das [Microsoft Security Compliance Toolkit 1.0](https://www.microsoft.com/download/details.aspx?id=55319) herunter.

    Weitere Informationen finden Sie unter [Microsoft-Sicherheitsbaselines](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/bg-p/Microsoft-Security-Baselines).

### <a name="protect-data"></a>Schützen von Daten
- Die **Härtung der Hyper-V-Umgebung** erfordert die Härtung von Windows Server bei der Ausführung auf einem virtuellen Computer ebenso, wie das bei einem auf einem physischen Server ausgeführten Betriebssystem erforderlich ist. Da sich in virtuellen Umgebungen normalerweise mehrere VMs den gleichen physischen Host teilen, ist es unverzichtbar, sowohl den physischen Host als auch die virtuellen Computer zu schützen, die auf ihm ausgeführt werden. Ein Angreifer, der einen Host schädigt, kann mehrere virtuelle Computer mit entsprechend größerem Einfluss auf Workloads und Dienste beeinträchtigen. In diesem Abschnitt werden die folgenden Methoden erörtert, die Sie verwenden können, um Windows Server in einer Hyper-V-Umgebung zu härten:

    - **Geschützte Fabric und abgeschirmte virtuelle Computer** stärken die Sicherheit für VMs, die in Hyper-V-Umgebungen ausgeführt werden, indem Angreifer daran gehindert werden, Dateien auf virtuellen Computern zu ändern. Eine *geschützte Fabric* besteht aus einem Hostüberwachungsdienst (Host Guardian Service, HGS), bei dem es sich normalerweise um einen aus drei Knoten bestehenden Cluster, einen oder mehrere geschützte Hosts und eine Reihe abgeschirmter VMs handelt. Der Nachweisdienst bewertet die Gültigkeit von Hostanforderungen, während der Schlüsselschutzdienst bestimmt, ob Schlüssel freigegeben werden, die von den geschützten Hosts zum Starten der abgeschirmten VM verwendet werden können.

        Weitere Informationen finden Sie unter [Guarded fabric and shielded VMs overview](/windows-server/security/guarded-fabric-shielded-vm/guarded-fabric-and-shielded-vms) (Übersicht zu geschützter Fabric und abgeschirmten VMs).
     
     - Das **virtuelle Trusted Platform Module (vTPM)** in Windows Server unterstützt TPM für VMs, mit dessen Hilfe Sie erweiterte Sicherheitstechnologien wie BitLocker in VMs einsetzen können. TPM-Unterstützung lässt sich auf jeder Hyper-V-VM der 2. Generation aktivieren, entweder mithilfe des Hyper-V-Managers oder des Windows PowerShell-Cmdlets `Enable-VMTPM`.
     
        Weitere Informationen finden Sie unter [Enable-VMTPM](/powershell/module/hyper-v/enable-vmtpm).
     
     - **Software Defined Networking (SDN)** in Azure Stack HCI und Windows Server konfiguriert und verwaltet physische und virtuelle Netzwerkgeräte wie Router, Switches und Gateways in Ihrem Rechenzentrum zentral. Virtuelle Netzwerkelemente, wie etwa der virtuelle Hyper-V-Switch, die Hyper-V-Netzwerkvirtualisierung und das RAS Gateway, wurden als integrale Elemente Ihrer SDN-Infrastruktur konzipiert.

        Weitere Informationen dazu finden Sie unter [Softwaredefiniertes Netzwerk (SDN)](/windows-server/networking/sdn/).

### <a name="protect-identities"></a>Schutz von Identitäten
- Die **Local Administrator Password Solution (LAPS)** ist ein schlanker Mechanismus für Mitgliedssysteme einer Active Directory-Domäne, der die Kennwörter aller lokalen Administratorkonten in regelmäßigen Abständen auf einen neuen, zufälligen und eindeutigen Wert festlegt. Kennwörter werden in einem geschützten vertraulichen Attribut im entsprechenden Computerobjekt in Active Directory gespeichert, wo sie nur von speziell autorisierten Benutzern abgerufen werden können. LAPS verwendet lokale Konten für die Remoteverwaltung von Computern auf eine Weise, die einige Vorteile gegenüber Domänenkonten bietet. Weitere Informationen finden Sie unter [Remoteverwendung lokaler Konten: LAPS ändert alles](/archive/blogs/secguide/remote-use-of-local-accounts-laps-changes-everything).

    Zum Einstieg in die Verwendung von LAPS laden Sie [Local Administrator Password Solution (LAPS)](https://www.microsoft.com/download/details.aspx?id=46899) herunter.

- **Microsoft Advanced Threat Analytics (ATA)** ist ein lokal eingesetztes Produkt, das Sie verwenden können, um das Erkennen von Angriffsversuchen auf privilegierte Identitäten zu unterstützen. ATA analysiert den Netzwerkdatenverkehr auf Protokolle, die Authentifizierungs-, Autorisierungs- und Informationsdaten sammeln, wie etwa Kerberos und DNS. ATA verwendet die Daten zum Erstellen von Verhaltensprofilen von Benutzern und anderen Entitäten im Netzwerk, um Anomalien und bekannte Angriffsmuster zu erkennen.
    
    Weitere Informationen dazu finden Sie unter [Was ist Advanced Threat Analytics?](/advanced-threat-analytics/what-is-ata).

- **Windows Defender Remote Credential Guard** schützt Anmeldeinformationen über eine Remotedesktopverbindung durch Umleiten von Kerberos-Anforderungen zurück an das Gerät, das die Verbindung anfordert. Es stellt außerdem SSO (Single Sign-On, einmaliges Anmelden) für Remotedesktopsitzungen zur Verfügung. Wenn das Zielgerät während einer Remotedesktopsitzung beschädigt wird, werden Ihre Anmeldeinformationen nicht offengelegt, da weder Anmeldeinformationen noch Ableitungen daraus jemals über das Netzwerk an das Zielgerät übergeben werden.

    Weitere Informationen finden Sie unter [Verwalten von Windows Defender Credential Guard](/windows/security/identity-protection/credential-guard/credential-guard-manage).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur Sicherheit und der Einhaltung gesetzlicher Bestimmungen finden Sie außerdem unter:
- [Sicherheit und Gewissheit](/windows-server/security/security-and-assurance)
- [Bewährte Methoden für die Sicherheit von Azure-Lösungen](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/)
