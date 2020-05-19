---
title: Behandeln von Problemen mit virtuellen Netzwerkgeräten in Azure Stack Hub
description: Beheben Sie Probleme und Fehler mit der VM- oder VPN-Konnektivität bei der Verwendung eines virtuellen Netzwerkgeräts (Network Virtual Appliance, NVA) eines Drittanbieters in Microsoft Azure Stack Hub.
author: sethmanheim
ms.author: sethm
ms.date: 05/12/2020
ms.topic: article
ms.reviewer: sranthar
ms.lastreviewed: 05/12/2020
ms.openlocfilehash: 5e42f9e1b099df2ab1dc1063b3bf2710f59ad66f
ms.sourcegitcommit: 999c6cd0ab64cd2d695feb8405a9c720c9ae755b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83342945"
---
# <a name="troubleshoot-network-virtual-appliance-issues"></a>Behandeln von Problemen mit virtuellen Netzwerkgeräten

Bei der Verwendung eines virtuellen Netzwerkgeräts (Network Virtual Appliance, NVA) eines Drittanbieters in Microsoft Azure Stack Hub treten unter Umständen Probleme und Fehler mit der VM- oder VPN-Konnektivität auf. Dieser Artikel enthält die wesentlichen Schritte, um grundlegende Anforderungen an die Azure Stack Hub-Plattform für NVA-Konfigurationen zu überprüfen.

Technischer Support für NVAs von Drittanbietern und ihre Integration in die Azure Stack Hub-Plattform wird vom NVA-Anbieter bereitgestellt.

> [!NOTE]
> Bei einem Konnektivitäts- oder Routingproblem, das eine NVA betrifft, sollten Sie [sich direkt an den Anbieter der NVA](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) wenden.

Wenn das Problem mit dem Azure Stack Hub-NVA in diesem Artikel nicht behandelt wird, erstellen Sie ein [Azure Stack Hub-Supportticket](../operator/azure-stack-manage-basics.md#where-to-get-support).

## <a name="checklist-for-troubleshooting-with-nva-vendor"></a>Checkliste für die Problembehandlung mit NVA-Anbietern

- Softwareupdates für NVA-VM-Software
- Einrichtung und Funktionalität von Dienstkonten
- Benutzerdefinierte Routen (User-Defined Routes, UDRs) in Subnetzen virtueller Netzwerke, die Datenverkehr an das NVA weiterleiten
- UDRs in Subnetzen virtueller Netzwerke, die Datenverkehr von einem NVA weiterleiten
- Weiterleiten von Tabellen und Regeln im NVA (z. B. von NIC1 an NIC2)
- Ablaufverfolgung für NVA-NICs, um das Empfangen und Senden von Netzwerkdatenverkehr zu überprüfen

## <a name="basic-troubleshooting-steps"></a>Grundlegende Schritte zur Problembehandlung

- Überprüfen der grundlegenden Konfiguration
- Überprüfen der NVA-Leistung
- Erweiterte Behandlung von Netzwerkproblemen

## <a name="check-the-minimum-configuration-requirements-for-nvas-on-azure"></a>Überprüfen der Mindestanforderungen für die Konfiguration von NVAs in Azure

Für jedes NVA müssen grundlegende Konfigurationsanforderungen erfüllt werden, damit es ordnungsgemäß in Azure Stack Hub ausgeführt werden kann. Der folgende Abschnitt enthält die Schritte zum Überprüfen dieser grundlegenden Konfigurationen. Weitere Informationen [erhalten Sie beim Anbieter der NVA](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

> [!IMPORTANT]
> Bei Verwendung eines S2S-Tunnels werden Pakete mit zusätzlichen Headern weiter gekapselt. Durch diese Kapselung vergrößert sich das Paket insgesamt. In diesen Szenarien müssen Sie TCP **MSS** mit **1350** verknüpfen. Wenn Ihre VPN-Geräte MSS-Clamping nicht unterstützen, können Sie stattdessen den MTU-Wert der Tunnelschnittstelle auf **1400** Bytes festlegen. Weitere Informationen finden Sie unter [Optimieren der TCP-/IP-Leistung von virtuellen Netzwerken](/azure/virtual-network/virtual-network-tcpip-performance-tuning).

### <a name="check-whether-ip-forwarding-is-enabled-on-nva"></a>Überprüfen, ob die IP-Weiterleitung für das NVA aktiviert ist

#### <a name="use-the-azure-stack-hub-portal"></a>Verwenden des Azure Stack Hub-Portals

- Suchen Sie die NVA-Ressource im Azure Stack Hub-Portal, und wählen Sie **Netzwerke** und dann die Netzwerkschnittstelle aus.
- Wählen Sie auf der Seite **Netzwerkschnittstelle** die Option **IP-Konfiguration** aus.
- Stellen Sie sicher, dass IP-Weiterleitung aktiviert ist.

#### <a name="use-powershell"></a>Verwenden von PowerShell

- Führen Sie den folgenden Befehl aus (ersetzen Sie die in Klammern stehenden Werte durch Ihre Informationen):

   ```powershell
   Get-AzureRMNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NIC name>
   ```

- Überprüfen Sie die Eigenschaft **EnableIPForwarding**.
- Wenn die IP-Weiterleitung nicht aktiviert ist, führen Sie die folgenden Befehle aus, um diese zu aktivieren:

   ```powershell
   $nic2 = Get-AzureRMNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NIC name>
   $nic2.EnableIPForwarding = 1
   Set-AzureRMNetworkInterface -NetworkInterface $nic2
   Execute: $nic2 #and check for an expected output:
   EnableIPForwarding   : True
   NetworkSecurityGroup : null
   ```

### <a name="check-whether-the-traffic-can-be-routed-to-the-nva"></a>Überprüfen, ob der Datenverkehr an die NVA weitergeleitet werden kann

- Suchen Sie einen virtuellen Computers, der zum Umleiten des Datenverkehrs an das virtuelle Netzwerkgerät konfiguriert ist.
- Führen Sie unter Windows `Tracert <Private IP of NVA>` aus, oder führen Sie `Traceroute <Private IP of NVA>` aus, um zu überprüfen, ob es sich beim nächsten Hop um ein virtuelles Netzwerkgerät handelt.
- Wenn das NVA nicht als nächster Hop aufgeführt ist, überprüfen und aktualisieren Sie die Azure Stack Hub-Routingtabellen.

Einige Betriebssysteme auf Gastebene verfügen möglicherweise über Firewallrichtlinien zum Blockieren von ICMP-Datenverkehr. Diese Firewallregeln müssen aktualisiert werden, damit die vorangehenden Befehle funktionieren.

### <a name="check-whether-the-traffic-can-reach-the-nva"></a>Überprüfen, ob der Datenverkehr das NVA erreichen kann

- Suchen Sie einen virtuellen Computer, der mit dem NVA verbunden sein sollte.
- Führen Sie unter Windows `ping (ICMP)` oder `Test-NetConnection (TCP) <Private IP of NVA>` und unter Linux `Tcpping <Private IP of NVA>` aus, um zu überprüfen, ob der Datenverkehr von Netzwerksicherheitsgruppen blockiert wird.
- Wird der Datenverkehr blockiert, ändern Sie die NSGs, um Datenverkehr zuzulassen.

### <a name="check-whether-nva-and-vms-are-listening-for-expected-traffic"></a>Überprüfen, ob das NVA und die VMs auf erwarteten Datenverkehr lauschen

- Stellen Sie über RDP oder SSH eine Verbindung mit der NVA her, und führen Sie dann folgenden Befehl aus:

   **Windows**:
   ```shell
   netstat -an
   ```

   **Linux:**
   ```shell
   netstat -an | grep -i listen
   ```

- Wenn der TCP-Port, der von der in den Ergebnissen aufgeführten NVA-Software verwendet wird, nicht angezeigt wird, müssen Sie die Anwendung auf dem NVA und dem virtuellen Computer so konfigurieren, dass er auf Datenverkehr lauscht und antwortet, der an diesen Ports eingeht. [Wenden Sie sich an den NVA-Anbieter, wenn Sie Unterstützung benötigen sollten](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

## <a name="check-nva-performance"></a>Überprüfen der NVA-Leistung

### <a name="validate-vm-cpu"></a>Überprüfen der VM-CPU

Wenn die CPU-Auslastung nahezu 100 Prozent erreicht, kann es zu Problemen kommen, die sich auf die Netzwerkpaketverluste auswirken. Untersuchen Sie während einer CPU-Spitze, welcher Prozess auf der Gast-VM für die hohe CPU-Auslastung verantwortlich ist, und beheben Sie dies nach Möglichkeit. Eventuell müssen Sie auch die Größe der VM in eine größere SKU-Größe ändern oder bei einer VM-Skalierungsgruppe die Anzahl der Instanzen erhöhen. [Wenden Sie sich an den NVA-Anbieter](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines), wenn Sie Unterstützung zu einem dieser Probleme benötigen sollten.

### <a name="validate-vm-network-statistics"></a>Überprüfen der VM-Netzwerkstatistik

Wenn das VM-Netzwerk Spitzen oder Zeiträume mit hoher Auslastung aufweist, müssen Sie möglicherweise auch die SKU-Größe der VM erhöhen, um von einem höherem Durchsatz zu profitieren.

## <a name="advanced-network-administrator-troubleshooting"></a>Erweiterte Behandlung von Problemen mit Netzwerkadministratoren

### <a name="capture-network-trace"></a>Erfassen der Netzwerkablaufverfolgung

Erfassen Sie während der Ausführung von [**PsPing**](/sysinternals/downloads/psping) oder **Nmap** eine gleichzeitige Netzwerkablaufverfolgung auf der Quell-VM, der NVA und der Ziel-VM, und beenden Sie dann die Ablaufverfolgung.

- Um eine gleichzeitige Netzwerkablaufverfolgung zu erfassen, führen Sie den folgenden Befehl aus:

   **Windows**

   ```shell
   netsh trace start capture=yes tracefile=c:\server_IP.etl scenario=netconnection
   ```

   **Linux**

   ```shell
   sudo tcpdump -s0 -i eth0 -X -w vmtrace.cap
   ```

- Verwenden Sie **PsPing** oder **Nmap** von der Quell-VM zur Ziel-VM (z.B. `PsPing 10.0.0.4:80` oder `Nmap -p 80 10.0.0.4`).

- Öffnen Sie die Netzwerkablaufverfolgung auf der Ziel-VM, indem Sie **tcpdump** oder eine Paketanalyse Ihrer Wahl verwenden. Wenden Sie einen Anzeigefilter für die IP-Adresse der Quell-VM an, über die Sie **PsPing** oder **Nmap** ausgeführt haben, etwa `IPv4.address==10.0.0.4` (Windows-Netzwerkmonitor) oder `tcpdump -nn -r vmtrace.cap src` bzw. `dst host 10.0.0.4` (Linux).

### <a name="analyze-traces"></a>Analysieren von Ablaufverfolgungen

Falls die bei der Ablaufverfolgung der Back-End-VM eingehenden Pakete nicht zu sehen sind, liegt wahrscheinlich ein Konflikt mit einer fehlerhaften NSG oder UDR oder fehlerhaften NVA-Routingtabellen vor.

Wenn Sie den Eingang der Pakete beobachten, aber keine Antwort erfolgt, müssen Sie möglicherweise ein Problem mit der VM-Anwendung oder der Firewall beheben. [Wenden Sie sich an den NVA-Anbieter](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines), wenn Sie Unterstützung zu einem dieser Probleme benötigen sollten.

### <a name="create-a-support-ticket"></a>Erstellen ein Supporttickets

Wenn das Problem mit keinem der oben genannten Schritte behoben werden kann, erstellen Sie ein [Supportticket](../operator/azure-stack-manage-basics.md#where-to-get-support), und verwenden Sie das [Tool für die bedarfsgesteuerte Protokollsammlung](../operator/azure-stack-configure-on-demand-diagnostic-log-collection.md), um Protokolle bereitzustellen.
