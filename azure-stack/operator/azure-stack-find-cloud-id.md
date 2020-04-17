---
title: Ermitteln Ihrer Cloud-ID
description: Hier erfahren Sie, wie Sie Ihre Cloud-ID in „Hilfe und Support“ von Azure Stack Hub finden.
author: justinha
ms.topic: article
ms.date: 10/08/2019
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 10/08/2019
ms.openlocfilehash: e0045ae6bf76b6b4e5973f65c6c0a5f758e0d46b
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "79520621"
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

* [Proaktives Senden von Protokollen](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md)
* [Sofortiges Senden von Protokollen](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md)






