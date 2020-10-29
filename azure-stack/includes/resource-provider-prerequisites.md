---
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: include
ms.date: 12/09/2019
ms.reviewer: bryanla
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: 2a980becc3bf749987759f90a9ab84050d033a04
ms.sourcegitcommit: 81e2d627c9dc4cc365deb4a0e0674b5ab3a7efbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92297869"
---
Wenn Sie bereits einen Ressourcenanbieter installiert haben, haben Sie wahrscheinlich alle Schritte für die folgenden Voraussetzungen ausgeführt und können diesen Abschnitt überspringen. Führen Sie andernfalls vor dem Fortfahren diese Schritte aus: 

1. [Registrieren Sie Ihre Azure Stack Hub-Instanz in Azure](../operator/azure-stack-registration.md), wenn Sie dies noch nicht getan haben. Dieser Schritt ist erforderlich, weil eine Verbindung mit Azure herstellen und aus Azure Elemente in Marketplace herunterladen.

2. Wenn Sie nicht mit dem **Marketplace-Verwaltung** -Feature des Azure Stack Hub-Administratorportals vertraut sind, sollten Sie [Herunterladen von Marketplace-Elementen in Azure Stack Hub](../operator/azure-stack-download-azure-marketplace-item.md) lesen. In dem Artikel werden Sie durch die Schritte geleitet, mit denen Elemente aus Azure in den Azure Stack Hub-Marketplace heruntergeladen werden. Dieser Artikel gilt sowohl für ein verbundenes als auch für ein nicht verbundenes Szenario. Ist Ihre Azure Stack Hub-Instanz nicht verbunden oder teilweise verbunden, gibt es weitere Voraussetzungen, die zur Vorbereitung auf die Installation erfüllt sein müssen.

3. Aktualisieren Sie Ihr Stammverzeichnis für Azure Active Directory (Azure AD). Ab Build 1910 muss eine neue Anwendung in Ihrem Stammverzeichnismandanten registriert werden. Mit dieser App kann Azure Stack Hub neuere Ressourcenanbieter (z. B. Event Hubs oder IoT Hub) erfolgreich mit Ihrem Azure AD-Mandanten erstellen und registrieren. Dies ist eine einmalige Aktion, die nach dem Upgrade auf Build 1910 oder höher ausgeführt werden muss. Wird dieser Schritt nicht ausgeführt, tritt bei der Installation von Ressourcenanbietern aus dem Marketplace ein Fehler auf. 

   - Nachdem Sie Ihre Azure Stack Hub-Instanz erfolgreich auf 1910 oder höher aktualisiert haben, befolgen Sie die [Anweisungen zum Klonen/Herunterladen des Azure Stack Hub-Tools-Repository](../operator/azure-stack-powershell-download.md). 
   - Befolgen Sie dann die Anweisungen zum [Aktualisieren des Azure Stack Hub Azure AD-Stammverzeichnisses (nach dem Installieren von Updates oder neuen Ressourcenanbietern)](https://github.com/Azure/AzureStack-Tools/tree/master/Identity#updating-the-azure-stack-aad-home-directory-after-installing-updates-or-new-resource-providers). 