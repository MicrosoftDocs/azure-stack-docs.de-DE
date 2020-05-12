---
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: include
ms.date: 12/09/2019
ms.reviewer: bryanla
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: d84b6a7d7f4ec1777b8e58e8cafc9ba64726393f
ms.sourcegitcommit: c263a86d371192e8ef2b80ced2ee0a791398cfb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/06/2020
ms.locfileid: "82847873"
---
Wenn Sie bereits einen Ressourcenanbieter installiert haben, haben Sie wahrscheinlich alle Schritte für die folgenden Voraussetzungen ausgeführt und können diesen Abschnitt überspringen. Führen Sie diese Schritte andernfalls aus, bevor Sie fortfahren: 

1. [Registrieren Sie Ihre Azure Stack Hub-Instanz in Azure](../operator/azure-stack-registration.md), wenn Sie dies noch nicht getan haben. Dieser Schritt ist erforderlich, weil eine Verbindung mit Azure herstellen und aus Azure Elemente in Marketplace herunterladen.

2. Wenn Sie nicht mit dem **Marketplace-Verwaltung**-Feature des Azure Stack Hub-Administratorportals vertraut sind, sollten Sie [Herunterladen von Marketplace-Elementen in Azure Stack Hub](../operator/azure-stack-download-azure-marketplace-item.md) lesen. In dem Artikel werden Sie durch die Schritte geleitet, mit denen Elemente aus Azure in den Azure Stack Hub-Marketplace heruntergeladen werden. Dieser Artikel gilt sowohl für ein verbundenes als auch für ein nicht verbundenes Szenario. Ist Ihre Azure Stack Hub-Instanz nicht verbunden oder teilweise verbunden, gibt es weitere Voraussetzungen, die zur Vorbereitung auf die Installation erfüllt sein müssen.

3. Aktualisieren Sie Ihr Stammverzeichnis für Azure Active Directory (Azure AD). Ab Build 1910 muss die neue DRP-Anwendung (Deployment Resource Provider) verwendet werden, um Ihren Stammverzeichnismandanten zu registrieren. Diese Anwendung versetzt DRP in die Lage, Ressourcenanbieter erfolgreich zu erstellen und zu registrieren. Wird dieser Schritt nicht ausgeführt, tritt bei der Installation Ihres Ressourcenanbieters ein Fehler auf. 

   - Nachdem Sie Ihre Azure Stack Hub-Instanz erfolgreich auf 1910 oder höher aktualisiert haben, befolgen Sie die [Anweisungen zum Klonen/Herunterladen des Azure Stack Hub-Tools-Repository](../operator/azure-stack-powershell-download.md). 
   - Befolgen Sie dann die Anweisungen zum [Aktualisieren des Azure Stack Hub Azure AD-Stammverzeichnisses (nach dem Installieren von Updates oder neuen Ressourcenanbietern)](https://github.com/Azure/AzureStack-Tools/tree/master/Identity#updating-the-azure-stack-aad-home-directory-after-installing-updates-or-new-resource-providers). 