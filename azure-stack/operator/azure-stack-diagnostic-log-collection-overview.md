---
title: Übersicht über die Sammlung von Azure Stack Hub-Diagnoseprotokollen
description: Erläutert die Sammlung von Diagnoseprotokollen in Azure Stack Hub Hilfe und Support, darunter die Sammlung bei Bedarf und die automatische Protokollsammlung.
author: justinha
ms.topic: article
ms.date: 11/07/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 11/07/2019
ms.openlocfilehash: 05d54d3db2429faa410cc67a46fba234d285a1af
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77700050"
---
# <a name="overview-of-azure-stack-hub-diagnostic-log-collection"></a>Übersicht über die Sammlung von Azure Stack Hub-Diagnoseprotokollen 

Bei Azure Stack Hub handelt es sich um eine große Sammlung von Komponenten, die zusammenarbeiten und miteinander interagieren. Alle diese Komponenten generieren eigene eindeutige Protokolle. Dies kann die Diagnose von Problemen zu einer Herausforderung machen – insbesondere bei Fehlern, die von verschiedenen interagierenden Azure Stack Hub-Komponenten stammen. Um dieser Herausforderung Rechnung zu tragen, haben wir einen Prozess zur Sammlung von Diagnoseprotokollen entworfen. 

Vor Version 1907 beinhaltete der Diagnoseprozess die Verwendung von [Test-AzureStack](azure-stack-diagnostic-test.md) zum Überprüfen der Systemintegrität und die Nutzung des [privilegierten Endpunkts (PEP)](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs) zum Sammeln von Protokollen zur Problembehandlung. 

Seit Version 1907 steht auf der Seite **Hilfe und Support** ein einfacherer Prozess zur Verfügung, der die **Sammlung von Diagnoseprotokollen** beinhaltet. 
Die **Sammlung von Diagnoseprotokollen** ist Teil eines fortgesetzten Engagements zum Verbessern der Abläufe im Fehlerbehandlungsprozess für den Azure Stack Hub-Operator. Dank dieser Verbesserungen können Operatoren schnell Diagnoseprotokolle sammeln und mit den Microsoft Customer Support Services (CSS) teilen. Die Protokolle können in einem Blobcontainer in Azure gespeichert werden, wo der Zugriff nach Bedarf angepasst werden kann.    
   
Die **Sammlung von Diagnoseprotokollen** funktioniert auf zwei verschiedene Arten:

- **Automatische Sammlung**: Wenn sie aktiviert ist (empfohlen), wird die Sammlung von Protokollen automatisch durch bestimmte Integritätswarnungen ausgelöst und in Ihrem Azure-Speicherkonto gespeichert
- **Protokolle jetzt sammeln**: Dies ist eine bedarfsgesteuerte Option, bei der Sie sich für die Erfassung von Protokollen in einem 1–4 Stunden großen gleitenden Fenster in den letzten sieben Tagen entscheiden können

![Screenshot der Optionen für die Sammlung von Diagnoseprotokollen](media/azure-stack-automatic-log-collection/azure-stack-log-collection-overview.png)

Die **Diagnoseprotokollsammlung** weist eine einfache Benutzeroberfläche auf und erfordert kein PowerShell. Protokolle werden zuverlässig erfasst, auch wenn die Infrastrukturdienste außer Betrieb sind.
Wenn Ihre Richtlinie das Teilen von Diagnoseprotokollen mit CSS zulässt, ist die **Sammlung von Diagnoseprotokollen** ab der Version 1907 die empfohlene Sammlungsmethode. Sie sollten [den PEP](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs) zur Erfassung von Protokollen nur verwenden, wenn die **Sammlung von Diagnoseprotokollen** in Hilfe und Support nicht verfügbar ist.

## <a name="automatic-diagnostic-log-collection"></a>Automatische Sammlung von Diagnoseprotokollen 

Wenn eine [bestimmte Integritätswarnung](azure-stack-configure-automatic-diagnostic-log-collection.md#automatic-diagnostic-log-collection-alerts) aktiv ist, startet die automatische Sammlung von Diagnoseprotokollen und lädt proaktiv Diagnoseprotokolle aus Azure Stack Hub in ein Speicherblob hoch. Dadurch verkürzt sich die für das Freigeben von Diagnoseprotokollen mit CSS erforderliche Zeit erheblich. Diagnoseprotokolle werden nur gesammelt, wenn eine Warnung ausgelöst wird.  

Weitere Informationen zur automatischen Protokollsammlung finden Sie unter [Konfigurieren der automatischen Azure Stack Hub-Diagnoseprotokollsammlung](azure-stack-configure-automatic-diagnostic-log-collection.md).

## <a name="on-demand-diagnostic-log-collection"></a>Sammlung von Diagnoseprotokollen bei Bedarf

Bei der bedarfsgesteuerten Sammlung werden Diagnoseprotokolle von Azure Stack Hub auf ein Speicherblob in Azure hochgeladen, wenn ein Azure Stack Hub-Operator die Sammlung manuell auslöst.
CSS stellt eine SAS-URL (Shared Access Signature) für ein Speicherblob im Besitz von CSS zur Verfügung. Ein Azure Stack Hub-Operator kann auf **Protokolle jetzt sammeln** klicken und die SAS-URL eingeben. Diagnoseprotokolle werden dann direkt auf das CSS-Blob hochgeladen, ohne dass eine dazwischen liegende Freigabe benötigt wird. 

Weitere Informationen zum Erfassen von Protokollen bei Bedarf finden Sie unter [Azure Stack Hub-Diagnoseprotokolle jetzt sammeln](azure-stack-configure-on-demand-diagnostic-log-collection.md).

## <a name="bandwidth-considerations"></a>Bandbreitenaspekte

Die durchschnittliche Größe der Sammlung von Diagnoseprotokollen schwankt, je nachdem, ob sie bedarfsgesteuert oder automatisch ausgeführt wird. Die durchschnittliche Größe für die automatische Protokollsammlung beträgt etwa 2 GB, während die Größe einer bedarfsgesteuerten Sammlung davon abhängt, wie viele Stunden gesammelt werden. 

In der folgenden Tabelle sind Überlegungen zu Umgebungen mit eingeschränkten oder getakteten Azure-Verbindungen aufgelistet.

| Netzwerkverbindung | Auswirkung |
|--------------------|--------|
| Verbindung mit geringer Bandbreite/hoher Latenz | Der vollständige Upload des Protokolls dauert längere Zeit | 
| Gemeinsam genutzte Verbindung | Der Upload kann sich auch auf andere Anwendungen/Benutzer auswirken, die die gleiche Netzwerkverbindung nutzen |
| Getaktete Verbindung | Möglicherweise fallen zusätzliche Nutzungsgebühren seitens Ihres ISPs für die Mehrnutzung des Netzwerks an |

Weitere Informationen finden Sie unter [Bewährte Methoden für die automatische Azure Stack Hub-Protokollsammlung](azure-stack-best-practices-automatic-diagnostic-log-collection.md).

## <a name="see-also"></a>Weitere Informationen

[Verarbeiten von Azure Stack Hub-Protokoll- und -Kundendaten](https://docs.microsoft.com/azure-stack/operator/azure-stack-data-collection)

[Verwenden von Shared Access Signatures (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)

[Bewährte Methoden für die automatische Azure Stack Hub-Protokollsammlung](azure-stack-best-practices-automatic-diagnostic-log-collection.md)
