---
title: Lastenausgleich virtueller Computer
description: In diesem Thema erfahren Sie, wie Sie die Lastenausgleichsfunktion für VMs in Azure Stack HCI und Windows Server konfigurieren.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 1/14/2021
ms.openlocfilehash: 9caf8efdd8db46479cce3c5c4299fb5588c7d37a
ms.sourcegitcommit: 51ce5ba6cf0a377378d25dac63f6f2925339c23d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98224755"
---
# <a name="virtual-machine-load-balancing"></a>Lastenausgleich virtueller Computer

> Gilt für: Azure Stack HCI, Version 20H2; Windows Server 2019; Windows Server 2016

Ein wichtiger Aspekt bei HCI-Bereitstellungen ist der Kapitalaufwand (Capital Expenditure, CapEx), der für den Einstieg in die Produktion erforderlich ist. Es ist üblich, hier Redundanz vorzusehen, um Unterkapazität bei Datenverkehrsspitzen in der Produktion zu vermeiden, dies erhöht jedoch die CapEx. Diese Redundanz ist oftmals erforderlich, da auf einigen Servern im Cluster mehr (VMs) gehostet sind, während andere unterausgelastet sind.

VM-Lastenausgleich ist in Azure Stack HCI, Windows Server 2019 und Windows Server 2016 standardmäßig aktiviert und stellt eine Funktion dar, die es Ihnen ermöglicht, die Serverauslastung in Ihren Clustern zu optimieren. Sie erkennt überbelegte Server und migriert VMs zur Laufzeit von diesen Servern zu den unterbelegten Servern. Fehlerrichtlinien wie Antiaffinität, Fehlerdomänen (Websites) und mögliche Besitzer werden berücksichtigt.

Der VM-Lastenausgleich bewertet die Auslastung eines Servers auf der Grundlage der folgenden Heuristik:

- **Aktuelle Arbeitsspeicherauslastung:** Arbeitsspeicher ist die häufigste Ressourceneinschränkung auf einem Hyper-V-Host.
- **Durchschnittliche CPU-Auslastung, über ein Fünf-Minuten-Zeitfenster gemittelt:** Entschärft eine sich entwickelnde Überbelegung aller Server im Cluster.

## <a name="how-does-vm-load-balancing-work"></a>Wie funktioniert der VM-Lastenausgleich?

Der VM-Lastenausgleich wird automatisch ausgeführt, wenn Sie dem Cluster einen neuen Server hinzufügen. Er kann auch für regelmäßigen, wiederkehrenden Lastenausgleich konfiguriert werden.

### <a name="when-a-new-server-is-added-to-a-cluster"></a>Beim Hinzufügen eines neuen Servers zu einem Cluster

Wenn Sie Ihrem Cluster einen neuen Server hinzufügen, gleicht die Funktion für den VM-Lastenausgleich automatisch die Kapazität der vorhandenen Server in folgender Reihenfolge mit dem neu hinzugefügten Server ab:

1. Die Arbeitsspeicherauslastung und die CPU-Auslastung werden auf den vorhandenen Servern im Cluster ausgewertet.
2. Alle Server werden identifiziert, die den Schwellenwert überschreiten.
3. Die Server mit der höchsten Arbeitsspeicherauslastung und CPU-Auslastung werden ermittelt, um die Priorität des Ausgleichs zu ermitteln.
4. VMs werden im Betrieb von einem Server, der den Schwellenwert überschreitet, auf den neu hinzugefügten Server im Cluster migriert (ohne Ausfallzeit).

:::image type="content" source="media/vm-load-balancing/server-added.png" alt-text="Abbildung, die das Hinzufügen eines neuen Servers zu einem Cluster zeigt" border="false"::: 

### <a name="recurring-load-balancing"></a>Wiederkehrender Lastenausgleich

Standardmäßig ist der VM-Lastenausgleich für den regelmäßigen Ausgleich konfiguriert: Die Arbeitsspeicherauslastung und CPU-Auslastung werden auf jedem Server im Cluster alle 30 Minuten zum Zweck des Ausgleichs bewertet. Dies ist die Abfolge der Schritte:

1. Die Arbeitsspeicherauslastung und CPU-Auslastung werden auf allen Servern im Cluster ausgewertet.
2. Alle Server, die den Schwellenwert überschreiten, und alle Server unterhalb des Schwellenwerts werden identifiziert.
3. Die Server mit der höchsten Arbeitsspeicherauslastung und CPU-Auslastung werden ermittelt, um die Priorität des Ausgleichs zu ermitteln.
4. VMs werden live von einem Server, der den Schwellenwert überschreitet, auf einen anderen Server migriert, der unterhalb des unteren Schwellenwerts liegt (ohne Ausfallzeit).

:::image type="content" source="media/vm-load-balancing/periodic-balancing.png" alt-text="Abbildung, die den automatischen Neuausgleich eines Live-Clusters darstellt" border="false"::: 

## <a name="configure-vm-load-balancing-using-windows-admin-center"></a>Konfigurieren des VM-Lastenausgleichs mithilfe von Windows Admin Center

Konfigurieren des VM-Lastenausgleichs im Windows Admin Center 

:::image type="content" source="media/vm-load-balancing/vm-load-balancing.png" alt-text="Konfigurieren des VM-Lastenausgleichs mithilfe von Windows Admin Center" lightbox="media/vm-load-balancing/vm-load-balancing.png":::

1. Stellen Sie eine Verbindung mit Ihrem Cluster her, und wechseln Sie zu **Tools > Einstellungen**.

2. Wählen Sie unter **Einstellungen** **Lastenausgleich virtueller Computer** aus.

3. Wählen Sie unter **Balance virtual machines** (Virtuelle Computer ausgleichen) **Always** (Immer) aus, um einen Lastenausgleich beim Hinzufügen eines Servers zum Cluster und anschließend alle 30 Minuten durchzuführen, **Server joins** (Beitritt von Servern), um den Lastenausgleich nur beim Hinzufügen eines Servers zu Cluster auszuführen, oder **Never** (Niemals), um die Funktion für den VM-Lastenausgleich zu deaktivieren. Der Standardwert für diese Einstellung ist **Immer**.

4. Wählen Sie unter **Aggressiveness** (Aggressivität) **Low** (Gering) aus, um VMs im Betrieb zu migrieren, wenn der Server zu mehr als 80 % ausgelastet ist, **Medium** (Mittel), um die Migration auszuführen, wenn der Server zu mehr als 70 % ausgelastet ist, oder **High** (Hoch), um den Durchschnitt der Server im Cluster zu bilden und dann zu migrieren, wenn der Server mehr als 5 % über dem Durchschnitt liegt. Die Standardeinstellung ist **Low** (Gering).

## <a name="configure-vm-load-balancing-using-windows-powershell"></a>Konfigurieren des VM-Lastenausgleichs mithilfe von Windows PowerShell

Mithilfe der allgemeinen Clustereigenschaft `AutoBalancerMode` können Sie konfigurieren, ob und wann ein Lastenausgleich durchgeführt wird. Zum Steuern des Zeitpunkts für den Ausgleich des Clusters führen Sie den folgenden Befehl in PowerShell aus, und setzen Sie dabei einen der Werte aus der Tabelle unten ein:

```PowerShell
(Get-Cluster).AutoBalancerMode = <value>
```

|AutoBalancerMode |Verhalten|
|-----------------|-----------|
| 0 | Disabled |
| 1 | Lastenausgleich beim Hinzufügen eines Servers |
| 2 (Standardwert) | Lastenausgleich beim Hinzufügen eines Servers und alle 30 Minuten |

Mithilfe der allgemeinen Clustereigenschaft `AutoBalancerLevel` können Sie außerdem die Aggressivität des Ausgleichs konfigurieren. Zum Steuern des Schwellenwerts für die Aggressivität führen Sie den folgenden Befehl in PowerShell aus und setzen einen Wert aus der Tabelle unten ein:

```PowerShell
(Get-Cluster).AutoBalancerLevel = <value>
```

| AutoBalancerLevel | Aggressivität | Verhalten |
|-------------------|----------------|----------|
| 1 (Standard) | Niedrig | Verschieben, wenn der Host zu mehr als 80 % ausgelastet ist |
| 2 | Medium | Verschieben, wenn der Host zu mehr als 70 % ausgelastet ist |
| 3 | High | Den Durchschnitt aller Server im Cluster ermitteln und verschieben, wenn der Host 5 % höher als der Durchschnitt ausgelastet ist |

Wenn Sie überprüfen möchten, wie die Eigenschaften `AutoBalancerLevel` und `AutoBalancerMode` festgelegt sind, führen Sie in PowerShell den folgenden Befehl aus:

```PowerShell
Get-Cluster | fl AutoBalancer*
```

## <a name="next-steps"></a>Nächste Schritte

Verwandte Informationen:

- [Verwalten von VMs](vm.md)
- [Verwalten von VMs mit PowerShell](vm-powershell.md)
- [Erstellen von VM-Affinitätsregeln](vm-affinity.md)
