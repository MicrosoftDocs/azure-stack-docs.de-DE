---
title: Löschen von Kontingenten, Plänen, Angeboten und Abonnements | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure Stack-Kontingente, -Pläne, -Angebote und -Abonnements löschen.Löschen Sie Quoten, Pläne, Angebote und Abonnements.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/13/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 04/25/2019
ms.openlocfilehash: fca26723c062dd9f5155030fb11c8e1d695bf5f1
ms.sourcegitcommit: aefcf9c61bd8089a0aaa569af7643e5e15f4947c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68991807"
---
# <a name="delete-quotas-plans-offers-and-subscriptions"></a>Löschen von Kontingenten, Plänen, Angeboten und Abonnements

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

In diesem Artikel wird beschrieben, wie Sie nicht mehr benötigte Kontingente, Pläne, Angebote und Abonnements löschen können. Im Allgemeinen können Sie nur das löschen, was nicht verwendet wird. Beispielsweise ist das Löschen eines Angebots nur möglich, wenn es keine Abonnements gibt, die zu diesem Angebot gehören.

Abonnements sind die Ausnahme von diesem allgemeinen Prinzip: Sie können Abonnements löschen, die Ressourcen enthalten, und die Ressourcen werden zusammen mit dem Abonnement gelöscht.

Daher müssen Sie, wenn Sie ein Kontingent löschen möchten, alle Pläne und Angebote, die dieses Kontingent verwenden, noch einmal durchgehen: Beginnen Sie mit den Angeboten, stellen Sie sicher, dass diese keine Abonnements umfassen, löschen Sie jedes Angebot, löschen Sie dann die Pläne, die das Kontingent verwenden, und so weiter.

## <a name="delete-a-subscription"></a>Löschen eines Abonnements

Wählen Sie zum Löschen eines Abonnements **Alle Dienste** aus, und klicken Sie dann auf **Benutzerabonnements**, um eine Liste aller Abonnements auf dem System anzuzeigen. Wenn Sie an einem Angebot arbeiten, können Sie **Abonnements** auch von dort auswählen.

Sie können Abonnements aus dieser Liste löschen, oder Sie können mit PowerShell ein Skript schreiben, das alle Abonnements für Sie löscht, und zwar unter Verwendung der Befehle, die in der [Abonnements – Referenz löschen](/rest/api/azurestack/subscriptions/delete) dokumentiert sind.

> [!CAUTION]
> Durch Löschen eines Abonnements werden auch alle darin enthaltenen Daten und Ressourcen gelöscht.

## <a name="delete-an-offer"></a>Löschen eines Angebots

Zum Löschen eines Angebots navigieren Sie im Administratorportal zu **Alle Dienste** und dann zu **Angebote**. Wählen Sie das Angebot, das Sie löschen möchten, und dann **Löschen** aus.

![delsub1](media/azure-stack-delete-offer/delsub1.png)

Sie können ein Angebot nur löschen, wenn es keine Abonnements gibt, die es verwenden. Wenn Abonnements vorhanden sind, die auf dem Angebot basieren, ist die Option **Löschen** nicht verfügbar. In diesem Fall lesen Sie den Abschnitt [Löschen eines Abonnements](#delete-a-subscription).

## <a name="delete-a-plan"></a>Löschen eines Benutzers

Um einen Plan zu löschen, navigieren Sie im Administratorportal zu **Alle Dienste** und dann zu **Pläne**. Wählen Sie den Plan, den Sie löschen möchten, und dann **Löschen** aus.

![delsub2](media/azure-stack-delete-offer/delsub2.png)

Sie können einen Plan nur löschen, wenn es keine Angebote oder Abonnements gibt, die ihn verwenden. Wenn es Angebote gibt, die den Plan verwenden, löschen Sie den Plan, was zu einem Fehler führt, und Sie erhalten eine Fehlermeldung. Sie können **Übergeordnete Angebote** auswählen, um eine Liste der Angebote anzuzeigen, die den Plan verwenden. Weitere Informationen zum Löschen von Angeboten finden Sie unter [Löschen eines Angebots](#delete-an-offer).

Ein Plan kann einem Abonnement auch direkt als Add-On-Plan hinzugefügt worden sein, selbst wenn er nicht Teil des Angebots ist. In diesem Fall muss der Plan aus den Abonnements entfernt werden, von dem er verwendet wird, bevor er gelöscht werden kann.

Ein Plan kann auch dann nicht aus einem Abonnement entfernt werden, wenn er die einzige Quelle einer bestimmten Ressource für dieses Abonnement ist. Wenn Plan A beispielsweise zu Abonnement 1 hinzugefügt wurde und der einzige Plan ist, der dem Abonnement ein Netzwerkkontingent bereitstellt, dann kann er nicht aus dem Abonnement entfernt werden. Somit kann er nicht gelöscht werden.

## <a name="edit-and-delete-a-quota"></a>Bearbeiten und Löschen eines Kontingents

Sie können bestehende Kontingente über das Administratorportal anzeigen und bearbeiten: Wählen Sie **Regionsverwaltung** und den entsprechenden Ressourcenanbieter aus, und klicken Sie auf **Kontingente**. Sie können auch Kontingente für bestimmte Ressourcenanbieter löschen.

![delsub3](media/azure-stack-delete-offer/delsub3.png)

Alternativ können Sie bestimmte Kontingente mithilfe dieser REST-APIs löschen:

- [Compute](/rest/api/azurestack/quotas%20(compute)/delete)
- [Netzwerk](/rest/api/azurestack/quotas%20(network)/delete)
- [Speicher](/rest/api/azurestack/storagequotas/delete)

> [!NOTE]
> Ein Kontingent kann nicht gelöscht werden, wenn es von aktuellen Plänen verwendet wird. Sie müssen zuerst den Plan löschen, der auf das Kontingent verweist.

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen von Abonnements](azure-stack-subscribe-plan-provision-vm.md)
- [Bereitstellen von virtuellen Computern](../user/azure-stack-create-vm-template.md)