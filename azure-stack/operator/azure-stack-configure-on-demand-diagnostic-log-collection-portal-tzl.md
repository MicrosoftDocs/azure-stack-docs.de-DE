---
title: Sofortiges Senden von Azure Stack Hub-Diagnoseprotokollen
description: Hier wird beschrieben, wie Sie in Azure Stack Hub mithilfe des Administratorportals oder eines PowerShell-Skripts Diagnoseprotokolle bedarfsgesteuert sammeln.
author: justinha
ms.topic: article
ms.date: 03/30/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 03/30/2020
ms.openlocfilehash: 2a46c031be648eedf42e204496ed2a123c926dd9
ms.sourcegitcommit: b65952127f39c263b162aad990e4d5b265570a7f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80402718"
---
# <a name="send-azure-stack-hub-diagnostic-logs-now"></a>Sofortiges Senden von Azure Stack Hub-Diagnoseprotokollen

Azure Stack-Operatoren können Diagnoseprotokolle bei Bedarf über das Administratorportal oder per PowerShell an den Microsoft-Kundendienst (Microsoft Customer Support Services, CSS) senden, bevor sie Unterstützung anfordern. Wenn Azure Stack Hub mit Azure verbunden ist, empfiehlt es sich, die Option zum **sofortigen Senden von Protokollen** im Administratorportal zu verwenden, da die Protokolle so am einfachsten direkt an Microsoft gesendet werden können. Ist das Portal nicht verfügbar, sollten Operatoren stattdessen [Protokolle nun mit „Send-AzureStackDiagnosticLog“ senden](azure-stack-configure-on-demand-diagnostic-log-collection-powershell-tzl.md). 

Falls Sie über keine Internetverbindung verfügen oder die Protokolle nur lokal speichern möchten, verwenden Sie die Methode [Get-AzureStackLog](azure-stack-get-azurestacklog.md) für den Protokollversand. Das folgende Flussdiagramm zeigt, welche Option jeweils zum Senden von Diagnoseprotokollen verwendet werden muss: 

![Flussdiagramm zum sofortigen Senden von Protokollen an Microsoft](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

>[!NOTE]
>Als Alternative zum bedarfsgesteuerten Sammeln von Protokollen können Sie den Problembehandlungsprozess auch optimieren, indem Sie [Diagnoseprotokolle proaktiv sammeln](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md). Wenn die Integritätsbedingungen des Systems untersucht werden müssen, werden die Protokolle automatisch zur Analyse hochgeladen, bevor eine Anfrage an den CSS gesendet wird. Wenn die proaktive Diagnoseprotokollsammlung aktiviert ist, wird unter **Hilfe und Support** angezeigt, wenn die Protokollsammlung erfolgt. Wenn Sie auf die Option zum **sofortigen Senden von Protokollen** klicken, um Protokolle eines bestimmten Zeitfensters zu erfassen, während die proaktive Protokollsammlung ausgeführt wird, beginnt die bedarfsgesteuerte Protokollsammlung nach dem Abschluss der proaktiven Protokollsammlung.

Geben Sie Startzeit und Endzeit für die Protokollsammlung an, und klicken Sie auf die Option zum **Erfassen und Hochladen**. 

![Screenshot der Option zum sofortigen Senden von Protokollen](media/azure-stack-help-and-support/send-logs-now.png)

## <a name="next-steps"></a>Nächste Schritte

[Senden von Azure Stack Hub-Diagnoseprotokollen mithilfe des privilegierten Endpunkts (PEP)](azure-stack-configure-on-demand-diagnostic-log-collection-powershell-tzl.md)
