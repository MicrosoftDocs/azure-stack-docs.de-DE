---
title: Rotieren von Geheimnissen für den IoT Hub-Ressourcenanbieter in Azure Stack Hub
description: In diesem Artikel erfahren Sie, wie Sie Geheimnisse für den IoT Hub-Ressourcenanbieter in Azure Stack Hub rotieren.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/15/2020
ms.reviewer: bryanla
ms.lastreviewed: 11/15/2020
ms.openlocfilehash: d693b73794b71ec32816f2e79370e2f836b00e7b
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95517553"
---
# <a name="how-to-rotate-secrets-for-iot-hub-on-azure-stack-hub"></a>Rotieren von Geheimnissen für IoT Hub in Azure Stack Hub

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

In diesem Artikel erfahren Sie, wie Sie die Geheimnisse rotieren, die vom IoT Hub-Ressourcenanbieter verwendet werden.

## <a name="overview-and-prerequisites"></a>Übersicht und Voraussetzungen

[!INCLUDE [prereqs](../includes/resource-provider-va-rotate-secrets-prereqs.md)]

## <a name="prepare-a-new-tls-certificate"></a>Vorbereiten eines neuen TLS-Zertifikats

[!INCLUDE [prepare](../includes/resource-provider-va-rotate-secrets-prepare.md)]

## <a name="rotate-secrets"></a>Rotieren von Geheimnissen

[!INCLUDE [rotate](../includes/resource-provider-va-rotate-secrets-rotate.md)]

## <a name="troubleshooting"></a>Problembehandlung

Die Geheimnisrotation sollte erfolgreich und ohne Fehler abgeschlossen werden. Wenn Eine der folgenden Bedingungen im Administratorportal vorliegt, [öffnen Sie eine Supportanfrage](azure-stack-manage-basics.md#where-to-get-support), um Unterstützung zu erhalten:

   - Es liegen Authentifizierungsprobleme, einschließlich Probleme beim Herstellen einer Verbindung mit dem IoT Hub-Ressourcenanbieter vor.
   - Es kann kein Upgrade für den Ressourcenanbieter durchgeführt werden, oder Konfigurationsparameter können nicht bearbeitet werden.
   - Die Nutzungsmetriken werden nicht angezeigt.
   - Es werden keine Rechnungen generiert.
   - Es werden keine Sicherungen durchgeführt.

## <a name="next-steps"></a>Nächste Schritte

Ausführliche Informationen zur Rotation Ihrer Azure Stack Hub-Infrastrukturgeheimnisse finden Sie unter [Rotieren von Geheimnissen in Azure Stack Hub](azure-stack-rotate-secrets.md).