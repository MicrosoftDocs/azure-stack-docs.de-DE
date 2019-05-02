---
title: Bereitstellen von VM-Skalierungsgruppen in Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Cloudbediener dem Azure Stack-Marketplace VM-Skalierungsgruppen hinzufügen können.
services: azure-stack
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.topic: article
origin.date: 02/21/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: kivenkat
ms.lastreviewed: 10/22/2018
ms.openlocfilehash: 7237437ca19ee6f5580661683ea82569d9d84b64
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "64309360"
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>Bereitstellen von VM-Skalierungsgruppen in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*
  
VM-Skalierungsgruppen sind eine Azure Stack-Computeressource. Verwenden Sie sie zum Bereitstellen und Verwalten eines Satzes identischer virtueller Computer. Da alle virtuellen Computer genauso konfiguriert sind, ist bei Skalierungsgruppen keine Vorabbereitstellung der virtuellen Computer erforderlich. Es ist einfacher, umfangreiche Dienste zu erstellen, die auf hohe Rechenleistung, Big Data und Workloads in Containern ausgelegt sind.

Dieser Artikel beschreibt, wie Skalierungsgruppen im Azure Stack-Marketplace verfügbar gemacht werden. Wenn Sie dieses Verfahren abgeschlossen haben, können Ihre Benutzer VM-Skalierungsgruppen zu ihren Abonnements hinzufügen.

VM-Skalierungsgruppen in Azure Stack ähneln VM-Skalierungsgruppen in Azure. Weitere Informationen finden Sie in den folgenden Videos:

* [Mark Russinovich spricht über Azure-Skalierungsgruppen](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)
* [VM-Skalierungsgruppen mit Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

In Azure Stack unterstützen VM-Skalierungsgruppen keine automatische Skalierung. Sie können einer Skalierungsgruppe weitere Instanzen hinzufügen, indem Sie Resource Manager-Vorlagen, die CLI oder PowerShell verwenden.

## <a name="prerequisites"></a>Voraussetzungen

* **Marketplace**: Registrieren Sie Azure Stack global für Azure, um Marketplace-Elemente verfügbar zu machen. Befolgen Sie die Anleitung unter [Registrieren von Azure Stack in Azure](azure-stack-registration.md).
* **Betriebssystemimage**: Bevor eine VM-Skalierungsgruppe (Virtual Machine Scale Set, VMSS) erstellt werden kann, müssen Sie die VM-Images für die Verwendung in der VMSS aus [Azure Stack-Marketplace](azure-stack-download-azure-marketplace-item.md) herunterladen. Die Images müssen bereits vorhanden sein, bevor ein Benutzer eine neue VMSS erstellen kann.

## <a name="use-the-azure-stack-portal"></a>Verwenden des Azure Stack-Portals

>[!IMPORTANT]  
> Die Informationen in diesem Abschnitt gelten bei Verwendung von Azure Stack Version 1808 oder höher. Wenn Ihre Version 1807 oder niedriger ist, lesen Sie [Hinzufügen der VM-Skalierungsgruppe (vor 1808)](#add-the-virtual-machine-scale-set-prior-to-version-1808).

1. Melden Sie sich beim Azure Stack-Portal an. Navigieren Sie dann zu **Alle Dienste** und **VM-Skalierungsgruppen**, und wählen Sie unter **COMPUTE** die Option **VM-Skalierungsgruppen** aus.
   ![Auswählen von VM-Skalierungsgruppen](media/azure-stack-compute-add-scalesets/all-services.png)

2. Wählen Sie ***VM-Skalierungsgruppen erstellen*** aus.
   ![Erstellen einer VM-Skalierungsgruppe](media/azure-stack-compute-add-scalesets/create-scale-set.png)

3. Geben Sie Werte in die leeren Felder ein, und wählen Sie aus den Dropdownlisten das **Betriebssystem-Datenträgerimage**,das **Abonnement**, und die **Instanzgröße** aus. Wählen Sie **Ja** für **Verwaltete Datenträger verwenden** aus. Klicken Sie dann auf **Erstellen**.
    ![Konfigurieren und Erstellen](media/azure-stack-compute-add-scalesets/create.png)

4. Klicken Sie zum Anzeigen Ihrer neuen VM-Skalierungsgruppe auf **Alle Ressourcen**, suchen Sie nach dem Namen der VM-Skalierungsgruppe, und klicken Sie in den Suchergebnissen auf ihren Namen.
   ![Anzeigen der Skalierungsgruppe](media/azure-stack-compute-add-scalesets/search.png)

## <a name="add-the-virtual-machine-scale-set-prior-to-version-1808"></a>Hinzufügen der VM-Skalierungsgruppe (vor Version 1808)

>[!IMPORTANT]  
> Die Informationen in diesem Abschnitt gelten bei der Verwendung einer älteren Version von Azure Stack als 1808. Wenn Sie Version 1808 oder höher verwenden, lesen Sie [verwenden des Azure Stack-Portals](#use-the-azure-stack-portal).

1. Öffnen Sie den Azure Stack-Marketplace, und stellen Sie eine Verbindung mit Azure her. Wählen Sie **Marketplace management** (Marketplace-Verwaltung) aus, und klicken Sie auf **+ Add from Azure** (+ Aus Azure hinzufügen).

    ![Marketplace-Verwaltung](media/azure-stack-compute-add-scalesets/image01.png)

2. Fügen Sie das Marketplace-Element für VM-Skalierungsgruppen (Virtual Machine Scale Sets) hinzu, und laden Sie es herunter.

    ![VM-Skalierungsgruppe](media/azure-stack-compute-add-scalesets/image02.png)

## <a name="update-images-in-a-virtual-machine-scale-set"></a>Aktualisieren von Images in einer VM-Skalierungsgruppe

Nach der Erstellung einer VM-Skalierungsgruppe können Benutzer Images in der Skalierungsgruppe aktualisieren, ohne dass diese neu erstellt werden muss. Die Vorgehensweise zum Aktualisieren eines Images hängt von den folgenden Szenarien ab:

1. In der Bereitstellungsvorlage der VM-Skalierungsgruppe ist **latest** für **version** angegeben:  

   Wenn im Abschnitt `imageReference` der Vorlage für eine Skalierungsgruppe `version` auf **latest** festgelegt ist, nutzen Vorgänge zum zentralen Hochskalieren für die Skalierungsgruppe die neueste verfügbare Version des Images für die Skalierungsgruppeninstanzen. Nach Abschluss eines Vorgangs zum zentralen Hochskalieren können Sie ältere VM-Skalierungsgruppeninstanzen löschen. Die Werte für `publisher`, `offer` und `sku` bleiben unverändert.

   Das folgende JSON-Beispiel gibt `latest` an:  

    ```json  
    "imageReference": {
        "publisher": "[parameters('osImagePublisher')]",
        "offer": "[parameters('osImageOffer')]",
        "sku": "[parameters('osImageSku')]",
        "version": "latest"
        }
    ```

   Bevor beim zentralen Hochskalieren ein neues Image verwendet werden kann, müssen Sie dieses Image herunterladen:  

   * Wenn das Image im Marketplace neuer ist als das Image in der Skalierungsgruppe, laden Sie das neue Image herunter, das das ältere Image ersetzt. Nachdem der Benutzer das Image ersetzt hat, kann er mit dem zentralen Hochskalieren fortfahren.

   * Wenn die Imageversion im Marketplace der Imageversion in der Skalierungsgruppe entspricht, löschen Sie das in der Skalierungsgruppe verwendete Image, und laden Sie das neue Image herunter. In der Zeit zwischen dem Entfernen des ursprünglichen Images und dem Herunterladen des neuen Images können Sie nicht zentral hochskalieren.

     Bei diesem Vorgang müssen Images neu syndiziert werden, die das in der Version 1803 eingeführte platzsparende Dateiformat verwenden.

2. In der Bereitstellungsvorlage der VM-Skalierungsgruppe ist **nicht** „latest“ für **version**, sondern eine Versionsnummer angegeben:  

    Wenn Sie ein Image einer neueren Version herunterladen (sodass sich die verfügbare Version ändert), kann die Skalierungsgruppe nicht zentral hochskaliert werden. Dies ist beabsichtigt, da die in der Skalierungsgruppenvorlage angegebene Imageversion verfügbar sein muss.  

Weitere Informationen finden Sie unter [Einführung in virtuelle Azure Stack-Computer](../user/azure-stack-compute-overview.md#operating-system-disks-and-images).  

## <a name="scale-a-virtual-machine-scale-set"></a>Skalieren einer VM-Skalierungsgruppe

Sie können die Größe einer *VM-Skalierungsgruppe* skalieren, um sie zu vergrößern oder zu verkleinern.  

1. Wählen Sie im Portal Ihre Skalierungsgruppe aus, und wählen Sie dann **Skalierung** aus.

2. Verwenden Sie den Schieberegler, um die neue Ebene der Skalierung für diese VM-Skalierungsgruppe festzulegen, und klicken Sie dann auf **Speichern**.

     ![Skalieren der Gruppe](media/azure-stack-compute-add-scalesets/scale.png)

## <a name="remove-a-virtual-machine-scale-set"></a>Entfernen einer VM-Skalierungsgruppe

Führen Sie zum Entfernen eines Katalogelements einer VM-Skalierungsgruppe den folgenden PowerShell-Befehl aus:

```powershell  
Remove-AzsGalleryItem
```

> [!NOTE]
> Das Katalogelement wird unter Umständen nicht sofort entfernt. Möglicherweise müssen Sie das Portal mehrmals aktualisieren, bevor zu sehen ist, dass das Element aus dem Marketplace entfernt wurde.

## <a name="next-steps"></a>Nächste Schritte

* [Herunterladen von Marketplace-Elementen von Azure in Azure Stack](azure-stack-download-azure-marketplace-item.md)