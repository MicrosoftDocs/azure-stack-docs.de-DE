---
title: Erstellen eines virtuellen Linux-Computers mithilfe der Azure CLI in Azure Stack Hub
description: Erstellen Sie einen virtuellen Linux-Computer mithilfe der Azure CLI in Azure Stack Hub.
author: mattbriggs
ms.topic: quickstart
ms.date: 1/22/2020
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 49b2a950b523ca76143f50784efb048880d1ac93
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77701937"
---
# <a name="quickstart-create-a-linux-server-vm-by-using-the-azure-cli-in-azure-stack-hub"></a>Schnellstart: Erstellen eines virtuellen Linux-Servers mithilfe der Azure CLI in Azure Stack Hub

Sie können mithilfe der Azure CLI einen virtuellen Computer mit Ubuntu Server 16.04 LTS erstellen. In diesem Artikel erstellen und verwenden Sie einen virtuellen Computer. In diesem Artikel erhalten Sie außerdem Informationen zu den folgenden Schritten:

* Herstellen der Verbindung mit dem virtuellen Computer über einen Remoteclient
* Installieren eines NGINX-Webservers und Anzeigen der Standardstartseite
* Bereinigen nicht mehr benötigter Ressourcen

## <a name="prerequisites"></a>Voraussetzungen

* Ein Linux-Image im Azure Stack Hub-Marketplace

   Im Azure Stack Hub-Marketplace ist nicht standardmäßig ein Linux-Image enthalten. Bitten Sie den Azure Stack Hub-Betreiber, das benötigte Image „Ubuntu Server 16.04 LTS“ bereitzustellen. Hierzu kann der Betreiber die Anweisungen unter [Herunterladen von Marketplace-Elementen in Azure Stack Hub](../operator/azure-stack-download-azure-marketplace-item.md) befolgen.

* Azure Stack Hub erfordert für die Ressourcenerstellung und -verwaltung eine spezifische Version der Azure CLI. Falls die Azure CLI nicht für Azure Stack Hub konfiguriert ist, melden Sie sich beim [Azure Stack Development Kit](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) (oder im Falle einer [VPN-Verbindung](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) bei einem Windows-basierten externen Client) an, und befolgen Sie die Anweisungen zum [Installieren und Konfigurieren der Azure CLI](azure-stack-version-profiles-azurecli2.md).

* Ein öffentlicher SSH-Schlüssel (Secure Shell) mit dem Namen *id_rsa.pub* im SSH-Verzeichnis ( *.ssh*) Ihres Windows-Benutzerprofils. Ausführliche Informationen zum Erstellen von SSH-Schlüsseln finden Sie unter [Verwenden eines öffentlichen SSH-Schlüssels](azure-stack-dev-start-howto-ssh-public-key.md).

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Ressourcengruppe ist ein logischer Container, in dem Sie Azure Stack Hub-Ressourcen bereitstellen und verwalten können. Führen Sie im Development Kit oder im integrierten Azure Stack Hub-System den Befehl [az group create](/cli/azure/group#az-group-create) aus, um eine Ressourcengruppe zu erstellen.

> [!NOTE]
> Allen Variablen in den folgenden Codebeispielen wurden Werte zugewiesen. Sie können jedoch Ihre eigenen Werte zuweisen.

Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen myResourceGroup am lokalen Speicherort erstellt: 

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

Erstellen Sie mit dem Befehl [az vm create](/cli/azure/vm#az-vm-create) einen virtuellen Computer. Im folgenden Beispiel wird ein virtueller Computer namens „myVM“ erstellt. In diesem Beispiel wird *Demouser* als Benutzername und *Demouser@123* als Kennwort für den Administrator verwendet. Ändern Sie diese Werte in für Ihre Umgebung geeignete Werte.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "UbuntuLTS" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --location local
```

Die öffentliche IP-Adresse wird im Parameter **PublicIpAddress** zurückgegeben. Notieren Sie sich die Adresse zur späteren Verwendung mit dem virtuellen Computer.

## <a name="open-port-80-for-web-traffic"></a>Öffnen von Port 80 für Webdatenverkehr

Da dieser virtuelle Computer als IIS-Webserver fungieren soll, muss Port 80 für Internetdatenverkehr geöffnet werden. Verwenden Sie zum Öffnen von Port 80 den Befehl [az vm open-port](/cli/azure/vm): 

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="use-ssh-to-connect-to-the-virtual-machine"></a>Herstellen einer SSH-Verbindung mit dem virtuellen Computer

Stellen Sie über einen Clientcomputer mit installierter SSH eine Verbindung mit dem virtuellen Computer her. Verwenden Sie auf einem Windows-Client [PuTTY](https://www.putty.org/) zum Herstellen der Verbindung. Führen Sie den folgenden Befehl aus, um eine Verbindung mit dem virtuellen Computer herzustellen:

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

Nachdem der NGINX-Webserver installiert und Port 80 auf dem virtuellen Computer geöffnet wurde, können Sie über die öffentliche IP-Adresse des virtuellen Computers auf den Webserver zugreifen. Öffnen Sie dazu einen Browser, und navigieren Sie zu ```http://<public IP address>```.

![Willkommensseite des NGINX-Webservers](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Bereinigen Sie die Ressourcen, die Sie nicht mehr benötigen. Sie können mithilfe des Befehls [az group delete](/cli/azure/group#az-group-delete) entfernt werden. Führen Sie den folgenden Befehl aus:

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen einfachen virtuellen Linux-Server mit einem Webserver bereitgestellt. Weitere Informationen zu virtuellen Azure Stack Hub-Computern finden Sie unter [Features von Azure Stack Hub-VMs](azure-stack-vm-considerations.md).
