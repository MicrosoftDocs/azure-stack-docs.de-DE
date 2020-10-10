---
title: Problembehandlung
description: Leitfaden zur Problembehandlung für Azure Kubernetes Service in Azure Stack HCI
author: davannaw-msft
ms.topic: how-to
ms.date: 09/22/2020
ms.author: dawhite
ms.openlocfilehash: e30d5ba784efc6453ce161bc2a87db7c728d3fce
ms.sourcegitcommit: 373e9e3e84eaa33331db9f78e52486fbb6beb907
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/30/2020
ms.locfileid: "91592934"
---
# <a name="troubleshooting-azure-kubernetes-service-on-azure-stack-hci"></a>Problembehandlung für Azure Kubernetes Service in Azure Stack HCI

Wenn Sie einen Kubernetes-Cluster mithilfe von Azure Kubernetes Service in Azure Stack HCI erstellen oder verwalten, können Sie gelegentlich auf Probleme stoßen. Im Folgenden finden Sie die Richtlinien zur Problembehandlung, die Ihnen bei der Lösung dieser Probleme helfen sollen. 

## <a name="troubleshooting-azure-stack-hci"></a>Problembehandlung für Azure Stack HCI
Informationen dazu, wie Sie Probleme bei Clustervalidierungsberichten für Netzwerk- und QoS-Einstellungen (Quality of Service) auf den Servern in einem Azure Stack HCI-Cluster beheben und wie Sie überprüfen, ob wichtige Regeln definiert sind finden Sie unter [Beheben von Problemen bei Clustervalidierungsberichten](/azure-stack/hci/manage/validate-qos).

Informationen zur Behebung von Problemen mit CredSSP finden Sie unter [Problembehandlung für CredSSP](/azure-stack/hci/manage/troubleshoot-credssp).

## <a name="troubleshooting-windows-admin-center"></a>Problembehandlung für Windows Admin Center
Dieses Produkt befindet sich derzeit in der öffentlichen Vorschauversion, was bedeutet, dass es sich noch in der Entwicklung befindet. Derzeit gibt es mehrere Probleme mit der Windows Admin Center Azure Kubernetes Service-Erweiterung: 
* Zurzeit muss jeder Server im Cluster des Systems, mit dem Sie Azure Kubernetes Service in Azure Stack HCI einrichten, ein vertrauenswürdiger Server sein. Dies bedeutet, dass Windows Admin Center in der Lage sein muss, CredSSP-Vorgänge auf jedem Server im Cluster durchzuführen, nicht nur auf einem oder einigen wenigen Servern. 
* Wenn ein Fehler mit der Meldung `msft.sme.aks couldn't load` angezeigt wird, in der Ihnen mitgeteilt wird, dass keine Blöcke geladen werden konnten, verwenden Sie die neueste Version von Microsoft Edge oder Google Chrome, und versuchen Sie es erneut.
* Bevor Sie entweder den Assistenten für die Hosteinrichtung von Azure Kubernetes Service oder den Assistenten zum Erstellen des Kubernetes-Clusters starten, sollten Sie sich über das Windows Admin Center bei Azure anmelden. Während des Workflows kann das erneute Signieren erforderlich sein. Wenn Probleme bei der Anmeldung bei Azure über das Windows Admin Center auftreten, versuchen Sie, sich von einer anderen Quelle aus, z. B. dem [Azure-Portal](https://portal.azure.com/), bei Ihrem Azure-Konto anzumelden. Wenn weiterhin Probleme auftreten, lesen Sie den Artikel [Bekannte Probleme beim Windows Admin Center](/windows-server/manage/windows-admin-center/support/known-issues), bevor Sie sich an den Support wenden.
* In der aktuellen Iteration der Bereitstellung von Azure Kubernetes Service in Azure Stack HCI über das Windows Admin Center kann nur der Benutzer, der den Azure Kubernetes Service-Host eingerichtet hat, Kubernetes-Cluster auf dem System erstellen. Um dieses Problem zu umgehen, kopieren Sie den Ordner `.wssd` aus dem Benutzerprofil, das den Azure Kubernetes Service-Host eingerichtet hat, in das Benutzerprofil, das den neuen Kubernetes-Cluster starten wird.
* Wenn Sie in einem der beiden Assistenten einen Fehler aufgrund einer falschen Konfiguration erhalten, führen Sie Vorgänge zur Bereinigung des Clusters durch. Dazu kann es erforderlich sein, die Datei `C:\Program Files\AksHci\mocctl.exe` zu entfernen.
* Damit CredSSP innerhalb des Assistenten zum Erstellen von Clustern erfolgreich funktioniert, muss Windows Admin Center installiert sein und von demselben Konto verwendet werden. Die Installation mit einem Konto und der anschließende Versuch, es mit einem anderen Konto zu verwenden, führt zu Fehlern.
* Während der Bereitstellung des Clusters kann es ein Problem mit der Dateiübertragung von „helm.zip“ geben. Dies führt häufig zu einem Fehler, der besagt, dass der Pfad zur Datei „helm.zip“ nicht vorhanden oder ungültig ist. Kehren Sie zurück und wiederholen Sie die Bereitstellung, um dieses Problem zu beheben.
* Wenn Ihre Bereitstellung für längere Zeit hängen bleibt, haben Sie möglicherweise CredSSP- oder Konnektivitätsprobleme. Versuchen Sie die folgenden Schritte zur Problembehandlung für Ihre Bereitstellung: 
    1.  Führen Sie auf dem Computer, auf dem WAC ausgeführt wird, den folgenden Befehl in einem PowerShell-Fenster aus: 
    ```PowerShell
    Enter-PSSession <servername>
    ```
    2.  Wenn dieser Befehl erfolgreich ausgeführt wird, können Sie eine Verbindung mit dem Server herstellen, und es gibt kein Konnektivitätsproblem.
    
    3.  Wenn CredSSP-Probleme auftreten, führen Sie den folgenden Befehl aus, um die Vertrauensstellung zwischen dem Gateway- und dem Zielcomputer zu testen: 
    ```PowerShell
    Enter-PSSession –ComputerName <server> –Credential company\administrator –Authentication CredSSP
    ``` 
    Sie können auch den folgenden Befehl ausführen, um die Vertrauensstellung beim Zugriff auf das lokale Gateway zu testen: 
    ```PowerShell
    Enter-PSSession -computer localhost -credential (Get-Credential)
    ``` 
* Wenn Sie Azure Arc verwenden und über mehrere Mandanten-IDs verfügen, führen Sie den folgenden Befehl aus, um den gewünschten Mandanten vor der Bereitstellung anzugeben. Sollte dies nicht geschehen, könnte bei der Bereitstellung ein Fehler auftreten.

```Azure CLI
az login –tenant <tenant>
```
* Wenn Sie gerade erst ein neues Azure-Konto erstellt haben und sich auf Ihrem Gatewaycomputer nicht bei dem Konto angemeldet haben, kann es zu Problemen bei der Registrierung Ihres WAC-Gateways bei Azure kommen. Melden Sie sich auf einer anderen Browserregisterkarte oder in einem anderen Browserfenster bei Ihrem Azure-Konto an, und registrieren Sie dann das WAC-Gateway bei Azure, um dieses Problem zu beheben.

### <a name="creating-windows-admin-center-logs"></a>Erstellen von Windows Admin Center-Protokollen
Wenn Sie Probleme im Windows Admin Center melden, ist es hilfreich, Protokolle anzufügen, sodass das Entwicklungsteam Ihr Problem diagnostizieren kann. Fehler in Windows Admin Center treten im Allgemeinen in zwei Formen auf: Ereignisse, die in der Ereignisanzeige auf dem Computer, auf dem Windows Admin Center ausgeführt wird, angezeigt werden, oder JavaScript-Probleme, die in der Browserkonsole auftreten. Zum Sammeln von Protokollen für Windows Admin Center verwenden Sie das Skript `Get-SMEUILogs.ps1`, das im öffentlichen Vorschaupaket enthalten ist. 
 
Führen Sie den folgenden Befehl in dem Dateiverzeichnis aus, in dem Ihr Skript gespeichert ist, um das Skript zu verwenden: 
 
```PowerShell
./Get-SMEUILogs.ps1 -ComputerNames [comp1, comp2, etc.] -Destination [comp3] -HoursAgo [48] -NoCredentialPrompt
```
 
Der Befehl hat die folgenden Parameter:
 
* **-ComputerNames**: Eine Liste der Computer, von denen Sie Protokolle erfassen möchten.
* **-Destination**: Der Computer, auf dem Sie die Protokolle aggregieren möchten.
* **-HoursAgo**: Dieser Parameter gibt an, von wie vielen Stunden der Skriptruntime Sie Protokolle erfassen möchten.
* **-NoCredentialPrompt**: Dies ist ein Switch, mit dem Sie die Anmeldeinformationen deaktivieren und die Standardanmeldeinformationen in Ihrer aktuellen Umgebung verwenden können.
 
Wenn Sie Schwierigkeiten haben, dieses Skript auszuführen, können Sie den folgenden Befehl ausführen, um den Hilfetext anzuzeigen: 
 
```PowerShell
GetHelp .\Get-SMEUILogs.ps1 -Examples
```

## <a name="troubleshooting-windows-worker-nodes"></a>Problembehandlung für Windows-Workerknoten 
Zum Anmelden bei einem Windows-Workerknoten rufen Sie zunächst die IP-Adresse Ihres Knotens ab, indem Sie `kubectl get` ausführen und den Wert `EXTERNAL-IP` notieren:

```PowerShell
kubectl get nodes -o wide
``` 
Stellen Sie mit `ssh Administrator@ip` eine SSH-Verbindung mit dem Knoten her. Nachdem Sie eine SSH-Verbindung mit dem Knoten hergestellt haben, können Sie `net user administrator *` ausführen, um das Kennwort Ihres Administrators zu aktualisieren. 

## <a name="troubleshooting-linux-worker-nodes"></a>Problembehandlung für Linux-Workerknoten 
Zum Anmelden bei einem Linux-Workerknoten rufen Sie zunächst die IP-Adresse Ihres Knotens ab, indem Sie `kubectl get` ausführen und den Wert `EXTERNAL-IP` notieren:

```PowerShell
kubectl get nodes -o wide
``` 
Stellen Sie mit `ssh clouduser@ip` eine SSH-Verbindung mit dem Knoten her. 

## <a name="troubleshooting-azure-arc-for-kubernetes"></a>Problembehandlung bei Azure Arc für Kubernetes
Zur Problembehandlung einiger allgemeiner Szenarien in Bezug auf Konnektivität, Berechtigungen und Arc-Agents besuchen Sie [Problembehandlung bei Azure Arc-fähigem Kubernetes](/azure/azure-arc/kubernetes/troubleshooting).

## <a name="next-steps"></a>Nächste Schritte
Wenn weiterhin Probleme auftreten, während Sie Azure Kubernetes Service in Azure Stack HCI verwenden, melden Sie Fehler über [GitHub](https://aka.ms/aks-hci-issues).  
