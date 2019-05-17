---
title: Verwenden des Azure Stack-Überprüfungstools | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie Protokolldateien für die Diagnose in Azure Stack sammeln.
services: azure-stack
author: mattbriggs
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 04/20/2019
ms.author: mabrigg
ms.reviewer: adshar
ms.lastreviewed: 12/03/2018
ms.openlocfilehash: 90a3b35675a8197694d04395f51ef85f7a8fee14
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65617969"
---
# <a name="validate-azure-stack-system-state"></a>Überprüfen des Azure Stack-Systemstatus

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Als Azure Stack-Betreiber in der Lage zu sein, die Integrität und den Status Ihres Systems bei Bedarf zu ermitteln, ist sehr wichtig. Das Azure Stack-Überprüfungstool (**Test-AzureStack**) ist ein PowerShell-Cmdlet, mit dem Sie eine Reihe von Tests auf Ihrem System ausführen können, um vorliegende Fehler zu identifizieren. In der Regel werden Sie aufgefordert, dieses Tool über den [privilegierten Endpunkt (PEP)](azure-stack-privileged-endpoint.md) auszuführen, wenn Sie sich mit der Meldung eines Problems an Microsoft Customer Services Support (CSS) wenden. Wenn die systemweiten Integritäts- und Statusinformationen vorliegen, kann CSS detaillierte Protokolle sammeln und analysieren, sich auf den Bereich konzentrieren, in dem der Fehler aufgetreten ist, und mit Ihnen zusammenarbeiten, um das Problem zu beheben.

## <a name="running-the-validation-tool-and-accessing-results"></a>Ausführen des Überprüfungstools und Zugreifen auf die Ergebnisse

Wie bereits erwähnt, wird das Überprüfungstool über den PEP ausgeführt. Jeder Test gibt im PowerShell-Fenster einen **PASS/FAIL**-Status zurück. Darüber hinaus wird ein detaillierter HTML-Bericht erstellt, auf den zu einem späteren Zeitpunkt während der [Protokollsammlung](azure-stack-diagnostics.md) zugegriffen werden kann. Hier ist eine Gliederung des Prozesses für End-to-End-Validierungstests: 

1. Greifen Sie auf den privilegierten Endpunkt (PEP) zu. Führen Sie die folgenden Befehle aus, um eine PEP-Sitzung einzurichten:

   ```powershell
   Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
   ```

   > [!TIP]
   > Wenn Sie auf einem ASDK-Hostcomputer auf den PEP zugreifen möchten, geben Sie für „-ComputerName“ den Wert „AzS-ERCS01“ an.

2. Sobald Sie im PEP sind, führen Sie Folgendes aus: 

   ```powershell
   Test-AzureStack
   ```

   Weitere Informationen finden Sie in den Abschnitten [Überlegungen zu Parametern](azure-stack-diagnostic-test.md#parameter-considerations) und [Beispiele für Anwendungsfälle](azure-stack-diagnostic-test.md#use-case-examples).

3. Wenn Tests **FAIL** melden, führen Sie Folgendes aus:

   ```powershell
   Get-AzureStackLog -FilterByRole SeedRing -OutputSharePath "<path>" -OutputShareCredential $cred
   ```

   Das Cmdlet sammelt Protokolle, die von Test-AzureStack generiert wurden. Weitere Informationen zu Diagnoseprotokollen finden Sie unter [Azure Stack-Diagnosetools](azure-stack-diagnostics.md). Sie sollten keine Protokolle sammeln und sich nicht an CSS wenden, wenn bei Tests **WARN** gemeldet wird.

4. Wenn Sie durch den CSS angewiesen wurden, das Überprüfungstool auszuführen, wird der CSS-Vertreter die von Ihnen gesammelten Protokolle anfordern, um die Behebung Ihres Problems fortsetzen zu können.

## <a name="tests-available"></a>Verfügbare Tests

Mithilfe des Überprüfungstools können Sie eine Reihe von Tests auf Systemebene sowie grundlegende Cloudszenarien ausführen, die Ihnen einen Einblick in den aktuellen Status bieten und Probleme in Ihrem System ermitteln.

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

Zusätzlich zu den vorstehenden Infrastrukturtests haben Sie die Möglichkeit zur Ausführung von Cloudszenariotests, um die Funktionalität für Infrastrukturkomponenten zu überprüfen. Weil in diesen Tests die Bereitstellung von Ressourcen enthalten ist, sind zu deren Ausführung Anmeldeinformationen für Cloudadministratoren erforderlich.

> [!NOTE]
> Aktuell können Sie Cloudszenariotests nicht mit AD FS-Anmeldeinformationen (Active Directory Federation Services, Active Directory-Verbunddienste) ausführen. 

Die folgenden Cloudszenarien werden durch das Überprüfungstool getestet:
- Ressourcengruppenerstellung   
- Planerstellung              
- Angebotserstellung            
- Speicherkontoerstellung   
- Erstellung des virtuellen Computers 
- Speichervorgang für Blobs   
- Speichervorgang für Warteschlangen  
- Speichervorgang für Tabellen  

## <a name="parameter-considerations"></a>Überlegungen zu Parametern

- Der Parameter **List** kann verwendet werden, um alle verfügbaren Testkategorien anzuzeigen.

- Die Parameter **Include** und **Ignore** können verwendet werden, um Testkategorien einzubeziehen bzw. auszuschließen. Weitere Informationen zu den bei diesen Argumenten verwendeten Daten finden Sie im folgenden Abschnitt.

  ```powershell
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
  ```

  ```powershell
  Test-AzureStack -Ignore AzsInfraPerformance
  ```

- Ein Mandanten-VM wird im Rahmen eines der Cloudszenariotests bereitgestellt. Dies können Sie mit **DoNotDeployTenantVm** deaktivieren.

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

Um die Bedienerfreundlichkeit zu verbessern, wurde ein Parameter **Gruppe** eingeführt, um mehrere Testkategorien gleichzeitig auszuführen. Aktuell gibt es 3 definierte Gruppen: **Default**, **UpdateReadiness** und **SecretRotationReadiness**.

- **Standard:** Standardausführung von **Test-AzureStack**. Diese Gruppe wird standardmäßig ausgeführt, wenn keine anderen Gruppen ausgewählt werden.
- **UpdateReadiness**: Überprüfung, um festzustellen, ob der Zeitstempel aktualisiert werden kann. Wird die Gruppe **UpdateReadiness** ausgeführt, werden Warnungen als Fehler in der Konsolenausgabe angezeigt und sollten als Hindernisse für die Aktualisierung angesehen werden. Die folgenden Kategorien sind Teil der **UpdateReadiness**-Gruppe:

  - **AzsAcsSummary**
  - **AzsDefenderSummary**
  - **AzsHostingInfraSummary**
  - **AzsInfraCapacity**
  - **AzsInfraRoleSummary**
  - **AzsPortalAPISummary**
  - **AzsSFRoleSummary**
  - **AzsStoreSummary**

- **SecretRotationReadiness**: Überprüfung, um festzustellen, ob ein Zeitstempel vorliegt, der eine Geheimnisrotation erlaubt. Wird die Gruppe **SecretRotationReadiness** ausgeführt, werden Warnungen als Fehler in der Konsolenausgabe angezeigt und sollten als Hindernisse für die Geheimnisrotation angesehen werden. Die folgenden Kategorien sind Teil der SecretRotationReadiness-Gruppe:

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

Im folgenden Beispiel wird **Test-AzureStack** ausgeführt, um die Systembereitschaft zu testen, bevor ein Update oder Hotfix mit **Gruppe** installiert wird. Bevor Sie die Installation eines Updates oder Hotfixes starten, sollten Sie **Test-AzureStack** ausführen, um den Status Ihrer Azure Stack-Instanz zu überprüfen:

```powershell
Test-AzureStack -Group UpdateReadiness
```

Wenn Ihre Azure Stack-Instanz jedoch eine Version vor 1811 verwendet, führen Sie **Test-AzureStack** mit den folgenden PowerShell-Befehlen aus:

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
```

### <a name="run-validation-tool-to-test-infrastructure-backup-settings"></a>Ausführen des Überprüfungstools zum Testen der Einstellungen für die Infrastruktursicherung

*Vor* der Konfiguration der Infrastruktursicherung können Sie mit dem Test **AzsBackupShareAccessibility** den Sicherungsfreigabepfad und die Sicherungsanmeldeinformationen testen: 

  ```powershell
  New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupSharePath "\\<fileserver>\<fileshare>" -BackupShareCredential <PSCredentials-for-backup-share>
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



## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Stack-Diagnosetools und zur Protokollierung von Problemen finden Sie unter [Azure Stack-Diagnosetools](azure-stack-diagnostics.md).

Weitere Informationen zur Problembehandlung finden Sie unter [Problembehandlung von Microsoft Azure Stack](azure-stack-troubleshooting.md).
