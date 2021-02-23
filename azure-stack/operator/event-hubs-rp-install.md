---
title: Installieren von Event Hubs in Azure Stack Hub
description: Erfahren Sie, wie Sie den Event Hubs-Ressourcenanbieter in Azure Stack Hub installieren.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 09/02/2020
ms.reviewer: jfggdl
ms.lastreviewed: 09/02/2020
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: 7f20866236099dadc7e6ca3e3c201572f51069ca
ms.sourcegitcommit: 34babe5abf1bceee718011b5c5c25f75e1b03b0c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100562487"
---
# <a name="how-to-install-event-hubs-on-azure-stack-hub"></a>Installieren von Event Hubs in Azure Stack Hub

In diesem Artikel erfahren Sie, wie Sie den Event Hubs-Ressourcenanbieter herunterladen und installieren, damit er Kunden zum Abonnieren angeboten werden kann. Sie müssen auch die [Voraussetzungen für eine Event Hubs-Installation](event-hubs-rp-prerequisites.md) erfüllen, bevor Sie fortfahren.

## <a name="download-packages"></a>Herunterladen von Paketen

Bevor Sie Event Hubs in Azure Stack Hub installieren können, müssen Sie den Ressourcenanbieter und dessen abhängige Pakete mit dem Marketplace-Verwaltungsfeature herunterladen. Wenn Sie nicht mit der Marketplace-Verwaltung vertraut sind, finden Sie weitere Informationen unter [Herunterladen von Marketplace-Elementen in Azure Stack Hub](azure-stack-download-azure-marketplace-item.md). In diesem Abschnitt werden die Schritte zum Herunterladen von Elementen aus dem Azure Marketplace erläutert. 

> [!NOTE]
> Der Downloadvorgang kann zwischen 30 Minuten und 2 Stunden dauern, je nach Netzwerklatenz und vorhandenen Paketen in Ihrer Azure Stack Hub-Instanz. 

::: zone pivot="state-connected"
Bei einem verbundenen Szenario laden Sie die Elemente aus dem Azure Marketplace direkt in den Azure Stack Hub-Marketplace herunter:

1. Melden Sie sich beim Azure Stack Hub-Administratorportal an.
2. Wählen Sie auf der linken Seite die Option **Marketplace-Verwaltung** aus.
3. Wählen Sie **Ressourcenanbieter** aus.
4. Wählen Sie **+ Add from Azure** (+ Aus Azure hinzufügen) aus.
5. Suchen Sie über die Suchleiste nach „Event Hubs“.
6. Wählen Sie in den Suchergebnissen die Zeile „Event Hubs“ aus. 
7. Wählen Sie auf der Downloadseite „Event Hubs“ die Event Hubs-Version aus, die Sie installieren möchten, und klicken Sie dann unten auf der Seite auf **Herunterladen**. 
   [![Pakete in Marketplace-Verwaltung](media/event-hubs-rp-install/1-marketplace-management-download.png)](media/event-hubs-rp-install/1-marketplace-management-download.png#lightbox)

Beachten Sie, dass zusätzliche Softwarepakete zusammen mit Event Hubs heruntergeladen werden, einschließlich:

- Microsoft Azure Stack Hub Add-On RP Windows Server INTERNAL ONLY
- PowerShell Desired State Configuration
::: zone-end

::: zone pivot="state-disconnected"
Für ein getrenntes oder teilweise verbundenes Szenario laden Sie die Pakete auf Ihren lokalen Computer herunter, und importieren Sie diese dann in Ihren Azure Stack Hub-Marketplace:

1. Sofern noch nicht geschehen, führen Sie die Schritte unter [Herunterladen von Marketplace-Elementen: nicht verbundenes oder partiell verbundenes Szenario](azure-stack-download-azure-marketplace-item.md?pivots=state-disconnected) aus. Hier laden Sie das Marketplace-Syndikation-Tool herunter und führen es aus. Mit diesem Tool können Sie die Event Hubs-Pakete herunterladen.
2. Nachdem das Fenster „Azure Marketplace-Elemente“ des Syndikation-Tools geöffnet wurde, suchen Sie nach „Event Hubs“, und wählen Sie diesen Eintrag aus, um die erforderlichen Pakete auf Ihren lokalen Computer herunterzuladen.
3. Nachdem der Download abgeschlossen ist, importieren Sie die Pakete in Ihre Azure Stack Hub-Instanz, und veröffentlichen Sie sie im Marketplace. 
::: zone-end

## <a name="installation"></a>Installation 

1. Wenn dies noch nicht geschehen ist, melden Sie am Azure Stack Hub-Administratorportal an, wählen Sie auf der linken Seite **Marketplace-Verwaltung** aus, und wählen Sie **Ressourcenanbieter** aus.
2. Nachdem Event Hubs und weitere erforderliche Software heruntergeladen wurden, werden die „Event Hubs“-Pakete in **Marketplace-Verwaltung** mit dem Status „Nicht installiert“ angezeigt. Möglicherweise gibt es andere Pakete, die mit dem Status „Heruntergeladen“ angezeigt werden. Wählen Sie die Zeile für die „Event Hubs“-Version aus, die Sie installieren möchten.
   [![Heruntergeladene Pakete in Marketplace-Verwaltung](media/event-hubs-rp-install/2-marketplace-management-downloaded.png)](media/event-hubs-rp-install/2-marketplace-management-downloaded.png#lightbox)
 
3. Auf der Seite für Event Hubs- Installationspakete wird oben ein blaues Banner angezeigt. Wählen Sie das Banner aus, um die Installation von Event Hubs zu starten.
   [![Verwaltung von Event Hubs im Marketplace: Starten der Installation](media/event-hubs-rp-install/3-marketplace-management-install-ready.png)](media/event-hubs-rp-install/3-marketplace-management-install-ready.png#lightbox)

### <a name="install-prerequisites"></a>Installieren der erforderlichen Komponenten

1. Als nächstes gelangen Sie zur Installationsseite. Wählen Sie **Voraussetzungen installieren** aus, um den Installationsvorgang zu starten.
   ![Verwaltung von Event Hubs im Marketplace: Installationsvoraussetzungen](media/event-hubs-rp-install/4-marketplace-management-install-prereqs-start.png)
 
2. Warten Sie, bis die Installation der Voraussetzungen erfolgreich abgeschlossen ist. Ein grünes Häkchen sollte neben **Voraussetzungen installieren** angezeigt werden, bevor Sie zum nächsten Schritt wechseln.

   ![Verwaltung von Event Hubs im Marketplace: Erfüllen der Installationsvoraussetzungen](media/event-hubs-rp-install/5-marketplace-management-install-prereqs-succeeded.png)

### <a name="prepare-secrets"></a>Vorbereiten der Geheimnisse 

1. Wählen Sie unter dem Schritt **2. Geheimnisse vorbereiten** den Link **Zertifikat hinzufügen** aus. Daraufhin wird der Bereich **Zertifikat hinzufügen** angezeigt.
   ![Marketplace-Verwaltung, Event Hubs: Geheimnisse vorbereiten](media/event-hubs-rp-install/6-marketplace-management-install-prepare-secrets.png)

2. Wählen Sie die Schaltfläche für Durchsuchen unter **Zertifikat hinzufügen** aus, die sich direkt rechts neben dem Feld mit dem Dateinamen des Zertifikats befindet.
3. Wählen Sie die PFX-Zertifikatsdatei aus, die Sie beim Ausführen der Schritte für die Voraussetzungen beschafft haben. Weitere Informationen finden Sie unter[Voraussetzungen für das Installieren von Event Hubs in Azure Stack Hub](event-hubs-rp-prerequisites.md). 

4. Geben Sie das Kennwort ein, das Sie angegeben haben, um eine sichere Zeichenfolge für das Event Hubs-SSL-Zertifikat zu erstellen. Wählen Sie anschließend **Hinzufügen**.
   ![Marketplace-Verwaltung, Event Hubs: Zertifikat hinzufügen](media/event-hubs-rp-install/7-marketplace-management-install-prepare-secrets-add-cert.png)

### <a name="install-resource-provider"></a>Installieren des Ressourcenanbieters

1. Wenn das Zertifikat erfolgreich installiert wurde, sollte ein grünes Häkchen neben **Geheimnisse vorbereiten** angezeigt werden. Ist dies der Fall, wechseln Sie zum nächsten Schritt. Wählen Sie nun die Schaltfläche **Installieren** neben **3. Ressourcenanbieter installieren** aus.
   ![Verwaltung von Event Hubs im Marketplace: Starten der Installation des Ressourcenanbieters](media/event-hubs-rp-install/8-marketplace-management-install-start.png)
 
2. Als nächstes wird die folgende Seite angezeigt, auf der mitgeteilt wird, dass der Event Hubs-Ressourcenanbieter installiert wird.
   [![Verwaltung von Event Hubs im Marketplace: Installation des Ressourcenanbieters](media/event-hubs-rp-install/9-marketplace-management-install-inprogress.png)](media/event-hubs-rp-install/9-marketplace-management-install-inprogress.png#lightbox)
 
3. Warten Sie, bis die Benachrichtigung angezeigt wird, dass die Installation abgeschlossen ist. Dieser Vorgang dauert in der Regel eine oder mehrere Stunden, je nach Ihrem Azure Stack Hub-Typ. 
   [![Verwaltung von Event Hubs im Marketplace: Abschließen der Installation des Ressourcenanbieters](media/event-hubs-rp-install/10-marketplace-management-install-complete.png)](media/event-hubs-rp-install/10-marketplace-management-install-complete.png#lightbox)

4. Überprüfen Sie, ob die Installation von Event Hubs erfolgreich war. Kehren Sie dazu zur Seite **Marketplace-Verwaltung**, **Ressourcenanbieter** zurück. Für den Status von Event Hubs sollte „Installiert“ angezeigt werden.
   ![Marketplace-Verwaltung, Event Hubs verfügbar](media/event-hubs-rp-install/11-marketplace-management-rps-installed.png)

## <a name="next-steps"></a>Nächste Schritte

Bevor Benutzer Event Hubs-Ressourcen bereitstellen können, müssen Sie mindestens einen Plan, ein Angebot und ein Abonnement erstellen. 

- Wenn Sie zum ersten Mal einen Dienst anbieten, sollten Sie mit dem Tutorial [Erstellen eines Dienstangebots für Benutzer in Azure Stack Hub](tutorial-offer-services.md) beginnen. Arbeiten Sie dann das nächste Tutorial, [Testen eines Dienstangebots](tutorial-test-offer.md), durch.
- Wenn Sie mit dem Konzept der Angebotserstellung vertraut sind, erstellen Sie ein Angebot und einen Plan, der den Event Hubs-Ressourcenanbieter umfasst. Erstellen Sie dann ein Abonnement für Ihre Benutzer, oder teilen Sie ihnen die Angebotsinformationen mit, damit sie ihr eigenes Abonnement erstellen können. Zum besseren Verständnis können Sie auch der Artikelreihe unter [Übersicht: Azure Stack Hub-Dienste, -Pläne, -Angebote und Abonnements](service-plan-offer-subscription-overview.md) folgen.

Wenn Sie auf Updates prüfen möchten, wechseln Sie zu [Aktualisieren von Event Hubs in Azure Stack Hub](resource-provider-apply-updates.md).

Wenn Sie den Ressourcenanbieter entfernen müssen, lesen Sie [Entfernen des Event Hubs-Ressourcenanbieters](event-hubs-rp-remove.md).

Weitere Informationen zur Benutzerumgebung finden Sie unter [Event Hubs in Azure Stack Hub – Übersicht](../user/event-hubs-overview.md) in den Benutzerdokumenten.