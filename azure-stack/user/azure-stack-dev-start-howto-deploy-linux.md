---
title: Bereitstellen einer Linux-VM in Azure Stack | Microsoft-Dokumentation
description: Bereitstellen einer App in Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 7662b696ce25cdb70f34824984f4d1ce14d88c69
ms.sourcegitcommit: 41927cb812e6a705d8e414c5f605654da1fc6952
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/25/2019
ms.locfileid: "64482350"
---
# <a name="deploy-a-linux-vm-to-host-a-web-app-in-azure-stack"></a>Bereitstellen einer Linux-VM zum Hosten einer Web-App in Azure Stack

Sie können eine einfache Linux-VM mit dem Ubuntu-Image im Marketplace erstellen und bereitstellen, um eine Web-App zu hosten, die mit einem Webframework erstellt wurde. Diese VM kann Web-Apps mithilfe folgender Komponenten und Elemente hosten:

- **Python**. Häufige Python-Webframeworks wie Flask, Bottle und Django.
- **Go**. Häufige GO-Frameworks wie Revel, Martini, Gocraft/Web und Gorilla. 
- **Ruby**. Sie können Ruby on Rails als Framework zum Bereitstellen Ihrer Ruby-Web-Apps einrichten. 
- **Java**. Java kann zum Entwickeln von Web-Apps verwendet werden, die auf einem Apache Tomcat-Server gehostet werden. Sie können Tomcat auf Linux installieren und Ihre Java-WAR-Dateien direkt auf dem Server bereitstellen. 

Nutzen Sie die Anleitungen in diesem Artikel, um beliebige Web-Apps, Frameworks und Back-End-Technologien einzurichten und zu betreiben, die das Linux-Betriebssystem verwenden. Mit Azure Stack verwalten Sie Ihre Infrastruktur, und mit den Verwaltungstools der von Ihnen verwendeten Technologie führen Sie Wartungsaufgaben für Ihre App aus.

## <a name="deploy-a-linux-vm-for-a-web-app"></a>Bereitstellen einer Linux-VM für eine Web-App

Sie erstellen einen geheimen Schlüssel, verwenden das Basisimage der Linux-VM, geben die spezifischen Attribute der VM an und erstellen dann die VM. Wenn die VM erstellt wurde, öffnen Sie die Ports, die Sie benötigen, damit Sie mit der VM arbeiten können und damit die VM Ihre App hosten kann. Sie erstellen auch den DNS-Namen. Schließlich stellen Sie eine Verbindung mit der VM her, und aktualisieren den Computer mithilfe von „apt-get“. Wenn Sie diesen Artikel durchgearbeitet haben, verfügen Sie über eine VM in Ihrer Azure Stack-Instanz, die auf das Hosten Ihrer Web-App vorbereitet ist.

Sie können direkt mit den Anleitungen beginnen oder erst sicherstellen, dass alle Voraussetzungen erfüllt sind.

## <a name="prerequisites-and-considerations"></a>Voraussetzungen und Überlegungen

- Sie benötigen ein Azure Stack-Abonnement.
- Das Abonnement benötigt Zugriff auf das Image **Ubuntu Server 16.04 LTS**. Sie können eine höhere Version dieses Images verwenden, die vorliegenden Anleitungen wurden aber für 16.04 LTS konzipiert. Wenn Sie nicht über dieses Image verfügen, wenden Sie sich an Ihren Cloudbetreiber, damit das Image in Ihrem Azure Stack Marketplace bereitgestellt wird.

<!-- Azure Stack specific considerations

### Authentication

Azure Stack works with two identity management services, Azure Active Directory (Azure AD) and Active Directory Federated Services (AD FS).  This section addresses how this procedure will work with either version.

### Connectivity

Azure Stack can be run in connected to completely disconnected scenarios. This section addresses considerations about the use case in relation to connectivity.

### Azure Stack Development Kit and Integrated Systems

While the two version of the product are the same product both version behave differently. Call out considerations about either version. 

### Azure Stack version

Place any version specific calls outs. The procedure will contain steps for the latest version. This section will contain call outs for previous version that are still supported. -->

## <a name="deploy-vm-using-the-portal"></a>Bereitstellen der VM über das Portal

Erstellen Sie mithilfe einer App wie z.B. PuTTY einen öffentlichen SSH-Schlüssel für Ihren Server. Greifen Sie auf Ihr Azure Stack-Portal zu, und fügen Sie den Ubuntu-Server hinzu. Richten Sie Ihr Netzwerk und Ihren DNS-Eintrag ein. Stellen Sie eine Verbindung mit Ihrem Server her, um ein Update durchzuführen.

### <a name="create-your-vm"></a>Erstellen Ihres virtuellen Computers

1. Erstellen Sie einen öffentlichen SSH-Schlüssel für Ihren Server. Weitere Informationen finden Sie unter [Verwenden eines öffentlichen SSH-Schlüssels](azure-stack-dev-start-howto-ssh-public-key.md).
2. Öffnen Sie Ihr Azure Stack-Portal.
3. Wählen Sie **Ressource erstellen** > **Compute** > **Ubuntu Server 16.04 LTS** aus.

    ![Bereitstellen einer Web-App auf einer Azure Stack-VM](media/azure-stack-dev-start-howto-deploy-linux/001-portal-compute.png)

4. Gehen Sie auf dem Blatt **Virtuellen Computer erstellen** unter **1. Grundeinstellungen konfigurieren** folgendermaßen vor:
    1. Geben Sie den **Namen Ihrer VM** ein.
    1. Wählen Sie den **VM-Datenträgertyp** aus: **SSD Premium** oder **HDD Standard**.
    1. Geben Sie Ihren **Benutzernamen** ein.
    1. Wählen Sie **Öffentlicher SSH-Schlüssel** als **Authentifizierungstyp** aus.
    1. Rufen Sie den öffentlichen SSH-Schlüssel ab, den Sie erstellt haben. Öffnen Sie den Schlüssel in einem Text-Editor. Kopieren Sie den Schlüssel, und fügen Sie ihn in das Feld **Öffentlicher SSH-Schlüssel** ein. Schließen Sie den Text von `---- BEGIN SSH2 PUBLIC KEY ----` bis `---- END SSH2 PUBLIC KEY ----` ein. Fügen Sie den gesamten Textblock in das Schlüsselfeld ein:
       ```text  
            ---- BEGIN SSH2 PUBLIC KEY ----
            Comment: "rsa-key-20190207"
            <Your key block>
            ---- END SSH2 PUBLIC KEY ----```
    1. Select your Azure Stack subscription.
    1. Create a new **Resource group** or use an existing depending on how you want to organize the resources for your app.
    1. Select your location. The ASDK is usually in a **local** region. The location will depend on your Azure Stack.
1. For **2. Size** type:
    - Select the size of data and RAM for your VM that is available in your Azure Stack.
    - You can either browse the list or filter for the size of your VM by **Compute type**, **CPUs**, and storage space.
    - Prices presented are estimates in your local currency that include only Azure infrastructure costs and any discounts for the subscription and location. The prices don't include any applicable software costs. Recommended sizes are determined by the publisher of the selected image based on hardware and software requirements.
    - Using a standard disk rather than a premium disk could impact operating system performance.

1. in **3. Configure optional** features type:
    1. For **High availability,** you can select an availability set. To provide redundancy to your application, you can group two or more virtual machines in an availability set. This configuration ensures that during a planned or unplanned maintenance event, at least one virtual machine will be available and meet the 99.95% Azure SLA. The availability set of a virtual machine can't be changed after it is created.
    1. For **Storage** select **Premium disks (SSD)** or **Standard disks (HDD)**. Premium disks (SSD) are backed by solid-state drives and offer consistent, low-latency performance. They provide the best balance between price and performance, and are ideal for I/O-intensive applications and production workloads. Standard disks (HDD) are backed by magnetic drives and are preferable for applications where data is accessed infrequently. Zone- redundant disks are backed by Zone redundant storage (ZRS) that replicates your data across multiple zones and are available even if a single zone is down. 
    1. You can select **Use managed disks**. Enable this feature to have Azure automatically manage the availability of disks to provide data redundancy and fault tolerance, without creating and managing storage accounts on your own. Managed disks may not be available in all regions. For more information, see [Introduction to Azure managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).
    1. Select **virtual network** to configure your network. Virtual networks are logically isolated from each other in Azure. You can configure their IP address ranges, subnets, route tables, gateways, and security settings, much like a traditional network in your data center. Virtual machines in the same virtual network can access each other by default. 
    1. Select **subnet** to configure your subnet. A subnet is a range of IP addresses in your virtual network, which can be used to isolate virtual machines from each other or from the Internet. 
    1. Select **Public IP address** to configure access to your VM or services running on your VM. Use a public IP address if you want to communicate with the virtual machine from outside the virtual network. 
    1. Select **Network Security Group**, **Basic, or **Advanced**. Set rules that allow or deny network traffic to the VM 
    1. Select **public inbound ports** to set access for common or custom protocols to your VM. The service specifies the destination protocol and port range for this rule. You can choose a predefined service, like RDP or SSH, or provide a custom port range.  
        For the  web server, you are going to want to HTTP (80), HTTPS (443), and SSH (22) open. If you plan on managing the machine with an RDP connection, open port 3389.
    1. Select **Extensions** if you would like to add Extension to your VM. Extensions add new features, like configuration management or antivirus protection, to your virtual machine using extensions. 
    1. Disable or enable **Monitoring**. Capture serial console output and screenshots of the virtual machine running on a host to help diagnose startup issues. 
    1. Select **diagnostics storage account** to specify the storage account holding your metrics. Metrics are written to a storage account so you can analyze them with your own tools. . 
    1. Select **OK**.
1. Review **4. Summary**:
    - The portal validates your settings.
    - You can download the Azure Resource Manager template for your VM if you would like to reuse your settings with an Azure Resource Manager workflow.
    - Press **OK** when the validation has passed. The deployment of the VM takes several minutes.

### Specify the open ports and DNS name

You will want to make your web app accessible to users on your network by opening the ports used to connect to the machine and adding a friendly DNS name such as `mywebapp.local.cloudapp.azurestack.external` that users can use in their web browsers.

#### Open inbound ports

You can modify the destination protocol and port range for predefined service, like RDP or SSH or provide a custom port range. For example, you may want to work with the port range of your web framework. GO, for instance, communicates on port 3000.

1. Open the Azure Stack portal for your tenant.
1. Find your VM. You may have pinned the VM to your dashboard, or you can search for the VM in the **Search resources** box.
1. Select **Networking** in your VM blade.
1. Select **Add inbound port** rule to open a port.
1. For Source, leave the default to **Any**.
1. For Source port range, leave the wildcard (*).
1. For Destination port range, add the port you would like to open, such as `3000`.
1. For **Protocol** leave **Any**.
1. For **Action** set to **Allow**.
1. For **Priority** leave for the default.
1. Type a **Name** and **Description** to help you remember why the port is open.
1. Select **Add**.

#### Add a DNS name for your server

In addition, you can create a DNS name for your server, and then users can connect to your web site using a URL.

1. Open the Azure Stack portal for your tenant.
1. Find your VM. You may have pinned the VM to your dashboard, or you can search for the VM in the **Search resources** box.
1. Select **Overview**.
1. Select **Configure** under VM.
1. Select **Dynamic** for **Assignment**.
1. Type the DNS name label such as `mywebapp` so that your full URL will be: `mywebapp.local.cloudapp.azurestack.external` (for an ASDK app).

### Connect via SSH to update your VM

1. On the same network as your Azure Stack, open your SSH client. For more information, see [How to use an SSH public key](azure-stack-dev-start-howto-ssh-public-key.md).
1. Type:
    ```bash  
        sudo apt-get update
        sudo apt-get -y upgrade
    ```

<!--

## Deploy VM using the PowerShell

Include a sentence or two to explain only what is needed to complete the procedure.

1. Step one of the procedures.

    | Parameter | Example | Description |
    | --- | --- | --- |
    | item      | "dog"   | Describe what it is and where to find the information. |

2. Step two of the procedure

    ```PowerShell  
    verb-command -item "dog"
    ```

3. Step three of the procedures.

    ```PowerShell  
    verb-command -item "dog"
    ```

4. Step four of the procedures.

    ```PowerShell  
    verb-command -item "dog"
    ```

## Deploy VM using the CLI

Include a sentence or two to explain only what is needed to complete the procedure.

1. Step one of the procedures.

    | Parameter | Example | Description |
    | --- | --- | --- |
    | item      | "dog"   | Describe what it is and where to find the information. |

2. Step two of the procedure

    ```CLI  
    verb-command -item "dog"
    ```

3. Step three of the procedures.

    ```CLI  
    verb-command -item "dog"
    ```

4. Step four of the procedures.

    ```CLI  
    verb-command -item "dog"
    ```

-->

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum [Entwickeln für Azure Stack](azure-stack-dev-start.md)