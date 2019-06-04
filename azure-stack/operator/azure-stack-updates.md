---
title: Übersicht zum Verwalten von Updates in Azure Stack | Microsoft-Dokumentation
description: Informationen zur Updateverwaltung für integrierte Azure Stack-Systeme.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: mabrigg
ms.lastreviewed: 04/04/2019
ms.reviewer: justini
ms.openlocfilehash: 64ccb5ec0b24202aa847d5114ba35ae20af44cc8
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66268027"
---
# <a name="manage-updates-in-azure-stack-overview"></a>Übersicht zum Verwalten von Updates in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

Microsoft-Updatepakete für integrierte Azure Stack-Systeme werden in der Regel jeden Monat herausgegeben. Informieren Sie sich bei Ihrem Originalgerätehersteller (Original Equipment Manufacturer, OEM) nach dem jeweiligen Benachrichtigungsprozess, um sicherzustellen, dass die Updatebenachrichtigungen Ihre Organisation erreichen. Sie können auch in dieser Dokumentationsbibliothek unter **Übersicht** > **Anmerkungen zu dieser Version** überprüfen, ob Informationen zu Releases vorliegen, die aktiv unterstützt werden. 

Jedes Release von Microsoft-Softwareupdates ist in einem einzelnen Updatepaket gebündelt. Als Azure Stack-Betreiber können Sie über das Azure Stack-Verwaltungsportal Updatepakete importieren und installieren und ihren Installationsstatus überwachen.

Ihr OEM-Anbieter veröffentlicht auch Updates, z. B. Treiber- und Firmwareupdates. Diese Updates werden zwar als separate Pakete vom Anbieter bereitgestellt, aber einige davon werden auf die gleiche Weise wie Updatepakete von Microsoft importiert, installiert und verwaltet.

Um den Support für Ihr System aufrechtzuerhalten, müssen Sie sicherstellen, dass Azure Stack stets bis zu einer bestimmten Version aktualisiert ist. Machen Sie sich unbedingt mit der [Azure Stack servicing policy](azure-stack-servicing-policy.md) (Azure Stack-Wartungsrichtlinie) vertraut.

> [!NOTE]
> Sie können Azure Stack-Updatepakete nicht auf Azure Stack Development Kit anwenden. Die Updatepakete sind für integrierte Systeme vorgesehen. Weitere Informationen finden Sie unter [Erneutes Bereitstellen des ASDK](/azure-stack/asdk).

## <a name="the-update-resource-provider"></a>Der Updateressourcenanbieter

Azure Stack umfasst einen Updateressourcenanbieter, der die Anwendung von Microsoft-Softwareupdates verarbeitet. Dieser Anbieter überprüft, ob Updates auf alle physischen Hosts, Service Fabric-Anwendungen und -Laufzeiten sowie alle virtuellen Computer der Infrastruktur und die ihnen zugeordneten Dienste angewandt werden.

Bei der Installation der Updates können Sie den allgemeinen Status anzeigen, während der Updateprozess die verschiedenen Subsysteme in Azure Stack (z.B. physische Hosts und virtuelle Computer der Infrastruktur) erfasst.

## <a name="plan-for-updates"></a>Planen für Updates

Es wird dringend empfohlen, dass Sie die Benutzer über alle Wartungsvorgänge unterrichten und normale Wartungsfenster möglichst außerhalb der Geschäftszeiten planen. Wartungsvorgänge können sowohl Mandantenworkloads als auch Portalvorgänge beeinträchtigen.

Bei der Planung für Ihr Wartungsfenster ist es wichtig, den spezifischen Typ des von Microsoft herausgegebenen Updatepakets zu prüfen. Hierauf wird auch im entsprechenden Versionshinweis hingewiesen. Neben einem gelegentlichen Hotfix verfügt jedes Updatepaket über einen entsprechenden Typ (**Vollständig** oder **Express**). Mit vollständigen Updatepaketen werden die Betriebssysteme des physischen Hosts in der Skalierungseinheit aktualisiert, und es ist ein längeres Wartungsfenster erforderlich. Express-Updatepakete sind dagegen bereichsbezogen und führen keine Aktualisierung der zugrunde liegenden Betriebssysteme des physischen Hosts durch.

- Bevor Sie mit der Installation dieses Updates beginnen, führen Sie [Test-AzureStack](azure-stack-diagnostic-test.md) mit den folgenden Parametern aus, um den Status von Azure Stack zu überprüfen und alle gefundenen operativen Probleme (einschließlich aller Warnungen und Fehler) zu beheben. Überprüfen Sie auch aktive Warnungen, und lösen Sie solche auf, die eine Aktion erfordern.  

  ```powershell
  Test-AzureStack -Group UpdateReadiness
  ``` 

## <a name="using-the-update-tile-to-manage-updates"></a>Verwenden der Update-Kachel zum Verwalten von Updates

Sie können Updates über das Administratorportal verwalten. Als Azure Stack-Operator können Sie zu folgenden Zwecken die Update-Kachel im Dashboard verwenden:

- Anzeigen wichtiger Informationen wie z. B. der aktuellen Version
- Installieren von Updates und Überwachen des Status
- Überprüfen des Updateverlaufs der zuvor installierten Updates
- Anzeigen der aktuellen OEM-Paketversion der Cloud
 
## <a name="determine-the-current-version"></a>Bestimmen der aktuellen Version

Die aktuelle Version von Azure Stack können Sie auf der Kachel „Update“ anzeigen. So öffnen Sie Kachel

1. Öffnen Sie das Azure Stack-Verwaltungsportal.
2. Wählen Sie **Dashboard** aus. Auf der Kachel **Update** ist die aktuelle Version aufgeführt. 

    ![Kachel „Updates“ auf dem Standarddashboard](./media/azure-stack-updates/image1.png)

    Im Screenshot wird beispielsweise die Version 1.1903.0.35 angezeigt.

## <a name="install-updates-and-monitor-progress"></a>Installieren von Updates und Überwachen des Status


1. Öffnen Sie das Azure Stack-Verwaltungsportal.
2. Wählen Sie **Dashboard** aus. Wählen Sie die Kachel „Update“ aus.
3. Wählen Sie **Jetzt aktualisieren** aus.

    ![Details zur Azure Stack-Updateausführung](media/azure-stack-updates/azure-stack-update-button.png)

4.  Sie können den allgemeinen Status anzeigen, während im Updateprozess die verschiedenen Subsysteme in Azure Stack durchlaufen werden. Beispiele für Subsysteme sind physische Hosts, Service Fabric, virtuelle Computer der Infrastruktur und Dienste, die das Administrator- sowie das Benutzerportal umfassen. Während des gesamten Updateprozesses meldet der Updateressourcenanbieter zusätzliche Details zu dem Update, z. B. die Anzahl der erfolgreich ausgeführten Schritte sowie die Anzahl der laufenden Schritte.

5. Wählen Sie auf dem Blatt mit den Details zur Updateausführung die Option **Vollständiges Protokoll herunterladen** aus, um die vollständigen Protokolle herunterzuladen.

    ![Details zur Azure Stack-Updateausführung](media/azure-stack-updates/update-run-details.png)

6. Nach Abschluss des Vorgangs gibt der Updateressourcenanbieter die Bestätigungsmeldung **Erfolgreich** aus. Damit wissen Sie, dass der Updateprozess abgeschlossen ist und wie lange er gedauert hat. Dort können Sie über den Filter Informationen zu allen verfügbaren oder installierten Updates anzeigen.

    ![Details zur Azure Stack-Updateausführung – Erfolgreich](media/azure-stack-updates/update-success.png)

   Wenn beim Update Fehler auftreten, wird auf der Kachel „Update“ die Meldung **Eingreifen erforderlich** angezeigt. Verwenden Sie die Option **Vollständiges Protokoll herunterladen**, um den allgemeinen Status abzurufen und zu ermitteln, wo die Fehler möglicherweise aufgetreten sind. Die Azure Stack-Protokollsammlung erleichtert die Diagnose und Problembehandlung.

## <a name="next-steps"></a>Nächste Schritte

- [Azure Stack-Wartungsrichtlinie](azure-stack-servicing-policy.md) 
- [Region management in Azure Stack](azure-stack-region-management.md) (Regionsverwaltung in Azure Stack)
