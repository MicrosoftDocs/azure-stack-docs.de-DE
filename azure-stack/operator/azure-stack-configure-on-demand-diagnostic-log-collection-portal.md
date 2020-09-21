---
title: Sofortiges Senden von Azure Stack Hub-Diagnoseprotokollen
description: Hier wird beschrieben, wie Sie in Azure Stack Hub mithilfe des Administratorportals oder eines PowerShell-Skripts Diagnoseprotokolle bedarfsgesteuert sammeln.
author: myoungerman
ms.topic: article
ms.date: 08/24/2020
ms.author: v-myoung
ms.reviewer: shisab
ms.lastreviewed: 08/24/2020
ms.openlocfilehash: 3d4725a832aa1ba88c0ecc5e2b435696cba3bbcd
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/15/2020
ms.locfileid: "90573155"
---
# <a name="send-azure-stack-hub-diagnostic-logs-now"></a>Sofortiges Senden von Azure Stack Hub-Diagnoseprotokollen

::: moniker range=">= azs-2002"

Azure Stack-Operatoren können Diagnoseprotokolle bei Bedarf über das Administratorportal oder per PowerShell an den Microsoft-Support senden, bevor sie Unterstützung anfordern. Wenn Azure Stack Hub mit Azure verbunden ist, empfiehlt es sich, die Option zum **sofortigen Senden von Protokollen** im Administratorportal zu verwenden, da die Protokolle so am einfachsten direkt an Microsoft gesendet werden können. Ist das Portal nicht verfügbar, sollten Operatoren stattdessen [Protokolle nun mit „Send-AzureStackDiagnosticLog“ senden](./azure-stack-configure-on-demand-diagnostic-log-collection-powershell.md?view=azs-2002). 

Falls Sie über keine Internetverbindung verfügen oder die Protokolle nur lokal speichern möchten, verwenden Sie die Methode [Get-AzureStackLog](azure-stack-get-azurestacklog.md) für den Protokollversand. Das folgende Flussdiagramm zeigt, welche Option jeweils zum Senden von Diagnoseprotokollen verwendet werden muss: 

![Flussdiagramm zum sofortigen Senden von Protokollen an Microsoft](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

>[!NOTE]
>Als Alternative zum bedarfsgesteuerten Sammeln von Protokollen können Sie den Problembehandlungsprozess auch optimieren, indem Sie [Diagnoseprotokolle proaktiv sammeln](./azure-stack-configure-automatic-diagnostic-log-collection.md?view=azs-2002). Wenn die Integritätsbedingungen des Systems untersucht werden müssen, werden die Protokolle automatisch zur Analyse hochgeladen, bevor eine Anfrage an den Microsoft-Support gesendet wird. Wenn die proaktive Diagnoseprotokollsammlung aktiviert ist, wird unter **Hilfe und Support** angezeigt, wenn die Protokollsammlung erfolgt. Wenn Sie auf die Option zum **sofortigen Senden von Protokollen** klicken, um Protokolle eines bestimmten Zeitfensters zu erfassen, während die proaktive Protokollsammlung ausgeführt wird, beginnt die bedarfsgesteuerte Protokollsammlung nach dem Abschluss der proaktiven Protokollsammlung.

Geben Sie Startzeit und Endzeit für die Protokollsammlung an, und klicken Sie auf die Option zum **Erfassen und Hochladen**. 

![Screenshot der Option zum sofortigen Senden von Protokollen](media/azure-stack-help-and-support/send-logs-now.png)


::: moniker-end
::: moniker range=">= azs-2005"
## <a name="save-logs-locally"></a>Lokales Speichern von Protokollen

Sie können Protokolle in einer lokalen SMB-Freigabe speichern, wenn Azure Stack Hub nicht mit Azure verbunden ist. Geben Sie auf dem Blatt **Einstellungen** den Pfad sowie einen Benutzernamen und ein Kennwort mit Schreibberechtigung für die Freigabe ein. Während eines Supportfalls werden von Microsoft-Support ausführliche Schritte für die Übertragung dieser lokalen Protokolle bereitgestellt. Wenn das Administratorportal nicht verfügbar ist, können Sie [Get-AzureStackLog](azure-stack-get-azurestacklog.md) verwenden, um Protokolle lokal zu speichern.

![Screenshot der Optionen für die Sammlung von Diagnoseprotokollen](media/azure-stack-help-and-support/save-logs-locally.png)

::: moniker-end
::: moniker range="<= azs-1910"
## <a name="use-help-and-support-to-collect-diagnostic-logs-on-demand"></a>Verwenden von „Hilfe und Support“ zum bedarfsgesteuerten Sammeln von Diagnoseprotokollen

Zur Behandlung eines Problems fordert der Microsoft-Support einen Azure Stack Hub-Operator möglicherweise auf, bei Bedarf Diagnoseprotokolle für ein bestimmtes Zeitfenster aus der vorangegangenen Woche zu erfassen. In diesem Fall stellt der Microsoft-Support dem Operator eine SAS-URL zum Hochladen der Sammlung zur Verfügung. 
Führen Sie die folgenden Schritte aus, um die bedarfsgesteuerte Protokollsammlung mithilfe der SAS-URL vom Microsoft-Support zu konfigurieren:

1. Öffnen Sie **Hilfe und Support > Protokollsammlung > Protokolle jetzt sammeln**. 
1. Wählen Sie ein beliebiges gleitendes Fenster von 1–4 Stunden in den letzten sieben Tagen aus. 
1. Wählen Sie die lokale Zeitzone aus.
1. Geben Sie die vom Microsoft-Support bereitgestellte SAS-URL ein.

   ![Screenshot einer bedarfsgesteuerten Protokollsammlung](media/azure-stack-automatic-log-collection/collect-logs-now.png)

>[!NOTE]
>Wenn die automatische Diagnoseprotokollsammlung aktiviert ist, wird es in **Hilfe und Support** angezeigt, wenn die Protokollsammlung erfolgt. Wenn Sie auf **Jetzt Protokolle sammeln** klicken, um Protokolle eines bestimmten Zeitfensters zu erfassen, während die automatische Protokollsammlung ausgeführt wird, beginnt die bedarfsgesteuerte Protokollsammlung nach dem Abschluss der automatischen Protokollsammlung. 


::: moniker-end


## <a name="next-steps"></a>Nächste Schritte

[Senden von Azure Stack Hub-Diagnoseprotokollen mithilfe des privilegierten Endpunkts (PEP)](./azure-stack-configure-on-demand-diagnostic-log-collection-powershell.md?view=azs-2002)
