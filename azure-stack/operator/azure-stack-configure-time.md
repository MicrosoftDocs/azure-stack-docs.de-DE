---
title: Konfigurieren des Zeitservers für Azure Stack Hub | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie den Zeitserver für Azure Stack Hub konfigurieren.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 10/10/2019
ms.openlocfilehash: 2a473ab2b44362b7fc93cdd1879b1869f469ddfa
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76534055"
---
# <a name="configure-the-time-server-for-azure-stack-hub"></a>Konfigurieren des Zeitservers für Azure Stack Hub

Sie können den privilegierten Endpunkt (PEP) verwenden, um den Zeitserver in Azure Stack Hub zu aktualisieren. Verwenden Sie einen Hostnamen, der in mindestens zwei IP-Adressen von NTP-Servern aufgelöst wird.

Azure Stack Hub verwendet NTP (Network Time Protocol) zum Herstellen einer Verbindung mit Zeitservern im Internet. NTP-Server stellen eine genaue Systemzeit bereit. Die Uhrzeit wird auf physischen Netzwerkswitches, auf dem Hardwarelebenszyklus-Host, im Infrastrukturdienst und auf virtuellen Computern von Azure Stack Hub verwendet. Wird die Uhr nicht synchronisiert, treten in Azure Stack Hub unter Umständen schwerwiegende Netzwerk- und Authentifizierungsprobleme auf. Protokolldateien, Dokumente und andere Dateien werden möglicherweise mit falschen Zeitstempeln erstellt.

Die Angabe eines Zeitservers (NTP) ist erforderlich, damit Azure Stack Hub die Uhrzeit synchronisieren kann. Bei der Bereitstellung von Azure Stack Hub geben Sie die Adresse eines NTP-Servers an. Die Uhrzeit ist ein kritischer Rechenzentrumsinfrastruktur-Dienst. Ändert sich der Dienst, müssen Sie die Uhrzeit aktualisieren.

> [!NOTE]
> Azure Stack Hub unterstützt die Synchronisierung der Uhrzeit nur mit einem Zeitserver (NTP). Sie können nicht mehrere NTPs bereitstellen, mit denen Azure Stack Hub die Uhrzeit synchronisiert.

## <a name="configure-time"></a>Konfigurieren der Uhrzeit

1. [Stellen Sie eine Verbindung mit dem PEP her.](azure-stack-privileged-endpoint.md) 
    > [!Note]  
    > Es ist nicht erforderlich, den privilegierten Endpunkt zu entsperren, indem Sie ein Supportticket erstellen.

2. Führen Sie den folgenden Befehl aus, um den aktuellen konfigurierten NTP-Server zu überprüfen:

    ```PowerShell
    Get-AzsTimeSource
    ```

3. Führen Sie den folgenden Befehl zum Aktualisieren von Azure Stack Hub aus, um den neuen NTP-Server zu verwenden und sofort die Uhrzeit zu synchronisieren.

    > [!Note]  
    > Mit diesem Verfahren wird nicht der Zeitserver auf den physischen Switches aktualisiert.

    ```PowerShell
    Set-AzsTimeSource -TimeServer NEWTIMESERVERIP -resync
    ```

4. Überprüfen Sie die Ausgabe des Befehls auf Fehler.


## <a name="next-steps"></a>Nächste Schritte

[Anzeigen des Bereitschaftsberichts](azure-stack-validation-report.md)  
[Allgemeine Überlegungen zur Azure Stack Hub-Integration](azure-stack-datacenter-integration.md)  
