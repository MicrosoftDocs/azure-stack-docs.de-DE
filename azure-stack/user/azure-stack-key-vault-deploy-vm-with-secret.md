---
title: Bereitstellen einer Azure Stack-VM mithilfe eines in Key VM gespeicherten Kennworts | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie einen virtuellen Computer mithilfe eines Kennworts bereitstellen, das in einem Azure Stack-Schlüsseltresor gespeichert ist.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 2a75ee1794e9ebfeb995ea03137d12c6c50cce4f
ms.sourcegitcommit: a7207f4a4c40d4917b63e729fd6872b3dba72968
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/03/2019
ms.locfileid: "71909506"
---
# <a name="deploy-an-azure-stack-vm-using-a-password-stored-in-key-vault"></a>Bereitstellen einer Azure Stack-VM mithilfe eines in Key VM gespeicherten Kennworts

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

In diesem Artikel erfahren Sie Schritt für Schritt, wie Sie einen virtuellen Windows Server-Computer mit einem sicheren Kennwort bereitstellen, das in einer Azure Stack Key Vault-Instanz gespeichert ist. Die Verwendung eines Schlüsseltresorkennworts ist sicherer als die Übergabe eines Nur-Text-Kennworts.

## <a name="overview"></a>Übersicht

Sie können Werte (etwa Kennwörter) als Geheimnis in einem Azure Stack-Schlüsseltresor speichern. Auf das erstellte Geheimnis kann dann in Azure Resource Manager-Vorlagen verwiesen werden. Die Verwendung von Geheimnissen mit Resource Manager bietet folgende Vorteile:

* Das Geheimnis muss nicht bei jeder Bereitstellung einer Ressource manuell eingegeben werden.
* Sie können angeben, welche Benutzer oder Dienstprinzipale Zugriff auf ein Geheimnis haben sollen.

## <a name="prerequisites"></a>Voraussetzungen

* Sie müssen ein Angebot abonnieren, das den Key Vault-Dienst umfasst.
* [Installieren Sie PowerShell für Azure Stack-](../operator/azure-stack-powershell-install.md)
* [Konfigurieren der PowerShell-Umgebung](azure-stack-powershell-configure-user.md)

Die folgenden Schritte beschreiben das Verfahren zum Erstellen eines virtuellen Computers durch Abrufen des Kennworts, das in einem Schlüsseltresor gespeichert ist:

1. Erstellen eines Geheimnisses im Schlüsseltresor
2. Aktualisieren Sie die `azuredeploy.parameters.json`-Datei.
3. Stellen Sie die Vorlage bereit.

> [!NOTE]  
> Führen Sie die Schritte über das Azure Stack Development Kit (ASDK) oder über einen externen Client aus, wenn eine Verbindung per VPN besteht.

## <a name="create-a-key-vault-secret"></a>Erstellen eines Geheimnisses im Schlüsseltresor

Das folgende Skript erstellt einen Schlüsseltresor und speichert ein Kennwort als Geheimnis im Schlüsseltresor. Verwenden Sie beim Erstellen des Schlüsseltresors den `-EnabledForDeployment`-Parameter. Dieser Parameter stellt sicher, dass von Azure Resource Manager-Vorlagen auf den Schlüsseltresor verwiesen werden kann.

```powershell

$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "MySecret"

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location
  -EnabledForTemplateDeployment

$secretValue = ConvertTo-SecureString -String '<Password for your virtual machine>' -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
  -SecretValue $secretValue

```

Wenn Sie das vorherige Skript ausführen, enthält die Ausgabe den URI (Uniform Resource Identifier) des Geheimnisses. Notieren Sie sich diesen URI. Sie müssen in der [Vorlage zum Bereitstellung eines virtuellen Windows-Computers mit Kennwort in einem Schlüsseltresor](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv) darauf verweisen. Laden Sie den Ordner [101-vm-secure-password](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv) auf den Entwicklungscomputer herunter. Dieser Ordner enthält die Dateien `azuredeploy.json` und `azuredeploy.parameters.json`, die Sie in den nächsten Schritten benötigen.

Ändern Sie die Datei `azuredeploy.parameters.json` gemäß den Werten Ihrer Umgebung. Die Parameter, die hier von besonderem Interesse sind, sind der Tresorname, die Tresorressourcengruppe und der URI des Geheimnisses (vom vorherigen Skript erstellt). Die unten stehende Datei ist ein Beispiel für eine Parameterdatei.

## <a name="update-the-azuredeployparametersjson-file"></a>Aktualisieren der Datei „azuredeploy.parameters.json“

Aktualisieren Sie die Datei `azuredeploy.parameters.json` gemäß Ihrer Umgebung mit den Werten für den Schlüsseltresor-URI, den Geheimnisnamen und den Administratorbenutzernamen des virtuellen Computers. Die folgende JSON-Datei zeigt ein Beispiel der Vorlagenparameterdatei:

```json
{
    "$schema":  "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion":  "1.0.0.0",
    "parameters":  {
       "adminUsername":  {
         "value":  "demouser"
          },
       "adminPassword":  {
         "reference":  {
            "keyVault":  {
              "id":  "/subscriptions/xxxxxx/resourceGroups/RgKvPwd/providers/Microsoft.KeyVault/vaults/KvPwd"
              },
            "secretName":  "MySecret"
         }
       },
       "dnsLabelPrefix":  {
          "value":  "mydns123456"
        },
        "windowsOSVersion":  {
          "value":  "2016-Datacenter"
        }
    }
}

```

## <a name="template-deployment"></a>Bereitstellung von Vorlagen

Stellen Sie nun die Vorlage mithilfe des folgenden PowerShell-Skripts bereit:

```powershell  
New-AzureRmResourceGroupDeployment `
  -Name KVPwdDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

Wenn die Vorlage erfolgreich bereitgestellt wurde, erhalten Sie die folgende Ausgabe:

![Ausgabe der Bereitstellung](media/azure-stack-key-vault-deploy-vm-with-secret/deployment-output.png)

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen einer Beispiel-App mit Key Vault](azure-stack-key-vault-sample-app.md)
* [Bereitstellen eines virtuellen Computers mit einem Key Vault-Zertifikat](azure-stack-key-vault-push-secret-into-vm.md)
