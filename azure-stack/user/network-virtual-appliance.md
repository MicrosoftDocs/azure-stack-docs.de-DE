---
title: Behandeln von Problemen mit virtuellen Netzwerkgeräten in Azure Stack Hub
description: Beheben Sie Probleme mit der VM- oder VPN-Konnektivität bei der Verwendung eines virtuellen Netzwerkgeräts (Network Virtual Appliance, NVA) in Microsoft Azure Stack Hub.
author: sethmanheim
ms.author: sethm
ms.date: 11/22/2020
ms.topic: article
ms.reviewer: sranthar
ms.lastreviewed: 11/22/2020
ms.openlocfilehash: 271587baa3890a7dbb02d7ac935ceb51e2e405b7
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95517147"
---
# <a name="troubleshoot-network-virtual-appliance-problems"></a>Behandeln von Problemen mit virtuellen Netzwerkgeräten

Unter Umständen treten Verbindungsprobleme bei virtuellen Computern oder VPNs auf, die ein virtuelles Netzwerkgerät (Network Virtual Appliance, NVA) in Azure Stack Hub verwenden.

Dieser Artikel hilft bei der Überprüfung der grundlegenden Anforderungen an die Azure Stack Hub-Plattform für NVA-Konfigurationen.

Der Anbieter eines NVA bietet technischen Support für das virtuelle Netzwerkgerät und seine Integration in die Azure Stack Hub-Plattform.

> [!NOTE]
> Bei einem Konnektivitäts- oder Routingproblem, das ein NVA betrifft, sollten Sie sich direkt an den [NVA-Anbieter](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) wenden.

Wird Ihr NVA-Problem mit Azure Stack Hub in diesem Artikel nicht behandelt, erstellen Sie ein [Azure Stack Hub-Supportticket](../operator/azure-stack-manage-basics.md#where-to-get-support).

## <a name="checklist-for-troubleshooting-with-an-nva-vendor"></a>Checkliste für die Problembehandlung mit einem NVA-Anbieter

- Updates für NVA-VM-Software
- Einrichtung und Funktionalität von Dienstkonten
- Benutzerdefinierte Routen (User-Defined Routes, UDRs) in Subnetzen virtueller Netzwerke, die Datenverkehr an das NVA weiterleiten
- UDRs in Subnetzen virtueller Netzwerke, die Datenverkehr von einem NVA weiterleiten
- Weiterleiten von Tabellen und Regeln im NVA (z. B. von NIC1 an NIC2)
- Ablaufverfolgung für NVA-NICs, um das Empfangen und Senden von Netzwerkdatenverkehr zu überprüfen

## <a name="basic-troubleshooting-steps"></a>Grundlegende Schritte zur Problembehandlung

1. Überprüfen der grundlegenden Konfiguration
2. Überprüfen der NVA-Leistung
3. Erweiterte Behandlung von Netzwerkproblemen

## <a name="check-the-minimum-configuration-requirements-for-nvas-on-azure"></a>Überprüfen der Mindestanforderungen für die Konfiguration von NVAs in Azure

Für jedes NVA müssen grundlegende Konfigurationsanforderungen erfüllt werden, damit es ordnungsgemäß in Azure Stack Hub ausgeführt werden kann. Der folgende Abschnitt enthält die Schritte zum Überprüfen dieser grundlegenden Konfigurationen. Weitere Informationen [erhalten Sie vom NVA-Anbieter](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

> [!IMPORTANT]
> Wenn Pakete einen S2S-Tunnel nutzen, werden sie mit zusätzlichen Headern weiter gekapselt. Durch diese Kapselung vergrößern sich die einzelnen Pakete insgesamt.
>
> In diesem Szenario müssen Sie TCP MSS mit 1,350 Byte verknüpfen. Wenn Ihre VPN-Geräte MSS-Clamping nicht unterstützen, können Sie stattdessen den MTU-Wert der Tunnelschnittstelle auf 1.400 Byte festlegen.
>
> Weitere Informationen finden Sie unter [Optimierung der TCP/IP-Leistung für Azure-VMs](/azure/virtual-network/virtual-network-tcpip-performance-tuning).

### <a name="check-whether-ip-forwarding-is-enabled-on-the-nva"></a>Überprüfen, ob die IP-Weiterleitung für das NVA aktiviert ist

### <a name="portal"></a>[Portal](#tab/portal)

1. Suchen Sie die NVA-Ressource im Azure Stack Hub-Portal, und wählen Sie **Netzwerke** und dann die Netzwerkschnittstelle aus.
2. Wählen Sie auf der Seite **Netzwerkschnittstelle** die Option **IP-Konfiguration** aus.
3. Stellen Sie sicher, dass IP-Weiterleitung aktiviert ist.

### <a name="powershell-az"></a>[PowerShell Az](#tab/az)

1. Führen Sie den folgenden Befehl aus. Ersetzen Sie die Werte in spitzen Klammern durch Ihre eigenen Informationen.

   ```powershell
   Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NIC name>
   ```

2. Überprüfen Sie die Eigenschaft **EnableIPForwarding**.

3. Wenn die IP-Weiterleitung nicht aktiviert ist, führen Sie die folgenden Befehle aus, um diese zu aktivieren:

   ```powershell
   $nic2 = Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NIC name>
   $nic2.EnableIPForwarding = 1
   Set-AzNetworkInterface -NetworkInterface $nic2
   Execute: $nic2 #and check for an expected output:
   EnableIPForwarding   : True
   NetworkSecurityGroup : null
   ```

### <a name="powershell-azurerm"></a>[PowerShell AzureRM](#tab/azurerm)

1. Führen Sie den folgenden Befehl aus. Ersetzen Sie die Werte in spitzen Klammern durch Ihre eigenen Informationen.

   ```powershell
   Get-AzureRMNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NIC name>
   ```

2. Überprüfen Sie die Eigenschaft **EnableIPForwarding**.

3. Wenn die IP-Weiterleitung nicht aktiviert ist, führen Sie die folgenden Befehle aus, um diese zu aktivieren:

   ```powershell
   $nic2 = Get-AzureRMNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NIC name>
   $nic2.EnableIPForwarding = 1
   Set-AzureRMNetworkInterface -NetworkInterface $nic2
   Execute: $nic2 #and check for an expected output:
   EnableIPForwarding   : True
   NetworkSecurityGroup : null
   ```

---

### <a name="check-whether-traffic-can-be-routed-to-the-nva"></a>Überprüfen, ob der Datenverkehr an das NVA weitergeleitet werden kann

1. Suchen Sie einen virtuellen Computer, der zum Umleiten des Datenverkehrs an das virtuelle Netzwerkgerät konfiguriert ist.
1. Um zu überprüfen, ob es sich beim NVA um den nächsten Hop handelt, führen Sie `Tracert <Private IP of NVA>`für Windows oder `Traceroute <Private IP of NVA>` aus.
1. Wenn das NVA nicht als nächster Hop aufgeführt ist, überprüfen und aktualisieren Sie die Azure Stack Hub-Routingtabellen.

Einige Betriebssysteme auf Gastebene verfügen möglicherweise über Firewallrichtlinien zum Blockieren von ICMP-Datenverkehr. Aktualisieren Sie diese Firewallregeln, damit die vorangehenden Befehle funktionieren.

### <a name="check-whether-traffic-can-reach-the-nva"></a>Überprüfen, ob der Datenverkehr das NVA erreichen kann

1. Suchen Sie einen virtuellen Computer, der mit dem NVA verbunden sein sollte.
1. Überprüfen Sie, ob Netzwerksicherheitsgruppen (NSGs) Datenverkehr blockieren. Führen Sie unter Windows `ping` (ICMP) oder `Test-NetConnection <Private IP of NVA>` (TCP) aus. Führen Sie unter Linux `Tcpping <Private IP of NVA>` aus.
1. Wenn Ihre NSGs Datenverkehr blockieren, ändern Sie sie so, dass Datenverkehr zugelassen wird.

### <a name="check-whether-the-nva-and-vms-are-listening-for-expected-traffic"></a>Überprüfen, ob das NVA und die VMs auf erwarteten Datenverkehr lauschen

1. Stellen Sie über RDP oder SSH eine Verbindung mit dem NVA her, und führen Sie dann den folgenden Befehl aus:

   **Windows**

   ```shell
   netstat -an
   ```

   **Linux**

   ```shell
   netstat -an | grep -i listen
   ```

1. Suchen Sie nach den TCP-Ports, die von der in den Ergebnissen aufgeführten NVA-Software verwendet werden. Werden sie nicht angezeigt, konfigurieren Sie die Anwendung auf dem virtuellen Netzwerkgerät und dem virtuellen Computer, um auf Datenverkehr zu lauschen und zu reagieren, der diese Ports erreicht. [Wenden Sie sich an den NVA-Anbieter, wenn Sie Unterstützung benötigen.](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)

## <a name="check-nva-performance"></a>Überprüfen der NVA-Leistung

### <a name="validate-vm-cpu-usage"></a>Überprüfen der VM-CPU-Auslastung

Wenn die CPU-Auslastung fast 100 Prozent erreicht, kann es zu Problemen kommen, die sich auf die Netzwerkpaketverluste auswirken.

Untersuchen Sie während einer CPU-Spitze, welcher Prozess auf der Gast-VM für die hohe CPU-Auslastung verantwortlich ist. Beheben Sie dies nach Möglichkeit.

Eventuell müssen Sie auch die Größe der VM in eine größere SKU ändern oder bei einer VM-Skalierungsgruppe die Anzahl der Instanzen erhöhen.

[Wenden Sie sich an den NVA-Anbieter](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines), wenn Sie Unterstützung benötigen sollten.

### <a name="validate-vm-network-statistics"></a>Überprüfen der VM-Netzwerkstatistik

Wenn das VM-Netzwerk Spitzen oder Zeiträume mit hoher Auslastung aufweist, erhöhen Sie ggf. die SKU-Größe der VM, um von einem höherem Durchsatz zu profitieren.

## <a name="advanced-network-administrator-troubleshooting"></a>Erweiterte Behandlung von Problemen mit Netzwerkadministratoren

### <a name="capture-a-network-trace"></a>Erfassen einer Netzwerkablaufverfolgung

Erfassen Sie während der Ausführung von [`PsPing`](/sysinternals/downloads/psping) oder `Nmap` eine gleichzeitige Netzwerkablaufverfolgung auf der Quell- und Ziel-VM und dem NVA. Beenden Sie dann die Ablaufverfolgung.

1. Um eine gleichzeitige Netzwerkablaufverfolgung zu erfassen, führen Sie den folgenden Befehl aus:

   **Windows**

   ```shell
   netsh trace start capture=yes tracefile=c:\server_IP.etl scenario=netconnection
   ```

   **Linux**

   ```shell
   sudo tcpdump -s0 -i eth0 -X -w vmtrace.cap
   ```

2. Verwenden Sie `PsPing` oder `Nmap` von der Quell-VM zur Ziel-VM. Bespiele sind `PsPing 10.0.0.4:80` oder `Nmap -p 80 10.0.0.4`.

3. Öffnen Sie die Netzwerkablaufverfolgung auf der Ziel-VM, indem Sie **tcpdump** oder eine Paketanalyse Ihrer Wahl verwenden. Wenden Sie einen Anzeigefilter für die IP-Adresse der Quell-VM an, über die Sie `PsPing` oder `Nmap` ausgeführt haben. Ein Windows-Beispiel für **netmon** lautet `IPv4.address==10.0.0.4`. Linux-Beispiele sind `tcpdump -nn -r vmtrace.cap src` und `dst host 10.0.0.4`.

### <a name="analyze-traces"></a>Analysieren von Ablaufverfolgungen

Falls die bei der Ablaufverfolgung der Back-End-VM eingehenden Pakete nicht zu sehen sind, liegt wahrscheinlich ein Konflikt mit einer NSG oder UDR oder fehlerhaften NVA-Routingtabellen vor.

Wenn Sie sehen, dass Pakete eingehen, aber keine Antwort enthalten, müssen Sie möglicherweise ein Problem mit einer VM-Anwendung oder -Firewall beheben.

[Wenden Sie sich an den NVA-Anbieter](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines), wenn Sie Unterstützung benötigen sollten.

### <a name="create-a-support-ticket"></a>Erstellen ein Supporttickets

Wenn das Problem mit den oben genannten Schritten nicht behoben werden kann, erstellen Sie ein [Supportticket](../operator/azure-stack-manage-basics.md#where-to-get-support), und verwenden Sie das [Tool für die bedarfsgesteuerte Protokollsammlung](../operator/azure-stack-diagnostic-log-collection-overview.md), um Protokolle bereitzustellen.
