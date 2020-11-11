---
title: Übersicht über IoT Hub in Azure Stack Hub
description: Hier erfahren Sie mehr über den IoT Hub-Ressourcenanbieter in Azure Stack Hub und über Unterschiede gegenüber der in Azure gehosteten Version von IoT Hub.
author: yiyiguo
ms.author: yiygu
ms.service: azure-stack
ms.topic: how-to
ms.date: 1/6/2020
ms.openlocfilehash: adf6635fbdcf62398218a8e6ad0ce061900e0059
ms.sourcegitcommit: 0e3296fb27b9dabbc2569bf85656c4c7b1d58ba9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050304"
---
# <a name="iot-hub-on-azure-stack-hub-overview"></a>Übersicht über IoT Hub in Azure Stack Hub

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

Mit IoT Hub in Azure Stack Hub können Sie hybride IoT-Lösungen erstellen. IoT Hub ist ein verwalteter Dienst, der als zentraler Nachrichtenhub für die bidirektionale Kommunikation zwischen Ihrer IoT-Anwendung und den Geräten dient, die von der Anwendung verwaltet werden. Sie können IoT Hub in Azure Stack Hub verwenden, um IoT-Lösungen mit zuverlässiger und sicherer Kommunikation zwischen IoT-Geräten und einer lokalen Back-End-Lösung zu erstellen. 

## <a name="differences-between-azure-iot-hub-and-iot-hub-on-azure-stack-hub"></a>Unterschiede zwischen Azure IoT Hub und IoT Hub in Azure Stack Hub

| Funktion | Azure IoT Hub | IoT Hub in Azure Stack Hub |
|-|-|-|
| Skalieren | https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling | Die Tarife S2 und S3 werden für IoT Hub unterstützt.|
| Dienstupdate | Automatic | Manuell |

## <a name="iot-hub-throttling"></a>IoT Hub-Drosselung

Die Drosselung und Kontingente für IoT Hub in den Azure Stack Hub-Tarifen S2 und S3 sind mit IoT Hub in Azure identisch. Weitere Informationen finden Sie unter [Kontingente und Drosselung von Azure IoT Hub](/azure/iot-hub/iot-hub-devguide-quotas-throttling#quotas-and-throttling).

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Installation von IoT Hub in Azure Stack Hub finden Sie unter [Voraussetzungen](iot-hub-rp-prerequisites.md).

Weitere Informationen über die Unterschiede zwischen einer verbundenen und einer nicht verbundenen Azure Stack Hub-Instanz finden Sie im Artikel zu [Azure Stack-Verbindungsmodellen](azure-stack-connection-models.md).
