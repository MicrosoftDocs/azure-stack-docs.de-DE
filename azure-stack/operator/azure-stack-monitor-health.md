---
title: Überwachen von Integrität und Warnungen in Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie die Integrität und Warnungen in Azure Stack überwacht werden.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: mabrigg
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: 4763025917172e3b8acaeb1ad748410e65143864
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782514"
---
# <a name="monitor-health-and-alerts-in-azure-stack"></a>Überwachen von Integrität und Warnungen in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Azure Stack umfasst Funktionen zur Infrastrukturüberwachung, mit denen Sie Informationen zu Integrität und Warnungen für eine Azure Stack-Region anzeigen können. Auf der Kachel **Regionsverwaltung**, die standardmäßig im Administratorportal für das Standardanbieterabonnement angeheftet ist, werden alle bereitgestellten Regionen von Azure Stack aufgelistet. Auf der Kachel wird die Anzahl der aktiven Benachrichtigungen mit dem Schweregrad „Kritisch“ und „Warnung“ für jede Region angezeigt. Die Kachel ist der Einstiegspunkt in die Integritäts- und Warnfunktion von Azure Stack.

![Kachel „Regionsverwaltung“](media/azure-stack-monitor-health/image1.png)

## <a name="understand-health-in-azure-stack"></a>Grundlegendes zur Integrität in Azure Stack

Die Integrität und Warnungen werden vom Integritätsressourcenanbieter verwaltet. Azure Stack-Infrastrukturkomponenten werden bei der Bereitstellung und Konfiguration von Azure Stack beim Integritätsressourcenanbieter registriert. Nach der Registrierung können die Integrität und Warnungen für jede Komponente angezeigt werden. Die Integrität in Azure Stack basiert auf einem einfachen Konzept. Wenn Warnungen für eine registrierte Instanz einer Komponente vorhanden sind, gibt der Integritätsstatus dieser Komponente den schlechtesten aktiven Warnungsschweregrad an, nämlich „Warnung“ oder „Kritisch“.

## <a name="alert-severity-definition"></a>Definition des Warnungsschweregrads

In Azure Stack werden Warnungen mit nur zwei Schweregraden ausgelöst: **Warnung** und **Kritisch**.

- **Warning**  
  Ein Operator kann die Warnung auf geplante Weise bearbeiten. Die Warnung wirkt sich in der Regel nicht auf Benutzerworkloads aus.

- **Critical** (Kritisch)  
  Ein Operator sollte kritische Warnungen mit Dringlichkeit bearbeiten. Hierbei handelt es sich um Probleme, die sich derzeit oder in Kürze auf Azure Stack-Benutzer auswirken.


## <a name="view-and-manage-component-health-state"></a>Anzeigen und Verwalten des Integritätsstatus von Komponenten

Sie können den Integritätsstatus der Komponenten im Administratorportal sowie über die Rest-API und PowerShell anzeigen.

Um den Integritätsstatus im Portal anzuzeigen, klicken Sie auf die Region, die in der Kachel **Regionsverwaltung** angezeigt werden soll. Sie können den Integritätsstatus der Infrastrukturrollen und der Ressourcenanbieter anzeigen.

![Liste von Infrastrukturrollen](media/azure-stack-monitor-health/image2.png)

Um ausführlichere Informationen anzuzeigen, klicken Sie auf einen Ressourcenanbieter oder eine Infrastrukturrolle.

> [!WARNING]  
> Wenn Sie auf eine Infrastrukturrolle und dann auf die Rolleninstanz klicken, werden Optionen zum **Starten**, **Neustarten** oder **Herunterfahren** angezeigt. Verwenden Sie diese Aktionen nicht beim Anwenden von Updates auf ein integriertes System. Verwenden Sie diese Optionen außerdem **nicht** in einer Azure Stack Development Kit-Umgebung. Diese Optionen sind nur für eine Umgebung mit integrierten Systemen konzipiert, in denen es mehr als eine Rolleninstanz pro Infrastrukturrolle gibt. Der Neustart einer Rolleninstanz (insbesondere „AzS-Xrp01“) im Development Kit führt zur Instabilität des Systems. Um Unterstützung bei der Problembehandlung zu erhalten, veröffentlichen Sie Ihr Problem im [Azure Stack-Forum](https://aka.ms/azurestackforum).
>

## <a name="view-alerts"></a>Anzeigen von Warnungen

Die Liste der aktiven Warnungen für jede Azure Stack-Region kann direkt über das Blatt **Regionsverwaltung** abgerufen werden. Die erste Kachel in der Standardkonfiguration ist die Kachel **Warnungen**, in der eine Zusammenfassung der Benachrichtigungen mit den Schweregraden „Kritisch“ und „Warnung“ für die Region angezeigt werden. Sie können die Kachel „Warnungen“ wie alle anderen Kacheln auf diesem Blatt für den Schnellzugriff an das Dashboard anheften.

![Kachel „Warnungen“ mit einer Warnung](media/azure-stack-monitor-health/image3.png)

Durch Auswahl des oberen Teils der Kachel **Warnungen** navigieren Sie zur Liste aller aktiven Warnungen für die Region. Wenn Sie in der Kachel das Zeilenelement **Kritisch** oder **Warnung** auswählen, navigieren Sie zu einer gefilterten Liste von Warnungen („Kritisch“ oder „Warnung“). 

Auf dem Blatt **Warnungen** können Benachrichtigungen nach Status („Aktiv“ oder „Geschlossen“) oder Schweregrad („Kritisch“ oder „Warnung“) gefiltert werden. Die Standardansicht zeigt alle aktiven Warnungen an. Alle geschlossene Warnungen werden nach sieben Tagen aus dem System entfernt.

![Filterbereich zum Filtern nach dem Status „Kritisch“ oder „Warnung“](media/azure-stack-monitor-health/alert-view.png)

Die Aktion **API anzeigen** zeigt die zum Generieren der Listenansicht verwendete REST-API an. Anhand dieser Aktion können Sie sich schnell mit der REST-API-Syntax vertraut machen, die Sie zum Abfragen von Warnungen verwenden können. Sie können diese API bei der Automatisierung oder Integration in Ihre vorhandenen Rechenzentrumslösungen für die Überwachung, Berichterstattung und Ticketausstellung verwenden.

Sie können auf eine bestimmte Warnung klicken, um die Warnungsdetails anzuzeigen. In den Warnungsdetails werden alle Felder angezeigt, die der Warnung zugeordnet sind. Zudem ermöglichen sie eine schnelle Navigation zur betroffenen Komponente und Quelle der Warnung. Beispielsweise tritt die folgende Warnung auf, wenn eine der Infrastrukturrolleninstanzen offline geschaltet wird oder nicht aufrufbar ist.  

![Blatt „Warnungsdetails“](media/azure-stack-monitor-health/alert-detail.png)

## <a name="repair-alerts"></a>„Reparieren“-Warnungen

In manchen Warnungen können Sie **Reparieren** auswählen.

Wenn Sie die Aktion **Reparieren** auswählen, werden spezifische Schritte für die Warnung ausgeführt, um zu versuchen, das Problem zu beheben. Nach Auswahl der Aktion ist der Status der Aktion **Reparieren** als Portalbenachrichtigung verfügbar.

![Reparatur wird ausgeführt](media/azure-stack-monitor-health/repair-in-progress.png)

Die Aktion **Reparieren** meldet den erfolgreichen Abschluss oder einen Fehler beim Abschließen der Aktion auf demselben Portalbenachrichtigungsblatt.  Wenn eine Reparaturaktion für eine Warnung fehlschlägt, können Sie die Aktion **Reparieren** aus den Details der Warnung erneut ausführen. Wird die Reparaturaktion erfolgreich abgeschlossen, führen Sie die Aktion **Reparieren** **nicht** erneut aus.

![Die Reparatur wird erfolgreich abgeschlossen](media/azure-stack-monitor-health/repair-completed.png)

Nachdem die Infrastrukturrolleninstanz wieder online geschaltet wurde, wird diese Warnung automatisch geschlossen. Nicht alle, aber viele Warnungen werden automatisch geschlossen, wenn das zugrunde liegende Problem behoben wird. Warnungen, die eine Schaltfläche für die Aktion „Reparieren“ bereitstellen, werden automatisch geschlossen, wenn das Problem von Azure Stack behoben wurde.  Bei allen anderen Warnungen wählen Sie nach Durchführung der Schritte zur Behebung **Warnung schließen** aus. Wenn das Problem weiterhin auftritt, erstellt Azure Stack eine neue Warnung. Wenn Sie das Problem behoben haben, bleibt die Warnung geschlossen und erfordert keine weiteren Schritte.

## <a name="next-steps"></a>Nächste Schritte

[Verwalten von Updates in Azure Stack](azure-stack-updates.md)

[Region management in Azure Stack](azure-stack-region-management.md) (Regionsverwaltung in Azure Stack)
