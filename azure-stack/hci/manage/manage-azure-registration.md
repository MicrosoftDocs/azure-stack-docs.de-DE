---
title: Verwalten der Azure-Registrierung für Azure Stack HCI
description: Vorgehensweise in PowerShell zum Verwalten Ihrer Azure-Registrierung für Azure Stack HCI und Grundlegendes zum Registrierungsstatus.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 07/29/2020
ms.openlocfilehash: 696ef552dcf49f31fb613a22393617e653f7e10d
ms.sourcegitcommit: eb91a28a19a74f799b093ae2a705f7f6e4c5cd49
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/30/2020
ms.locfileid: "87436466"
---
# <a name="manage-azure-registration"></a>Verwalten der Azure-Registrierung

> Gilt für Azure Stack HCI v20H2

Nachdem Sie einen Azure Stack HCI-Cluster erstellt haben, müssen Sie [den Cluster bei Azure Arc registrieren](../deploy/register-with-azure.md). Nach der Registrierung synchronisiert der Cluster regelmäßig Informationen zwischen dem lokalen Cluster und der Cloud. In diesem Thema wird erläutert, wie Ihr Registrierungsstatus zu verstehen ist und wie Sie die Registrierung Ihres Clusters aufheben, wenn Sie bereit sind, ihn außer Betrieb zu nehmen.

## <a name="understanding-registration-status"></a>Grundlegendes zum Registrierungsstatus

Verwenden Sie zum Verstehen des Registrierungsstatus das PowerShell-Cmdlet `Get-AzureStackHCI` und die Eigenschaften `ClusterStatus`, `RegistrationStatus` und `ConnectionStatus`. Beispielsweise wird für die Eigenschaft `ClusterStatus` nach dem Installieren des Azure Stack HCI-Betriebssystems vor dem Erstellen eines Clusters oder dem Beitreten zu einem Cluster der Status „not yet“ angezeigt:

:::image type="content" source="media/manage-azure-registration/1-get-azurestackhci.png" alt-text="Azure-Registrierungsstatus vor der Clustererstellung":::

Nach dem Erstellen des Clusters wird nur für `RegistrationStatus` der Status „not yet“ angezeigt:

:::image type="content" source="media/manage-azure-registration/2-get-azurestackhci.png" alt-text="Azure-Registrierungsstatus nach der Clustererstellung":::

Azure Stack HCI muss gemäß den Bestimmungen für Azure-Onlinedienste innerhalb von 30 Tagen nach der Installation registriert werden. Wenn nach 30 Tagen keine Clustererstellung erfolgt ist, wird für `ClusterStatus` der Wert `OutOfPolicy` angezeigt, und wenn nach 30 Tagen keine Registrierung erfolgt ist, wird für `RegistrationStatus` `OutOfPolicy` angezeigt.

Nach der Registrierung des Clusters können Sie die `ConnectionStatus`- und die `LastConnected`-Uhrzeit sehen, die normalerweise innerhalb des vergangenen Tages liegt, es sei denn, der Cluster wurde vorübergehend vom Internet getrennt. Ein Azure Stack HCI-Cluster kann bis zu 30 Tage in Folge uneingeschränkt offline betrieben werden.

:::image type="content" source="media/manage-azure-registration/3-get-azurestackhci.png" alt-text="Azure-Registrierungsstatus nach der Registrierung":::

Wenn dieser maximale Zeitraum überschritten wird, wird für `ConnectionStatus` `OutOfPolicy` angezeigt.

## <a name="azure-active-directory-permissions"></a>Azure Active Directory-Berechtigungen

Über das Erstellen einer Azure-Ressource in Ihrem Abonnement hinaus wird durch das Registrieren von Azure Stack HCI eine App-Identität in Ihrem Azure Active Directory-Mandanten erstellt, die konzeptionell einem Benutzer ähnlich ist. Die App-Identität erbt den Clusternamen. Diese Identität fungiert gegebenenfalls innerhalb Ihres Abonnements im Auftrag des Azure Stack HCI-Clouddiensts.

Wenn der Benutzer, der `Register-AzureStackHCI` ausführt, ein Azure Active Directory-Administrator ist oder ihm ausreichende Berechtigungen erteilt wurden, geschieht all dies automatisch, und es ist kein weiteres Eingreifen erforderlich. Andernfalls ist möglicherweise die Genehmigung durch Ihren Azure Active Directory-Administrator erforderlich, um die Registrierung abzuschließen. Ihr Administrator kann wahlweise der App explizit seine Zustimmung erteilen oder Berechtigungen delegieren, so dass Sie der App Zustimmung erteilen können:

:::image type="content" source="media/manage-azure-registration/aad-permissions.png" alt-text="Diagramm zu Azure Active Directory-Berechtigungen und Identität" border="false":::

Öffnen Sie zum Erteilen Ihrer Zustimmung „portal.azure.com“, und melden Sie sich mit einem Azure-Konto an, das über ausreichende Berechtigungen für Azure Active Directory verfügt. Navigieren Sie zu **Azure Active Directory** und dann zu **App-Registrierungen**. Wählen Sie die App-Identität aus, die nach Ihrem Cluster benannt ist, und navigieren Sie zu **API-Berechtigungen**.

Für die App sind zwei Berechtigungen erforderlich:

```http
https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Census.Sync

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Billing.Sync
```

Das Einholen der Genehmigung bei Ihrem Azure Active Directory-Administrator kann einige Zeit in Anspruch nehmen, daher wird das Cmdlet `Register-AzureStackHCI` beendet und hinterlässt die Registrierung im Status „pending admin consent“, also teilweise abgeschlossen. Nachdem die Zustimmung erteilt wurde, führen Sie einfach `Register-AzureStackHCI` erneut aus, um die Registrierung abzuschließen.

## <a name="unregister-azure-stack-hci-with-azure"></a>Aufheben der Registrierung von Azure Stack HCI bei Azure

Wenn Sie bereit sind, Ihren Azure Stack HCI-Cluster außer Betrieb zu setzen, verwenden Sie das Cmdlet `Unregister-AzStackHCI` zum Aufheben der Registrierung. Dadurch werden alle über Azure Arc bereitgestellten Überwachungs-, Support-und Abrechnungsfunktionen beendet. Die Azure-Ressource, die den Cluster darstellt, und die Azure Active Directory-App-Identität werden gelöscht, nicht jedoch die Ressourcengruppe, da sie andere, damit nicht in Zusammenhang stehende Ressourcen enthalten kann.

Wenn Sie das Cmdlet `Unregister-AzStackHCI` für einen Clusterknoten ausführen, verwenden Sie diese Syntax, und geben Sie Ihre Azure-Abonnement-ID sowie den Ressourcennamen des Azure Stack HCI-Clusters an, dessen Registrierung Sie aufheben möchten:

```PowerShell
Unregister-AzStackHCI -SubscriptionId "e569b8af-6ecc-47fd-a7d5-2ac7f23d8bfe" -ResourceName HCI001
```

Sie werden aufgefordert, „microsoft.com/devicelogin“ auf einem anderen Gerät (etwa auf Ihrem PC oder Smartphone) aufzurufen, den Code einzugeben und sich dort für die Authentifizierung mit Azure anzumelden.

Wenn Sie das Cmdlet auf einem Verwaltungs-PC ausführen, müssen Sie zudem den Namen eines Servers im Cluster angeben:

```PowerShell
Unregister-AzStackHCI -ComputerName ClusterNode1 -SubscriptionId "e569b8af-6ecc-47fd-a7d5-2ac7f23d8bfe" -ResourceName HCI001
```

Ein interaktives Azure-Anmeldefenster wird angezeigt. Welche Aufforderungen genau angezeigt werden, unterscheidet sich je nach Ihren Sicherheitseinstellungen (z. B. zweistufige Authentifizierung). Folgen Sie den Anweisungen, um sich anzumelden.

## <a name="next-steps"></a>Nächste Schritte

Verwandte Informationen finden Sie außerdem unter:

- [Herstellen einer Verbindung von Azure Stack HCI mit Azure](../deploy/register-with-azure.md)
- [Überwachen von Azure Stack HCI mit Azure Monitor](azure-monitor.md)
