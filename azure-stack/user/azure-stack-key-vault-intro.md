---
title: Einführung in Key Vault in Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Key Vault Schlüssel und Geheimnisse in Azure Stack verwaltet.
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
ms.date: 10/01/2019
ms.author: sethm
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 2ad70754a63660a2ad374a324e8778c405446d29
ms.sourcegitcommit: bbf3edbfc07603d2c23de44240933c07976ea550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71714669"
---
# <a name="introduction-to-key-vault-in-azure-stack"></a>Einführung in Key Vault in Azure Stack

## <a name="prerequisites"></a>Voraussetzungen

* Abonnieren Sie ein Angebot, das den Azure Key Vault-Dienst umfasst.  
* PowerShell wird installiert und [für die Verwendung mit Azure Stack konfiguriert](azure-stack-powershell-configure-user.md).

## <a name="key-vault-basics"></a>Key Vault-Grundlagen

Key Vault in Azure Stack unterstützt Sie dabei, kryptografische Schlüssel und Geheimnisse zu schützen, die von Cloud-Apps und -diensten verwendet werden. Mit Key Vault können Sie Schlüssel und Geheimnisse wie die folgenden verschlüsseln:

* Authentifizierungsschlüssel
* Speicherkontoschlüssel
* Datenverschlüsselungsschlüssel
* PFX-Dateien
* Kennwörter

Der Schlüsseltresor optimiert die Schlüsselverwaltung und ermöglicht es Ihnen, die Kontrolle über Schlüssel zu behalten, die für den Datenzugriff und die Verschlüsselung Ihrer Daten verwendet werden. Entwickler können Schlüssel für Tests und Entwicklung innerhalb von Minuten erstellen und diese später nahtlos in Schlüssel für die Produktion migrieren. Sicherheitsadministratoren können nach Bedarf Berechtigungen für Schlüssel erteilen (und widerrufen).

Jeder Benutzer mit einem Azure Stack-Abonnement kann Schlüsseltresore erstellen und verwenden. Von Key Vault profitieren zwar in erster Linie Entwickler und Sicherheitsadministratoren, die Lösung kann jedoch durch den Betreiber implementiert und verwaltet werden, der andere Azure Stack-Dienste für eine Organisation verwaltet. So kann sich der Azure Stack-Administrator beispielsweise mit einem Azure Stack-Abonnement anmelden und einen Tresor für die Schlüsselspeicherung der Organisation erstellen. Im Anschluss daran, kann er dann diese Aufgaben übernehmen:

* Erstellen oder Importieren eines Schlüssels oder Geheimnisses
* Widerrufen oder Löschen eines Schlüssels oder Geheimnisses
* Autorisieren des Zugriffs von Benutzern oder Apps auf den Schlüsseltresor, sodass sie die Schlüssel und Geheimnisse verwalten oder verwenden können
* Konfigurieren der Schlüsselverwendung (beispielsweise Signieren oder Verschlüsseln)

Der Betreiber kann Entwicklern Uniform Resource Identifiers (URIs) zur Verfügung stellen, die sie in ihren Apps aufrufen können. Darüber hinaus kann der Betreiber Sicherheitsadministratoren Protokollinformationen zur Schlüsselverwendung zur Verfügung stellen.

Entwickler können die Schlüssel außerdem durch Verwendung von APIs direkt verwalten. Weitere Informationen finden Sie im [Entwicklerhandbuch zu Azure-Schlüsseltresor](/azure/key-vault/key-vault-developers-guide).

## <a name="scenarios"></a>Szenarien

Die folgenden Szenarien veranschaulichen, wie Entwickler und Sicherheitsadministratoren von Key Vault profitieren können.

### <a name="developer-for-an-azure-stack-app"></a>Entwickler einer Azure Stack-App

**Problem:** Ich möchte eine App für Azure Stack schreiben, die Schlüssel für die Signierung und Verschlüsselung verwendet. Diese Schlüssel sollen sich außerhalb meiner App befinden, damit die Lösung auch für eine geografisch verteilte App geeignet ist.

**Stellungnahme:** Schlüssel werden in einem Tresor gespeichert und bei Bedarf über einen URI aufgerufen.

### <a name="developer-for-software-as-a-service-saas"></a>Entwickler von SaaS-Lösungen (Software-as-a-Service)

**Problem:** Ich möchte weder die Verantwortung noch die Haftung für Schlüssel und Geheimnisse meiner Kunden übernehmen. Ich möchte, dass Kunden sowohl die Verantwortung als auch die Verwaltung für ihre Schlüssel übernehmen, sodass ich mich auf meine eigentlichen Aufgaben konzentrieren kann – die Bereitstellung der Softwarefunktionen.

**Stellungnahme:** Kunden können ihre eigenen Schlüssel in Azure Stack importieren und verwalten.

### <a name="chief-security-officer-cso"></a>Sicherheitsbeauftragter (Chief Security Officer, CSO)

**Problem:** Ich möchte sicherstellen, dass meine Organisation die Kontrolle über den gesamten Lebenszyklus der Schlüssel behält und die Schlüsselverwendung überwachen kann.

**Stellungnahme:** Mit Key Vault sind Ihre Schlüssel für Microsoft nicht sichtbar und können auch nicht extrahiert werden. Wenn eine App kryptografische Vorgänge unter Verwendung von Kundenschlüsseln ausführen muss, verwendet Key Vault die Schlüssel im Auftrag der App. Die App hat keine Informationen über die Kundenschlüssel. Auch wenn wir verschiedene Azure Stack-Dienste und -Ressourcen nutzen, können Sie die Schlüssel über einen zentralen Ort in Azure Stack verwalten. Der Tresor stellt eine einzige Benutzeroberfläche bereit – unabhängig davon, über wie viele Tresore Sie in Azure Stack verfügen, welche Regionen diese unterstützen und von welchen Apps die Tresore verwendet werden.

## <a name="next-steps"></a>Nächste Schritte

* [Verwalten von Key Vault in Azure Stack über das Portal](azure-stack-key-vault-manage-portal.md)  
* [Verwalten von Key Vault in Azure Stack mithilfe von PowerShell](azure-stack-key-vault-manage-powershell.md)
