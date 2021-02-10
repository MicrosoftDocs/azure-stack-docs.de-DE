---
title: Registrieren von Windows Admin Center bei Azure
description: So registrieren Sie Ihr Windows Admin Center-Gateway bei Azure.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 01/28/2021
ms.openlocfilehash: d98cb4f0531903d27d97d9fb055c7ae2db35a65e
ms.sourcegitcommit: b461597917b768412036bf852c911aa9871264b2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99050058"
---
# <a name="register-windows-admin-center-with-azure"></a>Registrieren von Windows Admin Center bei Azure

> Gilt für Azure Stack HCI v20H2; Windows Server 2019

Für die Verwendung von Azure-Diensten mit Windows Admin Center müssen Sie Windows Admin Center zunächst auf einem Verwaltungscomputer installieren und eine einmalige Registrierung Ihres Windows Admin Center-Gateways durchführen. Dies ist eine Voraussetzung für die Registrierung Ihres Clusters bei Azure und sollte auf demselben Verwaltungscomputer erfolgen, auf dem Sie auch die [Clusterregistrierung](../deploy/register-with-azure.md) durchführen möchten. Verwenden Sie hierbei die gleiche Azure-Abonnement-ID und -Mandanten-ID.

## <a name="complete-the-gateway-registration-process-using-windows-admin-center"></a>Ausführen der Gatewayregistrierung mit Windows Admin Center

1. Starten Sie Windows Admin Center, und klicken Sie auf das Zahnradsymbol **Einstellungen** in der oberen rechten Ecke, über das Sie zur Seite „Konten“ gelangen. Wählen Sie dann im **Gateway**-Menü links **Azure** aus, und klicken Sie auf **Registrieren**.

   :::image type="content" source="media/register-wac/register-wac.png" alt-text="Klicken auf „Einstellungen“ > „Gateway“ > „Azure“ und dann auf „Registrieren“" lightbox="media/register-wac/register-wac.png":::

2. Sie erhalten einen eindeutigen Code. Klicken Sie auf die Schaltfläche **Kopieren** rechts neben dem Code. Klicken Sie auf **Code eingeben**, wodurch ein neues Browserfenster geöffnet wird, in das Sie den in Ihrer App oder auf Ihrem Gerät angezeigten Code einfügen können.

   :::image type="content" source="media/register-wac/enter-code.png" alt-text="Kopieren des eindeutigen Codes, Klicken auf „Code eingeben“ und Einfügen des Codes in das Dialogfeld" lightbox="media/register-wac/enter-code.png":::

3. Nach dem Einfügen des Codes werden Sie benachrichtigt, dass Ihre Anmeldung bei Windows Admin Center auf einem Remotegerät oder in einem Dienst bevorsteht. Geben Sie Ihre E-Mail-Adresse oder Telefonnummer ein. Wenn es sich bei Ihrem Gerät um ein verwaltetes Gerät handelt, werden Sie zur Authentifizierung zur Anmeldeseite Ihrer Organisation weitergeleitet. Befolgen Sie die Anweisungen, und geben Sie die entsprechenden Anmeldeinformationen ein.

   :::image type="content" source="media/register-wac/sign-in.png" alt-text="Anmelden bei Windows Admin Center mit Ihrer E-Mail-Adresse oder Telefonnummer" lightbox="media/register-wac/sign-in.png":::

   Die folgende Meldung sollte angezeigt werden: „Sie haben sich bei der Windows Admin Center-Anwendung auf Ihrem Gerät angemeldet“. Schließen Sie das Browserfenster, um zur ursprünglichen Registrierungsseite zurückzukehren.

4. Stellen Sie eine Verbindung mit Azure Active Directory her, indem Sie Ihre Azure Active Directory-ID (Mandanten-ID) eingeben. Falls Sie bereits über eine Azure-Mandanten-ID verfügen und die obigen Schritte ausgeführt haben, ist das Feld für die ID ggf. bereits ausgefüllt. Wenn Ihre Organisation Ihnen keine vorhandene ID angegeben hat, belassen Sie die **Azure Active Directory-Anwendung** auf der Einstellung **Neue erstellen**. Wenn Sie bereits über eine ID verfügen, klicken Sie auf **Vorhandene verwenden**, dann wird ein leeres Feld angezeigt, in dem Sie die ID eingeben können, die Ihnen von Ihrem Administrator zur Verfügung gestellt wurde. Nachdem Sie Ihre ID eingegeben haben, bestätigt Windows Admin Center, dass ein Konto mit dieser ID gefunden wurde. Wenn Sie über eine vorhandene ID verfügen, aber nicht wissen, wozu sie dient, führen Sie [diese Schritte](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) aus, um sie abzurufen.

   :::image type="content" source="media/register-wac/connect-to-aad.png" alt-text="Verbinden mit Azure Active Directory durch Bereitstellen Ihrer vorhandenen Azure Active Directory-(Mandanten-)ID oder Erstellen einer neuen ID" lightbox="media/register-wac/connect-to-aad.png":::

5. Klicken Sie auf die Schaltfläche **Verbinden**, um die Verbindung zu Azure herzustellen. Es sollte eine Bestätigung angezeigt werden, dass Sie jetzt mit Azure AD verbunden sind.

6. Erteilen Sie Berechtigungen in Azure, indem Sie im Azure-Portal zu **App-Berechtigungen** navigieren. Wählen Sie unter **Einwilligung erteilen** die Option **Administratoreinwilligung erteilen** aus.

7. Schließen Sie das Fenster, und melden Sie sich mit Ihrem Azure-Konto bei Windows Admin Center an.

## <a name="next-steps"></a>Nächste Schritte

Sie sind nun zu diesen Schritten bereit:

- [Herstellen einer Verbindung von Azure Stack HCI mit Azure](../deploy/register-with-azure.md)
- [Verwenden von Azure Stack HCI mit Windows Admin Center](../get-started.md)
- [Herstellen einer Verbindung mit Azure-Hybriddiensten](/windows-server/manage/windows-admin-center/azure/)