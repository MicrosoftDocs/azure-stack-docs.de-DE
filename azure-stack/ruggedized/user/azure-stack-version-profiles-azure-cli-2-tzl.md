---
title: Verwalten von Azure Stack Hub mit der Azure CLI | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die plattformübergreifende Befehlszeilenschnittstelle (Command-Line Interface, CLI) verwenden, um Ressourcen in Azure Stack Hub zu verwalten und bereitzustellen.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 1/9/2020
ms.openlocfilehash: 34dafb113d2e87dddd0d744878c8be5e406751d0
ms.sourcegitcommit: 8526f642ef859b0006c3991d966f93608a87288a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/15/2021
ms.locfileid: "98243560"
---
# <a name="manage-and-deploy-resources-to-azure-stack-hub-with-azure-cli"></a>Verwalten und Bereitstellen von Ressourcen in Azure Stack Hub mit der Azure CLI

*Anwendungsbereich: Integrierte Azure Stack Hub-Systeme*

Mit den Schritten in diesem Artikel können Sie die Azure-Befehlszeilenschnittstelle (Azure CLI) für das Verwalten von Azure Stack Hub-Ressourcen über Linux-, Mac- und Windows-Clientplattformen einrichten.

## <a name="prepare-for-azure-cli"></a>Vorbereiten für Azure CLI

**Der Endpunkt der VM-Aliase** stellt einen Alias wie „UbuntuLTS“ oder „Win2012Datacenter“ bereit. Dieser Alias verweist beim Bereitstellen von VMs als einzelner Parameter auf einen Herausgeber, ein Angebot, eine SKU und die Version eines Images.  

Im folgenden Abschnitt wird beschrieben, wie Sie den Endpunkt der VM-Aliase einrichten.

### <a name="set-up-the-virtual-machine-aliases-endpoint"></a>Einrichten des Endpunkts der VM-Aliase

Sie können einen öffentlich zugänglichen Endpunkt einrichten, der eine VM-Aliasdatei hostet. Bei der VM-Aliasdatei handelt es sich um eine JSON-Datei, die einen allgemeinen Namen für ein Image bereitstellt. Sie verwenden den Namen, wenn Sie eine VM bereitstellen, als Azure CLI-Parameter.

1. Wenn Sie ein benutzerdefiniertes Image veröffentlichen, notieren Sie sich die während der Veröffentlichung angegebenen Informationen zu Herausgeber, Angebot, SKU und Version. Wenn es sich um ein Image aus dem Marketplace handelt, können Sie die Informationen mithilfe des Cmdlets ```Get-AzureVMImage``` anzeigen.  

2. Laden Sie die [Beispieldatei](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) von GitHub herunter.

3. Erstellen Sie ein Speicherkonto in Azure Stack Hub. Erstellen Sie anschließend einen Blobcontainer. Legen Sie die Zugriffsrichtlinie auf „Öffentlich“ fest.  

4. Laden Sie die JSON-Datei in den neuen Container hoch. Wenn das erledigt ist, können Sie die URL des Blobs anzeigen. Wählen Sie den Blobnamen aus, und wählen Sie dann die URL in den „Blob-Eigenschaften“ aus.

### <a name="install-or-upgrade-cli"></a>Installieren oder Aktualisieren der CLI

Melden Sie sich an Ihrer Entwicklungsarbeitsstation an und installieren die CLI. Für Azure Stack Hub wird Version 2.0 oder höher der Azure CLI benötigt. Für die neueste Version der API-Profile ist eine aktuelle Version der CLI erforderlich. Mit den im Artikel [Installieren der Azure CLI](/cli/azure/install-azure-cli) beschriebenen Schritten installieren Sie die CLI. 

Öffnen Sie ein Terminal oder ein Eingabeaufforderungsfenster, und führen Sie den folgenden Befehl aus, um zu überprüfen, ob die Installation erfolgreich war:

```shell
az --version
```

Daraufhin sollten die Version der Azure-CLI und die anderen abhängigen Bibliotheken angezeigt werden, die auf Ihrem Computer installiert sind.
    
![Azure CLI am Python-Speicherort von Azure Stack Hub](media/azure-stack-version-profiles-azure-cli-2/cli-python-location.png)


## <a name="windowslinux-azure-ad"></a>Windows/Linux (Azure AD)

In diesem Abschnitt wird die Einrichtung der CLI bei Verwendung von Azure AD als Identitätsverwaltungsdienst und der Nutzung der CLI auf einem Windows-/Linux-Computer ausführlich beschrieben.

### <a name="connect-to-azure-stack-hub"></a>Herstellen einer Verbindung mit Azure Stack Hub

1. Registrieren Sie Ihre Azure Stack Hub-Umgebung, indem Sie den Befehl `az cloud register` ausführen.

2. Registrieren Sie Ihre Umgebung. Verwenden Sie beim Ausführen von `az cloud register` die folgenden Parameter:

    | Wert | Beispiel | BESCHREIBUNG |
    | --- | --- | --- |
    | Umgebungsname | AzureStackUser | Verwenden Sie `AzureStackUser` für die Benutzerumgebung. Geben Sie `AzureStackAdmin` an, falls Sie der Betreiber der Umgebung sind. |
    | Resource Manager-Endpunkt | `https://management.local.azurestack.external` | <!-- TZLASDKFIX The **ResourceManagerUrl** in the ASDK is: `https://management.local.azurestack.external/`--> Der **ResourceManagerUrl**-Wert in integrierten Systemen lautet: `https://management.<region>.<fqdn>/` Wenn Sie eine Frage zum integrierten Systemendpunkt haben, können Sie sich an Ihren Cloudbetreiber wenden. |
    | Speicherendpunkt | local.azurestack.external | <!-- TZLASDKFIX `local.azurestack.external` is for the ASDK.--> Bei einem integrierten System verwenden Sie einen Endpunkt für Ihr System.  |
    | Key Vault-Suffix | .vault.local.azurestack.external | <!-- TZLASDKFIX `.vault.local.azurestack.external` is for the ASDK.--> Bei einem integrierten System verwenden Sie einen Endpunkt für Ihr System.  |
    | VM-Image-Aliasdokument: Endpunkt | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | Der URI des Dokuments, in dem die VM-Imagealiase enthalten sind. Weitere Informationen finden Sie unter [Einrichten des Endpunkts der VM-Aliase](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

1. Legen Sie die aktive Umgebung mithilfe der folgenden Befehle fest.

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Aktualisieren Sie Ihre Umgebungskonfiguration so, dass das spezifische API-Versionsprofil für Azure Stack Hub verwendet wird. Führen Sie den folgenden Befehl aus, um die Konfiguration zu aktualisieren:

    ```azurecli
    az cloud update --profile 2019-03-01-hybrid
   ```
 
1. Melden Sie sich bei Ihrer Azure Stack Hub-Umgebung an, indem Sie den Befehl `az login` ausführen. Melden Sie sich entweder als Benutzer oder als Dienstprinzipal bei der Azure Stack Hub-Umgebung an. 

   - Anmelden als *Benutzer*: 

     Sie können entweder den Benutzernamen und das Kennwort direkt im Befehl `az login` eingeben oder die Authentifizierung über einen Browser ausführen. Sie müssen das letztgenannte Verfahren wählen, wenn für Ihr Konto mehrstufige Authentifizierung aktiviert ist:

     ```azurecli
     az login -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > Wenn für Ihr Benutzerkonto die mehrstufige Authentifizierung aktiviert ist, verwenden Sie den Befehl `az login`, ohne den Parameter `-u` anzugeben. Durch die Ausführung des Befehls erhalten Sie eine URL und einen Code für die Authentifizierung.

   - Melden Sie sich als *Dienstprinzipal* an: 
    
     [Erstellen Sie einen Dienstprinzipal über das Azure-Portal](../../operator/azure-stack-create-service-principals.md) oder die CLI, und weisen Sie ihm eine Rolle zu, bevor Sie sich anmelden. Melden Sie sich anschließend mit dem folgenden Befehl an:

     ```azurecli  
     az login --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> --service-principal -u <Application Id of the Service Principal> -p <Key generated for the Service Principal>
     ```

### <a name="test-the-connectivity"></a>Testen der Konnektivität

Wenn alles eingerichtet ist, können Sie mit der CLI Ressourcen in Azure Stack Hub erstellen. Sie können beispielsweise eine Ressourcengruppe für eine App erstellen und einen virtuellen Computer hinzufügen. Verwenden Sie den folgenden Befehl, um eine Ressourcengruppe mit dem Namen „MyResourceGroup“ zu erstellen:

```azurecli
az group create -n MyResourceGroup -l local
```

Wenn die Ressourcengruppe erfolgreich erstellt wurde, werden mit dem vorherigen Befehl die folgenden Eigenschaften der neu erstellten Ressource ausgegeben:

![Ausgabe der Ressourcengruppenerstellung](media/azure-stack-connect-cli/image-1.png)

## <a name="windowslinux-ad-fs"></a>Windows/Linux (AD FS)

In diesem Abschnitt wird die Einrichtung der CLI bei Verwendung der Active Directory-Verbunddienste (AD FS) als Identitätsverwaltungsdienst und der Nutzung der CLI auf einem Windows-/Linux-Computer ausführlich beschrieben.


### <a name="connect-to-azure-stack-hub"></a>Herstellen einer Verbindung mit Azure Stack Hub

1. Registrieren Sie Ihre Azure Stack Hub-Umgebung, indem Sie den Befehl `az cloud register` ausführen.

2. Registrieren Sie Ihre Umgebung. Verwenden Sie beim Ausführen von `az cloud register` die folgenden Parameter:

    | Wert | Beispiel | BESCHREIBUNG |
    | --- | --- | --- |
    | Umgebungsname | AzureStackUser | Verwenden Sie `AzureStackUser` für die Benutzerumgebung. Geben Sie `AzureStackAdmin` an, falls Sie der Betreiber der Umgebung sind. |
    | Resource Manager-Endpunkt | `https://management.local.azurestack.external` | <!-- TZLASDKFIX The **ResourceManagerUrl** in the ASDK is: `https://management.local.azurestack.external/`--> Der **ResourceManagerUrl**-Wert in integrierten Systemen lautet: `https://management.<region>.<fqdn>/` Wenn Sie eine Frage zum integrierten Systemendpunkt haben, können Sie sich an Ihren Cloudbetreiber wenden. |
    | Speicherendpunkt | local.azurestack.external | <!-- TZLASDKFIX `local.azurestack.external` is for the ASDK.--> Bei einem integrierten System verwenden Sie einen Endpunkt für Ihr System.  |
    | Key Vault-Suffix | .vault.local.azurestack.external | <!-- TZLASDKFIX `.vault.local.azurestack.external` is for the ASDK.--> Bei einem integrierten System verwenden Sie einen Endpunkt für Ihr System.  |
    | VM-Image-Aliasdokument: Endpunkt | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | Der URI des Dokuments, in dem die VM-Imagealiase enthalten sind. Weitere Informationen finden Sie unter [Einrichten des Endpunkts der VM-Aliase](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

1. Legen Sie die aktive Umgebung mithilfe der folgenden Befehle fest.

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Aktualisieren Sie Ihre Umgebungskonfiguration so, dass das spezifische API-Versionsprofil für Azure Stack Hub verwendet wird. Führen Sie den folgenden Befehl aus, um die Konfiguration zu aktualisieren:

    ```azurecli
    az cloud update --profile 2019-03-01-hybrid
   ```

1. Melden Sie sich bei Ihrer Azure Stack Hub-Umgebung an, indem Sie den Befehl `az login` ausführen. Sie können sich entweder als Benutzer oder als Dienstprinzipal bei der Azure Stack Hub-Umgebung anmelden. 

   - Anmelden als *Benutzer*:

     Sie können entweder den Benutzernamen und das Kennwort direkt im Befehl `az login` eingeben oder die Authentifizierung über einen Browser ausführen. Sie müssen das letztgenannte Verfahren wählen, wenn für Ihr Konto mehrstufige Authentifizierung aktiviert ist:

     ```azurecli
     az cloud register  -n <environmentname>   --endpoint-resource-manager "https://management.local.azurestack.external"  --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>   --profile "2019-03-01-hybrid"
     ```

     > [!NOTE]
     > Wenn für Ihr Benutzerkonto die mehrstufige Authentifizierung aktiviert ist, verwenden Sie den Befehl `az login`, ohne den Parameter `-u` anzugeben. Durch die Ausführung des Befehls erhalten Sie eine URL und einen Code für die Authentifizierung.

   - Melden Sie sich als *Dienstprinzipal* an: 
    
     Bereiten Sie die PEM-Datei vor, die für die Anmeldung des Dienstprinzipals verwendet werden soll.

     Exportieren Sie auf dem Clientcomputer, auf dem der Prinzipal erstellt wurde, das Dienstprinzipalzertifikat als PFX-Datei mit dem privaten Schlüssel. Sie finden diesen unter `cert:\CurrentUser\My`. Das Zertifikat trägt den gleichen Namen wie der Prinzipal.

     Konvertieren Sie die PFX-Datei in eine PEM-Datei (mit dem OpenSSL-Hilfsprogramm).

     Melden Sie sich bei der Befehlszeilenschnittstelle an:
  
     ```azurecli  
     az login --service-principal \
      -u <Client ID from the Service Principal details> \
      -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
      --tenant <Tenant ID> \
      --debug 
     ```

### <a name="test-the-connectivity"></a>Testen der Konnektivität

Wenn alles eingerichtet ist, können Sie mit der CLI Ressourcen in Azure Stack Hub erstellen. Sie können beispielsweise eine Ressourcengruppe für eine App erstellen und einen virtuellen Computer hinzufügen. Verwenden Sie den folgenden Befehl, um eine Ressourcengruppe mit dem Namen „MyResourceGroup“ zu erstellen:

```azurecli
az group create -n MyResourceGroup -l local
```

Wenn die Ressourcengruppe erfolgreich erstellt wurde, werden mit dem vorherigen Befehl die folgenden Eigenschaften der neu erstellten Ressource ausgegeben:

![Ausgabe der Ressourcengruppenerstellung](media/azure-stack-connect-cli/image-1.png)


## <a name="known-issues"></a>Bekannte Probleme

Es gibt bekannte Probleme bei der Verwendung der CLI in Azure Stack Hub:

 - Der interaktive CLI-Modus. Beispielsweise wird der Befehl `az interactive` in Azure Stack Hub noch nicht unterstützt.
 - Verwenden Sie zum Abrufen der Liste der in Azure Stack Hub verfügbaren VM-Images anstelle des Befehls `az vm image list` den Befehl `az vm image list --all`. Durch Angabe der Option `--all` wird sichergestellt, dass in der Antwort nur die Images zurückgegeben werden, die in Ihrer Azure Stack Hub-Umgebung verfügbar sind.
 - VM-Imagealiase, die in Azure verfügbar sind, gelten möglicherweise nicht für Azure Stack Hub. Wenn Sie VM-Images verwenden, müssen Sie den gesamten URN-Parameter (Canonical:UbuntuServer:14.04.3-LTS:1.0.0) anstelle des Imagealias verwenden. Dieser URN muss mit den Imagespezifikationen übereinstimmen, die vom Befehl `az vm images list` abgerufen wurden.

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen von Vorlagen mit der Azure CLI](../../user/azure-stack-deploy-template-command-line.md)
- [Aktivieren der Azure CLI für Azure Stack Hub-Benutzer (Operator)](../../operator/azure-stack-cli-admin.md)
- [Verwalten von Benutzerberechtigungen](../../user/azure-stack-manage-permissions.md)