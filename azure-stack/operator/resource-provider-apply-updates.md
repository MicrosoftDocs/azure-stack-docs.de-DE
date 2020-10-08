---
title: Anwenden von Updates auf einen Azure Stack Hub-Ressourcenanbieter
description: Erfahren Sie, wie Sie ein Dienstupdate auf einen Ressourcenanbieterin Azure Stack Hub anwenden.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/18/2019
ms.reviewer: jfggdl
ms.lastreviewed: 11/18/2019
ms.openlocfilehash: ff1a6258e682d3230ea7968a97b9e392991ca72b
ms.sourcegitcommit: 53b0dde60a6435936a5e0cb9e931245f262d637a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2020
ms.locfileid: "91107124"
---
# <a name="how-to-update-an-azure-stack-hub-resource-provider"></a>Aktualisieren eines Azure Stack Hub-Ressourcenanbieters

> [!IMPORTANT]
> Überprüfen Sie die neuesten Versionshinweise des Ressourcenanbieters auf Informationen zu neuen Funktionen, Fehlerbehebungen und bekannten Problemen, die sich auf die Bereitstellung auswirken können, bevor Sie fortfahren. Die Versionshinweise können auch die minimale Azure Stack Hub-Version angeben, die für den Ressourcenanbieter erforderlich ist.

Ressourcenanbieter, die aus Marketplace installiert wurden, erfordern regelmäßige Wartung. Die Wartung erfolgt durch Anwenden von Dienstupdates, die von Microsoft turnusmäßig bereitgestellt werden. Updates können sowohl neue Features als auch Hotfixes enthalten.  

## <a name="check-for-updates"></a>Suchen nach Updates

Ressourcenanbieter werden mit dem Updatefeature aktualisiert, das auch zum Anwenden von Azure Stack Hub-Updates verwendet wird.

1. Melden Sie sich beim Azure Stack Hub-Administratorportal an.
2. Wählen Sie auf der linken Seite den Link **Alle Dienste** aus, und wählen Sie dann im Abschnitt **Administration** die Option **Updates** aus.
   ![Seite „Alle Dienste“](media/resource-provider-apply-updates/1-all-services.png)

3. Auf der Seite **Updates** finden Sie Updates für die Ressourcenanbieter im Abschnitt **Ressourcenanbieter**.

   [![Der Screenshot zeigt den Abschnitt „Ressourcenanbieter“.](media/resource-provider-apply-updates/3-update-available.png)](media/resource-provider-apply-updates/3-update-available.png#lightbox)

## <a name="apply-an-update"></a>Anwenden eines Updates

Wenn für einen bestimmten Ressourcenanbieter ein Update verfügbar ist:

1. Wählen Sie die Zeile des Ressourcenanbieters aus, den Sie aktualisieren möchten. Daraufhin wird der **Download**-Link oben auf der Seite aktiviert.
   [![Seite „Update verfügbar“](media/resource-provider-apply-updates/4-download.png)](media/resource-provider-apply-updates/3-update-available.png#lightbox)

2. Klicken Sie auf den Link **Download**, um das Installationspaket des Ressourcenanbieters herunterzuladen. Der Eintrag in der **Status**-Spalte für den Ressourcenanbieter ändert sich von „Verfügbar“ in "Download läuft“.
3. Sobald sich der **Status** in „Bereit zur Installation“ geändert hat, ist der Download abgeschlossen. Daraufhin wird auch der **Jetzt installieren**-Link oben auf der Seite aktiviert.
4. Wählen Sie den Link **Jetzt installieren** aus, und Sie gelangen zur **Installieren**-Seite für den Ressourcenanbieter. 
5. Wählen Sie die Schaltfläche **Installieren** aus, um mit der Installation zu beginnen.
6. In der oberen rechten Ecke wird eine „Installation wird ausgeführt“-Benachrichtigung angezeigt, und es erfolgt eine Rückkehr zur Seite **Updates**. Die Ressourcenanbieterzeile **Status** wird in „Wird installiert“ geändert.
7. Wenn die Installation abgeschlossen ist, wird in einer weiteren Benachrichtigung Erfolg oder Fehler mitgeteilt. Bei einer erfolgreichen Installation wird auch die **Version** auf der Seite **Marketplace-Verwaltung - Ressourcenanbieter** aktualisiert.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über das [Feature für Updates des Administratordashboards](azure-stack-apply-updates.md).
