---
title: Hinzufügen von Commvault zum Azure Stack Hub-Marketplace
description: Erfahren Sie, wie Sie dem Azure Stack Hub-Marketplace Commvault hinzufügen.
author: mattbriggs
ms.topic: article
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/28/2019
ms.openlocfilehash: 8d2b9181c6db101113d1f60ef3ab5ab3a0929ed3
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "77698554"
---
# <a name="add-commvault-to-the-azure-stack-hub-marketplace"></a>Hinzufügen von Commvault zum Azure Stack Hub-Marketplace

In diesem Artikel wird beschrieben, wie Sie Commvault Live Sync als Angebot veröffentlichen, um einen virtuellen Computer für die Wiederherstellung zu aktualisieren, der sich in einer separaten Azure Stack Hub-Skalierungseinheit befindet. Sie können Commvault herunterladen und als Sicherungs- und Replikationslösung für Ihre Benutzer anbieten. 

## <a name="notes-for-commvault"></a>Hinweise für Commvault

- Der Benutzer muss die Sicherungs- und Replikationssoftware auf einem virtuellen Computer unter seinem Azure Stack Hub-Quellabonnement installieren. Die Sicherungs- und Wiederherstellungsimages können außerhalb von Azure Stack unter Azure Site Recovery und Azure Backup gespeichert werden. Für beide Dienste muss zunächst ein Recovery Services-Tresor in Azure erstellt werden, bevor Sie die Softwareimages, die in der Azure Stack Hub-Instanz installiert werden, von den folgenden Speicherorten herunterladen: [Azure Backup Server](https://go.microsoft.com/fwLink/?LinkId=626082&clcid=0x0409), [Azure Site Recovery](https://aka.ms/unifiedinstaller_eus).  
    
- Möglicherweise benötigen Sie Lizenzen für Drittanbietersoftware (sofern ausgewählt).
- Ihre Benutzer benötigen unter Umständen Hilfe bei der Verbindung der Quelle und des Ziels über ein Gateway für virtuelle Netzwerke (VPN) oder eine öffentliche IP-Adresse auf dem Sicherungs- und Replikationshost.
- Azure-Cloudzielabonnement oder Abonnement in einem Azure Stack Hub-Wiederherstellungsziel
- Zielressourcengruppe und Blob Storage-Konto in einem Azure Stack Hub-Wiederherstellungsziel
- Bei einigen Lösungen müssen Sie virtuelle Computer unter dem Zielabonnement erstellen, die das ganze Jahr über rund um die Uhr ausgeführt werden müssen, damit Änderungen vom Quellserver empfangen werden können. Unter [Sichern Ihres virtuellen Computers in Azure Stack Hub mit Commvault](../user/azure-stack-network-howto-backup-commvault.md) werden die virtuellen Zielcomputer für die Wiederherstellung in Commvault Live Sync bei der Erstkonfiguration erstellt und im Leerlauf beibehalten (nicht ausgeführt, nicht abrechenbar), bis Änderungen während eines Replikationszyklus angewandt werden müssen.


## <a name="get-commvault-for-your-marketplace"></a>Abrufen von Commvault für Ihren Marketplace

1. Öffnen Sie das Azure Stack Hub-Verwaltungsportal.
2. Wählen Sie **Marketplace management** > **Add from Azure** (Marketplace-Verwaltung > Aus Azure hinzufügen) aus.

    ![Commvault für Azure Stack Hub](./media/azure-stack-network-offer-backup-commvault/get-commvault-for-marketplace.png)

3. Geben Sie `commvault` ein.
4. Wählen Sie **Commvault Trial** (Commvault-Testversion) aus. Wählen Sie anschließend **Herunterladen** aus.


## <a name="next-steps"></a>Nächste Schritte

[Sichern Ihres virtuellen Computers in Azure Stack Hub mit Commvault](../user/azure-stack-network-howto-backup-commvault.md)

[Übersicht über das Anbieten von Diensten in Azure Stack Hub](service-plan-offer-subscription-overview.md)
