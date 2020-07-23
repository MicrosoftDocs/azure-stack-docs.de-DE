---
title: Ermitteln Ihrer Cloud-ID
description: Hier erfahren Sie, wie Sie Ihre Cloud-ID in „Hilfe und Support“ von Azure Stack Hub finden.
author: justinha
ms.topic: article
ms.date: 10/08/2019
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 10/08/2019
ms.openlocfilehash: 09b22541ed77915a2a28c4abf5212ccbbbf5fb81
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86488959"
---
# <a name="find-your-cloud-id"></a>Ermitteln Ihrer Cloud-ID

In diesem Thema erfahren Sie, wie Sie Ihre Cloud-ID über das Administratorportal oder den privilegierten Endpunkt (PEP) ermitteln. 

## <a name="use-the-administrator-portal"></a>Verwenden des Administratorportals

1. Öffnen Sie das Administratorportal. 
1. Klicken Sie auf **Regionsverwaltung**.

   ![Screenshot des Dashboards](./media/azure-stack-automatic-log-collection/dashboard.png)

1. Klicken Sie auf **Eigenschaften**, und kopieren Sie die Cloud-ID des Stempels (**Stamp Cloud ID**).

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

* [Proaktives Senden von Protokollen](./azure-stack-configure-automatic-diagnostic-log-collection.md?view=azs-2002)
* [Sofortiges Senden von Protokollen](./azure-stack-configure-on-demand-diagnostic-log-collection-portal.md?view=azs-2002)
