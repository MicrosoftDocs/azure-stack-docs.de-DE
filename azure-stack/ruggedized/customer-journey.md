---
title: User Journey vom Einkauf bis nach der Bereitstellung von Azure Stack Hub | Microsoft-Dokumentation
description: Hier erfahren Sie, was Sie bei einer erfolgreichen lokalen Bereitstellung von Azure Stack Hub Ruggedized von Microsoft erwarten können – von der Planung bis zu den Aktionen nach der Bereitstellung.
services: azure-stack
documentationcenter: ''
author: ashika789
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2020
ms.author: justinha
ms.reviewer: asganesh
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: f34a449490ab7b57beacc942584f9616fd6d543c
ms.sourcegitcommit: 5fbc60b65d27c916ded7a95ba4102328d550c7e5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97598316"
---
# <a name="customer-journey"></a>Customer Journey

Dieser Artikel beschreibt den End-to-End-Prozess für die Azure Stack Hub-Rechenzentrumsintegration – von der Anschaffung bis zu den Schritten nach der Bereitstellung. Die Integration ist ein gemeinschaftliches Projekt zwischen dem Kunden und Microsoft. In den folgenden Abschnitten werden die einzelnen Phasen der Projektzeitachse und die genauen Schritte der Projektbeteiligten behandelt.

## <a name="introduction"></a>Einführung

In der folgenden Tabelle ist dargestellt, was während der verschiedenen Phasen der Bereitstellung zu erwarten ist.

|   |Bestellvorgang  |Vor der Bereitstellung |Integration, Validierung, Transport |Lokale Bereitstellung  |Nach der Bereitstellung |
|---|---------------|---------------|-----------------------------------|--------------------|----------------|
| **Microsoft** |- Signal für die Lieferung an einen Standort in den USA<br>– Azure Stack Hub Ruggedized  = 10 Tage |Bereitstellen der erforderlichen Tools und der Dokumentation zum Sammeln der Anforderungen an das Rechenzentrum  |- Validierung der Konfigurationsartefakte und Überprüfung der Validierungsergebnisse<br>- Überprüfung der Hardwarelieferung  |- Rack und Stack<br>- Netzwerkintegration<br>- Azure Stack Hub-Bereitstellung<br>- Übergabe an den Kunden    |Registrierung und Marketplace-Syndikation|
| **Kunde** |Meldet den Kauf    |- Füllt Netzwerkdetails im Bereitstellungsarbeitsblatt aus<br>- Sammelt Zertifikate<br>- Registriert Azure AD-Konten<br>- Führt alle bereitgestellten Validierungstools aus    |Sicherstellen, dass der Standort alle Anforderungen an Netzwerk, Stromversorgung und Kühlkomponenten erfüllt    |- Vorbereitung mit Konfigurationsartefakten für die Bereitstellung<br>- Verfügbarer Netzwerktechniker vom Kunden   |     |


## <a name="order-process"></a>Bestellvorgang

Ihre Organisation gibt in Zusammenarbeit mit Microsoft eine Bestellung für eine festgelegte Anzahl von Systemen auf. Nachdem Sie die Bestellung aufgegeben haben, hat Microsoft 10 Tage Zeit, das Azure Stack Hub Ruggedized-Gerät an Ihren Standort in den USA zu liefern. Microsoft sorgt dafür, dass alle Anforderungen an eine sichere Lieferkette erfüllt sind. 

>[!NOTE] 
>Die Abrechnung beginnt 14 Tage nach dem Versand der Hardware.


Um eine Azure Stack Hub-Ressource zu erstellen, führen Sie im Azure-Portal die folgenden Schritte aus.

1. Melden Sie sich mit Ihren Microsoft Azure-Anmeldeinformationen beim Azure-Portal unter der folgenden URL an: [https://portal.azure.com](https://portal.azure.com).
1. Wählen Sie im linken Bereich **+ Ressource erstellen** aus. Suchen Sie nach **Azure Stack Hub Ruggedized**, und wählen Sie den Eintrag aus. Klicken Sie auf **Erstellen**.
1. Wählen Sie das Abonnement aus, das Sie für das Azure Stack Hub-Gerät verwenden möchten. Wählen Sie das Land/die Region aus, in das/die das physische Gerät versendet werden soll. Wählen Sie **Geräte anzeigen** aus.
1. Ein Kurzformular wird angezeigt. Füllen Sie das Formular aus, und wählen Sie **Senden** aus. Microsoft aktiviert Ihr Abonnement.
1. Nachdem das Abonnement aktiviert wurde, sollten Sie in der Lage sein, die Ressourcenerstellung fortzusetzen. Wählen Sie auf dem Blatt **Gerätetyp auswählen** die Option **Auswählen** aus. 
1. Geben Sie auf der Registerkarte **Grundlagen** die folgenden **Projektdetails** ein, bzw. wählen Sie sie aus:
    
    |Einstellung  |Wert  |
    |---------|---------|
    |Subscription    |Dieser Wert wird auf der Grundlage Ihrer zuvor getroffenen Auswahl automatisch aufgefüllt. Das Abonnement ist mit Ihrem Abrechnungskonto verknüpft. |
    |Resource group  |Wählen Sie eine vorhandene Gruppe aus, oder erstellen Sie eine neue Gruppe.   |

1. Geben Sie die folgenden **Instanzendetails** ein, bzw. wählen Sie sie aus:

    |Einstellung  |Wert  |
    |---------|---------|
    |Name   | Ein Anzeigename, der die Ressource identifiziert.<br>Der Name muss 2 bis 50 Zeichen lang sein und darf nur Buchstaben, Zahlen und Bindestriche enthalten.<br> Der Name muss mit einem Buchstaben oder einer Zahl beginnen und enden.        |
    |Region     |Eine Liste aller Regionen, in denen die Azure Stack Hub-Ressource verfügbar ist, finden Sie unter [Verfügbare Azure-Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Die für Azure Government verfügbaren Regionen finden Sie bei Bedarf unter [Azure-Regionen](https://azure.microsoft.com/global-infrastructure/regions/).<br> Wählen Sie den Standort aus, der der geografischen Region, in der Ihr Gerät bereitgestellt werden soll, am nächsten ist.|


1. Klicken Sie auf **Weiter: Lieferanschrift**.

    - Falls Sie bereits über ein Gerät verfügen, aktivieren Sie das Kombinationsfeld **I have a Azure Stack Hub device** (Ich habe ein Azure Stack Hub-Gerät).

    - Handelt es sich um das neue Gerät, das Sie bestellen möchten, geben Sie Kontaktname, Unternehmen, Lieferadresse für das Gerät sowie Kontaktinformationen ein.

1. Klicken Sie auf **Weiter: Überprüfen + erstellen**.
1. Überprüfen Sie auf der Registerkarte **Überprüfen + erstellen** die **Preisdetails**, die **Nutzungsbedingungen** und die Details für Ihre Ressource. Aktivieren Sie das Kontrollkästchen **Ich habe die angegebenen Informationen gelesen und stimme den Datenschutzbestimmungen zu.** .
1. Klicken Sie auf **Erstellen**.

Die Erstellung der Ressource dauert einige Minuten. Nachdem die Ressource erfolgreich erstellt und bereitgestellt wurde, erhalten Sie eine Benachrichtigung. Wählen Sie **Zu Ressource wechseln** aus.

Nach der Bestellung wird diese von Microsoft geprüft, und Sie erhalten eine E-Mail mit den Versanddetails.

## <a name="pre-deployment"></a>Vor der Bereitstellung

Sie entscheiden, wie Sie Azure Stack Hub in Ihr Rechenzentrum integrieren. Microsoft hat ein [Bereitstellungsarbeitsblatt](../operator/azure-stack-deployment-worksheet.md) veröffentlicht, das Sie durch die Erfassung aller für die erfolgreiche Integration in Ihr Rechenzentrum erforderlichen Informationen führt. Außerdem sind zum Zeitpunkt der Bereitstellung bestimmte Zertifikate erforderlich. Um Sie bei der Beschaffung dieser Zertifikate zu unterstützen, stellt Microsoft das Tool [Azure Stack Hub Readiness Checker](../operator/azure-stack-validation-report.md) zur Verfügung. Dieses Tool hilft Ihnen beim Erstellen von Zertifikatsignieranforderungen (Certificate Signing Requests, CSRs), die Sie für Ihre interne Zertifizierungsstelle bereitstellen können. 

>[!Important]
>Alle Voraussetzungen werden überprüft, um Verzögerungen bei der Bereitstellung zu vermeiden. Die Überprüfung der Voraussetzungen kann einige Zeit in Anspruch nehmen und die Koordination und Datensammlung aus verschiedenen Abteilungen innerhalb Ihres Unternehmens erfordern.

Sie wählen Folgendes aus:

- **Azure Stack Hub-Verbindungsmodell und Identitätsanbieter.** Sie können Azure Stack Hub entweder [mit einer Verbindung mit dem Internet (und mit Azure)](../operator/azure-stack-connected-deployment.md) oder [unverbunden](../operator/azure-stack-disconnected-deployment.md) bereitstellen. Um optimal von Azure Stack Hub (Hybridszenarien) zu profitieren, empfiehlt es sich, die Bereitstellung mit einer Verbindung mit Azure durchzuführen. Die Wahl von Active Directory-Verbunddienste (AD FS) oder Azure Active Directory (Azure AD) ist eine Entscheidung, die Sie bei der Bereitstellung einmalig treffen müssen. **Sie können Ihren Identitätsanbieter später nicht mehr ändern, ohne das gesamte System neu bereitzustellen.**
- **Netzwerkintegration.** Die [Netzwerkintegration](../operator/azure-stack-network.md) ist äußerst wichtig für Bereitstellung, Betrieb und Verwaltung von Azure Stack Hub-Systemen. Es müssen verschiedene Aspekte berücksichtigt werden, um sicherzustellen, dass die Azure Stack Hub-Lösung resilient ist und über eine hoch verfügbare physische Infrastruktur verfügt, um die zugehörigen Vorgänge zu unterstützen.
- **Firewallintegration.** Wir empfehlen Ihnen, Azure Stack Hub [mit einer Firewall](../operator/azure-stack-firewall.md) zu schützen. Firewalls können bei der Verhinderung von DDoS-Angriffen, bei Angriffserkennung und Inhaltsuntersuchung helfen. Allerdings muss daran gedacht werden, dass sie zu einem Durchsatzengpass für Azure-Speicherdienste werden können.
- **Zertifikatanforderungen.** Es ist sehr wichtig, dass [alle erforderlichen Zertifikate](../operator/azure-stack-pki-certs.md) zur Verfügung stehen, bevor ein Vor-Ort-Techniker in Ihrem Datencenter zur Bereitstellung eintrifft.

Nachdem alle erforderlichen Informationen im Bereitstellungsarbeitsblatt erfasst wurden, überprüft Microsoft, ob alle Validierungstools ausgeführt wurden, und unterstützt Sie bei allen weiteren Fragen. 

## <a name="hardware-delivery"></a>Hardwarelieferung

Microsoft arbeitet mit Ihnen zusammen, um sicherzustellen, dass alle erforderlichen Hardwareanforderungen innerhalb der festgelegten Zeit am Standort in den USA eintreffen.  

Es ist **äußerst wichtig**, dass alle erforderlichen Daten gesperrt und verfügbar sind, *bevor der Microsoft-Techniker für die Bereitstellung der Lösung vor Ort eintrifft*.

- Im Bereitstellungsarbeitsblatt wurden alle Daten ausgefüllt. 
- Alle Zertifikate müssen überprüft und einsatzbereit sein.
- Der Regionsname muss festgelegt werden.
- Alle Parameter für die Netzwerkintegration wurden abgeschlossen.

>[!Tip]
>Wenn eine dieser Informationen geändert wurde, stellen Sie in Zusammenarbeit mit Ihrer internen Organisation sicher, dass diese Informationen vor der Ankunft des Bereitstellungstechnikers vor Ort aktualisiert werden. Dadurch werden Verzögerungen bei der Bereitstellung vermieden.

## <a name="onsite-deployment"></a>Lokale Bereitstellung

Die Bereitstellung von Azure Stack Hub wird durch einen Bereitstellungstechniker von Microsoft vor Ort gestartet. Dabei wird vorausgesetzt, dass während der Bereitstellung auch ein Netzwerktechniker Ihrer Organisation vor Ort verfügbar ist.

Anhand der folgenden Überprüfungen wird beschrieben, was Sie vom Vor-Ort-Techniker während des Bereitstellungsverfahrens erwarten sollten:

- Auspacken und Inventarisieren der Hardware
- Anschließen der Stromversorgung und Einschalten der Lösung
- Überprüfen der Integrität der physischen Hardware
- Überprüfen der gesamten Verkabelung und Grenzkonnektivität, um sicherzustellen, dass die Lösung ordnungsgemäß zusammengesetzt wird und Ihre Anforderungen erfüllt.
- Konfigurieren des Lösungs-HLH (Hardware Lifecycle Host, Hardwarelebenszyklus-Host)
- Datencenternetzwerkintegration
- Überprüfen, ob alle Einstellungen der physischen Hardware korrekt sind
- Sicherstellen, dass die Firmware für alle Komponenten die neueste durch die Lösung genehmigte Version aufweist.
- Starten der Bereitstellung. 

## <a name="post-deployment"></a>Nach der Bereitstellung

Der Bereitstellungstechniker von Microsoft muss mehrere Schritte ausführen, bevor die Lösung an den Kunden übergeben wird. In dieser Phase ist die Validierung wichtig, um sicherzustellen, dass das System korrekt bereitgestellt wird und einwandfrei funktioniert.

Aktionen, die vom Microsoft-Bereitstellungstechniker durchgeführt werden müssen:

- Aktivieren von Ressourcenanbietern (RPs), die Mehrwert schaffen
- Ausführen von [test-azurestack](../operator/azure-stack-diagnostic-test.md)
- [Registrierung](../operator/azure-stack-registration-role.md) bei Azure
- [Marketplace-Syndikation](../operator/azure-stack-marketplace.md)
- Sichern der Konfigurationsdateien für Switch und HLH
- Vorbereiten einer Kundenzusammenfassung zur Bereitstellung
- [Überprüfen auf Updates](../operator/azure-stack-updates.md), um sicherzustellen, dass die Lösungssoftware auf die neueste Version aktualisiert wurde

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über die [Schritte zum Installieren und Konfigurieren von Microsoft Azure Stack Hub Ruggedized](deployment-overview.md).

