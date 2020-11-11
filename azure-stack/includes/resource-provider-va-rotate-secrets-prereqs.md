---
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: include
ms.date: 10/10/2020
ms.reviewer: bryanla
ms.lastreviewed: 10/20/2020
ms.openlocfilehash: 294f0f5b1f6ac45d53f5d3eebc222b3edba23776
ms.sourcegitcommit: 81e2d627c9dc4cc365deb4a0e0674b5ab3a7efbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/21/2020
ms.locfileid: "93050297"
---
> [!NOTE]
> Die Rotation von Geheimnissen für Ressourcenanbietern mit Mehrwert wird derzeit nur über PowerShell unterstützt. 

Wie die Azure Stack Hub-Infrastruktur verwenden Ressourcenanbieter mit Mehrwert sowohl interne als auch externe Geheimnisse. Geheimnisse können verschiedene Formen aufweisen, zum Beispiel Kennwörter und von X509-Zertifikaten verwaltete Verschlüsselungsschlüssel. Als Operator tragen Sie folgende Verantwortungen:

- Bereitstellen aktueller externer Geheimnisse, z. B. ein neues TLS-Zertifikat, das zum Schutz der Ressourcenanbieterendpunkte verwendet wird
- Verwalten der regulären Rotation von Ressourcenanbietergeheimnissen

Vorbereitung des Rotationsprozesses:

1. Überprüfen Sie die [Zertifikatanforderungen der Public Key-Infrastruktur (PKI) von Azure Stack Hub](../operator/azure-stack-pki-certs.md#certificate-requirements) auf wichtige Informationen zu Voraussetzungen, einschließlich Informationen zum erforderlichen PFX-Format, bevor Sie das X509-Zertifikat abrufen oder erneuern. Überprüfen Sie auch die Voraussetzungen für Ihren spezifischen Ressourcenanbieter mit Mehrwert, die im [Abschnitt zu optionalen PaaS-Zertifikaten](../operator/azure-stack-pki-certs.md#optional-paas-certificates) angegeben werden.

2. Falls noch nicht geschehen, [installieren Sie das PowerShell Az-Modul für Azure Stack Hub](../operator/powershell-install-az-module.md), bevor Sie fortfahren. Für die Azure Stack Hub-Geheimnisrotation ist Version 2.0.2-preview oder höher erforderlich. Weitere Informationen finden Sie unter [Migrieren von AzureRM zum Az-Modul von Azure PowerShell in Azure Stack Hub](../operator/migrate-azurerm-az.md).
