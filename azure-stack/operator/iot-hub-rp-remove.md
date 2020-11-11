---
title: Entfernen von IoT Hub in Azure Stack Hub
description: Hier finden Sie Anweisungen zum Deinstallieren von IoT Hub in Azure Stack Hub.
author: yiyiguo
ms.author: yiygu
ms.service: azure-stack
ms.topic: how-to
ms.date: 1/6/2020
ms.openlocfilehash: 4095336f6a18441512e8c1052051d6e1608d904a
ms.sourcegitcommit: 0e3296fb27b9dabbc2569bf85656c4c7b1d58ba9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050305"
---
# <a name="how-to-remove-iot-hub-on-azure-stack-hub"></a>Entfernen von IoT Hub in Azure Stack Hub

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

In diesem Artikel finden Sie Anweisungen zum Entfernen des IoT Hub-Ressourcenanbieters in Azure Stack Hub. Dieser Prozess dauert etwa 37 Minuten.

> [!WARNING]
> Sobald IoT Hub deinstalliert wurde, werden **_alle Geräte und Daten gelöscht_**. Der Vorgang ist **NICHT** wiederherstellbar.

## <a name="uninstalling-iot-hub"></a>Deinstallieren von IoT Hub

1) Navigieren Sie zur **Marketplace-Verwaltung**. IoT Hub sollte in der Liste enthalten und als installiert gekennzeichnet sein. Klicken Sie auf **IoT Hub**.

    [![Liste der Ressourcenanbieter](../operator/media/iot-hub-rp-remove/uninstall1.png)](../operator/media/iot-hub-rp-remove/uninstall1.png#lightbox)

2) Klicken Sie unter **IoT Hub** auf **Deinstallieren** , geben Sie den Ressourcenanbieternamen **microsoft.iothub** an, und klicken Sie dann darunter auf **Deinstallieren**.

    [![Deinstallieren von IoT Hub und anschließendes Bestätigen](../operator/media/iot-hub-rp-remove/uninstall2.png)](../operator/media/iot-hub-rp-remove/uninstall2.png#lightbox)

3) Warten Sie, bis die Deinstallation abgeschlossen ist. Warten Sie mindestens 10 Minuten, bevor Sie nochmal versuchen, IoT Hub zu installieren.

>[!IMPORTANT]
>Die Abhängigkeiten (z. B. Event Hubs) werden **NICHT** deinstalliert. Wenn Sie Abhängigkeiten aus dem Marketplace entfernen/deinstallieren möchten, müssen Sie dies separat durchführen.

## <a name="next-steps"></a>Nächste Schritte

Informationen zur erneuten Installation finden Sie unter [Installieren des IoT Hub-Ressourcenanbieters in Azure Stack Hub](iot-hub-rp-install.md).
