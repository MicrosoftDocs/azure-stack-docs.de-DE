---
title: Überprüfungsbericht für Azure Stack | Microsoft-Dokumentation
description: Verwenden Sie den Azure Stack Readiness Checker-Bericht für die Durchsicht von Überprüfungsergebnissen.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 10/23/2018
ms.openlocfilehash: d02173731f8cf7834160a0228c589b036aac7fe6
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2019
ms.locfileid: "64984015"
---
# <a name="azure-stack-validation-report"></a>Azure Stack-Überprüfungsbericht

Verwenden Sie das *Azure Stack Readiness Checker*-Tool, um Überprüfungen auszuführen, die die Bereitstellung und Wartung einer Azure Stack-Umgebung unterstützen. Das Tool schreibt die Ergebnisse in eine JSON-Berichtsdatei. Der Bericht zeigt detaillierte und zusammengefasste Daten über den Stand der Voraussetzungen für die Bereitstellung von Azure Stack. Darüber hinaus enthält er Informationen zur Geheimnisrotation für vorhandene Azure Stack-Bereitstellungen.  

## <a name="where-to-find-the-report"></a>Hier finden Sie den Bericht

Wenn das Tool ausgeführt wird, protokolliert es die Ergebnisse in **AzsReadinessCheckerReport.json**. Das Tool erstellt auch ein Protokoll namens **AzsReadinessChecker.log**. Der Speicherort dieser Dateien wird zusammen mit den Überprüfungsergebnissen in PowerShell angezeigt:

![Ausführen der Überprüfung](./media/azure-stack-validation-report/validation.png)

Beide Dateien behalten die Ergebnisse der nachfolgenden Durchsicht der Überprüfung bei, wenn sie auf demselben Computer ausgeführt werden. Beispielsweise kann das Tool ausgeführt werden, um Zertifikate zu überprüfen, erneut ausgeführt werden, um die Azure-Identität zu überprüfen, und dann ein drittes Mal, um die Registrierung zu überprüfen. Die Ergebnisse aller drei Überprüfungen sind im sich daraus ergebenden JSON-Bericht verfügbar.  

Standardmäßig werden beide Dateien in das Verzeichnis **C:\Users\<Benutzername>\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json** geschrieben.  

- Verwenden Sie den `-OutputPath <path>`-Parameter am Ende der Befehlszeile, um einen anderen Berichtsspeicherort anzugeben.
- Verwenden Sie den `-CleanReport`-Parameter am Ende der Befehlszeile, um Informationen zu früheren Ausführungen des Tools aus **AzsReadinessCheckerReport.json** zu löschen.

## <a name="view-the-report"></a>Anzeigen des Berichts

Um den Bericht in PowerShell anzuzeigen, geben Sie den Pfad zum Bericht als Wert für `-ReportPath` an. Dieser Befehl zeigt den Inhalt des Berichts an und identifiziert Überprüfungen, für die noch keine Ergebnisse vorhanden sind.

Um den Bericht beispielsweise von einer PowerShell-Eingabeaufforderung aus anzuzeigen, die für den Speicherort geöffnet ist, an dem sich der Bericht befindet, führen Sie den folgenden Befehl aus:

```shell
Read-AzsReadinessReport -ReportPath .\AzsReadinessReport.json
```

Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```shell
Reading All Validation(s) from Report C:\Contoso-AzsReadinessCheckerReport.json

############### Certificate Validation Summary ###############

Certificate Validation results not available.

############### Registration Validation Summary ###############

Azure Registration Validation results not available.

############### Azure Identity Results ###############

Test                          : ServiceAdministrator
Result                        : OK
AAD Service Admin             : admin@contoso.onmicrosoft.com
Azure Environment             : AzureCloud
Azure Active Directory Tenant : contoso.onmicrosoft.com
Error Details                 : 

############### Azure Identity Validation Summary ###############

Azure Identity Validation found no errors or warnings.

############### Azure Stack Graph Validation Summary ###############

Azure Stack Graph Validation results not available.

############### Azure Stack ADFS Validation Summary ###############

Azure Stack ADFS Validation results not available.

############### AzsReadiness Job Summary ###############

Index             : 0
Operations        : 
StartTime         : 2018/10/22 14:24:16
EndTime           : 2018/10/22 14:24:19
Duration          : 3
PSBoundParameters :
```

## <a name="view-the-report-summary"></a>Anzeigen der Berichtzusammenfassung

Um eine Zusammenfassung des Berichts anzuzeigen, können Sie den `-summary`-Parameter an das Ende des PowerShell-Befehls hinzufügen. Beispiel: 

```powershell
Read-AzsReadinessReport -ReportPath .\Contoso-AzsReadinessReport.json -summary
```

Die Zusammenfassung zeigt Überprüfungen, für die keine Ergebnisse vorhanden sind, und gibt an, ob die abgeschlossenen Überprüfungen bestanden oder nicht bestanden wurden. Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```shell
Reading All Validation(s) from Report C:\Contoso-AzsReadinessCheckerReport.json

############### Certificate Validation Summary ###############

Certificate Validation found no errors or warnings.

############### Registration Validation Summary ###############

Registration Validation found no errors or warnings.

############### Azure Identity Validation Summary ###############

Azure Identity Validation found no errors or warnings.

############### Azure Stack Graph Validation Summary ###############

Azure Stack Graph Validation results not available.

############### Azure Stack ADFS Validation Summary ###############

Azure Stack ADFS Validation results not available.
```

## <a name="view-a-filtered-report"></a>Anzeigen eines gefilterten Berichts

Verwenden Sie zum Anzeigen eines Berichts, der nach einem bestimmten Überprüfungstyp gefiltert ist, den Parameter **-ReportSections** mit einem der folgenden Werte:

- Zertifikat
- AzureRegistration
- AzureIdentity
- Graph
- ADFS
- Aufträge
- Alle  

Um beispielsweise die Berichtzusammenfassung nur für Zertifikate anzuzeigen, verwenden Sie die folgende PowerShell-Befehlszeile:

```powershell
Read-AzsReadinessReport -ReportPath .\Contoso-AzsReadinessReport.json -ReportSections Certificate - Summary
```
