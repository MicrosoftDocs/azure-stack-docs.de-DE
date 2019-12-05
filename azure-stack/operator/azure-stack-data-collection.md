---
title: Verarbeiten von Azure Stack-Protokoll- und -Kundendaten | Microsoft-Dokumentation
description: Es wird beschrieben, wie von Azure Stack Kundendaten und Informationen gesammelt werden.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: patricka
ms.reviewer: chengwei
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: ff633133b7d0fd0489b3e81295ea53351968ac8f
ms.sourcegitcommit: 7817d61fa34ac4f6410ce6f8ac11d292e1ad807c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74690196"
---
# <a name="azure-stack-log-and-customer-data-handling"></a>Azure Stack-Protokoll und Behandlung von Kundendaten 
*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*  

Im Rahmen der Funktion von Microsoft als Verarbeiter oder Vorverarbeiter von personenbezogenen Daten in Verbindung mit Azure Stack verpflichtet sich Microsoft ab dem 25. Mai 2018 gegenüber Kunden zu Folgendem:

- Zusagen zur „Verarbeitung personenbezogener Daten (DSGVO)“ im Abschnitt zu den „Datenschutzbestimmungen“ der [Lizenzbedingungen für Onlinedienste](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31).
- Allgemeine Regulierungsbestimmungen der Europäischen Union zum Datenschutz in Anhang 4 der [Lizenzbedingungen für Onlinedienste](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31).

Für Azure Stack im Rechenzentrum des Kunden ist Microsoft ausschließlich Data Controller der Daten, die gemeinsam mit Microsoft über [Diagnose](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs), [Telemetrie](azure-stack-telemetry.md), und [Abrechnung](azure-stack-usage-reporting.md) genutzt werden.  

## <a name="data-access-controls"></a>Datenzugriffssteuerung 
Microsoft-Mitarbeiter, die mit der Untersuchung eines bestimmten Supportfalls beauftragt sind, erhalten Lesezugriff auf die verschlüsselten Daten. Microsoft-Mitarbeiter haben bei Bedarf ebenfalls Zugriff auf Tools zum Löschen der Daten. Jeder Zugriff auf Kundendaten wird überwacht und protokolliert.  

Datenzugriffssteuerung:
- Daten werden nach dem Schließen des Falls nur maximal 90 Tage lang gespeichert.
- Der Kunde hat immer die Möglichkeit, die Daten zu einem beliebigen Zeitpunkt in diesem 90-Tage-Zeitraum entfernen zu lassen.
- Microsoft-Mitarbeiter erhalten von Fall zu Fall Zugriff auf die Daten und insoweit, wie zum Lösen des Supportproblems erforderlich.
- Wenn Microsoft Kundendaten gemeinsam mit OEM-Partnern nutzen muss, ist die Zustimmung des Kunden erforderlich.  

### <a name="what-data-subject-requests-dsr-controls-do-customers-have"></a>Welche Data Subject Requests-Kontrollen (DSR) stehen Kunden zur Verfügung?
Microsoft unterstützt bei Bedarf die Datenlöschung pro Kundenanforderung. Kunden können anfordern, dass einer unserer Supportmitarbeiter ihre gesamten Protokolle für einen bestimmten Fall zu einem beliebigen Zeitpunkt löscht, bevor die Daten dauerhaft gelöscht werden.  

### <a name="does-microsoft-notify-customers-when-the-data-is-deleted"></a>Benachrichtigt Microsoft Kunden, wenn die Daten gelöscht werden?
Für die automatisierte Datenlöschaktion (90 Tage nach dem Schließen des Falls) wenden wir uns nicht proaktiv an Kunden und unterrichten sie nicht über die Löschung.

Für die bedarfsgesteuerte Datenlöschaktion haben Microsoft-Supportmitarbeiter Zugriff auf das Tool, mit dem sie Daten auf Anforderung löschen können. Sie können dem Kunden telefonisch bestätigen, dass dieser Vorgang abgeschlossen wurde.

## <a name="diagnostic-data"></a>Diagnosedaten
Im Rahmen des Supportprozesses können Azure Stack-Betreiber für den Azure Stack-Support und Engineering-Teams [Diagnoseprotokolle freigeben](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs), um die Problembehandlung zu unterstützen.

Microsoft stellt ein Tool und Skript für Kunden bereit, um angeforderte Diagnoseprotokolldateien zu sammeln und hochzuladen. Nach der Erfassung werden die Protokolldateien über eine HTTPS-geschützte, verschlüsselte Verbindung an Microsoft übertragen. Da die Verschlüsselung über das Netzwerk per HTTPS bereitgestellt wird, ist während der Übertragung kein Kennwort für die Verschlüsselung erforderlich. Nach dem Empfang werden die Protokolle verschlüsselt und gespeichert, bis sie automatisch 90 Tage nach dem Schließen des Supportfalls gelöscht werden.

## <a name="telemetry-data"></a>Telemetriedaten
Die [Telemetrie von Azure Stack](azure-stack-telemetry.md) lädt über „Benutzererfahrung und Telemetrie im verbundenen Modus“ automatisch Systemdaten an Microsoft hoch. Azure Stack-Operator können Telemetriefeatures und Datenschutzeinstellungen jederzeit anpassen.

Die Erfassung von vertraulichen Daten, z. B. Kreditkartennummern, Benutzernamen und Kennwörtern oder E-Mail-Adressen usw., ist von Microsoft nicht beabsichtigt. Sollten wir feststellen, dass versehentlich vertrauliche Informationen empfangen wurden, werden diese gelöscht.

## <a name="billing-data"></a>Abrechnungsdaten
[Azure Stack Billing](azure-stack-usage-reporting.md) nutzt die globale Abrechnungs- und Nutzungspipeline von Azure und ist daher im Einklang mit den Microsoft-Konformitätsrichtlinien.

Azure Stack-Operator können Azure Stack für die Weiterleitung abrechnungsrelevanter Nutzungsinformationen an Azure konfigurieren. Diese Konfiguration ist für Kunden mit integrierten Azure Stack-Systemen erforderlich, die das Abrechnungsmodell mit nutzungsbasierter Bezahlung wählen. Verwendungsberichte werden unabhängig von Telemetriedaten gesteuert und sind für Kunden mit integrierten Systemen, die sich für das Kapazitätsmodell entscheiden, sowie für Azure Stack Development Kit-Benutzer nicht erforderlich. Für diese Szenarien können Verwendungsberichte [mithilfe des Registrierungsskripts](azure-stack-usage-reporting.md) deaktiviert werden.


## <a name="next-steps"></a>Nächste Schritte 
[Informieren Sie sich ausführlicher über die Sicherheit von Azure Stack.](azure-stack-security-foundations.md) 
