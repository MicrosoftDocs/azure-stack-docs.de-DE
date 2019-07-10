---
title: Konfigurieren von Azure Stack-Sicherheitskontrollen
description: Erfahren Sie, wie Sie Sicherheitskontrollen in Azure Stack konfigurieren.
services: azure-stack
author: PatAltimore
ms.service: azure-stack
ms.topic: article
ms.date: 06/17/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 06/17/2019
ms.openlocfilehash: b36a6d826dc7249f10b4785b27511096e45923a9
ms.sourcegitcommit: 7348876a97e8bed504b5f5d90690ec8d1d9472b0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67557856"
---
# <a name="configure-azure-stack-security-controls"></a>Konfigurieren von Azure Stack-Sicherheitskontrollen

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

Dieser Artikel beschreibt die Sicherheitskontrollen, die in Azure Stack geändert werden können, und hebt die Vor- und Nachteile hervor, wo zutreffend.

Die Azure Stack-Architektur basiert auf den Säulen zweier Sicherheitsprinzipien: „Annahme einer Sicherheitsverletzung“ und „Standardmäßig verstärkt“. Weitere Informationen zu Sicherheit in Azure Stack finden Sie unter [Sicherheitsstatus der Azure Stack-Infrastruktur](azure-stack-security-foundations.md). Zwar ist der Standardsicherheitsstatus von Azure Stack produktionsbereit, doch gibt es einige Bereitstellungsszenarien, für die zusätzliche, verstärkende Sicherheitsmaßnahmen erforderlich sind.

## <a name="tls-version-policy"></a>TLS-Versionsrichtlinie

Das TLS-Protokoll (Transport Layer Security) ist ein weit verbreitetes Kryptografieprotokoll zur Einrichtung einer verschlüsselte Kommunikation über das Netzwerk. TLS hat sich im Laufe der Zeit entwickelt, und es wurden mehrere Versionen veröffentlicht. Die Azure Stack-Infrastruktur verwendet ausschließlich TLS 1.2 für die gesamte Kommunikation. Bei externen Schnittstellen verwendet Azure Stack zurzeit standardmäßig TLS 1.2. Aus Gründen der Abwärtskompatibilität unterstützt es aber auch eine Aushandlungen auf das niedrigere TLS 1.1. und 1.0. Wenn ein TLS-Client Kommunikation über TLS 1.1 oder TLS 1.0 anfordert, berücksichtigt Azure Stack die Anforderung, indem es eine niedrigere Version von TLS aushandelt. Wenn der Client TLS 1.2 anfordert, stellt Azure Stack eine TLS-Verbindung mit TLS 1.2 her.

Da TLS 1.0 und 1.1 inkrementell eingestellt oder von Organisationen und Compliancestandards gesperrt werden, können Sie nun ab dem Update 1906 die TLS-Richtlinie in Azure Stack konfigurieren. Sie können eine reine TLS 1.2-Richtlinie erzwingen, bei der jeder Versuch zum Einrichten einer TLS-Sitzung mit einer niedrigeren Version als 1.2 nicht zulässig ist und abgelehnt wird.

> [!IMPORTANT]
> Microsoft empfiehlt die Verwendung einer reinen TLS 1.2-Richtlinie für Azure Stack-Produktionsumgebungen.

## <a name="get-tls-policy"></a>Abrufen der TLS-Richtlinie

Verwenden Sie den [privilegierten Endpunkt (PEP)](azure-stack-privileged-endpoint.md), um die TLS-Richtlinie für alle Azure Stack-Endpunkte anzuzeigen:

```powershell
Get-TLSPolicy
```

Beispielausgabe:

    TLS_1.2

## <a name="set-tls-policy"></a>Festlegen der TLS-Richtlinie

Verwenden Sie den [privilegierten Endpunkt (PEP)](azure-stack-privileged-endpoint.md), um die TLS-Richtlinie für alle Azure Stack-Endpunkte festzulegen:

```powershell
Set-TLSPolicy -Version <String>
```

Parameter für das Cmdlet *Set-TLSPolicy*:

| Parameter | BESCHREIBUNG | type | Erforderlich |
|---------|---------|---------|---------|
| *Version* | Zulässige Versionen von TLS in Azure Stack | string | Ja|

Verwenden Sie einen der folgenden Werte, um die zulässigen TLS-Versionen für alle Azure Stack-Endpunkte zu konfigurieren:

| Versionswert | BESCHREIBUNG |
|---------|---------|
| *TLS_All* | Azure Stack-TLS-Endpunkte unterstützen TLS 1.2, aber die Aushandlung einer niedrigeren Version auf TLS 1.1 und TLS 1.0 ist zulässig. |
| *TLS_1.2* | Azure Stack-TLS-Endpunkte unterstützen nur TLS 1.2. | 

Die Aktualisierung der TLS-Richtlinie dauert ein paar Minuten.

### <a name="enforce-tls-12-configuration-example"></a>Konfigurationsbeispiel für das Erzwingen von TLS 1.2

In diesem Beispiel wird Ihre TLS-Richtlinie so festgelegt, dass sie ausschließlich TLS 1.2 erzwingt.

```powershell
Set-TLSPolicy -Version TLS_1.2
```

Beispielausgabe:

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

### <a name="allow-all-versions-of-tls-12-11-and-10-configuration-example"></a>Konfigurationsbeispiel für das Zulassen aller Versionen von TLS (1.2, 1.1 und 1.0)

In diesem Beispiel wird Ihre TLS-Richtlinie so festgelegt, dass alle Versionen von TLS (1.2, 1.1 und 1.0) zugelassen werden.

```powershell
Set-TLSPolicy -Version TLS_All
```

Beispielausgabe:

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

## <a name="next-steps"></a>Nächste Schritte

- [Informationen zum Sicherheitsstatus der Azure Stack-Infrastruktur](azure-stack-security-foundations.md)
- [Erfahren Sie, wie Sie Ihre Geheimnisse in Azure Stack rotieren.](azure-stack-rotate-secrets.md)
- [Aktualisieren von Windows Defender Antivirus in Azure Stack](azure-stack-security-av.md)
