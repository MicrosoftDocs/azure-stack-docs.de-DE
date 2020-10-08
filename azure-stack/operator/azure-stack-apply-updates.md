---
title: Installieren von Azure Stack Hub-Updates
description: Erfahren Sie, wie Sie Azure Stack Hub-Updates installieren.
author: sethmanheim
ms.topic: how-to
ms.date: 07/22/2020
ms.author: sethm
ms.lastreviewed: 09/10/2019
ms.reviewer: sranthar
ms.openlocfilehash: d0222de6db809fcbb73d31ac1ef4298e18895643
ms.sourcegitcommit: 53b0dde60a6435936a5e0cb9e931245f262d637a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2020
ms.locfileid: "91106567"
---
# <a name="install-azure-stack-hub-updates"></a>Installieren von Azure Stack Hub-Updates

Sie können Updatepakete über das Blatt **Update** im Azure Stack Hub-Verwaltungsportal installieren. In diesem Artikel werden die Schritte zum Aktualisieren, Überwachen und Beheben von Problemen mit dem Updatevorgang beschrieben. Verwenden Sie das Blatt **Update**, um Updateinformationen anzuzeigen, Updates zu installieren, den Aktualisierungsfortschritt zu überwachen, den Aktualisierungsverlauf zu überprüfen und die aktuelle Version des Azure Stack Hub- und OEM-Pakets anzuzeigen.

Sie können Updates über das Administratorportal verwalten und im Dashboard den Abschnitt **Updates** verwenden, um Folgendes durchzuführen:

- Anzeigen wichtiger Informationen, z. B. die aktuelle Version
- Installieren von Updates und Überwachen des Status
- Überprüfen des Updateverlaufs der zuvor installierten Updates
- Anzeigen der aktuellen OEM-Paketversion der Cloud

## <a name="determine-the-current-version"></a>Bestimmen der aktuellen Version

Die aktuelle Version von Azure Stack Hub können Sie auf dem Blatt **Update** anzeigen. So öffnen Sie das Blatt

1. Öffnen Sie das Azure Stack Hub-Administratorportal.

2. Wählen Sie **Dashboard** aus. Auf dem Blatt **Updates** ist die aktuelle Version aufgeführt. In dieser Abbildung wird beispielsweise die Version 1.1903.0.35 angezeigt:

    ![Kachel „Updates“ auf dem Standarddashboard](./media/azure-stack-update-apply/image1.png)

## <a name="install-updates-and-monitor-progress"></a>Installieren von Updates und Überwachen des Status

> [!IMPORTANT]
> Vergewissern Sie sich vor dem Anwenden von Updates in Azure Stack Hub, dass Sie alle Schritte in der [Prüfliste für das Update](release-notes-checklist.md) ausgeführt und ein entsprechendes Wartungsfenster für den von Ihnen angesetzten Updatetyp geplant haben.

1. Öffnen Sie das Azure Stack Hub-Administratorportal.

2. Wählen Sie **Dashboard** aus. Wählen Sie **Update** aus.

3. Wählen Sie das verfügbare Update aus, das Sie installieren möchten. Wenn kein Update als **Verfügbar** markiert ist, [bereiten Sie das Updatepaket vor](azure-stack-update-prepare-package.md).

4. Wählen Sie **Jetzt aktualisieren** aus.

    ![Der Screenshot zeigt, wie Sie ein Update in Azure Stack Hub starten.](./media/azure-stack-update-apply/image2.png)

5. Sie können den allgemeinen Status anzeigen, während im Updateprozess die verschiedenen Subsysteme in Azure Stack Hub durchlaufen werden. Beispiele für Subsysteme sind physische Hosts, Service Fabric, virtuelle Computer der Infrastruktur und Dienste, die sowohl das Verwaltungs- als auch das Benutzerportal umfassen. Während des gesamten Updateprozesses meldet der Updateressourcenanbieter zusätzliche Details zu dem Update, z. B. die Anzahl der erfolgreich ausgeführten Schritte sowie die Anzahl der laufenden Schritte.

6. Wählen Sie auf dem Blatt mit den Details zur Updateausführung die Option **Download summary** (Zusammenfassung herunterladen) aus, um die vollständigen Protokolle herunterzuladen.

    Falls beim Überwachen des Updates ein Problem auftritt, können Sie den [privilegierten Endpunkt](./azure-stack-privileged-endpoint.md) nutzen, um den Status einer Azure Stack Hub-Updateausführung zu überwachen. Darüber hinaus können Sie den privilegierten Endpunkt verwenden, um einen nicht erfolgreichen Updatevorgang ab dem letzten erfolgreichen Schritt fortzusetzen, wenn das Azure Stack Hub-Portal nicht mehr verfügbar ist. Anweisungen hierzu finden Sie unter [Überwachen von Updates in Azure Stack Hub mithilfe von PowerShell](azure-stack-update-monitor.md).

    ![Details zur Azure Stack Hub-Updateausführung](./media/azure-stack-update-apply/image3.png)

7. Nach Abschluss des Vorgangs zeigt der Updateressourcenanbieter die Bestätigungsmeldung **Erfolgreich** an. Hiermit wird angegeben, dass der Updateprozess abgeschlossen ist und wie lange er gedauert hat. Sie können über den Filter Informationen zu allen verfügbaren oder installierten Updates anzeigen.

    ![azure-stack-update-apply](./media/azure-stack-update-apply/image4.png)

    Wenn beim Update Fehler auftreten, wird auf dem Blatt **Update** die Meldung **Eingreifen erforderlich** angezeigt. Verwenden Sie die Option **Vollständiges Protokoll herunterladen**, um den allgemeinen Status abzurufen und zu ermitteln, wo die Fehler aufgetreten sind. Die Azure Stack Hub-Protokollsammlung erleichtert die Diagnose und Problembehandlung.

## <a name="review-update-history"></a>Prüfen des Updateverlaufs

1. Öffnen Sie das Administratorportal.

2. Wählen Sie **Dashboard** aus. Wählen Sie **Update** aus.

3. Wählen Sie **Updateverlauf** aus.

    ![Azure Stack Hub-Updateverlauf](./media/azure-stack-update-apply/image7.png)

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über das Verwalten von Updates in Azure Stack Hub](./azure-stack-updates.md)  
- [Azure Stack Hub-Wartungsrichtlinie](./azure-stack-servicing-policy.md)  
