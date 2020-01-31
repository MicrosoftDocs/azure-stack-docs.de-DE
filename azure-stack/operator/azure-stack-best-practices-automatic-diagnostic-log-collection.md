---
title: Bewährte Methoden für die automatische Azure Stack Hub-Protokollsammlung
description: Bewährte Methoden für die automatische Protokollsammlung in Azure Stack Hub – Hilfe und Support
author: justinha
ms.topic: article
ms.date: 07/25/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 07/25/2019
ms.openlocfilehash: 56af62717b4cd32d7a5130d1d324bd40bf61adb9
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76878427"
---
# <a name="best-practices-for-automatic-azure-stack-hub-log-collection"></a>Bewährte Methoden für die automatische Azure Stack Hub-Protokollsammlung 

In diesem Thema werden bewährte Methoden zum Verwalten der automatischen Sammlung von Diagnoseprotokollen für Azure Stack Hub behandelt. 

## <a name="collecting-logs-from-multiple-azure-stack-hub-systems"></a>Sammeln von Protokollen aus mehreren Azure Stack Hub-Systemen

Richten Sie für jede Azure Stack Hub-Skalierungseinheit, aus der Sie Protokolle sammeln möchten, einen Blobcontainer ein. Informationen zum Konfigurieren der Blobcontainer finden Sie unter [Konfigurieren der automatischen Azure Stack Hub-Diagnoseprotokollsammlung](azure-stack-configure-automatic-diagnostic-log-collection.md). Es empfiehlt sich, nur Diagnoseprotokolle aus der gleichen Azure Stack Hub-Skalierungseinheit innerhalb eines einzelnen Blobcontainers zu speichern. 

## <a name="retention-policy"></a>Aufbewahrungsrichtlinie

Erstellen Sie eine [Lebenszyklusverwaltungs-Regel](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts) in Azure Blob Storage, um die Protokoll-Aufbewahrungsrichtlinie zu verwalten. Wir empfehlen, Diagnoseprotokolle 30 Tage lang aufzubewahren. Melden Sie sich zum Erstellen einer Lebenszyklusverwaltungs-Regel in Azure Storage beim Azure-Portal an, klicken Sie auf **Speicherkonten**, klicken Sie auf den Blobcontainer, und klicken Sie dann unter **Blob-Dienst** auf **Lebenszyklusverwaltung**.

![Screenshot, der die Lebenszyklusverwaltung im Azure-Portal darstellt](media/azure-stack-automatic-log-collection/blob-storage-lifecycle-management.png)


## <a name="sas-token-expiration"></a>SAS-Tokenablauf

Legen Sie den Ablauf der SAS-URL auf zwei Jahre fest. Sollten Sie jemals Ihre Speicherkontoschlüssel erneuern, achten Sie darauf, die SAS-URL neu zu generieren. Sie sollten das SAS-Token gemäß den bewährten Methoden verwalten. Weitere Informationen finden Sie unter [Bewährte Methoden bei der Verwendung von SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#best-practices-when-using-sas).


## <a name="bandwidth-consumption"></a>Bandbreitenverbrauch

Die durchschnittliche Größe der Sammlung von Diagnoseprotokollen schwankt, je nachdem, ob die Protokollsammlung bedarfsgesteuert oder automatisch ausgeführt wird. 

Bei der bedarfsgesteuerten Protokollsammlung hängt die Größe der Protokollsammlung davon ab, wie viele Stunden erfasst werden. Sie können ein beliebiges gleitendes Fenster von 1–4 Stunden in den letzten sieben Tagen auswählen. 

Wenn die automatische Sammlung von Diagnoseprotokollen aktiviert ist, überwacht der Dienst kritische Warnungen. Wenn eine kritische Warnung ausgelöst und etwa 30 Minuten lang aufrecht erhalten wird, sammelt der Dienst entsprechende Protokolle und lädt sie hoch. Die Größe dieser Protokollsammlung beträgt durchschnittlich ca. 2 GB. Im Fall eines Fehlers bei einem Patch oder Update beginnt die automatische Protokollsammlung nur, wenn eine kritische Warnung ausgelöst wird und ca. 30 Minuten lang besteht. Es empfiehlt sich, die [Anleitung zum Überwachen von Patches und Updates](azure-stack-updates.md) zu befolgen.
Warnungsüberwachung, Protokollsammlung und Upload erfolgen für den Benutzer transparent. 



In einem fehlerfreien System werden überhaupt keine Protokolle gesammelt. Bei einem fehlerhaften System wird die Protokollsammlung möglicherweise zweimal oder dreimal im Lauf eines Tages ausgeführt, in der Regel jedoch nur einmal. Maximal könnte sie in einem besonders ungünstigsten Szenario bis zu zehnmal am Tag ausgeführt werden.  

Die folgende Tabelle kann dabei helfen, den Einfluss der aktivierten automatischen Protokollsammlung in Umgebungen mit eingeschränkten oder getakteten Verbindungen einzuschätzen.

| Netzwerkverbindung | Auswirkung |
|--------------------|--------|
| Verbindung mit geringer Bandbreite/hoher Latenz | Der vollständige Upload des Protokolls dauert längere Zeit | 
| Gemeinsam genutzte Verbindung | Der Upload kann sich auch auf andere Anwendungen/Benutzer auswirken, die die gleiche Netzwerkverbindung nutzen |
| Getaktete Verbindung | Möglicherweise fallen zusätzliche Nutzungsgebühren seitens Ihres ISPs für die Mehrnutzung des Netzwerks an |


## <a name="managing-costs"></a>Kostenverwaltung

Gebühren für Azure [Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/) richten sich nach der Menge der pro Monat gespeicherten Daten und nach weiteren Faktoren, wie der Datenredundanz. Wenn Sie nicht über ein Speicherkonto verfügen, können Sie sich beim Azure-Portal anmelden, auf **Speicherkonten** klicken und die Schritte zum [Erstellen einer SAS-URL für Azure-Blobcontainer](azure-stack-configure-automatic-diagnostic-log-collection.md) ausführen.

Es wird empfohlen, eine [Lebenszyklusverwaltungs-Richtlinie](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts) für Azure Blob Storage zu erstellen, um die laufenden Speicherkosten zu minimieren. Informationen zum Einrichten des Speicherkontos finden Sie unter [Konfigurieren der automatischen Azure Stack Hub-Diagnoseprotokollsammlung](azure-stack-configure-automatic-diagnostic-log-collection.md).

## <a name="see-also"></a>Weitere Informationen

[Konfigurieren der automatischen Azure Stack Hub-Protokollsammlung](azure-stack-best-practices-automatic-diagnostic-log-collection.md)

