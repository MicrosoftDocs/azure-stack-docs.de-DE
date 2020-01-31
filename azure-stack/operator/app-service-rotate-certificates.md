---
title: Rotieren von Geheimnissen und Zertifikaten in App Service in Azure Stack Hub
description: Hier erhalten Sie Informationen zum Rotieren von Geheimnissen und Zertifikaten, die von Azure App Service in Azure Stack Hub verwendet werden.
author: BryanLa
ms.topic: article
ms.date: 01/10/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/10/2020
ms.openlocfilehash: f1a42f5b04ea83d9ff9130fb63ba6833cd7d2914
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76876540"
---
# <a name="rotate-app-service-on-azure-stack-hub-secrets-and-certificates"></a>Rotieren von Geheimnissen und Zertifikaten in App Service in Azure Stack Hub

Diese Anweisungen gelten nur für Azure App Service in Azure Stack Hub.  Das Rotieren von Geheimnissen in Azure App Service in Azure Stack Hub ist nicht Teil des zentralisierten Rotationsverfahrens von Geheimnissen für Azure Stack Hub.  Bediener können die Gültigkeit von Geheimnissen innerhalb des Systems, das Datum der letzten Aktualisierung und die verbleibende Zeit bis zum Ablauf der Geheimnisse überwachen.

> [!Important]
> Die Bediener erhalten keine Warnungen zum Ablauf von Geheimnissen im Azure Stack Hub-Dashboard, da Azure App Service in Azure Stack Hub nicht in den Azure Stack Hub-Warnungsdienst integriert ist.  Die Bediener müssen die Geheimnisse regelmäßig mit den Verwaltungsfunktionen für Azure App Service in Azure Stack Hub im Azure Stack Hub-Administratorportal überwachen.

Dieses Dokument enthält die Vorgehensweise zum Rotieren der folgenden Geheimnisse:

* Verschlüsselungsschlüssel, die in Azure App Service in Azure Stack Hub verwendet werden
* Anmeldeinformationen für Datenbankverbindungen, die von Azure App Service in Azure Stack Hub für die Interaktion mit den Hosting- und Messungsdatenbanken verwendet werden
* Zertifikate, die von Azure App Service in Azure Stack Hub zum Sichern von Endpunkten verwendet werden
* Systemanmeldeinformationen für Rollen in Azure App Service in Azure Stack Hub

## <a name="rotate-encryption-keys"></a>Rotieren von Verschlüsselungsschlüsseln

Führen Sie zum Rotieren der Verschlüsselungsschlüssel, die in Azure App Service in Azure Stack Hub verwendet werden, die folgenden Schritte aus:

1. Öffnen Sie die Sie App Service-Verwaltungsfunktionen im Azure Stack Hub-Administratorportal.

1. Navigieren Sie zur Menüoption **Geheimnisse**.

1. Klicken Sie im Abschnitt „Verschlüsselungsschlüssel“ auf die Schaltfläche **Rotieren**.

1. Klicken Sie auf **OK**, um die Rotation zu starten.

1. Die Verschlüsselungsschlüssel werden rotiert, und alle Rolleninstanzen werden aktualisiert. Bediener können den Status mithilfe der Schaltfläche **Status** überwachen.

## <a name="rotate-connection-strings"></a>Rotieren von Verbindungszeichenfolgen

Führen Sie die folgenden Schritte aus, um die Anmeldeinformationen für die Datenbank-Verbindungszeichenfolge für die Hosting- und Messungsdatenbanken für App Service zu aktualisieren:

1. Öffnen Sie die Sie App Service-Verwaltungsfunktionen im Azure Stack Hub-Administratorportal.

1. Navigieren Sie zur Menüoption **Geheimnisse**.

1. Klicken Sie im Abschnitt „Verbindungszeichenfolgen“ auf die Schaltfläche**Rotieren**.

1. Geben Sie den **SQL SA-Benutzernamen** und das **Kennwort** ein, und klicken Sie auf **OK**, um die Rotation zu starten. 

1. Die Anmeldeinformationen werden in allen Azure App Service-Rolleninstanzen rotiert. Bediener können den Status mithilfe der Schaltfläche **Status** überwachen.

## <a name="rotate-certificates"></a>Rotieren von Zertifikaten

Führen Sie zum Rotieren der Zertifikate, die in Azure App Service in Azure Stack Hub verwendet werden, die folgenden Schritte aus:

1. Öffnen Sie die Sie App Service-Verwaltungsfunktionen im Azure Stack Hub-Administratorportal.

1. Navigieren Sie zur Menüoption **Geheimnisse**.

1. Klicken Sie im Abschnitt „Zertifikate“ auf die Schaltfläche **Rotieren**.

1. Füllen Sie die Felder **Zertifikatsdatei** und **Kennwort** für die zu rotierenden Zertifikate aus, und klicken Sie auf **OK**.

1. Die Zertifikate werden in den Rolleninstanzen für Azure App Service in Azure Stack Hub wie erforderlich rotiert.  Bediener können den Status mithilfe der Schaltfläche **Status** überwachen.

## <a name="rotate-system-credentials"></a>Rotieren von Systemanmeldeinformationen

Führen Sie zum Rotieren der Systemanmeldeinformationen, die in Azure App Service in Azure Stack Hub verwendet werden, die folgenden Schritte aus:

1. Öffnen Sie die Sie App Service-Verwaltungsfunktionen im Azure Stack Hub-Administratorportal.

1. Navigieren Sie zur Menüoption **Geheimnisse**.

1. Klicken Sie im Abschnitt „Systemanmeldeinformationen“ auf die Schaltfläche **Rotieren**.

1. Wählen Sie den **Bereich** für die zu rotierenden Systemanmeldeinformationen aus.  Bediener können angeben, ob die Systemanmeldeinformationen für alle Rollen oder einzelne Rollen rotiert werden sollen.

1. Geben Sie einen Wert für **Benutzername des lokalen Administrators** und ein neues **Kennwort** ein, bestätigen Sie das **Kennwort**, und klicken Sie auf **OK**.

1. Die Anmeldeinformationen werden in den entsprechenden Rolleninstanzen für Azure App Service in Azure Stack Hub wie erforderlich rotiert.  Bediener können den Status mithilfe der Schaltfläche **Status** überwachen.



