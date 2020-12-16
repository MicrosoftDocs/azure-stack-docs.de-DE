---
title: Switchanmeldeinformationen
description: Erläutert das Aktualisieren der Switchanmeldeinformationen für Azure Stack Hub Ruggedized
author: troettinger
ms.author: thoroet
ms.service: azure-stack
ms.topic: article
ms.date: 10/14/2020
ms.reviewer: justinha
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: a761544c8d514fd69364d917890d284ab06e9962
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96941150"
---
# <a name="switch-credentials"></a>Switchanmeldeinformationen

In diesem Thema wird gezeigt, wie Sie die Administratoranmeldeinformationen und SNMP-Anmeldeinformationen (Simple Network Management Protocol) für alle Switches ändern. 

## <a name="prerequisites"></a>Voraussetzungen

Vor dem Ausführen der Schritte:

- Stellen Sie mithilfe von Remotedesktop eine Verbindung mit dem HLH her.
- Suchen Sie auf dem HLH nach PuTTY, dessen Pfad i. d. R. „E:\Tools\Putty\putty.exe“ lautet. Wenn PuTTY nicht verfügbar ist, laden Sie es herunter, und kopieren Sie es auf den HLH.
- Stellen Sie sicher, dass Sie über die aktuellen und die neuen Anmeldeinformationen für die Switches verfügen.

## <a name="update-credentials-for-the-admin-and-enable-accounts"></a>Aktualisieren der Anmeldeinformationen für Administrator- und Aktivierungskonten 

Für jeden Switch in der Skalierungseinheit (BMC, TOR1 und TOR2):

1. Melden Sie sich auf dem HLH mithilfe von PuTTY unter Verwendung der aktuellen Anmeldeinformationen bei dem Switch an. 
1. Führen Sie den folgenden Befehl aus, und ersetzen Sie \<new password\> durch die neuen Administrator- und Aktivierungsanmeldeinformationen. 
   ```ini
   enable
   configuration terminal
   username admin pass
   word <new_password> privilege 15
   enable password <new_password>
   ```
1. Lassen Sie die aktuelle Sitzung für den Switch geöffnet.
1. Melden Sie sich auf dem HLH mithilfe von PuTTY unter Verwendung der neuen Anmeldeinformationen bei dem Switch an.
1. Führen Sie den folgenden Befehl aus. Sie werden von der Aktivierung nicht zur Eingabe eines Kennworts aufgefordert.

   ```ini
   enable
   write
   dir flash:
   ```

1. Überprüfen Sie, ob die Startkonfiguration das aktuelle Datum aufweist.
1. Nachdem Sie sich dessen vergewissert haben, schließen Sie diese Sitzung und die ursprüngliche Sitzung.

## <a name="update-snmp-accounts"></a>Aktualisieren von SNMP-Konten

Für jeden Switch in der Skalierungseinheit (BMC, TOR1 und TOR2):

1. Melden Sie sich auf dem HLH mithilfe von PuTTY bei dem Switch an.
1. Führen Sie den folgenden Befehl aus, um die aktuellen SNMP-Benutzer und -Gruppen mit Lese-/Schreibzugriff abzurufen:

   ```ini
   enable
   show run snmp | grep user
   ```

   Im folgenden Beispiel wird der Typ des zurückgegebenen Inhalts gezeigt, einschließlich des Benutzer- und Gruppennamens sowie des Kennworthashs:

   ```ini
   snmp-server user <user> <group> 3 encrypted auth sha <password_hash>
   ```

1. Führen Sie den folgenden Befehl aus. Ersetzen Sie dabei \<user\> und \<group\> durch die im vorherigen Schritt ermittelten Informationen, und ersetzen Sie \<password\> durch das neue Kennwort:

   ```ini
   configuration terminal
   snmp-server user <user> <group> 3 auth sha <password_new>
   end
   write
   exit
   ```

## <a name="next-steps"></a>Nächste Schritte

[Rotieren von Geheimnissen in Azure Stack Hub](../operator/azure-stack-rotate-secrets.md)