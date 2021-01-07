---
title: Arbeitsstation mit privilegiertem Zugriff und privilegierter Endpunktzugriff
description: Hier erfahren Sie mehr über die Arbeitsstation mit privilegiertem Zugriff und den privilegierten Endpunktzugriff.
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 9eef242f6e422cdba3b64927d6bfda93fab530b3
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97874522"
---
# <a name="privileged-access-workstation-and-privileged-endpoint-access"></a>Arbeitsstation mit privilegiertem Zugriff und privilegierter Endpunktzugriff

## <a name="overview"></a>Übersicht

Für diesen Vorgang müssen Sie eine Verbindung mit der Arbeitsstation mit privilegiertem Zugriff (PAW) herstellen. Der Kunde muss Ihnen die Möglichkeit geben, mithilfe einer Remotedesktopverbindung eine Verbindung mit der PAW herzustellen.

## <a name="configuring-the-winrm"></a>Konfigurieren von WinRM

Stellen Sie sicher, dass die IP-Adressen des privilegierten Endpunkts wie im Azure Stack Hub-Verwaltungsportal definiert als vertrauenswürdiger Host für die PAW festgelegt sind, um Verbindungen mit dem privilegierten Endpunkt der PAW zuzulassen. Die Anweisungen zum Abrufen dieser IP-Adressen aus dem Verwaltungsportal finden Sie unter „Überprüfen des Zugriffs und der Integrität von Skalierungseinheitknoten“ auf Seite 16.

Starten Sie eine PowerShell-Sitzung mit erhöhten Benutzerrechten, um die vertrauenswürdigen WinRM-Hosts anzuzeigen oder zu bearbeiten:

-   Anzeigen vertrauenswürdiger Hosts

Führen Sie in PowerShell Folgendes aus, um die aktuellen vertrauenswürdigen Hosts anzuzeigen:

-   Bearbeiten vertrauenswürdiger Hosts

Wenn die ERCS-IPs (Emergency Recovery Console Server) nicht vorhanden sind, führen Sie Folgendes aus, um einen neuen Wert für vertrauenswürdige Hosts festzulegen. Ersetzen Sie *\<ERCS01_IP\*, *\<ERCS02_IP\* und *\<ERCS03_IP\* durch die drei IPs für privilegierte Endpunkte, die im Azure Stack Hub-Verwaltungsportal definiert sind:

## <a name="connect-to-the-privileged-endpoint"></a>Herstellen einer Verbindung mit dem privilegierten Endpunkt

Öffnen Sie in der PAW eine PowerShell-Sitzung mit erhöhten Rechten, und führen Sie die folgenden beiden Befehle aus. Ersetzen Sie *\<ERCS_IP\* wie zuvor beschrieben durch eine IP-Adresse von einer der privilegierten Endpunktinstanzen. Wenn Sie dazu aufgefordert werden, geben Sie die vom Kunden angegebenen Anmeldeinformationen für den privilegierten Endpunkt (PEP) ein.

## <a name="close-the-privileged-endpoint"></a>Beenden der Sitzung mit dem privilegierten Endpunkt

Führen Sie Folgendes aus, um die Sitzung für den privilegierten Endpunkt zu beenden:

## <a name="further-reading"></a>Weitere nützliche Informationen

Weitere Informationen zum Herstellen einer Verbindung mit dem privilegierten Endpunkt und zur Arbeit mit diesem finden Sie unter [Verwenden des privilegierten Endpunkts in Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint)
[Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint).
