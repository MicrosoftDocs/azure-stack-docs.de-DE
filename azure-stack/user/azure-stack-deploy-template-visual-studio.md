---
title: Bereitstellen von Vorlagen mit Visual Studio in Azure Stack Hub
description: Erfahren Sie, wie Sie Vorlagen mit Visual Studio in Azure Stack Hub bereitstellen.
author: mattbriggs
ms.topic: article
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: ea268d367ab529fabe8a8b363ed14ce17bc790aa
ms.sourcegitcommit: db3c9179916a36be78b43a8a47e1fd414aed3c2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2020
ms.locfileid: "84146800"
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
