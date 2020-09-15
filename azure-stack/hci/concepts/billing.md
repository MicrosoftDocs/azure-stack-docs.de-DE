---
title: Abrechnung und Zahlung für die Azure Stack HCI
description: So funktionieren Abrechnung und Zahlung für die Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/01/2020
ms.openlocfilehash: 22758f9a6b905e2c948a47c5870a05edcb63acdf
ms.sourcegitcommit: 1c5e7d8419037c0f3ef6fe9d8e6bfb6a59659c84
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2020
ms.locfileid: "89428480"
---
# <a name="azure-stack-hci-billing-and-payment"></a>Abrechnung und Zahlung für die Azure Stack HCI

> Gilt für: Azure Stack HCI, Version 20H2

Die Azure Stack HCI ist ein Azure-Dienst, der genau wie jeder andere Azure-Dienst in Ihrer Azure-Abonnementabrechnung aufgeführt wird. Es ist keine herkömmliche lokale Softwarelizenz erforderlich. Gast-VMs benötigen jedoch möglicherweise eine individuelle Betriebssystemlizenzierung. Währungen und Rabatte werden zentral von der Azure Commerce-Abrechnungsplattform verarbeitet, und der Kunde erhält am Ende des Monats eine einheitliche, aufgeschlüsselte Rechnung.

## <a name="what-does-azure-stack-hci-charge-for"></a>Welche Gebühren fallen für die Azure Stack HCI an?

Das Azure Stack HCI-Abrechnungsmodell im Stil der Cloud ist für Kunden, die Azure oder einen anderen Clouddienst bereits nutzen, konsistent, vertraut und einfach. Während der öffentlichen Vorschau fallen keine Kosten an. Nach Ende der Vorschau erfolgt die Abrechnung basierend auf einem Pauschalpreis pro physischem Prozessorkern in einem Azure Stack HCI-Cluster. (Bei Verwendung anderer Azure-Dienste fallen weitere Nutzungsgebühren an.)

Das Preismodell unterscheidet sich von einer nutzungsbasierten oder verbrauchsbasierten Abrechnung, die auf der Anzahl von VMs beruht. Auch wenn die Anzahl virtueller Prozessorkerne (vCPUs) im Laufe des Monats variiert, wirkt sich dies nicht auf den Preis aus, den Sie für die Azure Stack HCI bezahlen: Die Abrechnung basiert weiterhin auf der Anzahl physischer Kerne, die im Cluster vorhanden sind.

## <a name="advantages-of-the-azure-stack-hci-billing-model"></a>Vorteile des Azure Stack HCI-Abrechnungsmodells

- Es ist einfach: Es gibt keine Beschränkungen nach oben oder unten im Hinblick auf Arbeitsspeicher, Speicherplatz oder Netzwerk-E/A. Komplizierte Berechnungen sind daher nicht erforderlich.
- Es belohnt Kunden, die ihre Virtualisierungsinfrastruktur effizienter, mit einer höheren VM-Dichte, ausführen.
- Die Kosten für Ihre lokale Azure Stack HCI-Bereitstellung sind leicht herauszufinden, und die Kosten sind vom Netzwerkedge bis zum Rechenzentrum transparent gestaffelt.

## <a name="how-the-number-of-processor-cores-is-assessed"></a>Auswertung der Anzahl von Prozessorkernen

Um die Anzahl von Kernen in einem Cluster zu ermitteln, überprüft die Azure Stack HCI regelmäßig die Anzahl physischer Kerne und meldet Sie an Azure. Wenn die Verbindung nur gelegentlich besteht oder unterbrochen wird, ist das kein Problem. Der Vorgang kann jederzeit später wiederholt werden. Tage oder Wochen von Kerndaten können gleichzeitig hochgeladen werden. Kunden müssen sich mindestens einmal alle 30 Tage mit Azure verbinden, um die Abrechnung zu ermöglichen.

Verwenden Sie das Cmdlet **`Sync-AzureStackHCI`** , um Kerndaten manuell in Azure hochzuladen.

## <a name="faq"></a>Häufig gestellte Fragen

- Wenn ich bereits über ein Azure-Abonnement verfüge, kann ich es für die Azure Stack HCI verwenden? **Ja**
- Wenn die Finanzabteilung meiner Organisation bereits Ausgaben in Azure genehmigt hat, wird die Azure Stack HCI dadurch abgedeckt? **Ja**
- Wenn ich eine Azure-Zahlungsverpflichtung eingegangen bin, kann ich diese auf die Azure Stack HCI anrechnen? **Ja**
- Wenn ich über Azure-Guthaben verfüge (z. B. für Schüler/Studenten oder als Preis erhalten), kann ich dieses für die Azure Stack HCI verwenden? **Ja**
- Wenn meine Organisation einen Enterprise Agreement-Rabatt ausgehandelt hat, gilt dieser auch für die Azure Stack HCI? **Ja**
- Funktionieren die Kostenverwaltungstools im Azure-Portal für Azure Stack HCI? **Ja**
- Können Drittanbieter- oder benutzerdefinierte Tools, die mit den Azure-Abrechnungs-APIs erstellt wurden, für die Azure Stack HCI verwendet werden? **Ja**

## <a name="next-steps"></a>Nächste Schritte

Verwandte Informationen:

- [Preisübersicht: Informationen zur Azure-Preisgestaltung](https://azure.microsoft.com/pricing/)
- [Azure Cost Management und Abrechnung](/azure/cost-management-billing/cost-management-billing-overview)
