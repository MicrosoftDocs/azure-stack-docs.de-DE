---
title: Abrufen von Authentifizierungsinformationen für Azure Stack Hub
description: Hier erfahren Sie, wie Sie Authentifizierungsinformationen für Azure Stack Hub abrufen.
author: mattbriggs
ms.topic: how-to
ms.date: 12/13/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/13/2019
ms.openlocfilehash: 18e0f405cbe4107fee320b9ffa1bff8711439bfd
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883987"
---
# <a name="get-authentication-information-for-azure-stack-hub"></a>Abrufen von Authentifizierungsinformationen für Azure Stack Hub

Für die Authentifizierung bei Azure Stack Hub müssen Sie die Abonnement-ID, die Mandanten-ID, den Standort und den Resource Manager-Endpunkt von Azure Stack Hub angeben. Diese Werte können Sie über den [Azure Stack Hub-Resource Manager-Endpunkt](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-ruby?view=azs-1910#the-azure-stack-hub-resource-manager-endpoint) für Azure Stack Hub abrufen. Sie können sie auch mithilfe der in diesem Artikel beschriebenen Schritte abrufen.

## <a name="values-needed-to-authenticate"></a>Für die Authentifizierung erforderliche Werte

Sie benötigen die folgenden Informationen:

-   **Abonnement-ID**  

    Sie verwenden die Abonnement-ID, um in Azure Stack Hub auf Angebote zuzugreifen.

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

Informieren Sie sich ausführlicher über die Verwendung von [Azure Stack Hun Resource Manager](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles?view=azs-1910) mit Azure Stack Hub.
