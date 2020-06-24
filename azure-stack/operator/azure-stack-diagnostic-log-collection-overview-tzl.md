---
title: Diagnoseprotokollsammlung in Azure Stack Hub
description: Erfahren Sie mehr über die Diagnoseprotokollsammlung in Azure Stack Hub – Hilfe und Support.
author: justinha
ms.topic: article
ms.date: 02/26/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 02/26/2020
ms.openlocfilehash: d3c6ecaa062f97aef76835d3c291b4ecaf405b11
ms.sourcegitcommit: 5f4f0ee043ff994efaad44129ce49be43c64d5dc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/16/2020
ms.locfileid: "84819468"
---
# <a name="diagnostic-log-collection-in-azure-stack-hub"></a>Diagnoseprotokollsammlung in Azure Stack Hub

::: moniker range=">= azs-2002"

Bei Azure Stack Hub handelt es sich um eine umfangreiche Sammlung von Windows-Komponenten und lokalen Azure-Diensten, die miteinander interagieren. Von allen diesen Komponenten und Diensten werden jeweils eigene Protokolle generiert. Daher haben wir eine nahtlose Umgebung für die Sammlung von Diagnoseprotokollen bereitgestellt, um eine effiziente Problemdiagnose durch den Microsoft-Support zu ermöglichen.

Die Sammlung von Diagnoseprotokollen in **Hilfe und Support**  unterstützt Operatoren bei der schnellen Sammlung und Weitergabe von Diagnoseprotokollen an den Microsoft-Support über eine einfache Benutzeroberfläche ohne PowerShell. Protokolle werden auch gesammelt, wenn andere Infrastrukturdienste ausgefallen sind.  

Es wird empfohlen, diesen Ansatz für die Protokollsammlung zu verwenden und nur dann auf einen [privilegierten Endpunkt (PEP)](azure-stack-get-azurestacklog.md) zurückzugreifen, wenn das Administratorportal oder das Blatt **Hilfe und Support** nicht verfügbar ist.

>[!NOTE]
>Azure Stack Hub muss registriert sein und über eine Internetverbindung verfügen, um die Diagnoseprotokollsammlung verwenden zu können. Sollte Azure Stack Hub nicht registriert sein, [verwenden Sie den privilegierten Endpunkt (PEP)](azure-stack-get-azurestacklog.md) für die Weitergabe von Protokollen.

![Optionen für die Diagnoseprotokollsammlung in Azure Stack Hub](media/azure-stack-help-and-support/banner-enable-automatic-log-collection.png)

## <a name="collection-options-and-data-handling"></a>Sammlungsoptionen und Datenverarbeitung

Das Feature für die Sammlung von Diagnoseprotokollen bietet zwei Optionen zum Senden von Protokollen. In der folgenden Tabelle werden die einzelnen Optionen sowie die jeweilige Verarbeitung der Daten erläutert:

### <a name="send-logs-proactively"></a>Proaktives Senden von Protokollen

Die [proaktive Protokollsammlung](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md) optimiert und vereinfacht die Sammlung von Diagnoseprotokollen, damit Kunden vor dem Erstellen einer Supportanfrage Protokolle an Microsoft senden können. Diagnoseprotokolle werden aus Azure Stack Hub proaktiv zur Analyse hochgeladen. Diese Protokolle werden nur gesammelt, wenn eine [Systemintegritätswarnung](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md#proactive-diagnostic-log-collection-alerts) ausgelöst wird, und der Microsoft-Support greift nur im Kontext einer Supportanfrage auf diese Protokolle zu.

#### <a name="how-the-data-is-handled"></a>Datenverarbeitung

Sie erklären sich damit einverstanden, dass Microsoft in regelmäßigen Abständen und ausschließlich auf der Grundlage von Azure Stack Hub-Systemintegritätswarnungen automatisch Protokolle gesammelt. Darüber hinaus erklären Sie sich damit einverstanden, dass diese Protokolle in ein von Microsoft verwaltetes und gesteuertes Azure-Speicherkonto hochgeladen und dort aufbewahrt werden.

Die Daten werden ausschließlich für die Problembehandlung im Zusammenhang mit Systemintegritätswarnungen genutzt und ohne Ihre Einwilligung nicht zu Marketing- oder Werbezwecken oder für andere gewerbliche Zwecke verwendet. Die Daten können bis zu 90 Tage aufbewahrt werden, und alle von Microsoft gesammelten Daten werden im Einklang mit unseren [üblichen Datenschutzprinzipien](https://privacy.microsoft.com/) verarbeitet.

Der Widerruf Ihrer Einwilligung hat keine Auswirkungen auf Daten, die bereits mit Ihrer Zustimmung gesammelt wurden.

Mit der **proaktiven Protokollsammlung** gesammelte Protokolle werden in ein von Microsoft verwaltetes und gesteuertes Azure-Speicherkonto hochgeladen. Auf diese Protokolle kann von Microsoft im Kontext einer Supportanfrage sowie zur Verbesserung der Integrität von Azure Stack Hub zugegriffen werden.

### <a name="send-logs-now"></a>Sofortiges Senden von Protokollen

Das [sofortige Senden von Protokollen](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md) ist eine manuelle Option, bei der Diagnoseprotokolle nur dann aus Azure Stack Hub hochgeladen werden, wenn Sie (als Kunde) die Sammlung initiieren, was in der Regel vor der Erstellung einer Supportanfrage der Fall ist.

Azure Stack-Operatoren können Diagnoseprotokolle bei Bedarf über das Administratorportal oder mit PowerShell an den Microsoft-Support senden. Wenn Azure Stack Hub mit Azure verbunden ist, empfiehlt sich das [sofortige Senden von Protokollen über das Administratorportal](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md), da die Protokolle so am einfachsten direkt an Microsoft gesendet werden können. Ist das Portal nicht verfügbar, sollten Operatoren stattdessen [Protokolle sofort per PowerShell senden](azure-stack-configure-on-demand-diagnostic-log-collection-powershell-tzl.md).

Falls Sie über keine Internetverbindung verfügen oder die Protokolle nur lokal speichern möchten, verwenden Sie die Methode [Get-AzureStackLog](azure-stack-get-azurestacklog.md) für den Protokollversand. Das folgende Flussdiagramm zeigt, welche Option jeweils zum Senden von Diagnoseprotokollen verwendet werden muss:

![Flussdiagramm zum sofortigen Senden von Protokollen an Microsoft](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

#### <a name="how-the-data-is-handled"></a>Datenverarbeitung

Durch Initiieren der Sammlung von Diagnoseprotokollen über Azure Stack Hub erklären Sie sich damit einverstanden, dass diese Protokolle in ein von Microsoft verwaltetes und gesteuertes Azure-Speicherkonto hochgeladen und dort aufbewahrt werden. Der Microsoft-Support kann im Rahmen der Supportanfrage umgehend und ohne weitere Kundeninteraktion hinsichtlich der Protokollsammlung auf diese Protokolle zugreifen.

Die Daten werden ausschließlich für die Problembehandlung im Zusammenhang mit Systemintegritätswarnungen genutzt und ohne Ihre Einwilligung nicht zu Marketing- oder Werbezwecken oder für andere gewerbliche Zwecke verwendet. Die Daten können bis zu 90 Tage aufbewahrt werden, und alle von Microsoft gesammelten Daten werden im Einklang mit unseren [üblichen Datenschutzprinzipien](https://privacy.microsoft.com/) verarbeitet.

Protokolle, die im Zuge des **sofortigen Sendens von Protokollen** gesammelt werden, werden in einen von Microsoft verwalteten und gesteuerten Speicher hochgeladen. Auf diese Protokolle wird von Microsoft im Kontext einer Supportanfrage sowie zur Verbesserung der Integrität von Azure Stack Hub zugegriffen werden.

## <a name="bandwidth-considerations"></a>Bandbreitenaspekte

Die durchschnittliche Größe der Sammlung von Diagnoseprotokollen variiert abhängig von der Art der Ausführung (proaktiv oder manuell). Bei der **proaktiven Protokollsammlung** beträgt die durchschnittliche Größe ca. 2 GB. Beim **sofortigen Senden von Protokollen** hängt die Größe davon ab, wie viele Stunden die Sammlung umfasst.

In der folgenden Tabelle sind Überlegungen zu Umgebungen mit eingeschränkten oder getakteten Azure-Verbindungen aufgelistet.

| Netzwerkverbindung | Auswirkung |
|----|---|
| Verbindung mit geringer Bandbreite/hoher Latenz | Der vollständige Protokollupload dauert längere Zeit. |
| Gemeinsam genutzte Verbindung | Der Upload kann sich auch auf andere Apps/Benutzer auswirken, die die gleiche Netzwerkverbindung nutzen. |
| Getaktete Verbindung | Möglicherweise fallen zusätzliche Nutzungsgebühren seitens Ihres ISPs für die Mehrnutzung des Netzwerks an. |

::: moniker-end
::: moniker range="<= azs-1910"

## <a name="collecting-logs-from-multiple-azure-stack-hub-systems"></a>Sammeln von Protokollen aus mehreren Azure Stack Hub-Systemen

Richten Sie für jede Azure Stack Hub-Skalierungseinheit, aus der Sie Protokolle sammeln möchten, einen Blobcontainer ein. Informationen zum Konfigurieren der Blobcontainer finden Sie unter [Konfigurieren der automatischen Azure Stack Hub-Diagnoseprotokollsammlung](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md). Es empfiehlt sich, nur Diagnoseprotokolle aus der gleichen Azure Stack Hub-Skalierungseinheit innerhalb eines einzelnen Blobcontainers zu speichern.

## <a name="retention-policy"></a>Aufbewahrungsrichtlinie

Erstellen Sie eine [Lebenszyklusverwaltungs-Regel](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts) in Azure Blob Storage, um die Protokoll-Aufbewahrungsrichtlinie zu verwalten. Wir empfehlen, Diagnoseprotokolle 30 Tage lang aufzubewahren. Melden Sie sich zum Erstellen einer Lebenszyklusverwaltungs-Regel in Azure Storage beim Azure-Portal an, wählen Sie **Speicherkonten** aus, wählen Sie den Blobcontainer aus, und wählen Sie dann unter **Blob-Dienst** die Option **Lebenszyklusverwaltung** aus.

![Lebenszyklusverwaltung im Azure-Portal](media/azure-stack-automatic-log-collection/blob-storage-lifecycle-management.png)

## <a name="sas-token-expiration"></a>SAS-Tokenablauf

Legen Sie den Ablauf der SAS-URL auf zwei Jahre fest. Sollten Sie jemals Ihre Speicherkontoschlüssel erneuern, achten Sie darauf, die SAS-URL neu zu generieren. Sie sollten das SAS-Token gemäß den bewährten Methoden verwalten. Weitere Informationen finden Sie unter [Bewährte Methoden bei der Verwendung von SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#best-practices-when-using-sas).

## <a name="bandwidth-consumption"></a>Bandbreitenverbrauch

Die durchschnittliche Größe der Sammlung von Diagnoseprotokollen schwankt, je nachdem, ob die Protokollsammlung bedarfsgesteuert oder automatisch ausgeführt wird.

Bei der bedarfsgesteuerten Protokollsammlung hängt die Größe der Protokollsammlung davon ab, wie viele Stunden erfasst werden. Sie können ein beliebiges gleitendes Fenster von 1–4 Stunden in den letzten sieben Tagen auswählen.

Wenn die automatische Sammlung von Diagnoseprotokollen aktiviert ist, überwacht der Dienst kritische Warnungen. Wenn eine kritische Warnung ausgelöst und etwa 30 Minuten lang aufrecht erhalten wird, sammelt der Dienst entsprechende Protokolle und lädt sie hoch. Die Größe dieser Protokollsammlung beträgt durchschnittlich ca. 2 GB. Bei einem Fehler bei einem Patch oder Update beginnt die automatische Protokollsammlung nur, wenn eine kritische Warnung ausgelöst wird und ungefähr 30 Minuten lang besteht. Es wird empfohlen, die [Anleitung zum Überwachen von Patches und Updates](azure-stack-updates.md) zu befolgen. Warnungsüberwachung, Protokollsammlung und Upload erfolgen für den Benutzer transparent.

In einem fehlerfreien System werden überhaupt keine Protokolle gesammelt. Bei einem fehlerhaften System wird die Protokollsammlung möglicherweise zweimal oder dreimal im Lauf eines Tages ausgeführt, in der Regel jedoch nur einmal. Maximal könnte sie in einem besonders ungünstigsten Szenario bis zu 10 Mal am Tag ausgeführt werden.  

Die folgende Tabelle kann dabei helfen, den Einfluss der aktivierten automatischen Protokollsammlung in Umgebungen mit eingeschränkten oder getakteten Verbindungen einzuschätzen.

| Netzwerkverbindung | Auswirkung |
|---|---|
| Verbindung mit geringer Bandbreite/hoher Latenz | Der vollständige Protokollupload dauert längere Zeit. | 
| Gemeinsam genutzte Verbindung | Der Upload kann sich auch auf andere Apps/Benutzer auswirken, die die gleiche Netzwerkverbindung nutzen. |
| Getaktete Verbindung | Möglicherweise fallen zusätzliche Nutzungsgebühren seitens Ihres ISPs für die Mehrnutzung des Netzwerks an. |

## <a name="managing-costs"></a>Kostenverwaltung

Gebühren für Azure [Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/) richten sich nach der Menge der pro Monat gespeicherten Daten und nach weiteren Faktoren, etwa der Datenredundanz. Wenn Sie nicht über ein Speicherkonto verfügen, können Sie sich beim Azure-Portal anmelden, **Speicherkonten** auswählen und die Schritte zum [Erstellen einer SAS-URL für Azure-Blobcontainer](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md) ausführen.

Es wird empfohlen, eine [Lebenszyklusverwaltungs-Richtlinie](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts) für Azure Blob Storage zu erstellen, um die laufenden Speicherkosten zu minimieren. Informationen zum Einrichten des Speicherkontos finden Sie unter [Konfigurieren der automatischen Azure Stack Hub-Diagnoseprotokollsammlung](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md).

::: moniker-end

## <a name="see-also"></a>Weitere Informationen

[Verarbeiten von Azure Stack Hub-Protokoll- und -Kundendaten](https://docs.microsoft.com/azure-stack/operator/azure-stack-data-collection)
