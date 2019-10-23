---
title: Konfigurieren des Zeitservers für Azure Stack | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie den Zeitserver für Azure Stack konfigurieren.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 10/10/2019
ms.openlocfilehash: cc432538715c1c990a9efe6473b33303deb78734
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2019
ms.locfileid: "72280553"
---
# <a name="configure-the-time-server-for-azure-stack"></a>Konfigurieren des Zeitservers für Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*  

Sie können den privilegierten Endpunkt (PEP) verwenden, um den Zeitserver in Azure Stack zu aktualisieren. Verwenden Sie einen Hostnamen, der in mindestens zwei IP-Adressen von NTP-Servern aufgelöst wird.

Azure Stack verwendet NTP (Network Time Protocol) zum Herstellen einer Verbindung mit Zeitservern im Internet. NTP-Server stellen eine genaue Systemzeit bereit. Die Uhrzeit wird auf physischen Netzwerkswitches, auf dem Hardwarelebenszyklus-Host, im Infrastrukturdienst und auf virtuellen Computern von Azure Stack verwendet. Wird die Uhr nicht synchronisiert, treten in Azure Stack unter Umständen schwerwiegende Netzwerk- und Authentifizierungsprobleme auf. Protokolldateien, Dokumente und andere Dateien werden möglicherweise mit falschen Zeitstempeln erstellt.

Mindestens ein Zeitserver (NTP) ist erforderlich, damit Azure Stack die Uhrzeit synchronisieren kann. Bei der Bereitstellung von Azure Stack geben Sie die Adresse eines NTP-Servers an. Die Uhrzeit ist ein kritischer Rechenzentrumsinfrastruktur-Dienst. Ändert sich der Dienst, müssen Sie die Uhrzeit aktualisieren.

## <a name="configure-time"></a>Konfigurieren der Uhrzeit

1. [Stellen Sie eine Verbindung mit dem PEP her.](azure-stack-privileged-endpoint.md) 
    > [!Note]  
    > Es ist nicht erforderlich, den privilegierten Endpunkt zu entsperren, indem Sie ein Supportticket erstellen.

2. Führen Sie den folgenden Befehl aus, um den aktuellen konfigurierten NTP-Server zu überprüfen:

    ```PowerShell
    Get-AzsTimeSource
    ```

3. Führen Sie den folgenden Befehl zum Aktualisieren von Azure Stack aus, um den neuen NTP-Server zu verwenden und sofort die Uhrzeit zu synchronisieren.

    > [!Note]  
    > Mit diesem Verfahren wird nicht der Zeitserver auf den physischen Switches aktualisiert.

    ```PowerShell
    Set-AzsTimeSource -TimeServer NEWTIMESERVERIP -resync
    ```

4. Überprüfen Sie die Ausgabe des Befehls auf Fehler.


## <a name="next-steps"></a>Nächste Schritte

[Anzeigen des Bereitschaftsberichts](azure-stack-validation-report.md)  
[Allgemeine Überlegungen zur Azure Stack-Integration](azure-stack-datacenter-integration.md)  
