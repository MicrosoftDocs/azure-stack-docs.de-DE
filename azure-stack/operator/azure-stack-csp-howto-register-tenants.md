---
title: Hinzufügen von Mandanten für Nutzung und Abrechnung zu Azure Stack | Microsoft-Dokumentation
description: Die erforderlichen Schritte fügen Azure Stack einen Endbenutzer hinzu, der von einem Cloud-Dienstanbieter (CSP) verwaltet wird.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 01/05/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: alfredop
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: 8e177944a5f57c9475287325b705fac34ec513c0
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "64307824"
---
# <a name="add-tenant-for-usage-and-billing-to-azure-stack"></a>Hinzufügen von Mandanten für Nutzung und Abrechnung zu Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

In diesem Artikel werden die erforderlichen Schritte beschrieben, um einen Endbenutzer zu einer Azure Stack-Bereitstellung hinzuzufügen, die von einem Cloud-Dienstanbieter (Cloud Service Provider, CSP) verwaltet wird. Wenn der neue Mandant Ressourcen verwendet, meldet Azure Stack die Nutzung an sein CSP-Abonnement.

CSPs bieten Dienste häufig mehreren Endkunden (Mandanten) in ihrer Azure Stack-Bereitstellung an. Das Hinzufügen von Mandanten zur Azure Stack-Registrierung sorgt dafür, dass die Nutzung jedes Mandanten gemeldet und mit dem entsprechenden CSP-Abonnement abgerechnet wird. Wenn Sie die Schritte in diesem Artikel nicht ausführen, wird die Nutzung durch Mandanten dem für die anfängliche Azure Stack-Registrierung verwendeten Abonnement in Rechnung gestellt. Bevor Sie Azure Stack einen Endkunden für die Nutzungsnachverfolgung und zum Verwalten von Mandanten hinzufügen können, müssen Sie Azure Stack als CSP konfigurieren. Informationen zu Schritten und Ressourcen finden Sie unter [Verwalten von Nutzung und Abrechnung für Azure Stack als Cloud-Dienstanbieter ](azure-stack-add-manage-billing-as-a-csp.md).

Die folgende Abbildung zeigt die Schritte, die ein CSP befolgen muss, um einem neuen Kunden die Verwendung von Azure Stack zu ermöglichen und die Nutzungsnachverfolgung für den Kunden einzurichten. Durch Hinzufügen des Endkunden sind Sie auch in der Lage, Ressourcen in Azure Stack zu verwalten. Sie können zwischen zwei Optionen zur Verwaltung ihrer Ressourcen wählen:

1. Sie können den Endkunden verwalten und dem Endkunden Anmeldeinformationen für das lokale Azure Stack-Abonnement bereitstellen.  
2. Alternativ kann der Endkunde mit seinem Abonnement lokal arbeiten und den CSP als Gast mit Besitzerberechtigungen hinzufügen.  

## <a name="steps-to-add-an-end-customer"></a>Schritte zum Hinzufügen eines Endkunden

![Einrichten des Cloud-Dienstanbieters für die Nutzungsnachverfolgung und zum Verwalten des Endkundenkontos](media/azure-stack-csp-enable-billing-usage-tracking/process-csp-enable-billing.png)

### <a name="create-a-new-customer-in-partner-center"></a>Erstellen eines neuen Kunden im Partner Center

Erstellen Sie im Partner Center ein neues Azure-Abonnement für den Kunden. Anweisungen hierzu finden Sie unter [Neuen Kunden hinzufügen](https://msdn.microsoft.com/partner-center/add-a-new-customer).

### <a name="create-an-azure-subscription-for-the-end-customer"></a>Erstellen eines Azure-Abonnements für den Endkunden

Nachdem Sie einen Datensatz Ihres Kunden im Partner Center erstellt haben, können Sie ihm Abonnements zu Produkten im Katalog verkaufen. Anweisungen hierzu finden Sie unter [Erstellen eines neuen Abonnements](https://msdn.microsoft.com/partner-center/create-a-new-subscription).

### <a name="create-a-guest-user-in-the-end-customer-directory"></a>Erstellen eines Gastbenutzers im Endkundenverzeichnis

Wenn der Endkunde sein eigenes Konto verwaltet, erstellen Sie einen Gastbenutzer in seinem Verzeichnis, an den Sie die Informationen senden. Der Endbenutzer fügt dann den Gast hinzu und erhöht die Gastberechtigung für das Azure Stack-CSP-Konto auf **Besitzer**.

### <a name="update-the-registration-with-the-end-customer-subscription"></a>Aktualisieren der Registrierung mit dem Endkundenabonnement

Aktualisieren Sie Ihre Registrierung mit dem Abonnement des neuen Kunden. Azure meldet die Nutzung des Kunden mithilfe der Kundenidentität aus dem Partner Center. So wird sichergestellt, dass die Nutzung jedes Kunden unter dem individuellen CSP-Abonnement des Kunden gemeldet wird. Dies erleichtert die Nachverfolgung der Nutzung durch den Benutzer und die Abrechnung.

> [!NOTE]  
> Um diesen Schritt ausführen zu können, müssen Sie [Azure Stack registriert](azure-stack-registration.md ) haben.

1. Öffnen Sie Windows PowerShell mit einer Eingabeaufforderung mit erhöhten Rechten, und führen Sie Folgendes aus:  
    `Add-AzureRmAccount -EnvironmentName AzureChinaCloud`
2. Geben Sie Ihre Azure-Anmeldeinformationen ein.
3. Führen Sie in der PowerShell-Sitzung Folgendes aus:

   ```powershell
   New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties <PSObject>
   ```

### <a name="new-azurermresource-powershell-parameters"></a>PowerShell-Parameter New-AzureRmResource

Im folgenden Abschnitt werden die Parameter für das Cmdlet **New-AzureRmResource** beschrieben:

| Parameter | BESCHREIBUNG |
| --- | --- |
|registrationSubscriptionID | Das Azure-Abonnement, das für die anfängliche Registrierung von Azure Stack verwendet wurde.|
| customerSubscriptionID | Das Azure-Abonnement (nicht Azure Stack), das zu dem Kunden gehört, der registriert werden soll. Muss im CSP-Angebot erstellt werden – in der Praxis über das Partner Center. Wenn ein Kunde über mehrere Azure Active Directory-Mandanten verfügt, muss dieses Abonnement in dem Mandanten erstellt werden, der zum Anmelden bei Azure Stack verwendet wird. Die Kundenabonnement-ID muss aus Kleinbuchstaben bestehen. |
| Ressourcengruppe | Die Ressourcengruppe in Azure, in dem Ihre Registrierung gespeichert ist. |
| registrationName | Der Name der Registrierung Ihrer Azure Stack-Instanz. Dies ist ein in Azure gespeichertes Objekt. |
| Eigenschaften | Gibt die Eigenschaften für die Ressource an. Verwenden Sie diesen Parameter, um Werte für die Eigenschaften anzugeben, die speziell für diesen Ressourcentyp gelten.

> [!NOTE]  
> Mandanten müssen bei jeder Azure Stack-Instanz registriert werden, die sie verwenden. Wenn Sie über zwei Azure Stack-Bereitstellungen verfügen und ein Mandant beide verwendet, müssen Sie die anfänglichen Registrierungen jeder Bereitstellung mit dem Mandantenabonnement aktualisieren.

### <a name="onboard-tenant-to-azure-stack"></a>Integrieren von Mandanten in Azure Stack

Konfigurieren Sie Azure Stack so, dass Benutzer aus mehreren Azure AD-Mandanten Dienste in Azure Stack verwenden können. Anweisungen finden Sie unter [Aktivieren der Mehrinstanzenfähigkeit in Azure Stack](azure-stack-enable-multitenancy.md).

### <a name="create-a-local-resource-in-the-end-customer-tenant-in-azure-stack"></a>Erstellen einer lokalen Ressource im Endkundenmandanten in Azure Stack

Nachdem Sie Azure Stack den neuen Kunden hinzugefügt haben, bzw. der Endkundenmandant Ihr Gastkonto mit Besitzerberechtigungen aktiviert hat, stellen Sie sicher, dass Sie eine Ressource in Ihrem Mandanten erstellen können. Zum Beispiel müsste das [Erstellen eines virtuellen Windows-Computers mit dem Azure Stack-Portal](../user/azure-stack-quick-windows-portal.md) möglich sein.

## <a name="next-steps"></a>Nächste Schritte

- Wie Sie ggf. die in Ihrem Registrierungsprozess ausgelösten Fehlermeldungen überprüfen, erfahren Sie unter [Fehlermeldungen bei der Mandantenregistrierung](azure-stack-csp-ref-infrastructure.md#usage-and-billing-error-codes).
- Weitere Informationen zum Abrufen von Ressourcennutzungsinformationen aus Azure Stack finden Sie unter [Verbrauch und Abrechnung in Azure Stack](azure-stack-billing-and-chargeback.md).
- Wie ein Endkunde Sie als CSP als Manager für seinen Azure Stack-Mandanten hinzufügen kann, erfahren Sie unter [Wie Sie einem Cloud-Dienstanbieter ermöglichen, Ihr Azure Stack-Abonnement zu verwalten](../user/azure-stack-csp-enable-billing-usage-tracking.md).
