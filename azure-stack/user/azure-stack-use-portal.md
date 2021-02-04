---
title: Verwenden des Azure Stack Hub-Benutzerportals
description: Informationen zum Zugriff auf das Benutzerportal in Azure Stack Hub und zu dessen Verwendung.
author: mattbriggs
ms.topic: article
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: efemmano
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: 79757f734f76c620e8be4f47d73ab647a1accfee
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99247879"
---
# <a name="use-the-azure-stack-hub-user-portal"></a>Verwenden des Azure Stack Hub-Benutzerportals

Im Azure Stack Hub-Portal können Sie öffentliche Angebote abonnieren und die Dienste nutzen, die über diese Angebote bereitgestellt werden. Wenn Sie das globale Azure-Portal genutzt haben, sind Sie bereits mit der Funktionsweise der Website vertraut.

## <a name="access-the-portal"></a>Zugriff auf das Portal

Ihr Azure Stack Hub-Operator (ein Dienstanbieter oder ein Administrator in Ihrer Organisation) teilt Ihnen die richtige URL für den Zugriff auf das Portal mit.

- Bei einem integrierten System variiert die URL abhängig von der Region und dem externen Domänenamen des Betreibers und hat das Format https://portal.&lt;*Region*&gt;.&lt;*FQDN*&gt;.
- Wenn Sie das Azure Stack Development Kit (ASDK) verwenden, lautet die Portaladresse `https://portal.local.azurestack.external`.
- Die Standardzeitzone für alle Azure Stack Hub-Bereitstellungen ist auf „Koordinierte Weltzeit (UTC)“ festgelegt. Sie können bei der Installation von Azure Stack Hub eine Zeitzone auswählen, diese wird während der Installation jedoch automatisch auf die Standardeinstellung „UTC“ zurückgesetzt.

## <a name="customize-the-dashboard"></a>Anpassen des Dashboards

Das Dashboard enthält eine Reihe von Standardkacheln. Wählen Sie **Dashboard bearbeiten** aus, um das Standarddashboard anzupassen, oder wählen Sie **Neues Dashboard** aus, um ein benutzerdefiniertes Dashboard zu erstellen. Sie können ein Dashboard einfach durch Hinzufügen oder Entfernen von Kacheln anpassen. Wählen Sie beispielsweise zum Hinzufügen einer Kachel „Compute“ die Option **+Ressource erstellen** aus. Klicken Sie mit der rechten Maustaste auf **Compute**, und wählen Sie dann **An Dashboard anheften** aus.

![Bildschirmaufnahme des Azure Stack Hub-Benutzerportals](media/azure-stack-use-portal/userportal.png)

So stellen Sie die ursprünglichen Dashboardeinstellungen wieder her:
1.  Wählen Sie **Dashboard bearbeiten** aus. 
2.  Klicken Sie mit der rechten Maustaste, und wählen Sie **Standardzustand wiederherstellen** aus.

## <a name="create-subscription-and-browse-available-resources"></a>Erstellen von Abonnements und Durchsuchen verfügbarer Ressourcen

Wenn Sie noch kein Abonnement haben, müssen Sie ein Angebot abonnieren. Danach können Sie die verfügbaren Ressourcen durchsuchen. Führen Sie zum Durchsuchen und Erstellen von Ressourcen einen der folgenden Schritte aus:

- Wählen Sie auf dem Dashboard die Kachel **Marketplace** aus.
- Wählen Sie auf der Kachel **Alle Ressourcen** die Option **Ressourcen erstellen** aus.
- Wählen Sie im linken Navigationsbereich die Option **+Ressource erstellen** aus.

## <a name="learn-how-to-use-available-services"></a>Erlernen der Verwendung verfügbarer Dienste

Wenn Sie Anleitungen zur Verwendung verfügbarer Dienste wünschen, stehen Ihnen möglicherweise unterschiedliche Optionen zur Verfügung.

- Ihre Organisation oder Ihr Dienstanbieter stellt möglicherweise eine eigene Dokumentation bereit. Dies ist in der Regel der Fall, wenn angepasste Dienste oder Apps angeboten werden.
- Für Drittanbieter-Apps sind eigene Dokumentationen erforderlich.
- Für Azure-konsistente Dienste sollten Sie zuerst die Azure Stack Hub-Dokumentation lesen. Um auf die Azure Stack Hub-Benutzerdokumentation zuzugreifen, wählen Sie das Symbol „Hilfe“ ( **?** ) und dann **Hilfe und Support** aus.

    ![Option „Hilfe und Support“ in der Benutzeroberfläche](media/azure-stack-use-portal/HelpAndSupport.png)

    Insbesondere sollten Sie zum Einstieg die folgenden Artikel lesen:

    - [Wichtige Aspekte: Verwenden von Diensten oder Erstellen von Apps für Azure Stack Hub](azure-stack-considerations.md).
    - In der Dokumentation befindet sich im Abschnitt **Verwenden der Dienste** für jeden Dienst ein Artikel „Überlegungen“. Auf der Seite mit den Überlegungen werden die Unterschiede zwischen dem Angebot des Diensts in Azure und dem Angebot des gleichen Diensts in Azure Stack Hub beschrieben. Ein Beispiel hierfür ist [Considerations for Virtual Machines in Azure Stack](azure-stack-vm-considerations.md) (Überlegungen zu VMs in Azure Stack). Der Abschnitt **Verwenden der Dienste** enthält möglicherweise andere, für Azure Stack Hub individuelle Informationen.

      Sie können die Azure-Dokumentation als allgemeine Referenz für einen Dienst verwenden, aber Sie müssen sich dieser Unterschiede bewusst sein. Beachten Sie, dass die Dokumentationslinks auf der Kachel **Schnellstarttutorials** auf Azure-Dokumentation verweisen.

## <a name="get-support"></a>Support

Wenn Sie Unterstützung benötigen, wenden Sie sich an Ihr Unternehmen oder Ihren Dienstanbieter.

Bei Verwendung des Azure Stack Development Kit (ASDK) können Sie nur das [Azure Stack Hub-Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) als Support nutzen.

## <a name="next-steps"></a>Nächste Schritte

[Wichtige Aspekte: Verwenden von Diensten oder Erstellen von Apps für Azure Stack Hub](azure-stack-considerations.md)
