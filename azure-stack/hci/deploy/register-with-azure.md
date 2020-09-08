---
title: Herstellen einer Verbindung von Azure Stack HCI mit Azure
description: Registrieren von Azure Stack HCI bei Azure.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 08/12/2020
ms.openlocfilehash: aeed44bdc36036272e223cbc1cb740396ce1413e
ms.sourcegitcommit: 750d8682261ac1a3c47fbeb331ac0b4e6f76a03e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2020
ms.locfileid: "89219300"
---
# <a name="connect-azure-stack-hci-to-azure"></a>Herstellen einer Verbindung von Azure Stack HCI mit Azure

> Gilt für Azure Stack HCI v20H2; Windows Server 2019

Azure Stack HCI wird als Azure-Dienst bereitgestellt und muss gemäß den Bestimmungen für Azure-Onlinedienste innerhalb von 30 Tagen nach der Installation registriert werden. In diesem Thema wird erläutert, wie Sie Ihren Azure Stack HCI-Cluster für Überwachung, Support, Abrechnung und Hybriddienste bei [Azure Arc](https://azure.microsoft.com/services/azure-arc/) registrieren. Bei der Registrierung wird eine Azure Resource Manager-Ressource erstellt, um jeden der lokalen Azure Stack HCI-Cluster zu repräsentieren, wodurch die Azure-Verwaltungsebene effektiv auf Azure Stack HCI ausgedehnt wird. Die Informationen werden regelmäßig zwischen der Azure-Ressource und dem lokalen Cluster synchronisiert. 

## <a name="prerequisites-for-registration"></a>Voraussetzungen für die Registrierung

Sie können sich erst dann bei Azure registrieren, wenn Sie einen Azure Stack HCI-Cluster erstellt haben. Bei den Knoten kann es sich um physische oder um virtuelle Computer handeln, sie müssen aber über UEFI (Unified Extensible Firmware Interface) verfügen, was bedeutet, dass Sie keine virtuellen Computer der Hyper-V Generation 1 verwenden können. Die Azure Arc-Registrierung ist eine native Funktion von Azure Stack HCI, es ist daher kein Agent erforderlich.

### <a name="internet-access"></a>Zugriff auf das Internet

Die Azure Stack HCI-Knoten benötigen Cloudkonnektivität, um eine Verbindung mit Azure herstellen zu können. Beispielsweise sollte ein ausgehender Ping erfolgreich sein:

```PowerShell
C:\> ping bing.com
```

### <a name="azure-subscription"></a>Azure-Abonnement

Wenn Sie noch kein Azure-Konto besitzen, [erstellen Sie eins](https://azure.microsoft.com/). 

Sie können ein vorhandenes Abonnement beliebigen Typs verwenden:
- Kostenloses Konto mit Azure-Gutschriften [für Studenten](https://azure.microsoft.com/free/students/) oder [Visual Studio-Abonnenten](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)
- Abonnement mit [nutzungsbasierter Bezahlung](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) per Kreditkarte
- Durch einen Konzernvertrag (Enterprise Agreement, EA) erworbenes Abonnement
- Durch das CSP-Programm (Cloud Solution Provider, Cloudlösungsanbieter) erworbenes Abonnement

### <a name="azure-active-directory-permissions"></a>Azure Active Directory-Berechtigungen

Sie benötigen Azure Active Directory-Berechtigungen, um den Registrierungsprozess abzuschließen. Wenn Sie diese noch nicht besitzen, bitten Sie Ihren Azure AD-Administrator, Ihnen Berechtigungen zu erteilen oder sie an Sie zu delegieren. Weitere Informationen finden Sie unter [Verwalten von Azure-Registrierungen](../manage/manage-azure-registration.md#azure-active-directory-permissions).

## <a name="register-using-powershell"></a>Registrieren mithilfe von PowerShell

Verwenden Sie das folgende Verfahren, um einen Azure Stack HCI-Cluster mit Azure zu registrieren:

1. Stellen Sie eine Verbindung mit einem der Clusterknoten her, indem Sie eine PowerShell-Sitzung öffnen und den folgenden Befehl eingeben:

   ```PowerShell
   Enter-PSSession <server-name>
   ```

2. Installieren Sie das PowerShell-Modul für Azure Stack HCI:

   ```PowerShell
   Install-WindowsFeature RSAT-Azure-Stack-HCI
   ```

3. Installieren Sie die erforderlichen Cmdlets:

   ```PowerShell
   Install-Module Az.StackHCI
   ```

   > [!NOTE]
   > 1. Möglicherweise wird eine Aufforderung wie „Soll PowerShellGet jetzt den NuGet-Anbieter installieren und importieren?“ angezeigt, die Sie mit Ja (J) beantworten sollten.
   > 2. Außerdem werden Sie möglicherweise gefragt „Möchten Sie die Module aus ‚PSGallery‘ installieren?“, was Sie ebenfalls mit Ja (J) beantworten sollten.
   > 3. Schließlich könnten Sie annehmen, dass beim Installieren des gesamten **Az**-Moduls das Untermodul **StackHCI** enthalten ist, und langfristig betrachtet wird das auch der Fall sein. Der Standardkonvention von Azure PowerShell folgend, sind Untermodule in der Vorschauphase jedoch nicht automatisch enthalten; Sie müssen sie vielmehr explizit angeben. Daher müssen Sie zurzeit **Az.StackHCI** explizit anfordern, wie oben dargestellt.

4. Führen Sie die eigentliche Registrierung durch:

   ```PowerShell
   Register-AzStackHCI  -SubscriptionId "<subscription_ID>" [-ResourceName] [-ResourceGroupName]
   ```

   Diese Syntax registriert den lokalen Cluster (dem der lokale Server angehört) für den aktuellen Benutzer bei der standardmäßigen Azure-Region und -Cloudumgebung. Dabei werden für die Azure-Ressourcen und die Azure-Ressourcengruppe intelligente Standardnamen verwendet, aber Sie können diesem Befehl Parameter hinzufügen, um diese Werte bei Bedarf anzugeben.

   Beachten Sie, dass der Benutzer, der das Cmdlet `Register-AzStackHCI` ausführt, über [Azure Active Directory-Berechtigungen](../manage/manage-azure-registration.md#azure-active-directory-permissions) verfügen muss, sonst wird der Registrierungsvorgang nicht abgeschlossen, sondern beendet, und der Registrierung fehlt die Administratoreinwilligung. Nachdem die Berechtigung gewährt wurde, führen Sie einfach `Register-AzStackHCI` erneut aus, um die Registrierung abzuschließen.

5. Authentifizieren über Azure

   Um den Registrierungsprozess abzuschließen, müssen Sie sich mit Ihrem Azure-Konto authentifizieren (anmelden). Ihr Konto muss Zugriff auf das oben in Schritt 4 angegebene Azure-Abonnement besitzen, damit die Registrierung fortgesetzt werden kann. Kopieren Sie den mitgelieferten Code, navigieren Sie auf einem anderen Gerät (z. B. Ihrem PC oder Telefon) zu „microsoft.com/devicelogin“, geben Sie den Code ein, und melden Sie sich dort an. Dies ist das gleiche Verfahren, das Microsoft für andere Geräte mit eingeschränkten Eingabemodi verwendet, wie etwa Xbox.

Der Registrierungsworkflow erkennt, wenn Sie sich angemeldet haben, und wird bis zum Abschluss fortgesetzt. Anschließend sollten Sie Ihren Cluster im Portal sehen können.

## <a name="next-steps"></a>Nächste Schritte

Sie sind nun zu diesen Schritten bereit:

- [Überprüfen des Clusters](validate.md)
- [Erstellen von Volumes](../manage/create-volumes.md)
