---
title: Ausführen eines virtuellen Windows-Computers in Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie in Azure Stack einen virtuellen Windows-Computer ausführen.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 11/01/2019
ms.openlocfilehash: 5f9d8de7c08e8cfa0ad2af9bcb8f898fc32848a3
ms.sourcegitcommit: 7817d61fa34ac4f6410ce6f8ac11d292e1ad807c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74690218"
---
# <a name="run-a-windows-virtual-machine-on-azure-stack"></a>Ausführen eines virtuellen Windows-Computers in Azure Stack

Für die Bereitstellung eines virtuellen Computers (VM) in Azure Stack werden neben dem eigentlichen virtuellen Computer noch einige andere Komponenten benötigt, z. B. Netzwerk- und Speicherressourcen. In diesem Artikel werden die bewährten Methoden für das Ausführen eines virtuellen Windows-Computers in Azure veranschaulicht.

![Architektur für Windows-VM in Azure Stack](./media/iaas-architecture-vm-windows/image1.png)

## <a name="resource-group"></a>Resource group

Eine [Ressourcengruppe](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) ist ein logischer Container, der zusammengehörige Azure Stack-Ressourcen enthält. Ressourcen sollten allgemein auf der Grundlage ihrer Lebensdauer sowie auf der Grundlage der Benutzer gruppiert werden, die sie verwalten.

Legen Sie eng miteinander verknüpfte Ressourcen mit demselben Lebenszyklus in derselben [Ressourcengruppe](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) ab. Mithilfe von Ressourcengruppen können Sie Ressourcen als Gruppe bereitstellen und überwachen und Abrechnungskosten nach Ressourcengruppe verfolgen. Sie können auch Ressourcen als Gruppe löschen. Dies ist für Testbereitstellungen nützlich. Vergeben Sie aussagekräftige Ressourcennamen, um das Auffinden einer bestimmten Ressource und das Erfassen ihrer Rolle zu vereinfachen. Weitere Informationen finden Sie unter [Empfohlene Namenskonventionen für Azure-Ressourcen](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="virtual-machine"></a>Virtueller Computer

Sie können eine VM über eine Liste mit veröffentlichten Images oder über ein benutzerdefiniertes verwaltetes Image oder eine virtuelle Festplattendatei (VHD) bereitstellen, die in Azure Stack Blob Storage hochgeladen wurde.

Azure Stack bietet verschiedene Größen von Azure-VMs. Weitere Informationen finden Sie im Artikel zu den [Größen für virtuelle Computer in Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes). Starten Sie beim Verschieben einer vorhandenen Workload in Azure Stack mit der VM-Größe, die Ihren lokalen Servern/Azure am ehesten entspricht. Messen Sie dann die Leistung Ihrer tatsächlichen Workload in Bezug auf CPU, Arbeitsspeicher und Datenträger-IOPS (E/A-Vorgänge pro Sekunde), und passen Sie die Größe nach Bedarf an.

## <a name="disks"></a>Datenträger

Die Kosten basieren auf der Kapazität des bereitgestellten Datenträgers. IOPS und Durchsatz (also die Datenübertragungsrate) richten sich ebenfalls nach der VM-Größe. Berücksichtigen Sie beim Bereitstellen eines Datenträgers also alle drei Faktoren (Kapazität, IOPS und Durchsatz).

Die Datenträger-IOPS (Input/Output Operations Per Second, Ein-/Ausgabevorgänge pro Sekunde) in Azure Stack ist abhängig von der [VM-Größe](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes) und nicht vom Datenträgertyp. Dies bedeutet, dass der IOPS-Grenzwert für einen einzelnen zusätzlichen Datenträger für einen virtuellen Computer der Standard_Fs-Reihe 2.300 IOPS beträgt – unabhängig davon, ob Sie einen Datenträger des Typs SSD oder HDD auswählen. Der IOPS-Grenzwert bildet eine Obergrenze (zulässiges Maximum), um den „Noisy Neighbour“-Effekt zu verhindern. Sie sind keine IOPS-Garantie für eine VM mit einer bestimmten Größe.

Wir empfehlen auch die Verwendung von [Managed Disks](https://docs.microsoft.com/azure-stack/user/azure-stack-managed-disk-considerations). Verwaltete Datenträger führen die Speicherverarbeitung für Sie durch, sodass die Datenträgerverwaltung vereinfacht wird. Bei verwalteten Datenträgern ist kein Speicherkonto erforderlich. Sie geben einfach die Größe und den Typ des Datenträgers an, und dieser wird als Ressource mit Hochverfügbarkeit bereitgestellt.

Der Datenträger für das Betriebssystem ist eine in Azure Stack Blob Storage gespeicherte virtuelle Festplatte, sodass diese weiterhin bestehen bleibt, auch wenn der Hostcomputer ausgefallen ist. Es empfiehlt sich zudem, mindestens einen [Datenträger für Daten](https://docs.microsoft.com/azure-stack/user/azure-stack-manage-vm-disks) (VHD für Anwendungsdaten) zu erstellen. Installieren Sie, sofern möglich, Anwendungen auf einem Datenträger für Daten und nicht auf dem Datenträger für das Betriebssystem. Einige ältere Anwendungen müssen möglicherweise Komponenten auf dem Laufwerk „C:“ installieren. In diesem Fall können Sie die [Größe des Datenträgers für das Betriebssystem](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-expand-os-disk) mit PowerShell ändern.

Die VM wird auch mit einem temporären Datenträger erstellt (Laufwerk D: unter Windows). Dieser Datenträger wird in der Azure Stack-Speicherinfrastruktur auf einem temporären Volume gespeichert. Er kann bei Neustarts und anderen Ereignissen im VM-Lebenszyklus gelöscht werden. Verwenden Sie diesen Datenträger nur für temporäre Daten, z. B. Auslagerungsdateien.

## <a name="network"></a>Netzwerk

Die Netzwerkkomponenten enthalten die folgenden Ressourcen:

-   **Virtuelles Netzwerk**. Jeder virtuelle Computer wird in einem virtuellen Netzwerk bereitgestellt, das in mehrere Subnetze segmentiert werden kann.

-   **Netzwerkschnittstelle (NIC)** Die NIC ermöglicht der VM die Kommunikation mit dem virtuellen Netzwerk. Falls Sie mehrere Netzwerkschnittstellenkarten für Ihre VM benötigen, sollten Sie sich darüber im Klaren sein, dass für jede [VM-Größe](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes) eine maximale Anzahl von Netzwerkschnittstellenkarten definiert ist.

-   **Öffentliche IP-Adresse/virtuelle IP-Adresse**. Eine öffentliche IP-Adresse wird für die Kommunikation mit der VM benötigt, z. B. per Remotedesktop (RDP). Die öffentliche IP-Adresse kann dynamisch oder statisch sein. Die Standardeinstellung ist „Dynamisch“.

-   Reservieren Sie eine [statische IP-Adresse](https://docs.microsoft.com/azure/virtual-network/virtual-networks-reserved-public-ip), falls Sie eine feste IP-Adresse benötigen, die sich nicht ändert – wenn Sie z. B. einen A-Eintrag in DNS erstellen oder die IP-Adresse einer Liste mit sicheren Adressen hinzufügen müssen.

-   Sie können auch einen vollständig qualifizierten Domänennamen (FQDN) für die IP-Adresse erstellen. Sie können anschließend einen [CNAME-Eintrag](https://en.wikipedia.org/wiki/CNAME_record) in DNS registrieren, der auf den FQDN verweist. Weitere Informationen finden Sie unter [Erstellen eines vollqualifizierten Domänennamens im Azure-Portal](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-portal-create-fqdn).

-   **Netzwerksicherheitsgruppen (NSG)** NSGs dienen dazu, Netzwerkdatenverkehr zu VMs zuzulassen oder zu verweigern. NSGs können entweder Subnetzen oder einzelnen VM-Instanzen zugeordnet werden.

Alle Netzwerksicherheitsgruppen enthalten eine Reihe von [Standardregeln](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules), einschließlich einer Regel, die den gesamten eingehenden Internetverkehr blockiert. Die Standardregeln können nicht gelöscht, aber von anderen Regeln überschrieben werden. Um Internetdatenverkehr zu ermöglichen, erstellen Sie Regeln, die eingehenden Datenverkehr an bestimmten Ports zulassen, z. B. Port 80 für HTTP. Fügen Sie zum Aktivieren von RDP eine NSG-Regel hinzu, die eingehenden Datenverkehr am TCP-Port 3389 zulässt.

## <a name="operations"></a>Vorgänge

**Diagnose**: Aktivieren Sie die Überwachung und Diagnose, einschließlich grundlegender Integritätsmetriken, Infrastrukturprotokolle zur Diagnose sowie der [Startdiagnose](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). Startdiagnosen dienen dazu, einen Fehler beim Startvorgang zu untersuchen, wenn Ihre VM einen nicht startfähigen Zustand hat. Erstellen Sie ein Azure Storage-Konto zum Speichern der Protokolle. Ein standardmäßiger lokal redundanter Speicher (LRS) reicht für Diagnoseprotokolle aus. Weitere Informationen finden Sie unter [Aktivieren von Überwachung und Diagnose](https://docs.microsoft.com/azure-stack/user/azure-stack-metrics-azure-data).

**Verfügbarkeit**: Ihre VM kann aufgrund geplanter Wartungsarbeiten, die vom Azure Stack-Betreiber geplant sind, neu gestartet werden. Zur Erreichung von Hochverfügbarkeit für ein Produktionssystem mit mehreren VMs in Azure werden die VMs in einer [Verfügbarkeitsgruppe](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) angeordnet, um sie auf mehrere Fehlerdomänen und Updatedomänen zu verteilen. Im kleineren Rahmen von Azure Stack Hub ist eine Fehlerdomäne in einer Verfügbarkeitsgruppe als einzelner Knoten in der Skalierungseinheit definiert.  

Die Infrastruktur von Azure Stack Hub verfügt zwar bereits über Resilienz gegenüber Ausfällen, aber bei einem Hardwarefehler kommt es bei der zugrunde liegenden Technologie (Failoverclustering) für VMs auf einem betroffenen physischen Server trotzdem noch zu Ausfallzeiten. Azure Stack Hub unterstützt die Verwendung einer Verfügbarkeitsgruppe mit maximal drei Fehlerdomänen, um Konsistenz mit Azure zu erzielen.

|                   |             |
|-------------------|-------------|
| **Fehlerdomänen** | In einer Verfügbarkeitsgruppe angeordnete VMs werden physisch voneinander isoliert, indem sie so gleichmäßig wie möglich auf mehrere Fehlerdomänen (Azure Stack Hub-Knoten) verteilt werden. Bei einem Hardwarefehler werden VMs aus der fehlerhaften Fehlerdomäne in anderen Fehlerdomänen neu gestartet. Sie werden getrennt von den anderen VMs in separaten Fehlerdomänen, aber nach Möglichkeit in derselben Verfügbarkeitsgruppe gespeichert. Nachdem die Hardware wieder in den Onlinezustand versetzt wurde, wird für die VMs ein neuer Ausgleichsvorgang durchgeführt, um die Hochverfügbarkeit sicherzustellen. |
| **Updatedomänen**| Updatedomänen sind eine andere Option von Azure, mit der für Hochverfügbarkeit in Verfügbarkeitsgruppen gesorgt wird. Eine Updatedomäne ist eine logische Gruppe von zugrunde liegender Hardware, die zur gleichen Zeit gewartet werden kann. VMs in derselben Updatedomäne werden während einer geplanten Wartung gemeinsam neu gestartet. Wenn Mandanten VMs in einer Verfügbarkeitsgruppe erstellen, werden die VMs von der Azure-Plattform automatisch auf diese Updatedomänen verteilt. <br>In Azure Stack Hub wird für VMs eine Livemigration über die anderen Onlinehosts im Cluster durchgeführt, bevor der zugrunde liegende Host aktualisiert wird. Da es während eines Hostupdates nicht zu Mandantenausfallzeiten kommt, ist das Updatedomänenfeature in Azure Stack Hub nur für die Vorlagenkompatibilität mit Azure vorhanden. Virtuelle Computer in einer Verfügbarkeitsgruppe zeigen im Portal „0“ als Nummer der Updatedomäne an. |

**Sicherungen** In diesem Artikel finden Sie Empfehlungen zum Schutz Ihrer Azure Stack-IaaS-VMs.

**Beenden eines virtuellen Computers:** Unter Azure wird zwischen den Zuständen „Stopped“ (Beendet) und „Deallocated“ (Zuordnung aufgehoben) unterschieden. Ihnen werden nur Gebühren berechnet, wenn der VM-Status angehalten wird, aber nicht, wenn die Zuordnung für den virtuellen Computer aufgehoben wurde. Sie können die Zuordnung des virtuellen Computers auch mit der Schaltfläche **Beenden** im Azure Stack-Portal aufheben. Wenn das Herunterfahren über das Betriebssystem erfolgt, während Sie angemeldet sind, wird der virtuelle Computer zwar beendet, aber die Zuordnung wird **nicht** aufgehoben. Es fallen also weiter Kosten an.

**Löschen eines virtuellen Computers:** Wenn Sie eine VM löschen, werden die VM-Datenträger nicht gelöscht. Dies bedeutet, dass Sie die VM problemlos löschen können, ohne dass Daten verloren gehen. Allerdings wird Ihnen der Speicherplatz weiter in Rechnung gestellt. Löschen Sie das verwaltete Datenträgerobjekt, um den VM-Datenträger zu löschen. Zur Verhinderung des versehentlichen Löschens verwenden Sie eine *Ressourcensperre*, um die gesamte Ressourcengruppe oder einzelne Ressourcen, z. B. einen virtuellen Computer, zu sperren.

## <a name="security-considerations"></a>Sicherheitshinweise

Führen Sie für Ihre VMs ein Onboarding bei [Azure Security Center](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack) durch, um sich eine zentrale Übersicht über den Sicherheitszustand Ihrer Azure-Ressourcen zu verschaffen. Mit Security Center werden potenzielle Sicherheitsprobleme überwacht, und Sie erhalten eine umfassende Darstellung des Sicherheitszustands Ihrer Bereitstellung. Security Center wird für jedes Azure-Abonnement individuell konfiguriert. Aktivieren Sie die Erfassung von Sicherheitsdaten wie unter [Einbinden Ihres Azure-Abonnements in Security Center Standard](https://docs.microsoft.com/azure/security-center/security-center-get-started) beschrieben. Nachdem die Datensammlung aktiviert wurde, durchsucht Security Center VMs automatisch, die unter diesem Abonnement erstellt werden.

**Patchverwaltung:** Informationen zum Konfigurieren der Patchverwaltung auf Ihrer VM finden Sie in diesem [Artikel](https://docs.microsoft.com/azure-stack/user/vm-update-management). Falls aktiviert, prüft Security Center, ob kritische und Sicherheitsupdates fehlen. Verwenden Sie [Gruppenrichtlinieneinstellungen](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates) auf dem virtuellen Computer, um automatische Systemupdates zu aktivieren.

**Antischadsoftware:** Falls aktiviert, prüft Security Center, ob Antischadsoftware installiert ist. Sie können auch das Security Center nutzen, um Antischadsoftware über das Azure-Portal zu installieren.

**Zugriffssteuerung:** Steuern Sie den Zugriff auf Azure-Ressourcen mithilfe der [rollenbasierten Zugriffssteuerung](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is) (Role-Based Access Control, RBAC). Mithilfe der RBAC können Sie Mitglieder Ihres DevOps-Teams Autorisierungsrollen zuweisen. Die Rolle „Leser“ kann beispielsweise Azure-Ressourcen anzeigen, diese aber nicht erstellen, verwalten oder löschen. Einige Berechtigungen gelten spezifisch für einen Azure-Ressourcentyp. Die VM-Rolle „Mitwirkender“ kann z. B. eine VM neu starten oder Ihre Zuordnung aufheben, das Administratorkennwort zurücksetzen, eine neue VM erstellen usw. Andere [integrierte RBAC-Rollen](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles), die für diese Architektur nützlich sein können, sind u. a. [DevTest Labs-Benutzer](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles#devtest-labs-user) und [Netzwerkmitwirkender](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles#network-contributor).

> [!Note]  
> Die RBAC schränkt nicht die Aktionen eines Benutzers ein, der bei einer VM angemeldet ist. Diese Berechtigungen werden vom Kontotyp im Gastbetriebssystem bestimmt.

**Überwachungsprotokolle:** Verwenden Sie [Aktivitätsprotokolle](https://docs.microsoft.com/azure-stack/user/azure-stack-metrics-azure-data?#activity-log), um Bereitstellungsaktionen und andere VM-Ereignisse anzuzeigen.

**Datenverschlüsselung:** Azure Stack nutzt BitLocker-128-Bit-AES-Verschlüsselung, um ruhende Benutzer- und Infrastrukturdaten im Speichersubsystem zu schützen. Weitere Informationen finden Sie unter [Verschlüsselung für ruhende Daten in Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-security-bitlocker).


## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Azure Stack-VMs finden Sie unter [Features von Azure Stack-VMs](azure-stack-vm-considerations.md).  
- Weitere Informationen zu Azure-Cloudmustern finden Sie unter [Cloudentwurfsmuster](https://docs.microsoft.com/azure/architecture/patterns).
