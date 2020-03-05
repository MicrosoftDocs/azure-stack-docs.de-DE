---
title: Nutzungsberichtsinfrastruktur für Cloudlösungsanbieter für Azure Stack Hub
description: Enthält Informationen zur Infrastruktur für die Erstellung von Nutzungsberichten, die zum Nachverfolgen der Nutzung für Mandanten eines Cloudlösungsanbieters (CSP) verwendet wird.
author: sethmanheim
ms.topic: article
ms.date: 01/22/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: 548bcef83794230a196b8afc9d6db11767678df5
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77700373"
---
# <a name="usage-reporting-infrastructure-for-cloud-solution-providers"></a>Nutzungsberichtsinfrastruktur für Cloudlösungsanbieter

Azure Stack Hub umfasst die erforderliche Infrastruktur zum Nachverfolgen der Nutzung in Echtzeit und leitet das Ergebnis an Azure weiter. In Azure verarbeitet Azure Commerce die [Nutzungsdaten und stellt die Nutzung den entsprechenden Azure-Abonnements in Rechnung](azure-stack-billing-and-chargeback.md). Dieser Prozess funktioniert genauso wie die Nachverfolgung der Nutzung in der globalen Azure-Cloud.

Einige Konzepte sind zwischen globalen Azure-Umgebungen und Azure Stack Hub konsistent. Azure Stack Hub verfügt über lokale Abonnements, die eine ähnliche Funktion wie ein Azure-Abonnement erfüllen. Lokale Abonnements sind nur lokal gültig. Lokale Abonnements werden Azure-Abonnements zugeordnet, wenn die Nutzung an Azure weitergeleitet wird.

Azure Stack Hub verfügt über lokale Nutzungszähler. Die lokale Nutzung wird den in Azure Commerce verwendeten Zählern zugeordnet. Allerdings unterscheiden sich die Zähler-IDs. Es sind lokal mehr Zähler verfügbar als derjenige, den Microsoft für die Abrechnung verwendet.

Zwischen Azure Stack Hub und Azure bestehen einige Unterschiede in der Preisgestaltung der Dienste. Beispielsweise basiert die Gebühr für virtuelle Computer in Azure Stack Hub allein auf der Nutzung von virtuellen Kernen pro Stunde, und für alle VM-Serien gilt derselbe Tarif. Das ist bei Azure anders. Der Grund ist, dass im globalen Azure die verschiedenen Preise unterschiedliche Hardware widerspiegeln. In Azure Stack Hub stellt der Kunde die Hardware bereit, sodass es keinen Grund gibt, für unterschiedliche VM-Klassen unterschiedliche Tarife zu berechnen.

Informationen zu den kommerziell verwendeten Azure Stack Hub-Verbrauchseinheiten und den zugehörigen Preisen finden Sie in Partner Center. Der Prozess entspricht dem Prozess für Azure-Dienste:

1. Navigieren Sie in Partner Center zum **Dashboardmenü**, und wählen Sie dann die Optionen **Verkaufen** und **Preise und Angebote** aus.
2. Wählen Sie unter **Nutzungsbasierte Dienste** die Option **Aktuell** aus.
3. Öffnen Sie das Arbeitsblatt **Azure in globaler CSP-Preisliste** .
4. Filtern Sie nach **Region = Azure Stack Hub**.

## <a name="terms-used-for-billing-and-usage"></a>Für Abrechnung und Nutzung verwendete Begriffe

Die folgenden Begriffe und-Konzepte werden für Abrechnung und Nutzung in Azure Stack Hub verwendet:

| Begriff | Definition |
| --- | --- |
| Direkter CSP-Partner | Ein direkter CSP-Partner erhält direkt von Microsoft eine Rechnung für die Nutzung von Azure sowie Azure Stack Hub und rechnet direkt mit seinen Kunden ab. |
| Indirekter CSP | Indirekte Vertriebspartner arbeiten mit einem indirekten Anbieter (auch als Verteiler bekannt) zusammen. Die Vertriebspartner werben Endkunden; der indirekte Anbieter unterhält die Abrechnungsbeziehung mit Microsoft, verwaltet die Kundenabrechnung und bietet zusätzliche Dienste wie Produktsupport. |
| Endkunde | Endkunden sind die Unternehmen und Behörden, die Besitzer der Apps und der anderen Workloads sind, die auf Azure Stack Hub ausgeführt werden. |

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen über das CSP-Programm finden Sie unter [Cloudlösungen](https://partner.microsoft.com/solutions/microsoft-cloud-solutions).
- Weitere Informationen zum Abrufen von Ressourcennutzungsinformationen aus Azure Stack Hub finden Sie unter [Verbrauch und Abrechnung in Azure Stack Hub](azure-stack-billing-and-chargeback.md).
