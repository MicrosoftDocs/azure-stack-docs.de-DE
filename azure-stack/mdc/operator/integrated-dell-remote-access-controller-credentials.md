---
title: Anmeldeinformationen für den Integrated Dell Remote Access Controller – MDC
description: Erfahren Sie, wie Sie die Anmeldeinformationen für den Integrated Dell Remote Access Controller (iDRAC) in Modular Data Center (MDC) aktualisieren.
author: troettinger
ms.author: thoroet
ms.service: azure-stack
ms.topic: article
ms.date: 10/27/2020
ms.reviewer: justinha
ms.lastreviewed: 10/27/2020
ms.openlocfilehash: d0d2f522038d0788d4942db68b36e5bb21884619
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910736"
---
# <a name="update-credentials-for-the-integrated-dell-remote-access-controller---modular-data-center-mdc"></a>Aktualisieren der Anmeldeinformationen für den Integrated Dell Remote Access Controller (iDRAC) in Modular Data Center (MDC)

In diesem Abschnitt wird beschrieben, wie Sie die Anmeldeinformationen für den Integrated Dell Remote Access Controller (iDRAC) ändern. 

## <a name="prerequisites"></a>Voraussetzungen

Vor dem Ausführen der Prozedur: 

- Stellen Sie über den Remotedesktop eine Verbindung mit der MGMT-VM her. 
- Stellen Sie sicher, dass Sie über die neuen Anmeldeinformationen für das Konto oder die Konten verfügen. 
 
## <a name="update-the-idrac-credentials"></a>Aktualisieren der iDRAC-Anmeldeinformationen

So aktualisieren Sie die iDRAC-Anmeldeinformationen für alle PowerEdge-Server (HLH-Knoten und Skalierungseinheitenknoten) in der Umgebung:

1. Melden Sie sich in einem Webbrowser bei https://<iDRAC_IP> an. 
1. Navigieren Sie zu **iDRAC Settings** > **Users** (iDRAC-Einstellungen > Benutzer). 
1. Wählen Sie den Benutzer aus, den Sie ändern möchten, und klicken Sie dann auf **Edit** (Bearbeiten). 
1. Geben Sie, wie in der folgenden Abbildung dargestellt, im Fenster **Edit User** (Benutzer bearbeiten) unter **Password** (Kennwort) und **Confirm Password** (Kennwort bestätigen) das neue Kennwort ein: 

   ![Screenshot mit Benutzerinformationen](../operator/media/idrac-credentials/enter-user.png)

1. Klicken Sie auf **Save** (Speichern) und dann auf **OK**. 

## <a name="next-steps"></a>Nächste Schritte

[Rotieren von Geheimnissen in Azure Stack Hub](../../operator/azure-stack-rotate-secrets.md)