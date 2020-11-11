---
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: include
ms.date: 10/10/2020
ms.reviewer: bryanla
ms.lastreviewed: 10/20/2020
ms.openlocfilehash: ff18909ef7586d8099f0e01f29d53f3dbc3677f1
ms.sourcegitcommit: 81e2d627c9dc4cc365deb4a0e0674b5ab3a7efbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/21/2020
ms.locfileid: "93050298"
---
Bestimmen Sie schließlich die Eigenschaften der letzten Bereitstellung des Ressourcenanbieters, und verwenden Sie diese, um den Prozess für die Geheimnisrotation abzuschließen.

### <a name="determine-deployment-properties"></a>Ermitteln der Bereitstellungseigenschaften

Ressourcenanbieter werden in Form eines Produktpakets mit Versionsangabe in Ihrer Azure Stack Hub-Umgebung bereitgestellt. Paketen wird eine eindeutige Paket-ID im Format `'<product-id>.<installed-version>'` zugewiesen. Dabei entspricht `<product-id>` einer eindeutigen Zeichenfolge, die den Ressourcenanbieter darstellt, und `<installed-version>` stellt eine spezifische Version dar. Die Geheimnisse, die jedem Paket zugeordnet sind, werden im Schlüsseltresordienst von Azure Stack Hub gespeichert. 

Öffnen Sie eine PowerShell-Konsole mit erhöhten Rechten, und führen Sie die folgenden Schritte aus, um die Eigenschaften zu ermitteln, die zum Rotieren der Geheimnisse des Ressourcenanbieters erforderlich sind:

1. Melden Sie sich mit Ihren Operatoranmeldeinformationen bei Ihrer Azure Stack Hub-Umgebung an. Das PowerShell-Anmeldeskript finden Sie unter [Herstellen einer Verbindung mit Azure Stack Hub mithilfe von PowerShell](../operator/azure-stack-powershell-configure-admin.md). Stellen Sie sicher, dass Sie die Az-Cmdlets von PowerShell (anstelle von AzureRM) verwenden, und ersetzen Sie alle Platzhalterwerte, z. B. Endpunkt-URLs und Verzeichnismandantennamen.

2. Führen Sie das Cmdlet `Get-AzsProductDeployment` aus, um eine Liste der neuesten Bereitstellungen von Ressourcenanbietern abzurufen. Die zurückgegebene `"value"`-Sammlung enthält ein Element für jeden bereitgestellten Ressourcenanbieter. Suchen Sie nach dem gewünschten Ressourcenanbieter, und notieren Sie sich die Werte der folgenden Eigenschaften:
   - `"name"`: Diese Eigenschaft enthält die Produkt-ID des Ressourcenanbieters im zweiten Segment des Werts. 
   - `"properties"."deployment"."version"`: Diese Eigenschaft enthält die Versionsnummer der aktuellen Bereitstellung. 

   Beachten Sie im folgenden Beispiel die Event Hubs-Ressourcenanbieterbereitstellung im ersten Element der Sammlung, das eine Produkt-ID von `"microsoft.eventhub"` und die Versionsnummer `"1.2003.0.0"` enthält.

   ```powershell
   PS C:\WINDOWS\system32> Get-AzsProductDeployment -AsJson
   VERBOSE: GET https://adminmanagement.myregion.mycompany.com/subscriptions/ze22ca96-z546-zbc6-z566-z35f68799816/providers/Microsoft.Deployment.Admin/locations/global/productDeployments?api-version=2019-01-01 with 0-char payload
   VERBOSE: Received 2656-char response, StatusCode = OK
   {
       "value":  [
                     {
                         "id":  "/subscriptions/ze22ca96-z546-zbc6-z566-z35f68799816/providers/Microsoft.Deployment.Admin/locations/global/productDeployments/microsoft.eventhub",
                         "name":  "global/microsoft.eventhub",
                         "type":  "Microsoft.Deployment.Admin/locations/productDeployments",
                         "properties":  {
                                            "status":  "DeploymentSucceeded",
                                            "subscriptionId":  "b37ae55a-a6c6-4474-ba97-81519412adf5",
                                            "deployment":  {
                                                               "version":  "1.2003.0.0",
                                                               "actionPlanInstanceResourceId":"/subscriptions/ze22ca96-z546-zbc6-z566-z35f68799816/providers/Microsoft.Deployment.Admin/locations/global/actionplans/abcdfcd3-fef0-z1a3-z85d-z6ceb0f31e36",
                                                               "parameters":  {
   
                                                                              }
                                                           },
                                            "lastSuccessfulDeployment":  {
                                                                             "version":  "1.2003.0.0",
                                                                             "actionPlanInstanceResourceId":"/subscriptions/ze22ca96-z546-zbc6-z566-z35f68799816/providers/Microsoft.Deployment.Admin/locations/global/actionplans/abcdfcd3-fef0-z1a3-z85d-z6ceb0f31e36",
                                                                             "parameters":  {
   
                                                                                            }
                                                                         },
                                            "provisioningState":  "Succeeded"
                                        }
                     },
                     {
                     ...
                     }
                 ]
   }
   ```

3. Erstellen Sie die Paket-ID des Ressourcenanbieters, indem Sie die Produkt-ID und Versionsnummer des Ressourcenanbieters verketten. Bei Verwendung der im vorherigen Schritt abgeleiteten Werte, lautet die Paket-ID des Event Hubs-Ressourcenanbieters beispielsweise `microsoft.eventhub.1.2003.0.0`. 

4. Führen Sie `Get-AzsProductSecret -PackageId` mit der Paket-ID aus, die Sie im vorherigen Schritt abgeleitet haben, um die Liste der Geheimnistypen abzurufen, die vom Ressourcenanbieter verwendet werden. Suchen Sie in der zurückgegebenen `value`-Sammlung das Element, das einen Wert von `"Certificate"` für die Eigenschaft `"properties"."secretKind"` enthält. Dieses Element enthält Eigenschaften für das Zertifikatsgeheimnis des Ressourcenanbieters. Notieren Sie sich den Namen, der diesem Zertifikatsgeheimnis zugewiesen ist und anhand des letzten Segments der Eigenschaft `"name"` direkt über `"properties"` identifiziert wird. 

   Im folgenden Beispiel enthält die Geheimnissammlung, die für den Event Hubs-Ressourcenanbieter zurückgegeben wird, ein `"Certificate"`-Geheimnis namens `aseh-ssl-gateway-pfx`. 

   ```powershell
   PS C:\WINDOWS\system32> Get-AzsProductSecret -PackageId 'microsoft.eventhub.1.2003.0.0' -AsJson
   VERBOSE: GET
   https://adminmanagement.myregion.mycompany.com/subscriptions/ze22ca96-z546-zbc6-z566-z35f68799816/providers/Microsoft.Deployment.Admin/locations/global/productPackages/microsoft.eventhub.1.2003.0.0/secrets?api-version=2019-01-01 with 0-char payload
   VERBOSE: Received 617-char response, StatusCode = OK
   {
       "value":  [
                     {
                         "id":  "/subscriptions/ze22ca96-z546-zbc6-z566-z35f68799816/providers/Microsoft.Deployment.Admin/locations/global/productPackages/microsoft.eventhub.1.2003.0.0/secrets/aseh-ssl-gateway-pfx",
                         "name":  "global/microsoft.eventhub.1.2003.0.0/aseh-ssl-gateway-pfx",
                         "type":  "Microsoft.Deployment.Admin/locations/productPackages/secrets",
                         "properties":  {
                                            "secretKind":  "Certificate",
                                            "description":  "Event Hubs gateway SSL certificate.",
                                            "expiresAfter":  "P730D",
                                            "secretDescriptor":  {
   
                                                                 },
                                            "secretState":  {
                                                                "status":  "Deployed",
                                                                "rotationStatus":  "None",
                                                                "expirationDate":  "2022-03-31T00:16:05.3068718Z"
                                                            },
                                            "provisioningState":  "Succeeded"
                                        }
                     },
                     ...
                 ]
   }
   ```

### <a name="rotate-the-secrets"></a>Rotieren der Geheimnisse

1. Verwenden Sie das Cmdlet `Set-AzsProductSecret`, um Ihr neues Zertifikat in den Schlüsseltresor zu importieren, der vom Rotationsprozess verwendet wird. Ersetzen Sie die variablen Platzhalter entsprechend, bevor Sie das Skript ausführen:

   | Platzhalter | BESCHREIBUNG | Beispielwert |
   | ----------- | ----------- | --------------|
   | `<product-id>` | Die Produkt-ID der neuesten Ressourcenanbieterbereitstellung | `microsoft.eventhub` |
   | `<installed-version>` | Die Version der neuesten Ressourcenanbieterbereitstellung | `1.2003.0.0` |
   | `<cert-secret-name>` | Der Name, unter dem das Zertifikatsgeheimnis gespeichert wird | `aseh-ssl-gateway-pfx` |
   | `<cert-pfx-file-path>` | Der Pfad zur PFX-Datei Ihres Zertifikats | `C:\dir\eh-cert-file.pfx` |
   | `<pfx-password>` | Das Kennwort, das der PFX-Datei Ihres Zertifikats zugewiesen ist | `strong@CertSecret6` |

   ```powershell
   $productId = '<product-id>'
   $packageId = $productId + '.' + '<installed-version>'
   $certSecretName = '<cert-secret-name>' 
   $pfxFilePath = '<cert-pfx-file-path>'
   $pfxPassword = ConvertTo-SecureString '<pfx-password>' -AsPlainText -Force   
   Set-AzsProductSecret -PackageId $packageId -SecretName $certSecretName -PfxFileName $pfxFilePath -PfxPassword $pfxPassword -Force
   ```

2. Verwenden Sie schließlich das Cmdlet `Invoke-AzsProductRotateSecretsAction`, um die internen und externen Geheimnisse zu rotieren:

   > [!NOTE]
   > Der Rotationsprozess dauert etwa 3,5 bis 4 Stunden.

   ```powershell
   Invoke-AzsProductRotateSecretsAction -ProductId $productId
   ```
   
   Sie können den Geheimnisrotationsprozess entweder in der PowerShell-Konsole oder im Administratorportal überwachen, indem Sie den Ressourcenanbieter im Marketplace-Dienst auswählen:

   [![Fortschritt der Geheimnisrotation](media/resource-provider-va-rotate-secrets-rotate/secret-rotation-in-progress.png)](media/resource-provider-va-rotate-secrets-rotate/secret-rotation-in-progress.png#lightbox)

