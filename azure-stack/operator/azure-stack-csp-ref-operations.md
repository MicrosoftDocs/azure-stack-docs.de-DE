---
title: Registrieren von Mandanten für die Nutzungsnachverfolgung in Azure Stack | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Mandanten registrieren und die Mandantennutzung in Azure Stack nachverfolgen.
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
ms.date: 09/17/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 01/08/2019
ms.openlocfilehash: a9e0dd05195d7ece62689aa8b5971cf72a6e3713
ms.sourcegitcommit: c2ea4ffb42563c26faaf2993ba7b484bcb6d5cb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71342821"
---
# <a name="register-tenants-for-usage-tracking-in-azure-stack"></a>Registrieren von Mandanten für die Nutzungsnachverfolgung in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

Dieser Artikel enthält Details zu Registrierungsvorgängen. Sie können diese Vorgänge für die folgenden Aufgaben verwenden:

- Verwalten von Mandantenregistrierungen
- Verwalten der Nutzungsnachverfolgung von Mandanten

## <a name="add-tenant-to-registration"></a>Hinzufügen von Mandanten zur Registrierung

Sie können diesen Vorgang verwenden, wenn Sie Ihrer Registrierung einen neuen Mandanten hinzufügen möchten. Die Mandantennutzung wird unter einem Azure-Abonnement gemeldet, das mit dem Azure AD-Mandanten (Azure Active Directory) verbunden ist.

Sie können den Vorgang auch verwenden, um das einem Mandanten zugeordnete Abonnement zu ändern. Rufen Sie PUT oder **New-AzureRMResource** auf, um die vorherige Zuordnung zu überschreiben.

Sie können einem Mandanten ein einzelnes Azure-Abonnement zuweisen. Wenn Sie versuchen, einem vorhandenen Mandanten ein zweites Abonnement hinzuzufügen, wird das erste Abonnement überschrieben.

### <a name="use-api-profiles"></a>Verwenden von API-Profilen

Die folgenden Registrierungs-Cmdlets erfordern, dass Sie beim Ausführen von PowerShell ein API-Profil angeben. API-Profile stellen mehrere Azure-Ressourcenanbieter und ihre API-Versionen dar. Sie unterstützen Sie dabei, bei der Interaktion mit mehreren Azure-Clouds die richtige Version der API zu verwenden. Wenn Sie z. B. im Zusammenhang mit globalen Azure- und Azure Stack-Instanzen mit mehreren Clouds arbeiten, geben API-Profile einen Namen an, der mit ihrem Veröffentlichungsdatum übereinstimmt. Sie verwenden das Profil **2017-09-03**.

Weitere Informationen zu Azure Stack und API-Profilen finden Sie unter [Verwalten von API-Versionsprofilen in Azure Stack](../user/azure-stack-version-profiles.md).

### <a name="parameters"></a>Parameter

| Parameter                  | BESCHREIBUNG |
|---                         | --- |
| registrationSubscriptionID | Das Azure-Abonnement, das für die anfängliche Registrierung verwendet wurde. |
| customerSubscriptionID     | Das Azure-Abonnement (nicht Azure Stack), das zu dem Kunden gehört, der registriert werden soll. Muss im CSP-Angebot (Cloud Solution Provider) über Partner Center erstellt werden. Wenn ein Kunde über mehrere Mandanten verfügt, erstellen Sie für den Mandanten ein Abonnement zum Anmelden bei Azure Stack. |
| resourceGroup              | Die Ressourcengruppe in Azure, in dem Ihre Registrierung gespeichert ist. |
| registrationName           | Der Name der Registrierung Ihrer Azure Stack-Instanz. Dies ist ein in Azure gespeichertes Objekt. Der Name hat in der Regel die Form **azurestack-CloudID**, wobei **CloudID** die Cloud-ID Ihrer Azure Stack-Bereitstellung ist. |

> [!NOTE]  
> Mandanten müssen bei jeder Azure Stack-Bereitstellung registriert werden, die sie verwenden. Wenn ein Mandant mehrere Azure Stack-Instanzen verwendet, aktualisieren Sie die anfängliche Registrierung jeder Bereitstellung mit dem Mandantenabonnement.

### <a name="powershell"></a>PowerShell

Verwenden Sie das Cmdlet **New-AzureRmResource**, um einen Mandanten hinzuzufügen. [Stellen Sie eine Verbindung mit Azure Stack her](azure-stack-powershell-configure-admin.md), und verwenden Sie dann in einer Eingabeaufforderung mit erhöhten Rechten das folgende Cmdlet:

```powershell
New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties
```

### <a name="api-call"></a>API-Aufruf

**Operation:** PUT  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  /providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Antwort**: 201 – Erstellt  
**Antworttext**: Leer  

## <a name="list-all-registered-tenants"></a>Auflisten aller registrierten Mandanten

Rufen Sie eine Liste aller Mandanten ab, die einer Registrierung hinzugefügt wurden.

 > [!NOTE]  
 > Wenn keine Mandanten registriert wurden, erhalten Sie keine Antwort.

### <a name="parameters"></a>Parameter

| Parameter                  | BESCHREIBUNG          |
|---                         | ---                  |
| registrationSubscriptionID | Das Azure-Abonnement, das für die anfängliche Registrierung verwendet wurde.   |
| resourceGroup              | Die Ressourcengruppe in Azure, in dem Ihre Registrierung gespeichert ist.    |
| registrationName           | Der Name der Registrierung Ihrer Azure Stack-Bereitstellung. Dies ist ein in Azure gespeichertes Objekt. Der Name hat in der Regel die Form **azurestack-CloudID**, wobei **CloudID** die Cloud-ID Ihrer Azure Stack-Bereitstellung ist.   |

### <a name="powershell"></a>PowerShell

Verwenden Sie das Cmdlet **Get-AzureRmResource**, um alle registrierten Mandanten aufzulisten. [Stellen Sie eine Verbindung mit Azure Stack her](azure-stack-powershell-configure-admin.md), und führen Sie dann in einer Eingabeaufforderung mit erhöhten Rechten das folgende Cmdlet aus:

```powershell
Get-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>API-Aufruf

Sie können eine Liste aller Mandantenzuordnungen mithilfe des GET-Vorgangs abrufen.

**Operation:** GET  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions?  
api-version=2017-06-01 HTTP/1.1`  
**Antwort**: 200  
**Antworttext**:

```json
{
    "value": [{
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 1}",
            "name": " cspSubscriptionId 1",
            "type": "Microsoft.AzureStack\customerSubscriptions",
            "properties": { "tenantId": "tId1" }
        },
        {
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 2}",
            "name": " cspSubscriptionId2 ",
            "type": "Microsoft.AzureStack\customerSubscriptions",
            "properties": { "tenantId": "tId2" }
        }
    ],
    "nextLink": "{originalRequestUrl}?$skipToken={opaqueString}"
}
```

## <a name="remove-a-tenant-mapping"></a>Entfernen einer Mandantenzuordnung

Sie können einen Mandanten entfernen, der einer Registrierung hinzugefügt wurde. Wenn dieser Mandant weiterhin Ressourcen in der Azure Stack-Instanz verwendet, wird deren Verwendung dem Abonnement in Rechnung gestellt, das zur anfänglichen Azure Stack-Registrierung verwendet wurde.

### <a name="parameters"></a>Parameter

| Parameter                  | BESCHREIBUNG          |
|---                         | ---                  |
| registrationSubscriptionID | Abonnement-ID für die Registrierung.   |
| resourceGroup              | Die Ressourcengruppe für die Registrierung.   |
| registrationName           | Der Name der Registrierung.  |
| customerSubscriptionID     | Die Kundenabonnement-ID.  |

### <a name="powershell"></a>PowerShell

Verwenden Sie das Cmdlet **Remove-AzureRmResource**, um einen Mandanten zu entfernen. [Stellen Sie eine Verbindung mit Azure Stack her](azure-stack-powershell-configure-admin.md), und führen Sie dann in einer Eingabeaufforderung mit erhöhten Rechten das folgende Cmdlet aus:

```powershell
Remove-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>API-Aufruf

Sie können Mandantenzuordnungen mit dem DELETE-Vorgang entfernen.

**Operation:** DELETE  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Antwort**: 204 Kein Inhalt  
**Antworttext**: Leer

## <a name="next-steps"></a>Nächste Schritte

- [Verbrauch und Abrechnung in Azure Stack](azure-stack-billing-and-chargeback.md)
