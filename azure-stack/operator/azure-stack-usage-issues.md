---
title: Verbindungsprobleme und -fehler bei der Nutzung in Azure Stack Hub
description: Problembehandlung von Problemen und Fehlern bei der Azure Stack Hub-Nutzung.
author: sethmanheim
ms.topic: article
ms.date: 01/22/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 06/27/2019
ms.openlocfilehash: 4a847578e73c5c9b518669a46340590242513686
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "79512348"
---
# <a name="usage-connectivity-errors"></a>Verbindungsfehler bei der Nutzung

Azure Stack Hub-Nutzungsdaten werden von der [*Azure-Bridge*](azure-stack-usage-reporting.md)-Komponente in Azure Stack Hub an Azure gesendet. Wenn die Bridge innerhalb von Azure Stack Hub keine Verbindung mit dem Azure-Nutzungsdienst herstellen kann, wird der folgende Fehler angezeigt:

![Bridge-Nutzungsfehler](media/azure-stack-usage-issues/usageerror2.png)

Das Fenster enthält möglicherweise weitere Informationen zum Fehler und der Behebung:

![Fehlerbehebung](media/azure-stack-usage-issues/usageerror3.png)

## <a name="resolve-connectivity-issues"></a>Lösen von Konnektivitätsproblemen

Führen Sie die folgenden Schritte aus, um das Problem zu beheben:

- Stellen Sie sicher, dass die Netzwerkkonfiguration der Azure-Bridge das Verbinden mit dem Remotedienst gestattet.

- Wechseln Sie zum Blatt [**Regionsverwaltung** > **Eigenschaften**](azure-stack-registration.md#verify-azure-stack-hub-registration), um die für die Registrierung verwendete Azure-Abonnement-ID, die Ressourcengruppe und den Namen der Registrierungsressource herauszufinden. Stellen Sie sicher, dass die Registrierungsressource unter der richtigen Azure-Abonnement-ID im Azure-Portal vorhanden ist. Zu diesem Zweck wechseln Sie zu **Alle Ressourcen**, erstellt unter der Azure-Abonnement-ID, und aktivieren Sie das Feld **Ausgeblendete Typen anzeigen**. Wenn Sie die Registrierungsressource nicht finden können, sollten Sie die Schritte unter [Erneuern oder Ändern der Registrierung](azure-stack-registration.md#renew-or-change-registration) ausführen, um Ihre Azure Stack Hub-Instanz erneut zu registrieren.

  ![Portal](media/azure-stack-usage-issues/stackres.png)

## <a name="error-codes"></a>Fehlercodes

In diesem Abschnitt werden die Nutzungsfehlercodes beschrieben.

| Fehlercode                 | Problem                                                                                                                                             | Wiederherstellung                                                                                                                                                                                                                                                                                        |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| NetworkError               | Die Azure Stack Hub-Bridge kann keine Anforderung an den Nutzungsdienst-Endpunkt in Azure senden.                                                            | Überprüfen Sie, ob ein Proxy den Zugriff auf den Nutzungsdienst-Endpunkt blockiert oder abfängt.                                                                                                                                                                                                             |
| RequestTimedOut            | Eine Anforderung wurde von der Azure-Bridge gesendet, aber der Nutzungsdienst in Azure konnte nicht innerhalb des Timeout-Zeitlimits antworten.                             | Überprüfen Sie, ob ein Proxy den Zugriff auf den Nutzungsdienst-Endpunkt blockiert oder abfängt.                                                                                                                                                                                                                        |
| LoginError                 | Die Authentifizierung bei Microsoft Azure Active Directory ist nicht möglich.                                                                                                             | Stellen Sie sicher, dass von allen XRP-VMs in Azure Stack Hub auf den Azure AD-Anmeldungsendpunkt zugegriffen werden kann.                                                                                                                                                                                                                     |
| CertificateValidationError | Die Azure-Bridge kann die Anforderung nicht senden, weil sie sich nicht beim Azure-Dienst authentifizieren kann.                                    | Überprüfen Sie, ob ein Proxy HTTPS-Datenverkehr zwischen dem Azure Stack Hub-XRP-Computer und dem Nutzungsgateway-Endpunkt abfängt.                                                                                                                                                                                      |
| Nicht autorisiert               | Die Azure-Bridge kann keine Daten per Push an den Nutzungsdienst in Azure übertragen, weil der Azure-Dienst die Azure Stack Hub-Bridge nicht authentifizieren kann. | Überprüfen Sie, ob die Registrierungsressource geändert wurde, und wenn dies der Fall ist, registrieren Sie Azure Stack Hub erneut. <br><br> Manchmal kann ein Zeitsynchronisierungsproblem zwischen Azure Stack Hub und Azure AD diesen Fehler verursachen. Stellen Sie in diesem Fall sicher, dass die Zeiten auf den XRP-VMs in Azure Stack Hub mit Azure AD synchronisiert sind. |
|                            |                                                                                                                                                   |                                                                                                                                                                                                                                                                                                    |

Zusätzlich müssen Sie eventuell die Protokolldateien für die Azure-Bridge-, WAS- und WASPublic-Komponenten bereitstellen.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Melden von Azure Stack Hub-Nutzungsdaten an Azure](azure-stack-usage-reporting.md).
- Wie Sie ggf. in Ihrem Registrierungsprozess ausgelöste Fehlermeldungen überprüfen, erfahren Sie unter [Fehlermeldungen bei der Mandantenregistrierung](azure-stack-registration-errors.md).
- Weitere Informationen zur [Nutzungsberichtsinfrastruktur für Cloudlösungsanbieter](azure-stack-csp-ref-infrastructure.md).
