---
title: Beheben von Problemen bei Clustervalidierungsberichten
description: Beheben von Problemen bei Clustervalidierungsberichten und Validieren der Konfiguration von QoS-Einstellungen für Azure Stack HCI-Cluster
author: khdownie
ms.topic: troubleshooting
ms.date: 07/21/2020
ms.author: v-kedow
ms.reviewer: JasonGerend
ms.openlocfilehash: c4da92a6d88a3d2046ee6136f2481ac23e5bd476
ms.sourcegitcommit: 0e52f460295255b799bac92b40122a22bf994e27
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/21/2020
ms.locfileid: "86868001"
---
# <a name="troubleshoot-cluster-validation-reporting"></a>Beheben von Problemen bei Clustervalidierungsberichten

> Gilt für: Azure Stack HCI, Version 20H2; Windows Server 2019

In diesem Thema erfahren Sie, wie Sie Probleme bei Clustervalidierungsberichten für Netzwerk- und QoS-Einstellungen (Quality of Service) auf den Servern in einem Azure Stack HCI-Cluster beheben und wie Sie überprüfen, ob wichtige Regeln definiert sind. Damit eine optimale Konnektivität und Leistung sichergestellt ist, überprüft der Clustervalidierungsprozess, ob die QoS-Konfiguration für Data Center Bridging (DCB) konsistent ist und – sofern definiert – geeignete Regeln für Failoverclustering und SMB-/SMB Direct-Datenverkehrsklassen enthält.

## <a name="install-data-center-bridging"></a>Installieren von Data Center Bridging

Data Center Bridging muss installiert sein, damit Sie QoS-spezifische Cmdlets verwenden können. Um zu überprüfen, ob das Data Center Bridging-Feature bereits auf einem Server installiert ist, führen Sie das folgende Cmdlet in PowerShell aus:

```PowerShell
Get-WindowsFeature -Name Data-Center-Bridging -ComputerName Server1
```

Wenn Data Center Bridging nicht installiert ist, führen Sie das folgende Cmdlet auf jedem Server im Cluster aus, um das Feature zu installieren:

```PowerShell
Install-WindowsFeature –Name Data-Center-Bridging -ComputerName Server1
```

## <a name="run-a-cluster-validation-test"></a>Ausführen eines Clustervalidierungstests

Verwenden Sie das Validierungsfeature in Windows Admin Center, indem Sie **Tools > Server > Bestand > Cluster überprüfen** auswählen, oder führen Sie den folgenden PowerShell-Befehl aus:

```PowerShell
Test-Cluster –Node Server1, Server2
```

Der Test überprüft u. a., ob die QoS-Konfiguration für DCB konsistent ist und ob alle Server im Cluster dieselbe Anzahl von Datenverkehrsklassen und QoS-Regeln aufweisen. Es wird auch überprüft, ob auf allen Servern QoS-Regeln für Failoverclustering und SMB-/SMB Direct-Datenverkehrsklassen definiert sind.

Sie können den Validierungsbericht in Windows Admin Center anzeigen oder auf eine Protokolldatei im aktuellen Arbeitsverzeichnis zugreifen. Beispiel: C:\Benutzer\<username>\AppData\Local\Temp\

Am Ende des Berichts finden Sie unter „Validate QoS Settings Configuration“ (Konfiguration der QoS-Einstellungen überprüfen) einen entsprechenden Bericht für jeden Server im Cluster.

Um zu ermitteln, welche Datenverkehrsklassen auf einem Server bereits eingerichtet sind, verwenden Sie das Cmdlet `Get-NetQosTrafficClass`.

Mehr dazu erfahren Sie unter [Überprüfen eines Azure Stack HCI-Clusters](../deploy/validate.md).

## <a name="validate-networking-qos-rules"></a>Validieren der QoS-Regeln für Netzwerkfunktionen

Überprüfen Sie die Konsistenz der Statuseinstellungen für DCB-Bereitschaft (Willing) und Prioritätsflusssteuerung auf den Servern im Cluster.

### <a name="dcb-willing-status"></a>DCB-Bereitschaftsstatus (Willing)

Netzwerkadapter, die das DCBX-Protokoll (Data Center Bridging Capability Exchange) unterstützen, können Konfigurationen von einem Remotegerät akzeptieren. Um diese Funktion zu aktivieren, muss das DCB-Willing-Bit im Netzwerkadapter auf TRUE festgelegt werden. Wenn das Willing-Bit auf FALSE festgelegt ist, lehnt das Gerät alle Konfigurationsversuche von Remotegeräten ab und erzwingt nur die lokalen Konfigurationen. Wenn Sie RoCE-Adapter (RDMA over Converged Ethernet) verwenden, muss das Willing-Bit auf allen Servern auf FALSE festgelegt werden.

Das DCB-Willing-Bit muss auf allen Servern in einem Azure Stack HCI-Cluster die gleiche Einstellung aufweisen.

Verwenden Sie das Cmdlet `Set-NetQosDcbxSetting`, um das DCB-Willing-Bit auf TRUE oder FALSE festzulegen, wie in folgendem Beispiel gezeigt:

```PowerShell
Set-NetQosDcbxSetting –Willing $false
```

### <a name="dcb-flow-control-status"></a>Status der DCB-Flusssteuerung

Eine prioritätsbasierte Flusssteuerung ist essenziell, wenn das Protokoll der höheren Ebene, wie z. B. Fibre Channel, einen verlustfreien zugrunde liegenden Transport annimmt. Die DCB-Flusssteuerung kann entweder global oder für einzelne Netzwerkadapter aktiviert oder deaktiviert werden. Wenn sie aktiviert ist, ermöglicht sie die Erstellung von QoS-Richtlinien, die bestimmten Anwendungsdatenverkehr priorisieren.

Damit QoS-Richtlinien während eines Failovers nahtlos funktionieren, müssen alle Server in einem Azure Stack HCI-Cluster dieselben Statuseinstellungen für die Flusssteuerung aufweisen. Wenn Sie RoCE-Adapter verwenden, muss die Prioritätsflusssteuerung auf allen Servern aktiviert sein.

Verwenden Sie das Cmdlet `Get-NetQosFlowControl`, um die aktuelle Konfiguration der Flusssteuerung abzurufen. Alle Prioritäten sind standardmäßig deaktiviert.

Verwenden Sie die Cmdlets `Enable-NetQosFlowControl` und `Disable-NetQosFlowControl` mit dem Parameter „-priority“, um die Prioritätsflusssteuerung zu aktivieren oder zu deaktivieren. Der folgende Befehl z. B. aktiviert die Flusssteuerung für Datenverkehr, der mit der Priorität 3 gekennzeichnet ist:

```PowerShell
Enable-NetQosFlowControl –Priority 3
```

## <a name="validate-storage-qos-rules"></a>Validieren von QoS-Regeln für Speicher

Überprüfen Sie, ob alle Knoten über eine QoS-Regel für das Failoverclustering und für SMB oder SMB Direct verfügen. Wenn dies nicht der Fall ist, können Konnektivitäts- und Leistungsprobleme auftreten.

### <a name="qos-rule-for-failover-clustering"></a>QoS-Regel für Failoverclustering

**Wenn** in einem Cluster QoS-Regeln für Speicher definiert sind, muss eine QoS-Regel für das Failoverclustering vorhanden sein, da sonst Konnektivitätsprobleme auftreten können. Um eine neue QoS-Regel für das Failoverclustering hinzuzufügen, verwenden Sie das Cmdlet `New-NetQosPolicy`, wie in folgendem Beispiel gezeigt:

```PowerShell
New-NetQosPolicy "Cluster" -IPDstPort 3343 -Priority 6
```

### <a name="qos-rule-for-smb"></a>QoS-Regel für SMB

Wenn für einige oder alle Knoten QoS-Regeln definiert sind, aber keine QoS-Regel für SMB vorhanden ist, kann dies zu Konnektivitäts- und Leistungsproblemen bei SMB führen. Um eine neue QoS-Netzwerkregel für SMB hinzuzufügen, verwenden Sie das Cmdlet `New-NetQosPolicy`, wie in folgendem Beispiel gezeigt:

```PowerShell
New-NetQosPolicy -Name "SMB" -SMB -PriorityValue8021Action 3
```

### <a name="qos-rule-for-smb-direct"></a>QoS-Regel für SMB Direct

SMB Direct umgeht den Netzwerkstapel und verwendet stattdessen RDMA-Methoden zum Übertragen von Daten. Wenn für einige oder alle Knoten QoS-Regeln definiert sind, aber keine QoS-Regel für SMB Direct vorhanden ist, kann dies zu Konnektivitäts- und Leistungsproblemen bei SMB Direct führen. Um eine neue QoS-Richtlinie für SMB Direct zu erstellen, führen Sie die folgenden Befehle aus:

```PowerShell
New-NetQosPolicy "SMB Direct" –NetDirectPort 445 –Priority 3
```

## <a name="next-steps"></a>Nächste Schritte

Verwandte Informationen:

- [Data Center Bridging](/windows-server/networking/technologies/dcb/dcb-top)
- [Verwalten von Data Center Bridging](/windows-server/networking/technologies/dcb/dcb-manage)
- [Gängige QoS-Konfigurationen](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/jj735302(v=ws.11))