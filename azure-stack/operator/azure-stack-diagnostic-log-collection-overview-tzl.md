---
title: Übersicht über die Sammlung von Azure Stack Hub-Diagnoseprotokollen
description: Hier wird die Sammlung von Diagnoseprotokollen in „Hilfe und Support“ von Azure Stack Hub erläutert – einschließlich der bedarfsabhängigen und der proaktiven Protokollsammlung.
author: justinha
ms.topic: article
ms.date: 02/26/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 02/26/2020
ms.openlocfilehash: 8f97ecd20e7ef8db69033268baf96060e1315751
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/18/2020
ms.locfileid: "79520633"
---
# <a name="overview-of-azure-stack-hub-diagnostic-log-collection"></a>Übersicht über die Sammlung von Azure Stack Hub-Diagnoseprotokollen 

Bei Azure Stack Hub handelt es sich um eine umfangreiche Sammlung von Windows-Komponenten und lokalen Azure-Diensten, die miteinander interagieren. Von allen diesen Komponenten und Diensten werden jeweils eigene Protokolle generiert. Daher haben wir eine nahtlose Umgebung für die Sammlung von Diagnoseprotokollen bereitgestellt, um eine effiziente Problemdiagnose durch den Microsoft-Kundendienst (Customer Support Services, CSS) zu ermöglichen. 

Die Sammlung von Diagnoseprotokollen in „Hilfe und Support“ unterstützt Operatoren bei der schnellen Sammlung und Weitergabe von Diagnoseprotokollen an den Microsoft-Kundendienst (Customer Support Services, CSS) über eine einfache Benutzeroberfläche ohne PowerShell. Protokolle werden auch gesammelt, wenn andere Infrastrukturdienste ausgefallen sind.  
 
Es wird empfohlen, diesen Ansatz für die Protokollsammlung zu verwenden und nur dann auf einen [privilegierten Endpunkt (PEP)](azure-stack-get-azurestacklog.md) zurückzugreifen, wenn das Administratorportal oder das Blatt „Hilfe und Support“ nicht verfügbar ist. 

>[!NOTE]
>Azure Stack Hub muss registriert sein und über eine Internetverbindung verfügen, um die Diagnoseprotokollsammlung verwenden zu können. Sollte Azure Stack Hub nicht registriert sein, [verwenden Sie den privilegierten Endpunkt (PEP)](azure-stack-get-azurestacklog.md) für die Weitergabe von Protokollen. 

![Screenshot der Optionen für die Sammlung von Diagnoseprotokollen](media/azure-stack-help-and-support/banner-enable-automatic-log-collection.png)

## <a name="collection-options-and-data-handling"></a>Sammlungsoptionen und Datenverarbeitung

Das Feature für die Sammlung von Diagnoseprotokollen bietet zwei Optionen zum Senden von Protokollen. In der folgenden Tabelle werden die einzelnen Optionen sowie die jeweilige Verarbeitung der Daten erläutert: 

### <a name="send-logs-proactively"></a>Proaktives Senden von Protokollen

Die [proaktive Protokollsammlung](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md) optimiert und vereinfacht die Sammlung von Diagnoseprotokollen, damit Kunden vor dem Erstellen einer Supportanfrage Protokolle an Microsoft senden können. Diagnoseprotokolle werden aus Azure Stack Hub proaktiv zur Analyse hochgeladen. Diese Protokolle werden nur gesammelt, wenn eine [Systemintegritätswarnung](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md#proactive-diagnostic-log-collection-alerts) ausgelöst wird, und der Kundendienst greift nur im Kontext einer Supportanfrage auf diese Protokolle zu.


#### <a name="how-the-data-is-handled"></a>Datenverarbeitung

Sie erklären sich damit einverstanden, dass Microsoft in regelmäßigen Abständen und ausschließlich auf der Grundlage von Azure Stack Hub-Systemintegritätswarnungen automatisch Protokolle gesammelt. Darüber hinaus erklären Sie sich damit einverstanden, dass diese Protokolle in ein von Microsoft verwaltetes und gesteuertes Azure-Speicherkonto hochgeladen und dort aufbewahrt werden. 

Die Daten werden ausschließlich für die Problembehandlung im Zusammenhang mit Systemintegritätswarnungen genutzt und ohne Ihre Einwilligung nicht zu Marketing- oder Werbezwecken oder für andere gewerbliche Zwecke verwendet. Die Daten können bis zu 90 Tage aufbewahrt werden, und alle von Microsoft gesammelten Daten werden im Einklang mit unseren [üblichen Datenschutzprinzipien](https://privacy.microsoft.com/) verarbeitet.

Der Widerruf Ihrer Einwilligung hat keine Auswirkungen auf Daten, die bereits mit Ihrer Zustimmung gesammelt wurden.

Mit der **proaktiven Protokollsammlung** gesammelte Protokolle werden in ein von Microsoft verwaltetes und gesteuertes Azure-Speicherkonto hochgeladen. Auf diese Protokolle kann von Microsoft im Kontext einer Supportanfrage sowie zur Verbesserung der Integrität von Azure Stack Hub zugegriffen werden.

### <a name="send-logs-now"></a>Sofortiges Senden von Protokollen

Das [sofortige Senden von Protokollen](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md) ist eine manuelle Option, bei der Diagnoseprotokolle nur dann aus Azure Stack Hub hochgeladen werden, wenn Sie (als Kunde) die Sammlung initiieren, was in der Regel vor der Erstellung einer Supportanfrage der Fall ist. 

Azure Stack-Operatoren können Diagnoseprotokolle bei Bedarf über das Administratorportal oder per PowerShell an den Microsoft-Kundendienst (Microsoft Customer Support Services, CSS) senden. Wenn Azure Stack Hub mit Azure verbunden ist, empfiehlt sich das [sofortige Senden von Protokollen über das Administratorportal](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md), da die Protokolle so am einfachsten direkt an Microsoft gesendet werden können. Ist das Portal nicht verfügbar, sollten Operatoren stattdessen [Protokolle sofort per PowerShell senden](azure-stack-configure-on-demand-diagnostic-log-collection-powershell-tzl.md). 

Falls Sie über keine Internetverbindung verfügen oder die Protokolle nur lokal speichern möchten, verwenden Sie die Methode [Get-AzureStackLog](azure-stack-get-azurestacklog.md) für den Protokollversand. Das folgende Flussdiagramm zeigt, welche Option jeweils zum Senden von Diagnoseprotokollen verwendet werden muss: 

![Flussdiagramm zum sofortigen Senden von Protokollen an Microsoft](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

#### <a name="how-the-data-is-handled"></a>Datenverarbeitung

Durch Initiieren der Sammlung von Diagnoseprotokollen über Azure Stack Hub erklären Sie sich damit einverstanden, dass diese Protokolle in ein von Microsoft verwaltetes und gesteuertes Azure-Speicherkonto hochgeladen und dort aufbewahrt werden. Der Microsoft-Kundendienst kann im Rahmen der Supportanfrage umgehend und ohne weitere Kundeninteraktion hinsichtlich der Protokollsammlung auf diese Protokolle zugreifen. 

Die Daten werden ausschließlich für die Problembehandlung im Zusammenhang mit Systemintegritätswarnungen genutzt und ohne Ihre Einwilligung nicht zu Marketing- oder Werbezwecken oder für andere gewerbliche Zwecke verwendet. Die Daten können bis zu 90 Tage aufbewahrt werden, und alle von Microsoft gesammelten Daten werden im Einklang mit unseren [üblichen Datenschutzprinzipien](https://privacy.microsoft.com/) verarbeitet. 

Protokolle, die im Zuge des sofortigen Sendens von Protokollen gesammelt werden, werden in einen von Microsoft verwalteten und gesteuerten Speicher hochgeladen. Auf diese Protokolle wird von Microsoft im Kontext einer Supportanfrage sowie zur Verbesserung der Integrität von Azure Stack Hub zugegriffen werden. 

## <a name="bandwidth-considerations"></a>Bandbreitenaspekte

Die durchschnittliche Größe der Sammlung von Diagnoseprotokollen variiert abhängig von der Art der Ausführung (proaktiv oder manuell). Bei der **proaktiven Protokollsammlung** beträgt die durchschnittliche Größe ca. 2 GB. Beim **sofortigen Senden von Protokollen** hängt die Größe davon ab, wie viele Stunden die Sammlung umfasst.

In der folgenden Tabelle sind Überlegungen zu Umgebungen mit eingeschränkten oder getakteten Azure-Verbindungen aufgelistet.


| Netzwerkverbindung | Auswirkung |
|--------------------|--------|
| Verbindung mit geringer Bandbreite/hoher Latenz | Der vollständige Upload des Protokolls dauert längere Zeit | 
| Gemeinsam genutzte Verbindung | Der Upload kann sich auch auf andere Anwendungen/Benutzer auswirken, die die gleiche Netzwerkverbindung nutzen |
| Getaktete Verbindung | Möglicherweise fallen zusätzliche Nutzungsgebühren seitens Ihres ISPs für die Mehrnutzung des Netzwerks an | 

## <a name="see-also"></a>Weitere Informationen

[Verarbeiten von Azure Stack Hub-Protokoll- und -Kundendaten](https://docs.microsoft.com/azure-stack/operator/azure-stack-data-collection)

