---
title: Erstellen und Veröffentlichen eines Marketplace-Elements in Azure Stack Hub
description: Erfahren Sie, wie Sie ein Azure Stack Hub-Marketplace-Element erstellen und veröffentlichen.
author: sethmanheim
ms.topic: article
ms.date: 11/16/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 11/16/2020
ms.openlocfilehash: 1ca64f3d6084ca4f28967070c344b286e858cb9d
ms.sourcegitcommit: e88f0a1f2f4ed3bb8442bfb7b754d8b3a51319b4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99534078"
---
# <a name="create-and-publish-a-custom-azure-stack-hub-marketplace-item"></a>Erstellen und Veröffentlichen eines benutzerdefinierten Azure Stack Hub-Marketplace-Elements

Jedes im Azure Stack Hub-Marketplace veröffentlichte Element verwendet das Azure Gallery Package-Format (AZPKG). Mit dem *Azure Gallery Packager*-Tool können Sie ein benutzerdefiniertes Azure Gallery-Paket erstellen, das Sie in den Azure Stack Hub-Marketplace hochladen können, wo es dann von Benutzern heruntergeladen werden kann. Beim Bereitstellungsprozess wird eine Azure Resource Manager-Vorlage verwendet.

## <a name="marketplace-items"></a>Marketplace-Elemente

In den Beispielen in diesem Artikel wird veranschaulicht, wie Sie ein einzelnes VM-Marketplace-Angebot vom Typ Windows oder Linux erstellen.

### <a name="prerequisites"></a>Voraussetzungen

Führen Sie die folgenden Schritte aus, bevor Sie das VM-Marketplace-Element erstellen:

1. Laden Sie das benutzerdefinierte VM-Image in das Azure Stack Hub-Portal hoch. Dabei gehen Sie nach den Anweisungen unter [Hinzufügen eines VM-Images zu Azure Stack Hub](azure-stack-add-vm-image.md) vor. 
2. Befolgen Sie die Anweisungen in diesem Artikel, um das Image zu verpacken (Erstellen einer AZPKG-Datei) und in den Azure Stack Hub-Marketplace hochzuladen.

## <a name="create-a-marketplace-item"></a>Erstellen von Marketplace-Elementen

Führen Sie die folgenden Schritte aus, um ein benutzerdefiniertes Marketplace-Element zu erstellen:

1. Laden Sie das [Azure Gallery Packager-Tool](https://aka.ms/azsmarketplaceitem) und das Azure Stack Hub-Katalogpaket herunter. Dieser Download umfasst benutzerdefinierte VM-Vorlagen. Extrahieren Sie die ZIP-Datei. Sie können unter dem Ordner **Custom VMs** die verfügbaren Linux- oder Windows-Vorlagen verwenden. Sie können die vordefinierten Vorlagen wiederverwenden und die entsprechenden Parameter mit den Produktdetails des Elements ändern, das im Azure Stack Hub-Portal angezeigt wird. Oder Sie können einfach die AZPKG-Datei wiederverwenden und die folgenden Schritte überspringen, um das eigene Katalogpaket anzupassen.

2. Erstellen Sie eine Azure Resource Manager Vorlage, oder verwenden Sie unsere Beispielvorlagen für Windows/Linux. Diese Beispielvorlagen werden in der ZIP-Datei des Packager-Tools bereitgestellt, die Sie in Schritt 1 heruntergeladen haben. Sie können entweder die Vorlage verwenden und die Textfelder ändern, oder Sie können eine vorkonfigurierte Vorlage von GitHub herunterladen. Weitere Informationen zu den Azure Resource Manager-Vorlagen finden Sie unter [Azure Resource Manager-Vorlagen](/azure/azure-resource-manager/resource-group-authoring-templates).

3. Das Katalogpaket muss die folgende Struktur aufweisen:

   ![Screenshot: Struktur des Katalogpakets](media/azure-stack-create-and-publish-marketplace-item/gallerypkg1.png)

   Die Dateistruktur der Bereitstellungsvorlagen sieht wie folgt aus:

   ![Screenshot: Struktur der Bereitstellungsvorlagen](media/azure-stack-create-and-publish-marketplace-item/gallerypkg2.png)

4. Ersetzen Sie die folgenden markierten Werte (mit Zahlen) in der Vorlage „Manifest.json“ durch den Wert, den Sie beim [Hochladen des benutzerdefinierten Images ](azure-stack-add-vm-image.md#add-a-platform-image) angegeben haben.

   > [!NOTE]  
   > Führen Sie in der Azure Resource Manager-Vorlage niemals eine Hartcodierung von Geheimnissen wie Produktschlüsseln, Kennwörtern oder anderen kundenbezogenen Informationen durch. JSON-Vorlagendateien sind nach der Veröffentlichung im Katalog ohne Authentifizierung zugänglich. Speichern Sie alle Geheimnisse in [Key Vault](/azure/azure-resource-manager/resource-manager-keyvault-parameter), und rufen Sie sie in der Vorlage auf.

   Es wird empfohlen, dass Sie vor dem Veröffentlichen Ihrer eigenen benutzerdefinierten Vorlage versuchen, das Beispiel unverändert zu veröffentlichen, und sicherstellen, dass es in Ihrer Umgebung funktioniert. Nachdem Sie überprüft haben, dass dieser Schritt funktioniert, löschen Sie das Beispiel aus dem Katalog, und nehmen Sie iterative Änderungen vor, bis Sie mit dem Ergebnis zufrieden sind.

   Die folgende Vorlage ist ein Beispiel für die Datei „Manifest.json“:

    ```json
    {
       "$schema": "https://gallery.azure.com/schemas/2015-10-01/manifest.json#",
       "name": "Test", (1)
       "publisher": "<Publisher name>", (2)
       "version": "<Version number>", (3)
       "displayName": "ms-resource:displayName", (4)
       "publisherDisplayName": "ms-resource:publisherDisplayName", (5)
       "publisherLegalName": "ms-resource:publisherDisplayName", (6)
       "summary": "ms-resource:summary",
       "longSummary": "ms-resource:longSummary",
       "description": "ms-resource:description",
       "longDescription": "ms-resource:description",
       "links": [
        { "displayName": "ms-resource:documentationLink", "uri": "http://go.microsoft.com/fwlink/?LinkId=532898" }
        ],
       "artifacts": [
          {
             "isDefault": true
          }
       ],
       "images": [{
          "context": "ibiza",
          "items": [{
             "id": "small",
             "path": "icons\\Small.png", (7)
             "type": "icon"
             },
             {
                "id": "medium",
                "path": "icons\\Medium.png",
                "type": "icon"
             },
             {
                "id": "large",
                "path": "icons\\Large.png",
                "type": "icon"
             },
             {
                "id": "wide",
                "path": "icons\\Wide.png",
                "type": "icon"
             }]
        }]
    }
    ```

    In der folgenden Liste werden die vorangehenden nummerierten Werte in der Beispielvorlage erläutert:

    - (1): Der Name des Angebots.
    - (2): Der Name des Herausgebers ohne Leerzeichen.
    - (3): Die Version Ihrer Vorlage ohne Leerzeichen.
    - (4): Der Name, den Kunden sehen.
    - (5): Der Herausgebername, den Kunden sehen.
    - (6): Der offizielle Firmenname des Herausgebers.
    - (7): Der Pfad und der Name für die einzelnen Symbole.

5. Für alle Felder, die auf **ms-resource** verweisen, müssen Sie die entsprechenden Werte in der Datei **strings/resources.json** ändern:

    ```json
    {
    "displayName": "<OfferName.PublisherName.Version>",
    "publisherDisplayName": "<Publisher name>",
    "summary": "Create a simple VM",
    "longSummary": "Create a simple VM and use it",
    "description": "<p>This is just a sample of the type of description you could create for your gallery item!</p><p>This is a second paragraph.</p>",
    "documentationLink": "Documentation"
    }
    ```

6. Testen Sie die Vorlage mit den [Azure Stack Hub-APIs](../user/azure-stack-profiles-azure-resource-manager-versions.md), um sicherzustellen, dass die Ressource bereitgestellt werden kann.

7. Wenn Ihre Vorlage auf einem VM-Image basiert, befolgen Sie die Anweisungen zum [Hinzufügen eines VM-Images zu Azure Stack Hub](azure-stack-add-vm-image.md).

8. Speichern Sie die Azure Resource Manager-Vorlage im Ordner **/Contoso.TodoList/DeploymentTemplates/** .

9. Wählen Sie Symbole und Text für Ihr Marketplace-Element aus. Fügen Sie Symbole zum Ordner **Symbole** hinzu. Text wird der Datei **Ressourcen** im Ordner **Zeichenfolgen** hinzugefügt. Verwenden Sie für die Symbole die Namenskonvention für **kleine**, **mittlere**, **große** und **breite** Symbole. Eine ausführliche Beschreibung dieser Größen finden Sie in der [Referenz zur Benutzeroberfläche für Marketplace-Elemente](#reference-marketplace-item-ui).

    > [!NOTE]
    > Für die ordnungsgemäße Erstellung des Marketplace-Elements sind alle vier Symbolgrößen (klein, mittel, groß, breit) erforderlich.

10. Informationen zur weiteren Bearbeitung der Datei „Manifest.json“ finden Sie unter [Referenz: Datei „manifest.json“ für Marketplace-Elemente](#reference-marketplace-item-manifestjson).

11. Wenn Sie die Änderungen an den Dateien abgeschlossen haben, konvertieren Sie sie in eine AZPKG-Datei. Sie führen die Konvertierung mithilfe des Tools **AzureGallery.exe** und des zuvor heruntergeladenen Beispielkatalogpakets durch. Führen Sie den folgenden Befehl aus:

    ```shell
    .\AzureGallery.exe package -m c:\<path>\<gallery package name>\manifest.json -o c:\Temp
    ```

    > [!NOTE]
    > Sie können einen beliebigen Ausgabepfad wählen, und er muss sich nicht auf dem Laufwerk „C:“ befinden. Der vollständige Pfad zu sowohl der Datei „manifest.json“ als auch zum Ausgabepaket muss jedoch vorhanden sein. Beispiel: Wenn `C:\<path>\galleryPackageName.azpkg` der Ausgabepfad ist, muss der Ordner `C:\<path>` vorhanden sein.
    >
    >

## <a name="publish-a-marketplace-item"></a>Veröffentlichen von Marketplace-Elementen

### <a name="az-modules"></a>[Az-Module](#tab/az)

1. Verwenden Sie PowerShell oder Azure Storage-Explorer, um Ihr Marketplace-Element (.azpkg) in Azure Blob Storage hochzuladen. Sie können es in einen lokalen Azure Stack Hub-Speicher oder in Azure Storage hochladen – ein temporärer Speicherort für das Paket. Stellen Sie sicher, dass das Blob öffentlich zugänglich ist.

2. Der erste Schritt beim Importieren des Katalogpakets in Azure Stack Hub ist das Herstellen einer Remoteverbindung (RDP) mit dem virtuellen Clientcomputer, um die soeben von Ihnen erstellte Datei in Ihre Azure Stack Hub-Instanz zu kopieren.

3. Hinzufügen eines Kontexts:

    ```powershell
    $ArmEndpoint = "https://adminmanagement.local.azurestack.external"
    Add-AzEnvironment -Name "AzureStackAdmin" -ArmEndpoint $ArmEndpoint
    Connect-AzAccount -EnvironmentName "AzureStackAdmin"
    ```

4. Führen Sie das folgende Skript aus, um die Ressource in Ihren Katalog zu importieren:

    ```powershell
    Add-AzsGalleryItem -GalleryItemUri `
    https://sample.blob.core.windows.net/<temporary blob name>/<offerName.publisherName.version>.azpkg -Verbose
    ```

5. Vergewissern Sie sich, dass Sie über ein gültiges Speicherkonto verfügen, das zum Speichern des Elements verfügbar ist. Den `GalleryItemURI`-Wert erhalten Sie über das Azure Stack Hub-Administratorportal. Wählen **Speicherkonto -> Blobeigenschaften -> URL** mit der Erweiterung „.azpkg“ aus. Das Speicherkonto ist nur für die temporäre Verwendung zum Veröffentlichen im Marketplace vorgesehen.

   Nachdem Sie Ihr Katalogpaket fertiggestellt und mit **Add-AzsGalleryItem** hochgeladen haben, sollte Ihr benutzerdefinierter virtueller Computer nun sowohl im Marketplace als auch in der Ansicht **Erstellen einer Ressource** angezeigt werden. Beachten Sie, dass das benutzerdefinierte Katalogpaket unter **Marketplace-Verwaltung** nicht angezeigt wird.

   [![Hochgeladenes benutzerdefiniertes Marketplace-Element](media/azure-stack-create-and-publish-marketplace-item/pkg6sm.png "Hochgeladenes benutzerdefiniertes Marketplace-Element")](media/azure-stack-create-and-publish-marketplace-item/pkg6.png#lightbox)

6. Nachdem Ihr Element erfolgreich im Marketplace veröffentlicht wurde, können Sie den Inhalt aus dem Speicherkonto löschen.

   Auf alle Standardartefakte des Katalogs und Ihre benutzerdefinierten Katalogartefakte kann jetzt unter den folgenden URLs ohne Authentifizierung zugegriffen werden:

   - `https://galleryartifacts.adminhosting.[Region].[externalFQDN]/artifact/20161101/[TemplateName]/DeploymentTemplates/Template.json`
   - `https://galleryartifacts.hosting.[Region].[externalFQDN]/artifact/20161101/[TemplateName]/DeploymentTemplates/Template.json`

6. Sie können ein Marketplace-Element mit dem Cmdlet **Remove-AzGalleryItem** entfernen. Beispiel:

   ```powershell
   Remove-AzsGalleryItem -Name <Gallery package name> -Verbose
   ```

> [!Note]  
> Auf der Marketplace-Benutzeroberfläche wird unter Umständen ein Fehler angezeigt, nachdem Sie ein Element entfernt haben. Um das Problem zu beheben, klicken Sie im Portal auf **Einstellungen**. Wählen Sie anschließend unter **Portalanpassung** die Option **Änderungen verwerfen** aus.

### <a name="azurerm-modules"></a>[AzureRM-Module](#tab/azurerm)

1. Verwenden Sie PowerShell oder Azure Storage-Explorer, um Ihr Marketplace-Element (.azpkg) in Azure Blob Storage hochzuladen. Sie können es in einen lokalen Azure Stack Hub-Speicher oder in Azure Storage hochladen – ein temporärer Speicherort für das Paket. Stellen Sie sicher, dass das Blob öffentlich zugänglich ist.

2. Der erste Schritt beim Importieren des Katalogpakets in Azure Stack Hub ist das Herstellen einer Remoteverbindung (RDP) mit dem virtuellen Clientcomputer, um die soeben von Ihnen erstellte Datei in Ihre Azure Stack Hub-Instanz zu kopieren.

3. Hinzufügen eines Kontexts:

    ```powershell
    $ArmEndpoint = "https://adminmanagement.local.azurestack.external"
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint $ArmEndpoint
    Add-AzureRMAccount -EnvironmentName "AzureStackAdmin"
    ```

4. Führen Sie das folgende Skript aus, um die Ressource in Ihren Katalog zu importieren:

    ```powershell
    Add-AzsGalleryItem -GalleryItemUri `
    https://sample.blob.core.windows.net/<temporary blob name>/<offerName.publisherName.version>.azpkg -Verbose
    ```

5. Vergewissern Sie sich, dass Sie über ein gültiges Speicherkonto verfügen, das zum Speichern des Elements verfügbar ist. Den `GalleryItemURI`-Wert erhalten Sie über das Azure Stack Hub-Administratorportal. Wählen **Speicherkonto -> Blobeigenschaften -> URL** mit der Erweiterung „.azpkg“ aus. Das Speicherkonto ist nur für die temporäre Verwendung zum Veröffentlichen im Marketplace vorgesehen.

   Nachdem Sie Ihr Katalogpaket fertiggestellt und mit **Add-AzsGalleryItem** hochgeladen haben, sollte Ihr benutzerdefinierter virtueller Computer nun sowohl im Marketplace als auch in der Ansicht **Erstellen einer Ressource** angezeigt werden. Beachten Sie, dass das benutzerdefinierte Katalogpaket unter **Marketplace-Verwaltung** nicht angezeigt wird.

   [![Hochgeladenes benutzerdefiniertes Marketplace-Element](media/azure-stack-create-and-publish-marketplace-item/pkg6sm.png "Hochgeladenes benutzerdefiniertes Marketplace-Element")](media/azure-stack-create-and-publish-marketplace-item/pkg6.png#lightbox)

6. Nachdem Ihr Element erfolgreich im Marketplace veröffentlicht wurde, können Sie den Inhalt aus dem Speicherkonto löschen.

   Auf alle Standardartefakte des Katalogs und Ihre benutzerdefinierten Katalogartefakte kann jetzt unter den folgenden URLs ohne Authentifizierung zugegriffen werden:

   - `https://galleryartifacts.adminhosting.[Region].[externalFQDN]/artifact/20161101/[TemplateName]/DeploymentTemplates/Template.json`
   - `https://galleryartifacts.hosting.[Region].[externalFQDN]/artifact/20161101/[TemplateName]/DeploymentTemplates/Template.json`

6. Sie können ein Marketplace-Element mit dem Cmdlet **Remove-AzGalleryItem** entfernen. Beispiel:

   ```powershell
   Remove-AzsGalleryItem -Name <Gallery package name> -Verbose
   ```

> [!Note]  
> Auf der Marketplace-Benutzeroberfläche wird unter Umständen ein Fehler angezeigt, nachdem Sie ein Element entfernt haben. Um das Problem zu beheben, klicken Sie im Portal auf **Einstellungen**. Wählen Sie anschließend unter **Portalanpassung** die Option **Änderungen verwerfen** aus.

---

## <a name="reference-marketplace-item-manifestjson"></a>Referenz: Datei „manifest.json“ für Marketplace-Elemente

### <a name="identity-information"></a>Identitätsinformationen

| Name | Erforderlich | type | Einschränkungen | BESCHREIBUNG |
| --- | --- | --- | --- | --- |
| Name |X |String |[A-Za-z0-9]+ | |
| Herausgeber |X |String |[A-Za-z0-9]+ | |
| Version |X |String |[SemVer, v2](https://semver.org/) | |

### <a name="metadata"></a>Metadaten

| Name | Erforderlich | type | Einschränkungen | BESCHREIBUNG |
| --- | --- | --- | --- | --- |
| DisplayName |X |String |Empfehlung: 80 Zeichen |Im Portal wird der Elementname möglicherweise nicht richtig angezeigt, wenn er länger als 80 Zeichen ist. |
| PublisherDisplayName |X |String |Empfehlung: 30 Zeichen |Im Portal wird der Herausgebername möglicherweise nicht richtig angezeigt, wenn er länger als 30 Zeichen ist. |
| PublisherLegalName |X |String |Maximal 256 Zeichen | |
| Zusammenfassung |X |String |60–100 Zeichen | |
| LongSummary |X |String |140–256 Zeichen |Noch nicht für Azure Stack Hub gültig |
| BESCHREIBUNG |X |[HTML](https://github.com/Azure/portaldocs/blob/master/gallery-sdk/generated/index-gallery.md#gallery-item-metadata-html-sanitization) |500 bis 5.000 Zeichen | |

### <a name="images"></a>Bilder

Marketplace verwendet die folgenden Symbole:

| Name | Breite | Höhe | Notizen |
| --- | --- | --- | --- |
| Breite |255 px |115 px |Immer erforderlich |
| Groß |115 px |115 px |Immer erforderlich |
| Medium |90 px |90 px |Immer erforderlich |
| Klein |40 px |40 px |Immer erforderlich |
| Screenshot |533 px |324 px |Optional |

### <a name="categories"></a>Kategorien

Jedes Marketplace-Element muss mit einer Kategorie gekennzeichnet werden, die die Position des Elements auf der Portalbenutzeroberfläche angibt. Sie können eine der vorhandenen Kategorien in Azure Stack Hub auswählen (**Compute**, **Daten und Speicher** usw.) oder eine neue erstellen.

### <a name="links"></a>Links

Jedes Marketplace-Element kann verschiedene Links zu zusätzlichen Inhalten enthalten. Die Links werden als Liste der Namen und URIs angegeben:

| Name | Erforderlich | type | Einschränkungen | BESCHREIBUNG |
| --- | --- | --- | --- | --- |
| DisplayName |X |String |Maximal 64 Zeichen. | |
| Uri |X |URI | | |

### <a name="additional-properties"></a>Zusätzliche Eigenschaften

Zusätzlich zu den oben genannten Metadaten können Marketplace-Autoren benutzerdefinierte Schlüssel-Wert-Paare mit Daten in der folgenden Form angeben.

| Name | Erforderlich | type | Einschränkungen | BESCHREIBUNG |
| --- | --- | --- | --- | --- |
| DisplayName |X |String |Maximal 25 Zeichen. | |
| Wert |X |String |Maximal 30 Zeichen. | |

### <a name="html-sanitization"></a>HTML-Bereinigung

Für jedes Feld, das HTML zulässt, sind die folgenden [Elemente und Attribute zulässig](https://github.com/Azure/portaldocs/blob/master/gallery-sdk/generated/index-gallery.md#gallery-item-metadata-html-sanitization):

`h1, h2, h3, h4, h5, p, ol, ul, li, a[target|href], br, strong, em, b, i`

## <a name="reference-marketplace-item-ui"></a>Referenz: Benutzeroberfläche für Marketplace-Elemente

Im Azure Stack Hub-Portal werden folgende Symbole und Texte für Marketplace-Elemente angezeigt:

### <a name="create-blade"></a>Blatt "Erstellen"

![Blatt „Erstellen“ – Azure Stack Hub-Marketplace-Elemente](media/azure-stack-create-and-publish-marketplace-item/image1.png)

### <a name="marketplace-item-details-blade"></a>Blatt „Details“ für ein Marketplace-Element

![Blatt „Details“ für ein Azure Stack Hub-Marketplace-Element](media/azure-stack-create-and-publish-marketplace-item/image3.png)

## <a name="next-steps"></a>Nächste Schritte

- [Der Azure Stack Hub-Marketplace – Übersicht](azure-stack-marketplace.md)
- [Herunterladen von Marketplace-Elementen](azure-stack-download-azure-marketplace-item.md)
- [Format und Struktur von Azure Resource Manager-Vorlagen](/azure/azure-resource-manager/resource-group-authoring-templates)
