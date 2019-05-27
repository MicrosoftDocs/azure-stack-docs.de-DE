---
title: Erstellen eines virtuellen Linux-Computers mithilfe der Azure CLI in Azure Stack | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie einen virtuellen Linux-Computer mithilfe der Befehlszeilenschnittstelle in Azure Stack erstellen.
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
ms.date: 05/16/2019
ms.author: mabrigg
ms.custom: mvc
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 19c856bdf981775b0b3a8ee923046b51e6ef79ca
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782776"
---
# <a name="quickstart-create-a-linux-server-virtual-machine-using-azure-cli-in-azure-stack"></a>Schnellstart: Erstellen eines virtuellen Linux-Servers mithilfe der Azure CLI in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Sie können einen virtuellen Computer mit Ubuntu Server 16.04 LTS mithilfe der Azure CLI erstellen. Befolgen Sie die Schritte in diesem Artikel zum Erstellen und Verwenden eines virtuellen Computers. In diesem Artikel führen Sie auch die folgenden Schritte aus:

* Herstellen der Verbindung mit dem virtuellen Computer über einen Remoteclient
* Installieren des NGINX-Webservers und Anzeigen der Standardstartseite
* Bereinigen nicht mehr benötigter Ressourcen

## <a name="prerequisites"></a>Voraussetzungen

* **Ein Linux-Image im Azure Stack-Marketplace**

   Ein Linux-Image ist standardmäßig nicht im Azure Stack-Marketplace enthalten. Bitten Sie den Azure Stack-Operator, das benötigte Image **Ubuntu Server 16.04 LTS** bereitzustellen. Hierzu kann der Bediener die Schritte des Artikels [Herunterladen von Marketplace-Elementen von Azure in Azure Stack](../operator/azure-stack-download-azure-marketplace-item.md) ausführen.

* Azure Stack erfordert für die Ressourcenerstellung und -verwaltung eine spezifische Version der Azure CLI. Falls die Azure CLI nicht für Azure Stack konfiguriert ist, müssen Sie sich am [Development Kit](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) (oder im Falle einer [VPN-Verbindung](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) an einem Windows-basierten externen Client) anmelden und die Schritte zum [Installieren und Konfigurieren der Azure CLI](azure-stack-version-profiles-azurecli2.md) ausführen.

* Ein öffentlicher SSH-Schlüssel mit dem Namen „id_rsa.pub“ im SSH-Verzeichnis Ihres Windows-Benutzerprofils. Ausführliche Informationen zum Erstellen von SSH-Schlüsseln finden Sie unter [Verwenden eines öffentlichen SSH-Schlüssels](azure-stack-dev-start-howto-ssh-public-key.md).

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Ressourcengruppe ist ein logischer Container, in dem Sie Azure Stack-Ressourcen bereitstellen und verwalten können. Führen Sie im Development Kit oder im integrierten Azure Stack-System den Befehl [az group create](/cli/azure/group#az-group-create) aus, um eine Ressourcengruppe zu erstellen.

> [!NOTE]
> Allen Variablen in den Codebeispielen sind Werte zugewiesen. Sie können bei Bedarf aber auch neue Werte zuweisen.

Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen myResourceGroup am lokalen Speicherort erstellt: 

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

Erstellen Sie mit dem Befehl [az vm create](/cli/azure/vm#az-vm-create) einen virtuellen Computer. Im folgenden Beispiel wird ein virtueller Computer namens „myVM“ erstellt. In diesem Beispiel wird „Demouser“ als Benutzername und Demouser@123 als Kennwort für den Administrator verwendet. Ändern Sie diese Werte in für Ihre Umgebung geeignete Werte.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "UbuntuLTS" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --location local
```

Die öffentliche IP-Adresse wird im Parameter **PublicIpAddress** zurückgegeben. Notieren Sie sich diese Adresse, da Sie sie für die Nutzung des virtuellen Computers benötigen.

## <a name="open-port-80-for-web-traffic"></a>Öffnen von Port 80 für Webdatenverkehr

Da dieser virtuelle Computer als IIS-Webserver fungieren soll, muss Port 80 für Internetdatenverkehr geöffnet werden. Verwenden Sie zum Öffnen des gewünschten Ports den Befehl [az vm open-port](/cli/azure/vm): 

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

Nachdem NGINX installiert und der Port 80 auf dem virtuellen Computer geöffnet wurde, können Sie über die öffentliche IP-Adresse des virtuellen Computers auf den Webserver zugreifen. Öffnen Sie einen Browser, und navigieren Sie zu ```http://<public IP address>```.

![Willkommensseite des NGINX-Webservers](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Bereinigen Sie die Ressourcen, die Sie nicht mehr benötigen. Die Ressourcen können mithilfe des Befehls [az group delete](/cli/azure/group#az-group-delete) entfernt werden. Um die Ressourcengruppe und alle dazugehörigen Ressourcen zu löschen, führen Sie den folgenden Befehl aus:

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen einfachen virtuellen Linux-Server mit einem Webserver bereitgestellt. Um weitere Informationen zu virtuellen Computern unter Azure Stack zu erhalten, fahren Sie mit [Considerations for Virtual Machines in Azure Stack](azure-stack-vm-considerations.md) (Überlegungen zu virtuellen Computern in Azure Stack) fort.
