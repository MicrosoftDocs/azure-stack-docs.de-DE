---
title: Problembehandlung für CredSSP
description: Informationen zur Problembehandlung für CredSSP
author: v-dasis
ms.topic: how-to
ms.date: 12/14/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 472f0cf6103ac1559a9f8a0f757d66bc545f9a5d
ms.sourcegitcommit: f4a1a7e9d0b64ca84105d48170a23e1f473e976c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/23/2020
ms.locfileid: "97743538"
---
# <a name="troubleshoot-credssp"></a>Problembehandlung für CredSSP

> Gilt für Azure Stack HCI, Version v20H2

Bei einigen Azure Stack HCI-Vorgängen wird die Windows-Remoteverwaltung (WinRM) verwendet, die die Delegierung von Anmeldeinformationen standardmäßig nicht zulässt. Um die Delegierung zuzulassen, muss für den Computer vorübergehend der Credential Security Support Provider (CredSSP) aktiviert werden. CredSSP ist ein Security Support Provider, der es einem Client ermöglicht, Anmeldeinformationen zur Remoteauthentifizierung an einen Server zu delegieren.

Das Aktivieren von CredSSP beeinträchtigt den Sicherheitsstatus. Daher sollte CredSSP in den meisten Fällen nach Abschluss der Aufgabe oder des Vorgangs deaktiviert werden.

Für folgende Aufgaben muss CredSSP beispielsweise aktiviert werden:

- Erstellen eines Workflows für den Clustererstellungs-Assistenten
- Active Directory-Abfragen oder -Updates
- SQL Server-Abfragen oder -Updates
- Suchen nach Konten oder Computern in einer anderen Domäne oder einer nicht in die Domäne eingebundenen Umgebung

## <a name="troubleshooting-tips"></a>Tipps zur Problembehandlung

Wenn bei der Verwendung von CredSSP Probleme auftreten, können Ihnen die folgenden Tipps zur Problembehandlung möglicherweise weiterhelfen:

- Um den Clustererstellungs-Assistenten zu verwenden, wenn Windows Admin Center auf einem Server und nicht auf einem PC ausgeführt wird, müssen Sie Mitglied der Gruppe „Gatewayadministratoren“ auf dem Windows Admin Center-Server sein. Weitere Informationen finden Sie unter [Benutzerzugriffsoptionen in Windows Admin Center](/windows-server/manage/windows-admin-center/plan/user-access-options).

- Wenn keine Active Directory-Vertrauensstellung eingerichtet wurde oder die Vertrauensstellung fehlerhaft ist, meldet CredSSP beim Ausführen des Clustererstellungs-Assistenten u. U. ein Problem. Dieses Problem tritt auf, wenn arbeitsgruppenbasierte Server zum Erstellen des Clusters werden. Versuchen Sie in diesem Fall, jeden Server im Cluster manuell neu zu starten.

- Wenn Sie Windows Admin Center auf einem Server ausführen, muss das Benutzerkonto Mitglied der Gruppe „Gatewayadministratoren“ sein.

- Es wird empfohlen, Windows Admin Center auf einem Computer auszuführen, der Mitglied derselben Domäne wie die verwalteten Server ist.

- Um CredSSP auf einem Server aktivieren oder deaktivieren zu können, müssen Sie Mitglied der Gatewayadministratorgruppe auf diesem Computer sein. Weitere Informationen finden Sie in den ersten beiden Abschnitten des Artikels [Konfigurieren der Benutzerzugriffssteuerung und von Berechtigungen](/windows-server/manage/windows-admin-center/configure/user-access-control#gateway-access-role-definitions).

- Wenn Sie den WinRM-Dienst auf den Servern im Cluster neu starten, werden Sie unter Umständen aufgefordert, die WinRM-Verbindung zwischen den einzelnen Clusterservern und dem Windows Admin Center wiederherzustellen.

    Dazu können Sie beispielsweise auf jedem einzelnen Clusterserver im Windows Admin Center im Menü **Tools** die Option **Dienste**, **WinRM**, **Neu starten** und dann in der Eingabeaufforderung **Dienst neu starten** die Option **Ja** auswählen.

## <a name="manual-troubleshooting"></a>Manuelle Problembehandlung

Falls Sie die unten angegebene WinRM-Fehlermeldung erhalten, können Sie versuchen, die in diesem Abschnitt enthaltenen Schritte für die manuelle Überprüfung auszuführen, um den Fehler zu beheben. Beispiel für Fehlermeldung:

`Connecting to remote <sever name> failed with the following error message: The WinRM client cannot process the request. A computer policy does not allow the delegation of the user credentials to the target computer because the computer is not trusted. The identity of the target computer can be verified if you configure the WSMAN service to use a valid certificate.`

Bei den Schritten für die manuelle Überprüfung in diesem Abschnitt müssen Sie die folgenden Computer konfigurieren:
- Computer, auf dem Windows Admin Center ausgeführt wird
- Server, auf dem Sie die Fehlermeldung erhalten haben

Führen Sie je nach Bedarf die folgenden Problembehandlungsschritte aus, um den Fehler zu beheben:

**Problembehandlung 1:**
1. Starten Sie den Computer, auf dem Windows Admin Center ausgeführt wird, und den Server neu.
1. Versuchen Sie erneut, den Clustererstellungs-Assistenten auszuführen.

    Ausführliche Informationen zum Ausführen des Assistenten finden Sie unter [Erstellen eines Azure Stack HCI-Clusters mithilfe von Windows Admin Center](../deploy/create-cluster.md).

**Problembehandlung 2:**
1. Öffnen Sie Windows PowerShell auf dem Computer, auf dem Windows Admin Center ausgeführt wird, als Administrator, und führen Sie die folgenden Befehle aus:

    ```powershell
    Disable-WsmanCredSSP -Role Client  
    ```

    ```powershell  
    Enable-WsmanCredSSP -Role Client -DelagateComputer <Server FQDN Name>
    ```

1. Verwenden Sie das RDP-Feature, um eine Verbindung mit dem Server herzustellen, und führen Sie dann die folgenden PowerShell-Befehle aus:

    ```powershell  
    Disable-WsmanCredSSP -Role Server  
    ```

    ```powershell  
    Enable-WsmanCredSSP -Role Server  
    ```
    
1. Versuchen Sie erneut, den Clustererstellungs-Assistenten auszuführen.

    Ausführliche Informationen zum Ausführen des Assistenten finden Sie unter [Erstellen eines Azure Stack HCI-Clusters mithilfe von Windows Admin Center](../deploy/create-cluster.md).

**Problembehandlung 3:**
1. Führen Sie auf dem Computer, auf dem Windows Admin Center ausgeführt wird, den folgenden PowerShell-Befehl aus, um den Dienstprinzipalnamen (SPN) zu überprüfen:

    ```powershell
    setspn -Q WSMAN/<Windows Admin Center Computer Name>  
    ```
    
    Als Ergebnis sollte in etwa die folgende Ausgabe angezeigt werden:

    `WSMAN/<Windows Admin Center Computer Name>`

    `WSMAN/<Windows Admin Center Computer FQDN Name>`

1. Führen Sie die folgenden PowerShell-Befehle aus, um den SPN zu registrieren, falls die Ergebnisse nicht aufgeführt sind:

    ```powershell
    setspn -S WSMAN/<Windows Admin Center Computer Name> <Windows Admin Center Computer Name>  
    ```

    ```powershell
    setspn -S WSMAN/<Windows Admin Center Computer Name> <Windows Admin Center Computer FQDN Name>  
    ```

1. Verwenden Sie das RDP-Feature, um eine Verbindung mit dem Server herzustellen, und führen Sie dann den folgenden PowerShell-Befehl zum Überprüfen des SPN aus:

    ```powershell
    setspn -Q WSMAN/<Server Name>  
    ```

    Als Ergebnis sollte in etwa die folgende Ausgabe angezeigt werden:

    `WSMAN/<Server Name>`

    `WSMAN/<Server FQDN Name>`

1. Führen Sie die folgenden PowerShell-Befehle aus, um den SPN zu registrieren, falls die Ergebnisse nicht aufgeführt sind:

    ```powershell
    setspn -S WSMAN/<Server Name> <Server Name>  
    ```

    ```powershell
    setspn -S WSMAN/<Server Name> <Server FQDN Name>  
    ```

1. Versuchen Sie erneut, den Clustererstellungs-Assistenten auszuführen.

    Ausführliche Informationen zum Ausführen des Assistenten finden Sie unter [Erstellen eines Azure Stack HCI-Clusters mithilfe von Windows Admin Center](../deploy/create-cluster.md).


**Problembehandlung 4:**

Falls einer der obigen Problembehandlungsschritte nicht erfolgreich war oder nicht abgeschlossen werden konnte, kann dies ein Hinweis auf einen Datensatzkonflikt in Active Directory sein. Sie können einen anderen Computernamen verwenden, um eine Zurücksetzung durchzuführen und einen neuen Datensatz in Active Directory zu verwenden.

Führen Sie eine Neuinstallation des Azure Stack HCI-Betriebssystems mit einem neuen Computernamen durch, um den Datensatz in Active Directory zurückzusetzen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu CredSSP finden Sie unter [Credential Security Support Provider](/windows/win32/secauthn/credential-security-support-provider).