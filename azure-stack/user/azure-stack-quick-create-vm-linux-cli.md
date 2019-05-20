---
title: Erstellen eines virtuellen Linux-Computers mithilfe der Azure CLI in Azure Stack | Microsoft-Dokumentation
description: Erstellen eines virtuellen Linux-Computers mithilfe der CLI in Azure Stack.
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
ms.openlocfilehash: aa744414b554d5da72a63e8af6371bc94b2d6b39
ms.sourcegitcommit: 41927cb812e6a705d8e414c5f605654da1fc6952
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/25/2019
ms.locfileid: "64477390"
---
# <a name="quickstart-create-a-linux-server-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Schnellstart: Erstellen eines virtuellen Linux-Servers mithilfe der Azure CLI in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Sie können einen virtuellen Computer mit Ubuntu Server 16.04 LTS mithilfe der Azure CLI erstellen. Führen Sie die Schritte in diesem Artikel aus, um einen virtuellen Computer zu erstellen und zu verwenden. In diesem Artikel führen Sie auch die folgenden Schritte aus:

* Herstellen der Verbindung mit dem virtuellen Computer über einen Remoteclient
* Installieren des NGINX-Webservers und Anzeigen der Standardstartseite
* Bereinigen nicht mehr benötigter Ressourcen

## <a name="prerequisites"></a>Voraussetzungen

* **Ein Linux-Image im Azure Stack-Marketplace**

   Ein Linux-Image ist standardmäßig nicht im Azure Stack-Marketplace enthalten. Bitten Sie den Azure Stack-Operator, das benötigte Image **Ubuntu Server 16.04 LTS** bereitzustellen. Hierzu kann der Bediener die Schritte des Artikels [Herunterladen von Marketplace-Elementen von Azure in Azure Stack](../operator/azure-stack-download-azure-marketplace-item.md) ausführen.

* Azure Stack erfordert für die Ressourcenerstellung und -verwaltung eine spezifische Version der Azure CLI. Falls die Azure CLI nicht für Azure Stack konfiguriert ist, melden Sie sich beim [Development Kit](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) (oder im Falle einer [VPN-Verbindung](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) bei einem Windows-basierten externen Client) an, und führen Sie die Schritte zum [Installieren und Konfigurieren der Azure CLI](azure-stack-version-profiles-azurecli2.md) aus.

* Ein öffentlicher SSH-Schlüssel mit dem Namen „id_rsa.pub“ im SSH-Verzeichnis Ihres Windows-Benutzerprofils. Ausführliche Informationen zum Erstellen von SSH-Schlüsseln finden Sie unter [Verwenden eines öffentlichen SSH-Schlüssels](azure-stack-dev-start-howto-ssh-public-key.md).

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Ressourcengruppe ist ein logischer Container, in dem Sie Azure Stack-Ressourcen bereitstellen und verwalten können. Führen Sie im Development Kit oder im integrierten Azure Stack-System den Befehl [az group create](/cli/azure/group#az-group-create) aus, um eine Ressourcengruppe zu erstellen.

> [!NOTE]
>  Allen Variablen in den Codebeispielen sind Werte zugewiesen. Sie können bei Bedarf aber auch neue Werte zuweisen.

Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen myResourceGroup im lokalen Speicherort erstellt.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

Erstellen Sie mit dem Befehl [az vm create](/cli/azure/vm#az-vm-create) einen virtuellen Computer. Im folgenden Beispiel wird ein virtueller Computer namens „myVM“ erstellt. Dieses Beispiel verwendet „Demouser“ als Administratorbenutzername und Demouser@123 als Benutzerkennwort. Ändern Sie diese Werte in für Ihre Umgebung geeignete Werte.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "UbuntuLTS" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --location local
```

Die öffentliche IP-Adresse wird im Parameter **PublicIpAddress** zurückgegeben. Notieren Sie sich diese Adresse, da Sie sie für den Zugriff auf den virtuellen Computer benötigen.

## <a name="open-port-80-for-web-traffic"></a>Öffnen von Port 80 für Webdatenverkehr

Da dieser virtuelle Computer als IIS-Webserver fungieren soll, muss der Port 80 für Internetdatenverkehr geöffnet werden. Verwenden Sie zum Öffnen des gewünschten Ports den Befehl [az vm open-port](/cli/azure/vm).

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="use-ssh-to-connect-to-the-virtual-machine"></a>Herstellen einer SSH-Verbindung mit dem virtuellen Computer

Stellen Sie über einen Clientcomputer mit installierter SSH eine Verbindung mit dem virtuellen Computer her. Verwenden Sie auf einem Windows-Client [Putty](https://www.putty.org/), um die Verbindung zu erstellen. Führen Sie den folgenden Befehl aus, um eine Verbindung mit dem virtuellen Computer herzustellen:

```bash
ssh <publicIpAddress>
```

## <a name="install-the-nginx-web-server"></a>Installieren des NGINX-Webservers

Führen Sie das folgende Skript aus, um Paketquellen zu aktualisieren und das neueste NGINX-Paket zu installieren:

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Anzeigen der NGINX-Willkommensseite

Nachdem NGINX installiert und der Port 80 auf dem virtuellen Computer geöffnet wurde, können Sie über die öffentliche IP-Adresse des virtuellen Computers auf den Webserver zugreifen. Öffnen Sie einen Webbrowser, und gehen Sie zu ```http://<public IP address>```.

![Willkommensseite des NGINX-Webservers](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Bereinigen Sie die Ressourcen, die Sie nicht mehr benötigen. Die Ressourcen können mithilfe des Befehls [az group delete](/cli/azure/group#az-group-delete) entfernt werden. Um die Ressourcengruppe und alle dazugehörigen Ressourcen zu löschen, führen Sie den folgenden Befehl aus:

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen einfachen virtuellen Linux-Server mit einem Webserver bereitgestellt. Um weitere Informationen zu virtuellen Computern unter Azure Stack zu erhalten, fahren Sie mit [Considerations for Virtual Machines in Azure Stack](azure-stack-vm-considerations.md) (Überlegungen zu virtuellen Computern in Azure Stack) fort.
