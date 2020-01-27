---
title: Entscheidungen zu mit Azure verbundenen Bereitstellungen für in Azure Stack Hub integrierte Systeme | Microsoft-Dokumentation
description: Treffen Sie die Entscheidungen zur Planung der Bereitstellung für mit Azure verbundene Bereitstellungen von in Azure Stack Hub integrierten Systemen einschließlich Abrechnung und Identität.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: 92a5ebcabff9562d23ba6a058d12cb7a33778882
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75817702"
---
# <a name="azure-connected-deployment-planning-decisions-for-azure-stack-hub-integrated-systems"></a>Planungsentscheidungen zu mit Azure verbundenen Bereitstellungen für in Azure Stack Hub integrierte Systeme
Nachdem Sie entschieden haben, [wie Sie Azure Stack Hub in Ihre Hybrid Cloud-Umgebung integrieren](azure-stack-connection-models.md), können Sie Ihre Entscheidungsfindung für die Azure Stack Hub-Bereitstellung abschließen.

Die Bereitstellung von mit Azure verbundenem Azure Stack Hub bedeutet, dass Sie entweder Azure Active Directory (Azure AD) oder Active Directory-Verbunddienste (AD FS) als Identitätsspeicher verwenden können. Sie können auch ein Abrechnungsmodell wählen: nutzungsabhängig oder kapazitätsabhängig. Eine mit Azure verbundene Bereitstellung ist die Standardoption, da sie Kunden ermöglicht, den größtmöglichen Nutzen aus Azure Stack Hub zu ziehen, insbesondere bei Hybrid Cloud-Szenarien, die sowohl Azure als auch Azure Stack Hub umfassen.

## <a name="choose-an-identity-store"></a>Wählen eines Identitätsspeichers
Bei einer mit Azure verbundenen Bereitstellung haben Sie die Wahl zwischen Azure AD oder AD FS als Identitätsspeicher. Bei einer nicht mit Azure verbundenen Bereitstellung (ohne Internetverbindung) kann nur AD FS verwendet werden.

Ihre Wahl des Identitätsspeichers hat keinen Einfluss auf die virtuellen Computer (VMs) der Mandanten. Die VMs der Mandanten können wählen, mit welchem Identitätsspeicher sie die Verbindung herstellen möchten, was davon abhängt, wie sie konfiguriert werden: Azure AD, in Windows Server Active Directory-Domäne eingebunden, Arbeitsgruppe usw. Dies ist unabhängig von der Entscheidung bezüglich des Azure Stack Hub-Identitätsanbieters.

Wenn Sie z. B. VMs von IaaS-Mandanten auf Azure Stack Hub aufsetzend bereitstellen und möchten, dass sie in eine Active Directory-Domäne des Unternehmens eingebunden werden und die Konten in dieser verwenden, können Sie dies nach wie vor tun. Sie sind nicht verpflichtet, den Azure AD-Identitätsspeicher, den Sie hier auswählen, für diese Konten zu verwenden.

### <a name="azure-ad-identity-store"></a>Azure AD-Identitätsspeicher
Bei Verwendung von Azure AD als Identitätsspeicher werden zwei Azure AD-Konten benötigt: ein globales Administratorkonto und ein Abrechnungskonto. Diese Konten können entweder dasselbe oder verschiedene Konten sein. Während die Verwendung desselben Benutzerkontos einfacher und praktischer sein kann, wenn Sie nur eine begrenzte Anzahl von Azure-Konten haben, können Ihre geschäftlichen Anforderungen die Verwendung zweier Konten nahelegen:

1. **Globales Administratorkonto** (nur für mit Azure verbundene Bereitstellungen erforderlich). Hierbei handelt es sich um ein Azure-Konto, das zum Erstellen von Apps und Dienstprinzipalen für Azure Stack Hub-Infrastrukturdienste in Azure AD verwendet wird. Dieses Konto muss über Administratorrechte für das Verzeichnis verfügen, in dem Ihr Azure Stack Hub-System installiert werden soll. Es wird zum globalen Administratorkonto des „Cloudbetreibers“ für den Azure AD-Benutzer und für die folgenden Aufgaben verwendet:

    - Bereitstellen und Delegieren von Apps und Dienstprinzipalen für alle Azure Stack Hub-Dienste, die mit Azure AD und der Graph-API interagieren müssen.
    - Als Dienstadministratorkonto. Dieses Konto ist der Besitzer des Standardanbieterabonnements (den Sie später jedoch ändern können). Mit diesem Konto können Sie sich beim Azure Stack Hub-Administratorportal anmelden und damit Angebote und Pläne erstellen, Kontingente festlegen und andere administrative Aufgaben in Azure Stack Hub erledigen.

2. **Abrechnungskonto** (sowohl für mit Azure verbundene als auch nicht verbundene Bereitstellungen erforderlich). Dieses Azure-Konto wird verwendet, um die Abrechnungsbeziehung zwischen Ihrem in Azure Stack Hub integrierten System und dem Azure-Commerce-Back-End herzustellen. Dies ist das Konto, dem Azure Stack Hub-Gebühren in Rechnung gestellt werden. Dieses Konto wird auch zum Anbieten von Elementen im Marketplace und in anderen Hybridszenarien verwendet.

### <a name="ad-fs-identity-store"></a>AD FS-Identitätsspeicher
Wählen Sie diese Option, wenn Sie Ihren eigenen Identitätsspeicher, wie z.B. Ihr unternehmenseigenes Active Directory, für Ihre Dienstadministratorkonten verwenden möchten.  

## <a name="choose-a-billing-model"></a>Wählen eines Abrechnungsmodells
Als Abrechnungsmodell können Sie entweder **Nutzungsbasiert** oder **Kapazität** wählen. Bereitstellungen mit dem nutzungsbasierten Abrechnungsmodell müssen die Nutzung mindestens einmal in 30 Tagen über eine Verbindung mit Azure melden können. Daher ist das nutzungsbasierte Abrechnungsmodell nur für verbundene Bereitstellungen verfügbar.  

### <a name="pay-as-you-use"></a>Nutzungsbasierte Abrechnung
Beim nutzungsbasierten Abrechnungsmodell wird die Nutzung einem Azure-Abonnement in Rechnung gestellt. Sie zahlen nur, wenn Sie die Azure Stack Hub-Dienste verwenden. Wenn Sie sich für dieses Modell entscheiden, benötigen Sie ein Azure-Abonnement und die Konto-ID, die diesem Abonnement zugeordnet ist (z. B. serviceadmin@contoso.onmicrosoft.com). EA-, CSP- und CSL-Abonnements werden unterstützt. Die Erstellung von Nutzungsberichten wird während der [Azure Stack Hub-Registrierung](azure-stack-registration.md) konfiguriert.

> [!NOTE]
> In den meisten Fällen werden EA-Abonnements von Enterprise-Kunden und CSP- oder CSL-Abonnements von Dienstanbietern verwendet.

Wenn Sie ein CSP-Abonnement verwenden möchten, konsultieren Sie die folgende Tabelle, um herauszufinden, welches CSP-Abonnement Sie verwenden sollten, da der richtige Ansatz vom jeweiligen CSP-Szenario abhängt:

|Szenario|Domänen- und Abonnementoptionen|
|-----|-----|
|Sie sind **direkter** oder **indirekter CSP-Partner** und betreiben Azure Stack Hub|Verwenden Sie ein CSL-Abonnement (Common Service Layer).<br>     oder<br>Erstellen Sie einen Azure AD-Mandanten mit einem aussagekräftigen Namen in Partner Center. Beispiel: „&lt;Ihre Organisation>CSPAdmin“ mit Zuordnung eines Azure CSP-Abonnements.|
|Sie sind **indirekter CSP-Wiederverkäufer** und betreiben Azure Stack Hub|Bitten Sie Ihren indirekten CSP-Anbieter, in Partner Center einen Azure AD-Mandanten für Ihre Organisation und ein damit verbundenes Azure CSP-Abonnement zu erstellen.|

### <a name="capacity-based-billing"></a>Kapazitätsbasierte Abrechnung
Wenn Sie sich für das kapazitätsabhängige Abrechnungsmodells entscheiden, müssen Sie eine Azure Stack Hub-Kapazitätsplan-SKU kaufen, die auf der Kapazität Ihres Systems basiert. Sie müssen die Anzahl der physischen Kerne in Ihrer Azure Stack Hub-Instanz kennen, um die richtige Menge zu kaufen.

Für die kapazitätsbezogene Abrechnung ist für die Registrierung ein Azure-Abonnement des Typs EA (Enterprise Agreement) erforderlich. Der Grund dafür ist, dass die Registrierung die Verfügbarkeit der Artikel im Marketplace festlegt, wofür ein Azure-Abonnement erforderlich ist. Das Abonnement wird nicht zur Azure Stack Hub-Nutzung verwendet.

## <a name="learn-more"></a>Weitere Informationen
- Informationen zu Anwendungsfällen, zum Erwerb, zu Partnern und zu OEM-Hardwareanbietern finden Sie auf der Produktseite von [Azure Stack Hub](https://azure.microsoft.com/overview/azure-stack/).
- Informationen zur Roadmap und geografischer Verfügbarkeit für in Azure Stack Hub integrierte Systeme finden Sie im Whitepaper: [Azure Stack Hub: Eine Erweiterung von Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
- Um mehr über Microsoft Azure Stack Hub-Pakete und -Preise zu erfahren, [laden Sie die PDF-Datei herunter](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>Nächste Schritte
[Rechenzentrums-Netzwerkintegration](azure-stack-network.md)