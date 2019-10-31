---
title: Überprüfen von Softwareupdates von Microsoft mit Validation-as-a-Service in Azure Stack | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Softwareupdates von Microsoft mit Validation-as-a-Service überprüfen.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/29/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 6fe2f8e7ab435cae3517890f79c26611a80c8a60
ms.sourcegitcommit: cc3534e09ad916bb693215d21ac13aed1d8a0dde
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73167155"
---
# <a name="validate-software-updates-from-microsoft"></a>Überprüfen der Softwareupdates von Microsoft

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft veröffentlicht in regelmäßigen Abständen Updates für die Azure Stack-Software. Diese Updates werden Azure Stack-Entwicklungspartnern zur Verfügung gestellt. Die Updates werden vor der öffentlichen Bereitstellung zur Verfügung gestellt. Sie können die Updates mit Ihrer Lösung testen und Feedback an Microsoft senden.

Den Namen von Microsoft-Softwareupdates für Azure Stack liegt eine Benennungskonvention zugrunde. „1803“ bedeutet beispielsweise, dass es sich um ein Update für März 2018 handelt. Informationen zur Richtlinie, zum Rhythmus und zu Versionshinweisen von Azure Stack-Updates finden Sie unter [Azure Stack-Wartungsrichtlinie](../operator/azure-stack-servicing-policy.md).

## <a name="prerequisites"></a>Voraussetzungen

Machen Sie sich zunächst mit Folgendem vertraut, bevor Sie den monatlichen Aktualisierungsprozess in VaaS ausführen:

- [Validation-as-a-Service – wichtige Begriffe](azure-stack-vaas-key-concepts.md)
- [Testen der interaktiven Featureüberprüfung](azure-stack-vaas-interactive-feature-verification.md)

## <a name="required-tests"></a>Erforderliche Tests

Für die monatliche Softwareüberprüfung müssen die folgenden Tests in der angegebenen Reihenfolge ausgeführt werden:

- Schritt 1: Monatliche Azure Stack-Updateüberprüfung
- Schritt 2: Überprüfung des OEM-Erweiterungspakets
- Schritt 3: OEM – Cloud-Simulationsmodul

## <a name="validating-software-updates"></a>Überprüfen von Softwareupdates

1. Erstellen Sie einen neuen Workflow vom Typ **Paketvalidierung**.
1. Gehen Sie für die oben angegebenen erforderlichen Tests wie unter [Ausführen von Paketvalidierungstests](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests) beschrieben vor. Im folgenden Abschnitt finden Sie zusätzliche Anweisungen für den Test **Monthly Azure Stack Update Verification** (Monatliche Azure Stack-Updateüberprüfung).

Wenden Sie sich bei Fragen oder anderen Anliegen an den [VaaS-Support](mailto:vaashelp@microsoft.com).

## <a name="next-steps"></a>Nächste Schritte

- [Überwachen und Verwalten von Tests im VaaS-Portal](azure-stack-vaas-monitor-test.md)