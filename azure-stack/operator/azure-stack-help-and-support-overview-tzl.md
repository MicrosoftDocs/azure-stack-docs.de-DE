---
title: Übersicht über Hilfe und Support für Microsoft Azure Stack Hub
description: Enthält Informationen zum Support für Microsoft Azure Stack Hub.
author: justinha
ms.topic: article
ms.date: 02/26/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 02/26/2020
ms.openlocfilehash: d4d5fbd3979d7f30354cb3716e3277b895ddac78
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/18/2020
ms.locfileid: "79520615"
---
# <a name="microsoft-azure-stack-hub-help-and-support"></a>Microsoft Azure Stack Hub: Hilfe und Support

Azure Stack Hub-Operatoren können **Hilfe und Support** verwenden, um Diagnoseprotokolle zu sammeln und zur Problembehandlung an Microsoft zu senden. Auf **Hilfe und Support** kann im Azure Stack Hub-Portal über das Administratorportal zugegriffen werden. Mithilfe der dort verfügbaren Ressourcen können Operatoren mehr über Azure Stack erfahren, ihre Supportoptionen prüfen und die Hilfe von Experten anfordern.  

![Screenshot: Zugreifen auf „Hilfe und Support“ im Administratorportal](media/azure-stack-help-and-support/help-and-support.png)

## <a name="help-resources"></a>Hilferessourcen 

Operatoren können **Hilfe und Support** verwenden, um mehr über Azure Stack Hub zu erfahren, ihre Supportoptionen zu prüfen und Hilfe von Experten anzufordern. 

### <a name="things-to-try-first"></a>Was Sie zuerst ausprobieren sollten

Der Bereich **Hilfe und Support** verfügt oben über Links, die Sie zuerst ausprobieren können, z. B. Informieren über ein neues Konzept, Kennenlernen der Funktionsweise der Abrechnung oder Anzeigen der verfügbaren Supportoptionen. 

![Self-Service-Support](media/azure-stack-help-and-support/get-support-tiles.png)

- **Dokumentation**: Die [Dokumentation für Azure Stack Hub-Operatoren](index.yml) enthält Konzepte, Anleitungsthemen und Tutorials, in denen Sie erfahren, wie Sie Azure Stack Hub-Dienste wie virtuelle Computer, SQL-Datenbanken und Web-Apps anbieten. 

- **Informationen zur Abrechnung**: Sehen Sie sich die Tipps zu [Nutzung und Abrechnung](azure-stack-billing-and-chargeback.md) an.

- **Supportoptionen**: Azure Stack Hub-Operatoren können unter verschiedenen [Azure-Supportoptionen](https://aka.ms/azstacksupport) wählen, die die Anforderungen jedes Unternehmens erfüllen. 


### <a name="get-expert-help"></a>Inanspruchnehmen von Expertenhilfe 

Für ein integriertes System besteht ein koordinierter Eskalations- und Lösungsprozess zwischen Microsoft und seinen OEM-Hardwarepartnern (Original Equipment Manufacturer).

Wenn es zu einem Problem mit einem Clouddienst kommt, wird Support über den Microsoft-Kundensupport (Microsoft Customer Support Services, CSS) angeboten. Sie können oben rechts im Administratorportal auf **Hilfe** (Fragezeichen) und dann auf **Hilfe und Support** klicken, um die zugehörige **Übersicht** zu öffnen und eine neue Supportanfrage zu übermitteln. Beim Erstellen einer Supportanfrage wird der Azure Stack Hub-Dienst vorab ausgewählt. Wir empfehlen Kunden dringend, Tickets auf diese Weise zu übermitteln, anstatt das globale Azure-Portal zu verwenden. 

Wenden Sie sich zuerst an Ihren OEM-Hardwareanbieter, falls ein Problem in den folgenden Bereichen besteht: Bereitstellung, Patches und Updates, Hardware (einschließlich der vor Ort austauschbaren Teile (Field Replaceable Units)) und Software mit Hardware-Branding, z.B. auf dem Hardware-Lebenszyklushost ausgeführte Software. Wenden Sie sich bei allen anderen Problemen an Microsoft CSS.

![Inanspruchnehmen von Expertenhilfe für integrierte Systeme](media/azure-stack-help-and-support/get-support-integrated.png)

Fragen rund um den Support für das ASDK können Sie im [Azure Stack Hub-MSDN-Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) stellen. 

Sie können oben rechts im Administratorportal auf **Hilfe** (Fragezeichen) und dann auf **Hilfe und Support** klicken, um die zugehörige **Übersicht** mit einem Link zum Forum zu öffnen. MSDN-Foren werden regelmäßig überprüft.  
Da das Development Kit eine Evaluierungsumgebung ist, wird über den Microsoft CSS kein offizieller Support angeboten.

Sie können auch in den MSDN-Foren ein Problem zur Diskussion stellen oder das Onlinetraining nutzen und Ihre eigenen Fähigkeiten verbessern. 

![Inanspruchnehmen von Expertenhilfe](media/azure-stack-help-and-support/get-support-cards.png)

### <a name="get-up-to-speed-with-azure-stack-hub"></a>Einsteigen in Azure Stack Hub

Diese Tutorialreihe wird in Abhängigkeit davon angepasst, ob Sie das ASDK oder integrierte Systeme ausführen, damit Sie sich ohne Zeitverlust über Ihre jeweilige Umgebung informieren können. 

![Tutorials zum Support](media/azure-stack-help-and-support/get-support-tutorials.png)

## <a name="diagnostic-log-collection"></a>Erfassung von Diagnoseprotokollen

Diagnoseprotokolle können auf zwei Arten an Microsoft gesendet werden: 

- [Proaktives Senden von Protokollen:](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md) Wenn diese Option aktiviert ist, wird die Protokollsammlung durch bestimmte Integritätswarnungen ausgelöst. 
- [Sofortiges Senden von Protokollen:](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md) Sie können manuell ein bestimmtes gleitendes Fenster als Zeitrahmen für die Protokollsammlung auswählen.

![Screenshot der Optionen für die Sammlung von Diagnoseprotokollen](media/azure-stack-help-and-support/banner-enable-automatic-log-collection.png)


## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die [Sammlung von Diagnoseprotokollen](azure-stack-diagnostic-log-collection-overview-tzl.md).
- Erfahren Sie, wie Sie [Ihre Cloud-ID ermitteln](azure-stack-find-cloud-id.md).
- Informieren Sie sich über die [Problembehandlung für Azure Stack Hub](azure-stack-troubleshooting.md).
