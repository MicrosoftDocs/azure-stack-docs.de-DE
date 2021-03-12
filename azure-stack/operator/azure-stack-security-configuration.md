---
title: Konfigurieren von Sicherheitskontrollen in Azure Stack Hub
description: Hier erfahren Sie, wie Sie Sicherheitskontrollen in Azure Stack Hub konfigurieren.
author: BryanLa
ms.topic: how-to
ms.date: 03/04/2020
ms.author: bryanla
ms.reviewer: fiseraci
ms.lastreviewed: 06/17/2019
ms.openlocfilehash: 300584eba787cf90e459c4faccf5dddf97d37ceb
ms.sourcegitcommit: ccc4ee05d71496653b6e27de1bb12e4347e20ba4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102231182"
---
# <a name="configure-azure-stack-hub-security-controls"></a>Konfigurieren von Azure Stack Hub-Sicherheitskontrollen

Dieser Artikel beschreibt die Sicherheitskontrollen, die in Azure Stack Hub geändert werden können, und hebt die Vor- und Nachteile hervor, wo zutreffend.

Die Azure Stack Hub-Architektur basiert auf den Säulen zweier Sicherheitsprinzipien: „Annahme einer Sicherheitsverletzung“ und „Standardmäßig verstärkt“. Weitere Informationen zu Sicherheit in Azure Stack Hub finden Sie unter [Sicherheitskontrollen der Azure Stack Hub-Infrastruktur](azure-stack-security-foundations.md). Zwar ist der Standardsicherheitsstatus von Azure Stack Hub produktionsbereit, doch gibt es einige Bereitstellungsszenarien, für die zusätzliche, verstärkende Sicherheitsmaßnahmen erforderlich sind.

## <a name="tls-version-policy"></a>TLS-Versionsrichtlinie

Das TLS-Protokoll (Transport Layer Security) ist ein weit verbreitetes Kryptografieprotokoll zur Einrichtung verschlüsselter Kommunikation über das Netzwerk. TLS hat sich im Laufe der Zeit entwickelt, und es wurden mehrere Versionen veröffentlicht. Die Azure Stack Hub-Infrastruktur verwendet ausschließlich TLS 1.2 für die gesamte Kommunikation. Bei externen Schnittstellen verwendet Azure Stack Hub zurzeit standardmäßig TLS 1.2. Aus Gründen der Abwärtskompatibilität unterstützt es aber auch eine Aushandlungen auf das niedrigere TLS 1.1. und 1.0. Wenn ein TLS-Client Kommunikation über TLS 1.1 oder TLS 1.0 anfordert, berücksichtigt Azure Stack Hub die Anforderung, indem es eine niedrigere Version von TLS aushandelt. Wenn der Client TLS 1.2 anfordert, stellt Azure Stack Hub eine TLS-Verbindung mit TLS 1.2 her.

Da TLS 1.0 und 1.1 inkrementell eingestellt oder von Organisationen und Compliancestandards gesperrt werden, können Sie nun ab dem Update 1906 die TLS-Richtlinie in Azure Stack Hub konfigurieren. Sie können eine reine TLS 1.2-Richtlinie erzwingen, bei der jeder Versuch zum Einrichten einer TLS-Sitzung mit einer niedrigeren Version als 1.2 nicht zulässig ist und abgelehnt wird.

> [!IMPORTANT]
> Microsoft empfiehlt die Verwendung einer reinen TLS 1.2-Richtlinie für Azure Stack Hub-Produktionsumgebungen.

## <a name="get-tls-policy"></a>Abrufen der TLS-Richtlinie

Verwenden Sie den [privilegierten Endpunkt (PEP)](azure-stack-privileged-endpoint.md), um die TLS-Richtlinie für alle Azure Stack Hub-Endpunkte anzuzeigen:

```powershell
Get-TLSPolicy
```

Beispielausgabe:

```powershell
TLS_1.2
```

## <a name="set-tls-policy"></a>Festlegen der TLS-Richtlinie

Verwenden Sie den [privilegierten Endpunkt (PEP)](azure-stack-privileged-endpoint.md), um die TLS-Richtlinie für alle Azure Stack Hub-Endpunkte festzulegen:

```powershell
Set-TLSPolicy -Version <String>
```

Parameter für das Cmdlet *Set-TLSPolicy*:

| Parameter | BESCHREIBUNG | Typ | Erforderlich |
|-----|-----|-----|-----|
| *Version* | Zulässige Versionen von TLS in Azure Stack Hub | String | ja |

Verwenden Sie einen der folgenden Werte, um die zulässigen TLS-Versionen für alle Azure Stack Hub-Endpunkte zu konfigurieren:

| Versionswert | BESCHREIBUNG |
|-------|-------|
| *TLS_All* | Azure Stack Hub-TLS-Endpunkte unterstützen TLS 1.2, aber die Aushandlung einer niedrigeren Version auf TLS 1.1 und TLS 1.0 ist zulässig. |
| *TLS_1.2* | Azure Stack Hub-TLS-Endpunkte unterstützen nur TLS 1.2. |

Die Aktualisierung der TLS-Richtlinie dauert ein paar Minuten.

### <a name="enforce-tls-12-configuration-example"></a>Konfigurationsbeispiel für das Erzwingen von TLS 1.2

In diesem Beispiel wird Ihre TLS-Richtlinie so festgelegt, dass sie ausschließlich TLS 1.2 erzwingt.

```powershell
Set-TLSPolicy -Version TLS_1.2
```

Beispielausgabe:

```powershell
VERBOSE: Successfully setting enforce TLS 1.2 to True
VERBOSE: Invoking action plan to update GPOs
VERBOSE: Create Client for execution of action plan
VERBOSE: Start action plan
<...>
VERBOSE: Verifying TLS policy
VERBOSE: Get GPO TLS protocols registry 'enabled' values
VERBOSE: GPO TLS applied with the following preferences:
VERBOSE:     TLS protocol SSL 2.0 enabled value: 0
VERBOSE:     TLS protocol SSL 3.0 enabled value: 0
VERBOSE:     TLS protocol TLS 1.0 enabled value: 0
VERBOSE:     TLS protocol TLS 1.1 enabled value: 0
VERBOSE:     TLS protocol TLS 1.2 enabled value: 1
VERBOSE: TLS 1.2 is enforced
```

### <a name="allow-all-versions-of-tls-12-11-and-10-configuration-example"></a>Konfigurationsbeispiel für das Zulassen aller Versionen von TLS (1.2, 1.1 und 1.0)

In diesem Beispiel wird Ihre TLS-Richtlinie so festgelegt, dass alle Versionen von TLS (1.2, 1.1 und 1.0) zugelassen werden.

```powershell
Set-TLSPolicy -Version TLS_All
```

Beispielausgabe:

```powershell
VERBOSE: Successfully setting enforce TLS 1.2 to False
VERBOSE: Invoking action plan to update GPOs
VERBOSE: Create Client for execution of action plan
VERBOSE: Start action plan
<...>
VERBOSE: Verifying TLS policy
VERBOSE: Get GPO TLS protocols registry 'enabled' values
VERBOSE: GPO TLS applied with the following preferences:
VERBOSE:     TLS protocol SSL 2.0 enabled value: 0
VERBOSE:     TLS protocol SSL 3.0 enabled value: 0
VERBOSE:     TLS protocol TLS 1.0 enabled value: 1
VERBOSE:     TLS protocol TLS 1.1 enabled value: 1
VERBOSE:     TLS protocol TLS 1.2 enabled value: 1
VERBOSE: TLS 1.2 is not enforced
```

## <a name="next-steps"></a>Nächste Schritte

- [Informationen zum Sicherheitsstatus der Azure Stack Hub-Infrastruktur](azure-stack-security-foundations.md)
- [Informationen zum Rotieren Ihrer Geheimnisse in Azure Stack Hub](azure-stack-rotate-secrets.md)
- [Aktualisieren von Windows Defender Antivirus in Azure Stack Hub](azure-stack-security-av.md)
