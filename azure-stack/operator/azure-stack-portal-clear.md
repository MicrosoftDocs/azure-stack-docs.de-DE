---
title: Bedarfsgesteuertes Löschen von Portalbenutzerdaten aus Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie als Azure Stack-Operator, wie Sie Portalbenutzerdaten löschen, wenn dies von Azure Stack-Benutzern angefordert wird.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.date: 09/10/2019
ms.author: sethm
ms.reviewer: troettinger
ms.lastreviewed: 09/10/2019
monikerRange: azs-1802
ms.openlocfilehash: 2dd88656491a474e4082ff4e8321af836776b1f0
ms.sourcegitcommit: 451cfaa24b349393f36ae9d646d4d311a14dd1fd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2019
ms.locfileid: "72019114"
---
# <a name="clear-portal-user-data-from-azure-stack"></a>Löschen von Portalbenutzerdaten aus Azure Stack

Azure Stack-Operatoren können Portalbenutzerdaten nach Bedarf löschen, wenn dies von Azure Stack-Benutzer angefordert wird. Als Azure Stack-Benutzer können Sie das Portal an Ihre Anforderungen anpassen, indem Sie Kacheln anheften und das Dashboardlayout ändern. Benutzer können auch das Design ändern und die Standardsprache an die persönlichen Vorlieben anpassen. 

Die Portalbenutzerdaten umfassen Favoriten und die zuletzt abgerufenen Ressourcen im Azure Stack-Benutzerportal. In diesem Artikel wird beschrieben, wie Sie die Portalbenutzerdaten löschen.

Portalbenutzereinstellungen sollten erst nach dem Löschen des Benutzerabonnements entfernt werden.

> [!NOTE]
> Nach dem Befolgen der Anweisungen in diesem Artikel können im Systemabschnitt der Ereignisprotokolle noch einige Benutzerdaten vorhanden sein. Diese Daten werden möglicherweise für einige Tage beibehalten, bis für die Protokolle automatisch ein Rollover ausgeführt wird.

## <a name="requirements"></a>Requirements (Anforderungen)

- [Installieren Sie PowerShell für Azure Stack](azure-stack-powershell-install.md).
- [Laden Sie die aktuellen Azure Stack-Tools aus GitHub herunter](azure-stack-powershell-download.md).
- Das Benutzerkonto muss noch im Verzeichnis vorhanden sein.
- Der Azure Stack-Administrator benötigt Anmeldeinformationen für den Zugriff auf den Resource Manager-Endpunkt des Administrators.

> [!NOTE]
> Wenn Sie versuchen, Portalbenutzerinformationen von einem Benutzer zu löschen, der über ein Gästeverzeichnis eingeladen wurde (Mehrmandantenfähigkeit), müssen Sie in diesem Verzeichnis über Leseberechtigung verfügen. Weitere Informationen finden Sie im [CSP-Szenario](#clear-portal-user-data-in-guest-directory) weiter unten in diesem Artikel.

## <a name="clear-portal-user-data-using-a-user-principal-name"></a>Löschen von Portalbenutzerdaten mithilfe eines Benutzerprinzipalnamens

In diesem Szenario wird davon ausgegangen, dass das Standardanbieterabonnement und der Benutzer demselben Verzeichnis angehören oder dass Sie Lesezugriff auf das Verzeichnis haben, in dem sich der Benutzer befindet.

Stellen Sie sicher, dass Sie die [aktuelle Version der Azure Stack-Tools](azure-stack-powershell-download.md) von GitHub herunterladen, bevor Sie fortfahren.

Verwenden Sie für diesen Vorgang einen Computer, der mit dem Resource Manager-Endpunkt des Administrators von Azure Stack kommunizieren kann.

1. Öffnen Sie eine Windows PowerShell-Sitzung mit erhöhten Rechten (als Administrator ausführen), navigieren Sie zum Stammordner im Verzeichnis **AzureStack-Tools-master**, und importieren Sie das erforderliche PowerShell-Modul:

   ```powershell
   Import-Module .\DatacenterIntegration\Portal\PortalUserDataUtilities.psm1
   ```

2. Führen Sie die folgenden Befehle aus: Stellen Sie sicher, dass Sie die Platzhalter durch Werte ersetzen, die Ihrer Umgebung entsprechen.

   ```powershell
   ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.

   $adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

   ## Replace the following value with the Azure Stack directory tenant ID.
   $azureStackDirectoryTenantId = "f5025bf2-547f-4b49-9693-6420c1d5e4ca"

   ## Replace the following value with the user directory tenant ID.
   $userDirectoryTenantId = " 7ddf3648-9671-47fd-b63d-eecd82ed040e"

   ## Replace the following value with name of the user principal whose portal user data is to be cleared.
   $userPrincipalName = "myaccount@contoso.onmicrosoft.com"

   Clear-AzsUserDataWithUserPrincipalName -AzsAdminArmEndpoint $adminARMEndpoint `
    -AzsAdminDirectoryTenantId $azureStackDirectoryTenantId `
    -UserPrincipalName $userPrincipalName `
    -DirectoryTenantId $userDirectoryTenantId
   ```

   > [!NOTE]
   > `azureStackDirectoryTenantId` ist optional. Wenn Sie diesen Wert nicht angeben, sucht das Skript in allen Mandantenverzeichnissen, die in Azure Stack registriert sind, nach dem Benutzerprinzipalnamen und löscht dann die Portaldaten für alle übereinstimmenden Benutzer.

## <a name="clear-portal-user-data-in-guest-directory"></a>Löschen von Portalbenutzerdaten im Gastverzeichnis

In diesem Szenario hat der Azure Stack-Operator keinen Zugriff auf das Gastverzeichnis, in dem sich der Benutzer befindet. Dies ist ein gängiges Szenario für Cloudlösungsanbieter (Cloud Solution Provider, CSP).

Damit ein Azure Stack-Operator die Portalbenutzerdaten entfernen kann, ist mindestens die Benutzerobjekt-ID erforderlich.

Der Benutzer muss die Objekt-ID abfragen und für den Azure Stack-Operator bereitstellen. Der Operator kann nicht auf das Verzeichnis zugreifen, in dem sich der Benutzer befindet.

### <a name="user-retrieves-the-user-object-id"></a>Abrufen der Benutzerobjekt-ID durch den Benutzer

1. Öffnen Sie eine Windows PowerShell-Sitzung mit erhöhten Rechten (als Administrator ausführen), navigieren Sie zum Stammordner im Verzeichnis **AzureStack-Tools-master**, und importieren Sie das erforderliche PowerShell-Modul.

   ```powershell
   Import-Module .\DatacenterIntegration\Portal\PortalUserDataUtilities.psm1
   ```

2. Führen Sie die folgenden Befehle aus: Stellen Sie sicher, dass Sie die Platzhalter durch Werte ersetzen, die Ihrer Umgebung entsprechen.

   ```powershell
   ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
   $userARMEndpoint = "https://management.local.azurestack.external"

   ## Replace the following value with the directory tenant ID, which contains the user account.
   $userDirectoryTenantId = "3160cbf5-c227-49dd-8654-86e924c0b72f"

   ## Replace the following value with the name of the user principal whose portal user data is to be cleared.
   $userPrincipleName = "myaccount@contoso.onmicrosoft.com"

   Get-UserObjectId -DirectoryTenantId $userDirectoryTenantId `
    -AzsArmEndpoint $userARMEndpoint `
    -UserPricinpalName $userPrincipleName
   ```

   > [!NOTE]
   > Als Benutzer müssen Sie dem Azure Stack-Operator die Benutzerobjekt-ID bereitstellen. Das ist die Ausgabe des vorherigen Skripts.

## <a name="azure-stack-operator-removes-the-portal-user-data"></a>Entfernen der Portalbenutzerdaten durch den Azure Stack-Operator

Nachdem Sie als Azure Stack-Operator die Benutzerobjekt-ID erhalten haben, führen Sie die folgenden Befehle aus, um die Portalbenutzerdaten zu entfernen:

1. Öffnen Sie eine Windows PowerShell-Sitzung mit erhöhten Rechten (als Administrator ausführen), navigieren Sie zum Stammordner im Verzeichnis **AzureStack-Tools-master**, und importieren Sie das erforderliche PowerShell-Modul.

   ```powershell
   Import-Module .\DatacenterIntegration\Portal\PortalUserDataUtilities.psm1
   ```

2. Führen Sie die folgenden Befehle aus, und stellen Sie dabei sicher, dass Sie den Parameter entsprechend Ihrer Umgebung anpassen:

   ```powershell
   ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
   $AzsAdminARMEndpoint = "https://adminmanagement.local.azurestack.external"

   ## Replace the following value with the Azure Stack directory tenant ID.
   $AzsAdminDirectoryTenantId = "f5025bf2-547f-4b49-9693-6420c1d5e4ca"
   
   ## Replace the following value with the directory tenant ID of the user to clear.
   $DirectoryTenantId = "3160cbf5-c227-49dd-8654-86e924c0b72f"

   ## Replace the following value with the name of the user principal whose portal user data is to be cleared.
   $userObjectID = "s-1-*******"
   Clear-AzsUserDataWithUserObject -AzsAdminArmEndpoint $AzsAdminARMEndpoint `
    -AzsAdminDirectoryTenantId $AzsAdminDirectoryTenantId `
    -DirectoryTenantID $DirectoryTenantId `
    -UserObjectID $userObjectID `
   ```

## <a name="next-steps"></a>Nächste Schritte

- [Registrieren Sie sich für Azure Stack in Azure](azure-stack-registration.md), und füllen Sie den [Azure Stack-Marketplace](azure-stack-marketplace.md) mit Elementen auf, die Sie für Ihre Benutzer anbieten möchten.
