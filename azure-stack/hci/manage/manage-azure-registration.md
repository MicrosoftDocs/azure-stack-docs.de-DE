---
title: Verwalten der Azure Stack HCI-Clusterregistrierung mit Azure
description: Hier wird beschrieben, wie Sie Ihre Azure-Registrierung für Azure Stack HCI-Cluster verwalten, sich mit dem Registrierungsstatus vertraut machen und die Registrierung für einen Cluster aufheben, wenn Sie bereit für dessen Außerbetriebnahme sind.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 02/10/2021
ms.openlocfilehash: 7128ddae1a1b67e0085806ecd988f475c9bf8708
ms.sourcegitcommit: 5ea0e915f24c8bcddbcaf8268e3c963aa8877c9d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100487458"
---
# <a name="manage-cluster-registration-with-azure"></a>Verwalten der Clusterregistrierung mit Azure

> Gilt für Azure Stack HCI v20H2

Nachdem Sie einen Azure Stack HCI-Cluster erstellt haben, müssen Sie [Windows Admin Center](register-windows-admin-center.md) und anschließend den [Cluster bei Azure registrieren](../deploy/register-with-azure.md). Nach der Registrierung synchronisiert der Cluster regelmäßig Informationen zwischen dem lokalen Cluster und der Cloud. In diesem Thema wird erläutert, wie Ihr Registrierungsstatus zu verstehen ist, wie Sie Azure Active Directory-Berechtigungen gewähren und wie Sie die Registrierung Ihres Clusters aufheben, wenn Sie bereit sind, ihn außer Betrieb zu nehmen.

## <a name="understanding-registration-status-using-windows-admin-center"></a>Anzeigen der Informationen zum Registrierungsstatus per Windows Admin Center

Wenn Sie mit Windows Admin Center eine Verbindung mit einem Cluster herstellen, wird im Dashboard der Verbindungsstatus für Azure angezeigt. Der Status **Verbunden** bedeutet, dass der Cluster bereits bei Azure registriert ist und innerhalb des letzten Tags eine erfolgreiche Synchronisierung mit der Cloud durchgeführt wurde.

   :::image type="content" source="media/manage-azure-registration/registration-status.png" alt-text="Ständige Anzeige des Verbindungsstatus für den Cluster im Windows Admin Center-Dashboard" lightbox="media/manage-azure-registration/registration-status.png":::

Sie können weitere Informationen anzeigen, indem Sie auf der linken Seite ganz unten im Menü **Tools** die Option **Einstellungen** und dann **Azure Stack HCI-Registrierung** auswählen.

   :::image type="content" source="media/manage-azure-registration/azure-stack-hci-registration.png" alt-text="Weitere Informationen: „Einstellungen“ > „Tools“ > „Azure Stack HCI-Registrierung“" lightbox="media/manage-azure-registration/azure-stack-hci-registration.png":::

## <a name="understanding-registration-status-using-powershell"></a>Anzeigen der Informationen zum Registrierungsstatus per PowerShell

Verwenden Sie zum Ermitteln des Registrierungsstatus mit Windows PowerShell das PowerShell-Cmdlet `Get-AzureStackHCI` und die Eigenschaften `ClusterStatus`, `RegistrationStatus` und `ConnectionStatus`. Beispielsweise wird für die Eigenschaft `ClusterStatus` nach dem Installieren des Azure Stack HCI-Betriebssystems vor dem Erstellen eines Clusters oder dem Beitreten zu einem Cluster der Status „not yet“ angezeigt:

:::image type="content" source="media/manage-azure-registration/1-get-azurestackhci.png" alt-text="Azure-Registrierungsstatus vor der Clustererstellung":::

Nach dem Erstellen des Clusters wird nur für `RegistrationStatus` der Status „not yet“ angezeigt:

:::image type="content" source="media/manage-azure-registration/2-get-azurestackhci.png" alt-text="Azure-Registrierungsstatus nach der Clustererstellung":::

Azure Stack HCI muss gemäß den Bestimmungen für Azure-Onlinedienste innerhalb von 30 Tagen nach der Installation registriert werden. Wenn nach 30 Tagen keine Clustererstellung erfolgt ist, wird für `ClusterStatus` der Wert `OutOfPolicy` angezeigt, und wenn nach 30 Tagen keine Registrierung erfolgt ist, wird für `RegistrationStatus` `OutOfPolicy` angezeigt.

Nach der Registrierung des Clusters werden `ConnectionStatus` und die `LastConnected`-Uhrzeit angezeigt. Dieser Zeitpunkt liegt normalerweise innerhalb des letzten Tags, sofern die Internetverbindung für den Cluster nicht vorübergehend getrennt wurde. Ein Azure Stack HCI-Cluster kann bis zu 30 Tage in Folge uneingeschränkt offline betrieben werden.

:::image type="content" source="media/manage-azure-registration/3-get-azurestackhci.png" alt-text="Azure-Registrierungsstatus nach der Registrierung":::

Wenn dieser maximale Zeitraum überschritten wird, wird für `ConnectionStatus` `OutOfPolicy` angezeigt.

## <a name="azure-active-directory-app-permissions"></a>Azure Active Directory-App-Berechtigungen

Über das Erstellen einer Azure-Ressource in Ihrem Abonnement hinaus wird durch das Registrieren von Azure Stack HCI eine App-Identität in Ihrem Azure Active Directory-Mandanten erstellt, die konzeptionell einem Benutzer ähnlich ist. Die App-Identität erbt den Clusternamen. Diese Identität fungiert gegebenenfalls innerhalb Ihres Abonnements im Auftrag des Azure Stack HCI-Clouddiensts.

Wenn der Benutzer, der den Cluster registriert, ein Azure Active Directory-Administrator ist oder ihm ausreichende Berechtigungen erteilt wurden, geschieht all dies automatisch, und es ist kein weiteres Eingreifen erforderlich. Andernfalls ist möglicherweise die Genehmigung durch Ihren Azure Active Directory-Administrator erforderlich, um die Registrierung abzuschließen. Ihr Administrator kann wahlweise der App explizit seine Zustimmung erteilen oder Berechtigungen delegieren, so dass Sie der App Zustimmung erteilen können:

:::image type="content" source="media/manage-azure-registration/aad-permissions.png" alt-text="Diagramm zu Azure Active Directory-Berechtigungen und Identität" border="false":::

Öffnen Sie zum Erteilen Ihrer Zustimmung [portal.azure.com](https://portal.azure.com), und melden Sie sich mit einem Azure-Konto an, das über ausreichende Berechtigungen für Azure Active Directory verfügt. Navigieren Sie zu **Azure Active Directory** und dann zu **App-Registrierungen**. Wählen Sie die App-Identität aus, die nach Ihrem Cluster benannt ist, und navigieren Sie zu **API-Berechtigungen**.

Für das allgemein verfügbare Release von Azure Stack HCI benötigt die App die folgenden Berechtigungen, die sich von den App-Berechtigungen unterscheiden, die für die öffentliche Vorschau erforderlich waren:

```http
https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Cluster.Read

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Cluster.ReadWrite

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.ClusterNode.Read

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.ClusterNode.ReadWrite
```

App-Berechtigungen für die öffentliche Vorschau (jetzt veraltet):

```http
https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Census.Sync

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Billing.Sync
```

Das Einholen der Genehmigung bei Ihrem Azure Active Directory-Administrator kann einige Zeit in Anspruch nehmen, daher wird das Cmdlet `Register-AzStackHCI` beendet und hinterlässt die Registrierung im Status „pending admin consent“, also teilweise abgeschlossen. Nachdem die Zustimmung erteilt wurde, führen Sie einfach `Register-AzStackHCI` erneut aus, um die Registrierung abzuschließen.

## <a name="azure-active-directory-user-permissions"></a>Azure Active Directory-Benutzerberechtigungen

Der Benutzer, der Register-AzStackHCI ausführt, benötigt Azure AD-Berechtigungen für folgende Aufgaben:

- Azure AD-Anwendungen erstellen/abrufen/festlegen/entfernen (New/Get/Set/Remove-AzureADApplication)
- Azure AD-Dienstprinzipal erstellen/abrufen (New/Get-New-AzureADServicePrincipal)
- AD-Anwendungsgeheimnisse verwalten (New/Get/Remove-AzureADApplicationKeyCredential)
- Zustimmung zur Verwendung bestimmter Anwendungsberechtigungen erteilen (New/Get/Remove AzureADServiceAppRoleAssignments)

Hierzu haben Sie drei Möglichkeiten.

### <a name="option-1-allow-any-user-to-register-applications"></a>Option 1: Gestatten der Registrierung von Anwendungen durch alle Benutzer

Navigieren Sie in Azure Active Directory zu **Benutzereinstellungen > App-Registrierungen**. Wählen Sie unter **Benutzer können Anwendungen registrieren** die Option **Ja** aus.

Dadurch wird allen Benutzern das Registrieren von Anwendungen gestattet. Der Benutzer muss allerdings trotzdem die Clusterregistrierung vom Azure AD-Administrator genehmigen lassen. Beachten Sie, dass es sich hierbei um eine Einstellung auf Mandantenebene handelt, für große Unternehmenskunden ist dies daher möglicherweise nicht geeignet.

### <a name="option-2-assign-cloud-application-administration-role"></a>Option 2: Zuweisen der Verwaltungsrolle für die Cloudanwendung

Weisen Sie dem Benutzer die integrierte Azure AD-Rolle „Cloudanwendungsverwaltung“ zu. Auf diese Weise kann der Benutzer Cluster registrieren und die Registrierung aufheben, ohne dass zusätzlich die Zustimmung des AD-Administrators erforderlich ist.

### <a name="option-3-create-a-custom-ad-role-and-consent-policy"></a>Option 3: Erstellen einer benutzerdefinierten AD-Rolle und einer Zustimmungsrichtlinie

Die restriktivste Option besteht im Erstellen einer benutzerdefinierten AD-Rolle mit einer benutzerdefinierten Zustimmungsrichtlinie, die eine mandantenweite Administratoreinwilligung für die erforderlichen Berechtigungen an den Azure Stack HCI-Dienst delegiert. Wenn diese benutzerdefinierte Rolle zugewiesen ist, können Benutzer sich registrieren und die Zustimmung erteilen, ohne dass zusätzlich die Zustimmung des AD-Administrators erforderlich ist.

   > [!NOTE]
   > Diese Option erfordert eine Azure AD Premium-Lizenz und verwendet benutzerdefinierte AD-Rollen und Funktionen für benutzerdefinierte Zustimmungsrichtlinien, die sich derzeit in der öffentlichen Vorschauphase befinden.

   1. Stellen Sie eine Verbindung mit Azure AD her:
   
      ```powershell
      Connect-AzureAD
      ```

   2. Erstellen Sie eine benutzerdefinierte Zustimmungsrichtlinie:

      ```powershell
      New-AzureADMSPermissionGrantPolicy -Id "AzSHCI-registration-consent-policy" -DisplayName "Azure Stack HCI registration admin app consent policy" -Description "Azure Stack HCI registration admin app consent policy"
      ```

   3. Fügen Sie eine Bedingung hinzu, die erforderliche App-Berechtigungen für den Azure Stack HCI-Dienst mit der App-ID 1322e676-dee7-41ee-a874-ac923822781c enthält. Beachten Sie, dass die folgenden Berechtigungen für das allgemein verfügbare Release von Azure Stack HCI gelten und nicht mit der öffentlichen Vorschau funktionieren, es sei denn, Sie haben das [Vorschauupdate vom 23. November 2020 (KB4586852)](https://support.microsoft.com/help/4595086/azure-stack-hci-release-notes-overview) auf alle Server in Ihrem Cluster angewendet und die Version 0.4.1 oder höher des Az.StackHCI-Moduls heruntergeladen.
   
      ```powershell
      New-AzureADMSPermissionGrantConditionSet -PolicyId "AzSHCI-registration-consent-policy" -ConditionSetType "includes" -PermissionType "application" -ResourceApplication "1322e676-dee7-41ee-a874-ac923822781c" -Permissions "bbe8afc9-f3ba-4955-bb5f-1cfb6960b242","8fa5445e-80fb-4c71-a3b1-9a16a81a1966","493bd689-9082-40db-a506-11f40b68128f","2344a320-6a09-4530-bed7-c90485b5e5e2"
      ```

   4. Erteilen Sie Berechtigungen zum Zulassen der Registrierung von Azure Stack HCI, und notieren Sie sich die benutzerdefinierte Zustimmungsrichtlinie, die Sie in Schritt 2 erstellt haben:
   
      ```powershell
      $displayName = "Azure Stack HCI Registration Administrator "
      $description = "Custom AD role to allow registering Azure Stack HCI "
      $templateId = (New-Guid).Guid
      $allowedResourceAction =
      @(
             "microsoft.directory/applications/createAsOwner",
             "microsoft.directory/applications/delete",
             "microsoft.directory/applications/standard/read",
             "microsoft.directory/applications/credentials/update",
             "microsoft.directory/applications/permissions/update",
             "microsoft.directory/servicePrincipals/appRoleAssignedTo/update",
             "microsoft.directory/servicePrincipals/appRoleAssignedTo/read",
             "microsoft.directory/servicePrincipals/appRoleAssignments/read",
             "microsoft.directory/servicePrincipals/createAsOwner",
             "microsoft.directory/servicePrincipals/credentials/update",
             "microsoft.directory/servicePrincipals/permissions/update",
             "microsoft.directory/servicePrincipals/standard/read",
             "microsoft.directory/servicePrincipals/managePermissionGrantsForAll.AzSHCI-registration-consent-policy"
      )
      $rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}
      ```

   5. Erstellen Sie die neue benutzerdefinierte AD-Rolle:

      ```powershell
      $customADRole = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
      ```

   6. Weisen Sie die neue benutzerdefinierte AD-Rolle dem Benutzer zu, der den Azure Stack HCI-Cluster bei Azure registriert, indem Sie [diesen Anweisungen](/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal?context=/azure/active-directory/roles/context/ugr-context) folgen.

## <a name="unregister-azure-stack-hci-using-windows-admin-center"></a>Aufheben der Registrierung für Azure Stack HCI mit Windows Admin Center

Wenn Sie bereit für die Außerbetriebnahme Ihres Azure Stack HCI-Clusters sind, sollten Sie einfach mit Windows Admin Center eine Verbindung mit dem Cluster herstellen und auf der linken Seite ganz unten im Menü **Tools** die Option **Einstellungen** auswählen. Wählen Sie anschließend die Option **Azure Stack HCI-Registrierung** aus, und klicken Sie auf die Schaltfläche **Registrierung aufheben**. Beim Aufheben der Registrierung werden die Azure-Ressource, die den Cluster repräsentiert, die Azure-Ressourcengruppe (sofern die Gruppe während der Registrierung erstellt wurde und keine anderen Ressourcen enthält) und die Azure AD-App-Identität automatisch bereinigt. Dadurch werden alle über Azure Arc bereitgestellten Überwachungs-, Support-und Abrechnungsfunktionen beendet.

   > [!NOTE]
   > Das Aufheben der Registrierung eines Azure Stack HCI-Clusters muss von einem Azure Active Directory-Administrator oder einem anderen Benutzer, an den ausreichende Berechtigungen delegiert wurden, durchgeführt werden. Weitere Informationen finden Sie unter [Azure Active Directory-Benutzerberechtigungen](#azure-active-directory-user-permissions). Wenn Ihr Windows Admin Center-Gateway für eine andere Mandanten-ID von Azure Active Directory als diejenige registriert ist, die ursprünglich für die Registrierung des Clusters verwendet wurde, können Probleme auftreten, wenn Sie die Registrierung des Clusters über Windows Admin Center aufheben möchten. Befolgen Sie die untenstehenden PowerShell-Anweisungen, wenn dieser Fall eintritt.

## <a name="unregister-azure-stack-hci-using-powershell"></a>Aufheben der Registrierung für Azure Stack HCI mit PowerShell

Sie können auch das Cmdlet `Unregister-AzStackHCI` verwenden, um die Registrierung für einen Azure Stack HCI-Cluster aufzuheben. Sie können das Cmdlet entweder auf einem Clusterknoten oder einem Verwaltungs-PC ausführen.

Unter Umständen ist es erforderlich, dass Sie die aktuelle Version des `Az.StackHCI`-Moduls installieren. Unter Umständen wird die Aufforderung **Möchten Sie die Module aus „PSGallery“ installieren?** angezeigt. Antworten Sie mit **Ja**.

```PowerShell
Install-Module -Name Az.StackHCI
```

### <a name="unregister-from-a-cluster-node"></a>Aufheben der Registrierung über einen Clusterknoten

Wenn Sie das Cmdlet `Unregister-AzStackHCI` auf einem Server im Cluster ausführen, verwenden Sie die folgende Syntax und geben Ihre Azure-Abonnement-ID sowie den Ressourcennamen des Azure Stack HCI-Clusters an, dessen Registrierung Sie aufheben möchten:

```PowerShell
Unregister-AzStackHCI -SubscriptionId "e569b8af-6ecc-47fd-a7d5-2ac7f23d8bfe" -ResourceName HCI001
```

Sie werden aufgefordert, „microsoft.com/devicelogin“ auf einem anderen Gerät (etwa auf Ihrem PC oder Smartphone) aufzurufen, den Code einzugeben und sich dort für die Authentifizierung mit Azure anzumelden.

### <a name="unregister-from-a-management-pc"></a>Aufheben der Registrierung über einen Verwaltungs-PC

Wenn Sie das Cmdlet auf einem Verwaltungs-PC ausführen, müssen Sie zudem den Namen eines Servers im Cluster angeben:

```PowerShell
Unregister-AzStackHCI -ComputerName ClusterNode1 -SubscriptionId "e569b8af-6ecc-47fd-a7d5-2ac7f23d8bfe" -ResourceName HCI001
```

Ein interaktives Azure-Anmeldefenster wird angezeigt. Welche Aufforderungen genau angezeigt werden, unterscheidet sich je nach Ihren Sicherheitseinstellungen (z. B. zweistufige Authentifizierung). Folgen Sie den Anweisungen, um sich anzumelden.

## <a name="cleaning-up-after-a-cluster-that-was-not-properly-unregistered"></a>Durchführen der Bereinigung, nachdem die Registrierung für einen Cluster nicht korrekt aufgehoben wurde

Wenn ein Azure Stack HCI-Cluster von einem Benutzer zerstört wird, ohne die Registrierung aufzuheben, z. B. indem das Reimaging für die Hostserver durchgeführt wird oder Knoten des virtuellen Clusters gelöscht werden, sind in Azure noch Artefakte vorhanden. Diese Artefakte sind harmlos und verursachen keine Gebühren und keine Ressourcennutzung, aber sie können dazu beitragen, dass das Azure-Portal weniger übersichtlich ist. Zur Bereinigung können Sie sie manuell löschen.

Navigieren Sie im Azure-Portal zur Seite der Ressource, und wählen Sie oben in der Aktionsleiste die Option **Löschen** aus, um die Azure Stack HCI-Ressource zu löschen. Geben Sie den Namen der Ressource ein, um den Löschvorgang zu bestätigen, und wählen Sie dann **Löschen** aus. Navigieren Sie zum Löschen der Azure AD-App-Identität zu **Azure AD** > **App-Registrierungen**. Sie ist unter **Alle Anwendungen** angegeben. Wählen Sie **Löschen** aus, und bestätigen Sie den Vorgang.

Sie können die Azure Stack HCI-Ressource auch mithilfe von PowerShell löschen:

```PowerShell
Remove-AzResource -ResourceId "HCI001"
```

Möglicherweise müssen Sie das Modul `Az.Resources` installieren:

```PowerShell
Install-Module -Name Az.Resources
```

Wenn die Ressourcengruppe während der Registrierung erstellt wurde und keine weiteren Ressourcen enthält, können Sie sie auch löschen:

```PowerShell
Remove-AzResourceGroup -Name "HCI001-rg"
```

## <a name="next-steps"></a>Nächste Schritte

Verwandte Informationen finden Sie außerdem unter:

- [Registrieren von Windows Admin Center bei Azure](register-windows-admin-center.md)
- [Herstellen einer Verbindung von Azure Stack HCI mit Azure](../deploy/register-with-azure.md)