---
title: Rotieren von Geheimnissen für den Event Hubs-Ressourcenanbieter in Azure Stack Hub
description: In diesem Artikel erfahren Sie, wie Sie Geheimnisse für den IoT Hub-Ressourcenanbieter in Azure Stack Hub rotieren.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 10/15/2020
ms.reviewer: jfggdl
ms.lastreviewed: 10/15/2020
ms.openlocfilehash: fe238576814d1aae5d7469e9438f8921f912f0ea
ms.sourcegitcommit: f56a5b287c90b2081ae111385c8b7833931d4059
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97349466"
---
# <a name="how-to-rotate-secrets-for-event-hubs-on-azure-stack-hub"></a>Rotieren von Geheimnissen für Event Hubs in Azure Stack Hub

In diesem Artikel erfahren Sie, wie Sie die Geheimnisse rotieren, die vom Event Hubs-Ressourcenanbieter verwendet werden.

## <a name="overview-and-prerequisites"></a>Übersicht und Voraussetzungen

[!INCLUDE [prereqs](../includes/resource-provider-va-rotate-secrets-prereqs.md)]

## <a name="prepare-a-new-tls-certificate"></a>Vorbereiten eines neuen TLS-Zertifikats

[!INCLUDE [prepare](../includes/resource-provider-va-rotate-secrets-prepare.md)]

## <a name="rotate-secrets"></a>Rotieren von Geheimnissen

[!INCLUDE [rotate](../includes/resource-provider-va-rotate-secrets-rotate.md)]

## <a name="troubleshooting"></a>Problembehandlung

Die Geheimnisrotation sollte erfolgreich und ohne Fehler abgeschlossen werden. Wenn Eine der folgenden Bedingungen im Administratorportal vorliegt, [öffnen Sie eine Supportanfrage](azure-stack-manage-basics.md#where-to-get-support), um Unterstützung zu erhalten:

   - Es liegen Authentifizierungsprobleme, einschließlich Probleme beim Herstellen einer Verbindung mit dem Event Hubs-Ressourcenanbieter vor.
   - Es kann kein Upgrade für den Ressourcenanbieter durchgeführt werden, oder Konfigurationsparameter können nicht bearbeitet werden.
   - Die Nutzungsmetriken werden nicht angezeigt.
   - Es werden keine Rechnungen generiert.
   - Es werden keine Sicherungen durchgeführt.

## <a name="next-steps"></a>Nächste Schritte

Ausführliche Informationen zur Rotation Ihrer Azure Stack Hub-Infrastrukturgeheimnisse finden Sie unter [Rotieren von Geheimnissen in Azure Stack Hub](azure-stack-rotate-secrets.md).