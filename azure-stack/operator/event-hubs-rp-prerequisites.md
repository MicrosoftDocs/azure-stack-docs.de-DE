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
ms.openlocfilehash: b4286074828a0cea911ed6ccd977b6b0690e7eb1
ms.sourcegitcommit: 34babe5abf1bceee718011b5c5c25f75e1b03b0c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100562976"
---
# <a name="prerequisites-for-installing-event-hubs-on-azure-stack-hub"></a>Voraussetzungen für das Installieren von Event Hubs in Azure Stack Hub

Die folgenden Voraussetzungen müssen erfüllt sein, bevor Sie Event Hubs in Azure Stack Hub installieren können. **Möglicherweise sind mehrere Tage oder Wochen an Vorlaufzeit erforderlich**, um alle Schritte auszuführen.

> [!IMPORTANT]
> Für diese Voraussetzungen wird vorausgesetzt, dass Sie bereits mindestens ein integriertes Azure Stack Hub-System mit 4 Knoten bereitgestellt haben. Der Event Hubs-Ressourcenanbieter wird im Azure Stack Development Kit (ASDK) nicht unterstützt.

> [!IMPORTANT]
> Für Event Hubs ist Azure Stack Hub in der Buildversion 2005 oder höher erforderlich. Beachten Sie, dass Azure Stack Hub-Builds inkrementell sind. Wenn Sie beispielsweise die Version 1910 installiert haben, müssen Sie zunächst ein Upgrade auf [2002](./release-notes.md?view=azs-2002&preserve-view=true#2002-build-reference) und dann auf 2005 durchführen. Das heißt, Sie dürfen keinen zwischen zwei Builds liegenden Build überspringen.

## <a name="common-prerequisites"></a>Allgemeine Voraussetzungen

[!INCLUDE [Common RP prerequisites](../includes/resource-provider-prerequisites.md)]

## <a name="event-hubs-prerequisites"></a>Event Hubs-Voraussetzungen

1. Besorgen Sie PKI-SSL-Zertifikate (Public Key-Infrastruktur) für Event Hubs. Der alternative Antragstellername muss das folgende Namensmuster aufweisen: `CN=*.eventhub.<region>.<fqdn>`. „Antragstellername“ kann angegeben werden, wird jedoch nicht von Event Hubs verwendet, wenn Zertifikate verarbeitet werden. Nur der alternative Antragstellername wird verwendet. Die vollständige Liste der detaillierten Anforderungen finden Sie unter [Azure Stack Hub-PKI-Zertifikatanforderungen](azure-stack-pki-certs.md).  

   ![Beispielzertifikat](media/event-hubs-rp-prerequisites/certificate-example.png)

   > [!NOTE]
   > **PFX-Dateien müssen kennwortgeschützt sein**. Das Kennwort wird später während der Installation angefordert.

2. Lesen Sie unbedingt [Überprüfen von Azure Stack Hub-PKI-Zertifikaten](azure-stack-validate-pki-certs.md). In diesem Artikel erfahren Sie, wie Sie die Zertifikate vorbereiten und überprüfen, die Sie für den Event Hubs-Ressourcenanbieter verwenden. 

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Schritt [installieren Sie den Event Hubs-Ressourcenanbieter](event-hubs-rp-install.md).
