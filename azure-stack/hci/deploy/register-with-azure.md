---
title: Herstellen einer Verbindung von Azure Stack HCI mit Azure
description: Registrieren von Azure Stack HCI bei Azure.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: f45d2ada3b9699688b69b8848490b19961c500ac
ms.sourcegitcommit: f0c032b300d9c640653b1f795a6ea1439e049a6e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87333823"
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

Installieren Sie das PowerShell Module für Azure Stack HCI, indem Sie den folgenden PowerShell-Befehl auf einem oder mehreren Servern in einem Cluster ausführen, der das Azure Stack HCI-Betriebssystem ausführt:

```PowerShell
Install-WindowsFeature RSAT-Azure-Stack-HCI -ComputerName Server1
```

Installieren Sie die erforderlichen Cmdlets auf einem Clusterknoten oder Verwaltungs-PC:

```PowerShell
Install-Module Az.StackHCI
```
   > [!NOTE]
   > 1. Möglicherweise wird eine Aufforderung wie „Soll PowerShellGet jetzt den NuGet-Anbieter installieren und importieren?“ angezeigt, die Sie mit Ja (J) beantworten sollten.
   > 2. Außerdem werden Sie möglicherweise gefragt „Möchten Sie die Module aus ‚PSGallery‘ installieren?“, was Sie ebenfalls mit Ja (J) beantworten sollten.
   > 3. Schließlich könnten Sie annehmen, dass beim Installieren des gesamten **Az**-Moduls das Untermodul **StackHCI** enthalten ist, und langfristig betrachtet wird das auch der Fall sein. Der Standardkonvention von Azure PowerShell folgend, sind Untermodule in der Vorschauphase jedoch nicht automatisch enthalten; Sie müssen sie vielmehr explizit angeben. Daher müssen Sie zurzeit **Az.StackHCI** explizit anfordern, wie oben dargestellt.

Am einfachsten ist es, wenn Sie den folgenden Befehl auf einem Azure Stack HCI-Clusterknoten ausführen (Sie werden aufgefordert, sich bei Azure anzumelden):

```PowerShell
Register-AzStackHCI  -SubscriptionId "e569b8af-6ecc-47fd-a7d5-2ac7f23d8bfe" [-ResourceName] [-ResourceGroupName]
```

Die Minimalsyntax erfordert lediglich Ihre Azure-Abonnement-ID. Diese Syntax registriert den lokalen Cluster (dem der lokale Server angehört) für den aktuellen Benutzer bei der standardmäßigen Azure-Region und -Cloudumgebung. Dabei werden für die Azure-Ressourcen und die Azure-Ressourcengruppe intelligente Standardnamen verwendet. 

Wenn Sie den Cluster lieber über einen Verwaltungs-PC registrieren möchten, geben Sie den Parameter **-ComputerName** mit dem Namen eines Servers im Cluster und ggf. Ihren Anmeldeinformationen an:

```PowerShell
Register-AzStackHCI  -SubscriptionId "e569b8af-6ecc-47fd-a7d5-2ac7f23d8bfe" -ComputerName Server1 [–Credential] [-ResourceName] [-ResourceGroupName]
```

Standardmäßig erbt die Azure-Ressource, die zur Darstellung des Azure Stack HCI-Clusters erstellt wurde, den Clusternamen und wird mit dem gleichen Namen sowie dem Suffix „-rg“ in einer neuen Ressourcengruppe platziert. Mit den oben aufgeführten optionalen Parametern können Sie einen anderen Ressourcennamen angeben oder die Ressource in eine vorhandene Ressourcengruppe platzieren.

Beachten Sie, dass der Benutzer, der das Cmdlet `Register-AzStackHCI` ausführt, über [Azure Active Directory-Berechtigungen](../manage/manage-azure-registration.md#azure-active-directory-permissions) verfügen muss, sonst wird der Registrierungsvorgang nicht abgeschlossen, sondern beendet, und der Registrierung fehlt die Administratoreinwilligung. Nachdem die Berechtigung gewährt wurde, führen Sie einfach `Register-AzStackHCI` erneut aus, um die Registrierung abzuschließen.

   > [!NOTE]
   > Wenn bei der Registrierung eine Fehlermeldung ähnlich der folgenden angezeigt wird, **versuchen Sie, die Registrierung innerhalb der nächsten 24–48 Stunden zu wiederholen**. Die Azure-Integration wird zurzeit noch in den einzelnen Regionen eingeführt. Sie können Ihre Evaluierung fortsetzen, ohne Beeinträchtigung der Funktionalität. Achten Sie einfach darauf, später zurückzukehren und die Registrierung durchzuführen!
   >
   > `Register-AzStackHCI : Azure Stack HCI is not yet available in region <regionName>`
   >
   > Um zu überprüfen, ob Azure Stack HCI in Ihrer Azure-Region verfügbar ist, [verwenden Sie dieses Tool](https://azure.microsoft.com/global-infrastructure/services/) und suchen nach „hci“.

## <a name="authenticate-with-azure"></a>Authentifizieren über Azure
Nachdem die Abhängigkeiten installiert und die Parameter überprüft wurden, müssen Sie sich mit Ihrem Azure-Konto authentifizieren (anmelden). Ihr Konto muss Zugriff auf das angegebene Azure-Abonnement besitzen, damit die Registrierung fortgesetzt werden kann.

Wenn Sie `Register-AzStackHCI` lokal unter dem Azure Stack HCI-Betriebssystem ausgeführt haben, das kein interaktives Azure-Anmeldefenster anzeigen kann, werden Sie aufgefordert, auf einem anderen Gerät (z. B. Ihrem PC oder Smartphone) „microsoft.com/devicelogin“ aufzusuchen, den Code einzugeben und sich dort anzumelden. Dies ist das gleiche Verfahren, das Microsoft für andere Geräte mit eingeschränkten Eingabemodi verwendet, wie etwa Xbox.

Wenn Sie `Register-AzStackHCI` remote auf einem Verwaltungs-PC mit Desktop-Benutzeroberfläche ausgeführt haben, beispielsweise Windows 10, wird ein interaktives Azure-Anmeldefenster angezeigt. Welche Aufforderungen genau angezeigt werden, unterscheidet sich je nach Ihren Sicherheitseinstellungen (z. B. zweistufige Authentifizierung). Folgen Sie den Anweisungen, um sich anzumelden.

Der Registrierungsworkflow erkennt, wenn Sie sich angemeldet haben, und wird bis zum Abschluss fortgesetzt. Anschließend sollten Sie Ihren Cluster im Portal sehen können.

## <a name="next-steps"></a>Nächste Schritte

Sie sind nun zu diesen Schritten bereit:

- [Überprüfen des Clusters](validate.md)
- [Erstellen von Volumes](../manage/create-volumes.md)
