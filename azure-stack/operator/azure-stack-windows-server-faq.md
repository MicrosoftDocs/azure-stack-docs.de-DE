---
title: Häufig gestellte Fragen zum Azure Stack Hub Marketplace
titleSuffix: Azure Stack Hub
description: Hier finden Sie eine Liste der häufig gestellten Fragen zum Azure Stack Hub-Marketplace für Windows Server.
author: sethmanheim
ms.topic: article
ms.date: 07/23/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 08/29/2019
ms.openlocfilehash: fff299a0d537bb4190e66a57eb642db7e8b9824d
ms.sourcegitcommit: f2a5ce52fcf69e05fe89be8211b7360de46f4a94
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133638"
---
# <a name="azure-stack-hub-marketplace-faq"></a>Häufig gestellte Fragen zum Azure Stack Hub Marketplace

In diesem Artikel werden einige häufig gestellte Fragen zu Marketplace-Elementen im [Azure Stack Hub-Marketplace](azure-stack-marketplace.md) beantwortet.

## <a name="marketplace-items"></a>Marketplace-Elemente

### <a name="who-should-i-contact-for-support-issues-with-azure-stack-hub-marketplace-items"></a>An wen kann ich mich bei Supportproblemen mit Azure Stack Hub Marketplace-Elementen wenden?

Der Azure Marketplace-Supportleitfaden gilt auch für Azure Stack Hub Marketplace-Elemente. Herausgeber sind dafür verantwortlich, technischen Support für ihre Produkte im Azure Stack Hub Marketplace bereitzustellen. Weitere Informationen zum Supportleitfaden für Azure Marketplace-Elemente finden Sie im [Artikel mit häufig gestellten Fragen zu Azure Marketplace](/azure/marketplace/marketplace-faq-publisher-guide#customer-support).

### <a name="how-do-i-update-to-a-newer-windows-image"></a>Wie führe ich eine Aktualisierung auf ein neueres Windows-Image durch?

Finden Sie zunächst heraus, ob Azure Resource Manager-Vorlagen auf bestimmte Versionen verweisen. Wenn dies der Fall ist, aktualisieren Sie diese Vorlagen, oder behalten Sie die älteren Imageversionen bei. Es wird empfohlen, die **Version mit der Bezeichnung „latest“** zu verwenden.

Falls VM-Skalierungsgruppen auf eine bestimmte Version verweisen, sollten Sie dann überlegen, ob diese später skaliert werden, und entscheiden, ob Sie ältere Versionen beibehalten möchten. Löschen Sie vor dem Herunterladen der neueren Images die älteren Images im Azure Stack Hub-Marketplace, wenn keine dieser Bedingungen zutrifft. Verwenden Sie zum Löschen die Marketplace-Verwaltung, falls das ursprüngliche Image auf diese Weise heruntergeladen wurde. Laden Sie dann die neuere Version herunter.

### <a name="what-are-the-licensing-options-for-windows-server-images-on-azure-stack-hub-marketplace"></a>Welche Lizenzierungsoptionen sind für Windows Server-Images in Azure Stack Hub-Marketplace verfügbar?

Microsoft bietet über den Azure Stack Hub-Marketplace zwei Versionen von Windows Server-Images an. In einer Azure Stack Hub-Umgebung kann nur eine Imageversion verwendet werden.  

- **Nutzungsbasierte Bezahlung:** Diese Images führen die Windows-Verbrauchseinheiten zum vollen Preis aus.
   Zielgruppe für diese Option: Enterprise Agreement-Kunden (EA), die das *Nutzungsabrechnungsmodell* verwenden, sowie CSPs, die nicht die SPLA-Lizenzierung verwenden möchten.
- **BYOL (Bring-Your-Own-License, Verwendung Ihrer eigenen Lizenz)** : Diese Images führen grundlegende Verbrauchseinheiten aus.
   Zielgruppe für diese Option: EA-Kunden mit einer Windows Server-Lizenz, und CSPs, die die SPLA-Lizenzierung verwenden.

Der Azure-Vorteil bei Hybridnutzung (Azure Hybrid Use Benefit, AHUB) wird in Azure Stack Hub nicht unterstützt. Kunden, die ihre Lizenz über das „Kapazitätsmodell“ bezogen haben, müssen das BYOL-Image verwenden. Wenn Sie das Azure Stack Development Kit (ASDK) testen, können Sie eine dieser Optionen verwenden.

### <a name="what-if-i-downloaded-the-wrong-version-to-offer-my-tenantsusers"></a>Was geschieht, wenn ich die falsche Version für meine Mandanten/Benutzer heruntergeladen habe?

Löschen Sie zuerst die falsche Version über die Marketplace-Verwaltung. Warten Sie, bis der Vorgang abgeschlossen ist (achten Sie auf die Benachrichtigungen über den Abschluss, nicht auf das Blatt **Marketplace-Verwaltung**). Laden Sie dann die richtige Version herunter.

Wenn Sie beide Versionen des Images herunterladen, ist nur die neueste Version für Endkunden im Azure Stack Hub-Marketplace sichtbar.

### <a name="what-if-my-user-incorrectly-checked-the-i-have-a-license-box-in-previous-windows-builds-and-they-dont-have-a-license"></a>Was geschieht, wenn mein Benutzer fälschlicherweise das Kontrollkästchen „Ich besitze eine Lizenz“ in den vorherigen Windows-Builds aktiviert hat, obwohl er keine Lizenz besitzt?

Durch Ausführen des folgenden Skripts können Sie das Lizenzmodellattribut ändern, um vom BYOL-Modell zum Modell mit nutzungsbasierter Bezahlung zu wechseln:

```powershell
$vm= Get-Azurermvm -ResourceGroup "<your RG>" -Name "<your VM>"
$vm.LicenseType = "None"
Update-AzureRmVM -ResourceGroupName "<your RG>" -VM $vm
```

Sie können den Lizenztyp Ihrer VM überprüfen, indem Sie den folgenden Befehl ausführen. Wenn das Lizenzmodell **Windows_Server** lautet, wird Ihnen der BYOL-Preis in Rechnung gestellt. Andernfalls werden Ihnen die Windows-Verbrauchseinheiten pro Modell mit nutzungsbasierter Zahlung berechnet.

```powershell
$vm | ft Name, VmId,LicenseType,ProvisioningState
```

### <a name="what-if-i-have-an-older-image-and-my-user-forgot-to-check-the-i-have-a-license-box-or-we-use-our-own-images-and-we-do-have-enterprise-agreement-entitlement"></a>Was geschieht, wenn wir ein älteres Image besitzen und mein Benutzer vergessen hat, das Kontrollkästchen „Ich besitze eine Lizenz“ zu aktivieren, oder wir unsere eigenen Images verwenden und über Enterprise Agreement-Berechtigung verfügen?

Durch Ausführen der folgenden Befehle können Sie das Lizenzmodellattribut auf das BYOL-Modell umstellen:

```powershell
$vm= Get-Azurermvm -ResourceGroup "<your RG>" -Name "<your VM>"
$vm.LicenseType = "Windows_Server"
Update-AzureRmVM -ResourceGroupName "<your RG>" -VM $vm
```

### <a name="what-about-other-vms-that-use-windows-server-such-as-sql-or-machine-learning-server"></a>Was passiert mit anderen VMs, die Windows Server verwenden, z.B. SQL Server oder Machine Learning Server?

Diese Images wenden den **licenseType**-Parameter an, weshalb das Modell mit nutzungsbasierter Zahlung angewendet wird. Sie können diesen Parameter festlegen (siehe hierzu die Antwort zur vorherigen häufig gestellten Frage). Dies gilt nur für die Windows Server-Software, nicht für sich überlagernde Produkte wie SQL, für das Bring Your Own License erforderlich ist. Die Lizenzierung mit nutzungsbasierter Zahlung gilt nicht für sich überlagernde Softwareprodukte.

Sie können die **licenseType**-Eigenschaft für SQL Server-Images vom Azure Stack Hub-Marketplace nur ändern, wenn die Version **XX.X.20190410** oder höher lautet. Wenn Sie eine ältere Version der SQL Server-Images aus dem Azure Stack Hub-Marketplace ausführen, können Sie das **licenseType**-Attribut nicht ändern, und Sie müssen die Bereitstellung mit den neuesten SQL Server-Images aus dem Azure Stack Hub-Marketplace erneut ausführen.

### <a name="i-have-an-enterprise-agreement-ea-and-will-be-using-my-ea-windows-server-license-how-do-i-make-sure-images-are-billed-correctly"></a>Ich besitze ein Enterprise Agreement (EA) und verwende meine Windows Server-EA-Lizenz. Wie stelle ich sicher, dass die Images ordnungsgemäß abgerechnet werden?

Sie können **licenseType: Windows_Server** in einer Azure Resource Manager-Vorlage hinzufügen. Diese Einstellung muss jedem Ressourcenblock eines virtuellen Computers (VM) hinzugefügt werden.

## <a name="activation"></a>Aktivierung

Die folgenden Bedingungen müssen erfüllt sein, um eine Windows Server-VM in Azure Stack Hub zu aktivieren:

- Der OEM hat den entsprechenden BIOS-Marker in jedem Hostsystem in Azure Stack Hub festgelegt.
- Windows Server 2012 R2 und Windows Server 2016 müssen die [automatische Aktivierung von VMs](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) verwenden. Der Schlüsselverwaltungsdienst (Key Management Service, KMS) und andere Aktivierungsdienste werden in Azure Stack Hub nicht unterstützt.

### <a name="how-can-i-verify-that-my-vm-is-activated"></a>Wie kann ich sicherstellen, dass meine VM aktiviert ist?

Führen Sie den folgenden Befehl in einer Eingabeaufforderung mit erhöhten Rechten aus:

```shell
slmgr /dlv
```

Wenn er richtig aktiviert ist, wird dies klar und deutlich mit dem Hostnamen in der `slmgr`-Ausgabe angezeigt. Verlassen Sie sich nicht auf Wasserzeichen in der Anzeige, da sie möglicherweise nicht auf dem neuesten Stand sind oder von einer anderen VM hinter Ihrer VM angezeigt werden.

### <a name="my-vm-isnt-set-up-to-use-avma-how-can-i-fix-it"></a>Meine VM ist nicht für die Verwendung von AVMA konfiguriert. Wie kann ich dies beheben?

Führen Sie den folgenden Befehl in einer Eingabeaufforderung mit erhöhten Rechten aus:

```shell
slmgr /ipk <AVMA key>
```

Die für das Image zu verwendenden Schlüssel werden im Artikel [Automatische Aktivierung von VMs](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) aufgeführt.

### <a name="i-create-my-own-windows-server-images-how-can-i-make-sure-they-use-avma"></a>Ich erstelle meine eigenen Windows Server-Images. Wie kann ich sicherstellen, dass diese AVMA verwenden?

Es wird empfohlen, die Befehlszeile `slmgr /ipk` mit dem entsprechenden Schlüssel auszuführen, bevor Sie den Befehl `sysprep` ausführen. Alternativ können Sie den AVMA-Schlüssel in einer Setupdatei namens „Unattend.exe“ einschließen.

### <a name="i-am-trying-to-use-my-windows-server-2016-image-created-on-azure-and-its-not-activating-or-using-kms-activation"></a>Ich möchte mein in Azure erstelltes Windows Server 2016-Image verwenden, doch es kann nicht aktiviert werden oder die KMS-Aktivierung funktioniert nicht.

Führen Sie den Befehl `slmgr /ipk` aus. Für Azure-Images wird das Fallback auf AVMA möglicherweise nicht richtig ausgeführt, aber wenn eine Verbindung mit dem Azure KMS-System hergestellt werden kann, werden sie aktiviert. Es wird empfohlen, sicherzustellen, dass diese VMs für die Verwendung von AVMA konfiguriert sind.

### <a name="i-have-performed-all-of-these-steps-but-my-vms-are-still-not-activating"></a>Ich habe all diese Schritte ausgeführt, aber meine VMs sind immer noch nicht aktiviert.

Wenden Sie sich an Ihren Hardwareanbieter, um sicherzustellen, dass die richtigen BIOS-Marker installiert wurden.

### <a name="what-about-earlier-versions-of-windows-server"></a>Was ist mit früheren Versionen von Windows Server?

Die [automatische Aktivierung von VMs](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) wird in älteren Versionen von Windows Server nicht unterstützt. Sie müssen die virtuellen Computer manuell aktivieren.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Der Azure Stack Hub-Marketplace – Übersicht](azure-stack-marketplace.md)
- [Herunterladen von Marketplace-Elementen von Azure in Azure Stack Hub](azure-stack-download-azure-marketplace-item.md)
