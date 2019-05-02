---
title: Nutzungsberichtsinfrastruktur für Cloud-Dienstanbieter für Azure Stack | Microsoft-Dokumentation
description: Azure Stack umfasst die erforderliche Infrastruktur zum Nachverfolgen der Nutzung für von einem Cloud-Dienstanbieter verwalteten Mandanten in Echtzeit und leitet das Ergebnis an Azure weiter.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 01/05/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: alfredop
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: 1b8b83491211ae26493a6bb41c7f0f219f47f620
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "64307194"
---
# <a name="usage-reporting-infrastructure-for-cloud-service-providers"></a>Nutzungsberichtsinfrastruktur für Cloud-Dienstanbieter

Azure Stack umfasst die erforderliche Infrastruktur zum Nachverfolgen der Nutzung in Echtzeit und leitet das Ergebnis an Azure weiter. In Azure verarbeitet Azure Commerce die Nutzungsdaten und stellt die Nutzung den entsprechenden Azure-Abonnements in Rechnung. Dies erfolgt auf die gleiche Weise wie die Nutzungsnachverfolgung in der globalen Azure-Cloud.

Sie sollten beachten, dass bestimmte Konzepte zwischen globalem Azure und Azure Stack konsistent sind. Azure Stack verfügt über lokale Abonnements, die eine ähnliche Rolle wie ein Azure-Abonnement erfüllen. Lokale Abonnements sind nur lokal gültig. Lokale Abonnements werden Azure-Abonnements zugeordnet, wenn die Nutzung an Azure weitergeleitet wird.

Azure Stack verfügt über lokale Nutzungszähler. Die lokale Nutzung wird den in Azure Commerce verwendeten Zählern zugeordnet. Allerdings unterscheiden sich die Zähler-IDs. Es sind lokal mehr Zähler verfügbar als derjenige, den Microsoft für die Abrechnung verwendet.

Es gibt zwischen Azure Stack und Azure einige Unterschiede in der Art, wie Preise für Dienste berechnet werden. In Azure Stack basiert die Gebühr für virtuelle Computer – im Gegensatz zu Azure – z.B. allein auf „Virtuelle Kerne/Stunden“ mit demselben Tarif für alle VM-Serien. Der Grund ist, dass im globalen Azure die verschiedenen Preise unterschiedliche Hardware widerspiegeln. In Azure Stack stellt der Kunde die Hardware bereit, sodass es keinen Grund gibt, für unterschiedliche VM-Klassen unterschiedliche Tarife in Rechnung zu stellen.

Sie können sich über die in Commerce verwendeten Azure Stack-Zähler und deren Preise auf die gleiche Weise wie im Fall von Azure-Diensten im Partner Center informieren:

1. Wechseln Sie im Partner Center zu **Dashboardmenü** > **Preise und Angebote**.
2. Wählen Sie unter **Nutzungsbasierte Dienste** die Option **Aktuell** aus.
3. Öffnen Sie das Arbeitsblatt **Azure in globaler CSP-Preisliste** .
4. Filtern Sie nach **Region = Azure Stack**.

## <a name="usage-and-billing-error-codes"></a>Nutzungs- und Abrechnungsfehlercodes

Beim Hinzufügen von Mandanten zu einer Registrierung können die folgenden Fehlermeldungen auftreten.

| Error                           | Details                                                                                                                                                                                                                                                                                                                           | Kommentare                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|---------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **RegistrationNotFound**            | Die bereitgestellte Registrierung wurde nicht gefunden. Stellen Sie sicher, dass die folgenden Informationen ordnungsgemäß bereitgestellt wurden:<br>1. Abonnementbezeichner (bereitgestellter Wert: _Abonnementbezeichner_),<br>2. Ressourcengruppe (bereitgestellter Wert: _Ressourcengruppe_),<br>3. Registrierungsname (bereitgestellter Wert: _Registrierungsname_).                             | Dieser Fehler tritt gewöhnlich auf, wenn die Informationen, die auf die anfängliche Registrierung verweisen, nicht korrekt sind. Wenn Sie die Ressourcengruppe und den Namen Ihrer Registrierung überprüfen müssen, finden Sie beides im Azure-Portal, indem Sie alle Ressourcen auflisten. Wenn Sie mehrere Registrierungsressourcen finden, sehen Sie sich die **CloudDeploymentID** in den Eigenschaften an, und wählen Sie die Registrierung, deren **CloudDeploymentID** derjenigen Ihrer Cloud entspricht. Um die **CloudDeploymentID** zu finden, können Sie diesen PowerShell-Befehl auf Azure Stack anwenden:<br>`$azureStackStampInfo = Invoke-Command -Session $session -ScriptBlock { Get-AzureStackStampInformation }` |
| **BadCustomerSubscriptionId**       | Der bereitgestellte _Kundenabonnementbezeichner_ und _Registrierungsnamen_-Abonnementbezeichner befinden sich nicht im Besitz des gleichen Microsoft-Cloud-Dienstanbieters. Überprüfen Sie, ob der Kundenabonnementbezeichner richtig ist. Wenden Sie sich an den Support, wenn das Problem weiterhin besteht. | Dieser Fehler tritt auf, wenn das Kundenabonnement ein CSP-Abonnement ist, aber zu einem anderen CSP-Partner als das in der anfänglichen Registrierung verwendete Abonnement gehört. Diese Prüfung soll verhindern, dass ein CSP-Partner die Abrechnung erhält, der nicht für den verwendeten Azure Stack verantwortlich ist.                                                                                                                                                                                                                                                                          |
| **InvalidCustomerSubscriptionId**   | Der angegebene „_Kundenabonnementbezeichner_“ ist ungültig. Stellen Sie sicher, dass ein gültiges Azure-Abonnement bereitgestellt wird.                                                                                                                                                                         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| **CustomerSubscriptionNotFound**    | Der _Kundenabonnementbezeichner_ wurde nicht unter dem Registrierungsnamen gefunden. Stellen Sie sicher, dass ein gültiges Azure-Abonnement verwendet wird, und dass der Abonnementbezeichner mithilfe von PUT der Registrierung hinzugefügt wurde.                                                   | Dieser Fehler tritt auf, wenn versucht wird, zu überprüfen, ob ein Mandant dem Abonnement hinzugefügt wurde, und festgestellt wird, dass das Kundenabonnement nicht der Registrierung zugeordnet ist. Der Kunde wurde der Registrierung nicht hinzugefügt, bzw. die Abonnement-ID wurde falsch geschrieben.                                                                                                                                                                                                                                                                                                                                |
| **UnauthorizedCspRegistration**     | Der bereitgestellte _Registrierungsname_ ist nicht mehrinstanzenfähig. Senden Sie eine E-Mail an azstCSP@microsoft.com, und beziehen Sie Registrierungsnamen, Ressourcengruppe und den in der Registrierung verwendeten Abonnementbezeichner ein.                                                                                    | Die Mehrinstanzenfähigkeit einer Registrierung muss von Microsoft bestätigt werden, bevor Sie beginnen können, ihr Mandanten hinzuzufügen.                                                                                                                                                                                                                                                                                                                                                                                             |
| **CustomerSubscriptionsNotAllowed** | Kundenabonnementvorgänge werden für nicht verbundene Kunden nicht unterstützt. Um dieses Feature verwenden zu können, ist eine erneute Registrierung mit Lizenzierung für nutzungsbasierte Bezahlung erforderlich.                                                                                                                                                                    | Die Registrierung, der Sie Mandanten hinzuzufügen versuchen, ist eine Kapazitätsregistrierung, d.h., beim Erstellen der Registrierung wurde der Parameter `BillingModel Capacity` verwendet. Nur Registrierungen mit nutzungsbasierter Bezahlung können Mandanten hinzufügen. Sie müssen sich erneut mit dem Parameter `BillingModel PayAsYouUse` registrieren.                                                                                                                                                                                                                                                                                          |
| **InvalidCSPSubscription**          | Der bereitgestellte _Kundenabonnementbezeichner_ ist kein gültiges CSP-Abonnement. Stellen Sie sicher, dass ein gültiges Azure-Abonnement bereitgestellt wird.                                                                                                                                                        | Dies liegt wahrscheinlich daran, dass das Kundenabonnement falsch geschrieben wurde.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| **MetadataResolverBadGatewayError** | Einer der Upstreamserver hat einen unerwarteten Fehler zurückgegeben. Versuchen Sie es später erneut. Wenden Sie sich an den Support, wenn das Problem weiterhin besteht.                                                                                                                                                                                                |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |

## <a name="terms-used-for-billing-and-usage"></a>Für Abrechnung und Nutzung verwendete Begriffe

Die folgenden Begriffe und-Konzepte werden für Abrechnung und Nutzung in Azure Stack verwendet:

| Begriff | Definition |
| --- | --- |
| Direkter CSP-Partner | Ein direkter Cloud-Dienstanbieter-Partner (Cloud Solution Provider, CSP) erhält direkt von Microsoft eine Rechnung für die Verwendung von Azure sowie Azure Stack und rechnet direkt mit seinen Kunden ab. |
| Indirekter CSP | Indirekte Vertriebspartner arbeiten mit einem indirekten Anbieter (auch als Verteiler bekannt) zusammen. Die Vertriebspartner werben Endkunden; der indirekte Anbieter unterhält die Abrechnungsbeziehung mit Microsoft, verwaltet die Kundenabrechnung und bietet zusätzliche Dienste wie Produktsupport. |
| Endkunde | Endkunden sind die Unternehmen und Regierungsbehörden, die Besitzer der Anwendungen und anderen Arbeitslasten sind, die auf Azure Stack ausgeführt werden. |

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen über das CSP-Programm finden Sie unter [Programm für Cloud-Lösungsanbieter](https://partner.microsoft.com/solutions/microsoft-cloud-solutions).
- Weitere Informationen zum Abrufen von Ressourcennutzungsinformationen aus Azure Stack finden Sie unter [Verbrauch und Abrechnung in Azure Stack](azure-stack-billing-and-chargeback.md).

<!-- Update_Description: wording update -->