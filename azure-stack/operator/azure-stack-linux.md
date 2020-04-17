---
title: Hinzufügen von Linux-Images zum Azure Stack Hub-Marketplace
description: Hier erfahren Sie, wie Sie Linux-Images zum Azure Stack Hub-Marketplace hinzufügen.
author: sethmanheim
ms.topic: article
ms.date: 01/23/2020
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/16/2019
ms.openlocfilehash: 532f61b2b306dab833c35dab403226e70950d43a
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "79295095"
---
# <a name="add-linux-images-to-the-azure-stack-hub-marketplace"></a>Hinzufügen von Linux-Images zum Azure Stack Hub-Marketplace

Sie können virtuelle Linux-Computer in Azure Stack bereitstellen, indem Sie ein Linux-basiertes Image im Azure Stack Hub-Marketplace hinzufügen. Am einfachsten wird ein Linux-Image über die Marketplace-Verwaltung zu Azure Stack Hub hinzugefügt. Diese Images wurden vorbereitet und auf Kompatibilität mit Azure Stack Hub getestet.

## <a name="marketplace-management"></a>Marketplace-Verwaltung

Informationen zum Herunterladen von Linux-Images aus dem Azure Marketplace finden Sie unter [Herunterladen von Marketplace-Elementen von Azure in Azure Stack Hub](azure-stack-download-azure-marketplace-item.md). Wählen Sie die Linux-Images aus, die Sie Benutzern in Azure Stack Hub zur Verfügung stellen möchten.

Diese Images werden regelmäßig aktualisiert. Überprüfen Sie daher immer wieder die Marketplace-Verwaltung, um auf dem neuesten Stand zu bleiben.

## <a name="prepare-your-own-image"></a>Vorbereiten eines eigenen Image

Laden Sie nach Möglichkeit die Images herunter, die über die Marketplace-Verwaltung verfügbar sind. Diese Images wurden für Azure Stack Hub vorbereitet und getestet.

### <a name="azure-linux-agent"></a>Azure Linux-Agent

Der Azure Linux-Agent (in der Regel **WALinuxAgent** oder **walinuxagent**) ist erforderlich, es können aber nicht alle Versionen des Agents mit Azure Stack Hub verwendet werden. Versionen zwischen 2.2.21 und 2.2.34 (einschließlich) werden in Azure Stack Hub nicht unterstützt. Um die aktuellen Agent-Versionen über 2.2.35 zu verwenden, installieren Sie den Hotfix 1901 bzw. 1902, oder aktualisieren Sie Azure Stack Hub auf die Version 1903 (oder höher). Beachten Sie, dass [cloud-init](https://cloud-init.io/) erst ab Version 1910 in Azure Stack Hub unterstützt wird.

| Azure Stack Hub-Build | Azure Linux-Agent-Build |
| ------------- | ------------- |
| 1.1901.0.99 oder früher | 2.2.20 |
| 1.1902.0.69  | 2.2.20  |
|  1.1901.3.105   | 2.2.35 oder höher |
| 1.1902.2.73  | 2.2.35 oder höher |
| 1.1903.0.35  | 2.2.35 oder höher |
| Builds nach 1903 | 2.2.35 oder höher |
| Nicht unterstützt | 2.2.21-2.2.34 |
| Builds nach 1910 | Alle Azure WALA-Agentversionen|

Sie können Ihr eigenes Linux-Image gemäß den folgenden Anweisungen vorbereiten:

* [CentOS-basierte Verteilungen](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
* [SLES &amp; openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu Server](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="cloud-init"></a>cloud-init

[cloud-init](https://cloud-init.io/) wird erst ab Version 1910 in Azure Stack Hub unterstützt. Wenn Sie „cloud-init“ zum Anpassen Ihres virtuellen Linux-Computers verwenden möchten, können Sie die folgenden PowerShell-Anweisungen verwenden:

### <a name="step-1-create-a-cloud-inittxt-file-with-your-cloud-config"></a>Schritt 1: Erstellen der Datei „cloud-init.txt“ mit Ihrer cloud-config-Datei

Erstellen Sie eine Datei namens „cloud-init.txt“, und fügen Sie die folgende Cloudkonfiguration ein.

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
    path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
    path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
  ```
  
### <a name="step-2-reference-the-cloud-inittxt-during-the-linux-vm-deployment"></a>Schritt 2: Verweisen auf „cloud-init.txt“ während der Linux-VM-Bereitstellung

Laden Sie die Datei in ein Azure Storage-Konto, ein Azure Stack Hub-Speicherkonto oder ein GitHub-Repository hoch, das von Ihrer Azure Stack Hub-Linux-VM erreichbar ist.
Derzeit wird die Verwendung von „cloud-init“ für die VM-Bereitstellung nur für REST, PowerShell und die CLI unterstützt. Eine entsprechende Portalbenutzeroberfläche in Azure Stack Hub ist nicht vorhanden.

Sie können [diesen](../user/azure-stack-quick-create-vm-linux-powershell.md) Anweisungen folgen, um den virtuellen Linux-Computer mithilfe von PowerShell zu erstellen. Achten Sie jedoch darauf, als Teil des `-CustomData`-Flags auf die Datei „cloud-init.txt“ zu verweisen:

```powershell
$VirtualMachine =Set-AzureRmVMOperatingSystem -VM $VirtualMachine `
  -Linux `
  -ComputerName "MainComputer" `
  -Credential $cred -CustomData "#include https://cloudinitstrg.blob.core.windows.net/strg/cloud-init.txt"
```

## <a name="add-your-image-to-marketplace"></a>Hinzufügen Ihres Images zum Marketplace

Gehen Sie wie unter [Verfügbarmachen eines VM-Images in Azure Stack](azure-stack-add-vm-image.md) beschrieben vor. Stellen Sie sicher, dass der `OSType`-Parameter auf `Linux` festgelegt ist.

Nachdem Sie das Image zum Marketplace hinzugefügt haben, wird ein Marketplace-Element erstellt, und Benutzer können einen virtuellen Linux-Computer bereitstellen.

## <a name="next-steps"></a>Nächste Schritte

* [Herunterladen von Marketplace-Elementen von Azure in Azure Stack Hub](azure-stack-download-azure-marketplace-item.md)
* [Azure Stack Hub-Marketplace – Übersicht](azure-stack-marketplace.md)
