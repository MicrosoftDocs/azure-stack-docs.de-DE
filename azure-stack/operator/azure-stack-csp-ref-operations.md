---
title: Registrieren von Mandanten für die Nutzungsnachverfolgung in Azure Stack Hub
description: Hier erfahren Sie, wie Sie in Azure Stack Hub Mandanten registrieren und die Mandantennutzung nachverfolgen.
author: sethmanheim
ms.topic: article
ms.date: 11/17/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 11/17/2020
ms.openlocfilehash: 990ac200fdf36e951dbee9d2fed96f4918a9a8bd
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95517802"
---
# <a name="register-tenants-for-usage-tracking-in-azure-stack-hub"></a>Registrieren von Mandanten für die Nutzungsnachverfolgung in Azure Stack Hub

Dieser Artikel enthält Details zu Registrierungsvorgängen. Sie können diese Vorgänge für die folgenden Aufgaben verwenden:

- Verwalten von Mandantenregistrierungen.
- Verwalten der Nutzungsnachverfolgung von Mandanten.

## <a name="add-tenant-to-registration"></a>Hinzufügen von Mandanten zur Registrierung

Sie können diesen Vorgang verwenden, wenn Sie Ihrer Registrierung einen neuen Mandanten hinzufügen möchten. Die Mandantennutzung wird unter einem Azure-Abonnement gemeldet, das mit dem Azure AD-Mandanten (Azure Active Directory) verbunden ist.

Sie können den Vorgang auch verwenden, um das einem Mandanten zugeordnete Abonnement zu ändern. Rufen Sie PUT oder das PowerShell-Cmdlet **New-AzResource** auf, um die vorherige Zuordnung zu überschreiben. Wenn Sie das AzureRM-Modul von PowerShell verwenden, verwenden Sie das PowerShell-Cmdlet **New-AzureResource**.

Sie können einem Mandanten ein einzelnes Azure-Abonnement zuweisen. Wenn Sie versuchen, einem vorhandenen Mandanten ein zweites Abonnement hinzuzufügen, wird das erste Abonnement überschrieben.

### <a name="use-api-profiles"></a>Verwenden von API-Profilen

Die folgenden Registrierungs-Cmdlets erfordern, dass Sie beim Ausführen von PowerShell ein API-Profil angeben. API-Profile stellen mehrere Azure-Ressourcenanbieter und ihre API-Versionen dar. Sie unterstützen Sie dabei, bei der Interaktion mit mehreren Azure-Clouds die richtige Version der API zu verwenden. Wenn Sie z. B. im Zusammenhang mit globalen Azure- und Azure Stack Hub-Instanzen mit mehreren Clouds arbeiten, geben API-Profile einen Namen an, der mit ihrem Veröffentlichungsdatum übereinstimmt. Sie verwenden das Profil **2017-09-03**.

Weitere Informationen zu Azure Stack Hub und API-Profilen finden Sie unter [Verwalten von API-Versionsprofilen in Azure Stack Hub](../user/azure-stack-version-profiles.md).

### <a name="parameters"></a>Parameter

| Parameter                  | BESCHREIBUNG |
|---                         | --- |
| registrationSubscriptionID | Das Azure-Abonnement, das für die anfängliche Registrierung verwendet wurde. |
| customerSubscriptionID     | Das Azure-Abonnement (nicht Azure Stack Hub), das zu dem Kunden gehört, der registriert werden soll. Muss im CSP-Angebot (Cloud Solution Provider) über Partner Center erstellt werden. Wenn ein Kunde über mehrere Mandanten verfügt, erstellen Sie für den Mandanten ein Abonnement zum Anmelden bei Azure Stack Hub. Bei der Kundenabonnement-ID wird zwischen Groß-/Kleinschreibung unterschieden. |
| resourceGroup              | Die Ressourcengruppe in Azure, in dem Ihre Registrierung gespeichert ist. |
| registrationName           | Der Name der Registrierung Ihrer Azure Stack Hub-Instanz. Dies ist ein in Azure gespeichertes Objekt. Der Name hat in der Regel die Form **azurestack-CloudID**, wobei **CloudID** die Cloud-ID Ihrer Azure Stack Hub-Bereitstellung ist. |

> [!NOTE]  
> Mandanten müssen bei jeder Azure Stack Hub-Bereitstellung registriert werden, die sie verwenden. Wenn ein Mandant mehrere Azure Stack Hub-Instanzen verwendet, aktualisieren Sie die anfängliche Registrierung jeder Bereitstellung mit dem Mandantenabonnement.


### <a name="powershell"></a>PowerShell
### <a name="az-modules"></a>[Az-Module](#tab/az1)

Verwenden Sie das Cmdlet **New-AzResource**, um einen Mandanten hinzuzufügen. [Stellen Sie eine Verbindung mit Azure her](/powershell/azure/get-started-azureps), und führen Sie dann in einer Eingabeaufforderung mit erhöhten Rechten den folgenden Befehl aus:

```powershell  
New-AzResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```
### <a name="azurerm-modules"></a>[AzureRM-Module](#tab/azurerm1)

Verwenden Sie das Cmdlet **New-AzureRmResource**, um einen Mandanten hinzuzufügen. [Stellen Sie eine Verbindung mit Azure her](/powershell/azure/get-started-azureps), und führen Sie dann in einer Eingabeaufforderung mit erhöhten Rechten den folgenden Befehl aus:

```powershell  
New-AzResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

---
### <a name="api-call"></a>API-Aufruf

**Operation:** PUT  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
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
| registrationName           | Der Name der Registrierung Ihrer Azure Stack Hub-Bereitstellung. Dies ist ein in Azure gespeichertes Objekt. Der Name hat in der Regel die Form **azurestack-CloudID**, wobei **CloudID** die Cloud-ID Ihrer Azure Stack Hub-Bereitstellung ist.   |

### <a name="powershell"></a>PowerShell

### <a name="az-modules"></a>[Az-Module](#tab/az2)

Verwenden Sie das Cmdlet **Get-AzResource**, um alle registrierten Mandanten aufzulisten. [Stellen Sie eine Verbindung mit Azure Stack Hub her](azure-stack-powershell-configure-admin.md), und führen Sie dann in einer Eingabeaufforderung mit erhöhten Rechten das folgende Cmdlet aus:

```powershell
Get-AzResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions" -ApiVersion 2017-06-01
```

### <a name="azurerm-modules"></a>[AzureRM-Module](#tab/azurerm2)

Verwenden Sie das Cmdlet **Get-AzureRMResource**, um alle registrierten Mandanten aufzulisten. [Stellen Sie eine Verbindung mit Azure Stack Hub her](azure-stack-powershell-configure-admin.md), und führen Sie dann in einer Eingabeaufforderung mit erhöhten Rechten das folgende Cmdlet aus:

```powershell
Get-AzureRMResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions" -ApiVersion 2017-06-01
```

---
### <a name="api-call"></a>API-Aufruf

Sie können eine Liste aller Mandantenzuordnungen mithilfe des GET-Vorgangs abrufen.

**Operation:** GET  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions?api-version=2017-06-01 HTTP/1.1`  
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

Sie können einen Mandanten entfernen, der einer Registrierung hinzugefügt wurde. Wenn dieser Mandant weiterhin Ressourcen in Azure Stack Hub verwendet, wird deren Nutzung dem Abonnement in Rechnung gestellt, das zur anfänglichen Azure Stack Hub-Registrierung verwendet wurde.

### <a name="parameters"></a>Parameter

| Parameter                  | BESCHREIBUNG          |
|---                         | ---                  |
| registrationSubscriptionID | Abonnement-ID für die Registrierung.   |
| resourceGroup              | Die Ressourcengruppe für die Registrierung.   |
| registrationName           | Der Name der Registrierung.  |
| customerSubscriptionID     | Die Kundenabonnement-ID. Bei der Kundenabonnement-ID wird zwischen Groß-/Kleinschreibung unterschieden.  |

### <a name="powershell"></a>PowerShell

### <a name="az-modules"></a>[Az-Module](#tab/az3)

Verwenden Sie das Cmdlet **Remove-AzResource**, um einen Mandanten zu entfernen. [Stellen Sie eine Verbindung mit Azure Stack Hub her](azure-stack-powershell-configure-admin.md), und führen Sie dann in einer Eingabeaufforderung mit erhöhten Rechten das folgende Cmdlet aus:

```powershell
Remove-AzResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="azurerm-modules"></a>[AzureRM-Module](#tab/azurerm3)

Verwenden Sie das Cmdlet **Remove-AzureRMResource**, um einen Mandanten zu entfernen. [Stellen Sie eine Verbindung mit Azure Stack Hub her](azure-stack-powershell-configure-admin.md), und führen Sie dann in einer Eingabeaufforderung mit erhöhten Rechten das folgende Cmdlet aus:

```powershell
Remove-AzureRMResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

---

### <a name="api-call"></a>API-Aufruf

Sie können Mandantenzuordnungen mit dem DELETE-Vorgang entfernen.

**Operation:** Delete  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Antwort**: 204 Kein Inhalt  
**Antworttext**: Leer

## <a name="next-steps"></a>Nächste Schritte

- [Abrufen von Informationen zum Ressourcenverbrauch in Azure Stack Hub](azure-stack-billing-and-chargeback.md)
