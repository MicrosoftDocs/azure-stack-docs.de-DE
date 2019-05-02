---
title: Einführung in Azure Stack Key Vault | Microsoft-Dokumentation
description: Erfahren Sie, wie Azure Stack Key Vault Schlüssel und Geheimnisse verwaltet
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 70f1684a-3fbb-4cd1-bf29-9f9882e98fe9
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/05/2019
ms.author: sethm
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: 87c93f77011082d3e43b1c7d238999441f1b90c1
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "64310332"
---
# <a name="introduction-to-key-vault-in-azure-stack"></a>Einführung in Key Vault in Azure Stack

## <a name="prerequisites"></a>Voraussetzungen

* Sie müssen ein Angebot abonnieren, das den Azure Key Vault-Dienst umfasst.  
* [PowerShell ist für die Verwendung mit Azure Stack konfiguriert.](azure-stack-powershell-configure-user.md)

## <a name="key-vault-basics"></a>Key Vault-Grundlagen

Key Vault in Azure Stack unterstützt Sie dabei, kryptografische Schlüssel und Geheimnisse zu schützen, die von Cloudanwendungen und -diensten verwendet werden. Mit Key Vault können Sie Schlüssel und Geheimnisse wie die folgenden verschlüsseln:

* Authentifizierungsschlüssel
* Speicherkontoschlüssel
* Datenverschlüsselungsschlüssel
* PFX-Dateien
* Kennwörter

Der Schlüsseltresor optimiert die Schlüsselverwaltung und ermöglicht es Ihnen, die Kontrolle über Schlüssel zu behalten, die für den Datenzugriff und die Verschlüsselung Ihrer Daten verwendet werden. Entwickler können Schlüssel für Tests und Entwicklung innerhalb von Minuten erstellen und diese später nahtlos in Schlüssel für die Produktion migrieren. Sicherheitsadministratoren können nach Bedarf Berechtigungen für Schlüssel erteilen (und widerrufen).

Jeder Benutzer mit einem Azure Stack-Abonnement kann Schlüsseltresore erstellen und verwenden. Von Key Vault profitieren zwar in erster Linie Entwickler und Sicherheitsadministratoren, die Lösung kann jedoch durch den Betreiber implementiert und verwaltet werden, der andere Azure Stack-Dienste für eine Organisation verwaltet. So kann sich der Azure Stack-Administrator beispielsweise mit einem Azure Stack-Abonnement anmelden, einen Tresor für die Schlüsselspeicherung der Organisation erstellen und dann diese operativen Aufgaben übernehmen:

* Erstellen oder Importieren eines Schlüssels oder Geheimnisses
* Widerrufen oder Löschen eines Schlüssels oder Geheimnisses
* Autorisieren des Zugriffs von Benutzern oder Anwendungen auf den Schlüsseltresor, sodass sie die Schlüssel und Geheimnisse verwalten oder verwenden können
* Konfigurieren der Schlüsselverwendung (beispielsweise Signieren oder Verschlüsseln)

Der Betreiber kann Entwicklern Uniform Resource Identifiers (URIs) zur Verfügung stellen, die sie in ihren Anwendungen aufrufen können. Darüber hinaus kann der Betreiber Sicherheitsadministratoren Protokollinformationen zur Schlüsselverwendung zur Verfügung stellen.

Entwickler können die Schlüssel außerdem durch Verwendung von APIs direkt verwalten. Weitere Informationen finden Sie im Key Vault-Entwicklerhandbuch.

## <a name="scenarios"></a>Szenarien

Die folgenden Szenarien veranschaulichen, wie Entwickler und Sicherheitsadministratoren von Key Vault profitieren können:

### <a name="developer-for-an-azure-stack-application"></a>Entwickler einer Azure Stack-Anwendung

**Problem:** Ich möchte eine Anwendung für Azure Stack schreiben, die Schlüssel für die Signierung und Verschlüsselung verwendet. Diese Schlüssel sollen sich außerhalb meiner Anwendung befinden, damit die Lösung auch für eine geografisch verteilte Anwendung geeignet ist.

**Stellungnahme:** Schlüssel werden in einem Tresor gespeichert und bei Bedarf über einen URI aufgerufen.

### <a name="developer-for-software-as-a-service-saas"></a>Entwickler von SaaS-Lösungen (Software-as-a-Service)

**Problem:** Ich möchte weder die Verantwortung noch die Haftung für Schlüssel und Geheimnisse meiner Kunden übernehmen. Ich möchte, dass Kunden selbst für ihre Schlüssel verantwortlich sind und diese selbst verwalten, sodass ich mich auf meine eigentlichen Aufgaben konzentrieren kann – die Bereitstellung der Softwarefeatures.

**Stellungnahme:** Kunden können eigene Schlüssel in Azure Stack importieren und sie verwalten.

### <a name="chief-security-officer-cso"></a>Sicherheitsbeauftragter (Chief Security Officer, CSO)

**Problem:** Ich möchte sicherstellen, dass meine Organisation die Kontrolle über den gesamten Lebenszyklus der Schlüssel behält und die Schlüsselverwendung überwachen kann.

**Stellungnahme:** Mit Key Vault sind Ihre Schlüssel für Microsoft nicht sichtbar und können auch nicht extrahiert werden. Wenn eine Anwendung kryptografische Vorgänge unter Verwendung von Kundenschlüsseln ausführen muss, verwendet Key Vault die Schlüssel im Auftrag der Anwendung. Die Anwendung hat keine Informationen über die Kundenschlüssel. Auch wenn wir verschiedene Azure Stack-Dienste und -Ressourcen nutzen, können Sie die Schlüssel über einen zentralen Ort in Azure Stack verwalten. Der Tresor stellt eine einzige Benutzeroberfläche bereit – unabhängig davon, über wie viele Tresore Sie in Azure Stack verfügen, welche Regionen diese unterstützen und von welchen Anwendungen die Tresore verwendet werden.

## <a name="next-steps"></a>Nächste Schritte

* [Verwalten von Key Vault in Azure Stack über das Portal](azure-stack-key-vault-manage-portal.md)  
* [Verwalten von Key Vault in Azure Stack mithilfe von PowerShell](azure-stack-key-vault-manage-powershell.md)

