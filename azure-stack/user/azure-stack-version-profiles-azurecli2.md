---
title: Verwalten von Azure Stack Hub mit der Azure CLI
description: Erfahren Sie, wie Sie die plattformübergreifende Befehlszeilenschnittstelle (Command-Line Interface, CLI) verwenden, um Ressourcen in Azure Stack Hub zu verwalten und bereitzustellen.
author: mattbriggs
ms.topic: article
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/16/2020
ms.openlocfilehash: fe68722e2cabea2cc863bbd6fb6d8414eb91031c
ms.sourcegitcommit: 8526f642ef859b0006c3991d966f93608a87288a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/15/2021
ms.locfileid: "98243577"
---
# <a name="install-azure-cli-on-azure-stack-hub"></a>Installieren der Azure CLI in Azure Stack Hub

Sie können die Azure CLI zum Verwalten von Azure Stack Hub mit Windows- oder Linux-Computern installieren. Dieser Artikel führt Sie durch die Schritte zum Installieren und Einrichten der Azure CLI.

## <a name="install-azure-cli"></a>Installieren der Azure-Befehlszeilenschnittstelle

1. Melden Sie sich an Ihrer Entwicklungsarbeitsstation an und installieren die CLI. Für Azure Stack Hub wird Version 2.0 oder höher der Azure CLI benötigt. 

2. Mit den im Artikel [Installieren der Azure CLI](/cli/azure/install-azure-cli) beschriebenen Schritten können Sie die CLI installieren. 

3. Öffnen Sie ein Terminal oder ein Eingabeaufforderungsfenster, und führen Sie den folgenden Befehl aus, um zu überprüfen, ob die Installation erfolgreich war:

    ```shell
    az --version
    ```

    Daraufhin sollten die Version der Azure-CLI und die anderen abhängigen Bibliotheken angezeigt werden, die auf Ihrem Computer installiert sind.

    ![Azure CLI am Python-Speicherort von Azure Stack Hub](media/azure-stack-version-profiles-azurecli2/cli-python-location.png)

2. Notieren Sie sich den Python-Speicherort der CLI. Wenn Sie das ASDK ausführen, müssen Sie diesen Speicherort zum Hinzufügen Ihres Zertifikats verwenden. Anweisungen zum Einrichten von Zertifikaten für die Installation der CLI im Azure Stack Development Kit (ASDK) finden Sie unter [Einrichten von Zertifikaten für die Azure CLI im Azure Stack Development Kit](../asdk/asdk-cli.md).

## <a name="connect-with-azure-cli"></a>Verbinden mit der Azure CLI

### <a name="azure-ad-on-windows"></a>[Azure AD unter Windows](#tab/ad-win)

In diesem Abschnitt wird die Einrichtung der CLI Schritt für Schritt beschrieben, wenn Sie Azure AD als Identitätsverwaltungsdienst und die CLI auf einem Windows-Computer verwenden.

#### <a name="connect-to-azure-stack-hub"></a>Herstellen einer Verbindung mit Azure Stack Hub

1. Wenn Sie das ASDK verwenden, müssen Sie das Stammzertifikat der Azure Stack Hub-Zertifizierungsstelle als vertrauenswürdig einstufen. Anweisungen hierzu finden Sie unter [Vertrauen des Zertifikats](../asdk/asdk-cli.md#trust-the-certificate).

2. Registrieren Sie Ihre Azure Stack Hub-Umgebung, indem Sie den Befehl `az cloud register` ausführen.

3. Registrieren Sie Ihre Umgebung. Verwenden Sie beim Ausführen von `az cloud register` die folgenden Parameter:

      | Wert | Beispiel | BESCHREIBUNG |
      | --- | --- | --- |
      | Umgebungsname | AzureStackUser | Verwenden Sie `AzureStackUser` für die Benutzerumgebung. Geben Sie `AzureStackAdmin` an, falls Sie der Betreiber der Umgebung sind. |
      | Resource Manager-Endpunkt | `https://management.contoso.onmicrosoft.com` | Die **ResourceManagerUrl** im ASDK lautet: `https://management.contoso.onmicrosoft.com/` Der **ResourceManagerUrl**-Wert in integrierten Systemen lautet: `https://management.<region>.<fqdn>/` Wenn Sie eine Frage zum integrierten Systemendpunkt haben, können Sie sich an Ihren Cloudbetreiber wenden. |
      | Speicherendpunkt | local.contoso.onmicrosoft.com | `local.azurestack.external` ist für das ASDK bestimmt. Bei einem integrierten System verwenden Sie einen Endpunkt für Ihr System.  |
      | Key Vault-Suffix | .vault.contoso.onmicrosoft.com | `.vault.local.azurestack.external` ist für das ASDK bestimmt. Bei einem integrierten System verwenden Sie einen Endpunkt für Ihr System.  |
      | Ressourcen-ID des Active Directory-Endpunktgraphen | https://graph.windows.net/ | Active Directory-Ressourcen-ID |
    
      ```azurecli  
      az cloud register `
          -n <environmentname> `
          --endpoint-resource-manager "https://management.<region>.<fqdn>" `
          --suffix-storage-endpoint "<fqdn>" `
          --suffix-keyvault-dns ".vault.<fqdn>" `
          --endpoint-active-directory-graph-resource-id "https://graph.windows.net/"
      ```

    In der Azure CLI-Referenzdokumentation finden Sie einen Verweis auf den [register-Befehl](/cli/azure/cloud?view=azure-cli-latest#az_cloud_register).


4. Legen Sie die aktive Umgebung mithilfe der folgenden Befehle fest.

      ```azurecli
      az cloud set -n <environmentname>
      ```

5. Aktualisieren Sie Ihre Umgebungskonfiguration so, dass das spezifische API-Versionsprofil für Azure Stack Hub verwendet wird. Führen Sie den folgenden Befehl aus, um die Konfiguration zu aktualisieren:

    ```azurecli
    az cloud update --profile 2019-03-01-hybrid
   ```
 
6. Melden Sie sich bei Ihrer Azure Stack Hub-Umgebung an, indem Sie den Befehl `az login` ausführen.

    Sie können sich mit Ihren Benutzeranmeldeinformationen oder mit einem vom Cloudoperator bereitgestellten [Dienstprinzipalnamen](/azure/active-directory/develop/app-objects-and-service-principals) (SPN) bei der Azure Stack Hub-Umgebung anmelden. 

   - Anmelden als *Benutzer*: 

     Sie können entweder den Benutzernamen und das Kennwort direkt im Befehl `az login` eingeben oder die Authentifizierung über einen Browser ausführen. Sie müssen das letztgenannte Verfahren wählen, wenn für Ihr Konto mehrstufige Authentifizierung aktiviert ist:

     ```azurecli
     az login -u "user@contoso.onmicrosoft.com" -p 'Password123!' --tenant contoso.onmicrosoft.com
     ```

     > [!NOTE]
     > Wenn für Ihr Benutzerkonto die mehrstufige Authentifizierung aktiviert ist, verwenden Sie den Befehl `az login`, ohne den Parameter `-u` anzugeben. Durch die Ausführung des Befehls erhalten Sie eine URL und einen Code für die Authentifizierung.

   - Melden Sie sich als *Dienstprinzipal* an: 
    
        [Erstellen Sie einen Dienstprinzipal über das Azure-Portal](../operator/azure-stack-create-service-principals.md) oder die CLI, und weisen Sie ihm eine Rolle zu, bevor Sie sich anmelden. Melden Sie sich anschließend mit dem folgenden Befehl an:
    
        ```azurecli  
        az login `
          --tenant <Azure Active Directory Tenant name. `
                    For example: myazurestack.onmicrosoft.com> `
        --service-principal `
          -u <Application Id of the Service Principal> `
          -p <Key generated for the Service Principal>
        ```
    
7. Vergewissern Sie sich, dass Ihre Umgebung ordnungsgemäß festgelegt ist und es sich Ihrer Umgebung um die aktive Cloud handelt.

      ```azurecli
          az cloud list --output table
      ```

Ihre Umgebung sollte aufgeführt sein, und **IsActive** sollte `true` sein. Beispiel:

```azurecli  
IsActive    Name               Profile
----------  -----------------  -----------------
False       AzureCloud         2019-03-01-hybrid
False       AzureChinaCloud    latest
False       AzureUSGovernment  latest
False       AzureGermanCloud   latest
True        AzureStackUser     2019-03-01-hybrid
```

#### <a name="test-the-connectivity"></a>Testen der Konnektivität

Wenn alles eingerichtet ist, können Sie mit der CLI Ressourcen in Azure Stack Hub erstellen. Sie können beispielsweise eine Ressourcengruppe für eine App erstellen und einen virtuellen Computer hinzufügen. Verwenden Sie den folgenden Befehl, um eine Ressourcengruppe mit dem Namen „MyResourceGroup“ zu erstellen:

```azurecli
az group create -n MyResourceGroup -l local
```

Wenn die Ressourcengruppe erfolgreich erstellt wurde, werden mit dem vorherigen Befehl die folgenden Eigenschaften der neu erstellten Ressource ausgegeben:

![Ausgabe der Ressourcengruppenerstellung](media/azure-stack-connect-cli/image1.png)

### <a name="ad-fs-on-windows"></a>[AD FS unter Windows](#tab/adfs-win)

In diesem Abschnitt wird die Einrichtung der CLI Schritt für Schritt beschrieben, wenn Sie Active Directory-Verbunddienste (AD FS) als Identitätsverwaltungsdienst und die CLI auf einem Windows-Computer verwenden.

#### <a name="connect-to-azure-stack-hub"></a>Herstellen einer Verbindung mit Azure Stack Hub


1. Wenn Sie das ASDK verwenden, müssen Sie das Stammzertifikat der Azure Stack Hub-Zertifizierungsstelle als vertrauenswürdig einstufen. Anweisungen hierzu finden Sie unter [Vertrauen des Zertifikats](../asdk/asdk-cli.md#trust-the-certificate).

2. Registrieren Sie Ihre Azure Stack Hub-Umgebung, indem Sie den Befehl `az cloud register` ausführen.

3. Registrieren Sie Ihre Umgebung. Verwenden Sie beim Ausführen von `az cloud register` die folgenden Parameter:

    | Wert | Beispiel | BESCHREIBUNG |
    | --- | --- | --- |
    | Umgebungsname | AzureStackUser | Verwenden Sie `AzureStackUser` für die Benutzerumgebung. Geben Sie `AzureStackAdmin` an, falls Sie der Betreiber der Umgebung sind. |
    | Resource Manager-Endpunkt | `https://management.local.azurestack.external` | Die **ResourceManagerUrl** im ASDK lautet: `https://management.local.azurestack.external/` Der **ResourceManagerUrl**-Wert in integrierten Systemen lautet: `https://management.<region>.<fqdn>/` Wenn Sie eine Frage zum integrierten Systemendpunkt haben, können Sie sich an Ihren Cloudbetreiber wenden. |
    | Speicherendpunkt | local.azurestack.external | `local.azurestack.external` ist für das ASDK bestimmt. Bei einem integrierten System verwenden Sie einen Endpunkt für Ihr System.  |
    | Key Vault-Suffix | .vault.local.azurestack.external | `.vault.local.azurestack.external` ist für das ASDK bestimmt. Bei einem integrierten System verwenden Sie einen Endpunkt für Ihr System.  |
    | VM-Image-Aliasdokument: Endpunkt | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | Der URI des Dokuments, in dem die VM-Imagealiase enthalten sind. Weitere Informationen finden Sie unter [Einrichten des Endpunkts der VM-Aliase](../asdk/asdk-cli.md#set-up-the-virtual-machine-alias-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

4. Legen Sie die aktive Umgebung mithilfe der folgenden Befehle fest.

      ```azurecli
      az cloud set -n <environmentname>
      ```

5. Aktualisieren Sie Ihre Umgebungskonfiguration so, dass das spezifische API-Versionsprofil für Azure Stack Hub verwendet wird. Führen Sie den folgenden Befehl aus, um die Konfiguration zu aktualisieren:

    ```azurecli
    az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Wenn Sie eine Version von Azure Stack Hub ausführen, die älter als Build 1808 ist, müssen Sie anstelle des API-Versionsprofils **2019-03-01-hybrid** das API-Versionsprofil **2017-03-09-profile** verwenden. Sie müssen außerdem eine aktuelle Version der Azure CLI verwenden.

6. Melden Sie sich bei Ihrer Azure Stack Hub-Umgebung an, indem Sie den Befehl `az login` ausführen. Sie können sich entweder als Benutzer oder als [Dienstprinzipal](/azure/active-directory/develop/app-objects-and-service-principals) bei der Azure Stack Hub-Umgebung anmelden. 

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

#### <a name="test-the-connectivity"></a>Testen der Konnektivität

Wenn alles eingerichtet ist, können Sie mit der CLI Ressourcen in Azure Stack Hub erstellen. Sie können beispielsweise eine Ressourcengruppe für eine App erstellen und einen virtuellen Computer hinzufügen. Verwenden Sie den folgenden Befehl, um eine Ressourcengruppe mit dem Namen „MyResourceGroup“ zu erstellen:

```azurecli
az group create -n MyResourceGroup -l local
```

Wenn die Ressourcengruppe erfolgreich erstellt wurde, werden mit dem vorherigen Befehl die folgenden Eigenschaften der neu erstellten Ressource ausgegeben:

![Ausgabe der Ressourcengruppenerstellung](media/azure-stack-connect-cli/image1.png)

### <a name="azure-ad-on-linux"></a>[Azure AD unter Linux](#tab/ad-lin)

In diesem Abschnitt wird die Einrichtung der CLI Schritt für Schritt beschrieben, wenn Sie Azure AD als Identitätsverwaltungsdienst und die CLI auf einem Linux-Computer verwenden.

#### <a name="connect-to-azure-stack-hub"></a>Herstellen einer Verbindung mit Azure Stack Hub

Führen Sie die folgenden Schritte aus, um eine Verbindung mit Azure Stack Hub herzustellen:


1. Wenn Sie das ASDK verwenden, müssen Sie das Stammzertifikat der Azure Stack Hub-Zertifizierungsstelle als vertrauenswürdig einstufen. Anweisungen hierzu finden Sie unter [Vertrauen des Zertifikats](../asdk/asdk-cli.md#trust-the-certificate).

2. Registrieren Sie Ihre Azure Stack Hub-Umgebung, indem Sie den Befehl `az cloud register` ausführen.

3. Registrieren Sie Ihre Umgebung. Verwenden Sie beim Ausführen von `az cloud register` die folgenden Parameter:

    | Wert | Beispiel | BESCHREIBUNG |
    | --- | --- | --- |
    | Umgebungsname | AzureStackUser | Verwenden Sie `AzureStackUser` für die Benutzerumgebung. Geben Sie `AzureStackAdmin` an, falls Sie der Betreiber der Umgebung sind. |
    | Resource Manager-Endpunkt | `https://management.local.azurestack.external` | Die **ResourceManagerUrl** im ASDK lautet: `https://management.local.azurestack.external/` Der **ResourceManagerUrl**-Wert in integrierten Systemen lautet: `https://management.<region>.<fqdn>/` Wenn Sie eine Frage zum integrierten Systemendpunkt haben, können Sie sich an Ihren Cloudbetreiber wenden. |
    | Speicherendpunkt | local.azurestack.external | `local.azurestack.external` ist für das ASDK bestimmt. Bei einem integrierten System verwenden Sie einen Endpunkt für Ihr System.  |
    | Key Vault-Suffix | .vault.local.azurestack.external | `.vault.local.azurestack.external` ist für das ASDK bestimmt. Bei einem integrierten System verwenden Sie einen Endpunkt für Ihr System.  |
    | VM-Image-Aliasdokument: Endpunkt | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | Der URI des Dokuments, in dem die VM-Imagealiase enthalten sind. Weitere Informationen finden Sie unter [Einrichten des Endpunkts der VM-Aliase](../asdk/asdk-cli.md#set-up-the-virtual-machine-alias-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

4. Legen Sie die aktive Umgebung fest. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

5. Aktualisieren Sie Ihre Umgebungskonfiguration so, dass das spezifische API-Versionsprofil für Azure Stack Hub verwendet wird. Führen Sie den folgenden Befehl aus, um die Konfiguration zu aktualisieren:

    ```azurecli
      az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Wenn Sie eine Version von Azure Stack Hub ausführen, die älter als Build 1808 ist, müssen Sie anstelle des API-Versionsprofils **2019-03-01-hybrid** das API-Versionsprofil **2017-03-09-profile** verwenden. Sie müssen außerdem eine aktuelle Version der Azure CLI verwenden.

6. Melden Sie sich bei Ihrer Azure Stack Hub-Umgebung an, indem Sie den Befehl `az login` ausführen. Sie können sich entweder als Benutzer oder als [Dienstprinzipal](/azure/active-directory/develop/app-objects-and-service-principals) bei der Azure Stack Hub-Umgebung anmelden. 

   * Anmelden als *Benutzer*:

     Sie können entweder den Benutzernamen und das Kennwort direkt im Befehl `az login` eingeben oder die Authentifizierung über einen Browser ausführen. Sie müssen das letztgenannte Verfahren wählen, wenn für Ihr Konto mehrstufige Authentifizierung aktiviert ist:

     ```azurecli
     az login \
       -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > Wenn für Ihr Benutzerkonto die mehrstufige Authentifizierung aktiviert ist, können Sie den Befehl `az login` verwenden, ohne den Parameter `-u` anzugeben. Durch die Ausführung des Befehls erhalten Sie eine URL und einen Code für die Authentifizierung.
   
   * Anmelden als *Dienstprinzipal*:
    
     [Erstellen Sie einen Dienstprinzipal über das Azure-Portal](../operator/azure-stack-create-service-principals.md) oder die CLI, und weisen Sie ihm eine Rolle zu, bevor Sie sich anmelden. Melden Sie sich anschließend mit dem folgenden Befehl an:

     ```azurecli  
     az login \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
       --service-principal \
       -u <Application Id of the Service Principal> \
       -p <Key generated for the Service Principal>
     ```

#### <a name="test-the-connectivity"></a>Testen der Konnektivität

Wenn alles eingerichtet ist, können Sie mit der CLI Ressourcen in Azure Stack Hub erstellen. Sie können beispielsweise eine Ressourcengruppe für eine App erstellen und einen virtuellen Computer hinzufügen. Verwenden Sie den folgenden Befehl, um eine Ressourcengruppe mit dem Namen „MyResourceGroup“ zu erstellen:

```azurecli
    az group create -n MyResourceGroup -l local
```

Wenn die Ressourcengruppe erfolgreich erstellt wurde, werden mit dem vorherigen Befehl die folgenden Eigenschaften der neu erstellten Ressource ausgegeben:

![Ausgabe der Ressourcengruppenerstellung](media/azure-stack-connect-cli/image1.png)

### <a name="ad-fs-linux"></a>[AD FS unter Linux](#tab/adfs-lin)

In diesem Abschnitt wird die Einrichtung der CLI Schritt für Schritt beschrieben, wenn Sie Active Directory-Verbunddienste (AD FS) als Verwaltungsdienst und die CLI auf einem Linux-Computer verwenden.

#### <a name="connect-to-azure-stack-hub"></a>Herstellen einer Verbindung mit Azure Stack Hub

Führen Sie die folgenden Schritte aus, um eine Verbindung mit Azure Stack Hub herzustellen:

1. Wenn Sie das ASDK verwenden, müssen Sie das Stammzertifikat der Azure Stack Hub-Zertifizierungsstelle als vertrauenswürdig einstufen. Anweisungen hierzu finden Sie unter [Vertrauen des Zertifikats](../asdk/asdk-cli.md#trust-the-certificate).

2. Registrieren Sie Ihre Azure Stack Hub-Umgebung, indem Sie den Befehl `az cloud register` ausführen.

3. Registrieren Sie Ihre Umgebung. Verwenden Sie beim Ausführen von `az cloud register` die folgenden Parameter.

    | Wert | Beispiel | BESCHREIBUNG |
    | --- | --- | --- |
    | Umgebungsname | AzureStackUser | Verwenden Sie `AzureStackUser` für die Benutzerumgebung. Geben Sie `AzureStackAdmin` an, falls Sie der Betreiber der Umgebung sind. |
    | Resource Manager-Endpunkt | `https://management.local.azurestack.external` | Die **ResourceManagerUrl** im ASDK lautet: `https://management.local.azurestack.external/` Der **ResourceManagerUrl**-Wert in integrierten Systemen lautet: `https://management.<region>.<fqdn>/` Wenn Sie eine Frage zum integrierten Systemendpunkt haben, können Sie sich an Ihren Cloudbetreiber wenden. |
    | Speicherendpunkt | local.azurestack.external | `local.azurestack.external` ist für das ASDK bestimmt. Bei einem integrierten System verwenden Sie einen Endpunkt für Ihr System.  |
    | Key Vault-Suffix | .vault.local.azurestack.external | `.vault.local.azurestack.external` ist für das ASDK bestimmt. Bei einem integrierten System verwenden Sie einen Endpunkt für Ihr System.  |
    | VM-Image-Aliasdokument: Endpunkt | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | Der URI des Dokuments, in dem die VM-Imagealiase enthalten sind. Weitere Informationen finden Sie unter [Einrichten des Endpunkts der VM-Aliase](../asdk/asdk-cli.md#set-up-the-virtual-machine-alias-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

4. Legen Sie die aktive Umgebung fest. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

5. Aktualisieren Sie Ihre Umgebungskonfiguration so, dass das spezifische API-Versionsprofil für Azure Stack Hub verwendet wird. Führen Sie den folgenden Befehl aus, um die Konfiguration zu aktualisieren:

    ```azurecli
      az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Wenn Sie eine Version von Azure Stack Hub ausführen, die älter als Build 1808 ist, müssen Sie anstelle des API-Versionsprofils **2019-03-01-hybrid** das API-Versionsprofil **2017-03-09-profile** verwenden. Sie müssen außerdem eine aktuelle Version der Azure CLI verwenden.

6. Melden Sie sich bei Ihrer Azure Stack Hub-Umgebung an, indem Sie den Befehl `az login` ausführen. Sie können sich entweder als Benutzer oder als [Dienstprinzipal](/azure/active-directory/develop/app-objects-and-service-principals) bei der Azure Stack Hub-Umgebung anmelden. 

7. Anmelden: 

   *  Als **Benutzer** mit einem Webbrowser unter Verwendung eines Gerätecodes:  

   ```azurecli  
    az login --use-device-code
   ```

   > [!NOTE]  
   >Durch die Ausführung des Befehls erhalten Sie eine URL und einen Code für die Authentifizierung.

   * Als Dienstprinzipal:
        
     Bereiten Sie die PEM-Datei vor, die für die Anmeldung des Dienstprinzipals verwendet werden soll.

      * Exportieren Sie auf dem Clientcomputer, auf dem der Prinzipal erstellt wurde, das Dienstprinzipalzertifikat als PFX-Datei mit dem privaten Schlüssel. Sie finden diesen unter `cert:\CurrentUser\My`. Das Zertifikat trägt den gleichen Namen wie der Prinzipal.
  
      * Konvertieren Sie die PFX-Datei in eine PEM-Datei (mit dem OpenSSL-Hilfsprogramm).

     Melden Sie sich bei der Befehlszeilenschnittstelle an:

      ```azurecli  
      az login --service-principal \
        -u <Client ID from the Service Principal details> \
        -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
        --tenant <Tenant ID> \
        --debug 
      ```

#### <a name="test-the-connectivity"></a>Testen der Konnektivität

Wenn alles eingerichtet ist, können Sie mit der CLI Ressourcen in Azure Stack Hub erstellen. Sie können beispielsweise eine Ressourcengruppe für eine App erstellen und einen virtuellen Computer hinzufügen. Verwenden Sie den folgenden Befehl, um eine Ressourcengruppe mit dem Namen „MyResourceGroup“ zu erstellen:

```azurecli
  az group create -n MyResourceGroup -l local
```

Wenn die Ressourcengruppe erfolgreich erstellt wurde, werden mit dem vorherigen Befehl die folgenden Eigenschaften der neu erstellten Ressource ausgegeben:

![Ausgabe der Ressourcengruppenerstellung](media/azure-stack-connect-cli/image1.png)

### <a name="known-issues"></a>Bekannte Probleme

Es gibt bekannte Probleme bei der Verwendung der CLI in Azure Stack Hub:

 - Der interaktive CLI-Modus. Beispielsweise wird der Befehl `az interactive` in Azure Stack Hub noch nicht unterstützt.
 - Verwenden Sie zum Abrufen der Liste der in Azure Stack Hub verfügbaren VM-Images anstelle des Befehls `az vm image list` den Befehl `az vm image list --all`. Durch Angabe der Option `--all` wird sichergestellt, dass in der Antwort nur die Images zurückgegeben werden, die in Ihrer Azure Stack Hub-Umgebung verfügbar sind.
 - VM-Imagealiase, die in Azure verfügbar sind, gelten möglicherweise nicht für Azure Stack Hub. Wenn Sie VM-Images verwenden, müssen Sie den gesamten URN-Parameter (Canonical:UbuntuServer:14.04.3-LTS:1.0.0) anstelle des Imagealias verwenden. Dieser URN muss mit den Imagespezifikationen übereinstimmen, die vom Befehl `az vm images list` abgerufen wurden.

---

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen von Vorlagen mit der Azure CLI](azure-stack-deploy-template-command-line.md)
- [Aktivieren der Azure CLI für Azure Stack Hub-Benutzer (Operator)](../operator/azure-stack-cli-admin.md)
- [Verwalten von Benutzerberechtigungen](azure-stack-manage-permissions.md)