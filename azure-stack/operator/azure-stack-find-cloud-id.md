---
title: Ermitteln Ihrer Cloud-ID
description: Hier erfahren Sie, wie Sie Ihre Cloud-ID in „Hilfe und Support“ von Azure Stack Hub finden.
author: PatAltimore
ms.topic: article
ms.date: 10/08/2019
ms.author: patricka
ms.reviewer: shisab
ms.lastreviewed: 10/08/2019
ms.openlocfilehash: 6da70761e8a37967d3debdff28b67447596c632a
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98255961"
---
# <a name="find-your-cloud-id"></a>Ermitteln Ihrer Cloud-ID

In diesem Thema erfahren Sie, wie Sie Ihre Cloud-ID über das Administratorportal oder den privilegierten Endpunkt (PEP) ermitteln. Die Cloud-ID ist die eindeutige ID für die Nachverfolgung von Supportdaten, die von einer bestimmten Skalierungseinheit hochgeladen wurden. Wenn Diagnoseprotokolle für die Supportanalyse hochgeladen werden, werden die Protokolle über die Cloud-ID dieser Skalierungseinheit zugeordnet.

## <a name="use-the-administrator-portal"></a>Verwenden des Administratorportals

1. Öffnen Sie das Administratorportal. 
1. Wählen Sie **Regionsverwaltung** aus.

   ![Screenshot des Dashboards](./media/azure-stack-automatic-log-collection/dashboard.png)

1. Wählen Sie **Eigenschaften** aus, und kopieren Sie die **Cloud-ID des Stempels**.

   ![Screenshot der Regionseigenschaften mit Cloud-ID des Stempels](media/azure-stack-automatic-log-collection/region-properties-blade-with-stamp-cloud-id.png)


## <a name="use-the-privileged-endpoint"></a>Verwenden des privilegierten Endpunkts

1. Öffnen Sie eine PowerShell-Sitzung mit erhöhten Rechten, und führen Sie das folgende Skript aus. Ersetzen Sie die IP-Adresse des virtuellen PEP-Computers und die Anmeldeinformationen des Cloudadministrators durch die entsprechenden Angaben für Ihre Umgebung. 

   ```powershell
   $ipAddress = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.

   $password = ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
   $cred = New-Object -TypeName System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $password)
   $session = New-PSSession -ComputerName $ipAddress -ConfigurationName PrivilegedEndpoint -Credential $cred

   $stampInfo = Invoke-Command -Session $session { Get-AzureStackStampInformation }
   if ($session) {
       Remove-PSSession -Session $session
   }

   $stampInfo.CloudID
   ```

## <a name="next-steps"></a>Nächste Schritte

* [Proaktives Senden von Protokollen](./diagnostic-log-collection.md#send-logs-proactively)
* [Sofortiges Senden von Protokollen](./diagnostic-log-collection.md#send-logs-now)