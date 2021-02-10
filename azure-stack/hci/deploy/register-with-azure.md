---
title: Herstellen einer Verbindung von Azure Stack HCI mit Azure
description: Hier erfahren Sie mehr über das Registrieren von Azure Stack HCI-Clustern mit Azure.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 01/28/2020
ms.openlocfilehash: 17e8758dfea300f6bc3e02609877dfed8f780383
ms.sourcegitcommit: b461597917b768412036bf852c911aa9871264b2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99050024"
---
# <a name="connect-azure-stack-hci-to-azure"></a>Herstellen einer Verbindung von Azure Stack HCI mit Azure

> Gilt für: Azure Stack HCI, v20H2

Azure Stack HCI wird als Azure-Dienst bereitgestellt und muss gemäß den Bestimmungen für Azure-Onlinedienste innerhalb von 30 Tagen nach der Installation registriert werden. In diesem Thema wird erläutert, wie Sie Ihren Azure Stack HCI-Cluster für Überwachung, Support, Abrechnung und Hybriddienste bei [Azure Arc](https://azure.microsoft.com/services/azure-arc/) registrieren. Bei der Registrierung wird eine Azure Resource Manager-Ressource erstellt, um jeden der lokalen Azure Stack HCI-Cluster zu repräsentieren, wodurch die Azure-Verwaltungsebene effektiv auf Azure Stack HCI ausgedehnt wird. Die Informationen werden regelmäßig zwischen der Azure-Ressource und dem oder den lokalen Cluster(n) synchronisiert. Die Azure Arc-Registrierung ist eine native Funktion des Azure Stack HCI-Betriebssystems, es ist daher kein Agent für die Registrierung erforderlich.

   > [!IMPORTANT]
   > Die Registrierung bei Azure ist erforderlich, und Ihr Cluster wird erst vollständig unterstützt, nachdem Ihre Registrierung aktiviert wurde. Wenn Sie Ihren Cluster nicht direkt nach der Bereitstellung bei Azure registrieren oder wenn Ihr Cluster registriert wurde, aber dafür seit mehr als 30 Tagen keine Verbindung mit Azure hergestellt wurde, lässt das System die Erstellung oder Hinzufügung von neuen virtuellen Computern (VMs) nicht zu. Falls Sie dann versuchen, VMs zu erstellen, wird die folgende Fehlermeldung angezeigt:
   >
   > *Fehler beim Konfigurieren der Rolle „Virtueller Computer“ für „vmname“. Fehler beim Ausführen des Auftrags. Fehler beim Öffnen von „vmname“-Clusterrollen. Der Dienst, auf den zugegriffen wird, ist für eine bestimmte Anzahl von Verbindungen lizenziert. Zurzeit können keine weiteren Verbindungen hergestellt werden, da das Maximum schon erreicht wurde.*
   >
   > Die Lösung besteht darin, ausgehende Verbindungen mit Azure zuzulassen und sicherzustellen, dass Ihr Cluster wie in diesem Thema beschrieben registriert wurde.

## <a name="prerequisites-for-registration"></a>Voraussetzungen für die Registrierung

Sie können sich erst dann bei Azure registrieren, wenn Sie einen Azure Stack HCI-Cluster erstellt haben. Damit der Support für den Cluster in Anspruch genommen werden kann, muss es sich bei den Clusterknoten um physische Server handeln. Virtuelle Computer können zum Testen verwendet werden, sie müssen jedoch UEFI (Unified Extensible Firmware Interface) unterstützen, d. h., dass Sie keine virtuellen Computer der Hyper-V Generation 1 verwenden können.

Am einfachsten ist die Registrierung möglich, indem ein Azure AD-Administrator diese per Windows Admin Center oder mit PowerShell durchführt.

   > [!IMPORTANT]
   > Falls Sie für die Registrierung des Clusters Windows Admin Center nutzen möchten, müssen Sie zunächst [Ihr Windows Admin Center-Gateway für Azure registrieren](../manage/register-windows-admin-center.md). Verwenden Sie hierfür die gleiche Azure-Abonnement-ID und -Mandanten-ID, die Sie auch für die Clusterregistrierung nutzen möchten.

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

## <a name="register-a-cluster-using-windows-admin-center"></a>Registrieren eines Clusters mit Windows Admin Center

Die einfachste Möglichkeit zum Registrieren Ihres Azure Stack HCI-Clusters ist die Verwendung von Windows Admin Center. Beachten Sie hierbei Folgendes: Der Benutzer muss über [Azure Active Directory-Berechtigungen](../manage/manage-azure-registration.md#azure-active-directory-app-permissions) verfügen, da der Registrierungsvorgang sonst nicht abgeschlossen werden kann. Stattdessen wird der Vorgang beendet, und die Genehmigung der Registrierung durch den Administrator ist ausstehend.

1. Vor Beginn des Registrierungsvorgangs müssen Sie zunächst [Ihr Windows Admin Center-Gateway für Azure registrieren](../manage/register-windows-admin-center.md), falls Sie dies nicht bereits getan haben.

   > [!IMPORTANT]
   > Beim Registrieren von Windows Admin Center für Azure ist es wichtig, dass Sie die gleiche Azure-Abonnement-ID und -Mandanten-ID verwenden, die Sie auch für die Registrierung des eigentlichen Clusters nutzen möchten. Eine Azure AD-Mandanten-ID steht für eine bestimmte Instanz von Azure AD mit Konten und Gruppen, während eine Azure-Abonnement-ID für eine Vereinbarung zur Nutzung von Azure-Ressourcen steht, für die Gebühren berechnet werden. Sie können Ihre Mandanten-ID ermitteln, indem Sie unter [portal.azure.com](https://portal.azure.com) die Option **Azure Active Directory** auswählen. Ihre Mandanten-ID wird unter **Mandanteninformationen** angezeigt. Navigieren Sie zum Ermitteln Ihrer Azure-Abonnement-ID zu **Abonnements**, und kopieren Sie sie aus der Liste.

2. Öffnen Sie Windows Admin Center, und wählen Sie ganz unten im Menü **Tools** auf der linken Seite die Option **Einstellungen** aus. Wählen Sie anschließend unten im Menü **Einstellungen** die Option **Azure Stack HCI-Registrierung** aus. Falls Ihr Cluster noch nicht für Azure registriert wurde, wird unter **Registrierungsstatus** der Status **Nicht registriert** angezeigt. Klicken Sie auf die Schaltfläche **Registrieren**, um fortzufahren.

3. Um den Registrierungsprozess abzuschließen, müssen Sie sich mit Ihrem Azure-Konto authentifizieren (anmelden). Damit die Registrierung durchgeführt werden kann, muss Ihr Konto über Zugriff auf das Azure-Abonnement verfügen, das Sie beim Registrieren Ihres Windows Admin Center-Gateways angegeben haben. Kopieren Sie den mitgelieferten Code, navigieren Sie auf einem anderen Gerät (z. B. Ihrem PC oder Telefon) zu „microsoft.com/devicelogin“, geben Sie den Code ein, und melden Sie sich dort an. Der Registrierungsworkflow erkennt, wenn Sie sich angemeldet haben, und wird bis zum Abschluss fortgesetzt. Anschließend sollten Sie Ihren Cluster im Portal sehen können.

## <a name="register-a-cluster-using-powershell"></a>Registrieren eines Clusters mit PowerShell

Verwenden Sie das folgende Verfahren, um einen Azure Stack HCI-Cluster mit einem Verwaltungs-PC bei Azure zu registrieren:

1. Installieren Sie die erforderlichen Cmdlets auf Ihrem Verwaltungs-PC. Wenn Sie einen Cluster registrieren, der mit dem aktuellen Image für die allgemein verfügbare Version der Azure Stack HCI bereitgestellt wurde, führen Sie einfach den folgenden Befehl aus. Wenn Ihr Cluster mit dem Image für die Public Preview bereitgestellt wurde, stellen Sie sicher, dass Sie das Update vom 23. November 2020 (KB4586852) auf jeden Server im Cluster angewendet haben, bevor Sie versuchen, die Registrierung mit Azure auszuführen.

   ```PowerShell
   Install-Module -Name Az.StackHCI
   ```

   > [!NOTE]
   > - Unter Umständen wird eine Aufforderung wie **Soll PowerShellGet jetzt den NuGet-Anbieter installieren und importieren?** angezeigt. Antworten Sie mit **Ja**.
   > - Die nächste Aufforderung lautet ggf. **Möchten Sie die Module aus „PSGallery“ installieren?** . Antworten Sie mit **Ja**.

2. Führen Sie die Registrierung mit dem Namen eines beliebigen Servers im Cluster aus. Um Ihre Azure-Abonnement-ID abzurufen, greifen Sie auf [portal.azure.com](https://portal.azure.com) zu, navigieren zu **Abonnements** und kopieren Ihre ID aus der Liste.

   ```PowerShell
   Register-AzStackHCI  -SubscriptionId "<subscription_ID>" -ComputerName Server1
   ```

   Mit dieser Syntax wird der Cluster (dem Server1 angehört) für den aktuellen Benutzer bei der standardmäßigen Azure-Region und -Cloudumgebung registriert. Hierbei werden für die Azure-Ressourcen und die Azure-Ressourcengruppe intelligente Standardnamen verwendet. Sie können auch die optionalen Parameter `-Region`, `-ResourceName` und `-ResourceGroupName` an diesen Befehl anfügen, um diese Werte anzugeben.

   Beachten Sie, dass der Benutzer, der das Cmdlet `Register-AzStackHCI` ausführt, über [Azure Active Directory-Berechtigungen](../manage/manage-azure-registration.md#azure-active-directory-app-permissions) verfügen muss. Ansonsten wird der Registrierungsvorgang nicht abgeschlossen, sondern beendet, und für die Registrierung fehlt die Genehmigung durch den Administrator. Nachdem die Berechtigung gewährt wurde, führen Sie einfach `Register-AzStackHCI` erneut aus, um die Registrierung abzuschließen.

3. Authentifizieren über Azure

   Um den Registrierungsprozess abzuschließen, müssen Sie sich mit Ihrem Azure-Konto authentifizieren (anmelden). Ihr Konto muss Zugriff auf das oben in Schritt 2 angegebene Azure-Abonnement besitzen, damit die Registrierung fortgesetzt werden kann. Kopieren Sie den mitgelieferten Code, navigieren Sie auf einem anderen Gerät (z. B. Ihrem PC oder Telefon) zu „microsoft.com/devicelogin“, geben Sie den Code ein, und melden Sie sich dort an. Der Registrierungsworkflow erkennt, wenn Sie sich angemeldet haben, und wird bis zum Abschluss fortgesetzt. Anschließend sollten Sie Ihren Cluster im Portal sehen können.

## <a name="next-steps"></a>Nächste Schritte

Sie sind nun zu diesen Schritten bereit:

- [Überprüfen des Clusters](validate.md)

