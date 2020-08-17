---
title: Problembehandlung für CredSSP
description: Informationen zur Problembehandlung für CredSSP
author: v-dasis
ms.topic: how-to
ms.date: 08/06/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 24e9483aa9658809adc9be7fbfa4a2cb13daab84
ms.sourcegitcommit: 952d26ad08fcc28ad3ad83e27644e61497623a44
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87899927"
---
# <a name="troubleshoot-credssp"></a>Problembehandlung für CredSSP

> Gilt für Azure Stack HCI, Version v20H2

Bei einigen Azure Stack HCI-Vorgängen wird die Windows-Remoteverwaltung (WinRM) verwendet, die die Delegierung von Anmeldeinformationen standardmäßig nicht zulässt. Um die Delegierung zuzulassen, muss für den Computer vorübergehend der Credential Security Support Provider (CredSSP) aktiviert werden. CredSSP ist ein Security Support Provider, der es einem Client ermöglicht, Anmeldeinformationen zur Remoteauthentifizierung an einen Zielserver zu delegieren. 

Das Aktivieren von CredSSP beeinträchtigt den Sicherheitsstatus. Daher sollte CredSSP in den meisten Fällen nach Abschluss der Aufgabe oder des Vorgangs deaktiviert werden.

Für folgende Aufgaben muss CredSSP beispielsweise aktiviert werden:

- Erstellen eines Workflows für den Clustererstellungs-Assistenten
- Active Directory-Abfragen oder -Updates
- SQ Server-Abfragen oder -Updates
- Suchen nach Konten oder Computern in einer anderen Domäne oder einer nicht in die Domäne eingebundenen Umgebung

## <a name="troubleshooting-tips"></a>Tipps zur Problembehandlung

Wenn bei der Verwendung von CredSSP Probleme auftreten, können Ihnen die folgenden Tipps zur Problembehandlung möglicherweise weiterhelfen:

- Wenn keine Active Directory-Vertrauensstellung eingerichtet wurde oder die Vertrauensstellung fehlerhaft ist, meldet CredSSP beim Ausführen des Clustererstellungs-Assistenten u. U. ein Problem. Dieses Problem tritt auf, wenn arbeitsgruppenbasierte Server zum Erstellen des Clusters werden. Versuchen Sie in diesem Fall, jeden Server im Cluster manuell neu zu starten.

- Wenn Sie Windows Admin Center auf einem Server ausführen (Dienstmodus), muss das Benutzerkonto Mitglied der Gatewayadministratorgruppe sein.

- Um CredSSP auf einem Server aktivieren oder deaktivieren zu können, müssen Sie Mitglied der Gatewayadministratorgruppe auf diesem Computer sein. Weitere Informationen finden Sie in den ersten beiden Abschnitten des Artikels [Konfigurieren der Benutzerzugriffssteuerung und von Berechtigungen](/windows-server/manage/windows-admin-center/configure/user-access-control#gateway-access-role-definitions).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu CredSSP finden Sie unter [Credential Security Support Provider](/windows/win32/secauthn/credential-security-support-provider).