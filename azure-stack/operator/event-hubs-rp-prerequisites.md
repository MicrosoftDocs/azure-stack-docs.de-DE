---
title: Voraussetzungen für das Installieren von Event Hubs in Azure Stack Hub
description: Erfahren Sie mehr über die Voraussetzungen, die erfüllt sein müssen, bevor Sie den Event Hubs-Ressourcenanbieter in Azure Stack Hub installieren.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/09/2019
ms.reviewer: jfggdl
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: 1d03c4fab16920b3df367050030c88162d9f1002
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86490098"
---
# <a name="prerequisites-for-installing-event-hubs-on-azure-stack-hub"></a>Voraussetzungen für das Installieren von Event Hubs in Azure Stack Hub

[!INCLUDE [preview-banner](../includes/event-hubs-preview.md)]

Die folgenden Voraussetzungen müssen erfüllt sein, bevor Sie Event Hubs in Azure Stack Hub installieren können. **Möglicherweise sind mehrere Tage oder Wochen an Vorlaufzeit erforderlich**, um alle Schritte auszuführen.

> [!IMPORTANT]
> Für diese Voraussetzungen wird vorausgesetzt, dass Sie bereits mindestens ein integriertes Azure Stack Hub-System mit 4 Knoten bereitgestellt haben. Der Event Hubs-Ressourcenanbieter wird im Azure Stack Development Kit (ASDK) nicht unterstützt.

> [!IMPORTANT]
> Für Event Hubs ist Azure Stack Hub in der Buildversion 2002 oder höher erforderlich. Beachten Sie, dass Azure Stack Hub-Builds inkrementell sind. Wenn Sie z. B. [Version 1908](./release-notes.md?view=azs-1908#1908-build-reference) installiert haben, müssen Sie zunächst ein Upgrade auf [1910](./release-notes.md?view=azs-1910#1910-build-reference) und dann auf 2002 durchführen. Das heißt, Sie dürfen keinen zwischen zwei Builds liegenden Build überspringen.

## <a name="common-prerequisites"></a>Allgemeine Voraussetzungen

[!INCLUDE [Common RP prerequisites](../includes/marketplace-resource-provider-prerequisites.md)]

## <a name="event-hubs-prerequisites"></a>Event Hubs-Voraussetzungen

1. Besorgen Sie PKI-SSL-Zertifikate (Public Key-Infrastruktur) für Event Hubs. Das SAN (Storage Area Network) muss dem folgenden Benennungsmuster entsprechen: `CN=*.eventhub.<region>.<fqdn>`. „Antragstellername“ kann angegeben werden, wird jedoch nicht von Event Hubs verwendet, wenn Zertifikate verarbeitet werden. Nur „Alternativer Antragstellername“ wird verwendet. Die vollständige Liste der detaillierten Anforderungen finden Sie unter [Azure Stack Hub-PKI-Zertifikatanforderungen](azure-stack-pki-certs.md).  

   ![Beispielzertifikat](media/event-hubs-rp-prerequisites/certificate-example.png)

   > [!NOTE]
   > **PFX-Dateien müssen kennwortgeschützt sein**. Das Kennwort wird später während der Installation angefordert.

2. Lesen Sie unbedingt [Überprüfen von Azure Stack Hub-PKI-Zertifikaten](azure-stack-validate-pki-certs.md). In diesem Artikel erfahren Sie, wie Sie die Zertifikate vorbereiten und überprüfen, die Sie für den Event Hubs-Ressourcenanbieter verwenden. 

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Schritt [installieren Sie den Event Hubs-Ressourcenanbieter](event-hubs-rp-install.md).
