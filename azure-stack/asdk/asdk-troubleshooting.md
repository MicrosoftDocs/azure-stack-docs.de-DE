---
title: Problembehandlung beim ASDK | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Probleme mit dem Azure Stack Development Kit (ASDK) behandeln.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: ab43d94c2e65032e5e525ec000e38cacb01b2980
ms.sourcegitcommit: 1bae55e754d7be75e03af7a4db3ec43fd7ff3e9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71319101"
---
# <a name="troubleshoot-the-asdk"></a>Problembehandlung beim ASDK
Dieser Artikel enthält allgemeine Informationen zur Problembehandlung beim Azure Stack Development Kit (ASDK). Hilfe zu integrierten Azure Stack-Systemen finden Sie unter [Problembehandlung für Microsoft Azure Stack](../operator/azure-stack-troubleshooting.md). 

Da das ASDK eine Evaluierungsumgebung ist, wird vom Microsoft-Kundendienst und -Support (CSS) kein Support geleistet. Falls bei Ihnen nicht dokumentierte Probleme auftreten, können Sie über das [MSDN-Forum für Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) Hilfe von Experten erhalten. 


## <a name="deployment"></a>Bereitstellung
### <a name="deployment-failure"></a>Fehler bei der Bereitstellung
Wenn während der Installation ein Fehler auftritt, können Sie mithilfe der Option „-rerun“ des Bereitstellungsskripts die Bereitstellung ab dem Schritt neu starten, in dem der Fehler aufgetreten ist. Beispiel:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>Am Ende der Bereitstellung ist die PowerShell-Sitzung noch geöffnet, und es wird keine Ausgabe angezeigt.
Dies ist wahrscheinlich nur auf das Standardverhalten des PowerShell-Befehlsfensters zurückzuführen, wenn dieses ausgewählt wurde. Die Bereitstellung des ASDK war erfolgreich, das Skript wurde jedoch beim Auswählen des Fensters angehalten. Sie können überprüfen, ob das Setup abgeschlossen ist, indem Sie in der Titelleiste des Befehlsfensters nach dem Wort „select“ suchen. Drücken Sie die ESC-Taste, um die Auswahl aufzuheben. Danach sollte die Abschlussmeldung angezeigt werden.

## <a name="virtual-machines"></a>Virtuelle Computer
### <a name="default-image-and-gallery-item"></a>Standardimage und Katalogelement
Vor der Bereitstellung virtueller Computer in Azure Stack müssen Sie zuerst ein Windows Server-Image und ein Katalogelement hinzufügen.

### <a name="after-restarting-my-azure-stack-host-some-vms-dont-automatically-start"></a>Nach einem Neustart des Azure Stack-Hosts werden einige virtuelle Computer nicht automatisch gestartet
Nach dem Neustart des Hosts sind die Azure Stack-Dienste möglicherweise nicht sofort verfügbar. Dies ist darauf zurückzuführen, dass [Infrastruktur-VMs](asdk-architecture.md#virtual-machine-roles) von Azure Stack und die RPs etwas Zeit zum Überprüfen der Konsistenz benötigen. Sie werden jedoch letztlich automatisch gestartet.

Es kann auch vorkommen, dass Mandanten-VMs nach einem Neustart des ASDK-Hosts nicht automatisch gestartet werden. Sie können sie mit einigen manuellen Schritten wieder in den Onlinezustand versetzen:

1.  Starten Sie auf dem ASDK-Host im Startmenü den **Failovercluster-Manager**.
2.  Wählen Sie den Cluster **S-Cluster.azurestack.local** aus.
3.  Wählen Sie **Rollen** aus.
4.  Mandanten-VMs werden mit dem Zustand *gespeichert* angezeigt. Sobald alle Infrastruktur-VMs ausgeführt werden, klicken Sie mit der rechten Maustaste auf die Mandanten-VMs, und wählen Sie **Start**, um die Ausführung des virtuellen Computers fortzusetzen.

### <a name="ive-deleted-some-vms-but-still-see-the-vhd-files-on-disk"></a>Ich habe einige VMs gelöscht, aber die VHD-Dateien werden auf dem Datenträger weiterhin angezeigt 
Dieses Verhalten ist beabsichtigt:

* Wenn Sie einen virtuellen Computer löschen, werden die VHDs nicht gelöscht. Datenträger sind separate Ressourcen in der Ressourcengruppe.
* Wenn ein Speicherkonto gelöscht wird, wird der Löschvorgang über Azure Resource Manager sofort angezeigt, die darin enthaltenen Datenträger bleiben jedoch möglicherweise im Speicher erhalten, bis die Garbage Collection ausgeführt wird.

Wenn „verwaiste“ VHD-Dateien angezeigt werden, müssen Sie wissen, ob diese zu einem Ordner für ein gelöschtes Speicherkonto gehören. Wenn das Speicherkonto nicht gelöscht wurde, ist es normal, dass die VHD-Dateien weiterhin bestehen.

Weitere Informationen zum Konfigurieren des Schwellenwerts für die Aufbewahrung und zur bedarfsgesteuerten Anforderung der Freigabe von Speicherplatz finden Sie unter [Manage Storage Accounts](../operator/azure-stack-manage-storage-accounts.md) (Verwalten von Speicherkonten).

## <a name="storage"></a>Storage
### <a name="storage-reclamation"></a>Freigabe von Speicherplatz
Es kann bis zu 14 Stunden dauern, bis freigegebene Kapazität im Portal angezeigt wird. Die Freigabe von Speicherplatz hängt von verschiedenen Faktoren ab, einschließlich der prozentualen Auslastung von internen Containerdateien im Blockblobspeicher. Je nach gelöschter Datenmenge besteht daher keine Garantie für die Menge des Speicherplatzes, die durch die Ausführung des Garbage Collectors freigegeben werden kann.

## <a name="next-steps"></a>Nächste Schritte
[Besuchen Sie das Azure Stack-Supportforum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack).
