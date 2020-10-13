---
title: Ändern der Datenschutzeinstellungen
description: In diesem Thema erfahren Sie, wie Sie Datenschutzeinstellungen im Azure Stack HCI-Betriebssystem oder in Windows Server ändern.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 10/05/2020
ms.openlocfilehash: 8055e02b702fb5e585b5570171c6dabb247b623f
ms.sourcegitcommit: 79be77eb2bebf314d956258fc9bc14b2014be190
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2020
ms.locfileid: "91745164"
---
# <a name="change-privacy-settings-on-individual-servers"></a>Ändern der Datenschutzeinstellungen auf einzelnen Servern

>Gilt für: Azure Stack HCI (Version 20H2), Windows Server 2019, Windows Server 2016

Hier erfahren Sie, wie Sie die Datenschutzeinstellungen auf einem Server unter dem Azure Stack HCI-Betriebssystem oder unter Windows Server ändern. Informationen dazu, wie Sie Datenschutzeinstellungen per Gruppenrichtlinie auf mehreren Servern gleichzeitig oder im gesamten Unternehmen verwalten, finden Sie unter [Verwalten von Enterprise-Diagnosedaten](/windows/privacy/configure-windows-diagnostic-data-in-your-organization#manage-enterprise-diagnostic-data).

## <a name="azure-stack-hci-or-server-core"></a>Azure Stack HCI oder Server Core
Wenn auf dem Server entweder das Azure Stack HCI-Betriebssystem oder Windows Server mit der Server Core-Installationsoption verwendet wird, führen Sie die folgenden Schritte aus:
1. Stellen Sie eine Verbindung mit einem Server im Azure Stack HCI-Cluster her. Verwenden Sie hierzu Remotedesktop, einen Controller für die Remoteverwaltung (monitorlos oder BMC) oder KVM, oder melden Sie sich lokal unter Verwendung einer Tastatur und eines Monitors an. 
1. Wenn Sie eine Verbindung mit einem Server unter Azure Stack HCI herstellen, wird automatisch das Serverkonfigurationstool (Sconfig) geöffnet. Wenn Sie eine Verbindung mit einem Server unter Windows Server mit Server Core herstellen, geben Sie an der Eingabeaufforderung `Sconfig` ein.
1. Geben Sie an der Eingabeaufforderung **Enter a number to select an option:** (Geben Sie eine Zahl ein, um eine Option auszuwählen:) die Zahl **10** ein, und drücken Sie die EINGABETASTE.
1. Wählen Sie für die Bestätigungsaufforderung **Change Telemetry** (Telemetrie ändern) die Option **Yes** (Ja) aus, um die folgenden Optionen anzuzeigen:

    Verfügbare Telemetrieeinstellungen: **1 Security** (1 Sicherheit), **2 Basic** (2 Einfach), **3 Enhanced** (3 Erweitert), **4 Full** (4 Vollständig)

    >[!NOTE]
    > Die Standardeinstellung für Azure Stack HCI ist **1 Security** (1 Sicherheit).

1. Geben Sie an der Eingabeaufforderung **Enter new telemetry setting:** (Neue Telemetrieeinstellung eingeben:) die gewünschte Option ein, und drücken Sie die EINGABETASTE.

## <a name="full-desktop"></a>„Full Desktop“ (Vollständiger Desktop)
Gehen Sie bei einem Server mit Windows Server und der Installationsoption „Full Desktop“ (Vollständiger Desktop) wie folgt vor:
1. Stellen Sie eine Verbindung mit dem Server-Manager-Dashboard von Windows Server her.

    Sie können mithilfe einer Tastatur und eines Monitors lokal eine Verbindung herstellen. Alternativ können Sie einen Controller für die Remoteverwaltung (monitorlos oder BMC) oder Remotedesktop verwenden. 

1. Wählen Sie im Server-Manager unter **Dashboard** die Option **Lokaler Server** aus.
1. Wählen Sie auf der Seite **Eigenschaften** des Servers neben **Feedback und Diagnose** die Option **Einstellungen** aus.

    Auf der Seite **Einstellungen** werden die Einstellungen **Feedbackhäufigkeit** und **Diagnose- und Nutzungsdaten** angezeigt. 
 
1. Erweitern Sie die Einstellung **Diagnose- und Nutzungsdaten**, um eine der folgenden Optionen auszuwählen:
    - **Erforderliche Diagnosedaten**
    - **Erweitert**
    - **Optionale Diagnosedaten**

    >[!NOTE]
    > Wenn auf der Seite **Einstellungen** der Hinweis **Einige Einstellungen werden von Ihrer Organisation verwaltet.** angezeigt wird, ist die Einstellung **Diagnose- und Nutzungsdaten** möglicherweise nicht verfügbar.

## <a name="next-steps"></a>Nächste Schritte
Informationen dazu, wie Sie Datenschutzeinstellungen per Gruppenrichtlinie auf mehreren Servern gleichzeitig oder im gesamten Unternehmen verwalten, finden Sie hier:
-   [Verwalten von Enterprise-Diagnosedaten](/windows/privacy/configure-windows-diagnostic-data-in-your-organization#manage-enterprise-diagnostic-data)
