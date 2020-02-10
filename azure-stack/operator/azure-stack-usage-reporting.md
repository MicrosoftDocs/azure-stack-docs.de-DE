---
title: Melden von Azure Stack Hub-Nutzungsdaten an Azure
titleSuffix: Azure Stack Hub
description: Hier erfahren Sie, wie Sie Azure Stack Hub-Nutzungsdaten an Azure melden.
services: azure-stack
documentationcenter: ''
author: sethmanheim
ms.topic: article
ms.date: 01/27/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: ed0bcd873d6bd33017172eb3accad227e2073668
ms.sourcegitcommit: 5f53810d3c5917a3a7b816bffd1729a1c6b16d7f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2020
ms.locfileid: "76972646"
---
# <a name="report-azure-stack-hub-usage-data-to-azure"></a>Melden von Azure Stack Hub-Nutzungsdaten an Azure

Nutzungsdaten, auch Verbrauchsdaten genannt, stellen die Menge der verwendeten Ressourcen dar.

Azure Stack Hub-Systeme mit mehreren Knoten, die das nutzungsbasierte Abrechnungsmodell verwenden, müssen die Nutzungsdaten zu Abrechnungszwecken an Azure melden. Azure Stack Hub-Operatoren müssen ihre Azure Stack Hub-Instanz so konfigurieren, dass diese Nutzungsdaten an Azure meldet.

> [!IMPORTANT]
> Alle Workloads [müssen in Mandantenabonnements bereitgestellt werden](#are-users-charged-for-the-infrastructure-vms), um die Lizenzbestimmungen von Azure Stack Hub einzuhalten.

Die Berichterstellung für Nutzungsdaten ist für diejenigen Benutzer der Azure Stack Hub-Systeme mit mehreren Knoten erforderlich, die im Rahmen des nutzungsbasierten Modells lizenziert sind. Sie ist optional für Kunden, die im Rahmen des Kapazitätsmodells lizenziert sind (siehe Seite [Azure Stack erwerben](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)). Für Azure Stack Development Kit-Benutzer (ASDK) können Azure Stack Hub-Operatoren Nutzungsdaten melden und das Feature testen. Allerdings werden Benutzern keinerlei Kosten berechnet, die für die Nutzung anfallen.

![Abrechnungsflow für Nutzungsdaten in Azure Stack Hub](media/azure-stack-usage-reporting/billing-flow.png)

Nutzungsdaten werden von Azure Stack Hub über die Azure-Bridge an Azure gesendet. In Azure verarbeitet das Commerce-System die Nutzungsdaten und erstellt die Rechnung. Nachdem die Rechnung erstellt wurde, kann der Besitzer des Azure-Abonnements diese im [Azure-Kontocenter](https://account.windowsazure.com/subscriptions) anzeigen und von dort herunterladen. Informationen zur Lizenzierung von Azure Stack Hub finden Sie im Dokument [Azure Stack Hub packaging and pricing](https://go.microsoft.com/fwlink/?LinkId=842847) (Paketerstellung und Preise von Azure Stack Hub).

## <a name="set-up-usage-data-reporting"></a>Einrichten der Berichterstellung zu Nutzungsdaten

Um die Berichterstellung für Nutzungsdaten einzurichten, müssen Sie [Ihre Azure Stack Hub-Instanz bei Azure registrieren](azure-stack-registration.md). Im Rahmen des Registrierungsprozesses wird die Komponente Azure-Bridge von Azure Stack Hub konfiguriert. Azure-Bridge verbindet Azure Stack Hub mit Azure. Die folgenden Nutzungsdaten werden von Azure Stack Hub an Azure gesendet:

- **ID der Verbrauchseinheit**: Eine eindeutige ID für die verwendete Ressource.
- **Menge**: Menge der Ressourcennutzung.
- **Speicherort**: Der Speicherort, an dem die aktuelle Azure Stack Hub-Ressource bereitgestellt ist.
- **Ressourcen-URI**: Ein vollqualifizierter URI der Ressource, deren Nutzung gemeldet wird.
- **Abonnement-ID**: Die Abonnement-ID des Azure Stack Hub-Benutzers. Dies ist das lokale Abonnement (Azure Stack Hub).
- **Zeit**: Die Start- und Endzeit der Nutzungsdaten. Es besteht eine Verzögerung zwischen dem Zeitpunkt, zu dem die Ressourcen in Azure Stack Hub genutzt werden, und dem Zeitpunkt, zu dem die Nutzungsdaten an das Commercesystem gemeldet werden. Azure Stack Hub aggregiert Nutzungsdaten für jeweils 24 Stunden, und das Melden der Nutzungsdaten an die Commercepipeline in Azure dauert zusätzlich einige Stunden. Daher kann es vorkommen, dass eine Nutzung kurz vor Mitternacht in Azure erst am nächsten Tag angezeigt wird.

## <a name="generate-usage-data-reporting"></a>Generieren der Berichterstellung zu Nutzungsdaten

- Um die Berichterstellung für Nutzungsdaten zu testen, erstellen Sie einige Ressourcen in Azure Stack Hub. Beispielsweise können Sie ein [Speicherkonto](azure-stack-provision-storage-account.md), eine [Windows Server-VM](../user/azure-stack-create-vm-template.md) und eine Linux-VM mit Basic- und Standard-SKUs erstellen, um zu veranschaulichen, wie die Kernnutzung gemeldet wird. Die Nutzungsdaten für verschiedene Ressourcentypen werden unter verschiedenen Verbrauchseinheiten gemeldet.

- Führen Sie Ihre Ressourcen einige Stunden lang aus. Nutzungsinformationen werden etwa einmal pro Stunde erfasst. Nachdem die Informationen erfasst wurden, werden diese Daten an Azure übertragen und im Commerce-System von Azure verarbeitet. Dieser Prozess kann einige Stunden dauern.

## <a name="view-usage---csp-subscriptions"></a>Anzeigen der Nutzung – CSP-Abonnements

Wenn Sie Ihre Azure Stack Hub-Instanz mit einem CSP-Abonnement registriert haben, können Sie Ihre Nutzung und Gebühren auf die gleiche Weise wie den Azure-Verbrauch anzeigen. Die Nutzung von Azure Stack Hub wird in Ihrer Rechnung und in der Abstimmungsdatei aufgeführt, die über [Partner Center](https://partnercenter.microsoft.com/partner/home) erhältlich ist. Die Abstimmungsdatei wird monatlich aktualisiert. Wenn Sie auf die aktuellen Informationen zur Nutzung von Azure Stack Hub zugreifen müssen, können Sie die Partner Center-APIs verwenden.

![Anzeigen der Abrechnungs- und Nutzungsdaten von Azure Stack Hub in Microsoft Partner Center](media/azure-stack-usage-reporting/partner-center.png)

## <a name="view-usage---enterprise-agreement-subscriptions"></a>Anzeigen der Nutzung – Enterprise Agreement-Abonnements

Wenn Sie Ihre Azure Stack Hub-Instanz mit einem Enterprise Agreement-Abonnement registriert haben, können Sie Ihre Nutzung und Gebühren im [EA-Portal](https://ea.azure.com/) anzeigen. Neben den Azure-Nutzungsdaten werden in diesem Portal im Abschnitt zu den Berichten unter den erweiterten Downloads die Azure Stack Hub-Nutzungsdaten aufgeführt.

## <a name="view-usage---other-subscriptions"></a>Anzeigen der Nutzung – Andere Abonnements

Wenn Sie Ihre Azure Stack Hub-Instanz mit einem anderen Abonnementtyp (etwa einem Abonnement mit nutzungsbasierter Bezahlung) registriert haben, können Sie die Nutzung und die Gebühren im Azure-Kontocenter anzeigen. Melden Sie sich als Azure-Kontoadministrator beim [Azure-Kontocenter](https://account.windowsazure.com/subscriptions) an, und wählen Sie das Azure-Abonnement aus, das Sie zum Registrieren von Azure Stack Hub verwendet haben. Sie können die Azure Stack Hub-Nutzungsdaten und den in Rechnung gestellten Betrag für jede verwendete Ressource anzeigen, wie in der folgenden Abbildung zu sehen:

![Anzeigen des Abrechnungs- und Nutzungsflows im Azure-Kontocenter](media/azure-stack-usage-reporting/pricing-details.png)

Beim ASDK fallen für Azure Stack Hub-Ressourcen keine Gebühren an, daher wird der Preis mit 0,00 USD angezeigt.

## <a name="which-azure-stack-hub-deployments-are-charged"></a>Welche Azure Stack Hub-Bereitstellungen werden in Rechnung gestellt?

Die Ressourcennutzung ist für das Azure Stack Development Kit kostenlos. Bei Azure Stack Hub-Systemen mit mehreren Knoten werden Workload-VMs, Speicherdienste und App-Dienste in Rechnung gestellt.

## <a name="are-users-charged-for-the-infrastructure-vms"></a>Müssen Benutzer für die Infrastruktur-VMs bezahlen?

Nein. Nutzungsdaten für einige VMs von Azure Stack Hub-Ressourcenanbietern werden Azure gemeldet, aber für diese VMs fallen keine Gebühren an. Auch die VMs, die während der Bereitstellung erstellt wurden, um die Azure Stack Hub-Infrastruktur zu ermöglichen, werden nicht berechnet.  

Benutzern werden nur VMs in Rechnung gestellt, die unter den Mandantenabonnements ausgeführt werden. Alle Workloads müssen in Mandantenabonnements bereitgestellt werden, um die Lizenzbestimmungen von Azure Stack Hub einzuhalten.

## <a name="i-have-a-windows-server-license-i-want-to-use-on-azure-stack-hub-how-do-i-do-it"></a>Wie kann ich eine Windows Server-Lizenz für Azure Stack Hub verwenden?

Durch die Verwendung der vorhandenen Lizenzen wird die Inanspruchnahme von Nutzungsverbrauchseinheiten vermieden. Vorhandenen Windows Server-Lizenzen können in Azure Stack Hub verwendet werden. Dieser Prozess wird im [Azure Stack Hub-Lizenzierungshandbuch](https://go.microsoft.com/fwlink/?LinkId=851536) im Abschnitt „Verwendung der vorhandenen Software mit Azure Stack Hub“ beschrieben. Kunden müssen ihre virtuellen Windows Server-Computer gemäß der Beschreibung im Artikel [Azure-Hybridvorteil für Windows Server](/azure/virtual-machines/windows/hybrid-use-benefit-licensing) bereitstellen, um ihre vorhandenen Lizenzen verwenden zu können.

## <a name="which-subscription-is-charged-for-the-resources-consumed"></a>Welchem Abonnement wird die Nutzung der Ressourcen in Rechnung gestellt?

Die Nutzung wird dem bei der [Registrierung von Azure Stack Hub bei Azure](azure-stack-registration.md) angegebenen Abonnement in Rechnung gestellt.

## <a name="what-types-of-subscriptions-are-supported-for-usage-data-reporting"></a>Welche Arten von Abonnements werden für das Melden von Nutzungsdaten unterstützt?

Für Azure Stack Hub-Systeme mit mehreren Knoten werden Enterprise Agreement-Abonnements (EA) und CSP-Abonnements unterstützt. Beim ASDK unterstützen Enterprise Agreement-Abonnements, Abonnements mit nutzungsbasierter Zahlung, CSP- und MSDN-Abonnements das Melden von Nutzungsdaten.

## <a name="does-usage-data-reporting-work-in-sovereign-clouds"></a>Funktioniert das Melden von Nutzungsdaten in unabhängigen Clouds?

Beim ASDK werden für das Melden von Nutzungsdaten Abonnements benötigt, die im globalen Azure-System erstellt wurden. Abonnements, die in einer der Sovereign Clouds (die Clouds „Azure Government“, „Azure Deutschland“ und „Azure China 21Vianet“) erstellt wurden, können nicht bei Azure registriert werden und unterstützen daher das Melden von Nutzungsdaten nicht.

## <a name="why-doesnt-the-usage-reported-in-azure-stack-hub-match-the-report-generated-from-azure-account-center"></a>Warum stimmen die in Azure Stack Hub gemeldeten Nutzungsdaten nicht mit dem vom Azure-Kontocenter erstellten Bericht überein?

Zwischen den von den Azure Stack Hub-Nutzungs-APIs und den im Azure-Kontocenter gemeldeten Nutzungsdaten besteht immer eine Verzögerung. Die Verzögerung ergibt sich aus der Zeit, die zum Hochladen der Nutzungsdaten von Azure Stack Hub in das Commercesystem von Azure benötigt wird. Aufgrund dieser Verzögerung kann es vorkommen, dass eine Nutzung kurz vor Mitternacht in Azure erst am nächsten Tag angezeigt wird. Wenn Sie die [Azure Stack Hub-Nutzungs-APIs](azure-stack-provider-resource-api.md) verwenden und die Ergebnisse mit den im Azure-Abrechnungsportal angezeigten Nutzungsdaten vergleichen, können Sie eine Abweichung feststellen.

## <a name="next-steps"></a>Nächste Schritte

- [Anbieternutzungs-API](azure-stack-provider-resource-api.md)  
- [Mandantennutzungs-API](azure-stack-tenant-resource-usage-api.md)
- [FAQ zur Nutzung](azure-stack-usage-related-faq.md)
- [Verwalten von Nutzung und Abrechnung als Cloudlösungsanbieter](azure-stack-add-manage-billing-as-a-csp.md)
