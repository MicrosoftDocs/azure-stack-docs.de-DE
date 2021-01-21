---
title: Installation von IoT Hub in Azure Stack Hub
description: In diesem Artikel erfahren Sie, wie Sie den IoT Hub-Ressourcenanbieter in Azure Stack Hub installieren.
author: yiyiguo
ms.author: yiygu
ms.service: azure-stack
ms.topic: how-to
ms.date: 1/6/2020
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: 47d06bc7363a9ce8d4de8971bf26c38bfdb16bcc
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98256114"
---
# <a name="how-to-install-iot-hub-on-azure-stack-hub"></a>Installation von IoT Hub in Azure Stack Hub

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

In diesem Artikel erfahren Sie, wie Sie den IoT Hub-Ressourcenanbieter herunterladen und installieren, damit er Kunden zum Abonnieren angeboten werden kann. Der Installationsprozess von IoT Hub dauert etwa 2 Stunden.

## <a name="download-iot-hub"></a>Herunterladen von IoT Hub

<!-- ### Connected Scenario -->
::: zone pivot="state-connected"
Wenn Ihre Azure Stack Hub-Instanz auf den Azure Marketplace zugreifen kann, führen Sie die in diesem Abschnitt beschriebenen Schritte aus, um IoT Hub und die Abhängigkeiten herunterzuladen und zu installieren. 

Führen Sie die folgenden Schritte aus, um IoT Hub für eine verbundene Bereitstellung herunterzuladen:

1. Melden Sie sich beim Azure Stack Hub-Administratorportal an. 
2. Klicken Sie links auf **Marketplace-Verwaltung**, wählen Sie dann **Ressourcenanbieter** aus, und klicken Sie anschließend auf **+ Add from Azure** (Aus Azure hinzufügen).

    [![Suche nach Ressourcenanbietern im Marketplace](media/iot-hub-rp-install/marketplace-rp-add-from-azure.png)](media/iot-hub-rp-install/marketplace-rp-add-from-azure.png#lightbox)

3. Filtern Sie bei Bedarf nach „IoT Hub“, und wählen Sie dann das **IoT Hub**-Paket aus.

    [![IoT Hub-Ressourcenanbieter im Marketplace](../operator/media/iot-hub-rp-install/download1.png)](../operator/media/iot-hub-rp-install/download1.png#lightbox)

4. Klicken Sie auf der Seite für das **IoT Hub**-Paket auf **Herunterladen**.

    [![IoT Hub-Paketdetails](../operator/media/iot-hub-rp-install/download2.png)](../operator/media/iot-hub-rp-install/download2.png#lightbox)

5. Warten Sie auf den Abschluss des Paketdownloads. Der Status sollte **Wird heruntergeladen** lauten. Der Prozess kann bis zu 10 Minuten dauern.

    [![Status des IoT Hub-Paketdownloads](../operator/media/iot-hub-rp-install/download3.png)](../operator/media/iot-hub-rp-install/download3.png#lightbox)

6. Sobald das Paket heruntergeladen wurde, wird der Status auf der Seite **Marketplace-Verwaltung** in **Nicht installiert** geändert.

    [![Heruntergeladenes, aber noch nicht installiertes IoT Hub-Paket](../operator/media/iot-hub-rp-install/download4.png)](../operator/media/iot-hub-rp-install/download4.png#lightbox)
::: zone-end

<!-- ### Disconnected or partially connected scenario -->
::: zone pivot="state-disconnected"
Laden Sie die Pakete zunächst auf Ihren lokalen Computer herunter, um IoT Hub für eine nicht oder nur teilweise verbundene Bereitstellung herunterzuladen. Nach Abschluss des Downloads importieren Sie diese in Ihre Azure Stack Hub-Instanz.

1. Sofern noch nicht geschehen, führen Sie die Schritte unter [Herunterladen von Marketplace-Elementen: nicht verbundenes oder partiell verbundenes Szenario](azure-stack-download-azure-marketplace-item.md?pivots=state-disconnected) aus. Hier laden Sie das Marketplace-Syndikationstool herunter und führen es aus. Mit diesem Tool können Sie die IoT Hub-Pakete herunterladen.
2. Nachdem das Fenster „Azure Marketplace-Elemente“ des Syndikationstools geöffnet wurde, suchen Sie nach „IoT Hub“, und wählen Sie diesen Eintrag aus, um die erforderlichen Pakete auf Ihren lokalen Computer herunterzuladen.
3. Nachdem der Download abgeschlossen ist, importieren Sie die Pakete in Ihre Azure Stack Hub-Instanz, und veröffentlichen Sie sie im Marketplace.
::: zone-end

## <a name="install-iot-hub"></a>Installieren von IoT Hub

Führen Sie die folgenden Schritte auf der Seite **Marketplace-Verwaltung** aus, um das IoT Hub-Paket zu installieren:

1. Klicken Sie auf die **IoT Hub**-Zeile und dann auf **Installation starten**.

    [![IoT Hub-Ressourcenanbieter wartet auf Installation](../operator/media/iot-hub-rp-install/install1.png)](../operator/media/iot-hub-rp-install/install1.png#lightbox)

2. Klicken Sie auf **Erforderliche Komponenten installieren**.

    [![Erforderliche Komponenten für die Installation des IoT Hub-Ressourcenanbieters](../operator/media/iot-hub-rp-install/install2.png)](../operator/media/iot-hub-rp-install/install2.png#lightbox)

3. Der Status der Installation kann im Benachrichtigungsbereich nachverfolgt werden. Dieser Schritt dauert etwa 10 Minuten.

    [![Erforderliche Komponenten für den IoT Hub-Ressourcenanbieter werden installiert](../operator/media/iot-hub-rp-install/install3.png)](../operator/media/iot-hub-rp-install/install3.png#lightbox)

4. Warten Sie, bis die Installation der erforderlichen Komponenten abgeschlossen ist. Dieser Schritt dauert in der Regel je nach Umgebung 5 bis 10 Minuten.

    [![Vorbereiten der Geheimnisse für den IoT Hub-Ressourcenanbieter](../operator/media/iot-hub-rp-install/install4.png)](../operator/media/iot-hub-rp-install/install4.png#lightbox)

5. Klicken Sie auf **Zertifikate hinzufügen** unter **Prepare Secrets** (Geheimnisse vorbereiten). Geben Sie das PFX-Zertifikat an, das mit den erforderlichen Komponenten erstellt wurde.

    [![Vorbereiten der Geheimnisse für den IoT Hub-Ressourcenanbieter: Zertifikat hochladen](../operator/media/iot-hub-rp-install/install5.png)](../operator/media/iot-hub-rp-install/install5.png#lightbox)

6. Suchen Sie nach dem erstellten PFX-Zertifikat, und geben Sie dieses und das Kennwort (die Eingabe für das Skript) an.

    [![Vorbereiten der Geheimnisse für den IoT Hub-Ressourcenanbieter: Zertifikat auswählen](../operator/media/iot-hub-rp-install/install6.png)](../operator/media/iot-hub-rp-install/install6.png#lightbox)

    [![Vorbereiten der Geheimnisse für den IoT Hub-Ressourcenanbieter: Kennwort angeben](../operator/media/iot-hub-rp-install/install61.png)](../operator/media/iot-hub-rp-install/install61.png#lightbox)

7. Klicken Sie unter **Ressourcenanbieter installieren** auf **Installieren**.

    [![Vorbereiten der Geheimnisse für den IoT Hub-Ressourcenanbieter: Abschluss](../operator/media/iot-hub-rp-install/install7.png)](../operator/media/iot-hub-rp-install/install7.png#lightbox)

8. Sobald die Installation startet, können Sie den Bereitstellungsstatus im Marketplace oder im Benachrichtigungsbereich einsehen.

    [![Fortschritt der Installation des IoT Hub-Ressourcenanbieters](../operator/media/iot-hub-rp-install/install8.png)](../operator/media/iot-hub-rp-install/install8.png#lightbox)

9. Die Installation kann 90 bis 120 Minuten dauern. Warten Sie bis zum Abschluss der Installation.

    [![Installation des IoT Hub-Ressourcenanbieters abgeschlossen](../operator/media/iot-hub-rp-install/install91.png)](../operator/media/iot-hub-rp-install/install91.png#lightbox)

    [![Marketplace-Ressourcenanbieter: Installierte Ressourcenanbieter](../operator/media/iot-hub-rp-install/install92.png)](../operator/media/iot-hub-rp-install/install92.png#lightbox)

Der IoT Hub-Ressourcenanbieter wurde erfolgreich installiert. Führen Sie nun die folgenden ersten Schritte aus:

1. Führen Sie bei Bedarf die Anweisungen zum [Erstellen von Tarifen, Angeboten und Abonnements](./service-plan-offer-subscription-overview.md) aus.

2. Wenn bereits ein Abonnement vorliegt, aktualisieren Sie das zugehörige Angebot bzw. den Tarif, sodass er den Dienst **Microsoft.Devices** enthält. Navigieren Sie zu **Plan** -> **Choose the plan to update** -> **Add Service and quota** (Tarif > Wählen Sie den zu ändernden Tarif aus > Dienst und Kontingent hinzufügen).

3. Fügen Sie den **Microsoft.Devices-Dienst** hinzu, und klicken Sie auf **Speichern**.

    [![Hinzufügen des IoT Hub-Diensts zum Tarif](../operator/media/iot-hub-rp-install/pd2.png)](../operator/media/iot-hub-rp-install/pd2.png#lightbox)

4. Jetzt sind Sie fertig. Nun können IoT Hub-Instanzen erstellt werden.

## <a name="using-iot-hub"></a>Verwenden von IoT Hub

Informationen zur Verwendung von IoT Hub finden Sie in der [Azure IoT Hub-Dokumentation](/azure/iot-hub).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen über die Verwaltung von IoT Hub in Azure Stack Hub finden Sie unter [Verwalten von IoT Hub in Azure Stack Hub](iot-hub-rp-manage.md).