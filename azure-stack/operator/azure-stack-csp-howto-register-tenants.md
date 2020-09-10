---
title: Hinzufügen von Mandanten für Nutzung und Abrechnung zu Azure Stack Hub
description: Finden Sie heraus, wie Sie einen Mandanten für die Nutzung und Abrechnung zu Azure Stack Hub hinzufügen.
author: sethmanheim
ms.topic: article
ms.date: 9/02/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 5/28/2020
ms.openlocfilehash: e0d426ac19645cbbc318f66b827946534539d125
ms.sourcegitcommit: 7c01ab4b2e2250a7acd67d1c5ba27d15c1e8bce0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2020
ms.locfileid: "89448570"
---
# <a name="add-tenant-for-usage-and-billing-to-azure-stack-hub"></a>Hinzufügen eines Mandanten für Nutzung und Abrechnung zu Azure Stack Hub

In diesem Artikel wird beschrieben, wie Sie einer von einem Cloudlösungsanbieter (Cloud Solution Provider, CSP) verwalteten Azure Stack Hub-Bereitstellung einen Mandanten hinzufügen. Wenn der neue Mandant Ressourcen verwendet, meldet Azure Stack Hub die Nutzung an das CSP-Abonnement des Mandanten.

CSPs bieten häufig mehreren Endkunden (Mandanten) in ihrer Azure Stack Hub-Bereitstellung Dienste an. Durch Hinzufügen von Mandanten zur Azure Stack Hub-Registrierung wird sichergestellt, dass die Nutzung jedes Mandanten gemeldet und dem entsprechenden CSP-Abonnement in Rechnung gestellt wird. Wenn Sie die Schritte in diesem Artikel nicht ausführen, wird die Nutzung durch Mandanten dem für die anfängliche Azure Stack Hub-Registrierung verwendeten Abonnement in Rechnung gestellt. Bevor Sie Azure Stack Hub einen Endkunden zum Nachverfolgen der Nutzung und Verwalten von Mandanten hinzufügen können, müssen Sie Azure Stack Hub als CSP konfigurieren. Informationen zu Schritten und Ressourcen finden Sie unter [Verwalten der Nutzung und Abrechnung für Azure Stack Hub als Cloud Solution Provider](azure-stack-add-manage-billing-as-a-csp.md).

Die folgende Abbildung zeigt die Schritte, die ein CSP ausführen muss, um einem neuen Endkunden die Verwendung von Azure Stack Hub zu ermöglichen und die Nutzungsnachverfolgung für den Kunden einzurichten. Durch Hinzufügen des Endkunden sind Sie auch in der Lage, Ressourcen in Azure Stack Hub zu verwalten. Sie können zwischen zwei Optionen zur Verwaltung ihrer Ressourcen wählen:

- Sie können den Endkunden verwalten und ihm Anmeldeinformationen für das lokale Azure Stack Hub-Abonnement bereitstellen.  
- Alternativ kann der Endkunde mit seinem Abonnement lokal arbeiten und den CSP als Gast mit Besitzerberechtigungen hinzufügen.

## <a name="add-an-end-customer"></a>Hinzufügen eines Endkunden

Bevor Sie einen Endkunden hinzufügen, müssen Sie die Abrechnung für mehrere Mandanten in Ihre Registrierung aktivieren. Um die Abrechnung für mehrere Mandanten zu aktivieren, senden Sie die Abonnement-ID der Registrierung, den Ressourcengruppennamen und den Registrierungsnamen an `azstcsp@microsoft.com`. Es dauert in der Regel 1 bis 2 Arbeitstage, bis die Abrechnung für mehrere Mandanten aktiviert ist.

Führen Sie die folgenden Schritte aus, um einen Endkunden wie in der folgenden Abbildung dargestellt hinzuzufügen:

![Einrichten des Cloudlösungsanbieters für die Nutzungsnachverfolgung und zum Verwalten des Endkundenkontos](media/azure-stack-csp-enable-billing-usage-tracking/process-csp-enable-billing.png)

### <a name="create-a-new-customer-in-partner-center"></a>Erstellen eines neuen Kunden im Partner Center

Erstellen Sie im Partner Center ein neues Azure-Abonnement für den Kunden. Anweisungen hierzu finden Sie unter [Neuen Kunden hinzufügen](/partner-center/add-a-new-customer).

### <a name="create-an-azure-subscription-for-the-end-customer"></a>Erstellen eines Azure-Abonnements für den Endkunden

Nachdem Sie einen Datensatz Ihres Kunden im Partner Center erstellt haben, können Sie ihm Abonnements zu Produkten im Katalog verkaufen. Anweisungen hierzu finden Sie unter [Erstellen eines neuen Abonnements](/partner-center/create-a-new-subscription).

### <a name="create-a-guest-user-in-the-end-customer-directory"></a>Erstellen eines Gastbenutzers im Endkundenverzeichnis

Standardmäßig haben Sie als CSP keinen Zugriff auf das Azure Stack Hub-Abonnement des Endkunden. Falls Ihr Kunde jedoch möchte, dass Sie seine Ressourcen verwalten, kann er Ihr Konto als Besitzer/Mitwirkender zu seinem Azure Stack Hub-Abonnement hinzufügen. Hierfür muss der Kunde Ihr Konto seinem Azure AD-Mandanten als Gastbenutzer hinzufügen. Es empfiehlt sich, zum Verwalten des Azure Stack Hub-Abonnements ein anderes Konto als Ihr Azure-CSP-Konto zu verwenden. So stellen Sie sicher, dass Sie den Zugriff auf das Azure-Abonnement Ihres Kunden nicht verlieren.

### <a name="update-the-registration-with-the-end-customer-subscription"></a>Aktualisieren der Registrierung mit dem Endkundenabonnement

Aktualisieren Sie Ihre Registrierung mit dem Abonnement des neuen Kunden. Azure meldet die Nutzung des Kunden mithilfe der Kundenidentität aus dem Partner Center. So wird sichergestellt, dass die Nutzung jedes Kunden unter dem individuellen CSP-Abonnement des Kunden gemeldet wird. Dies erleichtert die Nachverfolgung der Nutzung und die Abrechnung. Um diesen Schritt ausführen zu können, müssen Sie zuerst [Azure Stack Hub registrieren](azure-stack-registration.md).

1. Öffnen Sie Windows PowerShell in einer Eingabeaufforderung mit erhöhten Rechten, und führen Sie Folgendes aus:  

   ```powershell
   Add-AzureRmAccount
   ```

   >[!NOTE]
   > Wenn Ihre Sitzung abgelaufen ist, Ihr Kennwort geändert wurde oder Sie lediglich Konten wechseln möchten, führen Sie das folgende Cmdlet aus, bevor Sie sich mit **Add-AzureRmAccount** anmelden: `Remove-AzureRmAccount-Scope Process`.

2. Geben Sie Ihre Azure-Anmeldeinformationen ein.
3. Führen Sie in der PowerShell-Sitzung Folgendes aus:

   ```powershell
   New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
   ```

### <a name="new-azurermresource-powershell-parameters"></a>PowerShell-Parameter New-AzureRmResource

Im folgenden Abschnitt werden die Parameter für das Cmdlet **New-AzureRmResource** beschrieben:

| Parameter | BESCHREIBUNG |
| --- | --- |
|registrationSubscriptionID | Das Azure-Abonnement, das für die anfängliche Registrierung von Azure Stack Hub verwendet wurde.|
| customerSubscriptionID | Das Azure-Abonnement (nicht Azure Stack Hub), das zu dem Kunden gehört, der registriert werden soll. Muss im CSP-Angebot erstellt werden. In der Praxis über das Partner Center. Wenn ein Kunde über mehrere Azure Active Directory-Mandanten verfügt, muss dieses Abonnement in dem Mandanten erstellt werden, der zum Anmelden bei Azure Stack Hub verwendet wird. Bei der Kundenabonnement-ID wird zwischen Groß-/Kleinschreibung unterschieden. |
| resourceGroup | Die Ressourcengruppe in Azure, in dem Ihre Registrierung gespeichert ist. |
| registrationName | Der Name der Registrierung Ihrer Azure Stack Hub-Instanz. Dies ist ein in Azure gespeichertes Objekt.

> [!NOTE]  
> Mandanten müssen bei jeder Azure Stack Hub-Instanz registriert werden, die sie verwenden. Wenn Sie über zwei Azure Stack Hub-Bereitstellungen verfügen und ein Mandant beide verwendet, müssen Sie die anfänglichen Registrierungen jeder Bereitstellung beim Mandantenabonnement aktualisieren.

### <a name="onboard-tenant-to-azure-stack-hub"></a>Onboarding von Mandanten in Azure Stack Hub

Konfigurieren Sie Azure Stack Hub so, dass Benutzer aus mehreren Azure AD-Mandanten Dienste in Azure Stack Hub verwenden können. Anweisungen finden Sie unter [Aktivieren der Mehrinstanzenfähigkeit in Azure Stack Hub](azure-stack-enable-multitenancy.md).

### <a name="create-a-local-resource-in-the-end-customer-tenant-in-azure-stack-hub"></a>Erstellen einer lokalen Ressource im Endkundenmandanten in Azure Stack Hub

Nachdem Sie Azure Stack Hub den neuen Kunden hinzugefügt haben bzw. nachdem der Endkundenmandant Ihr Gastkonto mit Besitzerberechtigungen aktiviert hat, stellen Sie sicher, dass Sie im Mandanten eine Ressource erstellen können. Zum Beispiel müsste das [Erstellen eines virtuellen Windows-Computers mit dem Azure Stack Hub-Portal](../user/azure-stack-quick-windows-portal.md) möglich sein.

## <a name="next-steps"></a>Nächste Schritte

- Wie Sie ggf. in Ihrem Registrierungsprozess ausgelöste Fehlermeldungen überprüfen, erfahren Sie unter [Fehlermeldungen bei der Mandantenregistrierung](azure-stack-registration-errors.md).
- Weitere Informationen zum Abrufen von Ressourcennutzungsinformationen aus Azure Stack Hub finden Sie unter [Verbrauch und Abrechnung in Azure Stack Hub](azure-stack-billing-and-chargeback.md).
- Wie ein Endkunde Sie (in Ihrer Funktion als CSP) als Manager für seinen Azure Stack Hub-Mandanten hinzufügen kann, erfahren Sie unter [Ermöglichen der Verwaltung Ihres Azure Stack Hub-Abonnements durch Ihren Cloudlösungsanbieter](../user/azure-stack-csp-enable-billing-usage-tracking.md).
