---
title: Voraussetzungen für die Installation von Event Hubs in Azure Stack Hub
description: Erfahren Sie mehr über die Voraussetzungen, die erfüllt sein müssen, bevor Sie den Event Hubs-Ressourcenanbieter in Azure Stack Hub installieren.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/09/2019
ms.reviewer: jfggdl
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: 45267292e317088adcf9de4e5e34946cabe4d239
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94545884"
---
# <a name="prerequisites-for-installing-event-hubs-on-azure-stack-hub"></a>Voraussetzungen für das Installieren von Event Hubs in Azure Stack Hub

[!INCLUDE [preview-banner](../includes/event-hubs-preview.md)]

Die folgenden Voraussetzungen müssen erfüllt sein, bevor Sie Event Hubs in Azure Stack Hub installieren können. **Möglicherweise sind mehrere Tage oder Wochen an Vorlaufzeit erforderlich** , um alle Schritte auszuführen.

> [!IMPORTANT]
> Für diese Voraussetzungen wird vorausgesetzt, dass Sie bereits mindestens ein integriertes Azure Stack Hub-System mit 4 Knoten bereitgestellt haben. Der Event Hubs-Ressourcenanbieter wird im Azure Stack Development Kit (ASDK) nicht unterstützt.

> [!IMPORTANT]
> Für Event Hubs ist Azure Stack Hub in der Buildversion 2002 oder höher erforderlich. Beachten Sie, dass Azure Stack Hub-Builds inkrementell sind. Wenn Sie beispielsweise die Version 1910 installiert haben, müssen Sie zunächst ein Upgrade auf [2002](./release-notes.md?view=azs-2002#2002-build-reference) und dann auf 2005 durchführen. Das heißt, Sie dürfen keinen zwischen zwei Builds liegenden Build überspringen.

## <a name="common-prerequisites"></a>Allgemeine Voraussetzungen

[!INCLUDE [Common RP prerequisites](../includes/resource-provider-prerequisites.md)]

## <a name="event-hubs-prerequisites"></a>Event Hubs-Voraussetzungen

1. Besorgen Sie PKI-SSL-Zertifikate (Public Key-Infrastruktur) für Event Hubs. Das SAN (Storage Area Network) muss dem folgenden Benennungsmuster entsprechen: `CN=*.eventhub.<region>.<fqdn>`. „Antragstellername“ kann angegeben werden, wird jedoch nicht von Event Hubs verwendet, wenn Zertifikate verarbeitet werden. Nur „Alternativer Antragstellername“ wird verwendet. Die vollständige Liste der detaillierten Anforderungen finden Sie unter [Azure Stack Hub-PKI-Zertifikatanforderungen](azure-stack-pki-certs.md).  

   ![Beispielzertifikat](media/event-hubs-rp-prerequisites/certificate-example.png)

   > [!NOTE]
   > **PFX-Dateien müssen kennwortgeschützt sein**. Das Kennwort wird später während der Installation angefordert.

2. Lesen Sie unbedingt [Überprüfen von Azure Stack Hub-PKI-Zertifikaten](azure-stack-validate-pki-certs.md). In diesem Artikel erfahren Sie, wie Sie die Zertifikate vorbereiten und überprüfen, die Sie für den Event Hubs-Ressourcenanbieter verwenden. 

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Schritt [installieren Sie den Event Hubs-Ressourcenanbieter](event-hubs-rp-install.md).
