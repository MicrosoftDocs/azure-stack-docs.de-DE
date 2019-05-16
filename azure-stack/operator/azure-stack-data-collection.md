---
title: Azure Stack-Protokoll und die Behandlung von Daten | Microsoft-Dokumentation
description: Erfahren Sie, wie Azure Stack Informationen erfasst.
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
ms.date: 02/14/2019
ms.author: PatAltimore
ms.reviewer: chengwei
ms.lastreviewed: 02/14/2019
ms.openlocfilehash: e0b62c9f9519e1b77211327c4f9b48d4b53cf99a
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985869"
---
# <a name="azure-stack-log-and-customer-data-handling"></a>Azure Stack-Protokoll und Behandlung von Kundendaten 
*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*  

In dem Umfang, in dem Microsoft ein Prozessor oder Subprozessor personenbezogener Daten im Zusammenhang mit Azure Stack ist, geht Microsoft für alle Kunden, in Kraft tretend am 25. Mai 2018, die in (a) „Processing of Personal Data; GDPR (Verarbeitung von persönlichen Daten; DSGVO“ im Abschnitt „Data Protection Terms (Datenschutzbestimmungen))“ der [Online Services Terms (Onlinenutzungsbedingungen)](https://nam06.safelinks.protection.outlook.com/?url=http%3A%2F%2Fwww.microsoftvolumelicensing.com%2FDocumentSearch.aspx%3FMode%3D3%26DocumentTypeId%3D31&data=02%7C01%7Ccomartin%40microsoft.com%7Ce2ce478261764c79c3f308d68df01136%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636852459551078818&sdata=cpWsfZTBHpqEFr50DWQOryq342U8shgeFgMXVPQz5ug%3D&reserved=0) und (b) der „European Union General Data Protection Regulation Terms (Allgemeine Datenschutzbestimmungen der Europäischen Union)“ in Anhang 4 der [Online Services Terms (Onlinenutzungsbedingungen)](https://nam06.safelinks.protection.outlook.com/?url=http%3A%2F%2Fwww.microsoftvolumelicensing.com%2FDocumentSearch.aspx%3FMode%3D3%26DocumentTypeId%3D31&data=02%7C01%7Ccomartin%40microsoft.com%7Ce2ce478261764c79c3f308d68df01136%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636852459551088813&sdata=bv1CBiaCnYmjiv6S0dFCbWEd4fNCkPBjBwgylNa%2FNt0%3D&reserved=0) enthaltenen Verpflichtungen ein. 

Für Azure Stack im Rechenzentrum des Kunden ist Microsoft ausschließlich Data Controller der Daten, die gemeinsam mit Microsoft über [Diagnose](azure-stack-diagnostics.md), [Telemetrie](azure-stack-telemetry.md), und [Abrechnung](azure-stack-usage-reporting.md) genutzt werden.  

## <a name="data-access-controls"></a>Datenzugriffssteuerung 
Microsoft-Mitarbeiter, die mit der Untersuchung eines bestimmten Supportfalls beauftragt sind, erhalten Lesezugriff auf die verschlüsselten Daten. Microsoft-Mitarbeiter haben auch Zugriff auf Tools, die verwendet werden können, um Daten ggf. zu löschen. Jeder Zugriff auf Kundendaten wird überwacht und protokolliert.  

Datenzugriffssteuerung:
1.  Daten werden nur für bis zu 90 Tage nach dem Schließen des Falls gespeichert.
2.  Der Kunde hat immer die Möglichkeit, die Daten zu einem beliebigen Zeitpunkt in diesem 90-Tage-Zeitraum entfernen zu lassen.
3.  Microsoft-Mitarbeiter erhalten von Fall zu Fall Zugriff auf die Daten und insoweit, wie zum Lösen des Supportproblems erforderlich. 
4.  Wenn Microsoft Kundendaten gemeinsam mit OEM-Partnern nutzen muss, ist die Zustimmung des Kunden erforderlich.  

### <a name="what-data-subject-requests-dsr-controls-do-customers-have"></a>Welche Data Subject Requests-Kontrollen (DSR) stehen Kunden zur Verfügung?
Wie bereits erwähnt, unterstützt Microsoft bei Bedarf Datenlöschung pro Kundenanforderung. Kunden können anfordern, dass unsere Supportmitarbeiter alle ihre Protokolle für einen bestimmten Fall zu einem beliebigen, vom Kunden ausgewählten Zeitpunkt löschen, bevor die Daten dauerhaft gelöscht werden.  

### <a name="does-microsoft-notify-customers-when-the-data-is-deleted"></a>Benachrichtigt Microsoft Kunden, wenn die Daten gelöscht werden?
Für die automatisierte Datenlöschaktion (90 Tage nach dem Schließen des Falls) wenden wir uns nicht proaktiv an Kunden und unterrichten sie nicht über die Löschung. 

Für die bedarfsgesteuerte Datenlöschaktion haben Microsoft-Supportmitarbeiter Zugriff auf das Tool, wo sie das Löschen der Daten bei Bedarf initiieren können, und sie können Kunden dies nach Abschluss telefonisch bestätigen.

## <a name="diagnostic-data"></a>Diagnosedaten
Im Rahmen des Supportprozess können Azure Stack-Operator für Azure Stack-Support und Engineering-Teams [Diagnoseprotokolle freigeben](azure-stack-diagnostics.md), um die Problembehandlung zu vereinfachen.

Microsoft stellt ein Tool und Skript für Kunden bereit, um angeforderte Diagnoseprotokolldateien zu sammeln und hochzuladen. Nach der Erfassung werden die Protokolldateien über eine HTTPS-geschützte, verschlüsselte Verbindung an Microsoft übertragen. Da HTTPS die Verschlüsselung über das Netzwerk bereitstellt, ist während der Übertragung kein Kennwort für die Verschlüsselung erforderlich. Nach Empfangen werden die Protokolle verschlüsselt und gespeichert, bis sie automatisch 90 Tage nach Schließen des Supportfalls gelöscht werden.

## <a name="telemetry-data"></a>Telemetriedaten
Die [Telemetrie von Azure Stack](azure-stack-telemetry.md) lädt über „Benutzererfahrung und Telemetrie im verbundenen Modus“ automatisch Systemdaten an Microsoft hoch. Azure Stack-Operator können Telemetriefeatures und Datenschutzeinstellungen jederzeit anpassen.

Die Erfassung vertraulicher Informationen wie Kreditkartennummern, Benutzernamen und Kennwörtern, E-Mail-Adressen und Ähnlichem ist von Microsoft nicht beabsichtigt. Sollten wir feststellen, dass versehentlich vertrauliche Informationen empfangen wurden, werden diese gelöscht. 

## <a name="billing-data"></a>Abrechnungsdaten
[Azure Stack Billing](azure-stack-usage-reporting.md) nutzt die globale Abrechnungs- und Nutzungspipeline von Azure und ist daher im Einklang mit den Microsoft-Konformitätsrichtlinien.

Azure Stack-Operator können Azure Stack für die Weiterleitung abrechnungsrelevanter Nutzungsinformationen an Azure konfigurieren. Dies ist für Azure Stack-Kunden mit mehreren Knoten erforderlich, die sich für ein nutzungsbasiertes Abrechnungsmodell entscheiden. Verwendungsberichte werden unabhängig von Telemetriedaten gesteuert und sind für Kunden mit mehreren Knoten, die sich für das Kapazitätsmodell entscheiden, sowie für Azure Stack Development Kit-Benutzer nicht erforderlich. Für diese Szenarien können Verwendungsberichte [mithilfe des Registrierungsskripts](azure-stack-usage-reporting.md) deaktiviert werden.


## <a name="next-steps"></a>Nächste Schritte 
[Informieren Sie sich ausführlicher über die Sicherheit von Azure Stack.](azure-stack-security-foundations.md) 
