---
title: Updates und Upgrades
description: Eine Übersicht zur Anwendung von Updates und Upgrades auf Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: f56853ce64575d32ae10ccf7ac2aa0a1a640ec5b
ms.sourcegitcommit: 0e52f460295255b799bac92b40122a22bf994e27
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/21/2020
ms.locfileid: "86868083"
---
# <a name="updates-and-upgrades"></a>Updates und Upgrades

> Gilt für: Azure Stack HCI, Version 20H2

Azure Stack HCI-Lösungen sind für ein vorhersagbares Update- und Upgradeverhalten ausgelegt.

## <a name="support-period"></a>Supportzeitraum

Sie können Hardwareservice, Support und Sicherheitsupdates von Ihrem Microsoft Hardware Solution Partner für einen Zeitraum von mindestens fünf Jahren erwarten. Das Azure Stack HCI-Betriebssystem erhält zwei Jahre lang Support von Microsoft mit jährlichen Funktionsupdates, für deren Bereitstellung dem Kunden ein Jahr lang Zeit bleibt.

:::image type="content" source="media/updates/lifecycle-versions.png" alt-text="Azure Stack HCI – Übersicht der jährlichen Versionen" border="false":::

## <a name="updating-azure-stack-hci"></a>Aktualisieren von Azure Stack HCI

Wie Windows 10 wird auch Azure Stack HCI mithilfe von monatlichen Qualitäts- und Sicherheitsupdates von Microsoft jederzeit auf dem aktuellen Stand gehalten. Sie leiten die Updates mithilfe der clusterfähigen Aktualisierung ein, wie auch bei Windows Server.

Hardwarepartner und Lösungsentwickler können sich in das Windows Admin Center integrieren und Erweiterungen entwickeln, um Firmware, Treiber und BIOS von Servern auf dem aktuellen Stand und zwischen allen Clusterknoten konsistent zu halten. Kunden, die ein integriertes System mit bereits installiertem Azure Stack HCI erwerben, können Lösungsupdates über diese Erweiterungen installieren. Kunden, die einfach überprüfte Hardwareknoten kaufen, müssen die Updates möglicherweise nach den Empfehlungen des Hardwareanbieters separat durchführen.

Virtuelle Computer müssen ebenfalls regelmäßig aktualisiert werden. Über Windows Update und Windows Server Update Services hinaus können Sie die Azure-Updateverwaltung zum Aktualisieren von VMs verwenden.

## <a name="upgrading-to-azure-stack-hci"></a>Durchführen eines Upgrades auf Azure Stack HCI

Sie können ein Upgrade von Windows Server 2019 auf Azure Stack HCI durchführen, allerdings nicht nahtlos. Zurzeit ist kein In-Place-Upgrade verfügbar. Für das Durchführen von Upgrades sind ein Wartungsfenster und eine Neuinstallation auf jedem Server erforderlich, und jeder Knoten wird einzeln im Rahmen eines rollierenden Clusterupgrades neu aufgebaut.

## <a name="cluster-aware-updating"></a>Clusterfähiges Aktualisieren

Für das Update eines Clusters ist Orchestrierung erforderlich, da einige Updates einen Neustart des Betriebssystems voraussetzen und die Server einer nach dem anderen neu gestartet werden müssen, um die Verfügbarkeit des Clusters zu gewährleisten. Die Clusterfähige Aktualisierung (Cluster-Aware Updating, CAU) ist der Standard-Clusterorchestrator von Microsoft, der die Ausführung von Updates entweder über die integrierte Update-Benutzeroberfläche in Windows Admin Center oder manuell mithilfe von PowerShell-Befehlen ermöglicht. Die clusterfähige Aktualisierung ist erweiterbar und unterstützt Partner-Plug-Ins zum Abrufen von Firmware- und Treiberupdates.

## <a name="next-steps"></a>Nächste Schritte

Verwandte Informationen finden Sie außerdem unter:

- [Aktualisieren von Azure Stack HCI-Clustern](../manage/update-cluster.md)
