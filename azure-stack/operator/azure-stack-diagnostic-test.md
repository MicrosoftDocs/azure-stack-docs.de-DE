---
title: Überprüfen des Systemzustands mit dem Azure Stack Hub-Überprüfungstool
description: Es wird beschrieben, wie Sie das Azure Stack Hub-Überprüfungstool zum Überprüfen des Systemstatus verwenden.
author: PatAltimore
ms.topic: article
ms.date: 01/10/2020
ms.author: patricka
ms.reviewer: adshar
ms.lastreviewed: 01/10/2020
ms.openlocfilehash: b76275ba3f4a89196271ae899bd8a30e5819ee6c
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98255944"
---
# <a name="validate-azure-stack-hub-system-state"></a>Überprüfen des Azure Stack Hub-Systemstatus

Es ist sehr wichtig, dass Sie als Azure Stack Hub-Operator bei Bedarf die Integrität und den Status Ihres Systems ermitteln können. Das Azure Stack Hub-Überprüfungstool (**Test-AzureStack**) ist ein PowerShell-Cmdlet, mit dem Sie eine Reihe von Tests auf Ihrem System ausführen können, um vorliegende Fehler zu identifizieren. In der Regel werden Sie aufgefordert, dieses Tool über den [privilegierten Endpunkt (PEP)](azure-stack-privileged-endpoint.md) auszuführen, wenn Sie sich wegen eines Problems an den Microsoft-Support wenden. Wenn die systemweiten Integritäts- und Statusinformationen vorliegen, kann der Microsoft-Support detaillierte Protokolle sammeln und analysieren, sich auf den Bereich konzentrieren, in dem der Fehler aufgetreten ist, und mit Ihnen zusammenarbeiten, um das Problem zu beheben.

## <a name="running-the-validation-tool-and-accessing-results"></a>Ausführen des Überprüfungstools und Zugreifen auf die Ergebnisse

Wie oben bereits erwähnt, wird das Überprüfungstool über den PEP ausgeführt. Jeder Test gibt im PowerShell-Fenster einen **PASS/FAIL**-Status zurück. Hier ist eine Gliederung des Prozesses für End-to-End-Validierungstests angegeben:

1. Richten Sie die Vertrauensstellung ein. Führen Sie auf einem integrierten System in einer Windows PowerShell-Sitzung mit erhöhten Rechten den folgenden Befehl aus, um den PEP als vertrauenswürdigen Host auf der gehärteten virtuellen VM hinzuzufügen, die auf dem Hardwarelebenszyklus-Host oder der Arbeitsstation mit privilegiertem Zugriff ausgeführt wird.

   ```powershell
   winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
   ```

   Wenn Sie das ASDK (Azure Stack Development Kit) ausführen, melden Sie sich beim Development Kit-Host an.

1. Greifen Sie auf den PEP zu. Führen Sie die folgenden Befehle aus, um eine PEP-Sitzung einzurichten:

   ```powershell
   Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
   ```

   > [!TIP]
   > Verwenden Sie „AzS-ERCS01“ als „-ComputerName“, um auf den PEP auf einem ASDK-Hostcomputer (Azure Stack Development Kit) zuzugreifen.

1. Führen Sie auf dem PEP Folgendes aus:

   ```powershell
   Test-AzureStack
   ```

   Weitere Informationen finden Sie unter [Überlegungen zu Parametern](azure-stack-diagnostic-test.md#parameter-considerations) und [Beispiele für Anwendungsfälle](azure-stack-diagnostic-test.md#use-case-examples).

1. Führen Sie `Get-AzureStackLog` aus, wenn für Tests **FAIL** (FEHLER) gemeldet wird. Eine Anleitung zu einem integrierten System finden Sie unter [Ausführen von „Get-AzureStackLog“ in integrierten Azure Stack Hub-Systemen](azure-stack-get-azurestacklog.md).

   Das Cmdlet sammelt Protokolle, die von Test-AzureStack generiert wurden. Wir empfehlen Ihnen, keine Protokolle zu erfassen und sich stattdessen an den Microsoft-Support zu wenden, wenn bei Tests eine **WARNUNG** gemeldet wird.

1. Wenn Sie vom Microsoft-Support angewiesen wurden, das Überprüfungstool auszuführen, fordert der Mitarbeiter des Microsoft-Supports die von Ihnen gesammelten Protokolle an, um die Behebung Ihres Problems fortsetzen zu können.

## <a name="tests-available"></a>Verfügbare Tests

Mithilfe des Überprüfungstools können Sie eine Reihe von Tests auf Systemebene sowie grundlegende Cloudszenarien ausführen, die Ihnen einen Einblick in den aktuellen Status ermöglichen, um Probleme in Ihrem System zu beheben.

### <a name="cloud-infrastructure-tests"></a>Tests der Cloudinfrastruktur

Diese Tests mit geringer Auswirkung arbeiten auf einer Infrastrukturebene und liefern Ihnen Informationen zu verschiedenen Systemkomponenten und -funktionen. Tests sind derzeit in die folgenden Kategorien gruppiert:

| Testkategorie                                        | Argument für „-Include“ und „-Ignore“ |
| :--------------------------------------------------- | :-------------------------------- |
| Zusammenfassung des Azure Stack Hub ACS                              | AzsAcsSummary                     |
| Zusammenfassung von Azure Stack Hub: Active Directory                 | AzsAdSummary                      |
| Azure Stack Hub: Benachrichtigungszusammenfassung                            | AzsAlertSummary                   |
| Zusammenfassung der Azure Stack Hub-Anwendungsabstürze                | AzsApplicationCrashSummary        |
| Zusammenfassung der Verfügbarkeit von Azure Stack Hub-Sicherungsfreigaben       | AzsBackupShareAccessibility       |
| Zusammenfassung des Azure Stack Hub BMC                              | AzsStampBMCSummary                |
| Zusammenfassung der Cloudhostinginfrastruktur für Azure Stack Hub     | AzsHostingInfraSummary            |
| Nutzung der Cloudhostinginfrastruktur für Azure Stack Hub | AzsHostingInfraUtilization        |
| Zusammenfassung der Azure Stack Hub-Steuerungsebene                    | AzsControlPlane                   |
| Zusammenfassung von Azure Stack Hub Defender                         | AzsDefenderSummary                |
| Zusammenfassung Hostinginfrastruktur-Firmware für Azure Stack Hub  | AzsHostingInfraFWSummary          |
| Kapazität der Azure Stack Hub-Infrastruktur                  | AzsInfraCapacity                  |
| Leistung der Azure Stack Hub-Infrastruktur               | AzsInfraPerformance               |
| Zusammenfassung der Infrastrukturrolle von Azure Stack Hub              | AzsInfraRoleSummary               |
| Azure Stack Hub-Netzwerkinfrastruktur                            | AzsNetworkInfra                   |
| Zusammenfassung von Azure Stack Hub-Portal und API                   | AzsPortalAPISummary               |
| VM-Ereignisse der Azure Stack Hub-Skalierungseinheit                     | AzsScaleUnitEvents                |
| VM-Ressourcen der Azure Stack Hub-Skalierungseinheit                  | AzsScaleUnitResources             |
| Azure Stack Hub-Szenarien                                | AzsScenarios                      |
| Zusammenfassung der Azure Stack Hub SDN-Überprüfung                   | AzsSDNValidation                  |
| Zusammenfassung der Azure Stack Hub Service Fabric-Rolle              | AzsSFRoleSummary                  |
| Azure Stack Hub-Speicherdatenebene                       | AzsStorageDataPlane               |
| Zusammenfassung der Azure Stack Hub-Speicherdienste                 | AzsStorageSvcsSummary             |
| Zusammenfassung des Azure Stack Hub-SQL-Stores                        | AzsStoreSummary                   |
| Zusammenfassung des Azure Stack Hub-Updates                           | AzsInfraUpdateSummary             |
| Zusammenfassung der Azure Stack Hub-VM-Platzierung                     | AzsVmPlacement                    |

### <a name="cloud-scenario-tests"></a>Cloudszenariotests

Zusätzlich zu den obigen Infrastrukturtests können Sie auch Cloudszenariotests durchführen, um die Funktionalität für Infrastrukturkomponenten zu überprüfen. Da in diesen Tests die Bereitstellung von Ressourcen enthalten ist, sind für deren Durchführung Anmeldeinformationen für Cloudadministratoren erforderlich.

> [!NOTE]
> Derzeit können Sie Cloudszenariotests nicht mit AD FS-Anmeldeinformationen (Active Directory Federation Services, Active Directory-Verbunddienste) durchführen.

Die folgenden Cloudszenarien werden durch das Überprüfungstool getestet:
- Ressourcengruppenerstellung
- Planerstellung
- Angebotserstellung
- Speicherkontoerstellung
- Erstellung des virtuellen Computers (VM)
- Speichervorgang für Blobs
- Speichervorgang für Warteschlangen
- Speichervorgang für Tabellen

## <a name="parameter-considerations"></a>Überlegungen zu Parametern

- Der Parameter **List** kann verwendet werden, um alle verfügbaren Testkategorien anzuzeigen.

- Die Parameter **Include** und **Ignore** können verwendet werden, um Testkategorien einzubeziehen bzw. auszuschließen. Weitere Informationen zu diesen Argumenten finden Sie im folgenden Abschnitt.

  ```powershell
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
  ```

  ```powershell
  Test-AzureStack -Ignore AzsInfraPerformance
  ```

- Eine Mandanten-VM wird im Rahmen eines Cloudszenariotests bereitgestellt. Sie können **DoNotDeployTenantVm** verwenden, um diese VM-Bereitstellung zu deaktivieren.

- Sie müssen den Parameter **ServiceAdminCredential** angeben, um die Cloudszenariotests entsprechend der Beschreibung im Abschnitt [Beispiele für Anwendungsfälle](azure-stack-diagnostic-test.md#use-case-examples) auszuführen.

- **BackupSharePath** und **BackupShareCredential** werden beim Testen von Einstellungen für die Infrastruktursicherung verwendet, wie im Abschnitt [Beispiele für Anwendungsfälle](azure-stack-diagnostic-test.md#use-case-examples) gezeigt wird.

- **DetailedResults** kann verwendet werden, um Erfolgreich/Fehler/Warnung-Informationen für jeden Test und die Gesamtausführung zu erhalten. Wurde diese Option nicht angegeben, gibt **Test-AzureStack** **$true** zurück, wenn keine Fehler vorliegen, und **$false**, wenn Fehler aufgetreten sind.
- **TimeoutSeconds** kann verwendet werden, um eine bestimmte Zeit für jede abzuschließende Gruppe festzulegen.

- Das Überprüfungstool unterstützt auch gängige PowerShell-Parameter: Verbose, Debug, ErrorAction, ErrorVariable, WarningAction, WarningVariable, OutBuffer, PipelineVariable und OutVariable. Weitere Informationen finden Sie unter [about_Commonparameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).  

## <a name="use-case-examples"></a>Beispiele für Anwendungsfälle

### <a name="run-validation-without-cloud-scenarios"></a>Ausführen der Überprüfung ohne Cloudszenarien

Führen Sie das Überprüfungstool ohne den Parameter **ServiceAdminCredential** aus, um die Ausführung von Cloudszenariotests zu überspringen: 

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred
Test-AzureStack
```

### <a name="run-validation-with-cloud-scenarios"></a>Ausführen der Überprüfung mit Cloudszenarien

Wenn für das Überprüfungstool der Parameter **ServiceAdminCredentials** angegeben wird, werden standardmäßig die Cloudszenariotests ausgeführt: 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" 
```

Wenn Sie NUR Cloudszenarien ausführen möchten, ohne die übrigen Tests auszuführen, können Sie dazu den Parameter **Include** verwenden: 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" -Include AzsScenarios   
```

Der Benutzername des Cloudadministrators muss im UPN-Format eingegeben werden: serviceadmin@contoso.onmicrosoft.com (Azure AD). Geben Sie bei Aufforderung das Kennwort für das Cloudadministratorkonto ein.

### <a name="groups"></a>Gruppen

Um die Bedienerfreundlichkeit zu verbessern, wurde ein Parameter **Gruppe** eingeführt, um mehrere Testkategorien gleichzeitig auszuführen. Derzeit sind drei Gruppen definiert: **Default**, **UpdateReadiness** und **SecretRotationReadiness**.

- **Standard:** Standardausführung von **Test-AzureStack**. Diese Gruppe wird standardmäßig ausgeführt, wenn keine anderen Gruppen ausgewählt werden.
- **UpdateReadiness**: Hierbei wird überprüft, ob die Azure Stack Hub-Instanz aktualisiert werden kann. Wird die Gruppe **UpdateReadiness** ausgeführt, werden Warnungen als Fehler in der Konsolenausgabe angezeigt und sollten als Hindernisse für die Aktualisierung angesehen werden. Ab Azure Stack Hub-Version 1910 sind die folgenden Kategorien Teil der Gruppe **UpdateReadiness**:

  - **AzsInfraFileValidation**
  - **AzsActionPlanStatus**
  - **AzsStampBMCSummary**

- **SecretRotationReadiness**: Hierbei wird überprüft, ob sich die Azure Stack Hub-Instanz in einem Status befindet, in dem die Geheimnisrotation durchgeführt werden kann. Wird die Gruppe **SecretRotationReadiness** ausgeführt, werden Warnungen als Fehler in der Konsolenausgabe angezeigt und sollten als Hindernisse für die Geheimnisrotation angesehen werden. Die folgenden Kategorien sind Teil der SecretRotationReadiness-Gruppe:

  - **AzsAcsSummary**
  - **AzsDefenderSummary**
  - **AzsHostingInfraSummary**
  - **AzsInfraCapacity**
  - **AzsInfraRoleSummary**
  - **AzsPortalAPISummary**
  - **AzsSFRoleSummary**
  - **AzsStorageSvcsSummary**
  - **AzsStoreSummary**

#### <a name="group-parameter-example"></a>Beispiel für Gruppenparameter

Im folgenden Beispiel wird **Test-AzureStack** ausgeführt, um die Systembereitschaft zu testen, bevor ein Update oder Hotfix mit **Gruppe** installiert wird. Führen Sie vor dem Starten einer Update- oder Hotfixinstallation **Test-AzureStack** aus, um den Status Ihrer Azure Stack Hub-Instanz zu überprüfen:

```powershell
Test-AzureStack -Group UpdateReadiness
```

Wenn für Ihre Azure Stack Hub-Instanz eine ältere Version als 1811 verwendet wird, sollten Sie die folgenden PowerShell-Befehle verwenden, um **Test-AzureStack** auszuführen:

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
```

### <a name="run-validation-tool-to-test-infrastructure-backup-settings"></a>Ausführen des Überprüfungstools zum Testen der Einstellungen für die Infrastruktursicherung

*Vor* der Konfiguration der Infrastruktursicherung können Sie mit dem Test **AzsBackupShareAccessibility** den Sicherungsfreigabepfad und die Sicherungsanmeldeinformationen testen:

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupSharePath "\\<fileserver>\<fileshare>" -BackupShareCredential $using:backupcred
  ```

*Nach* der Konfiguration der Sicherung können Sie **AzsBackupShareAccessibility** ausführen, um zu überprüfen, ob über das ERCS auf die Freigabe zugegriffen werden kann:

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility
  ```

Um neue Anmeldeinformationen mit der konfigurierten Sicherungsfreigabe zu testen, führen Sie Folgendes aus: 

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupShareCredential "<PSCredential for backup share>"
  ```

### <a name="run-validation-tool-to-test-network-infrastructure"></a>Ausführen des Überprüfungstools zum Testen der Netzwerkinfrastruktur

Dieser Test überprüft die Konnektivität der Netzwerkinfrastruktur, indem er das softwaredefinierte Netzwerk (SDN) von Azure Stack Hub umgeht. Er veranschaulicht Verbindungen von einer öffentlichen VIP mit den konfigurierten DNS-Forwardern, NTP-Servern und Authentifizierungsendpunkten. Dies schließt die Konnektivität mit Azure ein, wenn Azure AD als Identitätsanbieter verwendet wird, oder mit dem Partnerserver, wenn AD FS als Identitätsanbieter verwendet wird.

Schließen Sie den Debug-Parameter ein, um eine detaillierte Ausgabe des Befehls zu erhalten:

```powershell
Test-AzureStack -Include AzsNetworkInfra -Debug
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Stack Hub-Diagnosetools und zur Protokollierung von Problemen finden Sie unter [Azure Stack Hub-Diagnosetools](./diagnostic-log-collection.md?view=azs-2002).

Weitere Informationen zur Problembehandlung finden Sie unter [Problembehandlung von Microsoft Azure Stack Hub](azure-stack-troubleshooting.md).