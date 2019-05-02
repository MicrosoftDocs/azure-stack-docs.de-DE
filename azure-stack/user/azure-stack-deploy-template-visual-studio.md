---
title: Bereitstellen von Vorlagen mithilfe von Visual Studio in Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Vorlagen mit Visual Studio in Azure Stack bereitstellen.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 628da2ae-64cc-42e0-b8b7-a6a3724cb974
ms.service: azure-stack
ms.custom: vs-azure
ms.workload: azure-vs
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: f4fda8bbbb1bf88934f641644f89c319317ec58f
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "64310374"
---
# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>Bereitstellen von Vorlagen in Azure Stack mithilfe von Visual Studio

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Sie können Visual Studio zum Bereitstellen von Azure Resource Manager-Vorlagen für Azure Stack verwenden.

## <a name="to-deploy-a-template"></a>So stellen Sie eine Vorlage bereit

1. Verwenden Sie Visual Studio, um Azure Stack zu [installieren und eine Verbindung herzustellen](azure-stack-install-visual-studio.md).
2. Öffnen Sie Visual Studio.
3. Wählen Sie **Datei**, und wählen Sie dann **Neu**. Wählen Sie in **Neues Projekt** die Option **Azure-Ressourcengruppe** aus.
4. Geben Sie einen **Namen** für das neue Projekt ein, und wählen Sie dann **OK**.
5. Wählen Sie in **Azure-Vorlage auswählen** in der Dropdownliste die Option **Azure Stack-Schnellstart** aus.
6. Wählen Sie **101-create-storage-account** und dann **OK**.
7. Erweitern Sie in Ihrem neuen Projekt den Knoten **Vorlagen** im **Projektmappen-Explorer**, um die verfügbaren Vorlagen anzuzeigen.
8. Wählen Sie im **Projektmappen-Explorer** den Namen des Projekts aus, und wählen Sie dann **Bereitstellen**. Wählen Sie **Neue Bereitstellung** aus.
9. Wählen Sie in **Für Ressourcengruppe bereitstellen** in der Dropdownliste **Abonnement** Ihr Microsoft Azure Stack-Abonnement aus.
10. Wählen Sie in der Liste **Ressourcengruppe** eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine neue.
11. Wählen Sie in der Liste **Ressourcengruppenstandort** einen Standort aus, und wählen Sie dann **Bereitstellen**.
12. Geben Sie in **Parameter bearbeiten** Werte für die Parameter ein (je nach Vorlage unterschiedlich), und wählen Sie dann **Speichern**.

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen von Vorlagen mithilfe der Befehlszeile](azure-stack-deploy-template-command-line.md)
* [Entwickeln von Vorlagen für Azure Stack](azure-stack-develop-templates.md)
