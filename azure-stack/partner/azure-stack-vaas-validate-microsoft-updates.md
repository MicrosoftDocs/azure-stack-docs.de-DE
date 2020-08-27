---
title: Überprüfen der Softwareupdates von Microsoft
titleSuffix: Azure Stack Hub
description: Hier erfahren Sie, wie Sie Softwareupdates von Microsoft mit Validation-as-a-Service in Azure Stack Hub überprüfen.
author: mattbriggs
ms.topic: tutorial
ms.date: 08/24/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: e0c9d3153da8030f828dc540f69a1d57b5e9d90a
ms.sourcegitcommit: 4922a14fdbc8a3b67df065336e8a21a42f224867
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2020
ms.locfileid: "88764850"
---
# <a name="validate-software-updates-from-microsoft"></a>Überprüfen der Softwareupdates von Microsoft

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft veröffentlicht in regelmäßigen Abständen Updates für die Azure Stack Hub-Software. Diese Updates werden Azure Stack Hub-Entwicklungspartnern zur Verfügung gestellt. Die Updates werden vor der öffentlichen Bereitstellung zur Verfügung gestellt. Sie können die Updates mit Ihrer Lösung testen und Feedback an Microsoft senden.

Microsoft-Softwareupdates für Azure Stack Hub liegt eine Namenskonvention zugrunde. Der Name „1803“ bedeutet beispielsweise, dass es sich um ein Update für März 2018 handelt. Informationen zur Wartungsrichtlinie und zu Versionshinweisen von Azure Stack Hub finden Sie unter [Azure Stack Hub-Wartungsrichtlinie](../operator/azure-stack-servicing-policy.md).

## <a name="prerequisites"></a>Voraussetzungen

Machen Sie sich zunächst mit Folgendem vertraut, bevor Sie den monatlichen Aktualisierungsprozess in VaaS (Validation-as-a-Service) ausführen:

- [Wichtige Begriffe zu Validation-as-a-Service](azure-stack-vaas-key-concepts.md)

## <a name="required-tests"></a>Erforderliche Tests

Für die monatliche Softwareüberprüfung müssen die folgenden Tests in der angegebenen Reihenfolge ausgeführt werden:

- OEM-Validierungsworkflow

## <a name="validating-software-updates"></a>Überprüfen von Softwareupdates

1. Erstellen Sie einen neuen Workflow vom Typ **Paketvalidierung**.
1. Gehen Sie für die oben angegebenen erforderlichen Tests wie unter [Ausführen von Paketvalidierungstests](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests) beschrieben vor. Im folgenden Abschnitt finden Sie zusätzliche Anweisungen für den Test **Monatliche Azure Stack Hub-Updateüberprüfung**.

Wenden Sie sich bei Fragen oder anderen Anliegen an den [VaaS-Support](mailto:vaashelp@microsoft.com).

## <a name="next-steps"></a>Nächste Schritte

- [Überwachen und Verwalten von Tests im VaaS-Portal](azure-stack-vaas-monitor-test.md)