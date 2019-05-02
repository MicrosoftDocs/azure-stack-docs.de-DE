---
title: Bereitstellen eines virtuellen Computers mit einem sicher gespeicherten Zertifikat in Azure Stack | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie einen virtuellen Computer bereitstellen und mithilfe eines Schlüsseltresors in Azure Stack ein Zertifikat per Push auf ihn übertragen.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: 46590eb1-1746-4ecf-a9e5-41609fde8e89
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
origin.date: 12/27/2018
ms.date: 04/29/2019
ms.author: v-jay
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: 965b9c0cde96e1e29828f37cdddaa9d30c0503a4
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "64310908"
---
# <a name="create-a-virtual-machine-and-install-a-certificate-retrieved-from-an-azure-stack-key-vault"></a>Erstellen eines virtuellen Computers und Installieren eines Zertifikats, das aus einem Azure Stack-Schlüsseltresor abgerufen wurde

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Es wird beschrieben, wie Sie einen virtuellen Azure Stack-Computer mit installiertem Schlüsseltresorzertifikat erstellen.

## <a name="overview"></a>Übersicht

Zertifikate werden in vielen Szenarien verwendet, z.B. zur Authentifizierung für Active Directory oder zur Verschlüsselung von Webdatenverkehr. Sie können Zertifikate auf sichere Weise als Geheimnisse in einem Azure Stack-Schlüsseltresor speichern. Die Nutzung von Azure Stack Key Vault hat folgende Vorteile:

* Zertifikate werden per Skript, Befehlszeilenverlauf oder Vorlage nicht offengelegt.
* Der Zertifikatverwaltungsprozess wurde optimiert.
* Sie haben die Kontrolle über die Schlüssel, mit denen auf Zertifikate zugegriffen wird.

### <a name="process-description"></a>Beschreibung des Prozesses

Die folgenden Schritte beschreiben den Prozess, der zum Übertragen eines Zertifikats per Pushvorgang auf den virtuellen Computer erforderlich ist:

1. Erstellen eines Geheimnisses im Schlüsseltresor
2. Aktualisieren der Datei „azuredeploy.parameters.json“
3. Stellen Sie die Vorlage bereit.

> [!NOTE]
> Führen Sie die Schritte über das Azure Stack Development Kit oder über einen externen Client aus, wenn eine Verbindung per VPN besteht.

## <a name="prerequisites"></a>Voraussetzungen

* Sie müssen ein Angebot abonnieren, das den Key Vault-Dienst umfasst.
* [Installieren Sie PowerShell für Azure Stack](../operator/azure-stack-powershell-install.md).
* [Konfigurieren der PowerShell-Umgebung des Azure Stack-Benutzers](azure-stack-powershell-configure-user.md).

## <a name="create-a-key-vault-secret"></a>Erstellen eines Geheimnisses im Schlüsseltresor

Das folgende Skript erstellt ein Zertifikat im PFX-Format sowie einen Schlüsseltresor und speichert das Zertifikat als Geheimnis im Schlüsseltresor.

> [!IMPORTANT]
> Beim Erstellen des Schlüsseltresors muss der Parameter `-EnabledForDeployment` verwendet werden. Dieser Parameter stellt sicher, dass von Azure Resource Manager-Vorlagen auf den Schlüsseltresor verwiesen werden kann.

```powershell
# Create a certificate in the .pfx format
New-SelfSignedCertificate `
  -certstorelocation cert:\LocalMachine\My `
  -dnsname contoso.microsoft.com

$pwd = ConvertTo-SecureString `
  -String "<Password used to export the certificate>" `
  -Force `
  -AsPlainText

Export-PfxCertificate `
  -cert "cert:\localMachine\my\<Certificate Thumbprint that was created in the previous step>" `
  -FilePath "<Fully qualified path where the exported certificate can be stored>" `
  -Password $pwd

# Create a key vault and upload the certificate into the key vault as a secret
$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "servicecert"
$fileName = "<Fully qualified path where the exported certificate can be stored>"
$certPassword = "<Password used to export the certificate>"

$fileContentBytes = get-content $fileName `
  -Encoding Byte

$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$jsonObject = @"
{
"data": "$filecontentencoded",
"dataType" :"pfx",
"password": "$certPassword"
}
"@
$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -sku standard `
  -EnabledForDeployment

$secret = ConvertTo-SecureString `
  -String $jsonEncoded `
  -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
   -SecretValue $secret
```

Wenn Sie das vorherige Skript ausführen, enthält die Ausgabe den URI des Geheimnisses. Notieren Sie sich diesen URI. Sie müssen in der [Vorlage zum Übertragen des Zertifikats per Push an Windows Resource Manager](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate) auf ihn verweisen. Laden Sie den Ordner für die [Vorlage „vm-push-certificate-windows“](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate) auf Ihren Entwicklungscomputer herunter. Dieser Ordner enthält die Dateien `azuredeploy.json` und `azuredeploy.parameters.json`, die Sie in den nächsten Schritten benötigen.

Ändern Sie die Datei `azuredeploy.parameters.json` gemäß den Werten Ihrer Umgebung. Die Parameter, die hier von besonderem Interesse sind, sind der Tresorname, die Tresorressourcengruppe und der URI des Geheimnisses (vom vorherigen Skript erstellt). Der folgende Abschnitt enthält ein Beispiel für eine Parameterdatei.

## <a name="update-the-azuredeployparametersjson-file"></a>Aktualisieren der Datei „azuredeploy.parameters.json“

Aktualisieren Sie die Datei `azuredeploy.parameters.json` mit `vaultName`, Geheimnis-URI, `VmName` und anderen Werten, die für Ihre Umgebung benötigt werden. Die folgende JSON-Datei zeigt ein Beispiel der Vorlagenparameterdatei:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "newStorageAccountName": {
      "value": "kvstorage01"
    },
    "vmName": {
      "value": "VM1"
    },
    "vmSize": {
      "value": "Standard_D1_v2"
    },
    "adminUserName": {
      "value": "demouser"
    },
    "adminPassword": {
      "value": "demouser@123"
    },
    "vaultName": {
      "value": "contosovault"
    },
    "vaultResourceGroup": {
      "value": "contosovaultrg"
    },
    "secretUrlWithVersion": {
      "value": "https://testkv001.vault.local.azurestack.external/secrets/testcert002/82afeeb84f4442329ce06593502e7840"
    }
  }
}
```

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

Stellen Sie die Vorlage mithilfe des folgenden PowerShell-Skripts bereit:

```powershell
# Deploy a Resource Manager template to create a VM and push the secret onto it
New-AzureRmResourceGroupDeployment `
  -Name KVDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

Wenn die Vorlage erfolgreich bereitgestellt wurde, erhalten Sie die folgende Ausgabe:

![Ergebnisse der Vorlagenbereitstellung](media/azure-stack-key-vault-push-secret-into-vm/deployment-output.png)

Azure Stack überträgt das Zertifikat während der Bereitstellung per Pushvorgang auf den virtuellen Computer. Der Zertifikatspeicherort hängt vom Betriebssystem der VM ab:

* Unter Windows wird das Zertifikat dem Zertifikatspeicherort **LocalMachine** mit dem vom Benutzer angegebenen Zertifikatspeicher hinzugefügt.
* Unter Linux wird das Zertifikat im Verzeichnis `/var/lib/waagent directory` abgelegt. Hierbei wird für die X509-Zertifikatdatei der Dateiname &lt;UppercaseThumbprint.crt&gt; und für den privaten Schlüssel der Dateiname &lt;UppercaseThumbprint.prv&gt; verwendet.

## <a name="retire-certificates"></a>Zurückziehen von Zertifikaten

Das Zurückziehen von Zertifikaten ist Teil des Zertifikatverwaltungsprozesses. Sie können die ältere Version eines Zertifikats nicht löschen, aber Sie können sie deaktivieren, indem Sie das Cmdlet `Set-AzureKeyVaultSecretAttribute` verwenden.

Im folgenden Beispiel wird gezeigt, wie Sie ein Zertifikat deaktivieren. Verwenden Sie Ihre eigenen Werte für die Parameter **VaultName**, **Name** und **Version**.

```powershell
Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0
```

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen eines virtuellen Computers mit einem Key Vault-Kennwort](azure-stack-key-vault-deploy-vm-with-secret.md)
* [Gewähren des Zugriffs einer Anwendung auf Key Vault](azure-stack-key-vault-sample-app.md)
