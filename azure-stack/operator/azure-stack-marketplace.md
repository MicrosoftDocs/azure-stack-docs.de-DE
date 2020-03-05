---
title: Der Azure Stack Hub-Marketplace – Übersicht
description: Eine Übersicht über den Azure Stack Hub-Marketplace und Marketplace-Elemente.
author: sethmanheim
ms.topic: article
ms.date: 01/07/2020
ms.author: sethm
ms.reviewer: ihcherie
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: eeeda1d2b26ba6087e888ecd8ab847756c8e95fa
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77699013"
---
# <a name="azure-stack-hub-marketplace-overview"></a>Der Azure Stack Hub-Marketplace – Übersicht

Der Azure Stack Hub-Marketplace ist eine Sammlung von Diensten, Apps und Ressourcen, die für Azure Stack angepasst wurden. Zu den Ressourcen gehören u. a. Netzwerke, VMs und Speicher. Verwenden Sie den Azure Stack Hub-Marketplace, um neue Ressourcen zu erstellen und neue Apps bereitzustellen, oder suchen Sie nach den Elementen, die Sie verwenden möchten, und wählen Sie sie aus. Benutzer müssen ein Angebot abonnieren, das ihnen Zugriff auf das Marketplace-Element gewährt, um dieses verwenden zu können.

Als Azure Stack Hub-Betreiber entscheiden Sie, welche Elemente Sie dem Azure Stack Hub-Marketplace hinzufügen (veröffentlichen). Sie können Elemente wie Datenbanken und App Services veröffentlichen. Durch die Veröffentlichung werden Elemente für alle Benutzer sichtbar. Sie können benutzerdefinierte Elemente veröffentlichen, die Sie erstellen, oder Sie können Elemente aus einer wachsenden [Liste von Azure Marketplace-Elementen](azure-stack-marketplace-azure-items.md) veröffentlichen. Wenn Sie ein Element im Azure Stack Hub-Marketplace veröffentlichen, wird es Benutzern innerhalb von fünf Minuten angezeigt.

> [!CAUTION]  
> Auf alle Artefakte von Katalogelementen, wie zum Beispiel Images und JSON-Dateien, kann ohne Authentifizierung zugegriffen werden, nachdem sie im Azure Stack Hub-Marketplace verfügbar gemacht wurden. Weitere Überlegungen zum Veröffentlichen von benutzerdefinierten Marketplace-Elementen finden Sie unter [Erstellen und Veröffentlichen eines Marketplace-Elements](azure-stack-create-and-publish-marketplace-item.md).

Um den Marketplace zu öffnen, wählen Sie im Administratorportal **+Ressource erstellen** aus.

![Erstellen einer Ressource im Azure Stack Hub-Administratorportal](media/azure-stack-marketplace/marketplace1.png)

## <a name="marketplace-items"></a>Marketplace-Elemente

Azure Stack Hub-Marketplace-Elemente sind Dienste, Apps oder Ressourcen, die Benutzer herunterladen und verwenden können. Alle Azure Stack Hub-Marketplace-Elemente, einschließlich administrativer Elemente wie Pläne und Angebote, sind für alle Benutzer sichtbar. Diese administrativen Elemente können zwar ohne Abonnement angezeigt, aber nicht von den Benutzern verwendet werden.

Jedes Marketplace-Element verfügt über:

* Eine Azure-Ressourcen-Manager-Vorlage für die Ressourcenbereitstellung
* Metadaten wie Zeichenfolgen, Symbole und andere Marketingmaterialien
* Formatierungsinformationen zum Anzeigen des Elements im Portal

Jedes im Azure Stack Hub-Marketplace veröffentlichte Element verwendet das Azure Gallery Package-Format (AZPKG). Fügen Sie Azure Stack Hub Bereitstellungs- oder Laufzeitressourcen (Code, ZIP-Dateien mit Software oder VM-Images) separat und nicht als Bestandteil des Marketplace-Elements hinzu.

Ab Version 1803 konvertiert Azure Stack Hub Images in platzsparende Dateien, wenn sie von Azure heruntergeladen werden, oder wenn Sie benutzerdefinierte Images hochladen. Mit diesem Vorgang dauert das Hinzufügen eines Images zwar länger, es wird jedoch Speicherplatz gespart, und die Bereitstellung dieser Images wird beschleunigt. Die Konvertierung erfolgt nur für neue Images. Vorhandene Images werden nicht geändert.

## <a name="next-steps"></a>Nächste Schritte

* [Herunterladen vorhandener Marketplace-Elemente aus Azure und Veröffentlichen in Azure Stack Hub](azure-stack-download-azure-marketplace-item.md)  
* [Erstellen und Veröffentlichen eines benutzerdefinierten Azure Stack Hub-Marketplace-Elements](azure-stack-create-and-publish-marketplace-item.md)
