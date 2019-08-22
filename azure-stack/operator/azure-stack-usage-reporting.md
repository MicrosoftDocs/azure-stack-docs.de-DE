---
title: Melden von Azure Stack-Nutzungsdaten an Azure | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie das Melden von Nutzungsdaten in Azure Stack einrichten.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/12/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 3aeae5c1a0106a0c13c9b6bbe2eb4ba07de14dd4
ms.sourcegitcommit: 58c28c0c4086b4d769e9d8c5a8249a76c0f09e57
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/12/2019
ms.locfileid: "68959440"
---
# <a name="report-azure-stack-usage-data-to-azure"></a>Melden von Azure Stack-Nutzungsdaten an Azure

Nutzungsdaten, auch Verbrauchsdaten genannt, stellen die Menge der verwendeten Ressourcen dar.

Azure Stack-Systeme mit mehreren Knoten, die das nutzungsbasierte Abrechnungsmodell verwenden, sollten die Nutzungsdaten zu Abrechnungszwecken an Azure melden. Azure Stack-Betreiber sollten ihre Azure Stack-Instanz so konfigurieren, dass diese Nutzungsdaten an Azure meldet.

> [!IMPORTANT]
> Alle Workloads [müssen unter Mandantenabonnements bereitgestellt werden](#are-users-charged-for-the-infrastructure-vms), um die Lizenzbedingungen von Azure Stack einzuhalten.

Die Nutzungsdaten-Berichterstellung ist für die Benutzer der Azure Stack-Systeme mit mehreren Knoten erforderlich, die im Rahmen des nutzungsbasierten Modells lizenziert sind. Sie ist optional für Kunden, die im Rahmen des Kapazitätsmodells lizenziert sind (siehe Seite [Azure Stack erwerben](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)). Für Azure Stack Development Kit-Benutzer (ASDK) können Azure Stack-Betreiber Nutzungsdaten melden und die Funktion testen. Allerdings werden Benutzern keinerlei Kosten berechnet, die für die Nutzung anfallen.

![Abrechnungsablauf](media/azure-stack-usage-reporting/billing-flow.png)

Nutzungsdaten werden von Azure Stack über Azure Bridge an Azure gesendet. In Azure verarbeitet das Commerce-System die Nutzungsdaten und erstellt die Rechnung. Nachdem die Rechnung erstellt wurde, kann der Besitzer des Azure-Abonnements diese im [Azure-Kontocenter](https://account.windowsazure.com/subscriptions) anzeigen und von dort herunterladen. Informationen zur Lizenzierung von Azure Stack finden Sie im Dokument [Azure Stack packaging and pricing](https://go.microsoft.com/fwlink/?LinkId=842847) (Paketerstellung und Preise von Azure Stack).

## <a name="set-up-usage-data-reporting"></a>Einrichten der Berichterstellung zu Nutzungsdaten

Um die Berichterstellung zu Nutzungsdaten einzurichten, müssen Sie [Ihre Azure Stack-Instanz bei Azure registrieren](azure-stack-registration.md). Im Rahmen des Registrierungsprozesses wird die Azure-Bridge-Komponente von Azure Stack konfiguriert, die eine Verbindung zwischen Azure Stack und Azure herstellt und die Nutzungsdaten sendet. Die folgenden Nutzungsdaten werden von Azure Stack an Azure gesendet:

- **ID der Verbrauchseinheit**: Eine eindeutige ID für die verwendete Ressource.
- **Menge**: Menge der Ressourcennutzung.
- **Speicherort**: Der Speicherort, an dem die aktuelle Azure Stack-Ressource bereitgestellt wurde.
- **Ressourcen-URI**: Ein vollqualifizierter URI der Ressource, deren Nutzung gemeldet wird.
- **Abonnement-ID:** Die Abonnement-ID des Azure Stack-Benutzers. Dies ist das lokale Abonnement (Azure Stack).
- **Zeit**: Die Start- und Endzeit der Nutzungsdaten. Es besteht eine Verzögerung zwischen der Zeit, zu der die Ressourcen in Azure Stack verwendet werden, und der Zeit, zu der die Nutzungsdaten an das Commerce-System gemeldet werden. Azure Stack aggregiert Nutzungsdaten für jeweils 24 Stunden, und der Vorgang des Meldens von Nutzungsdaten an das Commerce-System in Azure dauert zusätzlich einige Stunden. Daher kann es vorkommen, dass eine Nutzung kurz vor Mitternacht in Azure erst am nächsten Tag angezeigt wird.

## <a name="generate-usage-data-reporting"></a>Generieren der Berichterstellung zu Nutzungsdaten

- Um die Berichterstellung zu Nutzungsdaten zu testen, erstellen Sie einige Ressourcen in Azure Stack. Beispielsweise können Sie ein [Speicherkonto](azure-stack-provision-storage-account.md), eine [Windows Server-VM](../user/azure-stack-create-vm-template.md) und eine Linux-VM mit Basic- und Standard-SKUs erstellen, um zu veranschaulichen, wie die Kernnutzung gemeldet wird. Die Nutzungsdaten für verschiedene Ressourcentypen werden unter verschiedenen Verbrauchseinheiten gemeldet.

- Führen Sie Ihre Ressourcen einige Stunden lang aus. Nutzungsinformationen werden etwa einmal pro Stunde erfasst. Nachdem die Informationen erfasst wurden, werden diese Daten an Azure übertragen und im Commerce-System von Azure verarbeitet. Dieser Prozess kann einige Stunden dauern.

## <a name="view-usage---csp-subscriptions"></a>Anzeigen der Nutzung – CSP-Abonnements

Wenn Sie Ihre Azure Stack-Instanz mit einem CSP-Abonnement registriert haben, können Sie Ihre Nutzung und Gebühren auf die gleiche Weise anzeigen, wie Sie die Azure-Nutzungsdaten sehen. Die Nutzung von Azure Stack wird in Ihrer Rechnung und in der Abstimmungsdatei aufgeführt, die über [Partner Center](https://partnercenter.microsoft.com/partner/home) erhältlich ist. Die Abstimmungsdatei wird monatlich aktualisiert. Wenn Sie auf die aktuellen Informationen zur Nutzung von Azure Stack zugreifen müssen, können Sie die Partner Center APIs verwenden.

![Partner Center](media/azure-stack-usage-reporting/partner-center.png)

## <a name="view-usage---enterprise-agreement-subscriptions"></a>Anzeigen der Nutzung – Enterprise Agreement-Abonnements

Wenn Sie Ihre Azure Stack-Instanz mit einem Enterprise Agreement-Abonnement registriert haben, können Sie Ihre Nutzung und Gebühren im [EA-Portal](https://ea.azure.com/) sehen. Neben den Azure-Nutzungsdaten werden in diesem Portal im Abschnitt zu den Berichten unter den erweiterten Downloads die Azure Stack-Nutzungsdaten aufgeführt.

## <a name="view-usage---other-subscriptions"></a>Anzeigen der Nutzung – Andere Abonnements

Wenn Sie Ihre Azure Stack-Instanz mit einem anderen Abonnementtyp, z. B. einem nutzungsbasierten Abonnement, registriert haben, können Sie die Nutzung und die Gebühren im Azure-Kontocenter einsehen. Melden Sie sich als Azure-Kontoadministrator beim [Azure-Kontocenter](https://account.windowsazure.com/subscriptions) an, und wählen Sie das Azure-Abonnement aus, das Sie zum Registrieren von Azure Stack verwendet haben. Ihnen werden wie in der folgenden Abbildung dargestellt die Azure Stack-Nutzungsdaten und der berechnete Betrag für jede der verwendeten Ressourcen angezeigt:

![Abrechnungsablauf](media/azure-stack-usage-reporting/pricing-details.png)

Für das Azure Stack Development Kit fallen für Azure Stack-Ressourcen keine Gebühren an. Daher wird als Preis 0,00 USD angezeigt.

## <a name="which-azure-stack-deployments-are-charged"></a>Welche Azure Stack-Bereitstellungen werden in Rechnung gestellt?

Die Ressourcennutzung ist für das Azure Stack Development Kit kostenlos. Bei Azure Stack-Systemen mit mehreren Knoten werden Workload-VMs, Speicherdienste und App-Dienste in Rechnung gestellt.

## <a name="are-users-charged-for-the-infrastructure-vms"></a>Müssen Benutzer für die Infrastruktur-VMs bezahlen?

Nein. Nutzungsdaten für einige VMs von Azure Stack-Ressourcenanbietern werden Azure gemeldet, aber es fallen keine Gebühren für diese VMs oder die während der Bereitstellung erstellten VMs an, um die Azure Stack-Infrastruktur zu aktivieren.  

Benutzern werden nur VMs in Rechnung gestellt, die unter den Mandantenabonnements ausgeführt werden. Alle Workloads müssen unter Mandantenabonnements bereitgestellt werden, um die Lizenzbedingungen von Azure Stack einzuhalten.

## <a name="i-have-a-windows-server-license-i-want-to-use-on-azure-stack-how-do-i-do-it"></a>Wie kann ich eine Windows Server-Lizenz für Azure Stack verwenden?

Durch die Verwendung der vorhandenen Lizenzen wird die Inanspruchnahme von Nutzungsverbrauchseinheiten vermieden. Vorhandene Windows Server-Lizenzen können in Azure Stack verwendet werden, wie im [Azure Stack-Lizenzierungshandbuch](https://go.microsoft.com/fwlink/?LinkId=851536) im Abschnitt „Verwendung der vorhandenen Software mit Azure Stack“ beschrieben wird. Kunden müssen ihre virtuellen Windows Server-Computer gemäß der Beschreibung im Artikel [Azure-Hybridvorteil für Windows Server](/azure/virtual-machines/windows/hybrid-use-benefit-licensing) bereitstellen, um ihre vorhandenen Lizenzen verwenden zu können.

## <a name="which-subscription-is-charged-for-the-resources-consumed"></a>Welchem Abonnement wird die Nutzung der Ressourcen in Rechnung gestellt?

Die Nutzung wird dem bei der [Registrierung von Azure Stack bei Azure](azure-stack-registration.md) angegebene Abonnement in Rechnung gestellt.

## <a name="what-types-of-subscriptions-are-supported-for-usage-data-reporting"></a>Welche Arten von Abonnements werden für das Melden von Nutzungsdaten unterstützt?

Für Azure Stack-Systeme mit mehreren Knoten werden das Enterprise Agreement-Abonnement (EA) und das CSP-Abonnement unterstützt. Beim Azure Stack Development Kit unterstützen Enterprise Agreement-Abonnements, Abonnements mit nutzungsbasierter Zahlung, CSP- und MSDN-Abonnements das Melden von Nutzungsdaten.

## <a name="does-usage-data-reporting-work-in-sovereign-clouds"></a>Funktioniert das Melden von Nutzungsdaten in unabhängigen Clouds?

Beim Azure Stack Development Kit werden für das Melden von Nutzungsdaten Abonnements benötigt, die im globalen Azure-System erstellt wurden. Abonnements, die in einer der Sovereign Clouds (die Clouds „Azure Government“, „Azure Deutschland“ und „Azure China“) erstellt wurden, können nicht bei Azure registriert werden und unterstützen daher das Melden von Nutzungsdaten nicht.

## <a name="why-doesnt-the-usage-reported-in-azure-stack-match-the-report-generated-from-azure-account-center"></a>Warum stimmen die in Azure Stack gemeldeten Nutzungsdaten nicht mit dem vom Azure-Kontocenter erstellten Bericht überein?

Zwischen den von den Azure Stack-Nutzungs-APIs und den vom Azure-Kontocenter gemeldeten Nutzungsdaten tritt immer eine Verzögerung auf. Die Verzögerung ergibt sich aus der Zeit, die zum Hochladen der Nutzungsdaten von Azure Stack in das Commerce-System von Azure benötigt wird. Aufgrund dieser Verzögerung kann es vorkommen, dass eine Nutzung kurz vor Mitternacht in Azure erst am nächsten Tag angezeigt wird. Wenn Sie die [Azure Stack-Nutzungs-APIs](azure-stack-provider-resource-api.md) verwenden und die Ergebnisse mit den im Azure-Abrechnungsportal angezeigten Nutzungsdaten vergleichen, können Sie eine Abweichung feststellen.

## <a name="next-steps"></a>Nächste Schritte

- [Anbieternutzungs-API](azure-stack-provider-resource-api.md)  
- [Mandantennutzungs-API](azure-stack-tenant-resource-usage-api.md)
- [FAQ zur Nutzung](azure-stack-usage-related-faq.md)
- [Verwalten von Nutzung und Abrechnung als Cloud-Dienstanbieter](azure-stack-add-manage-billing-as-a-csp.md)
