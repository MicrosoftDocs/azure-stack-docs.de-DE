---
title: Übersicht über Validation-as-a-Service
titleSuffix: Azure Stack Hub
description: Hier finden Sie eine Übersicht über Validation-as-a-Service in Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 89e05e776538ab2032995634e69d704b1ae4981b
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97868181"
---
# <a name="what-is-validation-as-a-service-for-azure-stack-hub"></a>Was ist Validation-as-a-Service für Azure Stack Hub?

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Validation-as-a-Service (VaaS) ist ein nativer Azure-Dienst, der für Lösungspartner konzipiert wurde, die Azure Stack Hub-Angebote zusammen mit Microsoft entwickeln. Lösungspartner können mithilfe dieses Diensts überprüfen, ob ihre Lösungen die Anforderungen von Microsoft erfüllen und in Azure Stack Hub wie erwartet funktionieren.

VaaS wird vorrangig für folgende Zwecke eingesetzt:

- Überprüfen neuer Azure Stack Hub-Lösungen
- Überprüfen von Änderungen an der Azure Stack Hub-Software
- Digitales Signieren der Pakete von Lösungspartnern, die während der Bereitstellung verwendet werden
- Anzeigen einer Vorschau von VaaS-Testbegleitmaterial

## <a name="validate-a-new-azure-stack-hub-solution"></a>Überprüfen einer neuen Azure Stack Hub-Lösung

Partner überprüfen mithilfe des Workflows **Lösungsvalidierung** neue Azure Stack Hub-Lösungen. Die Lösung muss die erforderlichen HLK-Komponententests (Hardware Lab Kit) von Azure Stack Hub bestehen. Zum Zertifizieren unterschiedlicher Hardwarekonfigurationen muss der Workflow für jede neue Lösung zweimal ausgeführt werden: je einmal für die Minimal- und die Maximalkonfiguration.

Weitere Informationen finden Sie unter [Überprüfen einer neuen Azure Stack Hub-Lösung](azure-stack-vaas-validate-solution-new.md).

## <a name="validate-changes-to-the-azure-stack-hub-software"></a>Überprüfen von Änderungen an der Azure Stack Hub-Software

Partner überprüfen mithilfe des Workflows für die **Paketvalidierung**, ob ihre Lösung mit den neuesten Updates der Azure Stack Hub-Software ausgeführt werden kann. Der Workflow für die Paketvalidierung muss in der von Microsoft empfohlenen Hardwareumgebung ausgeführt werden, in der Patch und Update (P&U) zur Anwendung des Updates verwendet wurde. Es wird empfohlen, den Workflow auch für den Baselinebuild auszuführen.

Weitere Informationen finden Sie unter [Überprüfen der Softwareupdates von Microsoft](azure-stack-vaas-validate-microsoft-updates.md).

## <a name="get-digitally-signed-solution-partner-packages"></a>Abrufen digital signierter Pakete von Lösungspartnern

Neben der Validierung von Azure Stack Hub-Updates verwenden Partner den Workflow **Paketvalidierung**, um Updates für OEM-Anpassungspakete zu überprüfen. Zu diesen Updates zählen partnerspezifische Azure Stack Hub-Treiber, Firmware und weitere Software, die während der Bereitstellung von Azure Stack Hub-Software verwendet wird. Stellen Sie das Paket, das Sie in der aktuellen Version der Azure Stack Hub-Software überprüfen, mindestens in der Lösung mit der unterstützten Minimalgröße bereit. Das Paket wird an VaaS übermittelt, bevor Tests ausgeführt werden. Wenn die Tests erfolgreich verlaufen, teilen Sie [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com) mit, dass die Tests für das Paket abgeschlossen wurden und das Paket mit der digitalen Signatur von Azure Stack Hub digital signiert werden sollte. Microsoft signiert das Paket und benachrichtigt den Azure Stack Hub-Partner darüber, dass das Paket zum Herunterladen im Azure Stack Hub-Validierungsportal verfügbar ist.

Weitere Informationen finden Sie unter [Überprüfen von OEM-Paketen](azure-stack-vaas-validate-oem-package.md).

## <a name="preview-vaas-test-collateral"></a>Anzeigen einer Vorschau von VaaS-Testbegleitmaterial

Microsoft stellt in Azure Stack Hub regelmäßig neue Features zur Verfügung. Im Rahmen des Entwicklungsprozesses für die Bereitstellung dieser Features auf dem Markt wird im Workflow für den **Testdurchlauf** neues Begleitmaterial für Tests zur Verfügung gestellt. Der Workflow für den Testdurchlauf umfasst Begleitmaterial für Tests aus den anderen Workflows, damit Tests inoffiziell ausgeführt werden können. Verwenden Sie den Workflow für den Testdurchlauf nicht, um Ergebnisse zur Genehmigung zu senden. Verwenden Sie den Workflow „Lösungsvalidierung“ und den Workflow „Paketvalidierung“, um eine offizielle Genehmigung für Ihre Lösung zu erhalten.

Weitere Informationen finden Sie unter [Quickstart: Planen des ersten Tests mithilfe des Azure Stack Hub-Validierungsportals](azure-stack-vaas-schedule-test-pass.md).

## <a name="validation-workflow-tests-summary"></a>Zusammenfassung der Tests für den Validierungsworkflow

| Validierungsworkflow | Erforderliche Tests |
|----|------------|
| [Neue Lösungsvalidierung](azure-stack-vaas-validate-solution-new.md) | Cloud-Simulationsmodul<br>Compute SDK Operational Suite<br>Disk Identification Test<br>KeyVault Extension SDK Operational Suite<br>KeyVault SDK Operational Suite<br>Network SDK Operational Suite<br>Storage Account SDK Operational Suite<br> |
| [OEM-Paketvalidierung](azure-stack-vaas-validate-oem-package.md) | Überprüfung des OEM-Erweiterungspakets<br>Cloud-Simulationsmodul |
| [Monatliche Updatevalidierung](azure-stack-vaas-validate-microsoft-updates.md) | Monthly Azure Stack Hub Update Verification (Monatliche Azure Stack Hub-Updateüberprüfung)<br>Cloud-Simulationsmodul<br> |

## <a name="next-steps"></a>Nächste Schritte

- [Einrichten Ihrer Validation-as-a-Service-Ressourcen](azure-stack-vaas-set-up-resources.md)
- Erfahren Sie mehr über [Wichtige Begriffe zu Validation-as-a-Service](azure-stack-vaas-key-concepts.md)
