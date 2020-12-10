---
title: Häufig gestellte Fragen zu Azure Stack für Windows Server | Microsoft-Dokumentation
description: Liste von häufig gestellten Fragen zum Azure Stack-Marketplace für Windows Server
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 12/16/2019
ms.openlocfilehash: 6286939eeedc263540018aeea3ed62d02b706f24
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96941158"
---
# <a name="windows-server-in-azure-stack-marketplace-faq"></a>Häufig gestellte Fragen zum Azure Stack-Marketplace für Windows Server

In diesem Artikel werden einige häufig gestellte Fragen zu Windows Server-Images im [Azure Stack Hub-Marketplace](../../operator/azure-stack-marketplace.md) beantwortet.

## <a name="what-are-the-licensing-options-for-windows-server-marketplace-images-on-azure-stack"></a>Welche Lizenzierungsoptionen sind für Windows Server-Marketplace-Images in Azure Stack verfügbar?

Azure Stack Hub Ruggedized- und MDC-Benutzer haben Anspruch auf die kostenlose Nutzung von Windows Server als Gastbetriebssystem.

Microsoft bietet zwei Versionen von Windows Server-Images über den Azure Stack-Marketplace an. In einer Azure Stack-Umgebung kann immer nur eine Version dieses Images verwendet werden.

- **Nutzungsbasierte Bezahlung**: Diese Images sollten nicht für Azure Stack Hub Ruggedized und MDC verwendet werden.

- **BYOL (Bring-Your-Own-License, Verwendung Ihrer eigenen Lizenz)** : Diese Images können für Azure Stack Hub Ruggedized und MDC verwendet werden.

## <a name="what-about-other-vms-that-use-windows-server-such-as-sql-server"></a>Was ist mit anderen VMs, die Windows Server verwenden, z. B. SQL Server?

Die Windows Server-Softwarelizenz gilt nur für das Windows-Betriebssystem, nicht für zusätzliche Produkte wie SQL, für die Sie Ihre eigene Lizenz verwenden müssen.

## <a name="how-do-i-update-to-a-newer-windows-image"></a>Wie führe ich eine Aktualisierung auf ein neueres Windows-Image durch?

Finden Sie zunächst heraus, ob Azure Resource Manager-Vorlagen auf bestimmte Versionen verweisen. Wenn dies der Fall ist, aktualisieren Sie diese Vorlagen, oder behalten Sie die älteren Imageversionen bei. Empfohlen wird, die **Version mit der Bezeichnung „latest“** zu verwenden.

Falls VM-Skalierungsgruppen auf eine bestimmte Version verweisen, sollten Sie dann überlegen, ob diese später skaliert werden, und entscheiden, ob Sie ältere Versionen beibehalten möchten. Löschen Sie vor dem Herunterladen der neueren Images die älteren Images im Marketplace, wenn keine dieser Bedingungen zutrifft. Verwenden Sie zum Löschen die Marketplace-Verwaltung, falls das ursprüngliche Image auf diese Weise heruntergeladen wurde. Laden Sie dann die neuere Version herunter.

## <a name="what-if-i-downloaded-the-wrong-version-to-offer-my-tenantsusers"></a>Was geschieht, wenn ich die falsche Version für meine Mandanten/Benutzer heruntergeladen habe?

Löschen Sie zuerst die falsche Version über die Marketplace-Verwaltung. Warten Sie, bis der Vorgang abgeschlossen ist (achten Sie auf die Benachrichtigungen über den Abschluss, nicht auf das Blatt **Marketplace-Verwaltung**). Laden Sie dann die richtige Version herunter.

Wenn Sie beide Versionen des Images herunterladen, ist nur die neueste Version für Endkunden im Marketplace-Katalog sichtbar.

## <a name="activation"></a>Aktivierung

Damit Sie einen virtuellen Windows Server-Computer in Azure Stack aktivieren können, müssen die folgenden Bedingungen erfüllt sein:

- Windows Server 2012 R2 und Windows Server 2016 müssen die [automatische Aktivierung virtueller Computer](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) verwenden. Der Schlüsselverwaltungsdienst (Key Management Service, KMS) und andere Aktivierungsdienste werden nicht in Azure Stack unterstützt.

## <a name="how-can-i-verify-that-my-virtual-machine-is-activated"></a>Wie kann ich sicherstellen, dass mein virtueller Computer aktiviert ist?

Führen Sie den folgenden Befehl in einer Eingabeaufforderung mit erhöhten Rechten aus:

```shell
slmgr /dlv
```

Wenn er richtig aktiviert ist, wird dies deutlich mit dem Hostnamen in der Ausgabe für slmgr angezeigt. Verlassen Sie sich nicht auf Wasserzeichen in der Anzeige, da sie möglicherweise nicht auf dem neuesten Stand sind oder von einem anderen virtuellen Computer angezeigt werden, der sich hinter dem Ihren befindet.

## <a name="my-vm-is-not-set-up-to-use-avma-how-can-i-fix-it"></a>Meine VM ist nicht für die Verwendung von AVMA konfiguriert. Wie kann ich dies beheben?

Führen Sie den folgenden Befehl in einer Eingabeaufforderung mit erhöhten Rechten aus:

```shell
slmgr /ipk <AVMA key>
```

Die für das Image zu verwendenden Schlüssel werden im Artikel [Automatische Aktivierung virtueller Computer](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) aufgeführt.

## <a name="i-create-my-own-windows-server-images-how-can-i-make-sure-they-use-avma"></a>Ich erstelle meine eigenen Windows Server-Images. Wie kann ich sicherstellen, dass diese AVMA verwenden?

Es wird empfohlen, den Befehl `slmgr /ipk` mit dem entsprechenden Schlüssel auszuführen, bevor Sie den Befehl `sysprep` ausführen. Alternativ können Sie den AVMA-Schlüssel in einer Setupdatei namens „Unattend.exe“ einschließen.

## <a name="i-am-trying-to-use-my-windows-server-2016-image-created-on-azure-and-it-is-not-activating-or-using-kms-activation"></a>Ich möchte mein in Azure erstelltes Windows Server 2016-Image verwenden, doch es kann nicht aktiviert werden oder die KMS-Aktivierung funktioniert nicht.

Führen Sie den Befehl `slmgr /ipk` aus. Für Azure-Images wird das Fallback auf AVMA möglicherweise nicht richtig ausgeführt, aber wenn eine Verbindung mit dem Azure KMS-System hergestellt werden kann, werden sie aktiviert. Es wird empfohlen, sicherzustellen, dass diese VMs für die Verwendung von AVMA konfiguriert sind.

## <a name="i-have-performed-all-of-these-steps-but-my-virtual-machines-are-still-not-activating"></a>Ich habe all diese Schritte ausgeführt, aber meine virtuellen Computer sind noch nicht aktiviert.

Wenden Sie sich an den Microsoft-Support, um sicherzustellen, dass die richtigen BIOS-Marker installiert wurden.

## <a name="what-about-earlier-versions-of-windows-server"></a>Was ist mit früheren Versionen von Windows Server?

Die [Automatische Aktivierung virtueller Maschinen](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) wird in Windows Server-Versionen vor 2012 R2 nicht unterstützt. Sie müssen die VMs manuell mit Mehrfachaktivierungsschlüsseln aktivieren.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Der Azure Stack-Marketplace – Übersicht](../../operator/azure-stack-marketplace.md)
- [Herunterladen von Marketplace-Elementen von Azure in Azure Stack](azure-stack-download-azure-marketplace-item-tca.md)
