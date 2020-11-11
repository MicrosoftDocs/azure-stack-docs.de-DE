---
title: Voraussetzungen für die Installation von IoT Hub in Azure Stack Hub
description: Hier erfahren Sie mehr über die erforderlichen Voraussetzungen für die Installation des IoT Hub-Ressourcenanbieters in Azure Stack Hub.
author: yiyiguo
ms.author: yiygu
ms.service: azure-stack
ms.topic: how-to
ms.date: 1/6/2020
ms.openlocfilehash: f194ef78a31722a05742b14b312ea4aad58d3ed3
ms.sourcegitcommit: 0e3296fb27b9dabbc2569bf85656c4c7b1d58ba9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050307"
---
# <a name="prerequisites-for-installing-iot-hub-on-azure-stack-hub"></a>Voraussetzungen für die Installation von IoT Hub in Azure Stack Hub

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

Die folgenden Voraussetzungen müssen erfüllt sein, bevor Sie IoT Hub in Azure Stack Hub installieren können. **Möglicherweise sind mehrere Tage oder Wochen an Vorlaufzeit erforderlich** , um alle Schritte auszuführen.

> [!IMPORTANT]
> Für diese Voraussetzungen wird vorausgesetzt, dass Sie bereits mindestens ein integriertes Azure Stack Hub-System mit 4 Knoten und **Buildnummer 1.2005.6.53** oder höher bereitgestellt haben. Der IoT Hub-Ressourcenanbieter wird nicht im ASDK (Azure Stack Hub Development Kit) unterstützt.

## <a name="common-prerequisites"></a>Allgemeine Voraussetzungen

[!INCLUDE [Common RP prerequisites](../includes/resource-provider-prerequisites.md)]

## <a name="dependency-prerequisites"></a>Abhängigkeitsvoraussetzungen

1. Sie müssen [Event Hubs aus dem Marketplace herunterladen und installieren](event-hubs-rp-install.md). Die Bereitstellung von Event Hubs muss stattfinden, BEVOR die Bereitstellung von IoT Hub gestartet wird.
2. Laden Sie die folgenden abhängigen Elemente aus dem Marketplace herunter, bevor Sie das IoT Hub-Paket herunterladen, um einen schnelleren Download und eine schnellere Installation von IoT Hub zu erzielen. Andernfalls versucht die IoT Hub-Bereitstellung, die abhängigen Pakete herunterzuladen:
    * Benutzerdefinierte Skripterweiterung
    * PowerShell Desired State Configuration
    * Kostenlose Lizenz: SQL Server 2016 SP2 Express unter Windows Server 2016
    * SQL-IaaS-Erweiterung
    * Azure Stack Add-On RP Windows Server
3. Warten Sie nach erfolgreicher Installation von Event Hubs 10 Minuten, bevor Sie mit der Bereitstellung von IoT Hub fortfahren.

## <a name="certificate-requirements"></a>Zertifikatanforderungen

1. Beschaffen Sie ein PKI-TLS/SSL-Zertifikate (Public Key-Infrastruktur) für Event Hubs. Der alternative Antragstellername muss das folgende Namensmuster aufweisen: `CN=*.mgmtiothub.<region>.<fqdn>`.

   Der Antragstellername kann angegeben werden, wird aber nicht von IoT Hub zum Verarbeiten von Zertifikaten verwendet. Nur der alternative Antragstellername wird verwendet. Die vollständige Liste der detaillierten Anforderungen finden Sie unter [Azure Stack Hub-PKI-Zertifikatanforderungen](azure-stack-pki-certs.md).

   ![IoT Hub-Beispielzertifikat](media\iot-hub-rp-prerequisites\certificate.png)

2. Lesen Sie unbedingt [Überprüfen von Azure Stack Hub-PKI-Zertifikaten](azure-stack-validate-pki-certs.md). In diesem Artikel erfahren Sie, wie Sie die Zertifikate vorbereiten und überprüfen, die Sie für den IoT Hub-Ressourcenanbieter verwenden. 

## <a name="dns-configuration-requirements"></a>DNS-Konfigurationsanforderungen
 
Damit IoT Hub ordnungsgemäß im Netzwerk in Azure Stack Hub funktioniert, muss der Netzwerkadministrator das DNS konfigurieren. Suchen Sie im DNS-Verwaltungstool nach der Einstellung für die bedingte DNS-Weiterleitung, und fügen Sie eine bedingte Weiterleitungsregel hinzu, um Datenverkehr für `<region>.cloudapp.<externaldomainname>` zuzulassen. Beispiel: `ussouth.cloudapp.contoso.com`.

## <a name="next-steps"></a>Nächste Schritte

Als Nächstes installieren Sie den IoT Hub-Ressourcenanbieter in einer [verbundenen Azure Stack-Instanz](iot-hub-rp-install.md).
