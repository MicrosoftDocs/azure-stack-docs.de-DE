---
title: Kontingenttypen in Azure Stack Hub
description: Anzeigen und Bearbeiten der unterschiedlichen Kontingenttypen, die für Dienste und Ressourcen in Azure Stack Hub verfügbar sind.
author: sethmanheim
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: sethm
ms.reviewer: xiaofmao
ms.lastreviewed: 12/07/2019
ms.openlocfilehash: bc36b4fe7326596f4ef993ceb087818270cf1b5a
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "80423857"
---
# <a name="quota-types-in-azure-stack-hub"></a>Kontingenttypen in Azure Stack Hub


[Kontingente](service-plan-offer-subscription-overview.md#plans) definieren die Grenzwerte von Ressourcen, die ein Benutzerabonnement bereitstellen oder nutzen kann. Beispielsweise kann ein Benutzer für ein Kontingent ggf. bis zu fünf virtuelle Computer erstellen. Jede Ressource verfügt über eigene Kontingenttypen.

> [!IMPORTANT]
> Es kann bis zu zwei Stunden dauern, bis neue Kontingente im Benutzerportal verfügbar sind oder ein geändertes Kontingent erzwungen wird.

## <a name="compute-quota-types"></a>Computekontingenttypen

| **Typ** | **Standardwert** | **Beschreibung** |
| --- | --- | --- |
| Maximale Anzahl von VMs | 50 | Die maximale Anzahl von virtuellen Computern, die ein Abonnement an diesem Speicherort erstellen kann |
| Maximal zulässige Anzahl von VM-Kernen | 100 | Die maximale Anzahl von Kernen, die ein Abonnement an diesem Speicherort erstellen kann (ein virtueller A3-Computer hat beispielsweise vier Kerne) |
| Maximale Anzahl von Verfügbarkeitsgruppen | 10 | Die maximale Anzahl von Verfügbarkeitsgruppen, die an diesem Speicherort erstellt werden können |
| Maximale Anzahl von VM-Skalierungsgruppen | 100 | Die maximale Anzahl von Skalierungsgruppen, die an diesem Speicherort erstellt werden können |
| Maximale Kapazität (in GB) eines verwalteten Datenträgers Standard | 2048 | Die maximale Kapazität von verwalteten Datenträgern Standard, die an diesem Speicherort erstellt werden können. Bei diesem Wert handelt es sich um die Summe der Zuordnungsgröße aller verwalteten Standard-Datenträger und der verwendeten Größe aller Standard-Momentaufnahmen. |
| Maximale Kapazität (in GB) eines verwalteten Datenträgers Premium | 2048 | Die maximale Kapazität von verwalteten Datenträgern Premium, die an diesem Speicherort erstellt werden können. Bei diesem Wert handelt es sich um die Summe der Zuordnungsgröße aller verwalteten Premium-Datenträger und der verwendeten Größe aller Premium-Momentaufnahmen. |

> [!NOTE]
> Die maximale Kapazität von nicht verwalteten Datenträgern (Seitenblobs) ist getrennt vom Kontingent der verwalteten Datenträger. Sie können diesen Wert unter **Speicherkontingente** in **Maximale Kapazität (GB)** festlegen.

## <a name="storage-quota-types"></a>Speicherkontingenttypen

| **Element** | **Standardwert** | **Beschreibung** |
| --- | --- | --- |
| Maximale Kapazität (GB) |2048 |Die Gesamtspeicherkapazität, die von einem Abonnement an diesem Speicherort genutzt werden kann Bei diesem Wert handelt es sich um die Summe der verwendeten Größe aller Blobs (einschließlich nicht verwalteter Datenträger) und aller zugehörigen Momentaufnahmen, Tabellen und Warteschlangen. |
| Gesamte Anzahl von Speicherkonten |20 |Die maximale Anzahl von Speicherkonten, die ein Abonnement an diesem Speicherort erstellen kann |

> [!NOTE]
> Wird der Wert für **Maximale Kapazität (GB)** in einem Abonnement überschritten, können Sie in diesem Abonnement keine neue Speicherressource erstellen. Die in diesem Abonnement erstellten nicht verwalteten Datenträger auf virtuellen Computer können zwar weiterhin verwendet werden, dies führt jedoch unter Umständen dazu, dass die genutzte Gesamtkapazität weit über die Kontingentgrenze hinausgeht.<br>Die maximale Kapazität der verwalteten Datenträger ist getrennt vom Gesamtspeicherkontigent. Sie können diesen Wert unter **Computekontingente** festlegen.

## <a name="network-quota-types"></a>Netzwerkkontingenttypen

| **Element** | **Standardwert** | **Beschreibung** |
| --- | --- | --- |
| Maximale Anzahl von virtuellen Netzwerken |50 |Die maximale Anzahl von virtuellen Netzwerken, die ein Abonnement an diesem Speicherort erstellen kann |
| Maximale Anzahl von Gateways für virtuelle Netzwerke |1 |Die maximale Anzahl von Gateways für virtuelle Netzwerke (VPN Gateways), die ein Abonnement an diesem Speicherort erstellen kann. |
| Maximale Anzahl von Netzwerkverbindungen |2 |Die maximale Anzahl von Netzwerkverbindungen (Point-to-Point oder Site-to-Site), die ein Abonnement für alle Gateways für virtuelle Netzwerke an diesem Speicherort erstellen kann. |
| Maximale Anzahl von öffentlichen IP-Adressen |50 |Die maximale Anzahl von öffentlichen IP-Adressen, die ein Abonnement an diesem Speicherort erstellen kann. |
| Maximale Anzahl von Netzwerkschnittstellenkarten (NICs) |100 |Die maximale Anzahl von Netzwerkschnittstellen, die ein Abonnement an diesem Speicherort erstellen kann |
| Maximale Anzahl von Lastenausgleichsmodulen |50 |Die maximale Anzahl von Lastenausgleichsmodulen, die ein Abonnement an diesem Speicherort erstellen kann |
| Maximale Anzahl von Netzwerksicherheitsgruppen |50 |Die maximale Anzahl von Netzwerksicherheitsgruppen, die ein Abonnement an diesem Speicherort erstellen kann |

## <a name="event-hubs-quota-types"></a>Event Hubs-Kontingenttypen

| **Typ** | **Standardwert** | **Beschreibung** |
| --- | --- | --- |
| Maximal zulässige Anzahl von VM-Kernen | 10 | Die maximale Anzahl von Kernen, die ein Abonnement an diesem Speicherort erstellen kann (ein virtueller A3-Computer hat beispielsweise vier Kerne) |

## <a name="view-an-existing-quota"></a>Anzeigen eines vorhandenen Kontingents

Es gibt zwei verschiedene Möglichkeiten, ein vorhandenes Kontingent anzuzeigen:

### <a name="plans"></a>Pläne

1. Wählen Sie im linken Navigationsbereich des Administratorportals **Pläne** aus.
2. Wählen Sie den Plan aus, für den Sie Details anzeigen möchten, indem Sie auf seinen Namen klicken.
3. Wählen Sie in dem Blatt, das geöffnet wird, **Dienste und Kontingente** aus.
4. Wählen Sie das Kontingent aus, das Sie anzeigen möchten, indem Sie in der Spalte **Name** darauf klicken.

    [![Kontingente im Azure Stack Hub-Administratorportal](media/azure-stack-quota-types/quotas1sm.png "Anzeigen von Kontingenten im Administratorportal")](media/azure-stack-quota-types/quotas1.png#lightbox)

### <a name="resource-providers"></a>Ressourcenanbieter

1. Suchen Sie im Standarddashboard des Verwaltungsportals nach der Kachel **Ressourcenanbieter**.
2. Wählen Sie den Dienst mit dem Kontingent aus, das Sie anzeigen möchten (beispielsweise **Compute**, **Netzwerk** oder **Speicher**).
3. Klicken Sie auf **Kontingente**, und wählen Sie anschließend das Kontingent aus, das Sie anzeigen möchten.

## <a name="edit-a-quota"></a>Bearbeiten eines Kontingents

Es gibt zwei Möglichkeiten, ein Kontingent zu bearbeiten:

### <a name="edit-a-plan"></a>Bearbeiten eines Plans

1. Wählen Sie im linken Navigationsbereich des Administratorportals **Pläne** aus.
2. Wählen Sie den Plan aus, für den Sie ein Kontingent bearbeiten möchten, indem Sie auf seinen Namen klicken.
3. Wählen Sie in dem Blatt, das geöffnet wird, **Dienste und Kontingente** aus.
4. Wählen Sie das Kontingent aus, das Sie bearbeiten möchten, indem Sie in der Spalte **Name** darauf klicken.

    [![Kontingente im Azure Stack Hub-Administratorportal](media/azure-stack-quota-types/quotas1sm.png "Anzeigen von Kontingenten im Administratorportal")](media/azure-stack-quota-types/quotas1.png#lightbox)

5. Wählen Sie auf dem Blatt, das geöffnet wird, **In Compute bearbeiten**, **Im Netzwerk bearbeiten** oder **Im Speicher bearbeiten** aus.

    ![Bearbeiten eines Plans im Azure Stack Hub-Administratorportal](media/azure-stack-quota-types/quotas3.png "Bearbeiten eines Plans im Azure Stack Hub-Administratorportal")

Alternativ können Sie dieses Verfahren verwenden, um ein Kontingent zu bearbeiten:

1. Suchen Sie im Standarddashboard des Verwaltungsportals nach der Kachel **Ressourcenanbieter**.
2. Wählen Sie den Dienst mit dem Kontingent aus, das Sie ändern möchten (beispielsweise **Compute**, **Netzwerk** oder **Speicher**).
3. Klicken Sie auf **Kontingente**, und wählen Sie anschließend das Kontingent aus, das Sie ändern möchten.
4. Bearbeiten Sie im Bereich **Speicherkontingente festlegen**, **Computekontingente festlegen** oder **Netzwerkkontingente festlegen** (je nachdem, welchen Typ von Kontingent Sie bearbeitet möchten) die Werte, und wählen Sie dann **Speichern** aus.

### <a name="edit-original-configuration"></a>Bearbeiten der ursprünglichen Konfiguration
  
Anstatt einen [Add-On-Plan zu verwenden](create-add-on-plan.md), können Sie auch die ursprüngliche Konfiguration eines Kontingents bearbeiten. Wenn Sie ein Kontingent bearbeiten, gilt die neue Konfiguration automatisch global für alle Pläne, die dieses Kontingent verwenden, sowie für alle vorhandenen Abonnements, die diese Pläne verwenden. Die Bearbeitung eines Kontingents ist unterschiedlich, wenn Sie einen Add-On-Plan verwenden, um ein geändertes Kontingent bereitzustellen, das ein Benutzer abonniert.

Die neuen Werte für das Kontingent gelten global für alle Pläne, die das geänderte Kontingent verwenden, sowie für alle vorhandenen Abonnements, die diese Pläne verwenden.

## <a name="next-steps"></a>Nächste Schritte

- [Erfahren Sie mehr über Dienste, Pläne, Angebote und Kontingente.](service-plan-offer-subscription-overview.md)
- [Erstellen Sie Kontingente, während Sie einen Plan erstellen.](azure-stack-create-plan.md)
