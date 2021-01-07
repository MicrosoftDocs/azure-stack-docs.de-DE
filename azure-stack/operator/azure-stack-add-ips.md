---
title: Hinzufügen öffentlicher IP-Adressen in Azure Stack Hub
description: Erfahren Sie, wie Sie Azure Stack Hub öffentliche IP-Adressen hinzufügen.
author: PatAltimore
ms.topic: article
ms.date: 05/28/2020
ms.author: patricka
ms.reviewer: scottnap
ms.lastreviewed: 09/10/2019
ms.openlocfilehash: 5f083e507689e7d24fa02060e63a9e6302ef3568
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97871938"
---
# <a name="add-public-ip-addresses"></a>Hinzufügen öffentlicher IP-Adressen

In diesem Artikel werden externe Adressen als öffentliche IP-Adressen bezeichnet. Im Kontext von Azure Stack Hub ist eine öffentliche IP-Adresse eine IP-Adresse, auf die von außerhalb von Azure Stack Hub zugegriffen werden kann. Ob dieses externe Netzwerk öffentlich internetroutingfähig ist oder sich in einem Intranet befindet und einen privaten Adressraum verwendet, spielt für die Zwecke dieses Artikels keine Rolle, da die Schritte identisch sind. 

Sie können zwar mehrere IP-Pools einrichten, Sie können jedoch nicht auswählen, welcher Pool verwendet wird. Azure Stack Hub verwendet alle IP-Pools als einen Thread. Wenn Sie eine Ressource erstellen, können Sie keine IP-Adresse für die Zuweisung auswählen.

> [!IMPORTANT]
> Die Schritte in diesem Artikel gelten nur für Systeme, die mit dem Partner-Toolkit, Version 1809 oder höher bereitgestellt wurden. Für Systeme, die vor Version 1809 bereitgestellt wurden, müssen die Zugriffssteuerungslisten (Access Control Lists, ACLs) des TOR-Switches (Top-of-Rack) aktualisiert werden, um den neuen öffentlichen VIP-Poolbereich ZUZULASSEN. Wenn Sie ältere Switchkonfigurationen ausführen, wenden Sie sich an ihren OEM, um entweder die entsprechenden ZULASSUNGS-ACLs für den neuen öffentlichen IP-Pool hinzuzufügen, oder wiederholen Sie die Konfiguration des Switches mit dem neuesten Partner-Toolkit, um zu verhindern, dass die neuen öffentlichen IP-Adressen blockiert werden.

## <a name="add-a-public-ip-address-pool"></a>Hinzufügen eines öffentlichen IP-Adresspools
Sie können Ihrem Azure Stack Hub-System nach der erstmaligen Bereitstellung jederzeit öffentliche IP-Adressen hinzufügen. Sehen Sie sich an, wie Sie die [Nutzung öffentlicher IP-Adressen anzeigen](azure-stack-viewing-public-ip-address-consumption.md) können, um herauszufinden, welche aktuelle Nutzung und Verfügbarkeit öffentlicher IP-Adressen in Ihrem Azure Stack Hub vorliegt.

Ganz allgemein sieht der Vorgang des Hinzufügens eines neuen öffentlichen IP-Adressblocks zu Azure Stack Hub folgendermaßen aus:

 ![Hinzufügen eines IP-Datenflusses](media/azure-stack-add-ips/flow.svg)

## <a name="obtain-the-address-block-from-your-provider"></a>Abrufen des Adressblocks von Ihrem Anbieter
Zunächst müssen Sie den Adressblock abrufen, den Sie Azure Stack Hub hinzufügen möchten. Je nachdem, woher Sie Ihren Adressblock beziehen, müssen Sie die Vorlaufzeit berücksichtigen und diese mit der Rate abgleichen, mit der Sie öffentliche IP-Adressen in Azure Stack Hub nutzen.

> [!IMPORTANT]
> Azure Stack Hub akzeptiert jeden von Ihnen bereitgestellten Adressblock, sofern es sich um einen gültigen Adressblock handelt, der sich nicht mit einem vorhandenen Adressbereich in Azure Stack Hub überschneidet. Stellen Sie sicher, dass Sie einen gültigen Adressblock abrufen, der routingfähig ist und sich nicht mit dem externen Netzwerk überschneidet, mit dem Azure Stack Hub verbunden ist. Nachdem Sie den Bereich zu Azure Stack Hub hinzugefügt haben, können Sie ihn nicht mehr entfernen.

## <a name="add-the-ip-address-range-to-azure-stack-hub"></a>Hinzufügen des IP-Adressbereichs zu Azure Stack Hub

1. Wechseln Sie in einem Browser zu Ihrem Dashboard im Administratorportal. In diesem Beispiel verwenden wir `https://adminportal.local.azurestack.external`.
2. Melden Sie sich als Cloudoperator beim Azure Stack Hub-Administratorportal an.
3. Suchen Sie auf dem Standarddashboard nach der Liste „Regionsverwaltung“, und klicken Sie auf die Region, die Sie verwalten möchten. In diesem Beispiel verwenden wir die lokale Region.
4. Suchen Sie nach der Kachel „Ressourcenanbieter“, und klicken Sie auf den Netzwerkressourcenanbieter.
5. Klicken Sie auf die Kachel für die Nutzung öffentlicher IP-Pools.
6. Klicken Sie auf die Schaltfläche „IP-Pool hinzufügen“.
7. Geben Sie einen Namen für den IP-Pool an. Der von Ihnen ausgewählte Name hilft Ihnen dabei, den IP-Pool einfacher zu identifizieren. Sie können in diesem Feld kein Sonderzeichen wie „/“ verwenden. Es hat sich als bewährte Methode herausgestellt, den gleichen Namen wie der Adressbereich zu verwenden, aber das ist nicht erforderlich.
8. Geben Sie den Adressblock, den Sie hinzufügen möchten, in CIDR-Notation ein. Beispiel: 192.168.203.0/24
9. Wenn Sie einen gültigen CIDR-Bereich im Feld „Adressbereich“ (CIDR-Block) angeben, werden die Felder „IP-Startadresse“, „IP-Endadresse“ und „Verfügbare IP-Adressen“ automatisch mit Daten aufgefüllt. Diese Felder sind schreibgeschützt und werden automatisch generiert, sodass Sie diese Angaben nicht ändern können, ohne den Wert im Feld „Adressbereich“ zu ändern.
10. Wenn Sie die Informationen auf dem Blatt überprüft haben und alle Angaben richtig sind, wählen Sie **OK** aus, um die Änderung zu übernehmen und Azure Stack Hub den Adressbereich hinzuzufügen.


## <a name="next-steps"></a>Nächste Schritte 
[Überprüfen von Knotenaktionen für Skalierungseinheiten](azure-stack-node-actions.md)
