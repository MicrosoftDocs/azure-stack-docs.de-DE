---
title: Nutzungsberichtsinfrastruktur für Cloudlösungsanbieter für Azure Stack | Microsoft-Dokumentation
description: Enthält Informationen zur Infrastruktur für die Erstellung von Nutzungsberichten, die zum Nachverfolgen der Nutzung für Mandanten eines Cloudlösungsanbieters (CSP) verwendet wird.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: c170e450739609e0ef0be353f2354a17e39a28b1
ms.sourcegitcommit: b2d19e12a50195bb8925879ee75c186c9604f313
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/04/2019
ms.locfileid: "71961839"
---
# <a name="usage-reporting-infrastructure-for-cloud-solution-providers"></a>Nutzungsberichtsinfrastruktur für Cloudlösungsanbieter

Azure Stack umfasst die erforderliche Infrastruktur zum Nachverfolgen der Nutzung in Echtzeit und leitet das Ergebnis an Azure weiter. In Azure verarbeitet Azure Commerce die Nutzungsdaten und stellt die Nutzung den entsprechenden Azure-Abonnements in Rechnung. Dieser Prozess funktioniert genauso wie die Nachverfolgung der Nutzung in der globalen Azure-Cloud.

Einige Konzepte sind zwischen globalen Azure-Umgebungen und Azure Stack konsistent. Azure Stack verfügt über lokale Abonnements, die eine ähnliche Rolle wie ein Azure-Abonnement erfüllen. Lokale Abonnements sind nur lokal gültig. Lokale Abonnements werden Azure-Abonnements zugeordnet, wenn die Nutzung an Azure weitergeleitet wird.

Azure Stack verfügt über lokale Nutzungszähler. Die lokale Nutzung wird den in Azure Commerce verwendeten Zählern zugeordnet. Allerdings unterscheiden sich die Zähler-IDs. Es sind lokal mehr Zähler verfügbar als derjenige, den Microsoft für die Abrechnung verwendet.

Es gibt zwischen Azure Stack und Azure einige Unterschiede in der Art, wie Preise für Dienste berechnet werden. In Azure Stack basiert die Gebühr für virtuelle Computer – im Gegensatz zu Azure – z.B. allein auf „Virtuelle Kerne/Stunden“ mit demselben Tarif für alle VM-Serien. Der Grund ist, dass im globalen Azure die verschiedenen Preise unterschiedliche Hardware widerspiegeln. In Azure Stack stellt der Kunde die Hardware bereit, sodass es keinen Grund gibt, für unterschiedliche VM-Klassen unterschiedliche Tarife in Rechnung zu stellen.

Informationen zu den kommerziell verwendeten Azure Stack-Verbrauchseinheiten und den zugehörigen Preisen finden Sie im Partner Center. Der Prozess entspricht dem Prozess für Azure-Dienste:

1. Navigieren Sie in Partner Center zum **Dashboardmenü**, und wählen Sie dann die Optionen **Verkaufen** und **Preise und Angebote**.
2. Wählen Sie unter **Nutzungsbasierte Dienste** die Option **Aktuell** aus.
3. Öffnen Sie das Arbeitsblatt **Azure in globaler CSP-Preisliste** .
4. Filtern Sie nach **Region = Azure Stack**.

## <a name="terms-used-for-billing-and-usage"></a>Für Abrechnung und Nutzung verwendete Begriffe

Die folgenden Begriffe und-Konzepte werden für Abrechnung und Nutzung in Azure Stack verwendet:

| Begriff | Definition |
| --- | --- |
| Direkter CSP-Partner | Ein direkter CSP-Partner erhält direkt von Microsoft eine Rechnung für die Verwendung von Azure sowie Azure Stack und rechnet direkt mit seinen Kunden ab. |
| Indirekter CSP | Indirekte Vertriebspartner arbeiten mit einem indirekten Anbieter (auch als Verteiler bekannt) zusammen. Die Vertriebspartner werben Endkunden; der indirekte Anbieter unterhält die Abrechnungsbeziehung mit Microsoft, verwaltet die Kundenabrechnung und bietet zusätzliche Dienste wie Produktsupport. |
| Endkunde | Endkunden sind die Unternehmen und Behörden, die Besitzer der Apps und der anderen Arbeitslasten sind, die unter Azure Stack ausgeführt werden. |

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen über das CSP-Programm finden Sie unter [Cloudlösungen](https://partner.microsoft.com/solutions/microsoft-cloud-solutions).
- Weitere Informationen zum Abrufen von Ressourcennutzungsinformationen aus Azure Stack finden Sie unter [Verbrauch und Abrechnung in Azure Stack](azure-stack-billing-and-chargeback.md).
