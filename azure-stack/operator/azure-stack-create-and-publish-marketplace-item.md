---
title: Erstellen und Veröffentlichen eines Marketplace-Elements in Azure Stack | Microsoft-Dokumentation
description: Erstellen und veröffentlichen Sie ein Marketplace-Element in Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/20/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: b9e1e9a1cdd0afe18a5395c99fb2eef932791667
ms.sourcegitcommit: 1a8ebd8103608b5ee9e804d7015eefe05ef55185
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69643847"
---
# <a name="create-and-publish-a-marketplace-item"></a>Erstellen und Veröffentlichen eines Marketplace-Elements

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

## <a name="create-a-marketplace-item"></a>Erstellen von Marketplace-Elementen

1. Laden Sie das [Azure Gallery Packager-Tool](https://www.aka.ms/azurestackmarketplaceitem) und das Azure Stack Marketplace-Beispielelement herunter.
2. Öffnen Sie das Marketplace-Beispielelement, und benennen Sie den Ordner **SimpleVMTemplate** um. Verwenden Sie den gleichen Namen wie für das Marketplace-Element, z.B. **Contoso.TodoList**. Der Ordner enthält Folgendes:

   ```shell
   /Contoso.TodoList/
   /Contoso.TodoList/Manifest.json
   /Contoso.TodoList/UIDefinition.json
   /Contoso.TodoList/Icons/
   /Contoso.TodoList/Strings/
   /Contoso.TodoList/DeploymentTemplates/
   ```

3. [Erstellen Sie eine Azure Resource Manager-Vorlage](/azure/azure-resource-manager/resource-group-authoring-templates), oder wählen Sie eine Vorlage von GitHub aus. Das Marketplace-Element verwendet diese Vorlage, um eine Ressource zu erstellen.

    > [!NOTE]  
    > Führen Sie in der Azure Resource Manager-Vorlage niemals eine Hartcodierung von Geheimnissen wie Produktschlüsseln, Kennwörtern oder anderen kundenbezogenen Informationen durch. JSON-Vorlagendateien sind nach der Veröffentlichung im Katalog ohne Authentifizierung zugänglich. Speichern Sie alle Geheimnisse in [Key Vault](/azure/azure-resource-manager/resource-manager-keyvault-parameter), und rufen Sie sie in der Vorlage auf.

4. Testen Sie die Vorlage mit den Microsoft Azure Stack-APIs, um sicherzustellen, dass die Ressource bereitgestellt werden kann.
5. Wenn Ihre Vorlage auf einem VM-Image basiert, befolgen Sie die Anweisungen zum [Hinzufügen eines VM-Images zu Azure Stack](azure-stack-add-vm-image.md).
6. Speichern Sie die Azure Resource Manager-Vorlage im Ordner **/Contoso.TodoList/DeploymentTemplates/** .
7. Wählen Sie Symbole und Text für Ihr Marketplace-Element aus. Fügen Sie Symbole zum Ordner **Symbole** hinzu. Text wird der Datei **Ressourcen** im Ordner **Zeichenfolgen** hinzugefügt. Verwenden Sie für die Symbole die Namenskonvention für **kleine**, **mittlere**, **große** und **breite** Symbole. Eine ausführliche Beschreibung dieser Größen finden Sie in der [Referenz zur Benutzeroberfläche für Marketplace-Elemente](#reference-marketplace-item-ui).

   > [!NOTE]
   > Für die ordnungsgemäße Erstellung des Marketplace-Elements sind alle vier Symbolgrößen (klein, mittel, groß, breit) erforderlich.
   >
   >

8. Geben Sie in der Datei **manifest.json** für **name** den Namen des Marketplace-Elements ein. Geben Sie außerdem für **publisher** Ihren Namen oder Ihr Unternehmen ein. Aktualisieren Sie vor dem Veröffentlichen des Images die Datei „manifest.json file“, und aktualisieren Sie die folgende Zeile: "name": "xxx". Achten Sie darauf, dass Sie für jede Imageversion einen anderen Namen verwenden.
9. Fügen Sie unter **artifacts** für **name** und **path** die richtigen Informationen für die Azure Resource Manager-Vorlage ein, die Sie aufgenommen haben:

   ```json
   "artifacts": [
      {
          "name": "Your template name",
          "type": "Template",
          "path": "DeploymentTemplates\\your path",
          "isDefault": true
      }
   ```

10. Ersetzen Sie **My Marketplace Items** durch eine Liste der Kategorien, in denen Ihr Marketplace-Element angezeigt werden soll.

    ```json
    "categories":[
    "My Marketplace Items"
    ],
    ```

11. Informationen zur weiteren Bearbeitung der Datei „manifest.json“ finden Sie unter [Referenz: Datei „manifest.json“ für Marketplace-Elemente](#reference-marketplace-item-manifestjson).

12. Öffnen Sie eine Eingabeaufforderung, und führen Sie den folgenden Befehl aus, um die Ordner in eine AZPKG-Datei zu packen:

    ```shell
    AzureGalleryPackager.exe package -m <absolute path to manifest.json> -o <output location for the package>
    ```

    > [!NOTE]
    > Der vollständige Pfad zur Datei „manifest.json“ und das Ausgabepaket müssen vorhanden sein. Wenn der Ausgabepfad „C:\MarketPlaceItem\yourpackage.azpkg“ lautet, muss der Ordner **C:\MarketPlaceItem** vorhanden sein.
    >
    >

## <a name="publish-a-marketplace-item"></a>Veröffentlichen von Marketplace-Elementen

1. Verwenden Sie PowerShell oder Azure Storage-Explorer, um Ihr Marketplace-Element (.azpkg) in Azure Blob Storage hochzuladen. Sie können es in Azure Stack-Speicher oder Azure Storage als temporärem Speicherort für das Paket hochladen. Stellen Sie sicher, dass das Blob öffentlich zugänglich ist.
2. Stellen Sie auf dem virtuellen Clientcomputer in der Microsoft Azure Stack-Umgebung sicher, dass Ihre PowerShell-Sitzung mit Ihren Dienstadministrator-Anmeldeinformationen eingerichtet ist. Anweisungen zum Authentifizieren von PowerShell in Azure Stack finden Sie unter [Bereitstellen von Vorlagen mit PowerShell](../user/azure-stack-deploy-template-powershell.md).
3. Bei Verwendung von [PowerShell 1.3.0](azure-stack-powershell-install.md) oder höher können Sie das PowerShell-Cmdlet **Add-AzsGalleryItem** verwenden, um das Marketplace-Element für Azure Stack zu veröffentlichen. Verwenden Sie für ältere Versionen als PowerShell 1.3.0 das Cmdlet **Add-AzureRMGalleryitem** anstelle von **Add-AzsGalleryItem**. Hier ist ein Beispiel bei Verwendung von PowerShell 1.3.0 oder höher angegeben:

   ```powershell
   Add-AzsGalleryItem -GalleryItemUri `
   https://sample.blob.core.windows.net/gallerypackages/Microsoft.SimpleTemplate.1.0.0.azpkg -Verbose
   ```

   | Parameter | BESCHREIBUNG |
   | --- | --- |
   | SubscriptionID |Azure-Abonnement-ID. Sie kann mithilfe von PowerShell abgerufen werden. Wenn Sie sie lieber im Portal abrufen möchten, navigieren Sie zum Anbieterabonnement, und kopieren Sie die Abonnement-ID. |
   | GalleryItemUri |Blob-URI für das Katalogpaket, das bereits in den Speicher hochgeladen wurde |
   | Apiversion |Festgelegt auf **2015-04-01** |

4. Rufen Sie das Portal auf. Sie können jetzt das Marketplace-Element im Portal als Operator oder als Benutzer anzeigen. Es kann ein paar Minuten dauern, bis das Paket angezeigt wird.

5. Das Marketplace-Element wurde nun im Azure Stack Marketplace gespeichert. Sie können es aus dem Blobspeicherort löschen.

    > [!CAUTION]  
    > Auf alle Standardartefakte des Katalogs und Ihre benutzerdefinierten Katalogartefakte kann jetzt unter den folgenden URLs ohne Authentifizierung zugegriffen werden:  
`https://adminportal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`
`https://portal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`
`https://systemgallery.blob.[Region].[external FQDN]/dev20161101-microsoft-windowsazure-gallery/[Template Name]/UiDefinition.json`

6. Sie können ein Marketplace-Element mit dem Cmdlet **Remove-AzureRMGalleryItem** entfernen. Beispiel:

   ```powershell
   Remove-AzsGalleryItem -Name Microsoft.SimpleTemplate.1.0.0  -Verbose
   ```

   > [!NOTE]
   > Auf der Marketplace-Benutzeroberfläche wird unter Umständen ein Fehler angezeigt, nachdem Sie ein Element entfernt haben. Um das Problem zu beheben, klicken Sie im Portal auf **Einstellungen**. Wählen Sie anschließend unter **Portalanpassung** die Option **Änderungen verwerfen** aus.
   >
   >

## <a name="reference-marketplace-item-manifestjson"></a>Referenz: Datei „manifest.json“ für Marketplace-Elemente

### <a name="identity-information"></a>Identitätsinformationen

| NAME | Erforderlich | type | Einschränkungen | BESCHREIBUNG |
| --- | --- | --- | --- | --- |
| NAME |X |Zeichenfolge |[A-Za-z0-9]+ | |
| Herausgeber |X |Zeichenfolge |[A-Za-z0-9]+ | |
| Version |X |Zeichenfolge |[SemVer, v2](https://semver.org/) | |

### <a name="metadata"></a>Metadaten

| NAME | Erforderlich | type | Einschränkungen | BESCHREIBUNG |
| --- | --- | --- | --- | --- |
| DisplayName |X |Zeichenfolge |Empfehlung: 80 Zeichen |Im Portal wird der Elementname unter Umständen nicht richtig angezeigt, wenn er länger als 80 Zeichen ist. |
| PublisherDisplayName |X |Zeichenfolge |Empfehlung: 30 Zeichen |Im Portal wird der Herausgebername unter Umständen nicht richtig angezeigt, wenn er länger als 30 Zeichen ist. |
| PublisherLegalName |X |Zeichenfolge |Maximal 256 Zeichen | |
| Zusammenfassung |X |Zeichenfolge |60–100 Zeichen | |
| LongSummary |X |Zeichenfolge |140–256 Zeichen |Noch nicht für Azure Stack gültig |
| BESCHREIBUNG |X |[HTML](https://github.com/Azure/portaldocs/blob/master/gallery-sdk/generated/index-gallery.md#gallery-item-metadata-html-sanitization) |500 bis 5.000 Zeichen | |

### <a name="images"></a>Bilder

Marketplace verwendet die folgenden Symbole:

| NAME | Breite | Höhe | Notizen |
| --- | --- | --- | --- |
| Breite |255 px |115 px |Immer erforderlich |
| Groß |115 px |115 px |Immer erforderlich |
| Mittel |90 px |90 px |Immer erforderlich |
| Klein |40 px |40 px |Immer erforderlich |
| Screenshot |533 px |324 px |Immer erforderlich |

### <a name="categories"></a>Categories

Jedes Marketplace-Element muss mit einer Kategorie gekennzeichnet werden, die die Position des Elements auf der Portalbenutzeroberfläche angibt. Sie können eine der vorhandenen Kategorien in Azure Stack (**Compute**, **Daten und Speicher** usw.) oder eine neue Kategorie auswählen.

### <a name="links"></a>Links

Jedes Marketplace-Element kann verschiedene Links zu zusätzlichen Inhalten enthalten. Die Links werden als Liste der Namen und URIs angegeben:

| NAME | Erforderlich | type | Einschränkungen | BESCHREIBUNG |
| --- | --- | --- | --- | --- |
| DisplayName |X |Zeichenfolge |Maximal 64 Zeichen. | |
| Uri |X |URI | | |

### <a name="additional-properties"></a>Zusätzliche Eigenschaften

Zusätzlich zu den oben genannten Metadaten können Marketplace-Autoren benutzerdefinierte Schlüssel-Wert-Paare mit Daten in der folgenden Form angeben.

| NAME | Erforderlich | type | Einschränkungen | BESCHREIBUNG |
| --- | --- | --- | --- | --- |
| DisplayName |X |Zeichenfolge |Maximal 25 Zeichen. | |
| Wert |X |Zeichenfolge |Maximal 30 Zeichen. | |

### <a name="html-sanitization"></a>HTML-Bereinigung

Für jedes Feld, das HTML zulässt, sind die folgenden [Elemente und Attribute zulässig](https://github.com/Azure/portaldocs/blob/master/gallery-sdk/generated/index-gallery.md#gallery-item-metadata-html-sanitization):

`h1, h2, h3, h4, h5, p, ol, ul, li, a[target|href], br, strong, em, b, i`

## <a name="reference-marketplace-item-ui"></a>Referenz: Benutzeroberfläche für Marketplace-Elemente

Im Azure Stack-Portal werden folgende Symbole und Texte für Marketplace-Elemente angezeigt:

### <a name="create-blade"></a>Blatt "Erstellen"

![Blatt "Erstellen"](media/azure-stack-create-and-publish-marketplace-item/image1.png)

### <a name="marketplace-item-details-blade"></a>Blatt „Details“ für ein Marketplace-Element

![Blatt „Details“ für ein Marketplace-Element](media/azure-stack-create-and-publish-marketplace-item/image3.png)

## <a name="next-steps"></a>Nächste Schritte

* [Der Azure Stack-Marketplace – Übersicht](azure-stack-marketplace.md)
* [Herunterladen von Marketplace-Elementen](azure-stack-download-azure-marketplace-item.md)
