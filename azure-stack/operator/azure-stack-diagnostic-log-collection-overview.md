---
title: Diagnoseprotokollsammlung in Azure Stack Hub
description: Erfahren Sie mehr über die Diagnoseprotokollsammlung in Azure Stack Hub – Hilfe und Support.
author: justinha
ms.topic: article
ms.date: 05/11/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 05/11/2020
ms.openlocfilehash: c924c5a48337106c08d1112328c32c031d7371bc
ms.sourcegitcommit: 52b33ea180c38a5ecce150f5a9ea4a026344cc3d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/11/2020
ms.locfileid: "88074235"
---
# <a name="diagnostic-log-collection-in-azure-stack-hub"></a>Diagnoseprotokollsammlung in Azure Stack Hub


Bei Azure Stack Hub handelt es sich um eine umfangreiche Sammlung von Windows-Komponenten und lokalen Azure-Diensten, die miteinander interagieren. Von allen diesen Komponenten und Diensten werden jeweils eigene Protokolle generiert. Daher haben wir eine nahtlose Umgebung für die Sammlung von Diagnoseprotokollen bereitgestellt, um eine effiziente Problemdiagnose durch den Microsoft-Support zu ermöglichen.

Die Sammlung von Diagnoseprotokollen in **Hilfe und Support**  unterstützt Operatoren bei der schnellen Sammlung und Weitergabe von Diagnoseprotokollen an den Microsoft-Support über eine einfache Benutzeroberfläche ohne PowerShell. Protokolle werden auch gesammelt, wenn andere Infrastrukturdienste ausgefallen sind.  

Die Sammlung von Diagnoseprotokollen in „Hilfe und Support“ unterstützt Operatoren bei der schnellen Sammlung und Weitergabe von Diagnoseprotokollen an den Microsoft-Kundendienst (Customer Support Services, CSS) über eine einfache Benutzeroberfläche ohne PowerShell. Protokolle werden auch gesammelt, wenn andere Infrastrukturdienste ausgefallen sind.  

::: moniker range=">= azs-2002"

Es wird empfohlen, diesen Ansatz für die Protokollsammlung zu verwenden und nur dann auf einen [privilegierten Endpunkt (PEP)](azure-stack-get-azurestacklog.md) zurückzugreifen, wenn das Administratorportal oder das Blatt „Hilfe und Support“ nicht verfügbar ist. 

>[!NOTE]
>Azure Stack Hub muss registriert sein, um die Diagnoseprotokollsammlung verwenden zu können. Ist Azure Stack Hub nicht registriert, verwenden Sie [Get-AzureStackLog](azure-stack-get-azurestacklog.md), um Protokolle weiterzugeben. 

![Optionen für die Diagnoseprotokollsammlung in Azure Stack Hub](media/azure-stack-help-and-support/banner-enable-automatic-log-collection.png)

## <a name="collection-options-and-data-handling"></a>Sammlungsoptionen und Datenverarbeitung

::: moniker-end
::: moniker range=">= azs-2005"

Azure Stack Hub verfügt (abhängig von der Konnektivität mit Azure) über geeignete Möglichkeiten, um Diagnoseprotokolle zu erfassen, zu speichern und an CSS zu senden. Wenn Azure Stack Hub eine Verbindung mit Azure herstellen kann, empfiehlt es sich, die **proaktive Protokollsammlung** zu aktivieren. Dadurch werden automatisch Diagnoseprotokolle in ein von Microsoft gesteuertes Speicherblob in Azure hochgeladen, wenn eine kritische Warnung ausgelöst wird. Alternativ können Sie Protokolle mithilfe der Option zum **sofortigen Senden von Protokollen** bei Bedarf erfassen oder Protokolle lokal speichern, wenn keine Verbindung zwischen Azure Stack Hub und Azure besteht. 

In den folgenden Abschnitten werden die einzelnen Optionen sowie die jeweilige Verarbeitung der Daten erläutert. 

::: moniker-end

::: moniker range="= azs-2002"
Das Feature für die Sammlung von Diagnoseprotokollen bietet zwei Optionen zum Senden von Protokollen. In den folgenden Abschnitten werden die einzelnen Optionen sowie die jeweilige Verarbeitung der Daten erläutert. 
::: moniker-end

::: moniker range=">= azs-2002"

## <a name="send-logs-proactively"></a>Proaktives Senden von Protokollen

Die [proaktive Protokollsammlung](./azure-stack-configure-automatic-diagnostic-log-collection.md?view=azs-2002) optimiert und vereinfacht die Sammlung von Diagnoseprotokollen, damit Kunden vor dem Erstellen einer Supportanfrage Protokolle an Microsoft senden können. Diagnoseprotokolle werden aus Azure Stack Hub proaktiv zur Analyse hochgeladen. Diese Protokolle werden nur gesammelt, wenn eine [Systemintegritätswarnung](./azure-stack-configure-automatic-diagnostic-log-collection.md?view=azs-2002#proactive-diagnostic-log-collection-alerts) ausgelöst wird, und der Microsoft-Support greift nur im Kontext einer Supportanfrage auf diese Protokolle zu.


### <a name="how-the-data-is-handled"></a>Datenverarbeitung

Sie erklären sich damit einverstanden, dass Microsoft in regelmäßigen Abständen und ausschließlich auf der Grundlage von Azure Stack Hub-Systemintegritätswarnungen automatisch Protokolle gesammelt. Darüber hinaus erklären Sie sich damit einverstanden, dass diese Protokolle in ein von Microsoft verwaltetes und gesteuertes Azure-Speicherkonto hochgeladen und dort aufbewahrt werden.

Die Daten werden ausschließlich für die Problembehandlung im Zusammenhang mit Systemintegritätswarnungen genutzt und ohne Ihre Einwilligung nicht zu Marketing- oder Werbezwecken oder für andere gewerbliche Zwecke verwendet. Die Daten können bis zu 90 Tage aufbewahrt werden, und alle von Microsoft gesammelten Daten werden im Einklang mit unseren [üblichen Datenschutzprinzipien](https://privacy.microsoft.com/) verarbeitet.

Der Widerruf Ihrer Einwilligung hat keine Auswirkungen auf Daten, die bereits mit Ihrer Zustimmung gesammelt wurden.

Mit der **proaktiven Protokollsammlung** gesammelte Protokolle werden in ein von Microsoft verwaltetes und gesteuertes Azure-Speicherkonto hochgeladen. Auf diese Protokolle kann von Microsoft im Kontext einer Supportanfrage sowie zur Verbesserung der Integrität von Azure Stack Hub zugegriffen werden.

## <a name="send-logs-now"></a>Sofortiges Senden von Protokollen

Das [sofortige Senden von Protokollen](./azure-stack-configure-on-demand-diagnostic-log-collection-portal.md?view=azs-2002) ist eine manuelle Option, bei der Diagnoseprotokolle nur dann aus Azure Stack Hub hochgeladen werden, wenn Sie (als Kunde) die Sammlung initiieren, was in der Regel vor der Erstellung einer Supportanfrage der Fall ist.

Azure Stack-Operatoren können Diagnoseprotokolle bei Bedarf über das Administratorportal oder mit PowerShell an den Microsoft-Support senden. Wenn Azure Stack Hub mit Azure verbunden ist, empfiehlt sich das [sofortige Senden von Protokollen über das Administratorportal](./azure-stack-configure-on-demand-diagnostic-log-collection-portal.md?view=azs-2002), da die Protokolle so am einfachsten direkt an Microsoft gesendet werden können. Ist das Portal nicht verfügbar, sollten Operatoren stattdessen [Protokolle sofort per PowerShell senden](./azure-stack-configure-on-demand-diagnostic-log-collection-powershell.md?view=azs-2002).

::: moniker-end
::: moniker range="= azs-2002"
Falls Sie über keine Internetverbindung verfügen oder die Protokolle nur lokal speichern möchten, verwenden Sie die Methode [Get-AzureStackLog](azure-stack-get-azurestacklog.md) für den Protokollversand. Das folgende Flussdiagramm zeigt, welche Option jeweils zum Senden von Diagnoseprotokollen verwendet werden muss: 
::: moniker-end

::: moniker range=">= azs-2002"

![Flussdiagramm zum sofortigen Senden von Protokollen an Microsoft](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

### <a name="how-the-data-is-handled"></a>Datenverarbeitung

Durch Initiieren der Sammlung von Diagnoseprotokollen über Azure Stack Hub erklären Sie sich damit einverstanden, dass diese Protokolle in ein von Microsoft verwaltetes und gesteuertes Azure-Speicherkonto hochgeladen und dort aufbewahrt werden. Der Microsoft-Support kann im Rahmen der Supportanfrage umgehend und ohne weitere Kundeninteraktion hinsichtlich der Protokollsammlung auf diese Protokolle zugreifen.

::: moniker-end

::: moniker range=">= azs-2005"

## <a name="save-logs-locally"></a>Lokales Speichern von Protokollen

Sie können Protokolle in einer lokalen SMB-Freigabe speichern, wenn Azure Stack Hub nicht mit Azure verbunden ist. Geben Sie auf dem Blatt **Einstellungen** den Pfad sowie einen Benutzernamen und ein Kennwort mit Schreibberechtigung für die Freigabe ein. Während eines Supportfalls werden von Microsoft CSS ausführliche Schritte für die Übertragung dieser lokalen Protokolle bereitgestellt.

![Screenshot der Optionen für die Sammlung von Diagnoseprotokollen](media/azure-stack-help-and-support/save-logs-locally.png)

::: moniker-end

::: moniker range=">= azs-2002"

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

Richten Sie für jede Azure Stack Hub-Skalierungseinheit, aus der Sie Protokolle sammeln möchten, einen Blobcontainer ein. Informationen zum Konfigurieren der Blobcontainer finden Sie unter [Konfigurieren der automatischen Azure Stack Hub-Diagnoseprotokollsammlung](./azure-stack-configure-automatic-diagnostic-log-collection.md?view=azs-2002). Es empfiehlt sich, nur Diagnoseprotokolle aus der gleichen Azure Stack Hub-Skalierungseinheit innerhalb eines einzelnen Blobcontainers zu speichern.

## <a name="retention-policy"></a>Aufbewahrungsrichtlinie

Erstellen Sie eine [Lebenszyklusverwaltungs-Regel](/azure/storage/blobs/storage-lifecycle-management-concepts) in Azure Blob Storage, um die Protokoll-Aufbewahrungsrichtlinie zu verwalten. Wir empfehlen, Diagnoseprotokolle 30 Tage lang aufzubewahren. Melden Sie sich zum Erstellen einer Lebenszyklusverwaltungs-Regel in Azure Storage beim Azure-Portal an, wählen Sie **Speicherkonten** aus, wählen Sie den Blobcontainer aus, und wählen Sie dann unter **Blob-Dienst** die Option **Lebenszyklusverwaltung** aus.

![Lebenszyklusverwaltung im Azure-Portal](media/azure-stack-automatic-log-collection/blob-storage-lifecycle-management.png)

## <a name="sas-token-expiration"></a>SAS-Tokenablauf

Legen Sie den Ablauf der SAS-URL auf zwei Jahre fest. Sollten Sie jemals Ihre Speicherkontoschlüssel erneuern, achten Sie darauf, die SAS-URL neu zu generieren. Sie sollten das SAS-Token gemäß den bewährten Methoden verwalten. Weitere Informationen finden Sie unter [Bewährte Methoden bei der Verwendung von SAS](/azure/storage/common/storage-dotnet-shared-access-signature-part-1#best-practices-when-using-sas).

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

Gebühren für Azure [Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/) richten sich nach der Menge der pro Monat gespeicherten Daten und nach weiteren Faktoren, etwa der Datenredundanz. Wenn Sie nicht über ein Speicherkonto verfügen, können Sie sich beim Azure-Portal anmelden, **Speicherkonten** auswählen und die Schritte zum [Erstellen einer SAS-URL für Azure-Blobcontainer](./azure-stack-configure-automatic-diagnostic-log-collection.md?view=azs-2002) ausführen.

Es wird empfohlen, eine [Lebenszyklusverwaltungs-Richtlinie](/azure/storage/blobs/storage-lifecycle-management-concepts) für Azure Blob Storage zu erstellen, um die laufenden Speicherkosten zu minimieren. Informationen zum Einrichten des Speicherkontos finden Sie unter [Konfigurieren der automatischen Azure Stack Hub-Diagnoseprotokollsammlung](./azure-stack-configure-automatic-diagnostic-log-collection.md?view=azs-2002).

::: moniker-end

## <a name="see-also"></a>Weitere Informationen

[Verarbeiten von Azure Stack Hub-Protokoll- und -Kundendaten](./azure-stack-data-collection.md)
