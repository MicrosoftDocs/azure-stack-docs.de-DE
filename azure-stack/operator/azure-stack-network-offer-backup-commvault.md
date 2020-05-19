---
title: Hinzufügen von Commvault zum Azure Stack Hub-Marketplace
description: Hier erfahren Sie, wie Sie dem Azure Stack Hub-Marketplace Commvault hinzufügen.
author: mattbriggs
ms.topic: article
ms.date: 04/20/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/28/2019
ms.openlocfilehash: 6f61e1d34cd8c8fcd22d4ebb87ece01376dbb5dd
ms.sourcegitcommit: ddcd083430ca905653d412dc2f7b813218d79509
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83375126"
---
# <a name="add-commvault-to-azure-stack-hub-marketplace"></a>Hinzufügen von Commvault zum Azure Stack Hub-Marketplace

In diesem Artikel wird beschrieben, wie Sie Commvault Live Sync als Angebot veröffentlichen, um einen virtuellen Computer für die Wiederherstellung zu aktualisieren, der sich in einer separaten Azure Stack Hub-Skalierungseinheit befindet. Sie können Commvault herunterladen und als Sicherungs- und Replikationslösung für Ihre Benutzer anbieten.

## <a name="notes-for-commvault"></a>Hinweise für Commvault

- Der Benutzer muss die Sicherungs- und Replikationssoftware auf einem virtuellen Computer unter seinem Azure Stack Hub-Quellabonnement installieren. Die Sicherungs- und Wiederherstellungsimages können außerhalb von Azure Stack unter Azure Site Recovery und Azure Backup gespeichert werden. Für beide Dienste muss zunächst ein Recovery Services-Tresor in Azure erstellt werden, bevor Sie die Softwareimages herunterladen, die in der Azure Stack Hub-Instanz installiert werden sollen. Die Softwareimages können aus [Azure Backup Server](https://go.microsoft.com/fwLink/?LinkId=626082&clcid=0x0409) und [Azure Site Recovery](https://aka.ms/unifiedinstaller_eus) heruntergeladen werden.  

- Möglicherweise benötigen Sie Lizenzen für Drittanbietersoftware (sofern ausgewählt).
- Ihre Benutzer benötigen unter Umständen Hilfe bei der Verbindung der Quelle und des Ziels über ein VPN-Gateway oder eine öffentliche IP-Adresse auf dem Sicherungs- und Replikationshost.
- Azure-Cloudzielabonnement oder Abonnement in einem Azure Stack Hub-Wiederherstellungsziel
- Zielressourcengruppe und Blob Storage-Konto in einem Azure Stack Hub-Wiederherstellungsziel
- Bei einigen Lösungen müssen Sie virtuelle Computer unter dem Zielabonnement erstellen, die das ganze Jahr über rund um die Uhr ausgeführt werden müssen, damit Änderungen vom Quellserver empfangen werden können. Unter [Sichern Ihres virtuellen Computers in Azure Stack Hub mit Commvault](../user/azure-stack-network-howto-backup-commvault.md) werden von Commvault Live Sync die virtuellen Zielcomputer für die Wiederherstellung bei der Erstkonfiguration erstellt und im Leerlauf beibehalten (nicht ausgeführt, nicht abrechenbar), bis Änderungen während eines Replikationszyklus angewendet werden müssen.

## <a name="get-commvault-for-your-marketplace"></a>Abrufen von Commvault für Ihren Marketplace

1. Öffnen Sie das Azure Stack Hub-Administratorportal.
2. Wählen Sie **Marketplace management** > **Add from Azure** (Marketplace-Verwaltung > Aus Azure hinzufügen) aus.

    ![Commvault für Azure Stack Hub](./media/azure-stack-network-offer-backup-commvault/get-commvault-for-marketplace.png)

3. Geben Sie `commvault` ein.
4. Wählen Sie **Commvault Trial** (Commvault-Testversion) aus. Wählen Sie anschließend **Herunterladen** aus.

## <a name="next-steps"></a>Nächste Schritte

- [Sichern Ihres virtuellen Computers in Azure Stack Hub mit Commvault](../user/azure-stack-network-howto-backup-commvault.md)
- [Übersicht über das Anbieten von Diensten in Azure Stack Hub](service-plan-offer-subscription-overview.md)
