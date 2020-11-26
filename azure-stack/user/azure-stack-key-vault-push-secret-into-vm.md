---
title: Bereitstellen einer VM mit einem sicher gespeicherten Zertifikat in Azure Stack Hub
description: Erfahren Sie, wie Sie eine VM bereitstellen und mithilfe eines Schlüsseltresors in Azure Stack Hub ein Zertifikat per Push auf die VM übertragen.
author: sethmanheim
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: sethm
ms.lastreviewed: 11/20/2020
ms.openlocfilehash: a326004b5ed100b0fc7eeb841dd0fbc4ded9ee5a
ms.sourcegitcommit: b50dd116d6d1f89d42bd35ad0f85bb25c5192921
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "95518159"
---
# <a name="deploy-a-vm-with-a-securely-stored-certificate-on-azure-stack-hub"></a>Bereitstellen einer VM mit einem sicher gespeicherten Zertifikat in Azure Stack Hub

In diesem Artikel wird beschrieben, wie Sie eine Azure Stack Hub-VM mit installiertem Schlüsseltresorzertifikat bereitstellen.

## <a name="overview"></a>Übersicht

Zertifikate werden in vielen Szenarien verwendet, z.B. zur Authentifizierung für Active Directory oder zur Verschlüsselung von Webdatenverkehr. Sie können Zertifikate auf sichere Weise als Geheimnisse in einem Azure Stack Hub-Schlüsseltresor speichern. Die Nutzung eines Azure Stack Hub-Schlüsseltresors hat folgende Vorteile:

* Zertifikate werden per Skript, Befehlszeilenverlauf oder Vorlage nicht offengelegt.
* Der Zertifikatverwaltungsprozess wurde optimiert.
* Sie haben die Kontrolle über die Schlüssel, mit denen auf Zertifikate zugegriffen wird.

## <a name="process-description"></a>Beschreibung des Prozesses

Die folgenden Schritte beschreiben den Prozess, der zum Übertragen eines Zertifikats per Pushvorgang auf den virtuellen Computer erforderlich ist:

1. Erstellen Sie ein Geheimnis im Schlüsseltresor.
2. Aktualisieren Sie die Datei **azuredeploy.parameters.json**.
3. Stellen Sie die Vorlage bereit.

> [!NOTE]
> Führen Sie die Schritte über das Azure Stack Development Kit (ASDK) oder über einen externen Client aus, wenn eine Verbindung per VPN besteht.

## <a name="prerequisites"></a>Voraussetzungen

* Sie müssen ein Angebot abonnieren, das den Key Vault-Dienst umfasst.
* [Installieren von PowerShell für Azure Stack Hub](../operator/powershell-install-az-module.md)
* [Konfigurieren der PowerShell-Umgebung des Azure Stack Hub-Benutzers](azure-stack-powershell-configure-user.md)

## <a name="create-a-key-vault-secret"></a>Erstellen eines Geheimnisses im Schlüsseltresor

Das folgende Skript erstellt ein Zertifikat im PFX-Format sowie einen Schlüsseltresor und speichert das Zertifikat als Geheimnis im Schlüsseltresor. Der `contentType` des Geheimnisses muss auf `pfx` festgelegt werden.

> [!IMPORTANT]
> Beim Erstellen des Schlüsseltresors muss der Parameter `-EnabledForDeployment` verwendet werden. Dieser Parameter stellt sicher, dass von Azure Resource Manager-Vorlagen auf den Schlüsseltresor verwiesen werden kann.

### <a name="az-modules"></a>[Az-Module](#tab/az)

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
  -cert "cert:\localMachine\my\<certificate thumbprint that was created in the previous step>" `
  -FilePath "<Fully qualified path to where the exported certificate can be stored>" `
  -Password $pwd

# Create a key vault and upload the certificate into the key vault as a secret
$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "servicecert"
$fileName = "<Fully qualified path to where the exported certificate can be stored>"
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

New-AzResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzKeyVault `
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
### <a name="azurerm-modules"></a>[AzureRM-Module](#tab/azurerm)

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
  -cert "cert:\localMachine\my\<certificate thumbprint that was created in the previous step>" `
  -FilePath "<Fully qualified path to where the exported certificate can be stored>" `
  -Password $pwd

# Create a key vault and upload the certificate into the key vault as a secret
$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "servicecert"
$fileName = "<Fully qualified path to where the exported certificate can be stored>"
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

New-AzureRMResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRMKeyVault `
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

---



Wenn Sie dieses Skript ausführen, enthält die Ausgabe den URI des Geheimnisses. Notieren Sie sich diesen URI, da Sie in der [Vorlage „Zertifikat per Push an Windows Resource Manager übertragen“](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate) darauf verweisen müssen. Laden Sie den Ordner für die [Vorlage „vm-push-certificate-windows“](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate) auf Ihren Entwicklungscomputer herunter. Dieser Ordner enthält die Dateien **azuredeploy.json** und **azuredeploy.parameters.json**, die Sie für die folgenden Schritte benötigen.

Ändern Sie die Datei **azuredeploy.parameters.json** gemäß den Werten Ihrer Umgebung. Die wichtigen Parameter sind der Tresorname, die Tresorressourcengruppe und der Geheimnis-URI (wie vom vorherigen Skript generiert). Der folgende Abschnitt enthält ein Beispiel für eine Parameterdatei.

## <a name="update-the-azuredeployparametersjson-file"></a>Aktualisieren der Datei „azuredeploy.parameters.json“

Aktualisieren Sie die Datei **azuredeploy.parameters.json** gemäß Ihrer Umgebung mit dem `vaultName`, dem URI des Geheimnisses, dem `VmName` und anderen Parametern. Die folgende JSON-Datei zeigt ein Beispiel der Vorlagenparameterdatei:

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

### <a name="az-modules"></a>[Az-Module](#tab/az2)

```powershell
# Deploy a Resource Manager template to create a VM and push the secret to it
New-AzResourceGroupDeployment `
  -Name KVDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```
### <a name="azurerm-modules"></a>[AzureRM-Module](#tab/azurerm2)

```powershell
# Deploy a Resource Manager template to create a VM and push the secret to it
New-AzureRMResourceGroupDeployment `
  -Name KVDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```
---



Wenn die Vorlage erfolgreich bereitgestellt wurde, wird die folgende Ausgabe angezeigt:

![Ergebnisse der Vorlagenbereitstellung](media/azure-stack-key-vault-push-secret-into-vm/deployment-output.png)

Azure Stack Hub überträgt das Zertifikat während der Bereitstellung per Pushvorgang auf die VM. Der Zertifikatspeicherort hängt vom Betriebssystem der VM ab:

* Unter Windows wird das Zertifikat dem Zertifikatspeicherort **LocalMachine** mit dem vom Benutzer angegebenen Zertifikatspeicher hinzugefügt.
* Unter Linux wird das Zertifikat unter dem Verzeichnis **/var/lib/waagent** abgelegt, mit dem Dateinamen **UppercaseThumbprint.crt** für die X.509 Zertifikatdatei und **UppercaseThumbprint.prv** für den privaten Schlüssel.

## <a name="retire-certificates"></a>Zurückziehen von Zertifikaten

Das Zurückziehen von Zertifikaten ist Teil des Zertifikatverwaltungsprozesses. Sie können die ältere Version eines Zertifikats nicht löschen, aber Sie können sie deaktivieren, indem Sie das `Set-AzureKeyVaultSecretAttribute`-Cmdlet verwenden.

Im folgenden Beispiel wird gezeigt, wie Sie ein Zertifikat deaktivieren. Verwenden Sie Ihre eigenen Werte für die Parameter `VaultName`, `Name` und `Version`.

```powershell
Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0
```

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen eines virtuellen Computers mit einem Key Vault-Kennwort](azure-stack-key-vault-deploy-vm-with-secret.md)
* [Gewähren des Zugriffs einer Anwendung auf Key Vault](azure-stack-key-vault-sample-app.md)
