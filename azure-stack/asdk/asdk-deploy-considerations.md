---
title: Anforderungen und Überlegungen zu ASDK
description: In diesem Artikel werden die Hardware-, Software- und Umgebungsanforderungen für Azure Stack Development Kit (ASDK) erläutert.
author: myoungerman
ms.topic: article
ms.date: 09/23/2020
ms.author: v-myoung
ms.reviewer: misainat
ms.lastreviewed: 09/23/2020
ms.openlocfilehash: dbd0398b7f6582c12288c2ae72137c4a673ddfcc
ms.sourcegitcommit: 950dbc793b3498173923d0bc9fe56662a349abf1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2020
ms.locfileid: "92082087"
---
# <a name="asdk-requirements-and-considerations"></a>Anforderungen und Überlegungen zu ASDK

Stellen Sie vor der Bereitstellung des Azure Stack Development Kit (ASDK) sicher, dass der ASDK-Hostcomputer die in diesem Artikel beschriebenen Anforderungen erfüllt.

## <a name="hardware"></a>Hardware

| Komponente                                             | Minimum                                                                                                 | Empfohlen                                                                                             |
|-------------------------------------------------------|---------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| Laufwerke: Betriebssystem                         | 1 Betriebssystem-Datenträger mit mindestens 200 GB verfügbarem Speicherplatz für die Systempartition (SSD oder HDD).             | 1 Betriebssystem-Datenträger mit mindestens 200 GB verfügbarem Speicherplatz für die Systempartition (SSD oder HDD).                           |
| Laufwerke: Allgemeine Development Kit-Daten<sup>*</sup> | 4 Datenträger. Jeder Datenträger stellt eine Kapazität von mindestens 240GB bereit (SSD oder HDD). Alle verfügbaren Datenträger werden verwendet. | 4 Datenträger. Jeder Datenträger stellt eine Kapazität von mindestens 400GB bereit (SSD oder HDD). Alle verfügbaren Datenträger werden verwendet. |
| Compute: CPU                                          | Dual-Socket: 16 physische Kerne (gesamt).                                                                 | Dual-Socket: 20 physische Kerne (gesamt).                                                                 |
| Compute: Arbeitsspeicher                                       | 192 GB RAM.                                                                                             | 256 GB RAM.                                                                                             |
| Compute: BIOS                                         | Hyper-V aktiviert (mit SLAT-Unterstützung).                                                                    | Hyper-V aktiviert (mit SLAT-Unterstützung).                                                                    |
| Netzwerk: NIC                                          | Windows Server 2012 R2-Zertifizierung. Keine speziellen Features erforderlich.                                 | Windows Server 2012 R2-Zertifizierung. Keine speziellen Features erforderlich.                                 |
| HW-Logo-Zertifizierung                                 | [Certified for Windows Server 2012 R2 (Zertifiziert für Windows Server 2012 R2)](https://www.windowsservercatalog.com/content.aspx?ctf=logo.htm). | [Certified for Windows Server 2016 (Zertifiziert für Windows Server 2016)](https://www.windowsservercatalog.com/content.aspx?ctf=logo.htm).    |
  
<sup>*</sup> Sie benötigen mehr als diese empfohlene Kapazität, wenn Sie viele [Marketplace-Elemente](../operator/azure-stack-create-and-publish-marketplace-item.md) aus Azure hinzufügen möchten.

### <a name="hardware-notes"></a>Hinweise zur Hardware

**Konfiguration der Datenträgerlaufwerke**: Alle Datenlaufwerke müssen den gleichen Typ (SAS, SATA oder NVMe) und die gleiche Kapazität aufweisen. Wenn SAS-Laufwerke verwendet werden, müssen diese über einen einzelnen Pfad angefügt werden (MPIO, Multipfad-Unterstützung wird nicht bereitgestellt).

**HBA-Konfigurationsoptionen**

* (Bevorzugt) Einfacher HBA.
* RAID-HBA – Adapter muss im -Pass-Through-Modus konfiguriert sein.
* RAID-HBA: Datenträger sollten als einzelner Datenträger (RAID-0) konfiguriert werden.

**Unterstützte Kombinationen aus Bus und Medientypen**

* SATA-HDD
* SAS-HDD
* RAID-HDD
* RAID-SSD (wenn der Medientyp nicht angegeben/unbekannt<sup>*</sup> ist)
* SATA-SSD + SATA-HDD
* SAS-SSD + SAS-HDD
* NVMe

<sup>*</sup> RAID-Controller ohne Pass-Through-Funktion können den Medientyp nicht erkennen. Controller dieser Art kennzeichnen HDD und SSD als unbekannt. In diesem Fall wird die SSD als persistenter Speicher und nicht als Cachegerät verwendet. Daher können Sie das ASDK auf diesen SSDs bereitstellen.

**Beispiel-HBAs**: LSI 9207-8i, LSI-9300-8i oder LSI-9265-8i im Pass-Through-Modus.

OEM-Beispielkonfigurationen sind verfügbar.

### <a name="storage-resiliency-for-the-asdk"></a>Speicherresilienz für das ASDK

Das ASDK ist ein System mit einem einzelnen Knoten und daher nicht zum Überprüfen der Produktionsredundanz eines integrierten Azure Stack-Systems ausgelegt. Sie können jedoch die zugrunde liegende Speicherredundanz des ASDK erhöhen, indem Sie eine optimale Kombination von Festplatten- und SSD-Laufwerken verwenden. Sie können anstelle einer einfachen Resilienzkonfiguration (vergleichbar mit RAID0) eine Zwei-Wege-Spiegelungskonfiguration bereitstellen (vergleichbar mit RAID1). Verwenden Sie einen geeigneten Typ sowie eine ausreichende Kapazität und Anzahl von Laufwerken für die zugrunde liegende Konfiguration „Direkte Speicherplätze“.

So verwenden Sie eine Zwei-Wege-Spiegelungskonfiguration zum Gewährleisten der Speicherresilienz:

- Sie benötigen mehr als 2 TB Festplattenlaufwerk-Kapazität im System.
- Wenn Sie keine SSDs in Ihrem ASDK haben, benötigen Sie für eine Zwei-Wege-Spiegelungskonfiguration mindestens acht Festplattenlaufwerke.
- Wenn Sie SSDs in Ihrem ASDK und Festplattenlaufwerke haben, benötigen Sie mindestens fünf Festplattenlaufwerke. Allerdings werden sechs Festplattenlaufwerke empfohlen. Für sechs Festplattenlaufwerke empfiehlt es sich auch, mindestens drei entsprechende SSDs im System bereitzustellen, damit ein Cachedatenträger (SSD) für zwei Kapazitätslaufwerke (Festplattenlaufwerk) verfügbar ist.

Beispiel für eine Zwei-Wege-Spiegelungskonfiguration:

- Acht Festplattenlaufwerke
- Drei SSDs/sechs Festplattenlaufwerke
- Vier SSDs/acht Festplattenlaufwerke

## <a name="operating-system"></a>Betriebssystem
|  | **Anforderungen** |
| --- | --- |
| **Betriebssystemversion** |Windows Server 2016 oder höher. Die Version des Betriebssystems ist vor dem Starten der Bereitstellung nicht wichtig, da der Hostcomputer auf der virtuellen Festplatte gestartet wird, die in der Azure Stack-Installation enthalten ist. Das Betriebssystem und alle erforderlichen Patches sind bereits in das Image integriert. Verwenden Sie keine Schlüssel zum Aktivieren von Windows Server-Instanzen, die im ASDK verwendet werden. |

## <a name="account-requirements"></a>Kontoanforderungen
In der Regel stellen Sie das ASDK bereit, wenn eine Internetverbindung besteht und Sie eine Verbindung mit Microsoft Azure herstellen können. In diesem Fall müssen Sie ein Azure Active Directory-Konto (Azure AD) zum Bereitstellen des ASDK konfigurieren.

Wenn Ihre Umgebung nicht mit dem Internet verbunden ist oder Sie Azure AD nicht verwenden möchten, können Sie Azure Stack mithilfe von Active Directory-Verbunddienste (AD FS) bereitstellen. Das ASDK enthält eigene AD FS- und Active Directory Domain Services-Instanzen. Wenn Sie diese Bereitstellungsoption nutzen, müssen Sie vorab Konten einrichten.

> [!NOTE]
> Wenn Sie die AD FS-Bereitstellungsoption nutzen, müssen Sie Azure Stack erneut bereitstellen, um zu Azure AD zu wechseln.

### <a name="azure-active-directory-accounts"></a>Azure Active Directory-Konten
Zum Bereitstellen von Azure Stack über ein Azure AD-Konto müssen Sie vor dem Ausführen des PowerShell-Bereitstellungsskripts ein Azure AD-Konto vorbereiten. Dieses Konto wird der globale Administrator für den Azure AD-Mandanten. Es wird zum Bereitstellen und Delegieren von Anwendungen und Dienstprinzipalen für alle Azure Stack-Dienste verwendet, die mit Azure AD und Graph-API interagieren. Es fungiert außerdem als Besitzer des Standardanbieterabonnements (den Sie später jedoch ändern können). Mit diesem Konto können Sie sich beim Administratorportal Ihres Azure Stack-Systems anmelden.

1. Erstellen Sie ein Azure AD-Konto, das Verzeichnisadministrator für mindestens eine Azure AD-Instanz ist. Wenn Sie bereits eines haben, können Sie es verwenden. Andernfalls können Sie unter [https://azure.microsoft.com/free/](https://azure.microsoft.com/free/) kostenlos eins erstellen (in China verwenden Sie stattdessen <https://go.microsoft.com/fwlink/?LinkID=717821> ). Wenn Sie [Azure Stack zu einem späteren Zeitpunkt bei Azure registrieren](asdk-register.md) möchten, benötigen Sie auch ein Abonnement in diesem neu erstellten Konto.
   
    Speichern Sie diese Anmeldeinformationen zur Verwendung durch den Dienstadministrator. Dieses Konto kann Ressourcenclouds, Benutzerkonten, Mandantenpläne, Kontingente und Preise konfigurieren und verwalten. Im Portal ist es möglich, Websiteclouds und private Clouds mit VMs sowie Pläne zu erstellen und Benutzerabonnements zu verwalten.
1. Erstellen Sie mindestens ein Testbenutzerkonto in Ihrem Azure AD-Verzeichnis, damit Sie sich als Mandant beim ASDK anmelden können.
   
   | **Azure Active Directory-Konto** | **Unterstützt?** |
   | --- | --- |
   | Geschäfts-, Schul- oder Unikonto mit gültigem globalem Azure-Abonnement |Ja |
   | Microsoft-Konto mit gültigem globalem Azure-Abonnement |Ja |
   | Geschäfts-, Schul- oder Unikonto mit gültigem Azure-Abonnement für China |Ja |
   | Geschäfts-, Schul- oder Unikonto mit gültigem Azure-Abonnement für die US-Regierung |Ja |

Nach der Bereitstellung ist die globale Azure AD-Administratorberechtigung nicht erforderlich. Einige Vorgänge erfordern jedoch möglicherweise die Anmeldeinformationen für den globalen Administrator. Beispiele für solche Vorgänge sind ein Ressourcenanbieter-Installationsskript oder ein neues Feature, dem eine Berechtigung erteilt werden muss. Sie können entweder die globalen Administratorrechte des Kontos vorübergehend wiederherstellen oder ein separates globales Administratorkonto verwenden, das Besitzer des *Standardanbieterabonnements* ist.

## <a name="network"></a>Netzwerk
### <a name="switch"></a>Switch
Ein verfügbarer Port auf einem Switch für den ASDK-Computer.  

Der ASDK-Computer unterstützt das Herstellen einer Verbindung mit einem Switchzugriffsport oder Trunkport. Auf dem Switch sind keine speziellen Funktionen erforderlich.

### <a name="subnet"></a>Subnet
Verbinden Sie den ASDK-Computer nicht mit den folgenden Subnetzen:

* 192.168.200.0/24
* 192.168.100.0/27
* 192.168.101.0/26
* 192.168.102.0/24
* 192.168.103.0/25
* 192.168.104.0/25

Diese Subnetze sind für die internen Netzwerke innerhalb der ASDK-Umgebung reserviert.

### <a name="ipv4ipv6"></a>IPv4/IPv6
Es wird nur IPv4 unterstützt. Sie können keine IPv6-Netzwerke erstellen.

### <a name="dhcp"></a>DHCP
Stellen Sie sicher, dass in dem Netzwerk, mit dem die Netzwerkkarte eine Verbindung herstellt, ein DHCP-Server verfügbar ist. Wenn DHCP nicht verfügbar ist, müssen Sie ein weiteres statisches IPv4-Netzwerk neben dem vom Host verwendeten vorbereiten. Sie müssen diese IP-Adresse und das Gateway als Bereitstellungsparameter angeben.

### <a name="internet-access"></a>Zugriff auf das Internet
Azure Stack benötigt entweder direkten Internetzugriff oder Zugriff über einen transparenten Proxy. Azure Stack unterstützt nicht die Konfiguration eines Webproxys, um Zugriff auf das Internet zu aktivieren. Port 80 und 443 werden unter den Domänen „graph.windows.net“ und „login.microsoftonline.com“ verwendet.


## <a name="next-steps"></a>Nächste Schritte

- [Herunterladen des ASDK-Bereitstellungspakets](asdk-download.md).
- Weitere Informationen zu direkten Speicherplätzen finden Sie unter [Direkte Speicherplätze – Übersicht](/windows-server/storage/storage-spaces/storage-spaces-direct-overview).
