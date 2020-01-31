---
title: Problembehandlung beim ASDK
description: Erfahren Sie, wie Sie Probleme mit dem Azure Stack Development Kit (ASDK) behandeln.
author: justinha
ms.topic: article
ms.date: 11/05/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: d02b500fce65464ecc27e8fbbd6edbbf7c6882b3
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76873310"
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

### <a name="template-validation-error-parameter-osprofile-is-not-allowed"></a>Fehlerparameter „osProfile“ für die Vorlagenüberprüfung ist nicht zulässig

Wenn Sie bei der Vorlagenüberprüfung eine Fehlermeldung mit dem Hinweis erhalten, dass der Parameter „osProfile“ nicht zulässig ist, sollten Sie sicherstellen, dass Sie die richtigen Versionen der APIs für diese Komponenten verwenden:

- [Compute](https://docs.microsoft.com/azure-stack/user/azure-stack-profiles-azure-resource-manager-versions#microsoftcompute)
- [Network](https://docs.microsoft.com/azure-stack/user/azure-stack-profiles-azure-resource-manager-versions#microsoftnetwork)

Verwenden Sie [AzCopy 7.3.0](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#download-and-install-azcopy), um eine VHD von Azure nach Azure Stack zu kopieren. Wenden Sie sich an Ihren Anbieter, um Probleme zu beheben, die das Image selbst betreffen. Weitere Informationen zu den WALinuxAgent-Anforderungen für Azure Stack finden Sie unter [Azure LinuX-Agent](../operator/azure-stack-linux.md#azure-linux-agent).

### <a name="deployment-fails-due-to-lack-of-external-access"></a>Bereitstellungsfehler aufgrund von fehlendem externen Zugriff
Wenn es bei der Bereitstellung in Phasen, in denen ein externer Zugriff erforderlich ist, zu Fehlern kommt, wird eine Ausnahme wie im folgenden Beispiel zurückgegeben:

```
An error occurred while trying to test identity provider endpoints: System.Net.WebException: The operation has timed out.
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.GetResponse(WebRequest request)
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.ProcessRecord()at, <No file>: line 48 - 8/12/2018 2:40:08 AM
```
Falls dieser Fehler auftritt, sollten Sie überprüfen, ob die Mindestanforderungen für den Netzwerkbetrieb erfüllt sind. Überprüfen Sie hierzu die [Informationen zum Netzwerkdatenverkehr bei der Bereitstellung](../operator/deployment-networking.md). Für Partner steht im Rahmen des Toolkits für Partner außerdem ein Tool für die Netzwerküberprüfung zur Verfügung.

Andere Bereitstellungsfehler sind normalerweise auf Probleme bei der Verbindungsherstellung mit Ressourcen im Internet zurückzuführen.

Sie können die folgenden Schritte ausführen, um die Konnektivität mit Ressourcen im Internet zu überprüfen:

1. Öffnen Sie PowerShell.
2. Starten Sie über „Enter-PSSession“ eine Sitzung auf WAS01 oder einer beliebigen ERC-VM.
3. Führen Sie das folgende Cmdlet aus: 
   ```powershell
   Test-NetConnection login.windows.net -port 443
   ```

Wenn dieser Befehl nicht erfolgreich ist, überprüfen Sie, ob der TOR-Switch und alle weiteren Netzwerkgeräte so konfiguriert sind, dass sie [Netzwerkverkehr zulassen](../operator/azure-stack-network.md).


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
