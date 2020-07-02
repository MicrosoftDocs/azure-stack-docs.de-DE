---
title: 'Azure Stack Hub: Hilfe und Support'
description: Enthält Informationen zum Support für Microsoft Azure Stack Hub.
author: justinha
ms.topic: article
ms.date: 02/26/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 02/26/2020
ms.openlocfilehash: a9b81eb49abdcd08cef13e89845473db5c9c9032
ms.sourcegitcommit: b2b0fe629d840ca8d5b6353a90f1fcb392a73bd5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/25/2020
ms.locfileid: "85377160"
---
# <a name="azure-stack-hub-help-and-support"></a>Azure Stack Hub: Hilfe und Support

::: moniker range=">= azs-2002"

Azure Stack Hub-Operatoren können **Hilfe und Support** verwenden, um Diagnoseprotokolle zu sammeln und zur Problembehandlung an Microsoft zu senden. Auf **Hilfe und Support** kann im Azure Stack Hub-Portal über das Administratorportal zugegriffen werden. Mithilfe der dort verfügbaren Ressourcen können Operatoren mehr über Azure Stack erfahren, ihre Supportoptionen prüfen und die Hilfe von Experten anfordern.  

![Zugreifen auf Hilfe und Support im Azure Stack Hub-Administratorportal](media/azure-stack-help-and-support/help-and-support.png)

## <a name="help-resources"></a>Hilferessourcen

Operatoren können **Hilfe und Support** verwenden, um mehr über Azure Stack Hub zu erfahren, ihre Supportoptionen zu prüfen und Hilfe von Experten anzufordern.

### <a name="things-to-try-first"></a>Was Sie zuerst ausprobieren sollten

Oben im Bereich **Hilfe und Support** sind die Dinge angegeben, über die Sie sich zuerst informieren sollten, z. B. neue Konzepte, die Funktionsweise der Abrechnung oder die verfügbaren Supportoptionen.

![Self-Service-Unterstützung in Azure Stack Hub](media/azure-stack-help-and-support/get-support-tiles.png)

- **Dokumentation**: Die [Dokumentation für Azure Stack Hub-Operator](index.yml) enthält Konzepte, Gewusst wie-Themen und Tutorials, in denen beschrieben wird, wie Sie Azure Stack Hub-Dienste anbieten. Zu diesen Diensten gehören virtuelle Computer, SQL-Datenbanken, Web-Apps und mehr.

- **Informationen zur Abrechnung**: Sehen Sie sich die Tipps zu [Nutzung und Abrechnung](azure-stack-billing-and-chargeback.md) an.

- **Supportoptionen**: Azure Stack Hub-Operatoren können unter verschiedenen [Azure-Supportoptionen](https://aka.ms/azstacksupport) wählen, die die Anforderungen jedes Unternehmens erfüllen.

### <a name="get-expert-help"></a>Inanspruchnehmen von Expertenhilfe

Für ein integriertes System besteht ein koordinierter Eskalations- und Lösungsprozess zwischen Microsoft und seinen OEM-Hardwarepartnern (Original Equipment Manufacturer).

Wenn es zu einem Problem mit einem Clouddienst kommt, wird Support über den Microsoft-Support angeboten. Sie können oben rechts im Administratorportal **Hilfe** (Fragezeichen) und dann **Hilfe und Support** auswählen, um die zugehörige **Übersicht** zu öffnen und eine neue Supportanfrage zu übermitteln. Beim Erstellen einer Supportanfrage wird der Azure Stack Hub-Dienst vorab ausgewählt. Wir empfehlen Kunden dringend, Tickets auf diese Weise zu übermitteln, anstatt das globale Azure-Portal zu verwenden.

Wenden Sie sich zuerst an Ihren OEM-Hardwareanbieter, falls ein Problem in den folgenden Bereichen besteht: Bereitstellung, Patches und Updates, Hardware (einschließlich der vor Ort austauschbaren Teile (Field Replaceable Units)) und Software mit Hardware-Branding, z. B. auf dem Hardware-Lebenszyklushost ausgeführte Software. Wenden Sie sich bei allen anderen Problemen an den Microsoft-Support.

![Inanspruchnehmen von Expertenhilfe für integrierte Systeme](media/azure-stack-help-and-support/get-support-integrated.png)

Fragen rund um den Support für das Azure Stack Development Kit (ASDK) können Sie im [Azure Stack Hub-MSDN-Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) stellen.

Wählen Sie oben rechts im Administratorportal **Hilfe** (Fragezeichen) und dann **Hilfe und Support** aus, um die zugehörige **Übersicht** mit einem Link zum Forum zu öffnen. MSDN-Foren werden regelmäßig überprüft. Da das ASDK eine Evaluierungsumgebung ist, wird über Microsoft-Support kein offizieller Support angeboten.

Sie können auch in den MSDN-Foren ein Problem zur Diskussion stellen oder das Onlinetraining nutzen und Ihre eigenen Fähigkeiten verbessern.

![Hilfe zu Azure Stack Hub von Experten](media/azure-stack-help-and-support/get-support-cards.png)

### <a name="get-up-to-speed-with-azure-stack-hub"></a>Einsteigen in Azure Stack Hub

Diese Tutorialreihe wird in Abhängigkeit davon angepasst, ob Sie das ASDK oder integrierte Systeme ausführen, damit Sie sich ohne Zeitverlust über Ihre jeweilige Umgebung informieren können.

![Support-Tutorials für Azure Stack Hub](media/azure-stack-help-and-support/get-support-tutorials.png)

## <a name="diagnostic-log-collection"></a>Erfassung von Diagnoseprotokollen

Sie können Diagnoseprotokolle auf zwei Arten an Microsoft senden:

- [Proaktives Senden von Protokollen:](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md) Wenn diese Option aktiviert ist, wird die Protokollsammlung durch bestimmte Integritätswarnungen ausgelöst.
- [Sofortiges Senden von Protokollen:](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md) Sie können manuell ein bestimmtes gleitendes Fenster als Zeitrahmen für die Protokollsammlung auswählen.

![Optionen für die Sammlung von Diagnoseprotokollen](media/azure-stack-help-and-support/banner-enable-automatic-log-collection.png)

::: moniker-end
::: moniker range="<= azs-1910"

## <a name="diagnostic-log-collection"></a>Erfassung von Diagnoseprotokollen

Ab Version 1907 gibt es zwei neue Möglichkeiten, Protokolle in **Hilfe und Support** zu sammeln:

- **Automatische Sammlung**: Wenn diese Option aktiviert ist, wird die Protokollsammlung durch bestimmte Integritätswarnungen ausgelöst.
- **Protokolle jetzt sammeln**: Sie können ein gleitendes Fenster von 1 bis 4 Stunden in den letzten sieben Tagen auswählen.

![Optionen für die Sammlung von Diagnoseprotokollen](media/azure-stack-automatic-log-collection/azure-stack-log-collection-overview.png)

Integrierte Systeme können die Diagnoseprotokolle mit dem Microsoft-Support teilen. Da das ASDK (Azure Stack Development Kit) eine Evaluierungsumgebung ist, wird es vom Microsoft-Support nicht unterstützt. Weitere Informationen finden Sie unter [Sammlung von Azure Stack Hub-Diagnoseprotokollen – Übersicht ](azure-stack-diagnostic-log-collection-overview.md).

## <a name="help-and-support-for-earlier-releases-azure-stack-hub-pre-1905"></a>Hilfe und Support für frühere Releases von Azure Stack Hub (vor 1905)

Vorherige Azure Stack Hub-Releases verfügen ebenfalls über einen Link zu **Hilfe und Support**, der zur [Dokumentation für Azure Stack Hub-Operatoren](https://aka.ms/adminportaldocs) führt.

![Tutorials zum Support](media/azure-stack-help-and-support/get-support-previous.png)

Wenn es zu einem Problem mit einem Clouddienst kommt, wird Support über den Microsoft-Support angeboten. Sie können oben rechts im Administratorportal **Hilfe** (Fragezeichen), **Hilfe und Support** und dann **Neue Supportanfrage** auswählen, um eine neue Supportanfrage direkt an den Microsoft-Support zu übermitteln.

Für ein integriertes System besteht ein koordinierter Eskalations- und Lösungsprozess zwischen Microsoft und seinen OEM-Partnern. Wenn es zu einem Problem mit einem Clouddienst kommt, wird Support über den Microsoft-Support angeboten.

Wenden Sie sich zuerst an Ihren OEM-Hardwareanbieter, falls ein Problem in den folgenden Bereichen besteht: Bereitstellung, Patches und Updates, Hardware (einschließlich der vor Ort austauschbaren Teile (Field Replaceable Units)) und Software mit Hardware-Branding, z. B. auf dem Hardware-Lebenszyklushost ausgeführte Software. Wenden Sie sich bei allen anderen Problemen an den Microsoft-Support.

Fragen rund um den Support für das ASDK können Sie im [Azure Stack Hub-MSDN-Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) stellen.

Wählen Sie im Administratorportal oben rechts **Hilfe** (Fragezeichen) und dann **Neue Supportanfrage** aus, um Hilfe von Experten der Azure Stack Hub-Community zu erhalten. Da das ASDK eine Evaluierungsumgebung ist, wird über Microsoft-Support kein offizieller Support angeboten.

::: moniker-end

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die [Erfassung von Diagnoseprotokollen](azure-stack-diagnostic-log-collection-overview-tzl.md).
- Erfahren Sie, wie Sie [Ihre Cloud-ID ermitteln](azure-stack-find-cloud-id.md).
- Informieren Sie sich über die [Problembehandlung für Azure Stack Hub](azure-stack-troubleshooting.md).
