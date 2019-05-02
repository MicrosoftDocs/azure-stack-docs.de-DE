---
title: Veröffentlichen von benutzerdefinierten Marketplace-Elementen in Azure Stack (Cloudbetreiber) | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie als Azure Stack-Betreiber ein benutzerdefiniertes Marketplace-Element in Azure Stack veröffentlichen.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: 60871cbb-eed2-433c-a76d-d605c7aec06c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 01/09/2019
ms.date: 02/18/2019
ms.author: v-jay
ms.reviewer: unknown
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 6c1750e6a523828400b3d06d4e1c22fb34f8273f
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "64307602"
---
# <a name="azure-stack-marketplace-overview"></a>Der Azure Stack-Marketplace – Übersicht

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Der Azure Stack-Marketplace ist eine Sammlung von Diensten, Anwendungen und Ressourcen, die für die Azure Stack angepasst wurden. Zu den Ressourcen gehören u.a. Netzwerke, virtuelle Computer und Speicher. Verwenden Sie den Marketplace, um neue Ressourcen zu erstellen und neue Anwendungen bereitzustellen, oder suchen Sie nach den Elementen, die Sie verwenden möchten. Benutzer müssen ein Angebot abonnieren, das ihnen Zugriff auf das Marketplace-Element gewährt, um dieses zu verwenden.

Als Azure Stack-Betreiber entscheiden Sie, welche Elemente Sie zum Marketplace hinzufügen (veröffentlichen). Sie können z.B. Datenbanken, App Services usw. veröffentlichen. Durch die Veröffentlichung werden sie für alle Benutzer sichtbar. Sie können benutzerdefinierte Elemente veröffentlichen, die Sie erstellen, oder Sie können Elemente aus einer wachsenden [Liste von Azure Marketplace-Elementen](azure-stack-marketplace-azure-items.md) veröffentlichen. Wenn Sie ein Element im Marketplace veröffentlichen, wird es Benutzern innerhalb von fünf Minuten angezeigt.

> [!CAUTION]  
> Auf alle Artefakte von Katalogelementen, wie zum Beispiel Images und JSON-Dateien, kann ohne Authentifizierung zugegriffen werden, nachdem sie im Azure Stack-Marketplace verfügbar gemacht wurden. Weitere Überlegungen zum Veröffentlichen von benutzerdefinierten Marketplace-Elementen finden Sie unter [Erstellen und Veröffentlichen eines Marketplace-Elements](azure-stack-create-and-publish-marketplace-item.md).

Um den Marketplace zu öffnen, wählen Sie im Administratorportal **+Ressource erstellen** aus.

![Marketplace](media/azure-stack-publish-custom-marketplace-item/image1.png)

## <a name="marketplace-items"></a>Marketplace-Elemente

Azure Stack-Marketplace-Elemente sind Dienste, Anwendungen oder Ressourcen, die die Benutzer herunterladen und verwenden können. Alle Azure Stack Marketplace-Elemente, einschließlich administrativer Elemente wie Pläne und Angebote, sind für alle Benutzer sichtbar. Diese Elemente können ohne Abonnement angezeigt werden, sind für Benutzer jedoch nicht funktionsfähig.

Jedes Marketplace-Element verfügt über:

* Eine Azure-Ressourcen-Manager-Vorlage für die Ressourcenbereitstellung
* Metadaten wie Zeichenfolgen, Symbole und andere Marketingmaterialien
* Formatierungsinformationen zum Anzeigen des Elements im Portal

Jedes im Marketplace veröffentlichte Element verwendet das Azure Gallery Package-Format (AZPKG). Fügen Sie Bereitstellungs- oder Laufzeitressourcen (wie Code, ZIP-Dateien mit Software oder VM-Images) separat zu Azure Stack hinzu, nicht als Teil des Marketplace-Elements.

Ab Version 1803 konvertiert Azure Stack Images in platzsparende Dateien, wenn sie von Azure herunterladen werden, oder wenn Sie benutzerdefinierte Images hochladen. Mit diesem Vorgang dauert das Hinzufügen eines Images zwar länger, es wird jedoch Speicherplatz gespart, und die Bereitstellung dieser Images wird beschleunigt. Die Konvertierung erfolgt nur für neue Images. Vorhandene Images werden nicht geändert.

## <a name="next-steps"></a>Nächste Schritte

* [Herunterladen von Marketplace-Elementen](azure-stack-download-azure-marketplace-item.md)  
* [Erstellen und Veröffentlichen eines Marketplace-Elements](azure-stack-create-and-publish-marketplace-item.md)

<!-- Update_Description: wording update -->
