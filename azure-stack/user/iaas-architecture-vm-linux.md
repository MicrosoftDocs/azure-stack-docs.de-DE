---
title: Ausführen eines virtuellen Linux-Computers in Azure Stack Hub
description: Erfahren Sie, wie Sie in Azure Stack Hub einen virtuellen Linux-Computer ausführen.
author: mattbriggs
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 11/01/2019
ms.openlocfilehash: 0803dc8332e296c0b92912967d34a6dfd885d4df
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97873723"
---
# <a name="run-a-linux-virtual-machine-on-azure-stack-hub"></a>Ausführen eines virtuellen Linux-Computers in Azure Stack Hub

Für die Bereitstellung eines virtuellen Computers (VM) in Azure Stack Hub werden wie in Azure neben der eigentlichen VM noch einige andere Komponenten benötigt, z. B. Netzwerk- und Speicherressourcen. In diesem Artikel werden die bewährten Methoden für das Ausführen eines virtuellen Linux-Computers in Azure Stack Hub veranschaulicht.

![Architektur für Linux-VM in Azure Stack Hub](./media/iaas-architecture-vm-linux/image1.png)

## <a name="resource-group"></a>Resource group

Eine [Ressourcengruppe](/azure/azure-resource-manager/resource-group-overview) ist ein logischer Container, der zusammengehörige Azure Stack Hub-Ressourcen enthält. Ressourcen sollten allgemein auf der Grundlage ihrer Lebensdauer sowie auf der Grundlage der Benutzer gruppiert werden, die sie verwalten.

Legen Sie eng miteinander verknüpfte Ressourcen mit demselben Lebenszyklus in derselben [Ressourcengruppe](/azure/azure-resource-manager/resource-group-overview) ab. Mithilfe von Ressourcengruppen können Sie Ressourcen als Gruppe bereitstellen und überwachen und Abrechnungskosten nach Ressourcengruppe verfolgen. Sie können auch Ressourcen als Gruppe löschen. Dies ist für Testbereitstellungen nützlich. Vergeben Sie aussagekräftige Ressourcennamen, um das Auffinden einer bestimmten Ressource und das Erfassen ihrer Rolle zu vereinfachen. Weitere Informationen finden Sie unter [Empfohlene Namenskonventionen für Azure-Ressourcen](/azure/architecture/best-practices/naming-conventions).

## <a name="virtual-machine"></a>Virtueller Computer

Sie können eine VM über eine Liste mit veröffentlichten Images oder über ein benutzerdefiniertes verwaltetes Image oder eine virtuelle Festplattendatei (VHD) bereitstellen, die in Azure Stack Hub Blob Storage hochgeladen wurde. Azure Stack Hub unterstützt die Ausführung verschiedener beliebter Linux-Distributionen, z. B. CentOS, Debian, Red Hat Enterprise, Ubuntu und SUSE. Weitere Informationen finden Sie unter [Linux in Azure Stack Hub](../operator/azure-stack-linux.md). Sie können auch eines der veröffentlichten Linux-Images syndizieren, die im Azure Stack Hub Marketplace verfügbar sind.

Azure Stack Hub bietet verschiedene Größen von Azure-VMs. Weitere Informationen finden Sie im Artikel zu den [Größen für virtuelle Computer in Azure Stack Hub](./azure-stack-vm-sizes.md). Starten Sie beim Verschieben einer vorhandenen Workload in Azure Stack Hub mit der VM-Größe, die Ihren lokalen Servern/Azure am ehesten entspricht. Messen Sie dann die Leistung Ihrer tatsächlichen Workload in Bezug auf CPU, Arbeitsspeicher und Datenträger-IOPS (E/A-Vorgänge pro Sekunde), und passen Sie die Größe nach Bedarf an.

## <a name="disks"></a>Datenträger

Die Kosten basieren auf der Kapazität des bereitgestellten Datenträgers. IOPS und Durchsatz (also die Datenübertragungsrate) richten sich ebenfalls nach der VM-Größe. Berücksichtigen Sie beim Bereitstellen eines Datenträgers also alle drei Faktoren (Kapazität, IOPS und Durchsatz).

Die Datenträger-IOPS (Input/Output Operations Per Second, Ein-/Ausgabevorgänge pro Sekunde) in Azure Stack Hub ist abhängig von der [VM-Größe](./azure-stack-vm-sizes.md) und nicht vom Datenträgertyp. Dies bedeutet, dass der IOPS-Grenzwert für einen einzelnen zusätzlichen Datenträger für einen virtuellen Computer der Standard_Fs-Reihe 2.300 IOPS beträgt – unabhängig davon, ob Sie einen Datenträger des Typs SSD oder HDD auswählen. Der IOPS-Grenzwert bildet eine Obergrenze (zulässiges Maximum), um den „Noisy Neighbour“-Effekt zu verhindern. Sie sind keine IOPS-Garantie für eine VM mit einer bestimmten Größe.

Wir empfehlen auch die Verwendung von [Managed Disks](./azure-stack-managed-disk-considerations.md). Verwaltete Datenträger führen die Speicherverarbeitung für Sie durch, sodass die Datenträgerverwaltung vereinfacht wird. Bei verwalteten Datenträgern ist kein Speicherkonto erforderlich. Sie geben einfach die Größe und den Typ des Datenträgers an, und dieser wird als Ressource mit Hochverfügbarkeit bereitgestellt.

Der Datenträger für das Betriebssystem ist eine in [Azure Stack Hub Storage](./azure-stack-storage-overview.md) gespeicherte virtuelle Festplatte, sodass diese auch weiterhin bestehen bleibt, wenn der Hostcomputer ausgefallen ist. Für Linux-VMs ist /dev/sda1 der Datenträger für das Betriebssystem. Es empfiehlt sich zudem, mindestens einen [Datenträger für Daten](./azure-stack-manage-vm-disks.md) (VHD für Anwendungsdaten) zu erstellen.

Wenn Sie eine virtuelle Festplatte (VHD) erstellen, ist sie nicht formatiert. Melden Sie sich an der VM an, um den Datenträger zu formatieren. In der Linux-Shell werden Datenträger für Daten als /dev/sdc, /dev/sdd usw. angezeigt. Sie können lsblk ausführen, um die Blockgeräte einschließlich der Datenträger aufzulisten. Um einen Datenträger für Daten zu verwenden, erstellen Sie eine Partition und ein Dateisystem und binden den Datenträger ein. Beispiel:

```bash
# Create a partition.
sudo fdisk /dev/sdc \# Enter 'n' to partition, 'w' to write the change.

# Create a file system.
sudo mkfs -t ext3 /dev/sdc1

# Mount the drive.
sudo mkdir /data1
sudo mount /dev/sdc1 /data1
```

Wenn Sie einen Datenträger für Daten hinzufügen, wird dem Datenträger eine logische Einheitennummer (Logical Unit Number, LUN) zugewiesen. Optional können Sie die LUN-ID angeben, z. B. wenn Sie einen Datenträger austauschen und dieselbe LUN-ID beibehalten möchten oder über eine Anwendung verfügen, die nach einer bestimmten LUN-ID sucht. Beachten Sie jedoch, dass die LUN-IDs für jeden Datenträger eindeutig sein muss.

Die VM wird mit einem temporären Datenträger erstellt. Dieser Datenträger wird in der Azure Stack Hub-Speicherinfrastruktur auf einem temporären Volume gespeichert. Er kann bei Neustarts und anderen Ereignissen im VM-Lebenszyklus gelöscht werden. Verwenden Sie diesen Datenträger nur für temporäre Daten, z. B. Auslagerungsdateien. Für Linux-VMs ist der temporäre Datenträger /dev/sdb1 und wird in /mnt/resource oder /mnt bereitgestellt.

## <a name="network"></a>Netzwerk

Die Netzwerkkomponenten enthalten die folgenden Ressourcen:

-   **Virtuelles Netzwerk**. Jeder virtuelle Computer wird in einem virtuellen Netzwerk bereitgestellt, das in mehrere Subnetze segmentiert werden kann.

-   **Netzwerkschnittstelle (NIC)** Die NIC ermöglicht der VM die Kommunikation mit dem virtuellen Netzwerk. Falls Sie mehrere Netzwerkschnittstellenkarten für Ihre VM benötigen, sollten Sie sich darüber im Klaren sein, dass für jede [VM-Größe](./azure-stack-vm-sizes.md) eine maximale Anzahl von Netzwerkschnittstellenkarten definiert ist.

-   **Öffentliche IP-Adresse/virtuelle IP-Adresse**. Eine öffentliche IP-Adresse wird für die Kommunikation mit der VM benötigt, z. B. per Remotedesktop (RDP). Die öffentliche IP-Adresse kann dynamisch oder statisch sein. Die Standardeinstellung ist „Dynamisch“. Falls Sie mehrere Netzwerkschnittstellenkarten für Ihre VM benötigen, sollten Sie sich darüber im Klaren sein, dass für jede [VM-Größe](./azure-stack-vm-sizes.md) eine maximale Anzahl von Netzwerkschnittstellenkarten definiert ist.

-   Sie können auch einen vollständig qualifizierten Domänennamen (FQDN) für die IP-Adresse erstellen. Sie können anschließend einen [CNAME-Eintrag](https://en.wikipedia.org/wiki/CNAME_record) in DNS registrieren, der auf den FQDN verweist. Weitere Informationen finden Sie unter [Erstellen eines vollqualifizierten Domänennamens im Azure-Portal](/azure/virtual-machines/windows/portal-create-fqdn).

-   **Netzwerksicherheitsgruppe (NSG)** . Netzwerksicherheitsgruppen dienen zum Zulassen oder Verweigern von Netzwerkdatenverkehr zu VMs. NSGs können entweder Subnetzen oder einzelnen VM-Instanzen zugeordnet werden.

Alle Netzwerksicherheitsgruppen enthalten eine Reihe von [Standardregeln](/azure/virtual-network/security-overview#default-security-rules), einschließlich einer Regel, die den gesamten eingehenden Internetverkehr blockiert. Die Standardregeln können nicht gelöscht, aber von anderen Regeln überschrieben werden. Um Internetdatenverkehr zu ermöglichen, erstellen Sie Regeln, die eingehenden Datenverkehr an bestimmten Ports zulassen, z. B. Port 80 für HTTP. Fügen Sie zum Aktivieren von SSH eine NSG-Regel hinzu, die eingehenden Datenverkehr am TCP-Port 22 zulässt.

## <a name="operations"></a>Operationen (Operations)

**SSH**. Bevor Sie eine Linux-VM erstellen, wird ein 2048-Bit-RSA-Paar aus privatem und öffentlichem Schlüssel generiert. Verwenden Sie die öffentliche Schlüsseldatei bei der Erstellung der VM. Weitere Informationen finden Sie unter [Verwenden von SSH mit Linux auf Azure](/azure/virtual-machines/linux/mac-create-ssh-keys).

**Diagnose**: Aktivieren Sie die Überwachung und Diagnose, einschließlich grundlegender Integritätsmetriken, Infrastrukturprotokolle zur Diagnose sowie der [Startdiagnose](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). Startdiagnosen dienen dazu, einen Fehler beim Startvorgang zu untersuchen, wenn Ihre VM einen nicht startfähigen Zustand hat. Erstellen Sie ein Azure Storage-Konto zum Speichern der Protokolle. Ein standardmäßiger lokal redundanter Speicher (LRS) reicht für Diagnoseprotokolle aus. Weitere Informationen finden Sie unter [Aktivieren von Überwachung und Diagnose](./azure-stack-metrics-azure-data.md).

**Verfügbarkeit**: Ihre VM kann aufgrund geplanter Wartungsarbeiten, die vom Azure Stack Hub-Bediener geplant sind, neu gestartet werden. Stellen Sie mehrere VMs in einer [Verfügbarkeitsgruppe](../operator/azure-stack-app-service-deploy.md?view=azs-2002) bereit, um eine höhere Verfügbarkeit zu erzielen.

**Sicherungen** In [diesem Artikel](./azure-stack-manage-vm-protect.md) finden Sie Empfehlungen zum Schutz Ihrer Azure Stack Hub-IaaS-VMs.

**Beenden eines virtuellen Computers:** Unter Azure wird zwischen den Zuständen „Stopped“ (Beendet) und „Deallocated“ (Zuordnung aufgehoben) unterschieden. Ihnen werden nur Gebühren berechnet, wenn der VM-Status angehalten wird, aber nicht, wenn die Zuordnung für den virtuellen Computer aufgehoben wurde. Sie können die Zuordnung des virtuellen Computers auch mit der Schaltfläche **Beenden** im Azure Stack Hub-Portal aufheben. Wenn das Herunterfahren über das Betriebssystem erfolgt, während Sie angemeldet sind, wird der virtuelle Computer zwar beendet, aber die Zuordnung wird **nicht** aufgehoben. Es fallen also weiter Kosten an.

**Löschen eines virtuellen Computers:** Wenn Sie eine VM löschen, werden die VM-Datenträger nicht gelöscht. Dies bedeutet, dass Sie die VM problemlos löschen können, ohne dass Daten verloren gehen. Allerdings wird Ihnen der Speicherplatz weiter in Rechnung gestellt. Löschen Sie das verwaltete Datenträgerobjekt, um den VM-Datenträger zu löschen. Zur Verhinderung des versehentlichen Löschens verwenden Sie eine [Ressourcensperre](/azure/resource-group-lock-resources), um die gesamte Ressourcengruppe oder einzelne Ressourcen, z. B. einen virtuellen Computer, zu sperren.

## <a name="security-considerations"></a>Sicherheitshinweise

Führen Sie für Ihre VMs ein Onboarding bei [Azure Security Center](/azure/security-center/quick-onboard-azure-stack) durch, um sich eine zentrale Übersicht über den Sicherheitszustand Ihrer Azure-Ressourcen zu verschaffen. Mit Security Center werden potenzielle Sicherheitsprobleme überwacht, und Sie erhalten eine umfassende Darstellung des Sicherheitszustands Ihrer Bereitstellung. Security Center wird für jedes Azure-Abonnement individuell konfiguriert. Aktivieren Sie die Erfassung von Sicherheitsdaten wie unter [Einbinden Ihres Azure-Abonnements in Security Center Standard](/azure/security-center/security-center-get-started) beschrieben. Nachdem die Datensammlung aktiviert wurde, durchsucht Security Center VMs automatisch, die unter diesem Abonnement erstellt werden.

**Patchverwaltung:** Informationen zum Konfigurieren der Patchverwaltung auf Ihrer VM finden Sie in diesem [Artikel](./vm-update-management.md). Falls aktiviert, prüft Security Center, ob kritische und Sicherheitsupdates fehlen. Verwenden Sie [Gruppenrichtlinieneinstellungen](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates) auf dem virtuellen Computer, um automatische Systemupdates zu aktivieren.

**Antischadsoftware:** Falls aktiviert, prüft Security Center, ob Antischadsoftware installiert ist. Sie können auch das Security Center nutzen, um Antischadsoftware über das Azure-Portal zu installieren.

**Zugriffssteuerung:** Steuern Sie den Zugriff auf Azure-Ressourcen mithilfe der [rollenbasierten Zugriffssteuerung](/azure/active-directory/role-based-access-control-what-is) (Role-Based Access Control, RBAC). Mithilfe der RBAC können Sie Mitglieder Ihres DevOps-Teams Autorisierungsrollen zuweisen. Die Rolle „Leser“ kann beispielsweise Azure-Ressourcen anzeigen, diese aber nicht erstellen, verwalten oder löschen. Einige Berechtigungen gelten spezifisch für einen Azure-Ressourcentyp. Die VM-Rolle „Mitwirkender“ kann z. B. eine VM neu starten oder Ihre Zuordnung aufheben, das Administratorkennwort zurücksetzen, eine neue VM erstellen usw. Andere [integrierte RBAC-Rollen](/azure/active-directory/role-based-access-built-in-roles), die für diese Architektur nützlich sein können, sind u. a. [DevTest Labs-Benutzer](/azure/active-directory/role-based-access-built-in-roles#devtest-labs-user) und [Netzwerkmitwirkender](/azure/active-directory/role-based-access-built-in-roles#network-contributor).

> [!NOTE]  
> Die RBAC schränkt nicht die Aktionen eines Benutzers ein, der bei einer VM angemeldet ist. Diese Berechtigungen werden vom Kontotyp im Gastbetriebssystem bestimmt.

**Überwachungsprotokolle:** Verwenden Sie [Aktivitätsprotokolle](./azure-stack-metrics-azure-data.md?#activity-log), um Bereitstellungsaktionen und andere VM-Ereignisse anzuzeigen.

**Datenverschlüsselung:** Azure Stack Hub schützt Benutzer- und Infrastrukturdaten auf der Ebene des Speichersubsystems unter Verwendung von Verschlüsselung ruhender Daten. Das Speichersubsystem von Azure Stack Hub wird mithilfe von BitLocker mit 128-Bit-AES-Verschlüsselung verschlüsselt. Weitere Informationen finden Sie in [diesem](../operator/azure-stack-security-bitlocker.md) Artikel.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Azure Stack Hub-VMs finden Sie unter [Features von Azure Stack Hub-VMs](azure-stack-vm-considerations.md).  
- Weitere Informationen zu Azure-Cloudmustern finden Sie unter [Cloudentwurfsmuster](/azure/architecture/patterns).
