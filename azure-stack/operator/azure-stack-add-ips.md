---
title: Hinzufügen öffentlicher IP-Adressen in Azure Stack | Microsoft Docs
description: Erfahren Sie, wie Sie Azure Stack öffentliche IP-Adressen hinzufügen.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2019
ms.author: justinha
ms.reviewer: scottnap
ms.lastreviewed: 09/10/2019
ms.openlocfilehash: 3978ac3339a2bf6a9dd4df770736bca1a21cc5a3
ms.sourcegitcommit: 38f21e0bcf7b593242ad615c9d8ef8a1ac19c734
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70902646"
---
# <a name="add-public-ip-addresses"></a>Hinzufügen öffentlicher IP-Adressen
*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*  

In diesem Artikel werden externe Adressen als öffentliche IP-Adressen bezeichnet. Im Zusammenhang mit Azure Stack ist eine öffentliche IP-Adresse eine IP-Adresse, auf die von außerhalb von Azure Stack zugegriffen werden kann. Ob dieses externe Netzwerk öffentlich internetroutingfähig ist oder sich in einem Intranet befindet und einen privaten Adressraum verwendet, spielt für die Zwecke dieses Artikels keine Rolle, da die Schritte identisch sind.

> [!IMPORTANT]
> Die Schritte in diesem Artikel gelten nur für Systeme, die mit dem Partner-Toolkit, Version 1809 oder höher bereitgestellt wurden. Für Systeme, die vor Version 1809 bereitgestellt wurden, müssen die Zugriffssteuerungslisten (Access Control Lists, ACLs) des TOR-Switches (Top-of-Rack) aktualisiert werden, um den neuen öffentlichen VIP-Poolbereich ZUZULASSEN. Wenn Sie ältere Switchkonfigurationen ausführen, wenden Sie sich an ihren OEM, um entweder die entsprechenden ZULASSUNGS-ACLs für den neuen öffentlichen IP-Pool hinzuzufügen, oder wiederholen Sie die Konfiguration des Switches mit dem neuesten Partner-Toolkit, um zu verhindern, dass die neuen öffentlichen IP-Adressen blockiert werden.

## <a name="add-a-public-ip-address-pool"></a>Hinzufügen eines öffentlichen IP-Adresspools
Sie können Ihrem Azure Stack-System nach der erstmaligen Bereitstellung jederzeit öffentliche IP-Adressen hinzufügen. Sehen Sie sich an, wie Sie die [Nutzung öffentlicher IP-Adressen anzeigen](azure-stack-viewing-public-ip-address-consumption.md) können, um zu sehen, welche aktuelle Nutzung und Verfügbarkeit öffentlicher IP-Adressen in Ihrem Azure Stack vorliegt.

Ganz allgemein sieht der Vorgang des Hinzufügens eines neuen öffentlichen IP-Adressblocks zu Azure Stack folgendermaßen aus:

 ![Hinzufügen eines IP-Datenflusses](media/azure-stack-add-ips/flow.PNG)

## <a name="obtain-the-address-block-from-your-provider"></a>Abrufen des Adressblocks von Ihrem Anbieter
Zunächst müssen Sie den Adressblock abrufen, den Sie Azure Stack hinzufügen möchten. Je nachdem, woher Sie Ihren Adressblock beziehen, müssen Sie die Vorlaufzeit berücksichtigen und diese mit der Rate abgleichen, mit der Sie öffentliche IP-Adressen in Azure Stack nutzen.

> [!IMPORTANT]
> Azure Stack akzeptiert jeden von Ihnen bereitgestellten Adressblock, sofern es sich um einen gültigen Adressblock handelt, der sich nicht mit einem vorhandenen Adressbereich in Azure Stack überschneidet. Stellen Sie sicher, dass Sie einen gültigen Adressblock erhalten, der routingfähig ist und nicht mit dem externen Netzwerk überlappt, mit dem Azure Stack verbunden ist. Nachdem Sie den Bereich zu Azure Stack hinzugefügt haben, können Sie ihn nicht mehr entfernen.

## <a name="add-the-ip-address-range-to-azure-stack"></a>Hinzufügen des IP-Adressbereichs zu Azure Stack

1. Wechseln Sie in einem Browser zu Ihrem Dashboard im Admin-Portal. In diesem Beispiel verwenden wir https://adminportal.local.azurestack.external.
2. Melden Sie sich beim Azure Stack-Verwaltungsportal als Cloudoperator an.
3. Suchen Sie auf dem Standarddashboard nach der Liste „Regionsverwaltung“, und klicken Sie auf die Region, die Sie verwalten möchten. In diesem Beispiel verwenden wir die lokale Region.
4. Suchen Sie nach der Kachel „Ressourcenanbieter“, und klicken Sie auf den Netzwerkressourcenanbieter.
5. Klicken Sie auf die Kachel für die Nutzung öffentlicher IP-Pools.
6. Klicken Sie auf die Schaltfläche „IP-Pool hinzufügen“.
7. Geben Sie einen Namen für den IP-Pool an. Der von Ihnen ausgewählte Name hilft Ihnen dabei, den IP-Pool einfacher zu identifizieren. Es hat sich als bewährte Methode herausgestellt, den gleichen Namen wie der Adressbereich zu verwenden, aber das ist nicht erforderlich.
8. Geben Sie den Adressblock, den Sie hinzufügen möchten, in CIDR-Notation ein. Beispiel:  192.168.203.0/24
9. Wenn Sie einen gültigen CIDR-Bereich im Feld „Adressbereich“ (CIDR-Block) angeben, werden die Felder „IP-Startadresse“, „IP-Endadresse“ und „Verfügbare IP-Adressen“ automatisch mit Daten aufgefüllt. Diese Felder sind schreibgeschützt und werden automatisch generiert, sodass Sie diese Angaben nicht ändern können, ohne den Wert im Feld „Adressbereich“ zu ändern.
10. Wenn Sie die Informationen auf dem Blatt überprüft haben und alle Angaben richtig sind, wählen Sie **OK** aus, um die Änderung zu übernehmen und Azure Stack den Adressbereich hinzuzufügen.


## <a name="next-steps"></a>Nächste Schritte 
[Überprüfen von Knotenaktionen für Skalierungseinheiten](azure-stack-node-actions.md)
