---
title: Verwenden des Azure-Portals mit Azure Stack HCI
description: Anzeigen und Verwalten Ihrer Azure Stack HCI-Cluster mithilfe des Azure-Portals.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/15/2020
ms.openlocfilehash: e261c54140ed62a7b2fd19588e2b3ffc3b3911b8
ms.sourcegitcommit: 0579942530284d63472fb346cf5ff55de328cea4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/15/2020
ms.locfileid: "90560879"
---
# <a name="use-the-azure-portal-with-azure-stack-hci"></a>Verwenden des Azure-Portals mit Azure Stack HCI

> Gilt für: Azure Stack HCI, Version 20H2; Windows Server 2019

In diesem Thema wird erläutert, wie Sie eine Verbindung mit dem Azure Stack HCI-Teil des Azure-Portals herstellen, um eine globale Ansicht Ihrer Azure Stack HCI-Cluster anzuzeigen. Sie können das Azure-Portal verwenden, um Ihre Cluster zu verwalten und zu überwachen, auch wenn ihre physische Infrastruktur lokal gehostet wird. Cloudbasierte Überwachung macht die Aufrechterhaltung eines lokalen Überwachungssystems und einer lokalen Datenbank überflüssig, wodurch sich die Komplexität der Infrastruktur verringert. Ferner erhöht sich die Skalierbarkeit durch das Hochladen von Warnungen und anderen Informationen direkt in Azure, das täglich bereits Millionen von Objekten verwaltet.

## <a name="view-your-clusters-in-the-azure-portal"></a>Anzeigen Ihrer Cluster im Azure-Portal

Sobald ein Azure Stack HCI-Cluster registriert ist, sind seine Azure-Ressource im Azure-Portal sichtbar. Um ihn anzuzeigen, melden Sie sich zuerst beim [Azure-Portal](https://portal.azure.com) an. Wenn Sie [Ihren Cluster bereits bei Azure registriert haben](../deploy/register-with-azure.md), sollte eine neue Ressourcengruppe mit dem Namen Ihres Clusters mit einem angehängten „-rg“ angezeigt werden. Wenn Ihre Azure Stack HCI-Ressourcengruppe nicht angezeigt wird, suchen Sie nach „hci“, und wählen Sie Ihren Cluster im Dropdownmenü aus:

:::image type="content" source="media/azure-portal/azure-portal-search.png" alt-text="Durchsuchen des Azure-Portals nach „hci“, um Ihre Azure Stack HCI-Ressource zu finden":::

Auf der Startseite für den Azure Stack HCI-Dienst werden alle Ihre Cluster zusammen mit ihrer jeweiligen Ressourcengruppe, dem Standort und dem zugehörigen Abonnement aufgelistet.

:::image type="content" source="media/azure-portal/azure-portal-home.png" alt-text="Startseite für den Azure Stack HCI-Dienst im Azure-Portal":::

Klicken Sie auf eine Azure Stack HCI-Ressource, um die Übersichtsseite für diese Ressource anzuzeigen, auf der eine allgemeine Zusammenfassung der Cluster- und Serverknoten angezeigt wird.

:::image type="content" source="media/azure-portal/azure-portal-overview.png" alt-text="Zusammenfassende Übersichtsseite für eine Azure Stack HCI-Ressource im Azure-Portal":::

## <a name="view-the-activity-log"></a>Anzeigen des Aktivitätsprotokolls

Das Aktivitätsprotokoll bietet eine Liste der jüngsten Vorgänge und Ereignisse im Cluster zusammen mit deren Status, Uhrzeit, dem zugehörigen Abonnement und dem initiierenden Benutzer. Ereignisse können nach Abonnement, Schweregrad, Zeitspanne, Ressourcengruppe und Ressource gefiltert werden.

:::image type="content" source="media/azure-portal/azure-portal-activity-log.png" alt-text="Aktivitätsprotokoll-Bildschirm für eine Azure Stack HCI-Ressource im Azure-Portal":::

## <a name="configure-access-control"></a>Verwalten der Zugriffssteuerung

Verwenden Sie Zugriffssteuerung, um den Benutzerzugriff zu überprüfen, Rollen zu verwalten, Rollenzuweisungen hinzuzufügen und anzuzeigen sowie Zuweisungen zu verweigern.

:::image type="content" source="media/azure-portal/azure-portal-iam.png" alt-text="Zugriffssteuerungs-Bildschirm für eine Azure Stack HCI-Ressource im Azure-Portal":::

## <a name="add-and-edit-tags"></a>Hinzufügen und Bearbeiten von Tags

Tags sind Name/Wert-Paare, die Ihnen das Kategorisieren von Ressourcen und die Anzeige einer konsolidierten Abrechnung ermöglichen, indem Sie dasselbe Tag auf mehrere Ressourcen und Ressourcengruppen anwenden. Bei Tagnamen wird nicht zwischen Groß- und Kleinschreibung unterschieden und bei Tagwerten wird die Groß- und Kleinschreibung beachtet. [Weitere Informationen zu Tags](/azure/azure-resource-manager/management/tag-resources).

:::image type="content" source="media/azure-portal/azure-portal-tags.png" alt-text="Hinzufügen oder Bearbeiten von Tags für eine Azure Stack HCI-Ressource im Azure-Portal":::

## <a name="compare-azure-portal-and-windows-admin-center"></a>Vergleich des Azure-Portals mit dem Windows Admin Center

Im Gegensatz zum Windows Admin Center ist die Azure-Portal-Erfahrung für Azure Stack HCI für die Überwachung mehrerer Cluster im globalen Rahmen konzipiert. Anhand der Informationen in der folgenden Tabelle können Sie entscheiden, welches Verwaltungstool Ihren Anforderungen entspricht. Zusammen bieten sie ein konsistentes Design und erweisen sich in ergänzenden Szenarios als nützlich.

| Windows Admin Center | Azure-Portal |
| --------------- | --------------- |
| Verwaltung von lokaler Edge-Hardware und virtuellen Computern (VM), immer verfügbar | Verwaltung im großen Stil, zusätzliche Features |
| Verwalten der Azure Stack HCI-Infrastruktur | Verwalten weiterer Azure-Dienste |
| Überwachen und Aktualisieren einzelner Cluster | Überwachen und Aktualisieren im großen Stil |
| Manuelle Bereitstellung und Verwaltung von VMs | Self-Service-VMs aus Azure Arc |

## <a name="next-steps"></a>Nächste Schritte

Verwandte Informationen finden Sie außerdem unter:

- [Herstellen einer Verbindung von Azure Stack HCI mit Azure](../deploy/register-with-azure.md)
- [Überwachen von Azure Stack HCI mit Azure Monitor](azure-monitor.md)
