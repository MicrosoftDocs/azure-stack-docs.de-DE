---
title: Aktivieren der Azure CLI für Azure Stack Hub-Benutzer
description: Erfahren Sie, wie Sie die plattformübergreifende Befehlszeilenschnittstelle (Command-Line Interface, CLI) aktivieren, um Ressourcen in Azure Stack Hub zu verwalten und bereitzustellen.
author: mattbriggs
ms.topic: article
ms.date: 5/27/2020
ms.author: mabrigg
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 39cb4e97c0a73b1299df3f84ce45d7f4db820262
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94544257"
---
# <a name="enable-azure-cli-for-azure-stack-hub-users"></a>Aktivieren der Azure CLI für Azure Stack Hub-Benutzer

Sie können das CA-Stammzertifikat Benutzern in Azure Stack Hub zur Verfügung stellen, damit sie die Azure CLI auf ihren Entwicklungscomputern aktivieren können. Ihre Benutzer benötigen das Zertifikat, um Ressourcen über die CLI zu verwalten.

 - Das **Azure Stack Hub-Zertifizierungsstellen-Stammzertifikat** ist erforderlich, wenn Benutzer die CLI auf einer Arbeitsstation außerhalb von Azure Stack Development Kit (ASDK) verwenden.  

 - Der **Endpunkt der VM-Aliase** (Virtual Machines, virtuelle Computer) stellt einen Alias wie z. B. „UbuntuLTS“ oder „Win2012Datacenter“ bereit, der beim Bereitstellen von VMs als einzelner Parameter auf einen Herausgeber, ein Angebot, eine SKU und die Version eines Image verweist.  

In den folgenden Abschnitten wird beschrieben, wie Sie diese Werte abrufen.

## <a name="export-the-azure-stack-hub-ca-root-certificate"></a>Exportieren des Zertifizierungsstellen-Stammzertifikats für Azure Stack Hub

Wenn Sie ein integriertes System verwenden, müssen Sie das Stammzertifikat der Zertifizierungsstelle nicht exportieren. Sie müssen das Zertifizierungsstellen-Stammzertifikat des ASDK exportieren.

Um das ASDK-Stammzertifikat im PEM-Format zu exportieren, melden Sie sich an, und führen Sie das folgende Skript aus:

```powershell
$label = "AzureStackSelfSignedRootCert"
Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
$root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
if (-not $root)
{
    Write-Error "Certificate with subject CN=$label not found"
    return
}

Write-Host "Exporting certificate"
Export-Certificate -Type CERT -FilePath root.cer -Cert $root

Write-Host "Converting certificate to PEM format"
certutil -encode root.cer root.pem
```

## <a name="set-up-the-vm-aliases-endpoint"></a>Einrichten des Endpunkts der VM-Aliase

Azure Stack Hub-Betreiber sollten einen öffentlich zugänglichen Endpunkt einrichten, der eine VM-Aliasdatei hostet. Bei der VM-Aliasdatei handelt es sich um eine JSON-Datei, die einen allgemeinen Namen für ein Image bereitstellt. Sie verwenden den Namen, wenn Sie eine VM bereitstellen, als Azure CLI-Parameter.  

Stellen Sie vor dem Hinzufügen eines Eintrags zu einer Aliasdatei sicher, dass Sie [Images aus dem Azure Marketplace heruntergeladen](azure-stack-download-azure-marketplace-item.md) oder [ein eigenes benutzerdefiniertes Image veröffentlicht](azure-stack-add-vm-image.md) haben. Wenn Sie ein benutzerdefiniertes Image veröffentlichen, notieren Sie sich die während der Veröffentlichung angegebenen Informationen zu Herausgeber, Angebot, SKU und Version. Wenn es sich um ein Image aus dem Marketplace handelt, können Sie die Informationen mithilfe des `Get-AzureVMImage`-Cmdlets anzeigen.  

Eine [Aliasbeispieldatei](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) mit zahlreichen allgemeinen Imagealiasen ist verfügbar. Sie können diese als Ausgangspunkt verwenden. Hosten Sie diese Datei an einem Ort, auf den die CLI-Clients zugreifen können. Eine Möglichkeit besteht darin, die Datei in einem Blob Storage-Konto zu hosten und die URL für Ihre Benutzer freizugeben:

1. Laden Sie die [Beispieldatei](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) von GitHub herunter.
2. Erstellen Sie ein Speicherkonto in Azure Stack Hub. Erstellen Sie anschließend einen Blobcontainer. Legen Sie die Zugriffsrichtlinie auf „Öffentlich“ fest.  
3. Laden Sie die JSON-Datei in den neuen Container hoch. Wenn das erledigt ist, können Sie die URL des Blobs anzeigen. Wählen Sie den Blobnamen aus, und wählen Sie dann die URL in den Blobeigenschaften aus.

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen von Vorlagen mit der Azure CLI](../user/azure-stack-deploy-template-command-line.md )
- [Verbinden mit PowerShell](powershell-install-az-module.md)
- [Verwalten von Benutzerberechtigungen](azure-stack-manage-permissions.md)
