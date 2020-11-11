---
title: Übersicht über IoT Hub in Azure Stack Hub
description: Hier erfahren Sie mehr über den IoT Hub-Ressourcenanbieter in Azure Stack Hub.
author: yiyiguo
ms.author: yiygu
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/12/2019
ms.openlocfilehash: 098168947a28214a3955a1961c80a2c06825126b
ms.sourcegitcommit: 296c95cad20ed62bdad0d27f1f5246bfc1c81d5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93064717"
---
# <a name="overview-of-iot-hub-on-azure-stack-hub"></a>Übersicht über IoT Hub in Azure Stack Hub

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

Mit IoT Hub in Azure Stack Hub können Sie hybride IoT-Lösungen erstellen. IoT Hub ist ein verwalteter Dienst, der als zentraler Nachrichtenhub für die bidirektionale Kommunikation zwischen Ihrer IoT-Anwendung und den Geräten dient, die von der Anwendung verwaltet werden. Sie können IoT Hub in Azure Stack Hub verwenden, um IoT-Lösungen mit zuverlässiger und sicherer Kommunikation zwischen IoT-Geräten und einer lokalen Back-End-Lösung zu erstellen.

## <a name="features"></a>Features

| Funktion | IoT Hub in Azure | IoT Hub in der Azure Stack Hub-Vorschau (S2/S3) |
|-|-|-|
|Gerät-zu-Cloud-Telemetrie| ✔ | ✔ |
|Senden von Nachrichten aus der Cloud an Geräte| ✔ | ✔ |
|Gerätebasierte Identität| ✔ | ✔ |
|Nachrichtenrouting <sub>1</sub><sub>, 4</sub>| ✔ | ✔ |
|HTTP-, AMQP- und MQTT-Protokolle| ✔ | ✔ |
|Mehrinstanzenfähigkeit| ✔ | ✔ |
|Überwachung und Diagnose| ✔ | ✔ |
|Senden von Nachrichten aus der Cloud an Geräte| ✔ | ✔ |
|Geräteverwaltung, Gerätezwilling, Modulzwilling| ✔ | ✔ |
|Zwillingsbenachrichtigungen, Ereignisse im Gerätelebenszyklus| ✔ | ✔ |
|Mehrstufige Bereitstellung am Edge| ✔ | Bald verfügbar |
|Administratorportal <sub>2</sub>| ✘ | ✔ |
|Rotation für Geheimnisse <sub>2</sub>| ✘ | ✔ |
|Kapazitätsverwaltung <sub>2</sub>| ✘ | ✔ |
|Sicherung und Wiederherstellung <sub>3</sub>| ✘ | ✘ |
|DeviceConnected, DeviceDisconnected, ASC <sub>4</sub>| ✔ | ✘ |
|Gerätemodulkonfiguration| ✔ | Bald verfügbar |
|Gerätestreaming, IoT Plug & Play, Aufträge, Dateiuploads <sub>5</sub>| ✔ | ✘ |
|Überwachen des Verbindungsstatus von Geräten mit Event Grid <sub>4</sub>| ✔ | ✘ |
|Failover <sub>6</sub>| ✔ | ✘ |

<sub>1</sub> Diese Features sind auf integrierte Endpunkte, Event Hubs und Speicher beschränkt. Service Bus ist in Azure Stack Hub nicht verfügbar.  
<sub>2</sub> Diese Features sind für die Verwaltung von IoT Hub in Azure Stack Hub durch Operatoren vorgesehen.  
<sub>3</sub> Die Sicherung ist im Vorschaurelease verfügbar. Die Wiederherstellung wird in der allgemeinen Verfügbarkeit unterstützt.  
<sub>4</sub> Diese Features sind von anderen Diensten abhängig, die noch nicht in Azure Stack Hub verfügbar sind.  
<sub>5</sub> Diese Features sind in der Roadmap für Azure Stack Hub geplant.  
<sub>6</sub> Gilt nicht für Azure Stack Hub  

## <a name="api-available-for-iot-hub-on-azure-stack-hub"></a>Für IoT Hub in Azure Stack Hub verfügbare APIs

|APIs|IoT Hub in Azure Stack Hub|
|-|-|
|Apply Configuration On Device| ✔ |
| Configuration Create | ✔ |
| Configuration Delete | ✔ |
| Configuration Read | ✔ |
|Configuration Read Many| ✔ |
|Configuration Service Apply|  ✔ |
|Konfigurationsaktualisierung|  ✔ |
|Device Direct Invoke Method|  ✔ |
|GetDeviceAndModuleInScope|  ✔ |
|GetDevicesAndModulesInScope| ✔ |
|Unregister Device| ✔ |
|Get Devices| ✔ |
|Update Module Twin| ✔ |
|D2C Get Twin| ✔ |
|Dient zum Importieren von Geräten.| ✔ |
|Get Twin| ✔ |
|Unregister Module| ✔ |
|Gerät aktualisieren| ✔ |
|Update Module| ✔ |
|Query Devices| ✔ |
|Dient zum Exportieren von Geräten.| ✔ |
|Back up and Restore – ADM| ✔ |
|Replace Twin| ✔ |
|Back up and Restore – DSS| ✔ |
|D2C Notify DesiredProperties| ✔ |
|D2C Patch ReportedProperties| ✔ |
|Get Module Twin| ✔ |
|Module D2C Get Twin| ✔ |
|Modul abrufen| ✔ |
|Module D2C Notify DesiredProperties| ✔ |
|Module D2C Patch ReportedProperties| ✔ |
|Module Direct Invoke Method| ✔ |
|Update Twin| ✔ |
|Bulk Device Operations| ✔ |
|Device to Cloud Telemetry| ✔ |
|Register Device| ✔ |
|Register Module| ✔ |
|Replace Module Twin| ✔ |
|GenericAuthentication| ✔ |
|Get Device| ✔ |
|Partition Move/Role Change| ✔ |

## <a name="differences-between-iot-hub-on-azure-cloud-and-iot-hub-on-azure-stack"></a>Unterschiede zwischen IoT Hub in Azure Cloud und IoT Hub in Azure Stack

| Aspekt | IoT Hub in Cloud | IoT Hub in Stack |
|-|-|-|
| Nachrichtenverarbeitung | https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin |Standardmäßig werden Nachrichten an den integrierten dienstseitigen Endpunkt (messages/events) gesendet, der mit Event Hubs kompatibel ist. In Azure Cloud können Sie auf die Nachrichten vom Endpunkt zugreifen, indem Sie entweder die IoT Hub-Verbindungszeichenfolge oder die Event Hub-Verbindungszeichenfolge angeben. In Azure Stack Hub wird jedoch nur die Event Hub-Verbindungszeichenfolge unterstützt. |

## <a name="next-steps"></a>Nächste Schritte

Wenn IoT Hub in Ihrem Abonnement nicht verfügbar ist, wenden Sie sich an Ihren Administrator, um [den IoT Hub-Ressourcenanbieter in Azure Stack Hub zu installieren](../operator/iot-hub-rp-overview.md).

Informationen zur Verwendung von IoT Hub finden Sie in der [Azure IoT Hub-Dokumentation](/azure/iot-hub/).

