---
title: Herstellen einer Verbindung von Azure Stack HCI mit Azure
description: Hier erfahren Sie mehr über das Registrieren von Azure Stack HCI-Clustern mit Azure.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 12/28/2020
ms.openlocfilehash: de2ad8fecc2d79e8c8ff56e3a53a0769698a6fc1
ms.sourcegitcommit: 8790b8a4ecf4421409534df5ff510d537cc000da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/29/2020
ms.locfileid: "97801962"
---
# <a name="connect-azure-stack-hci-to-azure"></a>Herstellen einer Verbindung von Azure Stack HCI mit Azure

> Gilt für: Azure Stack HCI, v20H2

Azure Stack HCI wird als Azure-Dienst bereitgestellt und muss gemäß den Bestimmungen für Azure-Onlinedienste innerhalb von 30 Tagen nach der Installation registriert werden. In diesem Thema wird erläutert, wie Sie Ihren Azure Stack HCI-Cluster für Überwachung, Support, Abrechnung und Hybriddienste bei [Azure Arc](https://azure.microsoft.com/services/azure-arc/) registrieren. Bei der Registrierung wird eine Azure Resource Manager-Ressource erstellt, um jeden der lokalen Azure Stack HCI-Cluster zu repräsentieren, wodurch die Azure-Verwaltungsebene effektiv auf Azure Stack HCI ausgedehnt wird. Die Informationen werden regelmäßig zwischen der Azure-Ressource und dem oder den lokalen Cluster(n) synchronisiert.

   > [!IMPORTANT]
   > Die Registrierung bei Azure ist erforderlich, und Ihr Cluster wird erst vollständig unterstützt, nachdem Ihre Registrierung aktiviert wurde. Wenn Sie Ihren Cluster nicht innerhalb von 30 Tagen nach der Bereitstellung bei Azure registrieren oder wenn Ihr Cluster registriert wurde, aber dafür seit mehr als 30 Tagen keine Verbindung mit Azure hergestellt wurde, lässt das System die Erstellung oder Hinzufügung von neuen virtuellen Computern (VMs) nicht zu. Falls Sie dann versuchen, VMs zu erstellen, wird die folgende Fehlermeldung angezeigt:
   >
   > *Fehler beim Konfigurieren der Rolle „Virtueller Computer“ für „vmname“. Fehler beim Ausführen des Auftrags. Fehler beim Öffnen von „vmname“-Clusterrollen. Der Dienst, auf den zugegriffen wird, ist für eine bestimmte Anzahl von Verbindungen lizenziert. Zurzeit können keine weiteren Verbindungen hergestellt werden, da das Maximum schon erreicht wurde.*
   >
   > Die Lösung besteht darin, ausgehende Verbindungen mit Azure zuzulassen und sicherzustellen, dass Ihr Cluster wie in diesem Thema beschrieben registriert wurde.

## <a name="prerequisites-for-registration"></a>Voraussetzungen für die Registrierung

Sie können sich erst dann bei Azure registrieren, wenn Sie einen Azure Stack HCI-Cluster erstellt haben. Damit der Support für den Cluster in Anspruch genommen werden kann, muss es sich bei den Clusterknoten um physische Server handeln. Virtuelle Computer können zum Testen verwendet werden, sie müssen jedoch UEFI (Unified Extensible Firmware Interface) unterstützen, d. h., dass Sie keine virtuellen Computer der Hyper-V Generation 1 verwenden können. Die Azure Arc-Registrierung ist eine native Funktion des Azure Stack HCI-Betriebssystems, es ist daher kein Agent für die Registrierung erforderlich.

### <a name="internet-access"></a>Zugriff auf das Internet

Azure Stack HCI muss regelmäßig eine Verbindung mit der öffentlichen Azure-Cloud herstellen. Wenn die ausgehende Konnektivität durch die externe Unternehmensfirewall oder einen Proxyserver eingeschränkt ist, müssen diese so konfiguriert werden, dass sie ausgehenden Zugriff auf Port 443 (HTTPS) zu einer begrenzten Anzahl von bekannten Azure-IPs zulassen. Weitere Informationen zum Vorbereiten der Firewalls finden Sie unter [Konfigurieren von Firewalls für Azure Stack HCI](../concepts/configure-firewalls.md).

   > [!NOTE]
   > Beim Registrierungsprozess wird versucht, den PowerShell-Katalog zu kontaktieren, um sicherzustellen, dass Sie über die neueste Version der erforderlichen PowerShell-Module wie Az und AzureAD verfügen. Obwohl der PowerShell-Katalog in Azure gehostet wird, verfügt er derzeit nicht über ein Diensttag. Wenn Sie das Cmdlet oben nicht auf einem Verwaltungscomputer mit ausgehendem Internetzugriff ausführen können, sollten Sie die Module herunterladen und manuell auf einen Clusterknoten übertragen, auf dem Sie dann den Befehl `Register-AzStackHCI` ausführen. Sie haben auch die Möglichkeit, die [Module in einem Szenario ohne Verbindung zu installieren](/powershell/scripting/gallery/how-to/working-with-local-psrepositories?view=powershell-7.1#installing-powershellget-on-a-disconnected-system).

### <a name="azure-subscription-and-permissions"></a>Azure-Abonnement und -Berechtigungen

Wenn Sie noch kein Azure-Konto besitzen, [erstellen Sie eins](https://azure.microsoft.com/).

Sie können ein vorhandenes Abonnement beliebigen Typs verwenden:
- Kostenloses Konto mit Azure-Gutschriften [für Studenten](https://azure.microsoft.com/free/students/) oder [Visual Studio-Abonnenten](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)
- Abonnement mit [nutzungsbasierter Bezahlung](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) per Kreditkarte
- Durch einen Konzernvertrag (Enterprise Agreement, EA) erworbenes Abonnement
- Durch das CSP-Programm (Cloud Solution Provider, Cloudlösungsanbieter) erworbenes Abonnement

Der Benutzer, der den Cluster registriert, muss über Azure-Abonnementberechtigungen verfügen, um folgende Vorgänge ausführen zu können:

- Registrieren eines Ressourcenanbieters
- Erstellen/Abrufen/Löschen von Azure-Ressourcen und -Ressourcengruppen

Wenn Sie über ein EA- oder CSP-Azure-Abonnement verfügen, fordern Sie am einfachsten den Administrator Ihres Azure-Abonnements auf, dem Abonnement die integrierte Azure-Rolle „Besitzer“ oder „Mitwirkender“ zuzuweisen. Einige Administratoren bevorzugen jedoch möglicherweise eine restriktivere Option. In diesem Fall ist es möglich, eine benutzerdefinierte Azure-Rolle speziell für die Azure Stack HCI-Registrierung zu erstellen, indem Sie die folgenden Schritte ausführen:

1. Erstellen Sie eine JSON-Datei mit dem Namen **customHCIRole.json** und dem folgenden Inhalt: Dabei müssen Sie <subscriptionID> durch Ihre Azure-Abonnement-ID ersetzen. Um Ihre Abonnement-ID abzurufen, besuchen Sie [portal.azure.com](https://portal.azure.com), navigieren Sie zu „Abonnements“, kopieren Sie Ihre ID aus der Liste, und fügen Sie sie ein.

   ```json
   {
     "Name": "Azure Stack HCI registration role”,
     "Id": null,
     "IsCustom": true,
     "Description": "Custom Azure role to allow subscription-level access to register Azure Stack HCI",
     "Actions": [
       "Microsoft.Resources/subscriptions/resourceGroups/write",
       "Microsoft.Resources/subscriptions/resourceGroups/read",
       "Microsoft.Resources/subscriptions/resourceGroups/delete",
       "Microsoft.AzureStackHCI/register/action",
       "Microsoft.AzureStackHCI/Unregister/Action",
       "Microsoft.AzureStackHCI/clusters/*"
     ],
     "NotActions": [
     ],
   "AssignableScopes": [
       "/subscriptions/<subscriptionId>"
     ]
   }
   ```

2. Erstellen Sie die benutzerdefinierte Rolle:

   ```powershell
   New-AzRoleDefinition -InputFile <path to customHCIRole.json>
   ```

3. Weisen Sie die benutzerdefinierte Rolle dem Benutzer zu:

   ```powershell
   $user = get-AzAdUser -DisplayName <userdisplayname>
   $role = Get-AzRoleDefinition -Name "Azure Stack HCI registration role"
   New-AzRoleAssignment -ObjectId $user.Id -RoleDefinitionId $role.Id -Scope /subscriptions/<subscriptionid>
   ```

### <a name="azure-active-directory-permissions"></a>Azure Active Directory-Berechtigungen

Sie benötigen außerdem entsprechende Azure Active Directory-Berechtigungen, um den Registrierungsprozess ausführen zu können. Wenn Sie diese noch nicht besitzen, bitten Sie Ihren Azure AD-Administrator, seine Zustimmung zu geben oder die Berechtigungen an Sie zu delegieren. Weitere Informationen finden Sie unter [Verwalten von Azure-Registrierungen](../manage/manage-azure-registration.md#azure-active-directory-app-permissions).

## <a name="register-using-powershell"></a>Registrieren mithilfe von PowerShell

Verwenden Sie das folgende Verfahren, um einen Azure Stack HCI-Cluster mit einem Verwaltungs-PC bei Azure zu registrieren:

1. Installieren Sie die erforderlichen Cmdlets auf Ihrem Verwaltungs-PC. Wenn Sie einen Cluster registrieren, der mit dem aktuellen Image für die allgemein verfügbare Version der Azure Stack HCI bereitgestellt wurde, führen Sie einfach den folgenden Befehl aus. Wenn Ihr Cluster mit dem Image für die Public Preview bereitgestellt wurde, stellen Sie sicher, dass Sie das Update vom 23. November 2020 (KB4586852) auf jeden Server im Cluster angewendet haben, bevor Sie versuchen, die Registrierung mit Azure auszuführen.

   ```PowerShell
   Install-Module -Name Az.StackHCI
   ```

   > [!NOTE]
   > - Möglicherweise wird eine Aufforderung wie „Soll PowerShellGet jetzt den NuGet-Anbieter installieren und importieren?“ angezeigt, die Sie mit Ja (J) beantworten sollten.
   > - Außerdem werden Sie möglicherweise gefragt „Möchten Sie die Module aus ‚PSGallery‘ installieren?“, was Sie ebenfalls mit Ja (J) beantworten sollten.

2. Führen Sie die Registrierung mit dem Namen eines beliebigen Servers im Cluster aus. Um Ihre Azure-Abonnement-ID abzurufen, besuchen Sie [portal.azure.com](https://portal.azure.com), navigieren Sie zu „Abonnements“, kopieren Sie Ihre ID aus der Liste, und fügen Sie sie ein.

   ```PowerShell
   Register-AzStackHCI  -SubscriptionId "<subscription_ID>" -ComputerName Server1 [–Credential] [-ResourceName] [-ResourceGroupName] [-Region]
   ```

   Mit dieser Syntax wird der Cluster (dem Server1 angehört) für den aktuellen Benutzer bei der standardmäßigen Azure-Region und -Cloudumgebung registriert. Dabei werden für die Azure-Ressourcen und die Azure-Ressourcengruppe intelligente Standardnamen verwendet, Sie können diesem Befehl jedoch Parameter hinzufügen, um diese Werte bei Bedarf selbst anzugeben.

   Beachten Sie, dass der Benutzer, der das Cmdlet `Register-AzStackHCI` ausführt, über [Azure Active Directory-Berechtigungen](../manage/manage-azure-registration.md#azure-active-directory-app-permissions) verfügen muss, sonst wird der Registrierungsvorgang nicht abgeschlossen, sondern beendet, und der Registrierung fehlt die Administratoreinwilligung. Nachdem die Berechtigung gewährt wurde, führen Sie einfach `Register-AzStackHCI` erneut aus, um die Registrierung abzuschließen.

3. Authentifizieren über Azure

   Um den Registrierungsprozess abzuschließen, müssen Sie sich mit Ihrem Azure-Konto authentifizieren (anmelden). Ihr Konto muss Zugriff auf das oben in Schritt 4 angegebene Azure-Abonnement besitzen, damit die Registrierung fortgesetzt werden kann. Kopieren Sie den mitgelieferten Code, navigieren Sie auf einem anderen Gerät (z. B. Ihrem PC oder Telefon) zu „microsoft.com/devicelogin“, geben Sie den Code ein, und melden Sie sich dort an. Dies ist das gleiche Verfahren, das Microsoft für andere Geräte mit eingeschränkten Eingabemodi verwendet, wie etwa Xbox.

Der Registrierungsworkflow erkennt, wenn Sie sich angemeldet haben, und wird bis zum Abschluss fortgesetzt. Anschließend sollten Sie Ihren Cluster im Portal sehen können.

## <a name="next-steps"></a>Nächste Schritte

Sie sind nun zu diesen Schritten bereit:

- [Überprüfen des Clusters](validate.md)

