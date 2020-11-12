---
title: Einführung in Azure Stack Hub-VMs
description: Enthält Informationen zu Azure Stack Hub-VMs.
author: sethmanheim
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 01/05/2020
ms.openlocfilehash: ff462a8fbdb2de67e58f15248aaffa79f0abb695
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94545759"
---
# <a name="introduction-to-azure-stack-hub-vms"></a>Einführung in Azure Stack Hub-VMs

Azure Stack Hub verfügt über virtuelle Computer (VMs) als Option für bedarfsgesteuerte und skalierbare Computingressourcen. Sie können virtuelle Computer verwenden, wenn Sie mehr Kontrolle über Ihre Computingumgebung benötigen. Lesen Sie die Informationen in diesem Artikel zum Erstellen Ihres ersten virtuellen Computers.

Ein virtueller Azure Stack Hub-Computer bietet die Flexibilität der Virtualisierung, ohne dass Sie Cluster oder einzelne Computer verwalten müssen. Der virtuelle Computer muss allerdings weiterhin gewartet werden, z. B. durch das Konfigurieren, Patchen/Aktualisieren und Verwalten der darauf ausgeführten Software.

Sie können Azure Stack Hub-VMs auf mehrere Arten verwenden. Beispiel:

- **Entwickeln und Testen** : Mit virtuellen Azure Stack Hub-Computern können Sie einen Computer mit speziellen Konfigurationen erstellen, die zum Programmieren und Testen einer Anwendung erforderlich sind.

- **Anwendungen in der Cloud** : Da die Nutzung Ihrer Anwendung Schwankungen unterliegen kann, ist es unter Umständen wirtschaftlich sinnvoll, sie auf einem virtuellen Computer in Azure Stack Hub auszuführen. Sie bezahlen für zusätzliche virtuelle Computer, wenn Sie sie benötigen, und fahren sie andernfalls einfach herunter.

- **Erweitertes Datencenter** : VMs in einem virtuellen Azure Stack Hub-Netzwerk können mit dem Netzwerk Ihrer Organisation oder mit Azure verbunden werden.

Die virtuellen Computer, die von Ihrer Anwendung genutzt werden, können zur Erfüllung Ihrer Anforderungen hoch- oder aufskaliert werden.

## <a name="before-creating-a-vm"></a>Vor dem Erstellen einer VM

Beim Einrichten einer Anwendungsinfrastruktur in Azure Stack Hub müssen immer Designaspekte berücksichtigt werden. Stellen Sie daher vor der Erstellung Ihrer Infrastruktur Überlegungen zu folgenden VM-Aspekten an:

- Die Namen Ihrer Anwendungsressourcen
- Die Größe des virtuellen Computers
- Die maximal erstellbare Anzahl virtueller Computer
- Das Betriebssystem für den virtuellen Computer
- Die Konfiguration des virtuellen Computers nach dem Start
- Die zugehörigen Ressourcen, die der virtuelle Computer benötigt

### <a name="names"></a>Namen

Einer VM wird ein Name zugewiesen, und im Betriebssystem wird dafür ein Computername konfiguriert. Der Name eines virtuellen Computers kann bis zu 15 Zeichen lang sein.

Wenn Sie den Betriebssystem-Datenträger mithilfe von Azure Stack Hub erstellen, sind der Computername und der Name der VM identisch. Falls Sie ein eigenes Image hochladen und verwenden, das ein vorab konfiguriertes Betriebssystem enthält, und auf dessen Grundlage eine VM erstellen, können die Namen unterschiedlich sein. Beim Hochladen einer eigenen Imagedatei besteht die empfohlene bewährte Methode darin, für den Computernamen im Betriebssystem und für den Namen der VM eine identische Zeichenfolge zu verwenden.

### <a name="vm-size"></a>Größe des virtuellen Computers

Die Größe des virtuellen Computers richtet sich nach der Workload, die Sie ausführen möchten. Von der gewählten Größe hängen Faktoren wie Rechenleistung, Arbeitsspeicher und Speicherplatz ab. Azure Stack Hub verfügt über unterschiedliche Arten von Größen zur Unterstützung vieler Verwendungsarten.

### <a name="vm-limits"></a>Grenzwerte für virtuelle Computer

Für Ihr Abonnement gelten standardmäßig bestimmte Kontingentgrenzen, die die Bereitstellung von virtuellen Computern für Ihr Projekt beeinträchtigen können. Der derzeitige Grenzwert pro Abonnement liegt bei 20 VMs pro Region.

### <a name="operating-system-disks-and-images"></a>Betriebssystem-Datenträger und Images

Virtuelle Computer in Azure Stack Hub sind auf das VHD-/VHDX-Format (virtuelle Festplatten der 1. Generation) beschränkt. VHDs können zum Speichern des Computerbetriebssystems und von Daten verwendet werden. VHDs werden auch für die Images verwendet, die Sie zum Installieren eines Betriebssystems verwenden. Azure Stack Hub bietet einen Marketplace für verschiedene Versionen und Arten von Betriebssystemen. Marketplace-Images werden anhand von Herausgeber, Angebot, SKU und Version identifiziert (üblicherweise wird die aktuelle Version mit **latest** gekennzeichnet).

In der folgenden Tabelle ist angegeben, wie Sie die Informationen zu einem Image finden:

|Methode|BESCHREIBUNG|
|---------|---------|
|Azure Stack Hub-Portal|Die Werte werden automatisch angegeben, wenn Sie ein zu verwendendes Image auswählen.|
|Azure Stack Hub PowerShell|`Get-AzVMImagePublisher -Location "location"`<br>`Get-AzVMImageOffer -Location "location" -Publisher "publisherName"`<br>`Get-AzVMImageSku -Location "location" -Publisher "publisherName" -Offer "offerName"`|
|REST-APIs     |[List image publishers](/rest/api/compute/platformimages/platformimages-list-publishers) (Imageherausgeber auflisten)<br>[List image offers](/rest/api/compute/platformimages/platformimages-list-publisher-offers) (Imageangebote auflisten)<br>[List image SKUs](/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus) (Image-SKUs auflisten)|

Sie können ein eigenes Image hochladen und verwenden. In diesem Fall werden Herausgebername, Angebot und SKU nicht verwendet.

### <a name="extensions"></a>Erweiterungen

Mit VM-Erweiterungen können Sie den Funktionsumfang Ihres virtuellen Computers mittels Konfiguration nach der Bereitstellung sowie mittels automatisierter Aufgaben erweitern. Mit Erweiterungen können Sie folgende allgemeine Aufgaben umsetzen:

- **Ausführen benutzerdefinierter Skripts** : Die Erweiterung für benutzerdefinierte Skripts unterstützt Sie beim Konfigurieren von Workloads auf dem virtuellen Computer, indem beim Bereitstellen des virtuellen Computers Ihr Skript ausgeführt wird.

- **Bereitstellen und Verwalten von Konfigurationen** : Die PowerShell-Erweiterung zum Konfigurieren des gewünschten Zustands (Desired State Configuration, DSC) unterstützt Sie beim Einrichten der Konfiguration des gewünschten Zustands auf einem virtuellen Computer, um Konfigurationen und Umgebungen zu verwalten.

- **Sammeln von Diagnosedaten** : Die Azure-Diagnoseerweiterung unterstützt Sie dabei, den virtuellen Computer für die Sammlung von Diagnosedaten zu konfigurieren, die zum Überwachen der Integrität Ihrer Anwendung verwendet werden können.

### <a name="related-resources"></a>Zugehörige Ressourcen

Die Ressourcen in der folgenden Tabelle werden vom virtuellen Computer verwendet und müssen beim Erstellen des virtuellen Computers vorhanden sein oder erstellt werden:

|Resource|Erforderlich|BESCHREIBUNG|
|---------|---------|---------|
|Resource group|Ja|Der virtuelle Computer muss sich in einer Ressourcengruppe befinden.|
|Speicherkonto|Nein|Bei Verwendung verwalteter Datenträger benötigt die VM das Speicherkonto nicht, um die virtuellen Festplatten zu speichern. |
|Virtuelles Netzwerk|Ja|Der virtuelle Computer muss einem virtuellen Netzwerk angehören.|
|Öffentliche IP-Adresse|Nein|Für den Remotezugriff kann dem virtuellen Computer eine öffentliche IP-Adresse zugewiesen werden.|
|Netzwerkschnittstelle|Ja|Der virtuelle Computer benötigt die Netzwerkschnittstelle für die Kommunikation im Netzwerk.|
|Datenträger|Nein|Der virtuelle Computer kann Datenträger enthalten, um die Speicherkapazität zu erhöhen.|

## <a name="create-your-first-vm"></a>Erstellen Ihres ersten virtuellen Computers

Es gibt mehrere Methoden zum Erstellen einer VM. Welche Möglichkeit Sie auswählen, richtet sich nach Ihrer Umgebung. Die folgende Tabelle enthält hilfreiche Informationen zur Erstellung Ihres ersten virtuellen Computers:

|Methode|Artikel|
|---------|---------|
|Azure Stack Hub-Portal|[Erstellen einer Windows-VM mit dem Azure Stack Hub-Portal](azure-stack-quick-windows-portal.md).<br>[Erstellen einer Linux-VM mit dem Azure Stack Hub-Portal](azure-stack-quick-linux-portal.md).|
|Vorlagen|Azure Stack Hub-Schnellstartvorlagen befinden sich hier:<br> [https://github.com/Azure/AzureStack-QuickStart-Templates](https://aka.ms/aa6z60s)|
|PowerShell|[Erstellen einer Windows-VM in Azure Stack Hub mit PowerShell](azure-stack-quick-create-vm-windows-powershell.md)<br>[Erstellen einer Linux-VM in Azure Stack Hub mit PowerShell](azure-stack-quick-create-vm-linux-powershell.md)|
|Befehlszeilenschnittstelle (CLI)|[Erstellen einer Windows-VM in Azure Stack Hub mit der CLI](azure-stack-quick-create-vm-windows-cli.md)<br>[Erstellen einer Linux-VM in Azure Stack Hub mit der CLI](azure-stack-quick-create-vm-linux-cli.md)|

## <a name="manage-your-vm"></a>Verwalten Ihres virtuellen Computers

Sie können virtuelle Computer über ein browserbasiertes Portal, über Befehlszeilentools mit Skriptunterstützung oder direkt über APIs verwalten. Zu den typischen Verwaltungsaufgaben zählen unter anderem:

- Abrufen von Informationen zu einem virtuellen Computer.
- Herstellen einer Verbindung mit einem virtuellen Computer.
- Verwalten der Verfügbarkeit.
- Erstellen von Sicherungskopien.

### <a name="get-information-about-your-vm"></a>Abrufen von Informationen zu Ihrem virtuellen Computer

Die folgende Tabelle zeigt einige der Methoden, mit denen Sie Informationen zu einem virtuellen Computer abrufen können:

|Methode|BESCHREIBUNG|
|---------|---------|
|Azure Stack Hub-Portal|Klicken Sie im Hub-Menü auf **Virtual Machines** , und wählen Sie dann in der Liste den gewünschten virtuellen Computer aus. Auf der Seite des virtuellen Computers finden Sie Übersichtsinformationen, Einstellungswerte und Überwachungsmetriken.|
|Azure PowerShell|Die Verwaltung von virtuellen Computern erfolgt in Azure und Azure Stack Hub auf die gleiche Weise. Weitere Informationen zum Verwenden von PowerShell finden Sie in dem Azure-Thema [Erstellen und Verwalten von virtuellen Windows-Computern mit dem Azure PowerShell-Modul](/azure/virtual-machines/windows/tutorial-manage-vm#understand-vm-sizes).|
|Client-SDKs|Die Verwendung von C# zur Verwaltung von virtuellen Computern erfolgt in Azure und Azure Stack Hub auf die gleiche Weise. Weitere Informationen finden Sie unter [Erstellen und Verwalten von virtuellen Windows-Computern in Azure mithilfe von C#](/azure/virtual-machines/windows/csharp).|

### <a name="connect-to-your-vm"></a>Herstellen einer Verbindung mit Ihrer VM

Sie können die Option **Verbinden** im Azure Stack Hub-Portal verwenden, um eine Verbindung mit Ihrem virtuellen Computer herzustellen.

## <a name="next-steps"></a>Nächste Schritte

- [Überlegungen zur Verwendung von virtuellen Computern in Azure Stack Hub](azure-stack-vm-considerations.md)
