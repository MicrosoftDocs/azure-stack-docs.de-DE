---
title: Anmeldeinformationen für den Integrated Dell Remote Access Controller
description: In diesem Artikel wird erläutert, wie Sie die Anmeldeinformationen für den Integrated Dell Remote Access Controller aktualisieren.
author: troettinger
ms.author: thoroet
ms.service: azure-stack
ms.topic: article
ms.date: 10/27/2020
ms.reviewer: justinha
ms.lastreviewed: 10/27/2020
ms.openlocfilehash: 1525872e3c59ab7e5b0cd436ba5ab8e2e3836594
ms.sourcegitcommit: 9ecf9c58fbcc4bc42c1fdc688f370c643c761a29
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93330101"
---
# <a name="update-credentials-for-the-integrated-dell-remote-access-controller"></a>Aktualisieren der Anmeldeinformationen für den Integrated Dell Remote Access Controller

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