---
title: Übersicht über Validation-as-a-Service für Azure Stack| Microsoft-Dokumentation
description: Übersicht über Validation-as-a-Service in Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: d1cfc5c9c378ccfc48811c5896337ef91fdca2e8
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418563"
---
# <a name="what-is-validation-as-a-service-for-azure-stack"></a>Was ist Validation-as-a-Service für Azure Stack?

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Validation-as-a-Service (VaaS) ist ein nativer Azure-Dienst, der für Lösungspartner konzipiert wurde, die zusammen mit Microsoft Azure Stack-Angebote entwickeln. Lösungspartner können mithilfe dieses Diensts überprüfen, ob ihre Lösungen die Anforderungen von Microsoft erfüllen und in Azure Stack wie erwartet funktionieren.

VaaS wird vorrangig für folgende Zwecke eingesetzt:

- Überprüfen neuer Azure Stack-Lösungen
- Überprüfen von Änderungen an der Azure Stack-Software
- Digitales Signieren der Pakete von Lösungspartnern, die während der Bereitstellung verwendet wurden
- Anzeigen einer Vorschau von VaaS-Testbegleitmaterial

## <a name="validate-a-new-azure-stack-solution"></a>Überprüfen einer neuen Azure Stack-Lösung

Partner überprüfen mithilfe des Workflows **Lösungsvalidierung** neue Azure Stack-Lösungen. Die Lösung muss die erforderlichen HLK-Komponententests (Hardware Lab Kit) von Azure Stack bestehen. Zum Zertifizieren unterschiedlicher Hardwarekonfigurationen muss der Workflow für jede neue Lösung zweimal ausgeführt werden: je einmal für die Minimal- und die Maximalkonfiguration.

Weitere Informationen finden Sie unter [Überprüfen einer neuen Azure Stack-Lösung](azure-stack-vaas-validate-solution-new.md).

## <a name="validate-changes-to-the-azure-stack-software"></a>Überprüfen von Änderungen an der Azure Stack-Software

Partner überprüfen mithilfe des Workflows für die **Paketvalidierung**, ob ihre Lösung mit den neuesten Updates der Azure Stack-Software ausgeführt werden kann. Der Workflow für die Paketvalidierung muss in der von Microsoft empfohlenen Hardwareumgebung ausgeführt werden, in der Patch und Update (P&U) zur Anwendung des Updates verwendet wurde. Es wird empfohlen, den Workflow auch für den Baselinebuild auszuführen.

Weitere Informationen finden Sie unter [Überprüfen der Softwareupdates von Microsoft](azure-stack-vaas-validate-microsoft-updates.md).

## <a name="get-digitally-signed-solution-partner-packages"></a>Abrufen digital signierter Pakete von Lösungspartnern

Neben der Validierung von Azure Stack-Updates können Partner mithilfe des Workflows **Paketvalidierung** Updates für benutzerdefinierte OEM-Pakete überprüfen. Diese umfassen partnerspezifische Azure Stack-Treiber, Firmware und weitere Software, die während der Bereitstellung von Azure Stack-Software verwendet wird. Stellen Sie das Paket, das Sie in der aktuellen Version der Azure Stack-Software überprüfen, mindestens in der Lösung mit der unterstützten Minimalgröße bereit. Das Paket wird an VaaS übermittelt, bevor Tests ausgeführt werden. Wenn die Tests erfolgreich verlaufen, teilen Sie [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com) mit, dass die Tests für das Paket abgeschlossen wurden und das Paket mit der digitalen Signatur von Azure Stack digital signiert werden sollte. Microsoft signiert das Paket und benachrichtigt den Azure Stack-Partner darüber, dass das Paket zum Herunterladen im VaaS-Portal verfügbar ist.

Weitere Informationen finden Sie unter [Überprüfen von OEM-Paketen](azure-stack-vaas-validate-oem-package.md).

## <a name="preview-vaas-test-collateral"></a>Anzeigen einer Vorschau von VaaS-Testbegleitmaterial

Microsoft stellt in Azure Stack regelmäßig neue Features zur Verfügung. Im Rahmen des Entwicklungsprozesses für die Bereitstellung dieser Features auf dem Markt wird im Workflow für den **Testdurchlauf** neues Begleitmaterial für Tests zur Verfügung gestellt. Der Workflow für den Testdurchlauf umfasst Begleitmaterial für Tests aus den anderen Workflows, damit Tests inoffiziell ausgeführt werden können. Verwenden Sie den Workflow für den Testdurchlauf nicht, um Ergebnisse zur Genehmigung zu senden. Verwenden Sie den Workflow „Lösungsvalidierung“ und den Workflow „Paketvalidierung“, um eine offizielle Genehmigung für Ihre Lösung zu erhalten.

Weitere Informationen finden Sie unter [Quickstart: Planen des ersten Tests mithilfe des Validation-as-a-Service-Portals](azure-stack-vaas-schedule-test-pass.md).

## <a name="validation-workflow-tests-summary"></a>Zusammenfassung der Tests für den Validierungsworkflow

| Validierungsworkflow | Erforderliche Tests |
|----|------------|
| [Neue Lösungsvalidierung](azure-stack-vaas-validate-solution-new.md) | Cloud-Simulationsmodul<br>Compute SDK Operational Suite<br>Disk Identification Test<br>KeyVault Extension SDK Operational Suite<br>KeyVault SDK Operational Suite<br>Network SDK Operational Suite<br>Storage Account SDK Operational Suite<br> |
| [OEM-Paketvalidierung](azure-stack-vaas-validate-oem-package.md) | Überprüfung des OEM-Erweiterungspakets<br>Cloud-Simulationsmodul |
| [Monatliche Updatevalidierung](azure-stack-vaas-validate-microsoft-updates.md) | Monthly Azure Stack Update Verification (Monatliche Azure Stack-Updateüberprüfung)<br>Cloud-Simulationsmodul<br> |

## <a name="next-steps"></a>Nächste Schritte

- [Einrichten Ihrer Validation-as-a-Service-Ressourcen](azure-stack-vaas-set-up-resources.md)
- Erfahren Sie mehr über [Wichtige Begriffe zu Validation-as-a-Service](azure-stack-vaas-key-concepts.md)
