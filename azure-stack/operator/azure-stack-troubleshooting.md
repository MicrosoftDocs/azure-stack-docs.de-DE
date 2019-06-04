---
title: Problembehandlung für Microsoft Azure Stack | Microsoft-Dokumentation
description: Problembehandlung für Azure Stack
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/20/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 01/23/2019
ms.openlocfilehash: 9b78a7ee9af9dde3cbb40b52268cb4cbfc0a6dcc
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66268236"
---
# <a name="microsoft-azure-stack-troubleshooting"></a>Problembehandlung für Microsoft Azure Stack

Dieses Dokument enthält allgemeine Informationen zur Problembehandlung für Azure Stack. Die Empfehlungen und Codebeispiele werden in der vorliegenden Form bereitgestellt und führen ggf. nicht immer zur Behebung des Problems. 

## <a name="deployment"></a>Bereitstellung
### <a name="general-deployment-failure"></a>Allgemeiner Bereitstellungsfehler
Wenn während der Installation ein Fehler auftritt, können Sie mithilfe der Option „-rerun“ des Bereitstellungsskripts die Bereitstellung ab dem Schritt neu starten, in dem der Fehler aufgetreten ist.  

### <a name="at-the-end-of-asdk-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>Am Ende der ASDK-Bereitstellung ist die PowerShell-Sitzung noch geöffnet, und es wird keine Ausgabe angezeigt.
Dies ist wahrscheinlich nur auf das Standardverhalten des PowerShell-Befehlsfensters zurückzuführen, wenn dieses ausgewählt wurde. Die Bereitstellung des Development Kits war erfolgreich, das Skript wurde jedoch bei der Auswahl des Fensters angehalten. Sie können überprüfen, ob das Setup abgeschlossen ist, indem Sie in der Titelleiste des Befehlsfensters nach dem Wort „select“ suchen. Drücken Sie die ESC-Taste, um die Auswahl aufzuheben. Danach sollte die Abschlussmeldung angezeigt werden.

### <a name="deployment-fails-due-to-lack-of-external-access"></a>Bereitstellungsfehler aufgrund von fehlendem externen Zugriff
Wenn es bei der Bereitstellung in Phasen, in denen ein externer Zugriff erforderlich ist, zu Fehlern kommt, wird eine Ausnahme wie im folgenden Beispiel zurückgegeben:

```
An error occurred while trying to test identity provider endpoints: System.Net.WebException: The operation has timed out.
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.GetResponse(WebRequest request)
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.ProcessRecord()at, <No file>: line 48 - 8/12/2018 2:40:08 AM
```
Falls dieser Fehler auftritt, überprüfen Sie, ob die Mindestanforderungen für den Netzwerkbetrieb erfüllt sind. Überprüfen Sie hierzu die [Informationen zum Netzwerkdatenverkehr bei der Bereitstellung](deployment-networking.md). Für Partner steht im Rahmen des Toolkits für Partner außerdem ein Tool für die Netzwerküberprüfung zur Verfügung.

Bereitstellungsfehler mit der oben genannten Ausnahme sind typischerweise auf Probleme bei der Verbindungsherstellung mit Ressourcen im Internet zurückzuführen.

Um zu überprüfen, ob dieses Problem vorliegt, können Sie die folgenden Schritte ausführen:

1. Öffnen Sie PowerShell.
2. Starten Sie über „Enter-PSSession“ eine Sitzung auf WAS01 oder einer beliebigen ERC-VM.
3. Führen Sie das folgende Cmdlet aus: Test-NetConnection login.windows.net -port 443

Wenn dieser Befehl nicht erfolgreich ist, überprüfen Sie, ob der TOR-Switch und alle weiteren Netzwerkgeräte so konfiguriert sind, dass sie [Netzwerkverkehr zulassen](azure-stack-network.md).

## <a name="virtual-machines"></a>Virtuelle Computer
### <a name="default-image-and-gallery-item"></a>Standardimage und Katalogelement
Vor der Bereitstellung virtueller Computer in Azure Stack müssen Sie zuerst ein Windows Server-Image und ein Katalogelement hinzufügen.

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>Nach einem Neustart des Azure Stack-Hosts werden einige virtuelle Computer möglicherweise nicht automatisch gestartet.
Nach dem Neustart des Hosts ist Azure Stack möglicherweise nicht sofort verfügbar.  Dies ist darauf zurückzuführen, dass [Infrastruktur-VMs](../asdk/asdk-architecture.md#virtual-machine-roles ) von Azure Stack und Ressourcenanbieter etwas Zeit zum Überprüfen der Konsistenz benötigen. Sie werden jedoch letztlich automatisch gestartet.

Es kann auch vorkommen, dass Mandanten-VMs nach einem Neustart des Azure Stack Development Kit-Hosts nicht automatisch gestartet werden. Dies ist ein bekanntes Problem, und es sind nur einige manuelle Schritte erforderlich, um diese VMs wieder online zu schalten:

1.  Starten Sie auf dem Azure Stack Development Kit-Host im Startmenü den **Failovercluster-Manager**.
2.  Wählen Sie den Cluster **S-Cluster.azurestack.local** aus.
3.  Wählen Sie **Rollen** aus.
4.  Mandanten-VMs werden mit dem Zustand *gespeichert* angezeigt. Sobald alle Infrastruktur-VMs ausgeführt werden, klicken Sie mit der rechten Maustaste auf die Mandanten-VMs, und wählen Sie **Start**, um die Ausführung des virtuellen Computers fortzusetzen.

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>Ich habe einige virtuelle Computer gelöscht, die VHD-Dateien werden mir auf dem Datenträger aber weiterhin angezeigt. Ist dieses Verhalten zu erwarten?
Ja, dieses Verhalten wird erwartet. Es wurde aus den folgenden Gründen so entwickelt:

* Wenn Sie einen virtuellen Computer löschen, werden die VHDs nicht gelöscht. Datenträger sind separate Ressourcen in der Ressourcengruppe.
* Wenn ein Speicherkonto gelöscht wird, wird der Löschvorgang über Azure Resource Manager sofort angezeigt, die darin enthaltenen Datenträger bleiben jedoch möglicherweise im Speicher erhalten, bis die Garbage Collection ausgeführt wird.

Wenn „verwaiste“ VHDs angezeigt werden, müssen Sie wissen, ob diese zu einem Ordner für ein gelöschtes Speicherkonto gehören. Wenn das Speicherkonto nicht gelöscht wurde, ist es normal, dass die VHDs noch angezeigt werden.

Weitere Informationen zum Konfigurieren des Schwellenwerts für die Aufbewahrung und zur bedarfsgesteuerten Anforderung der Freigabe von Speicherplatz finden Sie unter [Manage Storage Accounts](azure-stack-manage-storage-accounts.md) (Verwalten von Speicherkonten).

## <a name="storage"></a>Storage
### <a name="storage-reclamation"></a>Freigabe von Speicherplatz
Es kann bis zu 14 Stunden dauern, bis freigegebene Kapazität im Portal angezeigt wird. Die Freigabe von Speicherplatz hängt von verschiedenen Faktoren ab, einschließlich der prozentualen Auslastung von internen Containerdateien im Blockblobspeicher. Je nach gelöschter Datenmenge besteht daher keine Garantie für die Menge des Speicherplatzes, der freigegeben werden kann, wenn der Garbage Collector ausgeführt wird.

