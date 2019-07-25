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
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 0a507b7488a34715e528b6bbf291fec9832ef027
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418286"
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

1. Monthly Azure Stack Update Verification (Monatliche Azure Stack-Updateüberprüfung)
2. Cloud-Simulationsmodul

## <a name="validating-software-updates"></a>Überprüfen von Softwareupdates

1. Erstellen Sie einen neuen Workflow vom Typ **Paketvalidierung**.
1. Gehen Sie für die oben angegebenen erforderlichen Tests wie unter [Ausführen von Paketvalidierungstests](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests) beschrieben vor. Im folgenden Abschnitt finden Sie zusätzliche Anweisungen für den Test **Monthly Azure Stack Update Verification** (Monatliche Azure Stack-Updateüberprüfung).

### <a name="apply-the-monthly-update"></a>Anwenden des monatlichen Updates

1. Wählen Sie einen Agent aus, für den Tests ausgeführt werden sollen.
1. Planen Sie eine monatliche Azure Stack-Updateüberprüfung (**Monthly Azure Stack Update Verification**).
1. Geben Sie den Speicherort des OEM-Erweiterungspakets, das derzeit auf dem Stamp bereitgestellt ist, sowie den Speicherort des OEM-Erweiterungspakets an, das im Rahmen des Updates angewendet wird. Informationen zum Konfigurieren der URLs für diese Pakete finden Sie unter [Verwalten von Paketen für die Überprüfung](azure-stack-vaas-validate-oem-package.md#managing-packages-for-validation).
1. Führen Sie die Schritte auf der Benutzeroberfläche des ausgewählten Agents aus.

Wenden Sie sich bei Fragen oder anderen Anliegen an den [VaaS-Support](mailto:vaashelp@microsoft.com).

## <a name="next-steps"></a>Nächste Schritte

- [Überwachen und Verwalten von Tests im VaaS-Portal](azure-stack-vaas-monitor-test.md)