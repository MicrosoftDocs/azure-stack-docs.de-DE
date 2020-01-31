---
title: Überprüfen von Softwareupdates von Microsoft mit Validation-as-a-Service in Azure Stack
description: Hier erfahren Sie, wie Sie Softwareupdates von Microsoft mit Validation-as-a-Service überprüfen.
author: mattbriggs
ms.topic: tutorial
ms.date: 10/29/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 5e4438f0c3a95b2254422a428741a28b685cb9f5
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884009"
---
# <a name="validate-software-updates-from-microsoft"></a>Überprüfen der Softwareupdates von Microsoft

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft veröffentlicht in regelmäßigen Abständen Updates für die Azure Stack-Software. Diese Updates werden Azure Stack-Entwicklungspartnern zur Verfügung gestellt. Die Updates werden vor der öffentlichen Bereitstellung zur Verfügung gestellt. Sie können die Updates mit Ihrer Lösung testen und Feedback an Microsoft senden.

Den Namen von Microsoft-Softwareupdates für Azure Stack liegt eine Benennungskonvention zugrunde. „1803“ bedeutet beispielsweise, dass es sich um ein Update für März 2018 handelt. Informationen zur Richtlinie, zum Rhythmus und zu Versionshinweisen von Azure Stack-Updates finden Sie unter [Azure Stack-Wartungsrichtlinie](../operator/azure-stack-servicing-policy.md).

## <a name="prerequisites"></a>Voraussetzungen

Machen Sie sich zunächst mit Folgendem vertraut, bevor Sie den monatlichen Aktualisierungsprozess in VaaS ausführen:

- [Validation-as-a-Service – wichtige Begriffe](azure-stack-vaas-key-concepts.md)

## <a name="required-tests"></a>Erforderliche Tests

Für die monatliche Softwareüberprüfung müssen die folgenden Tests in der angegebenen Reihenfolge ausgeführt werden:

- OEM-Validierungsworkflow

## <a name="validating-software-updates"></a>Überprüfen von Softwareupdates

1. Erstellen Sie einen neuen Workflow vom Typ **Paketvalidierung**.
1. Gehen Sie für die oben angegebenen erforderlichen Tests wie unter [Ausführen von Paketvalidierungstests](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests) beschrieben vor. Im folgenden Abschnitt finden Sie zusätzliche Anweisungen für den Test **Monthly Azure Stack Update Verification** (Monatliche Azure Stack-Updateüberprüfung).

Wenden Sie sich bei Fragen oder anderen Anliegen an den [VaaS-Support](mailto:vaashelp@microsoft.com).

## <a name="next-steps"></a>Nächste Schritte

- [Überwachen und Verwalten von Tests im VaaS-Portal](azure-stack-vaas-monitor-test.md)