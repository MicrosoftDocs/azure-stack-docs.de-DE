---
title: Hinzufügen von Mandanten für Nutzung und Abrechnung zu Azure Stack | Microsoft-Dokumentation
description: Finden Sie heraus, wie Sie einen Mandanten für die Nutzung und Abrechnung zu Azure Stack hinzufügen.
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
ms.date: 09/25/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 09/17/2019
ms.openlocfilehash: 76b870d795b79cf966dcf6742ad08f739d24a42a
ms.sourcegitcommit: 32609bdb04a07b063c8f20f892c30769ad6903dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71269492"
---
# <a name="add-tenant-for-usage-and-billing-to-azure-stack"></a>Hinzufügen von Mandanten für Nutzung und Abrechnung zu Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

Dieser Artikel zeigt, wie Sie einer von einem Cloudlösungsanbieter (Cloud Solution Provider, CSP) verwalteten Azure Stack-Bereitstellung einen Mandanten hinzufügen. Wenn der neue Mandant Ressourcen verwendet, meldet Azure Stack die Nutzung an sein CSP-Abonnement.

CSPs bieten Dienste häufig mehreren Endkunden (Mandanten) in ihrer Azure Stack-Bereitstellung an. Das Hinzufügen von Mandanten zur Azure Stack-Registrierung sorgt dafür, dass die Nutzung jedes Mandanten gemeldet und mit dem entsprechenden CSP-Abonnement abgerechnet wird. Wenn Sie die Schritte in diesem Artikel nicht ausführen, wird die Nutzung durch Mandanten dem für die anfängliche Azure Stack-Registrierung verwendeten Abonnement in Rechnung gestellt. Bevor Sie Azure Stack einen Endkunden für die Nutzungsnachverfolgung und zum Verwalten von Mandanten hinzufügen können, müssen Sie Azure Stack als CSP konfigurieren. Informationen zu Schritten und Ressourcen finden Sie unter [Verwalten von Nutzung und Abrechnung für Azure Stack als Cloudlösungsanbieter](azure-stack-add-manage-billing-as-a-csp.md).

Die folgende Abbildung zeigt die Schritte, die ein CSP ausführen muss, um einem neuen Endkunden die Verwendung von Azure Stack zu ermöglichen und die Nutzungsnachverfolgung für den Kunden einzurichten. Durch Hinzufügen des Endkunden sind Sie auch in der Lage, Ressourcen in Azure Stack zu verwalten. Sie können zwischen zwei Optionen zur Verwaltung ihrer Ressourcen wählen:

- Sie können den Endkunden verwalten und dem Endkunden Anmeldeinformationen für das lokale Azure Stack-Abonnement bereitstellen.  
- Alternativ kann der Endkunde mit seinem Abonnement lokal arbeiten und den CSP als Gast mit Besitzerberechtigungen hinzufügen.

## <a name="add-an-end-customer"></a>Hinzufügen eines Endkunden

Bevor Sie einen Endkunden hinzufügen, müssen Sie die Abrechnung für mehrere Mandanten in Ihre Registrierung aktivieren. Um die Abrechnung für mehrere Mandanten zu aktivieren, senden Sie die Abonnement-ID der Registrierung, den Ressourcengruppennamen und den Registrierungsnamen an `azstcsp@microsoft.com`. Es dauert in der Regel 1 bis 2 Arbeitstage, bis die Abrechnung für mehrere Mandanten aktiviert ist.

Führen Sie die folgenden Schritte aus, um einen Endkunden wie in der folgenden Abbildung dargestellt hinzuzufügen:

![Einrichten des Cloud-Dienstanbieters für die Nutzungsnachverfolgung und zum Verwalten des Endkundenkontos](media/azure-stack-csp-enable-billing-usage-tracking/process-csp-enable-billing.png)

### <a name="create-a-new-customer-in-partner-center"></a>Erstellen eines neuen Kunden im Partner Center

Erstellen Sie im Partner Center ein neues Azure-Abonnement für den Kunden. Anweisungen hierzu finden Sie unter [Neuen Kunden hinzufügen](/partner-center/add-a-new-customer).

### <a name="create-an-azure-subscription-for-the-end-customer"></a>Erstellen eines Azure-Abonnements für den Endkunden

Nachdem Sie einen Datensatz Ihres Kunden im Partner Center erstellt haben, können Sie ihm Abonnements zu Produkten im Katalog verkaufen. Anweisungen hierzu finden Sie unter [Erstellen eines neuen Abonnements](/partner-center/create-a-new-subscription).

### <a name="create-a-guest-user-in-the-end-customer-directory"></a>Erstellen eines Gastbenutzers im Endkundenverzeichnis

Standardmäßig haben Sie als CSP keinen Zugriff auf das Azure Stack-Abonnement des Endkunden. Falls Ihr Kunde möchte, dass seine Ressourcen von Ihnen verwaltet werden, kann er Ihr Konto als Besitzer/Mitwirkender seinem Azure Stack-Abonnement hinzufügen. Hierfür muss der Kunde Ihr Konto seinem Azure AD-Mandanten als Gastbenutzer hinzufügen. Es empfiehlt sich, ein anderes Konto als Ihr Azure-CSP-Konto zum Verwalten des Azure Stack-Abonnements Ihres Kunden zu verwenden. So stellen Sie sicher, dass Sie den Zugriff auf das Azure-Abonnement Ihres Kunden nicht verlieren.

### <a name="update-the-registration-with-the-end-customer-subscription"></a>Aktualisieren der Registrierung mit dem Endkundenabonnement

Aktualisieren Sie Ihre Registrierung mit dem Abonnement des neuen Kunden. Azure meldet die Nutzung des Kunden mithilfe der Kundenidentität aus dem Partner Center. So wird sichergestellt, dass die Nutzung jedes Kunden unter dem individuellen CSP-Abonnement des Kunden gemeldet wird. Dies erleichtert die Nachverfolgung der Nutzung und die Abrechnung. Um diesen Schritt ausführen zu können, müssen Sie [Azure Stack zunächst registrieren](azure-stack-registration.md).

1. Öffnen Sie Windows PowerShell mit einer Eingabeaufforderung mit erhöhten Rechten, und führen Sie Folgendes aus:  

   ```powershell
   Add-AzureRmAccount
   ```

   >[!Note]
   > Wenn Ihre Sitzung abgelaufen ist, Ihr Kennwort geändert wurde oder Sie lediglich Konten wechseln möchten, führen Sie das folgende Cmdlet aus, bevor Sie sich mit Add-AzureRmAccount anmelden: `Remove-AzureRmAccount-Scope Process`

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
| customerSubscriptionID | Das Azure-Abonnement (nicht Azure Stack), das zu dem Kunden gehört, der registriert werden soll. Muss im CSP-Angebot erstellt werden. In der Praxis über das Partner Center. Wenn ein Kunde über mehrere Azure Active Directory-Mandanten verfügt, muss dieses Abonnement in dem Mandanten erstellt werden, der zum Anmelden bei Azure Stack verwendet wird. Die Kundenabonnement-ID muss aus Kleinbuchstaben bestehen. |
| resourceGroup | Die Ressourcengruppe in Azure, in dem Ihre Registrierung gespeichert ist. |
| registrationName | Der Name der Registrierung Ihrer Azure Stack-Instanz. Dies ist ein in Azure gespeichertes Objekt. |
| Properties | Gibt die Eigenschaften für die Ressource an. Verwenden Sie diesen Parameter, um Werte für die Eigenschaften anzugeben, die speziell für diesen Ressourcentyp gelten.

> [!NOTE]  
> Mandanten müssen bei jeder Azure Stack-Instanz registriert werden, die sie verwenden. Wenn Sie über zwei Azure Stack-Bereitstellungen verfügen und ein Mandant beide verwendet, müssen Sie die anfänglichen Registrierungen jeder Bereitstellung mit dem Mandantenabonnement aktualisieren.

### <a name="onboard-tenant-to-azure-stack"></a>Integrieren von Mandanten in Azure Stack

Konfigurieren Sie Azure Stack so, dass Benutzer aus mehreren Azure AD-Mandanten Dienste in Azure Stack verwenden können. Anweisungen finden Sie unter [Aktivieren der Mehrinstanzenfähigkeit in Azure Stack](azure-stack-enable-multitenancy.md).

### <a name="create-a-local-resource-in-the-end-customer-tenant-in-azure-stack"></a>Erstellen einer lokalen Ressource im Endkundenmandanten in Azure Stack

Nachdem Sie Azure Stack den neuen Kunden hinzugefügt haben bzw. nachdem der Endkundenmandant Ihr Gastkonto mit Besitzerberechtigungen aktiviert hat, stellen Sie sicher, dass Sie im Mandanten eine Ressource erstellen können. Zum Beispiel müsste das [Erstellen eines virtuellen Windows-Computers mit dem Azure Stack-Portal](../user/azure-stack-quick-windows-portal.md) möglich sein.

## <a name="next-steps"></a>Nächste Schritte

- Wie Sie ggf. in Ihrem Registrierungsprozess ausgelöste Fehlermeldungen überprüfen, erfahren Sie unter [Fehlermeldungen bei der Mandantenregistrierung](azure-stack-registration-errors.md).
- Weitere Informationen zum Abrufen von Ressourcennutzungsinformationen aus Azure Stack finden Sie unter [Verbrauch und Abrechnung in Azure Stack](azure-stack-billing-and-chargeback.md).
- Wie ein Endkunde Sie (in Ihrer Funktion als CSP) als Manager für seinen Azure Stack-Mandanten hinzufügen kann, erfahren Sie unter [Ermöglichen der Verwaltung Ihres Azure Stack-Abonnements durch Ihren Clouddienstanbieter](../user/azure-stack-csp-enable-billing-usage-tracking.md).
