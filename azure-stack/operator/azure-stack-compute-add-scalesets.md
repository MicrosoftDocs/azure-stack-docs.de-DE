---
title: Bereitstellen von VM-Skalierungsgruppen in Azure Stack Hub
description: Erfahren Sie, wie Cloudoperator dem Azure Stack Hub-Marketplace VM-Skalierungsgruppen hinzufügen können.
author: sethmanheim
ms.topic: article
ms.date: 10/07/2020
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 10/22/2019
ms.openlocfilehash: a69f29a168853a2cb2bc1c757f90185e0b56636e
ms.sourcegitcommit: 2d2ae0b6db2e4f43f8496b184f30cddbb08b2cbd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2020
ms.locfileid: "91814993"
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack-hub"></a>Bereitstellen von VM-Skalierungsgruppen in Azure Stack Hub

VM-Skalierungsgruppen sind eine Azure Stack Hub-Computeressource. Verwenden Sie Skalierungsgruppen zum Bereitstellen und Verwalten eines Satzes identischer virtueller Computer (VMs). Da alle VMs gleich konfiguriert sind, ist bei Skalierungsgruppen keine Vorabbereitstellung der VMs erforderlich. Es ist einfacher, umfangreiche Dienste zu erstellen, die auf hohe Rechenleistung, Big Data und Workloads in Containern ausgelegt sind.

Dieser Artikel beschreibt, wie Skalierungsgruppen im Azure Stack Hub-Marketplace verfügbar gemacht werden. Wenn Sie dieses Verfahren abgeschlossen haben, können Ihre Benutzer VM-Skalierungsgruppen zu ihren Abonnements hinzufügen.

VM-Skalierungsgruppen in Azure Stack Hub ähneln VM-Skalierungsgruppen in Azure. Weitere Informationen finden Sie in den folgenden Videos:

* [Mark Russinovich spricht über Azure-Skalierungsgruppen](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)
* [VM-Skalierungsgruppen mit Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

In Azure Stack Hub unterstützen VM-Skalierungsgruppen keine automatische Skalierung. Sie können einer Skalierungsgruppe weitere Instanzen hinzufügen, indem Sie Resource Manager-Vorlagen, die CLI oder PowerShell verwenden.

## <a name="prerequisites"></a>Voraussetzungen

* **Azure Stack Hub Marketplace:** Registrieren Sie Azure Stack Hub global für Azure, um Elemente im Azure Stack Hub-Marketplace verfügbar zu machen. Befolgen Sie die Anleitung unter [Registrieren von Azure Stack Hub in Azure](azure-stack-registration.md).
* **Betriebssystemimage**: Bevor eine VM-Skalierungsgruppe erstellt werden kann, müssen Sie die VM-Images, die darin verwendet werden sollen, aus dem [Azure Stack Hub-Marketplace](azure-stack-download-azure-marketplace-item.md) herunterladen. Die Images müssen bereits vorhanden sein, damit ein Benutzer eine neue Skalierungsgruppe erstellen kann.

## <a name="use-the-azure-stack-hub-portal"></a>Verwenden des Azure Stack Hub-Portals

1. Melden Sie sich beim Azure Stack Hub-Portal an. Navigieren Sie dann zu **Alle Dienste** und **VM-Skalierungsgruppen**, und wählen Sie unter **COMPUTE** die Option **VM-Skalierungsgruppen** aus.
   [![Auswählen von VM-Skalierungsgruppen](media/azure-stack-compute-add-scalesets/all-services-small.png)](media/azure-stack-compute-add-scalesets/all-services.png#lightbox)

2. Wählen Sie **Hinzufügen**.

   ![Erstellen einer Skalierungsgruppe für virtuelle Computer](media/azure-stack-compute-add-scalesets/create-scale-set.png)

3. Geben Sie Werte in die leeren Felder ein, und wählen Sie aus den Dropdownlisten das **Betriebssystem-Datenträgerimage**,das **Abonnement**, und die **Instanzgröße** aus. Wählen Sie **Ja** für **Verwaltete Datenträger verwenden** aus. Wählen Sie anschließend **Erstellen**.
    [![Konfigurieren und Erstellen von VM-Skalierungsgruppen](media/azure-stack-compute-add-scalesets/create-small.png)](media/azure-stack-compute-add-scalesets/create.png#lightbox)

4. Klicken Sie zum Anzeigen Ihrer neuen VM-Skalierungsgruppe auf **Alle Ressourcen**, suchen Sie nach dem Namen der VM-Skalierungsgruppe, und klicken Sie in den Suchergebnissen auf ihren Namen.
   [![Anzeigen der VM-Skalierungsgruppe](media/azure-stack-compute-add-scalesets/search-small.png)](media/azure-stack-compute-add-scalesets/search.png#lightbox)

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

2. In der Bereitstellungsvorlage der VM-Skalierungsgruppe ist **nicht** „latest“ für **version**, sondern eine Versionsnummer angegeben:  

    Wenn der Azure Stack Hub-Betreiber ein Image mit einer neueren Version herunterlädt (und die ältere Version löscht), kann die Skalierungsgruppe nicht hochskaliert werden. Dies ist beabsichtigt, da die in der Skalierungsgruppenvorlage angegebene Imageversion verfügbar sein muss.  

Weitere Informationen finden Sie unter [Einführung in virtuelle Azure Stack-Computer](../user/azure-stack-compute-overview.md#operating-system-disks-and-images).  

## <a name="scale-a-virtual-machine-scale-set"></a>Skalieren einer VM-Skalierungsgruppe

Sie können die Größe einer VM-Skalierungsgruppe ändern, um sie zu vergrößern oder zu verkleinern.

1. Wählen Sie im Portal Ihre Skalierungsgruppe aus, und wählen Sie dann **Skalierung** aus.

2. Verwenden Sie den Schieberegler, um die neue Ebene der Skalierung für diese VM-Skalierungsgruppe festzulegen, und klicken Sie dann auf **Speichern**.

     [![Skalieren der VM-Skalierungsgruppe](media/azure-stack-compute-add-scalesets/scale-small.png)](media/azure-stack-compute-add-scalesets/scale.png#lightbox)

## <a name="next-steps"></a>Nächste Schritte

* [Herunterladen von Marketplace-Elementen von Azure in Azure Stack Hub](azure-stack-download-azure-marketplace-item.md)
