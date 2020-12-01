---
title: AKS-Problembehandlung
description: Dieser Artikel enthält Informationen zur Problembehandlung bei Azure Kubernetes Service (AKS) in Azure Stack HCI.
author: davannaw-msft
ms.topic: how-to
ms.date: 09/22/2020
ms.author: dawhite
ms.openlocfilehash: 4f13aff85c1444197fce5a01c62319026f844fe6
ms.sourcegitcommit: 30ea43f486895828710297967270cb5b8d6a1a18
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93415044"
---
# <a name="troubleshooting-azure-kubernetes-service-on-azure-stack-hci"></a>Problembehandlung für Azure Kubernetes Service in Azure Stack HCI

Wenn Sie einen Kubernetes-Cluster mithilfe von Azure Kubernetes Service in Azure Stack HCI erstellen oder verwalten, können Sie gelegentlich auf Probleme stoßen. Dieser Artikel enthält Anleitungen, mit denen Sie diese Probleme beheben können.

## <a name="troubleshooting-azure-stack-hci"></a>Problembehandlung für Azure Stack HCI
Informationen dazu, wie Sie Probleme bei Clustervalidierungsberichten für Netzwerk- und QoS-Einstellungen (Quality of Service) auf den Servern in einem Azure Stack HCI-Cluster beheben und wie Sie überprüfen, ob wichtige Regeln definiert sind, finden Sie unter [Beheben von Problemen bei Clustervalidierungsberichten](../hci/manage/validate-qos.md).

Informationen zur Behebung von Problemen mit CredSSP finden Sie unter [Problembehandlung für CredSSP](../hci/manage/troubleshoot-credssp.md).

## <a name="troubleshooting-windows-admin-center"></a>Problembehandlung für Windows Admin Center
Dieses Produkt befindet sich in der öffentlichen Vorschau, was bedeutet, dass es sich noch in der Entwicklung befindet. Derzeit gibt es mehrere Probleme mit der Windows Admin Center Azure Kubernetes Service-Erweiterung: 
* Zurzeit muss jeder Server im Cluster des Systems, mit dem Sie Azure Kubernetes Service in Azure Stack HCI einrichten, ein vertrauenswürdiger Server sein. Windows Admin Center muss also auf jedem Server im Cluster CredSSP-Vorgänge durchführen können, nicht nur auf einem oder einigen wenigen Servern. 
* Wenn Sie die Fehlermeldung `msft.sme.aks couldn't load` erhalten, in der Ihnen mitgeteilt wird, dass keine Blöcke geladen werden konnten, verwenden Sie die neueste Version von Microsoft Edge oder Google Chrome, und versuchen Sie es erneut.
* Bevor Sie entweder den Assistenten für die Hosteinrichtung von Azure Kubernetes Service oder den Assistenten zum Erstellen des Kubernetes-Clusters starten, sollten Sie sich über Windows Admin Center bei Azure anmelden. Möglicherweise müssen Sie sich während des Workflows erneut anmelden. Wenn Probleme bei der Anmeldung bei Azure über Windows Admin Center auftreten, versuchen Sie, sich von einer anderen Quelle aus, z. B. dem [Azure-Portal](https://portal.azure.com/), bei Ihrem Azure-Konto anzumelden. Wenn weiterhin Probleme auftreten, lesen Sie den Artikel [Bekannte Probleme beim Windows Admin Center](/windows-server/manage/windows-admin-center/support/known-issues), bevor Sie sich an den Support wenden.
* In der aktuellen Iteration der Bereitstellung von Azure Kubernetes Service in Azure Stack HCI über das Windows Admin Center kann nur der Benutzer, der den Azure Kubernetes Service-Host eingerichtet hat, Kubernetes-Cluster auf dem System erstellen. Um dieses Problem zu umgehen, kopieren Sie den Ordner „.wssd“ aus dem Profil des Benutzers, der den Azure Kubernetes Service-Host eingerichtet hat, in das Profil des Benutzers, der den neuen Kubernetes-Cluster erstellen wird.
* Wenn Sie in einem der beiden Assistenten einen Fehler aufgrund einer falschen Konfiguration erhalten, führen Sie Vorgänge zur Bereinigung des Clusters durch. Diese Vorgänge könnten das Entfernen der Datei „C:\Programme\AksHci\mocctl.exe“ einschließen.
* Damit CredSSP innerhalb des Assistenten zum Erstellen von Clustern erfolgreich funktioniert, muss Windows Admin Center installiert sein und von demselben Konto verwendet werden. Wenn Sie Windows Admin Center mit einem Konto installieren und versuchen, es mit einem anderen Konto zu verwenden, treten Fehler auf.
* Während der Bereitstellung des Clusters kann ein Problem mit der Dateiübertragung von „helm.zip“ auftreten. Dieses Problem führt häufig zu der Fehlermeldung, dass der Pfad zur helm.zip-Datei nicht vorhanden oder ungültig sei. Um dieses Problem zu beheben, wiederholen Sie die Bereitstellung.
* Wenn Ihre Bereitstellung für längere Zeit hängen bleibt, liegen möglicherweise CredSSP- oder Konnektivitätsprobleme vor. Versuchen Sie die folgenden Schritte zur Problembehandlung für Ihre Bereitstellung: 
    1.  Führen Sie auf dem Computer, auf dem Windows Admin Center ausgeführt wird, den folgenden Befehl in einem PowerShell-Fenster aus: 
          ```PowerShell
          Enter-PSSession <servername>
          ```
    2.  Wenn dieser Befehl erfolgreich ausgeführt wird, können Sie eine Verbindung mit dem Server herstellen, und es liegt kein Konnektivitätsproblem vor.
    
    3.  Wenn CredSSP-Probleme vorliegen, führen Sie den folgenden Befehl aus, um die Vertrauensstellung zwischen dem Gateway- und dem Zielcomputer zu testen: 
          ```PowerShell
          Enter-PSSession –ComputerName <server> –Credential company\administrator –Authentication CredSSP
          ``` 
        Sie können auch den folgenden Befehl ausführen, um die Vertrauensstellung beim Zugriff auf das lokale Gateway zu testen: 
          ```PowerShell
          Enter-PSSession -computer localhost -credential (Get-Credential)
          ``` 
* Wenn Sie Azure Arc verwenden und über mehrere Mandanten-IDs verfügen, führen Sie den folgenden Befehl aus, um den gewünschten Mandanten vor der Bereitstellung anzugeben. Wenn Sie dies nicht tun, kann bei der Bereitstellung ein Fehler auftreten.

   ```Azure CLI
   az login –tenant <tenant>
   ```
* Wenn Sie gerade erst ein neues Azure-Konto erstellt und sich auf Ihrem Gatewaycomputer nicht bei dem Konto angemeldet haben, können Probleme bei der Registrierung Ihres Windows Admin Center-Gateways bei Azure auftreten. Melden Sie sich auf einer anderen Browserregisterkarte oder in einem anderen Browserfenster bei Ihrem Azure-Konto an, und registrieren Sie dann das Windows Admin Center-Gateway bei Azure, um dieses Problem zu beheben.

### <a name="creating-windows-admin-center-logs"></a>Erstellen von Windows Admin Center-Protokollen
Wenn Sie Probleme mit dem Windows Admin Center melden, sollten Sie Protokolle anzufügen, damit das Entwicklungsteam Ihr Problem diagnostizieren kann. Fehler im Windows Admin Center treten in der Regel in einer dieser beiden Formen auf: 
- Ereignisse, die in der Ereignisanzeige auf dem Computer angezeigt werden, auf dem Windows Admin Center ausgeführt wird 
- JavaScript-Probleme, die in der Browserkonsole auftreten 

Zum Sammeln von Protokollen für Windows Admin Center verwenden Sie das im öffentlichen Vorschaupaket enthaltene Skript Get-SMEUILogs.ps1. 
 
Um das Skript zu verwenden, führen Sie den folgenden Befehl in dem Ordner aus, in dem Ihr Skript gespeichert ist: 
 
```PowerShell
./Get-SMEUILogs.ps1 -ComputerNames [comp1, comp2, etc.] -Destination [comp3] -HoursAgo [48] -NoCredentialPrompt
```
 
Der Befehl hat die folgenden Parameter:
 
* `-ComputerNames`: Eine Liste der Computer, von denen Sie Protokolle erfassen möchten.
* `-Destination`: Der Computer, auf dem Sie die Protokolle aggregieren möchten.
* `-HoursAgo`: Die Startzeit für das Sammeln von Protokollen in Stunden vor dem Ausführen des Skripts.
* `-NoCredentialPrompt`: Ein Switch, mit dem Sie die Anmeldeinformationen deaktivieren und die Standardanmeldeinformationen in Ihrer aktuellen Umgebung verwenden können.
 
Wenn Sie Schwierigkeiten haben, dieses Skript auszuführen, können Sie den folgenden Befehl ausführen, um den Hilfetext anzuzeigen: 
 
```PowerShell
GetHelp .\Get-SMEUILogs.ps1 -Examples
```

## <a name="troubleshooting-windows-worker-nodes"></a>Problembehandlung für Windows-Workerknoten 
Zum Anmelden bei einem Windows-Workerknoten rufen Sie zunächst die IP-Adresse Ihres Knotens ab, indem Sie `kubectl get` ausführen. Notieren Sie den Wert von `EXTERNAL-IP`.

```PowerShell
kubectl get nodes -o wide
``` 
Stellen Sie mit `ssh Administrator@ip` eine SSH-Verbindung mit dem Knoten her. Nachdem Sie eine SSH-Verbindung mit dem Knoten hergestellt haben, können Sie `net user administrator *` ausführen, um das Kennwort Ihres Administrators zu aktualisieren. 

## <a name="troubleshooting-linux-worker-nodes"></a>Problembehandlung für Linux-Workerknoten 
Zum Anmelden bei einem Linux-Workerknoten rufen Sie zunächst die IP-Adresse Ihres Knotens ab, indem Sie `kubectl get` ausführen. Notieren Sie den Wert von `EXTERNAL-IP`.

```PowerShell
kubectl get nodes -o wide
``` 
Stellen Sie mit `ssh clouduser@ip` eine SSH-Verbindung mit dem Knoten her. 

## <a name="troubleshooting-azure-arc-kubernetes"></a>Problembehandlung bei Kubernetes in Azure Arc
Informationen zur Problembehandlung bei einigen allgemeinen Szenarien in Bezug auf Konnektivität, Berechtigungen und Arc-Agents finden Sie unter [Problembehandlung bei Azure Arc-fähigem Kubernetes](/azure/azure-arc/kubernetes/troubleshooting).

## <a name="next-steps"></a>Nächste Schritte
Wenn weiterhin Probleme auftreten, während Sie Azure Kubernetes Service in Azure Stack HCI verwenden, können Sie Fehler über [GitHub](https://aka.ms/aks-hci-issues) melden.