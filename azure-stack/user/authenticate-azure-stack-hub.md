---
title: Abrufen von Authentifizierungsinformationen für Azure Stack Hub | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Authentifizierungsinformationen für Azure Stack Hub abrufen.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/13/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/13/2019
ms.openlocfilehash: 624f0fb87b84ee06284185a521a68bebeb695362
ms.sourcegitcommit: 7dd9d7bc2b86cca3be5118da149c1d422b2fb09d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2019
ms.locfileid: "75034007"
---
# <a name="how-to-get-authentication-information-for-azure-stack-hub"></a>Abrufen von Authentifizierungsinformationen für Azure Stack Hub

Für die Authentifizierung bei Azure Stack Hub müssen Sie die Abonnement-ID, die Mandanten-ID, den Standort und den Resource Manager-Endpunkt von Azure Stack Hub angeben. Diese Werte können Sie über den [Azure Stack Hub-Resource Manager-Endpunkt](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-ruby?view=azs-1910#the-azure-stack-resource-manager-endpoint) für Azure Stack Hub abrufen. Sie können sie auch mithilfe der in diesem Artikel beschriebenen Schritte abrufen.

## <a name="values-needed-to-authenticate"></a>Für die Authentifizierung erforderliche Werte

Sie benötigen die folgenden Informationen:

-   **Abonnement-ID**  

    Sie verwenden die Abonnement-ID, um in Azure Stack auf Angebote zuzugreifen.

-   **Tenant ID**

    Ein Verzeichnis ist ein Container mit Informationen zu Benutzern, Anwendungen, Gruppen und Dienstprinzipalen. Ein Verzeichnismandant ist eine Organisation, z. B. Microsoft oder Ihr eigenes Unternehmen.

-   **Location**

    Der Standort oder die Region ist eine Reihe von Rechenzentren, die innerhalb eines durch Wartezeit definierten Umkreises bereitgestellt und über ein dediziertes regionales Netzwerk mit geringer Wartezeit verbunden sind. Mit Azure Stack Hub kann Ihr Standort ein lokales Rechenzentrum anstelle einer Azure-Region enthalten.

-   **Resource Manager-Endpunkt von Azure Stack Hub**

    Microsoft Azure Resource Manager ist ein Verwaltungsframework, mit dem Administratoren Azure-Ressourcen bereitstellen, verwalten und überwachen können. Azure Resource Manager kann diese Aufgaben als Gruppe – anstatt einzeln – in einem gemeinsamen Vorgang verarbeiten.

## <a name="get-the-subscription-id"></a>Abrufen der Abonnement-ID

So rufen Sie die Abonnement-ID ab:

1.  Melden Sie sich beim Azure Stack Hub-Benutzerportal an.

2.  Wählen Sie **Alle Dienste** aus.

    > ![Azure Stack Hub-Authentifizierungsinformationen – Abonnement-ID – Mandanten-ID](./media/authenticate-azure-stack-hub/azure-stack-hub-auth-info.png)

3.  Wählen Sie **Abonnements**.

4.  Wählen Sie das Abonnement aus, das Sie verwenden möchten.

5.  Kopieren Sie die **Abonnement-ID** aus der **Übersicht**.

## <a name="get-the-tenant-id"></a>Abrufen der Mandanten-ID

So rufen Sie die Mandanten-ID ab:

1.  Melden Sie sich beim Azure Stack Hub-Benutzerportal an.

2.  Zeigen Sie mit dem Mauszeiger auf Ihren Benutzernamen in der oberen rechten Ecke des Blatts.

3.  Die **Verzeichnis-ID** ist die Mandanten-ID.

## <a name="get-the-azure-resource-manager-endpoint"></a>Abrufen des Azure Resource Manager-Endpunkts

Der Azure Resource Manager-Endpunkt ist der Metadatenendpunkt für den Bereitstellungs- und Verwaltungsdienst für Azure Stack Hub. Er bietet eine Verwaltungsebene, die das Erstellen, Aktualisieren und Löschen von Ressourcen in Ihrem Azure-Abonnement ermöglicht.

Bei einem integrierten System lautet die URL für den Azure Resource Manager-Endpunkt wie folgt:<br>`https://management.<location>.<fqdn>.com`

Zum Abrufen des Metadatenendpunkts, der auf die Eigenschaften (etwa Katalogendpunkt, Graph-Endpunkt, Portalendpunkt, Anmeldeendpunkt und Zielgruppen) verweist, lautet die URL `<ResourceManager>/metadata/endpoints?api-version=1.0`.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich ausführlicher über die Verwendung von [Azure Stack Resource Manager](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles?view=azs-1910) mit Azure Stack Hub.
