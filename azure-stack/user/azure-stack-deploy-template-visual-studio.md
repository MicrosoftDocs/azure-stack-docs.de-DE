---
title: Bereitstellen von Vorlagen mithilfe von Visual Studio in Azure Stack Hub | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Vorlagen mit Visual Studio in Azure Stack Hub bereitstellen.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 628da2ae-64cc-42e0-b8b7-a6a3724cb974
ms.service: azure-stack
ms.custom: vs-azure
ms.workload: azure-vs
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 86cd483b2416dc613ac74cdca77e7bf3f94b1fe6
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76536282"
---
# <a name="deploy-templates-in-azure-stack-hub-using-visual-studio"></a>Bereitstellen von Vorlagen in Azure Stack Hub mithilfe von Visual Studio

Sie können Visual Studio zum Bereitstellen von Azure Resource Manager-Vorlagen für Azure Stack Hub verwenden.

## <a name="to-deploy-a-template"></a>So stellen Sie eine Vorlage bereit

1. Verwenden Sie Visual Studio, um Azure Stack Hub zu [installieren und eine Verbindung herzustellen](azure-stack-install-visual-studio.md).
2. Öffnen Sie Visual Studio.
3. Wählen Sie **Datei**, und wählen Sie dann **Neu**. Wählen Sie in **Neues Projekt** die Option **Azure-Ressourcengruppe** aus.
4. Geben Sie einen **Namen** für das neue Projekt ein, und wählen Sie dann **OK**.
5. Wählen Sie in **Azure-Vorlage auswählen** in der Dropdownliste die Option **Azure Stack Hub-Schnellstart** aus.
6. Wählen Sie **101-create-storage-account** und dann **OK**.
7. Erweitern Sie in Ihrem neuen Projekt den Knoten **Vorlagen** im **Projektmappen-Explorer**, um die verfügbaren Vorlagen anzuzeigen.
8. Wählen Sie im **Projektmappen-Explorer** den Namen des Projekts aus, und wählen Sie dann **Bereitstellen**. Wählen Sie **Neue Bereitstellung** aus.
9. Wählen Sie in **Für Ressourcengruppe bereitstellen** in der Dropdownliste **Abonnement** Ihr Microsoft Azure Stack Hub-Abonnement aus.
10. Wählen Sie in der Liste **Ressourcengruppe** eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine neue.
11. Wählen Sie in der Liste **Ressourcengruppenstandort** einen Standort aus, und wählen Sie dann **Bereitstellen**.
12. Geben Sie in **Parameter bearbeiten** Werte für die Parameter ein (je nach Vorlage unterschiedlich), und wählen Sie dann **Speichern**.

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen von Vorlagen mithilfe der Befehlszeile](azure-stack-deploy-template-command-line.md)
* [Entwickeln von Vorlagen für Azure Stack Hub](azure-stack-develop-templates.md)
