---
title: Veröffentlichen von benutzerdefinierten Marketplace-Elementen in Azure Stack (Cloudbetreiber) | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie als Azure Stack-Betreiber ein benutzerdefiniertes Marketplace-Element in Azure Stack veröffentlichen.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 60871cbb-eed2-433c-a76d-d605c7aec06c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/12/2019
ms.author: sethm
ms.reviewer: ihcherie
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: be62ddc3c3e5ea7180164cac95edd125030cbc6a
ms.sourcegitcommit: 58c28c0c4086b4d769e9d8c5a8249a76c0f09e57
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/12/2019
ms.locfileid: "68959498"
---
# <a name="azure-stack-marketplace-overview"></a>Der Azure Stack-Marketplace – Übersicht

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Der Azure Stack-Marketplace ist eine Sammlung von Diensten, Anwendungen und Ressourcen, die für die Azure Stack angepasst wurden. Zu den Ressourcen gehören u.a. Netzwerke, virtuelle Computer und Speicher. Verwenden Sie den Marketplace, um neue Ressourcen zu erstellen und neue Anwendungen bereitzustellen, oder suchen Sie nach den Elementen, die Sie verwenden möchten. Benutzer müssen ein Angebot abonnieren, das ihnen Zugriff auf das Marketplace-Element gewährt, um dieses zu verwenden.

Als Azure Stack-Betreiber entscheiden Sie, welche Elemente Sie zum Marketplace hinzufügen (veröffentlichen). Sie können z. B. Datenbanken, App Services usw. veröffentlichen. Durch die Veröffentlichung werden Elemente für alle Benutzer sichtbar. Sie können benutzerdefinierte Elemente veröffentlichen, die Sie erstellen, oder Sie können Elemente aus einer wachsenden [Liste von Azure Marketplace-Elementen](azure-stack-marketplace-azure-items.md) veröffentlichen. Wenn Sie ein Element im Marketplace veröffentlichen, wird es Benutzern innerhalb von fünf Minuten angezeigt.

> [!CAUTION]  
> Auf alle Artefakte von Katalogelementen, wie zum Beispiel Images und JSON-Dateien, kann ohne Authentifizierung zugegriffen werden, nachdem sie im Azure Stack-Marketplace verfügbar gemacht wurden. Weitere Überlegungen zum Veröffentlichen von benutzerdefinierten Marketplace-Elementen finden Sie unter [Erstellen und Veröffentlichen eines Marketplace-Elements](azure-stack-create-and-publish-marketplace-item.md).

Um den Marketplace zu öffnen, wählen Sie im Administratorportal **+Ressource erstellen** aus.

![Marketplace](media/azure-stack-marketplace/marketplace1.png)

## <a name="marketplace-items"></a>Marketplace-Elemente

Azure Stack-Marketplace-Elemente sind Dienste, Anwendungen oder Ressourcen, die die Benutzer herunterladen und verwenden können. Alle Azure Stack Marketplace-Elemente, einschließlich administrativer Elemente wie Pläne und Angebote, sind für alle Benutzer sichtbar. Diese administrativen Elemente können ohne Abonnement angezeigt werden, sind für Benutzer jedoch nicht funktionsfähig.

Jedes Marketplace-Element verfügt über:

* Eine Azure-Ressourcen-Manager-Vorlage für die Ressourcenbereitstellung
* Metadaten wie Zeichenfolgen, Symbole und andere Marketingmaterialien
* Formatierungsinformationen zum Anzeigen des Elements im Portal

Jedes im Marketplace veröffentlichte Element verwendet das Azure Gallery Package-Format (AZPKG). Fügen Sie Azure Stack Bereitstellungs- oder Laufzeitressourcen (Code, ZIP-Dateien mit Software oder VM-Images) separat und nicht als Teil des Marketplace-Elements hinzu.

Ab Version 1803 konvertiert Azure Stack Images in platzsparende Dateien, wenn sie von Azure herunterladen werden, oder wenn Sie benutzerdefinierte Images hochladen. Mit diesem Vorgang dauert das Hinzufügen eines Images zwar länger, es wird jedoch Speicherplatz gespart, und die Bereitstellung dieser Images wird beschleunigt. Die Konvertierung erfolgt nur für neue Images. Vorhandene Images werden nicht geändert.

## <a name="next-steps"></a>Nächste Schritte

* [Herunterladen von Marketplace-Elementen](azure-stack-download-azure-marketplace-item.md)  
* [Erstellen und Veröffentlichen eines Marketplace-Elements](azure-stack-create-and-publish-marketplace-item.md)
