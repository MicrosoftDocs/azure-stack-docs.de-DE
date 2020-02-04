---
title: Übersicht über Hilfe und Support für Microsoft Azure Stack Hub
description: Enthält Informationen zum Support für Microsoft Azure Stack Hub.
author: justinha
ms.topic: article
ms.date: 07/24/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 07/24/2019
ms.openlocfilehash: 621c0abb4f2e50c72e5a024cc3c1e634e73fe0c2
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76882230"
---
# <a name="microsoft-azure-stack-hub-help-and-support"></a>Microsoft Azure Stack Hub: Hilfe und Support

**Hilfe und Support** im Azure Stack Hub-Portal enthält Ressourcen, mit denen Operatoren mehr über Azure Stack Hub erfahren, die bestehenden Supportoptionen prüfen und die Hilfe von Experten anfordern können. Ab Version 1907 können Operatoren Hilfe und Support außerdem verwenden, um Diagnoseprotokolle für die Problembehandlung zu sammeln.  

## <a name="help-resources"></a>Hilferessourcen 

Operatoren können **Hilfe und Support** auch verwenden, um mehr über Azure Stack Hub zu erfahren, ihre Supportoptionen zu prüfen und Hilfe von Experten anzufordern. 

### <a name="things-to-try-first"></a>Was Sie zuerst ausprobieren sollten

Der Bereich **Hilfe und Support** verfügt oben über Links, die Sie zuerst ausprobieren können, z. B. Informieren über ein neues Konzept, Kennenlernen der Funktionsweise der Abrechnung oder Anzeigen der verfügbaren Supportoptionen. 

![Self-Service-Support](media/azure-stack-help-and-support/get-support-tiles.png)

- **Dokumentation**: Die [Dokumentation für Azure Stack Hub-Operatoren](index.yml) enthält Konzepte, Themen zur Vorgehensweise und Tutorials mit Informationen dazu, wie Sie Azure Stack Hub-Dienste anbieten, z. B. virtuelle Computer, SQL-Datenbanken, Web-Apps und mehr. 

- **Informationen zur Abrechnung**: Sehen Sie sich die Tipps zu [Nutzung und Abrechnung](azure-stack-billing-and-chargeback.md) an.

- **Supportoptionen**: Azure Stack Hub-Operatoren können unter verschiedenen [Azure-Supportoptionen](https://aka.ms/azstacksupport) wählen, die die Anforderungen jedes Unternehmens erfüllen. 

### <a name="get-expert-help"></a>Inanspruchnehmen von Expertenhilfe 

Für ein integriertes System besteht ein koordinierter Eskalations- und Lösungsprozess zwischen Microsoft und seinen OEM-Hardwarepartnern (Original Equipment Manufacturer).

Wenn es zu einem Problem mit einem Clouddienst kommt, wird Support über den Microsoft-Kundensupport (Microsoft Customer Support Services, CSS) angeboten. Sie können oben rechts im Administratorportal auf **Hilfe** (Fragezeichen) und dann auf **Hilfe und Support** klicken, um die zugehörige **Übersicht** zu öffnen und eine neue Supportanfrage zu übermitteln. Beim Erstellen einer Supportanfrage wird der Azure Stack Hub-Dienst vorab ausgewählt. Wir empfehlen dringend, dass Kunden anstelle des öffentlichen Azure-Portals diese Vorgehensweise zum Übermitteln von Tickets nutzen. 

Wenden Sie sich zuerst an Ihren OEM-Hardwareanbieter, falls ein Problem in den folgenden Bereichen besteht: Bereitstellung, Patches und Updates, Hardware (einschließlich der vor Ort austauschbaren Teile (Field Replaceable Units)) und Software mit Hardware-Branding, z.B. auf dem Hardware-Lebenszyklushost ausgeführte Software. Wenden Sie sich bei allen anderen Problemen an Microsoft CSS.

![Inanspruchnehmen von Expertenhilfe für integrierte Systeme](media/azure-stack-help-and-support/get-support-integrated.png)

Fragen rund um den Support für das ASDK können Sie im [Azure Stack Hub-MSDN-Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) stellen. 

Sie können oben rechts im Administratorportal auf **Hilfe** (Fragezeichen) und dann auf **Hilfe und Support** klicken, um die zugehörige **Übersicht** mit einem Link zum Forum zu öffnen. MSDN-Foren werden regelmäßig überprüft.  
Da das Development Kit eine Evaluierungsumgebung ist, wird über den Microsoft CSS kein offizieller Support angeboten.

![Inanspruchnehmen von Expertenhilfe für ASDK](media/azure-stack-help-and-support/get-support-asdk.png)

Sie können auch in den MSDN-Foren ein Problem zur Diskussion stellen oder das Onlinetraining nutzen und Ihre eigenen Fähigkeiten verbessern. 

![Inanspruchnehmen von Expertenhilfe](media/azure-stack-help-and-support/get-support-cards.png)

### <a name="get-up-to-speed-with-azure-stack-hub"></a>Einsteigen in Azure Stack Hub

Diese Tutorialreihe wird in Abhängigkeit davon angepasst, ob Sie das ASDK oder integrierte Systeme ausführen, damit Sie sich ohne Zeitverlust über Ihre jeweilige Umgebung informieren können. 

![Tutorials zum Support](media/azure-stack-help-and-support/get-support-tutorials.png)

## <a name="diagnostic-log-collection"></a>Erfassung von Diagnoseprotokollen

Ab Version 1907 gibt es zwei neue Möglichkeiten, Protokolle in **Hilfe und Support** zu sammeln:

- **Automatische Sammlung**: Wenn diese Option aktiviert ist, wird die Protokollsammlung durch bestimmte Integritätswarnungen ausgelöst. 
- **Protokolle jetzt sammeln**: Sie können ein gleitendes Fenster von 1–4 Stunden in den letzten sieben Tagen auswählen.

![Screenshot der Optionen für die Sammlung von Diagnoseprotokollen](media/azure-stack-automatic-log-collection/azure-stack-log-collection-overview.png)

Integrierte Systeme können die Diagnoseprotokolle mit dem Microsoft-Kundensupport (Customer Support Service, CSS) teilen. Da das ASDK (Azure Stack Development Kit) eine Evaluierungsumgebung ist, wird es von CSS nicht unterstützt. Weitere Informationen finden Sie unter [Sammlung von Azure Stack Hub-Diagnoseprotokollen – Übersicht ](azure-stack-diagnostic-log-collection-overview.md).



## <a name="help-and-support-for-earlier-releases-azure-stack-hub-pre-1905"></a>Hilfe und Support für frühere Releases von Azure Stack Hub (vor 1905)

Vorherige Azure Stack Hub-Releases verfügen ebenfalls über einen Link zu **Hilfe und Support**, der zur [Dokumentation für Azure Stack Hub-Operatoren](https://aka.ms/adminportaldocs) führt.

![Tutorials zum Support](media/azure-stack-help-and-support/get-support-previous.png)

Wenn es zu einem Problem mit einem Clouddienst kommt, wird Support über den Microsoft-Kundensupport (Microsoft Customer Support Services, CSS) angeboten. Sie können oben rechts im Administratorportal auf **Hilfe** (Fragezeichen), **Hilfe und Support** und dann auf **Neue Supportanfrage** klicken, um eine neue Supportanfrage direkt an CSS zu übermitteln.

Für ein integriertes System besteht ein koordinierter Eskalations- und Lösungsprozess zwischen Microsoft und seinen OEM-Partnern. Wenn es zu einem Problem mit einem Clouddienst kommt, wird Support über Microsoft CSS angeboten. 

Wenden Sie sich zuerst an Ihren OEM-Hardwareanbieter, falls ein Problem in den folgenden Bereichen besteht: Bereitstellung, Patches und Updates, Hardware (einschließlich der vor Ort austauschbaren Teile (Field Replaceable Units)) und Software mit Hardware-Branding, z.B. auf dem Hardware-Lebenszyklushost ausgeführte Software. Wenden Sie sich bei allen anderen Problemen an Microsoft CSS.

Fragen rund um den Support für das Development Kit können Sie im [Azure Stack Hub-MSDN-Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) stellen. Sie können im Administratorportal oben rechts auf **Hilfe** (Fragezeichen) und dann auf **Neue Supportanfrage** klicken, um Hilfe von Experten der Azure Stack Hub-Community zu erhalten.
Da das Development Kit eine Evaluierungsumgebung ist, wird über den Microsoft CSS kein offizieller Support angeboten.

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die [Problembehandlung für Azure Stack Hub](azure-stack-troubleshooting.md).