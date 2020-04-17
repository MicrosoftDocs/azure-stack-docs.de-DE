---
title: Bedarfsgesteuertes Löschen von Portalbenutzerdaten aus Azure Stack Hub
description: Erfahren Sie als Azure Stack Hub-Operator, wie Sie Portalbenutzerdaten löschen, wenn dies von Azure Stack Hub-Benutzern angefordert wird.
author: sethmanheim
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: sethm
ms.reviewer: troettinger
ms.lastreviewed: 09/10/2019
ms.openlocfilehash: bede8fc72cfcdd6f3376c49a87293fef3626c380
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "77698333"
---
# <a name="clear-portal-user-data-from-azure-stack-hub"></a>Löschen von Portalbenutzerdaten aus Azure Stack Hub

Azure Stack Hub-Operatoren können Portalbenutzerdaten nach Bedarf löschen, wenn dies von Azure Stack Hub-Benutzer angefordert wird. Als Azure Stack Hub-Benutzer können Sie das Portal an Ihre Anforderungen anpassen, indem Sie Kacheln anheften und das Dashboardlayout ändern. Benutzer können auch das Design ändern und die Standardsprache an die persönlichen Vorlieben anpassen.

Die Portalbenutzerdaten umfassen Favoriten und die zuletzt abgerufenen Ressourcen im Azure Stack Hub-Benutzerportal. In diesem Artikel wird beschrieben, wie Sie die Portalbenutzerdaten löschen.

Portalbenutzereinstellungen sollten erst nach dem Löschen des Benutzerabonnements entfernt werden.

> [!NOTE]
> Nach dem Befolgen der Anweisungen in diesem Artikel können im Systemabschnitt der Ereignisprotokolle noch einige Benutzerdaten vorhanden sein. Diese Daten werden möglicherweise für einige Tage beibehalten, bis für die Protokolle automatisch ein Rollover ausgeführt wird.

## <a name="requirements"></a>Requirements (Anforderungen)

- [Installieren von PowerShell für Azure Stack Hub](azure-stack-powershell-install.md)
- [Laden Sie die aktuellen Azure Stack Hub-Tools aus GitHub herunter](azure-stack-powershell-download.md).
- Das Benutzerkonto muss noch im Verzeichnis vorhanden sein.
- Der Azure Stack Hub-Administrator benötigt Anmeldeinformationen für den Zugriff auf den Resource Manager-Endpunkt des Administrators.

> [!NOTE]
> Wenn Sie versuchen, Portalbenutzerinformationen von einem Benutzer zu löschen, der über ein Gästeverzeichnis eingeladen wurde (Mehrmandantenfähigkeit), müssen Sie in diesem Verzeichnis über Leseberechtigung verfügen. Weitere Informationen finden Sie im [CSP-Szenario](#clear-portal-user-data-in-guest-directory) weiter unten in diesem Artikel.

## <a name="clear-portal-user-data-using-a-user-principal-name"></a>Löschen von Portalbenutzerdaten mithilfe eines Benutzerprinzipalnamens

In diesem Szenario wird davon ausgegangen, dass das Standardanbieterabonnement und der Benutzer demselben Verzeichnis angehören oder dass Sie Lesezugriff auf das Verzeichnis haben, in dem sich der Benutzer befindet.

Stellen Sie sicher, dass Sie die [aktuelle Version der Azure Stack Hub-Tools](azure-stack-powershell-download.md) von GitHub herunterladen, bevor Sie fortfahren.

Verwenden Sie für diesen Vorgang einen Computer, der mit dem Resource Manager-Endpunkt des Administrators von Azure Stack Hub kommunizieren kann.

1. Öffnen Sie eine Windows PowerShell-Sitzung mit erhöhten Rechten (als Administrator ausführen), navigieren Sie zum Stammordner im Verzeichnis **AzureStack-Tools-master**, und importieren Sie das erforderliche PowerShell-Modul:

   ```powershell
   Import-Module .\DatacenterIntegration\Portal\PortalUserDataUtilities.psm1
   ```

2. Führen Sie die folgenden Befehle aus: Ersetzen Sie die Platzhalter unbedingt durch Werte, die Ihrer Umgebung entsprechen:

   ```powershell
   ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.

   $adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

   ## Replace the following value with the Azure Stack Hub directory tenant ID.
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
   > `azureStackDirectoryTenantId` ist optional. Wenn Sie diesen Wert nicht angeben, sucht das Skript in allen Mandantenverzeichnissen, die in Azure Stack Hub registriert sind, nach dem Benutzerprinzipalnamen und löscht dann die Portaldaten für alle übereinstimmenden Benutzer.

## <a name="clear-portal-user-data-in-guest-directory"></a>Löschen von Portalbenutzerdaten im Gastverzeichnis

In diesem Szenario hat der Azure Stack Hub-Operator keinen Zugriff auf das Gastverzeichnis, in dem sich der Benutzer befindet. Dies ist ein gängiges Szenario für Cloudlösungsanbieter (Cloud Solution Provider, CSP).

Damit ein Azure Stack Hub-Operator die Portalbenutzerdaten entfernen kann, ist mindestens die Benutzerobjekt-ID erforderlich.

Der Benutzer muss die Objekt-ID abfragen und für den Azure Stack Hub-Operator bereitstellen. Der Operator kann nicht auf das Verzeichnis zugreifen, in dem sich der Benutzer befindet.

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
   > Als Benutzer müssen Sie dem Azure Stack Hub-Operator die Benutzerobjekt-ID bereitstellen. Das ist die Ausgabe des vorherigen Skripts.

## <a name="azure-stack-hub-operator-removes-the-portal-user-data"></a>Entfernen der Portalbenutzerdaten durch den Azure Stack Hub-Operator

Nachdem Sie als Azure Stack Hub-Operator die Benutzerobjekt-ID erhalten haben, führen Sie die folgenden Befehle aus, um die Portalbenutzerdaten zu entfernen:

1. Öffnen Sie eine Windows PowerShell-Sitzung mit erhöhten Rechten (als Administrator ausführen), navigieren Sie zum Stammordner im Verzeichnis **AzureStack-Tools-master**, und importieren Sie das erforderliche PowerShell-Modul.

   ```powershell
   Import-Module .\DatacenterIntegration\Portal\PortalUserDataUtilities.psm1
   ```

2. Führen Sie die folgenden Befehle aus, und stellen Sie dabei sicher, dass Sie den Parameter entsprechend Ihrer Umgebung anpassen:

   ```powershell
   ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
   $AzsAdminARMEndpoint = "https://adminmanagement.local.azurestack.external"

   ## Replace the following value with the Azure Stack Hub directory tenant ID.
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

- [Registrieren Sie Azure Stack Hub in Azure](azure-stack-registration.md), und füllen Sie den [Azure Stack Hub-Marketplace](azure-stack-marketplace.md) mit Elementen auf, die Sie für Ihre Benutzer anbieten möchten.
