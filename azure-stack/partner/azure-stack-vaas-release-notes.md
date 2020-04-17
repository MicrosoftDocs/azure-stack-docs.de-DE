---
title: Versionshinweise zu Validation-as-a-Service
titleSuffix: Azure Stack Hub
description: Hier finden Sie Versionshinweise zu Validation-as-a-Service in Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: b842ea5de68cf64d498ec37ed86bb6e15b851395
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "79097754"
---
# <a name="release-notes-for-validation-as-a-service"></a>Versionshinweise zu Validation-as-a-Service

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Dieser Artikel enthält die Versionshinweise zu Validation-as-a-Service in Azure Stack Hub.

## <a name="version-4432"></a>Version 4.4.3.2

2\. Februar 2020

- Wartungsupdates für den Dienst.

- Behebung von Programmfehlern
  - Die Tests „Test101LinuxEmptyAttachedDiskManagedDisk“ und „Test101WindowsEmptyAttachedDiskManagedDisk“ wurden korrigiert.

## <a name="version-4421"></a>Version 4.4.2.1

9\. Januar 2020

- Updates für Testinhalte:
  - OEM-Validierungsworkflow (Version 5.1.52.0 -> 5.1.53.0): Verringern Sie die Anzahl der erforderlichen Parameter aus dem Bereich für den Testzeitplan.
  - Fehlerbehebung für Compute-Test – TestVMOperations

- Bekannte Probleme:
  - Wenden Sie sich an vaashelp@microsoft.com, wenn die folgenden Testfälle im Rahmen des OEM-Validierungsworkflows nicht ausgeführt werden können:
    - Test101LinuxEmptyAttachedDiskManagedDisk
    - Test101WindowsEmptyAttachedDiskManagedDisk

3\. Dezember 2019

- Updates für Testinhalte:
  - Die Onlinedokumentation für den Workflow des monatlichen Azure Stack Hub-Updates und den Workflow der OEM-Paketüberprüfung wurde aktualisiert. Lesen Sie die aktualisierte Dokumentation zum [Überprüfen von OEM-Paketen](azure-stack-vaas-validate-oem-package.md) und die aktualisierte Dokumentation zum [Überprüfen der Softwareupdates](azure-stack-vaas-validate-microsoft-updates.md) von Microsoft.
  - Aktualisierung des Workflows für die VaaS-Paketvalidierung: Für die monatliche Azure Stack Hub-Updateüberprüfung und die OEM-Paketvalidierung ist lediglich der OEM-Validierungsworkflow als Test erforderlich. Der Test aktualisiert den Stempel mit den bereitgestellten Azure Stack-/OEM-Paketen und führt Überprüfungstests des Cloud-Simulationsmoduls durch.
  - Aktualisierung der VaaS-PowerShell-Erweiterung: Die Automatisierung des Paketvalidierungsworkflows wird nun unterstützt. Ausführliche Informationen zum Speicherort dieser Erweiterung sowie eine ausführliche Verwendungsanleitung finden Sie im Artikel zur Automatisierung von Azure Stack Hub VaaS mit PowerShell.

- Bekannte Probleme:
  - Wenden Sie sich an vaashelp@microsoft.com, wenn die folgenden Testfälle im Rahmen des OEM-Validierungsworkflows nicht ausgeführt werden können:
    - Test101LinuxEmptyAttachedDiskManagedDisk
    - Test101WindowsEmptyAttachedDiskManagedDisk


## <a name="version-4353"></a>Version 4.3.5.3

7\. November 2019

- Updates für Testinhalte:
  - Monatliche Azure Stack Hub-Updateüberprüfung (Version 5.1.46.0 -> 5.1.49.0)
  - Überprüfung des OEM-Erweiterungspakets (Version 5.1.46.0 -> 5.1.49.0)
  - Ergebnisse für 5.1.46.0 wurden beibehalten. Benachrichtigen Sie bei erfolgreicher Ausführung unter 5.1.46.0 vaashelp@microsoft.com bei der Ergebnisübermittlung.

- Behebung von Programmfehlern
  - Es wurde ein Problem behoben, aufgrund dessen bei der Ausführung der monatlichen Azure Stack Hub-Updateüberprüfung ein Fehler auftrat, wenn die ZIP-Datei mit dem Update Sonderzeichen enthielt.

- Bekannte Probleme
  - Fehler bei VaaS-Tests, wenn „mstest.exe“ nicht gefunden wird. Problemumgehung:
    1. Drücken Sie im PowerShell-Fenster STRG+C für den Agent.
    1. Geben Sie „mstest.exe“ ein, um zu überprüfen, ob das Programm „mstest.exe“ erkannt wird.
    1. Wird „mstest.exe“ nicht erkannt, schließen Sie das aktuelle PowerShell-Fenster.
    1. Klicken Sie auf „Start“ (nicht auf „PowerShell“ auf der Taskleiste), suchen Sie PowerShell, und öffnen Sie die Shell als Administrator.
    1. Geben Sie „mstest.exe“ ein, und vergewissern Sie sich, dass dieser Befehl verfügbar ist.
    1. Starten Sie den Agent neu, und wiederholen Sie den Test.
  - Das Cloud-Simulationsmodul meldet gelegentlich Fehler bei \*VM-Tests. Schreiben Sie an vaashelp@microsoft.com, bevor Sie die Ausführung wiederholen.


29. Oktober 2019

- Die Onlinedokumentation für den Workflow des monatlichen Azure Stack Hub-Updates und den Workflow der OEM-Paketüberprüfung wurde aktualisiert.

    Lesen Sie die aktualisierte Dokumentation zum [Überprüfen von OEM-Paketen](azure-stack-vaas-validate-oem-package.md) und die aktualisierte Dokumentation zum [Überprüfen der Softwareupdates](azure-stack-vaas-validate-microsoft-updates.md) von Microsoft.
- Aktualisierung des VaaS-Workflows: Monatliches Azure Stack Hub-Update (Version 5.1.30.0 bis 5.1.46.0): Der Testworkflow für die monatliche Azure Stack Hub-Updateüberprüfung wurde aktualisiert.

    Der Workflow erfordert keinen manuellen Eingriff mehr und kann für eine nahtlose Ausführung geplant werden.
- Aktualisierung des VaaS-Workflows: OEM-Paketüberprüfung (Version 5.1.30.0 bis 5.1.46.0): Der Workflow für die OEM-Paketüberprüfung wurde aktualisiert.

    Der Workflow erfordert keinen manuellen Eingriff mehr und kann für eine nahtlose Ausführung geplant werden.
- Das Cloud-Simulationsmodul im OEM-Paketvalidierungsworkflow (Version 5.1.30.0 bis 5.1.46.0) wurde aktualisiert, um die Dauer der Validierung zu verkürzen: Die Laufzeit wurde auf 1 Stunde reduziert.
- Das Cloudsimulationsmodul im OEM-Paketüberprüfungsworkflow und im Azure Stack Hub-Updateworkflow (Version 5.1.30.0 -> 5.1.46.0) setzt voraus, dass die Updates in zwei unterschiedlichen übergeordneten Ordnern ohne andere Updates in untergeordneten Ordnern überprüft werden.
- Das Cloudsimulationsmodul im OEM-Paketüberprüfungsworkflow und im Azure Stack Hub-Updateworkflow (Version 5.1.30.0 bis 5.1.46.0) setzt voraus, dass die Tests in der folgenden Reihenfolge geplant werden: Test für die monatliche Azure Stack Hub-Updateüberprüfung, Test für die OEM-Erweiterungspaketüberprüfung und Cloudsimulationsmodul.
- Aktualisierung des VaaS-Agents: Der aktualisierte VaaS-Agent nutzt nun die Anmeldeinformationen des Azure Stack Hub-Cloudadministrators, um den Stempel abzufragen und die Stempelinformationen zum automatischen Auffüllen der Workflows zu erhalten.

    Bei diesem Update müssen alle Agents aktualisiert und neu gestartet werden. Weitere Informationen zum Aktualisieren des VaaS-Agents finden Sie in den folgenden Anweisungen: https://docs.microsoft.com/azure-stack/partner/azure-stack-vaas-local-agent.
- Aktualisierung der VaaS-Portalbenutzeroberfläche: Die Agent-Auswahltabelle wurde verschoben und befindet sich nun oberhalb des Bereichs für die Testplanung, um die Ausführung von Tests zu erleichtern.

    Beim Planen eines Auftrags müssen keine Stempelinformationen mehr eingegeben werden, wenn die VaaS-Agents ordnungsgemäß aktualisiert wurden.

## <a name="version-405"></a>Version 4.0.5

7\. Juni 2019

- Das Cloud-Simulationsmodul im Paketvalidierungsworkflow wurde aktualisiert, um die Dauer der Validierung zu verkürzen:  
    Laufzeit: Reduzierung auf 6 Stunden  
    Version: 5.1.13.0 -> 5.1.22.0  


17. Januar 2019

- Festplattenidentifikationstest aktualisiert, um Inkonsistenzen im Speicherpool zu beheben. Version: 5.1.14.0 -> 5.1.15.0
- Die monatliche Aktualisierungsüberprüfung für Azure Stack Hub wurde aktualisiert, um Inkonsistenzen bei der Software- und Inhaltsvalidierung zu beheben. Version: 5.1.14.0 -> 5.1.17.0
- Die Überprüfung für OEM-Erweiterungspaket wurde aktualisiert, um die notwendigen Prüfungen vor dem Azure Stack Hub-Aktualisierungsschritt durchzuführen. Version: 5.1.14.0 -> 5.1.16.0
- Interne Fehlerkorrekturen

## <a name="version-403"></a>Version 4.0.3

7\. Januar 2019

Wenn Sie den Workflow für die monatliche Updateüberprüfung für Azure Stack Hub ausführen und die Version für Ihr OEM-Updatepaket nicht 1810 oder höher ist, erhalten Sie einen Fehler, sobald Sie zum Schritt für das OEM-Update gelangen. Dieser Fehler wird irrtümlich angezeigt. Wir arbeiten derzeit an der Behebung des Fehlers. Schritte zur Behebung:

1. Führen Sie das OEM-Update wie gewohnt aus.
2. Führen Sie nach der erfolgreichen Anwendung des Pakets „Test-AzureStack“ aus, und speichern Sie die Ausgabe.
3. Brechen Sie den Test ab.
4. Senden Sie die gespeicherte Ausgabe an VaaSHelp@microsoft.com, um erfolgreiche Ergebnisse für die Ausführung zu erhalten.

## <a name="version-402"></a>Version 4.0.2

30. November 2018

- Interne Fehlerkorrekturen

## <a name="version-401"></a>Version 4.0.1

8\. Oktober 2018

- Voraussetzungen für VaaS:

    `Install-VaaSPrerequisites` erfordert keine Cloudadministrator-Anmeldeinformationen mehr. Wenn Sie die neueste Version dieses Cmdlets ausführen, finden Sie die überarbeiteten Befehle für die Installation der Voraussetzungen unter [Herunterladen und Installieren des lokalen Agents](azure-stack-vaas-local-agent.md#download-and-install-the-local-agent). Hier sind die Befehle angegeben:

    ```powershell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region
    ```

## <a name="version-400"></a>Version 4.0.0

29. August 2018

- Voraussetzungen für VaaS und VHD-Updates:

    `Install-VaaSPrerequisites` erfordert nun Cloudadministrator-Anmeldeinformationen aufgrund eines Problems bei der Paketvalidierung. Die Dokumentation unter [Herunterladen und Installieren des lokalen Agents](azure-stack-vaas-local-agent.md#download-and-install-the-local-agent) wurde mit dem folgenden Code aktualisiert:

    ```powershell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    $CloudAdminCreds = New-Object System.Management.Automation.PSCredential "<cloudAdminDomain\username>", (ConvertTo-SecureString "<cloudAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region `
                              -CloudAdminCredentials $CloudAdminCreds
    ```
    > [!NOTE]
    > Die im Skript erforderliche Angabe `$CloudAdminCreds` wird für die Überprüfung der Azure Stack Hub-Instanz benötigt. Es handelt sich nicht um die Azure Active Directory-Anmeldeinformationen, die vom VaaS-Mandanten verwendet werden.

- Aktualisierung des lokalen Agents:

    Die vorherige Version des lokalen Agents ist nicht mit der aktuellen Version 4.0.0 des Diensts kompatibel. Alle Benutzer müssen ihre lokalen Agents aktualisieren. Anweisungen zum Installieren des neuesten Agents finden Sie unter [Herunterladen und Installieren des lokalen Agents](azure-stack-vaas-local-agent.md#download-and-install-the-local-agent).

- Aktualisierung der PowerShell-Automatisierung:

    Es wurden Änderungen an den PowerShell-Skripts `LaunchVaaSTests` vorgenommen, die die neueste Version der Skripterstellungspakete erfordern. Anweisungen zum Installieren der neuesten Version des Skripterstellungspakets finden Sie unter [Starten des Testdurchlauf-Workflows](azure-stack-vaas-automate-with-powershell.md).

- Validation-as-a-Service-Portal:

  - Benachrichtigungen bei der Paketsignierung

    Wenn ein OEM-Anpassungspaket im Rahmen des Paketvalidierungsworkflows gesendet wird, wird das Paketformat überprüft, um sicherzustellen, dass es die veröffentlichte Spezifikation erfüllt. Wenn das Paket sie nicht erfüllt, tritt bei der Ausführung ein Fehler auf. E-Mail-Benachrichtigungen werden an die E-Mail-Adresse des registrierten Azure Active Directory-Kontakts für den Mandanten gesendet.

  - Testkategorie „Interaktiv“:

    Die Testkategorie **Interaktiv** wurde hinzugefügt. Mit diesen Tests werden interaktive, nicht automatisierte Azure Stack Hub-Szenarios durchgespielt.

  - Interaktive Featureüberprüfung:

    Die Möglichkeit, spezifisches Feedback für bestimmte Features abzugeben, ist jetzt in den Testdurchlauf-Workflows verfügbar. Der Test `OEM Update on Azure Stack Hub 1806 RC Validation 5.1.4.0` überprüft, ob bestimmte Updates richtig angewandt wurden, und sammelt dann Feedback.

## <a name="next-steps"></a>Nächste Schritte

- [Problembehandlung für Validation-as-a-Service](azure-stack-vaas-troubleshoot.md)
