---
title: Erstellen eines virtuellen Windows-Computers in Azure Stack mithilfe der Azure CLI | Microsoft-Dokumentation
description: Informationen zum Erstellen einer Windows-VM in Azure Stack mit der Azure CLI
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 01/14/2019
ms.author: mabrigg
ms.custom: mvc
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 67e0ccfa883e79d66eb9ca38a6cf15f00154c487
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "64312939"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Schnellstart: Erstellen eines virtuellen Windows Server-Computers mithilfe der Azure CLI in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Sie können einen virtuellen Windows Server 2016-Computer mithilfe der Azure CLI erstellen. Befolgen Sie die Schritte in diesem Artikel zum Erstellen und Verwenden eines virtuellen Computers. In diesem Artikel führen Sie auch die folgenden Schritte aus:

* Herstellen der Verbindung mit dem virtuellen Computer über einen Remoteclient
* Installieren des IIS-Webservers und Anzeigen der Standardstartseite
* Bereinigen Ihrer Ressourcen

## <a name="prerequisites"></a>Voraussetzungen

* Stellen Sie sicher, dass Ihr Azure Stack-Bediener das Image **Windows Server 2016** dem Azure Stack-Marketplace hinzugefügt hat.

* Azure Stack erfordert eine spezifische Version der Azure CLI, um die Ressourcen zu erstellen und zu verwalten. Wenn Sie die Azure CLI nicht für Azure Stack konfiguriert haben, führen Sie die Schritte zum [Installieren und Konfigurieren der Azure CLI](azure-stack-version-profiles-azurecli2.md) aus.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Ressourcengruppe ist ein logischer Container, in dem Sie Azure Stack-Ressourcen bereitstellen und verwalten können. Führen Sie in Ihrer Azure Stack-Umgebung den Befehl [az group create](/cli/azure/group#az-group-create) aus, um eine Ressourcengruppe zu erstellen.

> [!NOTE]
>  Allen Variablen in den Codebeispielen sind Werte zugewiesen. Sie können bei Bedarf aber auch neue Werte zuweisen.

Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen myResourceGroup im lokalen Speicherort erstellt.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

Erstellen Sie einen virtuellen Computer (VM) mit dem Befehl [az vm create](/cli/azure/vm#az-vm-create). Im folgenden Beispiel wird ein virtueller Computer namens „myVM“ erstellt. Dieses Beispiel verwendet „Demouser“ als Administratorbenutzername und Demouser@123 als Benutzerkennwort. Ändern Sie diese Werte in andere für Ihre Umgebung geeignete Werte.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "Win2016Datacenter" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --location local
```

Nachdem die VM erstellt wurde, enthält der Parameter **PublicIPAddress** in der Ausgabe die öffentliche IP-Adresse für den virtuellen Computer. Notieren Sie sich diese Adresse, da Sie sie für den Zugriff auf den virtuellen Computer benötigen.

## <a name="open-port-80-for-web-traffic"></a>Öffnen von Port 80 für Webdatenverkehr

Da diese VM als IIS-Webserver fungieren soll, muss Port 80 für Datenverkehr mit dem Internet geöffnet werden.

Verwenden Sie zum Öffnen von Port 80 den Befehl [az vm open-port](/cli/azure/vm).

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-the-virtual-machine"></a>Herstellen einer Verbindung mit dem virtuellen Computer

Erstellen Sie mit dem nächsten Befehl eine Remotedesktopverbindung mit Ihrem virtuellen Computer. Ersetzen Sie „Öffentliche IP-Adresse“ durch die IP-Adresse Ihres virtuellen Computers. Geben Sie bei Aufforderung den Benutzernamen und das Kennwort für den virtuellen Computer ein.

```
mstsc /v <Public IP Address>
```

## <a name="install-iis-using-powershell"></a>Installieren von IIS mithilfe von PowerShell

Nachdem Sie sich beim virtuellen Computer angemeldet haben, können Sie IIS mithilfe von PowerShell installieren. Starten Sie PowerShell auf dem virtuellen Computer, und führen Sie den folgenden Befehl aus:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Anzeigen der IIS-Willkommensseite

Die Standardwillkommensseite von IIS kann in einem beliebigen Webbrowser angezeigt werden. Verwenden Sie die öffentliche IP-Adresse, die Sie sich im vorherigen Abschnitt notiert haben, um die Standardseite zu besuchen.

![IIS-Standardwebsite](./media/azure-stack-quick-create-vm-windows-cli/default-iis-website.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Bereinigen Sie die Ressourcen, die Sie nicht mehr benötigen. Führen Sie den Befehl [az group delete](/cli/azure/group#az-group-delete) aus, um die Ressourcengruppe, den virtuellen Computer und alle dazugehörigen Ressourcen zu entfernen.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie einen einfachen virtuellen Windows Server-Computer bereitgestellt. Um weitere Informationen zu virtuellen Computern unter Azure Stack zu erhalten, fahren Sie mit [Considerations for Virtual Machines in Azure Stack](azure-stack-vm-considerations.md) (Überlegungen zu virtuellen Computern in Azure Stack) fort.
