---
title: Verwenden des Azure Stack-Überprüfungstools zum Überprüfen des Systemstatus | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie das Azure Stack-Überprüfungstool zum Überprüfen des Systemstatus verwenden.
services: azure-stack
author: justinha
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 06/26/2019
ms.author: justinha
ms.reviewer: adshar
ms.lastreviewed: 12/03/2018
ms.openlocfilehash: 194af241480cce42273ff81d91213a63b1b9fd59
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71829165"
---
# <a name="validate-azure-stack-system-state"></a>Überprüfen des Azure Stack-Systemstatus

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Es ist sehr wichtig, dass Sie als Azure Stack-Betreiber bei Bedarf die Integrität und den Status Ihres Systems ermitteln können. Das Azure Stack-Überprüfungstool (**Test-AzureStack**) ist ein PowerShell-Cmdlet, mit dem Sie eine Reihe von Tests auf Ihrem System ausführen können, um vorliegende Fehler zu identifizieren. In der Regel werden Sie aufgefordert, dieses Tool über den [privilegierten Endpunkt (PEP)](azure-stack-privileged-endpoint.md) auszuführen, wenn Sie sich wegen eines Problems an den Microsoft-Kundendienst und -Support (Microsoft Customer Services Support, CSS) wenden. Wenn die systemweiten Integritäts- und Statusinformationen vorliegen, kann CSS detaillierte Protokolle sammeln und analysieren, sich auf den Bereich konzentrieren, in dem der Fehler aufgetreten ist, und mit Ihnen zusammenarbeiten, um das Problem zu beheben.

## <a name="running-the-validation-tool-and-accessing-results"></a>Ausführen des Überprüfungstools und Zugreifen auf die Ergebnisse

Wie oben bereits erwähnt, wird das Überprüfungstool über den PEP ausgeführt. Jeder Test gibt im PowerShell-Fenster einen **PASS/FAIL**-Status zurück. Hier ist eine Gliederung des Prozesses für End-to-End-Validierungstests angegeben:

1. Greifen Sie auf den PEP zu. Führen Sie die folgenden Befehle aus, um eine PEP-Sitzung einzurichten:

   ```powershell
   Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
   ```

   > [!TIP]
   > Verwenden Sie „AzS-ERCS01“ als „-ComputerName“, um auf den PEP auf einem ASDK-Hostcomputer (Azure Stack Development Kit) zuzugreifen.

2. Führen Sie auf dem PEP Folgendes aus:

   ```powershell
   Test-AzureStack
   ```

   Weitere Informationen finden Sie unter [Überlegungen zu Parametern](azure-stack-diagnostic-test.md#parameter-considerations) und [Beispiele für Anwendungsfälle](azure-stack-diagnostic-test.md#use-case-examples).

3. Führen Sie `Get-AzureStackLog` aus, wenn für Tests **FAIL** (FEHLER) gemeldet wird. Eine Anleitung zu einem integrierten System finden Sie unter [So führen Sie „Get-AzureStackLog“ in integrierten Azure Stack-Systemen aus](azure-stack-configure-on-demand-diagnostic-log-collection.md#to-run-get-azurestacklog-on-azure-stack-integrated-systems) oder im ASDK unter [Ausführen von Get-AzureStackLog in einem System mit dem Azure Stack Development Kit (ASDK)](azure-stack-configure-on-demand-diagnostic-log-collection.md#run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system).

   Das Cmdlet sammelt Protokolle, die von Test-AzureStack generiert wurden. Wir empfehlen Ihnen, keine Protokolle zu erfassen und sich stattdessen an den Microsoft-Kundendienst und -Support (CSS) zu wenden, wenn bei Tests eine **WARNUNG** gemeldet wird.

4. Wenn Sie durch den CSS angewiesen wurden, das Überprüfungstool auszuführen, fordert der CSS-Mitarbeiter die von Ihnen gesammelten Protokolle an, um die Behebung Ihres Problems fortsetzen zu können.

## <a name="tests-available"></a>Verfügbare Tests

Mithilfe des Überprüfungstools können Sie eine Reihe von Tests auf Systemebene sowie grundlegende Cloudszenarien ausführen, die Ihnen einen Einblick in den aktuellen Status ermöglichen, um Probleme in Ihrem System zu beheben.

### <a name="cloud-infrastructure-tests"></a>Tests der Cloudinfrastruktur

Diese Tests mit geringer Auswirkung arbeiten auf einer Infrastrukturebene und liefern Ihnen Informationen zu verschiedenen Systemkomponenten und -funktionen. Tests sind derzeit in die folgenden Kategorien gruppiert:

| Testkategorie                                        | Argument für „-Include“ und „-Ignore“ |
| :--------------------------------------------------- | :-------------------------------- |
| Zusammenfassung des Azure Stack ACS                              | AzsAcsSummary                     |
| Zusammenfassung von Azure Stack: Active Directory                 | AzsAdSummary                      |
| Zusammenfassung der Azure Stack-Warnung                            | AzsAlertSummary                   |
| Zusammenfassung der Azure Stack-Anwendungsabstürze                | AzsApplicationCrashSummary        |
| Zusammenfassung der Verfügbarkeit von Azure Stack-Sicherungsfreigaben       | AzsBackupShareAccessibility       |
| Zusammenfassung des Azure Stack BMC                              | AzsStampBMCSummary                |
| Zusammenfassung der Cloudhostinginfrastruktur für Azure Stack     | AzsHostingInfraSummary            |
| Nutzung der Cloudhostinginfrastruktur für Azure Stack | AzsHostingInfraUtilization        |
| Zusammenfassung der Azure Stack-Steuerungsebene                    | AzsControlPlane                   |
| Zusammenfassung von Azure Stack Defender                         | AzsDefenderSummary                |
| Zusammenfassung Hostinginfrastruktur-Firmware für Azure Stack  | AzsHostingInfraFWSummary          |
| Kapazität der Azure Stack-Infrastruktur                  | AzsInfraCapacity                  |
| Leistung der Azure Stack-Infrastruktur               | AzsInfraPerformance               |
| Zusammenfassung der Infrastrukturrolle von Azure Stack              | AzsInfraRoleSummary               |
| Azure Stack-Netzwerkinfrastruktur                            | AzsNetworkInfra                   |
| Zusammenfassung von Azure Stack-Portal und API                   | AzsPortalAPISummary               |
| VM-Ereignisse der Azure Stack-Skalierungseinheit                     | AzsScaleUnitEvents                |
| VM-Ressourcen der Azure Stack-Skalierungseinheit                  | AzsScaleUnitResources             |
| Azure Stack-Szenarien                                | AzsScenarios                      |
| Zusammenfassung der Azure Stack SDN-Überprüfung                   | AzsSDNValidation                  |
| Zusammenfassung der Azure Stack Service Fabric-Rolle              | AzsSFRoleSummary                  |
| Azure Stack-Speicherdatenebene                       | AzsStorageDataPlane               |
| Zusammenfassung der Azure Stack-Speicherdienste                 | AzsStorageSvcsSummary             |
| Zusammenfassung des Azure Stack SQL-Stores                        | AzsStoreSummary                   |
| Zusammenfassung des Azure Stack-Updates                           | AzsInfraUpdateSummary             |
| Zusammenfassung der Azure Stack VM-Platzierung                     | AzsVmPlacement                    |

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

- Das Überprüfungstool unterstützt auch gängige PowerShell-Parameter: Verbose, Debug, ErrorAction, ErrorVariable, WarningAction, WarningVariable, OutBuffer, PipelineVariable und OutVariable. Weitere Informationen finden Sie unter [about_Commonparameters](https://go.microsoft.com/fwlink/?LinkID=113216).  

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
- **UpdateReadiness**: Hierbei wird überprüft, ob die Azure Stack-Instanz aktualisiert werden kann. Wird die Gruppe **UpdateReadiness** ausgeführt, werden Warnungen als Fehler in der Konsolenausgabe angezeigt und sollten als Hindernisse für die Aktualisierung angesehen werden. Die folgenden Kategorien sind Teil der **UpdateReadiness**-Gruppe:

  - **AzsAcsSummary**
  - **AzsDefenderSummary**
  - **AzsHostingInfraSummary**
  - **AzsInfraCapacity**
  - **AzsInfraRoleSummary**
  - **AzsPortalAPISummary**
  - **AzsSFRoleSummary**
  - **AzsStoreSummary**

- **SecretRotationReadiness**: Hierbei wird überprüft, ob sich die Azure Stack-Instanz in einem Status befindet, in dem die Geheimnisrotation durchgeführt werden kann. Wird die Gruppe **SecretRotationReadiness** ausgeführt, werden Warnungen als Fehler in der Konsolenausgabe angezeigt und sollten als Hindernisse für die Geheimnisrotation angesehen werden. Die folgenden Kategorien sind Teil der SecretRotationReadiness-Gruppe:

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

Im folgenden Beispiel wird **Test-AzureStack** ausgeführt, um die Systembereitschaft zu testen, bevor ein Update oder Hotfix mit **Gruppe** installiert wird. Führen Sie vor dem Starten einer Update- oder Hotfixinstallation **Test-AzureStack** aus, um den Status Ihrer Azure Stack-Instanz zu überprüfen:

```powershell
Test-AzureStack -Group UpdateReadiness
```

Wenn für Ihre Azure Stack-Instanz eine ältere Version als 1811 verwendet wird, sollten Sie die folgenden PowerShell-Befehle verwenden, um **Test-AzureStack** auszuführen:

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

Dieser Test überprüft die Konnektivität der Netzwerkinfrastruktur, indem er das softwaredefinierte Netzwerk (SDN) von Azure Stack umgeht. Er veranschaulicht Verbindungen von einer öffentlichen VIP mit den konfigurierten DNS-Forwardern, NTP-Servern und Authentifizierungsendpunkten. Dies schließt die Konnektivität mit Azure ein, wenn Azure AD als Identitätsanbieter verwendet wird, oder mit dem Partnerserver, wenn AD FS als Identitätsanbieter verwendet wird.

Schließen Sie den Debug-Parameter ein, um eine detaillierte Ausgabe des Befehls zu erhalten:

```powershell
Test-AzureStack -Include AzsNetworkInfra -Debug
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Stack-Diagnosetools und zur Protokollierung von Problemen finden Sie unter [Azure Stack-Diagnosetools](azure-stack-configure-on-demand-diagnostic-log-collection.md#using-pep-to-collect-diagnostic-logs).

Weitere Informationen zur Problembehandlung finden Sie unter [Problembehandlung von Microsoft Azure Stack](azure-stack-troubleshooting.md).
