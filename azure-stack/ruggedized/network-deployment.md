---
title: Azure Stack-Netzwerkbereitstellung für Azure Stack Hub Ruggedized
description: Informieren Sie sich über die Azure Stack-Netzwerkbereitstellung für das Azure Stack Hub Ruggedized-Gerät.
author: PatAltimore
ms.service: azure-stack
ms.topic: conceptual
ms.date: 10/14/2020
ms.author: patricka
ms.reviewer: shisab
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: df20e3f9ad04959bf2c85d8912644819cae09ba1
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97867688"
---
# <a name="azure-stack-hub-ruggedized-network-deployment"></a>Netzwerkbereitstellung für Azure Stack Hub Ruggedized

In diesem Thema werden die Zugriffsberechtigung für die ToR-Switches, IP-Adresszuweisungen und andere Netzwerkbereitstellungsaufgaben behandelt. 

## <a name="plan-configuration-deployment"></a>Planen der Konfigurationsbereitstellung

In den nächsten Abschnitten werden Berechtigungen und IP-Adresszuweisungen behandelt.

### <a name="physical-switch-access-control-list"></a>Zugriffssteuerungsliste für den physischen Switch

Zum Schutz der Azure Stack-Lösung haben wir auf den ToR-Switches Zugriffssteuerungslisten (Access Control Lists, ACLs) implementiert. In diesem Abschnitt wird die Implementierung dieser Sicherheit beschrieben. Die folgende Tabelle enthält die Quellen und Ziele jedes Netzwerks innerhalb der Azure Stack-Lösung:

[![Diagramm der Zugriffssteuerungslisten für die ToR-Switches](media/network-deployment/acls.png)](media/network-deployment/acls.png#lightbox)

In der folgenden Tabelle werden die Verweisbezeichnungen aus den ACLs den Azure Stack-Netzwerken gegenübergestellt.

| BMC Mgmt                                   | IP-Adressen von Bereitstellungs-VM, BMC-Schnittstelle, HLH-Server, NTP-Server und DNS-Server sind auf Grundlage von Protokoll und Port als zulässig enthalten. |
|--------------------------------------------|----------------------------------------------------------------------------------------------------------------------------|
| HLH Internal Accessible (PDU)              | Datenverkehr ist auf den BMC-Switch beschränkt                                                                                           |
| HLH External Accessible (OEM Tool VM)      | Die ACL ermöglicht den Zugriff auf über das Grenzgerät hinaus.                                                                             |
| Switch Mgmt                                | Dedizierte Verwaltungsschnittstellen für den Switch                                                                                    |
| Spine Mgmt                                 | Dedizierte Verwaltungsschnittstellen für Spine                                                                                     |
| Azure Stack                                | Azure Stack-Infrastrukturdienste und VMs, eingeschränktes Netzwerk                                                           |
| Infrastruktur                             |                                                                                                                            |
| Azure Stack                                | Geschützter Azure Stack-Endpunkt, Konsolenserver für die Notfallwiederherstellung                                                          |
| Infrastruktur                             |                                                                                                                            |
| Public (PEP/ERCS)                          |                                                                                                                            |
| Tor1, Tor2 RouterIP                         | Die Loopback-Schnittstelle am Switch für das BGP-Peering zwischen SLB und Switch/Router.                                   |
| Storage                                    | Private IP-Adressen, die nicht außerhalb der Region weitergeleitet werden                                                                              |
| Internal VIPs                              | Private IP-Adressen, die nicht außerhalb der Region weitergeleitet werden                                                                              |
| Public-VIPs                                | Vom Netzwerkcontroller verwalteter Netzwerkadressraum des Mandanten.                                                            |
| Public-Admin-VIPs                          | Kleine Teilmenge der Adressen im Mandantenpool, die für die Kommunikation mit internen VIPs und der Azure Stack-Infrastruktur erforderlich sind     |
| Kunde/Internet                          | Vom Kunden definiertes Netzwerk. Aus Sicht von Azure Stack ist „0.0.0.0“ das Grenzgerät.                                |
| 0.0.0.0                                    |                                                                                                                            |
| **Deny** (Verweigern)                                     | Der Kunde hat die Möglichkeit, dieses Feld so zu aktualisieren, dass zusätzliche Netzwerke zugelassen werden, um Verwaltungsfunktionen zu aktivieren.             |
| **Permit**                                 | Das Zulassen des Datenverkehrs ist aktiviert, aber SSH-Zugriff ist standardmäßig deaktiviert. Der Kunde hat die Möglichkeit, den SSH-Dienst zu aktivieren.         |
| **No Route**                                   | Routen werden nicht außerhalb der Azure Stack-Umgebung weitergegeben.                                                          |
| **MUX ACL**                                    | Azure Stack-MUX-ACLs werden verwendet.                                                                                        |
| **N/V**                                        | Nicht Teil einer VLAN-ACL.                                                                                                  |

### <a name="ip-address-assignments"></a>IP-Adresszuweisungen

Sie werden im Bereitstellungsarbeitsblatt aufgefordert, die folgenden Netzwerkadressen anzugeben, um den Azure Stack-Bereitstellungsprozess zu unterstützen. Das Bereitstellungsteam verwendet das Bereitstellungsarbeitsblatt, um die IP-Netzwerke in die kleineren vom System benötigten Netzwerke aufzuteilen. Ausführliche Beschreibungen zu den einzelnen Netzwerken finden Sie im Abschnitt „Netzwerkentwurf und -infrastruktur“ weiter oben.

In diesem Beispiel füllen Sie die Registerkarte „Netzwerkeinstellungen“ des Bereitstellungsarbeitsblatts mit den folgenden Werten aus:

-   BMC-Netzwerk: 10.193.132.0 /27

-   Privates Netzwerk für den Netzwerkspeicher und interne VIPs: 11.11.128.0 /24

-   Infrastrukturnetzwerk: 12.193.130.0 /24

-   Öffentliche virtuelle IP-Adresse (VIP) des Netzwerks: 13.200.132.0 /24

-   Switchinfrastrukturnetzwerk: 10.193.132.128 /26

Wenn Sie die Funktion „Generieren“ im Bereitstellungsarbeitsblatt ausführen, werden in der Tabelle zwei neue Registerkarten erstellt. Die erste Registerkarte enthält die Subnetzzusammenfassung und zeigt, wie die übergeordneten Netzwerke aufgeteilt wurden, um alle vom System benötigten Netzwerke zu erstellen. In Beispiel unten enthält diese Registerkarte nur einen Teil der Spalten. Das tatsächliche Ergebnis enthält mehr Details zu jedem aufgelisteten Netzwerk:

| **Rack** | **Subnetztyp** | **Name**                                   | **IPv4-Subnetz**   | **IPv4-Adressen** |
|----------|-----------------|--------------------------------------------|-------------------|--------------------|
| Rahmen   | P2P-Link        | P2P_Border/Border1_To_Rack1/TOR1           | 10.193.132.128/30 | 4                  |
| Rahmen   | P2P-Link        | P2P_Border/Border1_To_Rack1/TOR2           | 10.193.132.132/30 | 4                  |
| Rahmen   | P2P-Link        | P2P_Border/Border2_To_Rack1/TOR1           | 10.193.132.136/30 | 4                  |
| Rahmen   | P2P-Link        | P2P_Border/Border2_To_Rack1/TOR2           | 10.193.132.140/30 | 4                  |
| Rahmen   | P2P-Link        | P2P_Rack1/TOR1_To_Rack1/BMC                | 10.193.132.144/30 | 4                  |
| Rahmen   | P2P-Link        | P2P_Rack1/TOR2_To_Rack1/BMC                | 10.193.132.148/30 | 4                  |
| Rack1    | Loopback        | Loopback0_Rack1_TOR1                       | 10.193.132.152/32 | 1                  |
| Rack1    | Loopback        | Loopback0_Rack1_TOR2                       | 10.193.132.153/32 | 1                  |
| Rack1    | Loopback        | Loopback0_Rack1_BMC                        | 10.193.132.154/32 | 1                  |
| Rack1    | P2P-Link        | P2P_Rack1/TOR1-ibgp-1_To_Rack1/TOR2-ibgp-1 | 10.193.132.156/30 | 4                  |
| Rack1    | P2P-Link        | P2P_Rack1/TOR1-ibgp-2_To_Rack1/TOR2-ibgp-2 | 10.193.132.160/30 | 4                  |
| Rack1    | VLAN            | BMCMgmt                                    | 10.193.132.0/27   | 32                 |
| Rack1    | VLAN            | SwitchMgmt                                 | 10.193.132.168/29 | 8                  |
| Rack1    | VLAN            | CL01-RG01-SU01-Storage                     | 11.11.128.0/25    | 128                |
| Rack1    | VLAN            | CL01-RG01-SU01-Infra                       | 12.193.130.0/24   | 256                |
| Rack1    | Sonstiges           | CL01-RG01-SU01-VIPS                        | 13.200.132.0/24   | 256                |
| Rack1    | Sonstiges           | CL01-RG01-SU01-InternalVIPS                | 11.11.128.128/25  | 128                |

Die zweite Registerkarte mit der **IP-Adressnutzung** zeigt, wie die IP-Adressen verwendet werden:

#### <a name="bmc-network"></a>BMC-Netzwerk

Für das übergeordnete Netzwerk des BMC-Netzwerks ist mindestens ein Netzwerk der Größe /26 erforderlich. Das Gateway verwendet die erste IP-Adresse im Netzwerk, gefolgt von den BMC-Geräten im Rack. Dem HLH (Hardware Lifecycle Host) sind mehrere Adressen in diesem Netzwerk zugewiesen, die zum Bereitstellen, Überwachen und Warten des Racks verwendet werden können. Diese IP-Adressen sind in drei Gruppen unterteilt: DVM, InternalAccessible und ExternalAccessible.

- Rack: Rack1        
- Name: BMCMgmt      

| **Zugewiesen zu**      | **IPv4-Adresse** |
|----------------------|------------------|
| Netzwerk              | 10.193.132.0     |
| Gateway              | 10.193.132.1     |
| HLH-BMC              | 10.193.132.2     |
| AzS-Node01           | 10.193.132.3     |
| AzS-Node02           | 10.193.132.4     |
| AzS-Node03           | 10.193.132.5     |
| AzS-Node04           | 10.193.132.6     |
| ExternalAccessible-1 | 10.193.132.19    |
| ExternalAccessible-2 | 10.193.132.20    |
| ExternalAccessible-3 | 10.193.132.21    |
| ExternalAccessible-4 | 10.193.132.22    |
| ExternalAccessible-5 | 10.193.132.23    |
| InternalAccessible-1 | 10.193.132.24    |
| InternalAccessible-2 | 10.193.132.25    |
| InternalAccessible-3 | 10.193.132.26    |
| InternalAccessible-4 | 10.193.132.27    |
| InternalAccessible-5 | 10.193.132.28    |
| CL01-RG01-SU01-DVM00 | 10.193.132.29    |
| HLH-OS               | 10.193.132.30    |
| Übertragen            | 10.193.132.31    |

#### <a name="storage-network"></a>Speichernetzwerk

Das Speichernetzwerk ist ein privates Netzwerk, das nicht über das Rack hinaus weitergeleitet werden sollte. Es umfasst die erste Hälfte des übergeordneten privaten Netzwerks und wird vom Switch wie in der folgenden Tabelle gezeigt verteilt. Der Gateway entspricht der ersten IP-Adresse im Subnetz. Die zweite Hälfte wird für die internen VIPs verwendet. Sie bildet einen privaten Adresspool, der vom Azure Stack-SLB verwaltet und daher auf der Registerkarte mit der IP-Adressnutzung nicht angezeigt wird. Diese Netzwerke dienen zur Unterstützung von Azure Stack. ACLs auf den ToR-Switches verhindern, dass diese Netzwerke angekündigt werden oder dass von außerhalb der Lösung darauf zugegriffen werden kann.

- Rack: Rack1               
- Name: CL01-RG01-SU01-Storage 

| **Zugewiesen zu**              | **IPv4-Adresse** |
|------------------------------|------------------|
| Netzwerk                      | 11.11.128.0      |
| Gateway                      | 11.11.128.1      |
| TOR1                         | 11.11.128.2      |
| TOR2                         | 11.11.128.3      |
| Übertragen                    | 11.11.128.127    |

#### <a name="azure-stack-infrastructure-network"></a>Azure Stack-Infrastrukturnetzwerk

Das übergeordnete Infrastrukturnetzwerk erfordert ein Netzwerk der Größe /24, das auch nach dem Ausführen des Bereitstellungsarbeitsblatts die Größe /24 beibehält. Das Gateway erhält die erste IP-Adresse im Subnetz.

- Rack: Rack1 
- Name: CL01-RG01-SU01-Infra 

| **Zugewiesen zu**            | **IPv4-Adresse** |
|----------------------------|------------------|
| Netzwerk                    | 12.193.130.0     |
| Gateway                    | 12.193.130.1     |
| TOR1                       | 12.193.130.2     |
| TOR2                       | 12.193.130.3     |
| Übertragen                  | 12.193.130.255   |

#### <a name="switch-infrastructure-network"></a>Switchinfrastrukturnetzwerk

Das Infrastrukturnetzwerk ist in mehrere Netzwerke aufgeteilt, die von der physischen Switchinfrastruktur verwendet werden. Diese unterscheidet sich von der Azure Stack-Infrastruktur, die nur die Azure Stack-Software unterstützt. Das Netzwerk für die Switchinfrastruktur unterstützt ausschließlich die physische Switchinfrastruktur. Folgende Netzwerke werden von der Infrastruktur unterstützt:

| **Name**                                   | **IPv4-Subnetz**   |
|--------------------------------------------|-------------------|
| P2P_Border/Border1_To_Rack1/TOR1           | 10.193.132.128/30 |
| P2P_Border/Border1_To_Rack1/TOR2           | 10.193.132.132/30 |
| P2P_Border/Border2_To_Rack1/TOR1           | 10.193.132.136/30 |
| P2P_Border/Border2_To_Rack1/TOR2           | 10.193.132.140/30 |
| P2P_Rack1/TOR1_To_Rack1/BMC                | 10.193.132.144/30 |
| P2P_Rack1/TOR2_To_Rack1/BMC                | 10.193.132.148/30 |
| Loopback0_Rack1_TOR1                       | 10.193.132.152/32 |
| Loopback0_Rack1_TOR2                       | 10.193.132.153/32 |
| Loopback0_Rack1_BMC                        | 10.193.132.154/32 |
| P2P_Rack1/TOR1-ibgp-1_To_Rack1/TOR2-ibgp-1 | 10.193.132.156/30 |
| P2P_Rack1/TOR1-ibgp-2_To_Rack1/TOR2-ibgp-2 | 10.193.132.160/30 |
| SwitchMgmt                                 | 10.193.132.168/29 |
|                                            |                   |

-   Point-to-Point (P2P): Diese Netzwerke ermöglichen Konnektivität zwischen allen Switches. Die Subnetzgröße lautet für jede P2P-Verbindung /30. Die niedrigste IP-Adresse wird immer dem Upstreamgerät (Norden) im Stapel zugewiesen.

-   Loopback: Bei diesen Adressen handelt es sich um Netzwerke der Größe /32, die jedem im Rack verwendeten Switch zugewiesen werden. Den Grenzgeräten wird kein Loopback zugewiesen, da sie keinen Teil der Azure Stack-Lösung darstellen.

-   Switchverwaltung (Switch Mgmt): Dieses Netzwerk der Größe /29 unterstützt die dedizierten Verwaltungsschnittstellen der Switches im Rack. Die IP-Adressen werden wie in der folgenden Tabelle gezeigt zugewiesen. Sie finden diese Tabelle auch auf der Registerkarte zur IP-Adressnutzung im Bereitstellungsarbeitsblatt:

- Rack: Rack1    
- Name: SwitchMgmt 

| **Zugewiesen zu**  | **IPv4-Adresse** |
|------------------|------------------|
| Netzwerk          | 10.193.132.168   |
| Gateway          | 10.193.132.169   |
| TOR1             | 10.193.132.170   |
| TOR2             | 10.193.132.171   |
| Übertragen        | 10.193.132.175   |

## <a name="prepare-environment"></a>Vorbereiten der Umgebung

Das HLH-Image (Hardware Lifecycle Host) enthält den erforderlichen Linux-Container zum Generieren der Konfiguration für den physischen Netzwerkswitch.

Das aktuelle Toolkit für Partnerbereitstellungen umfasst das neueste Containerimage.
Das Containerimage auf dem HLH (Hardware Lifecycle Host) kann ersetzt werden, wenn eine aktualisierte Switchkonfiguration generiert werden soll.

Im Folgenden finden Sie die Schritte zum Aktualisieren des Containerimages:

1.  Herunterladen des Containerimages

2.  Ersetzen des Containerimages am folgenden Speicherort

## <a name="generate-configuration"></a>Generieren der Konfiguration

Im Folgenden finden Sie die Schritte zum Erstellen der JSON-Dateien und der Konfigurationsdateien für den Netzwerkswitch:

1.  Öffnen des Bereitstellungsarbeitsblatts

2.  Ausfüllen aller erforderlichen Felder auf allen Registerkarten

3.  Aufrufen der Funktion „Generieren“ im Bereitstellungsarbeitsblatt.  
    Es werden zwei zusätzliche Registerkarten erstellt, auf denen die generierten IP-Subnetze und -Zuweisungen angezeigt werden.

4.  Überprüfen der Daten und nach der Bestätigung Aufrufen der Funktion „Exportieren“.  
    Sie werden aufgefordert, einen Ordner anzugeben, in dem die JSON-Dateien gespeichert werden.

5.  Ausführen des Containers mithilfe von „Invoke-SwitchConfigGenerator.ps1“. Für dieses Skript muss eine PowerShell-Konsole mit erhöhten Rechten ausgeführt werden. Außerdem müssen bei der Ausführung die folgenden Parameter angegeben werden.

    -   ContainerName: Name des Containers, der die Switchkonfigurationen generiert

    -   ConfigurationData: Pfad zur Datei „ConfigurationData.json“, die aus dem Bereitstellungsarbeitsblatt exportiert wurde

    -   OutputDirectory: Pfad zum Ausgabeverzeichnis

    -   Offline: signalisiert, dass das Skript im Offlinemodus ausgeführt wird

    ```powershell
    C:\\WINDOWS\\system32\> .\\Invoke-SwitchConfigGenerate.ps1 -ContainerName generalonrampacr.azurecr.io/master -ConfigurationData .\\ConfigurationData.json -OutputDirectory c:\\temp -Offline
    ```

Wenn das Skript abgeschlossen wurde, erstellt es eine ZIP-Datei mit dem im Arbeitsblatt verwendeten Präfix. 

```console
C:\WINDOWS\system32> .\Invoke-SwitchConfigGenerate.ps1 -ContainerName generalonrampacr.azurecr.io/master -ConfigurationData .\ConfigurationData.json -OutputDirectory c:\temp -Offline                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         
Seconds : 2
Section : Validation
Step    : WindowsRequirement
Status  : True
Detail  : @{CurrentImage=10.0.18363.0}


Seconds : 2
Section : Validation
Step    : DockerService
Status  : True
Detail  : @{Status=Running}


Seconds : 9
Section : Validation
Step    : DockerSetup
Status  : True
Detail  : @{CPU=4; Memory=4139085824; OS=Docker Desktop; OSType=linux}


Seconds : 9
Section : Validation
Step    : DockerImage
Status  : True
Detail  : @{Container=generalonrampacr.azurecr.io/master:1.1910.78.1}


Seconds : 10
Section : Run
Step    : Container
Status  : True
Detail  : @{ID=2a20ba622ef9f58f9bcd069c3b9af7ec076bae36f12c5653f9469b988c01706c; ExternalPort=32768}


Seconds : 38
Section : Generate
Step    : Config
Status  : True
Detail  : @{OutputFile=c:\temp\N22R19.zip}


Seconds : 38
Section : Exit
Step    : StopContainer
Status  : True
Detail  : @{ID=2a20ba622ef9f58f9bcd069c3b9af7ec076bae36f12c5653f9469b988c01706c}
```

### <a name="custom-configuration"></a>Benutzerdefinierte Konfiguration

Sie können einige Umgebungseinstellungen für Ihre Azure Stack-Switchkonfiguration ändern. Sie können ermitteln, welche der Einstellungen Sie in der Vorlage ändern können. In diesem Artikel werden die einzelnen anpassbaren Einstellungen beschrieben. Zudem wird erläutert, wie sich die Änderungen auf Azure Stack auswirken können. Zu diesen Einstellungen gehören die Aktualisierung von Kennwörtern, Syslog-Server, SNMP-Überwachung, Authentifizierung und die Zugriffssteuerungsliste.

Während der Bereitstellung der Azure Stack-Lösung erstellt der Originalgerätehersteller (Original Equipment Manufacturer, OEM) die Switchkonfiguration sowohl für TOR-Instanzen als auch für BMC. Der OEM verwendet das Azure Stack-Automatisierungstool, um zu überprüfen, ob die erforderlichen Konfigurationen auf diesen Geräten ordnungsgemäß festgelegt sind. Die Konfigurationen basieren auf den Informationen in Ihrem Bereitstellungsarbeitsblatt für Azure Stack. 

>[!NOTE]
>Ändern Sie die Konfiguration **nicht** ohne Zustimmung des OEM oder des Microsoft Azure Stack-Technikteams. Eine Änderung an der Netzwerkgerätekonfiguration kann sich erheblich auf den Betrieb oder die Behebung von Netzwerkproblemen in ihrer Azure Stack-Instanz auswirken. Weitere Informationen zu diesen Funktionen in Ihrem Netzwerkgerät und dazu, wie diese Änderungen vorgenommen werden, erhalten Sie bei Ihrem OEM-Hardwareanbieter oder beim Microsoft-Support. Der OEM hat die Konfigurationsdatei mithilfe des Automatisierungstools auf der Grundlage Ihres Bereitstellungsarbeitsblatts für Azure Stack erstellt. 

Es gibt jedoch einige Werte, die in der Konfiguration der Netzwerkswitches hinzugefügt, entfernt oder geändert werden können.

#### <a name="password-update"></a>Aktualisieren von Kennwörtern

Der Operator kann das Kennwort jedes Benutzers auf den Netzwerkswitches jederzeit aktualisieren. Es ist nicht erforderlich, Informationen im Azure Stack-System zu ändern oder die Schritte zum Rotieren von Geheimnissen in Azure Stack auszuführen.

#### <a name="syslog-server"></a>Syslog-Server

Operatoren können die Switchprotokolle an einen Syslog-Server in Ihrem Datencenter umleiten.
Stellen Sie mit dieser Konfiguration sicher, dass die Protokolle für einen bestimmten Zeitpunkt für die Problembehandlung verwendet werden können. In der Standardeinstellung werden die Protokolle auf den Switches gespeichert. Deren Kapazität zum Speichern von Protokollen ist jedoch beschränkt. Im Abschnitt Aktualisierungen von Zugriffssteuerungslisten finden Sie eine Übersicht dazu, wie die Berechtigungen für den Verwaltungszugriff auf Switches konfiguriert werden.

#### <a name="snmp-monitoring"></a>SNMP-Überwachung

Der Operator kann das Simple Network Management Protocol (SNMP) v2 oder v3 konfigurieren, um die Netzwerkgeräte zu überwachen und Traps an eine Netzwerküberwachungsanwendung im Datencenter zu senden. Verwenden Sie aus Sicherheitsgründen SNMPv3, da es höhere Sicherheit als v2 bietet. Informationen zu den MIBs und der erforderlichen Konfiguration erhalten Sie bei Ihrem OEM-Hardwareanbieter.
Im Abschnitt Aktualisierungen von Zugriffssteuerungslisten finden Sie eine Übersicht dazu, wie die Berechtigungen für den Verwaltungszugriff auf Switches konfiguriert werden.

#### <a name="authentication"></a>Authentifizierung

Der Operator kann entweder RADIUS oder TACACS konfigurieren, um die Authentifizierung auf den Netzwerkgeräten zu verwalten. Informationen zu den unterstützten Methoden und der erforderlichen Konfiguration erhalten Sie bei Ihrem OEM-Hardwareanbieter. Im Abschnitt Aktualisierungen von Zugriffssteuerungslisten finden Sie eine Übersicht dazu, wie die Berechtigungen für den Verwaltungszugriff auf Switches konfiguriert werden.

#### <a name="access-control-list-updates"></a>Aktualisierungen von Zugriffssteuerungslisten

Der Operator kann einige Zugriffssteuerungslisten (ACLs) so ändern, dass der Zugriff auf Verwaltungsschnittstellen für Netzwerkgeräte und den Hardwarelebenszyklus-Host (HLH) von einem Netzwerkbereich vertrauenswürdiger Datencenter gestattet wird. Der Operator kann auswählen, welche Komponente von welchem Ort aus zugänglich ist. Mithilfe der Zugriffssteuerungsliste kann der Operator seinen Jumpbox-VMs für die Verwaltung innerhalb eines bestimmten Netzwerkbereichs den Zugriff auf die Schnittstelle für die Switch-Verwaltung, das HLH-Betriebssystem und den HLH-BMC gewähren.

Weitere Informationen finden Sie unter [Zugriffssteuerungsliste für den physischen Switch](#physical-switch-access-control-list).

#### <a name="tacacs-radius-and-syslog"></a>TACACS, RADIUS und Syslog

Die Azure Stack-Lösung wird nicht mit einer TACACS- oder RADIUS-Lösung für die Zugriffssteuerung von Geräten wie Switches und Routern ausgeliefert. Sie umfasst auch keine Syslog-Lösung zum Erfassen von Switchprotokollen. Allerdings unterstützen alle Geräte diese Dienste.
Zur Unterstützung der Integration eines vorhandenen TACACS-, RADIUS- oder Syslog-Servers in Ihrer Umgebung wird eine zusätzliche Datei mit der Konfiguration für den Netzwerkswitch bereitgestellt, die es dem Techniker vor Ort ermöglicht, den Switch an die Anforderungen des Kunden anzupassen.

## <a name="next-steps"></a>Nächste Schritte

[Netzwerkintegration](network-integration.md)
