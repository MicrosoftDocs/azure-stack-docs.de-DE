---
title: Installieren von Event Hubs in Azure Stack Hub
description: Erfahren Sie, wie Sie den Event Hubs-Ressourcenanbieter in Azure Stack Hub installieren.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/09/2019
ms.reviewer: jfggdl
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: e07d311c8edbe140834a020af489ae49d8380d86
ms.sourcegitcommit: dd53af1b0fc2390de162d41e3d59545d1baad1a7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80424602"
---
# <a name="how-to-install-event-hubs-on-azure-stack-hub"></a>Installieren von Event Hubs in Azure Stack Hub

[!INCLUDE [preview-banner](../includes/event-hubs-preview.md)]

In diesem Artikel erfahren Sie, wie Sie den Event Hubs-Ressourcenanbieter herunterladen und installieren, damit er Kunden zum Abonnieren angeboten werden kann.

## <a name="download-packages"></a>Herunterladen von Paketen

Bevor Sie Event Hubs in Azure Stack Hub installieren können, müssen Sie den Ressourcenanbieter und dessen abhängige Pakete herunterladen. Abhängig von ihrer Situation oder Ihren Anforderungen haben Sie zwei Optionen:

- Herunterladen von Event Hubs in einem verbundenen Szenario
- Herunterladen von Event Hubs in einem nicht verbundenen oder partiell verbundenen Szenario

Wenn Sie nicht mit dem **Marketplace-Verwaltung**-Feature des Azure Stack Hub-Administratorportals vertraut sind, sollten Sie [Herunterladen von Marketplace-Elementen in Azure Stack Hub](azure-stack-download-azure-marketplace-item.md) lesen. In diesem Artikel werden Sie durch die Schritte geleitet, mit denen Elemente aus Azure in den Azure Stack Hub-Marketplace heruntergeladen werden. Dieser Artikel gilt sowohl für ein verbundenes als auch für ein nicht verbundenes Szenario. 

### <a name="download-event-hubs---connected-scenario"></a>Herunterladen von Event Hubs: verbundenes Szenario

> [!NOTE]
> Der Downloadvorgang kann zwischen 30 Minuten und 2 Stunden dauern, je nach Netzwerklatenz und vorhandenen Paketen in Ihrer Azure Stack Hub-Instanz. 

Befolgen Sie diese Anweisungen, wenn Ihr Azure Stack Hub eine Internet-Verbindung hat:

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

Wechseln Sie, sobald der Downloadvorgang abgeschlossen ist, zum Abschnitt [Installieren der erforderlichen Komponenten](#install-prerequisites).

### <a name="download-event-hubs---disconnected-or-partially-connected-scenario"></a>Herunterladen von Event Hubs: nicht verbundenes oder partiell verbundenes Szenario

Zunächst laden Sie die Pakete auf Ihren lokalen Computer herunter, dann importieren Sie die Pakete in Ihre Azure Stack Hub-Instanz.

1. Sofern noch nicht geschehen, führen Sie die Schritte unter [Herunterladen von Marketplace-Elementen: nicht verbundenes oder partiell verbundenes Szenario](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario) aus. Hier laden Sie das Marketplace-Syndikation-Tool herunter und führen es aus. Mit diesem Tool können Sie die Event Hubs-Pakete herunterladen.
2. Nachdem das Fenster „Azure Marketplace-Elemente“ des Syndikation-Tools geöffnet wurde, suchen Sie nach „Event Hubs“, und wählen Sie diesen Eintrag aus, um die erforderlichen Pakete auf Ihren lokalen Computer herunterzuladen.
3. Nachdem der Download abgeschlossen ist, importieren Sie die Pakete in Ihre Azure Stack Hub-Instanz, und veröffentlichen Sie sie im Marketplace. 

## <a name="installation"></a>Installation 

1. Sofern noch nicht geschehen, melden Sie sich beim Azure Stack Hub-Administratorportal an.
2. Wählen Sie auf der linken Seite **Marketplace-Verwaltung** aus, und wählen Sie dann **Ressourcenanbieter** aus.
3. Nachdem Event Hubs und weitere erforderliche Software heruntergeladen wurden, sollten die „Event Hubs“-Pakete in **Marketplace-Verwaltung** mit dem Status „Nicht installiert“ angezeigt werden. Möglicherweise gibt es andere Pakete, die mit dem Status „Heruntergeladen“ angezeigt werden. Wählen Sie die Zeile für die „Event Hubs“-Version aus, die Sie installieren möchten.
   [![Heruntergeladene Pakete in Marketplace-Verwaltung](media/event-hubs-rp-install/2-marketplace-management-downloaded.png)](media/event-hubs-rp-install/2-marketplace-management-downloaded.png#lightbox)
 
4. Auf der Installationsseite für Event Hubs sollte oben ein blaues Banner angezeigt werden. Wählen Sie das Banner aus, um die Installation von Event Hubs zu starten.
   [![Marketplace-Verwaltung, Event Hubs: Installation starten](media/event-hubs-rp-install/3-marketplace-management-install-ready.png)](media/event-hubs-rp-install/3-marketplace-management-install-ready.png#lightbox)

### <a name="install-prerequisites"></a>Installieren der erforderlichen Komponenten

1. Als nächstes gelangen Sie zur Installationsseite. Wählen Sie **Voraussetzungen installieren** aus, um den Installationsvorgang zu starten.
   ![Marketplace-Verwaltung, Event Hubs: Voraussetzungen](media/event-hubs-rp-install/4-marketplace-management-install-prereqs-start.png)
 
2. Warten Sie, bis die Installation der Voraussetzungen erfolgreich abgeschlossen ist. Ein grünes Häkchen sollte neben **Voraussetzungen installieren** angezeigt werden, bevor Sie zum nächsten Schritt wechseln.

   ![Marketplace-Verwaltung, Event Hubs: Voraussetzungen](media/event-hubs-rp-install/5-marketplace-management-install-prereqs-succeeded.png)

### <a name="prepare-secrets"></a>Vorbereiten der Geheimnisse 

1. Wählen Sie unter dem Schritt **2. Geheimnisse vorbereiten** den Link **Zertifikat hinzufügen** aus. Daraufhin wird der Bereich **Zertifikat hinzufügen** angezeigt.
   ![Marketplace-Verwaltung, Event Hubs: Geheimnisse vorbereiten](media/event-hubs-rp-install/6-marketplace-management-install-prepare-secrets.png)

2. Wählen Sie die Schaltfläche für Durchsuchen unter **Zertifikat hinzufügen** aus, die sich direkt rechts neben dem Feld mit dem Dateinamen des Zertifikats befindet.
3. Wählen Sie die PFX-Zertifikatsdatei aus, die Sie beim Ausführen der Schritte für die Voraussetzungen beschafft haben. Weitere Informationen finden Sie unter[Voraussetzungen für das Installieren von Event Hubs in Azure Stack Hub](event-hubs-rp-prerequisites.md). 

4. Geben Sie das Kennwort ein, das Sie angegeben haben, um eine sichere Zeichenfolge für das Event Hubs-SSL-Zertifikat zu erstellen. Wählen Sie anschließend **Hinzufügen**.
   ![Marketplace-Verwaltung, Event Hubs: Zertifikat hinzufügen](media/event-hubs-rp-install/7-marketplace-management-install-prepare-secrets-add-cert.png)

### <a name="install-resource-provider"></a>Installieren des Ressourcenanbieters

1. Wenn das Zertifikat erfolgreich installiert wurde, sollte ein grünes Häkchen neben **Geheimnisse vorbereiten** angezeigt werden. Ist dies der Fall, wechseln Sie zum nächsten Schritt. Wählen Sie nun die Schaltfläche **Installieren**neben **3. Ressourcenanbieter installieren** aus.
   ![Marketplace-Verwaltung, Event Hubs: Installation starten](media/event-hubs-rp-install/8-marketplace-management-install-start.png)
 
2. Als nächstes wird die folgende Seite angezeigt, auf der mitgeteilt wird, dass der Event Hubs-Ressourcenanbieter installiert wird.
   [![Marketplace-Verwaltung, Event Hubs: Installation wird ausgeführt](media/event-hubs-rp-install/9-marketplace-management-install-inprogress.png)](media/event-hubs-rp-install/9-marketplace-management-install-inprogress.png#lightbox)
 
3. Warten Sie, bis die Benachrichtigung angezeigt wird, dass die Installation abgeschlossen ist. Dieser Vorgang dauert in der Regel eine oder mehrere Stunden, je nach Ihrem Azure Stack Hub-Typ. 
   [![Marketplace-Verwaltung, Event Hubs: Installation abgeschlossen](media/event-hubs-rp-install/10-marketplace-management-install-complete.png)](media/event-hubs-rp-install/10-marketplace-management-install-complete.png#lightbox)

4. Überprüfen Sie, ob die Installation von Event Hubs erfolgreich war. Kehren Sie dazu zur Seite **Marketplace-Verwaltung**, **Ressourcenanbieter** zurück. Für den Status von Event Hubs sollte „Installiert“ angezeigt werden.
   ![Marketplace-Verwaltung, Event Hubs verfügbar](media/event-hubs-rp-install/11-marketplace-management-rps-installed.png)

## <a name="register-event-hubs"></a>Registrieren von Event Hubs

Sie müssen nun den Event Hubs-Ressourcenanbieter registrieren. Die Registrierung ermöglicht es Ihnen, den Dienst über die Event Hubs-Verwaltungsseite zu verwalten.

1. Wählen Sie im Administratorportal oben links die Option **Alle Dienste** aus.
2. Wählen Sie **Abonnements**. Eine Liste mit Abonnements wird angezeigt. 
   > [!NOTE]
   > Achten Sie darauf, dass Sie nicht **Benutzerabonnements** auswählen.
3. Wählen Sie **Standardanbieterabonnement** auf der Seite **Abonnements** aus.
4. Wählen Sie **Ressourcenanbieter** links auf der Seite **Standardanbieterabonnement** aus.
5. Suchen Sie im oben angezeigten Feld **Nach Name filtern** nach der Zeichenfolge „EventHub“.
6. Sehen Sie sich die **Status**-Spalte der Ressourcenanbieterzeilen „Microsoft.EventHub“ und „Microsoft.EventHub.Admin“ an.
7. Wird der Status in einer der Zeilen als „Nicht registriert“ angezeigt, wählen Sie den Anbieter aus, und wählen Sie dann **Registrieren** aus. 
   ![Nicht registrierte Ressourcenanbieter](media/event-hubs-rp-install/12-default-subscription-rps-unregistered.png)
8. Wählen Sie nach einigen Sekunden **Aktualisieren** aus. Der Ressourcenanbieter sollte nun mit dem Status „Registriert“ angezeigt werden. 
9. „Microsoft.EventHub“ und „Microsoft.EventHub.Admin“ sollten nun mit dem Status „Registriert“ angezeigt werden.
   ![Registrierte Ressourcenanbieter](media/event-hubs-rp-install/13-default-subscription-rps-registered.png)

10. Kehren Sie zur Seite **Alle Dienste** zurück.
11. Suchen Sie nach „Event Hubs“. Nun sollte „Event Hubs“ angezeigt werden. Dies ist Ihr Einstiegspunkt zur Event Hubs-Verwaltungsseite. 
   ![Dienste verfügbar: Event Hubs](media/event-hubs-rp-install/14-all-service-event-hubs.png)
 
## <a name="next-steps"></a>Nächste Schritte

Bevor Benutzer Event Hubs-Ressourcen bereitstellen können, müssen Sie mindestens einen Plan, ein Angebot und ein Abonnement erstellen. 

- Wenn Sie zum ersten Mal einen Dienst anbieten, sollten Sie mit dem Tutorial [Erstellen eines Dienstangebots für Benutzer in Azure Stack Hub](tutorial-offer-services.md) beginnen. Arbeiten Sie dann das nächste Tutorial, [Testen eines Dienstangebots](tutorial-test-offer.md), durch.
- Wenn Sie mit dem Konzept der Angebotserstellung vertraut sind, erstellen Sie ein Angebot und einen Plan, der den Event Hubs-Ressourcenanbieter umfasst. Erstellen Sie dann ein Abonnement für Ihre Benutzer, oder teilen Sie ihnen die Angebotsinformationen mit, damit sie ihr eigenes Abonnement erstellen können. Zum besseren Verständnis können Sie auch der Artikelreihe unter [Übersicht: Azure Stack Hub-Dienste, -Pläne, -Angebote und Abonnements](service-plan-offer-subscription-overview.md) folgen.

Wenn Sie auf Updates prüfen möchten, wechseln Sie zu [Aktualisieren von Event Hubs in Azure Stack Hub](resource-provider-apply-updates.md).

Wenn Sie den Ressourcenanbieter entfernen müssen, lesen Sie [Entfernen des Event Hubs-Ressourcenanbieters](event-hubs-rp-remove.md).

Weitere Informationen zur Benutzerumgebung finden Sie unter [Event Hubs in Azure Stack Hub – Übersicht](../user/event-hubs-overview.md) in den Benutzerdokumenten.