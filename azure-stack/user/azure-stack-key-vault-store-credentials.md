---
title: Speichern von Dienstprinzipal-Anmeldeinformationen in Azure Stack mit Key Vault | Microsoft-Dokumentation
description: Erfahren Sie, wie Key Vault Dienstprinzipal-Anmeldeinformationen in Azure Stack speichert.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 01/16/2019
ms.date: 02/18/2019
ms.author: v-jay
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: d4bc560a1c607d4a12cfb3d08ffeacf9b5d2196a
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "64310860"
---
# <a name="store-service-principal-credentials-in-key-vault"></a>Speichern von Dienstprinzipal-Anmeldeinformationen in Key Vault

Zum Entwickeln von Anwendungen in Azure Stack muss normalerweise ein Dienstprinzipal erstellt werden, mit dessen Anmeldeinformationen vor der Bereitstellung eine Authentifizierung durchgeführt wird. Die gespeicherten Anmeldeinformationen für den Dienstprinzipal gehen jedoch häufig verloren. Dieser Artikel beschreibt das Erstellen eines Dienstprinzipals und das Speichern der Werte für den späteren Abruf in Azure Key Vault.

Weitere Informationen zu Key Vault finden Sie in [diesem Artikel](azure-stack-key-vault-intro.md).

## <a name="prerequisites"></a>Voraussetzungen

- Ein Abonnement eines Angebots, das den Azure Key Vault-Dienst enthält
- Konfiguration von PowerShell für die Verwendung mit Azure Stack

## <a name="key-vault-in-azure-stack"></a>Key Vault in Azure Stack

Key Vault in Azure Stack unterstützt Sie dabei, kryptografische Schlüssel und Geheimnisse zu schützen, die von Cloudanwendungen und -diensten verwendet werden. Mit Key Vault können Sie Schlüssel und Geheimnisse verschlüsseln.

Gehen Sie folgendermaßen vor, um einen Schlüsseltresor zu erstellen:

1. Melden Sie sich beim Azure Stack-Portal an.

2. Wählen Sie im Dashboard **+ Ressource erstellen**, **Sicherheit + Identität** und dann **Schlüsseltresor** aus.

   ![Erstellen eines Schlüsseltresors](media/azure-stack-key-vault-store-credentials/create-key-vault.png)

3. Weisen Sie Ihrem Tresor im Bereich **Schlüsseltresor erstellen** unter **Name** einen Namen zu. Tresornamen dürfen nur alphanumerische Zeichen und Bindestriche (-) enthalten. Sie sollten nicht mit einer Zahl beginnen.

4. Wählen Sie aus der Liste der verfügbaren Abonnements ein Abonnement aus.

5. Wählen Sie eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine neue.

6. Wählen Sie den Tarif aus.

7. Wählen Sie eine der vorhandenen Zugriffsrichtlinien aus, oder erstellen Sie eine neue. Mithilfe einer Zugriffsrichtlinie können Sie einen Benutzer, eine Anwendung oder eine Sicherheitsgruppe dazu berechtigen, Vorgänge mit diesem Tresor auszuführen.

8. Wählen Sie optional eine erweiterte Zugriffsrichtlinie aus, um den Zugriff auf Features zu ermöglichen.

9. Nachdem Sie die Einstellungen konfiguriert haben, wählen Sie **OK** aus, und wählen Sie dann **Erstellen** aus. Die Key Vault-Bereitstellung beginnt.

## <a name="create-a-service-principal"></a>Erstellen eines Dienstprinzipals

1. Melden Sie sich über das Azure-Portal bei Ihrem Azure-Konto an.

2. Wählen Sie **Azure Active Directory**, **App-Registrierungen** und dann **Hinzufügen** aus.

3. Geben Sie einen Namen und eine URL für die Anwendung an. Wählen Sie als Typ für die zu erstellende Anwendung entweder **Web-App/API** oder **Nativ** aus. Wählen Sie nach dem Festlegen der Werte **Erstellen** aus.

4. Wählen Sie **Active Directory**, **App-Registrierungen** und dann Ihre Anwendung aus.

5. Kopieren Sie die **Anwendungs-ID**, und speichern Sie sie in Ihrem Anwendungscode. In den Beispielanwendungen wird für die **Anwendungs-ID** der Begriff **Client-ID** verwendet.

6. Wählen Sie zum Generieren eines Authentifizierungsschlüssels die Option **Schlüssel** aus.

7. Geben Sie eine Beschreibung des Schlüssels und eine Dauer ein.

8. Wählen Sie **Speichern** aus.

9. Kopieren Sie den **Schlüssel**, der nach dem Klicken auf **Speichern** verfügbar wurde.

## <a name="store-the-service-principal-inside-key-vault"></a>Speichern des Dienstprinzipals in Key Vault

1. Melden Sie sich beim Benutzerportal für Azure Stack an, wählen Sie den zuvor erstellten Schlüsseltresor und dann die Kachel **Geheimnis** aus.

2. Wählen Sie im Bereich **Geheimnis** die Option **Generieren/importieren** aus.

3. Wählen Sie im Bereich **Geheimnis erstellen** in der Liste der Optionen **Manuell** aus. Wenn Sie den Dienstprinzipal mithilfe von Zertifikaten erstellt haben, wählen Sie die Zertifikate aus der Dropdownliste aus, und laden Sie dann die Datei hoch.

4. Geben Sie als Namen für Ihren Schlüssel die **Anwendungs-ID** ein, die Sie vom Dienstprinzipal kopiert haben. Schlüsselnamen dürfen nur alphanumerische Zeichen und Bindestriche (-) enthalten.

5. Fügen Sie den Wert des Schlüssels vom Dienstprinzipal auf der Registerkarte **Wert** ein.

6. Wählen Sie **Dienstprinzipal** als **Inhaltstyp** aus.

7. Legen Sie Werte für das **Aktivierungsdatum** und das **Ablaufdatum** für Ihren Schlüssel fest.

8. Wählen Sie **Erstellen** aus, um die Bereitstellung zu starten.

Nach dem Erstellen des Geheimnisses werden die Dienstprinzipalinformationen dort gespeichert. Sie können sie jederzeit unter **Geheimnisse** auswählen und die Eigenschaften anzeigen oder ändern. Der Abschnitt „Eigenschaften“ enthält den Geheimnisbezeichner. Hierbei handelt es sich um einen URI (Uniform Resource Identifier), über den externe Anwendungen auf dieses Geheimnis zugreifen.

## <a name="next-steps"></a>Nächste Schritte

- [Verwenden von Dienstprinzipalen](azure-stack-create-service-principals.md)
- [Verwalten von Key Vault in Azure Stack über das Portal](azure-stack-key-vault-manage-portal.md)  
- [Verwalten von Key Vault in Azure Stack mithilfe von PowerShell](azure-stack-key-vault-manage-powershell.md)