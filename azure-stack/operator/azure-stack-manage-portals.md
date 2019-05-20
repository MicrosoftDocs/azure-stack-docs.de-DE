---
title: Verwenden des Verwaltungsportals in Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie als Azure Stack-Bediener das Verwaltungsportal verwenden.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/25/2019
ms.author: mabrigg
ms.reviewer: efemmano
ms.lastreviewed: 02/25/2019
ms.openlocfilehash: 71932642d8c44d2043fe185eab3ebda25be7bb3f
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618614"
---
# <a name="quickstart-use-the-azure-stack-administration-portal"></a>Schnellstart: Verwenden des Azure Stack-Verwaltungsportals

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

In Azure Stack stehen zwei Portale zur Verfügung: das Verwaltungs- bzw. Administratorportal und das Benutzerportal (manchmal auch als *Mandantenportal* bezeichnet). Als Azure Stack-Bediener können Sie das Verwaltungsportal für tägliche Verwaltungsaufgaben und Vorgänge in Azure Stack verwenden.

## <a name="access-the-administrator-portal"></a>Zugreifen auf das Administratorportal

Rufen Sie zum Zugreifen auf das Administratorportal die Portal-URL auf, und melden Sie sich mit den Anmeldeinformationen eines Azure Stack-Betreibers an. Bei einem integrierten System variiert die Portal-URL basierend auf dem Regionsnamen und dem externen vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) der Azure Stack-Bereitstellung. Für Bereitstellungen des Azure Stack Development Kit (ASDK) ist die URL für das Verwaltungsportal immer gleich. 

| Environment | URL des Administratorportals |   
| -- | -- | 
| ASDK| https://adminportal.local.azurestack.external  |
| Integrierte Systeme | https://adminportal.&lt;*Region*&gt;.&lt;*FQDN*&gt; | 
| | |

> [!TIP]
> Bei einer ASDK-Umgebung müssen Sie zunächst sicherstellen, dass Sie über die Remotedesktopverbindung oder ein virtuelles privates Netzwerk (VPN) eine [Verbindung mit dem Development Kit-Host herstellen](../asdk/asdk-connect.md) können.

 ![Verwaltungsportal](media/azure-stack-manage-portals/admin-portal.png)

Die Standardzeitzone für alle Azure Stack-Bereitstellungen ist auf „Koordinierte Weltzeit (UTC)“ festgelegt. 

Im Administratorportal können Sie beispielsweise folgende Aufgaben ausführen:

* [Registrieren von Azure Stack in Azure](azure-stack-registration.md)
* [Auffüllen des Marketplace](azure-stack-download-azure-marketplace-item.md)
* [Erstellen von Plänen, Angeboten und Abonnements für Benutzer](azure-stack-plan-offer-quota-overview.md)
* [Überwachung von Zustand und Warnungen](azure-stack-monitor-health.md)
* [Verwalten von Azure Stack-Updates](azure-stack-updates.md)

Auf der Kachel **Schnellstart-Lernprogramme** stehen Links zur Onlinedokumentation für die häufigsten Aufgaben zur Verfügung.

Obwohl ein Bediener im Verwaltungsportal Ressourcen wie VMs, virtuelle Netzwerke und Speicherkonten erstellen kann, sollten Sie sich zum Erstellen und Testen von Ressourcen [beim Benutzerportal anmelden](../user/azure-stack-use-portal.md).

>[!NOTE]
>Über den Link **Virtuellen Computer erstellen** auf der Kachel des Schnellstarttutorials können Sie eine VM im Verwaltungsportal erstellen, diese Option dient jedoch nur zum Überprüfen der erfolgreichen Bereitstellung von Azure Stack.

## <a name="understand-subscription-behavior"></a>Grundlegendes zum Abonnementverhalten

Im Verwaltungsportal werden standardmäßig drei Abonnements erstellt: für den Verbrauch, den Standardanbieter und die Messung. Als Bediener verwenden Sie in erster Linie das *Standardabonnement des Anbieters*. Es ist nicht möglich, weitere Abonnements hinzuzufügen und im Verwaltungsportal zu verwenden. 

Andere Abonnements werden von Benutzern im Benutzerportal auf Grundlage der Pläne und Angebote erstellt, die Sie für diese erstellen. Über das Benutzerportal kann jedoch nicht auf die administrativen oder operativen Funktionen des Verwaltungsportals zugegriffen werden.

Die Verwaltungs- und Benutzerportale basieren auf separaten Instanzen von Azure Resource Manager. Aufgrund dieser Trennung von Azure Resource Manager sind Abonnements nicht portalübergreifend. Wenn Sie sich beispielsweise als Azure Stack-Bediener beim Benutzerportal anmelden, können Sie nicht auf das *Standardabonnement des Anbieters* zugreifen. Obwohl Sie keinen Zugriff auf administrative Funktionen haben, können Sie jedoch auf der Grundlage verfügbarer öffentlicher Angebote Abonnements für sich selbst erstellen. Solange Sie beim Benutzerportal angemeldet sind, gelten Sie als Mandantenbenutzer.

  >[!NOTE]
  >In einer ASDK-Umgebung gilt Folgendes: Wenn ein Benutzer dem gleichen Mandantenverzeichnis angehört wie der Azure Stack-Bediener, kann er sich beim Verwaltungsportal anmelden. Er kann jedoch nicht auf die Verwaltungsfunktionen zugreifen oder Abonnements hinzufügen, um auf Angebote zuzugreifen, die im Benutzerportal nicht für ihn verfügbar sind.

## <a name="administration-portal-tips"></a>Tipps zur Verwendung des Verwaltungsportals

### <a name="customize-the-dashboard"></a>Anpassen des Dashboards

Das Dashboard enthält eine Reihe von Standardkacheln. Wählen Sie **Dashboard bearbeiten** aus, um das Standarddashboard anzupassen, oder wählen Sie **Neues Dashboard** aus, um ein benutzerdefiniertes Dashboard hinzuzufügen. Sie können einem Dashboard einfach Kacheln hinzufügen. Wählen Sie beispielsweise **+Ressource erstellen** aus, klicken Sie mit der rechten Maustaste auf **Angebote und Pläne**, und wählen Sie dann **An Dashboard anheften** aus.

Es kann sein, dass im Portal manchmal ein leeres Dashboard angezeigt wird. Klicken Sie zum Wiederherstellen des Dashboards auf **Dashboard bearbeiten**, klicken Sie dann mit der rechten Maustaste, und wählen Sie **Auf Standardzustand zurücksetzen** aus.

### <a name="quick-access-to-online-documentation"></a>Schneller Zugriff auf Onlinedokumentation

Klicken Sie oben rechts im Administratorportal auf das Symbol „Hilfe und Support“ (Fragezeichen), um auf die Dokumentation für Azure Stack-Bediener zuzugreifen. Bewegen Sie den Cursor über das Symbol, und wählen Sie dann **Hilfe und Support** aus.

### <a name="quick-access-to-help-and-support"></a>Schneller Zugriff auf Hilfe und Support

Wenn Sie oben rechts im Administratorportal auf das Symbol „Hilfe und Support“ (Fragezeichen) klicken und dann **Neue Supportanfrage** auswählen, geschieht Folgendes:

- Bei Verwendung eines integrierten Systems wird mit dieser Aktion eine Website geöffnet, auf der Sie direkt ein Supportticket beim Microsoft-Kundensupport (Microsoft Customer Support Services, CSS) öffnen können. Informationen dazu, wann Sie den Microsoft-Support und wann den Support des OEM-Hardwareanbieters kontaktieren sollten, finden Sie unter [Supportquellen](azure-stack-manage-basics.md#where-to-get-support).
- Bei Verwendung des ASDK wird mit dieser Aktion direkt die [Website mit den Azure Stack-Foren](https://social.msdn.microsoft.com/Forums/home?forum=AzureStack) geöffnet. Diese Foren werden regelmäßig überprüft. Da das ASDK eine Evaluierungsumgebung ist, wird über Microsoft CSS kein offizieller Support angeboten.

### <a name="quick-access-to-the-azure-roadmap"></a>Schneller Zugriff auf die Azure-Roadmap

Wenn Sie oben rechts im Verwaltungsportal **Hilfe und Support** (Fragezeichen) und dann **Azure-Roadmap** auswählen, wird eine neue Browserregisterkarte geöffnet, über die Sie zur Azure-Roadmap gelangen. Durch Eingabe von **Azure Stack** in das Suchfeld **Produkte** werden alle Updates von Azure Stack-Roadmap angezeigt.

## <a name="next-steps"></a>Nächste Schritte

[Registrieren Sie sich für Azure Stack in Azure](azure-stack-registration.md), und füllen Sie den [Azure Stack-Marketplace](azure-stack-marketplace.md) mit Elementen auf, die Sie für Ihre Benutzer anbieten möchten. 
