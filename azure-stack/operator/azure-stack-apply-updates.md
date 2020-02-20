---
title: Installieren von Azure Stack Hub-Updates
description: Erfahren Sie, wie Sie Azure Stack Hub-Updates installieren.
author: ihenkel
ms.topic: article
ms.date: 1/22/2020
ms.author: inhenkel
ms.lastreviewed: 09/10/2019
ms.reviewer: ppacent
ms.openlocfilehash: bf65ebb1e0d9969062e63cb7e95920c89ece6437
ms.sourcegitcommit: b2173b4597057e67de1c9066d8ed550b9056a97b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/20/2020
ms.locfileid: "77492239"
---
# <a name="install-azure-stack-hub-updates"></a>Installieren von Azure Stack Hub-Updates

Sie können Updatepakete über das Blatt **Update** in Azure Stack Hub installieren. In diesem Artikel werden Sie durch die Schritte zum Aktualisieren, Überwachen und Beheben von Problemen mit dem Updatevorgang geführt. Verwenden Sie das Blatt „Update“, um Updateinformationen anzuzeigen, Updates zu installieren, den Aktualisierungsfortschritt zu überwachen, den Aktualisierungsverlauf zu überprüfen und die aktuelle Version des Azure Stack Hub- und OEM-Pakets anzuzeigen.

Sie können Updates über das Administratorportal verwalten und im Dashboard den Abschnitt **Updates** verwenden, um Folgendes durchzuführen:

- Anzeigen wichtiger Informationen, z. B. die aktuelle Version
- Installieren von Updates und Überwachen des Status
- Überprüfen des Updateverlaufs der zuvor installierten Updates
- Anzeigen der aktuellen OEM-Paketversion der Cloud

## <a name="determine-the-current-version"></a>Bestimmen der aktuellen Version

Die aktuelle Version von Azure Stack Hub können Sie auf dem Blatt **Update** anzeigen. So öffnen Sie das Blatt

1.  Öffnen Sie das Azure Stack Hub-Administratorportal.

2.  Wählen Sie **Dashboard** aus. Auf dem Blatt **Updates** ist die aktuelle Version aufgeführt.

    ![Kachel „Updates“ auf dem Standarddashboard](./media/azure-stack-update-apply/image1.png)

    In dieser Abbildung wird beispielsweise die Version 1.1903.0.35 angezeigt.

## <a name="install-updates-and-monitor-progress"></a>Installieren von Updates und Überwachen des Status

> [!Important]
> Vergewissern Sie sich vor dem Anwenden von Updates in Azure Stack Hub, dass Sie **alle** Schritte in der [Prüfliste für das Update](release-notes-checklist.md) ausgeführt und ein entsprechendes Wartungsfenster für den von Ihnen angesetzten Updatetyp geplant haben.

1. Öffnen Sie das Azure Stack Hub-Administratorportal.

2. Wählen Sie **Dashboard** aus. Wählen Sie **Update** aus.

3. Wählen Sie das verfügbare Update aus, das Sie installieren möchten. Wenn kein Update als **Verfügbar** markiert ist, müssen Sie das [Updatepaket vorbereiten](azure-stack-update-prepare-package.md).

4. Wählen Sie **Jetzt aktualisieren** aus.

    ![Details zur Azure Stack Hub-Updateausführung](./media/azure-stack-update-apply/image2.png)

5. Sie können den allgemeinen Status anzeigen, während im Updateprozess die verschiedenen Subsysteme in Azure Stack Hub durchlaufen werden. Beispiele für Subsysteme sind physische Hosts, Service Fabric, virtuelle Computer der Infrastruktur und Dienste, die sowohl das Verwaltungs- als auch das Benutzerportal umfassen. Während des gesamten Updateprozesses meldet der Updateressourcenanbieter zusätzliche Details zu dem Update, z. B. die Anzahl der erfolgreich ausgeführten Schritte sowie die Anzahl der laufenden Schritte.

6. Wählen Sie auf dem Blatt mit den Details zur Updateausführung die Option **Download summary** (Zusammenfassung herunterladen) aus, um die vollständigen Protokolle herunterzuladen.

    Falls beim Überwachen des Updates ein Problem auftritt, können Sie den [privilegierten Endpunkt](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint) nutzen, um den Status einer Azure Stack Hub-Updateausführung zu überwachen. Darüber hinaus können Sie den privilegierten Endpunkt verwenden, um einen nicht erfolgreichen Updatevorgang ab dem letzten erfolgreichen Schritt fortzusetzen, wenn das Azure Stack Hub-Portal nicht mehr verfügbar ist. Anweisungen hierzu finden Sie unter [Überwachen von Updates in Azure Stack Hub mithilfe von PowerShell](azure-stack-update-monitor.md).

    ![Details zur Azure Stack Hub-Updateausführung](./media/azure-stack-update-apply/image3.png)

7. Nach Abschluss des Vorgangs gibt der Updateressourcenanbieter die Bestätigungsmeldung **Erfolgreich** aus. Hiermit wird angegeben, dass der Updateprozess abgeschlossen ist und wie lange er gedauert hat. Sie können über den Filter Informationen zu allen verfügbaren oder installierten Updates anzeigen.

    ![azure-stack-update-apply](./media/azure-stack-update-apply/image4.png)

    Wenn beim Update Fehler auftreten, wird auf dem Blatt **Update** die Meldung **Eingreifen erforderlich** angezeigt. Verwenden Sie die Option **Vollständiges Protokoll herunterladen**, um den allgemeinen Status abzurufen und zu ermitteln, wo die Fehler aufgetreten sind. Die Azure Stack Hub-Protokollsammlung erleichtert die Diagnose und Problembehandlung.

## <a name="review-update-history"></a>Prüfen des Updateverlaufs

1. Öffnen Sie das Administratorportal.

2. Wählen Sie **Dashboard** aus. Wählen Sie **Update** aus.

3. Wählen Sie **Updateverlauf** aus.

    ![Azure Stack Hub-Updateverlauf](./media/azure-stack-update-apply/image7.png)

## <a name="next-steps"></a>Nächste Schritte

-   [Übersicht über das Verwalten von Updates in Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates)  
-   [Azure Stack Hub-Wartungsrichtlinie](https://docs.microsoft.com/azure-stack/operator/azure-stack-servicing-policy)  
