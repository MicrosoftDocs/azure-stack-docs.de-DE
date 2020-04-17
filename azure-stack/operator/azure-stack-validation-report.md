---
title: Azure Stack Hub-Überprüfungsbericht
titleSuffix: Azure Stack Hub
description: Verwenden Sie das Azure Stack Hub Readiness Checker-Tool, um einen Überprüfungsbericht zu generieren.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: unknown
ms.lastreviewed: 10/23/2019
ms.openlocfilehash: 5cc3f24c62fc9c91637744cf031da39c6c7279fb
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "78366499"
---
# <a name="azure-stack-hub-validation-report"></a>Azure Stack Hub-Überprüfungsbericht

Verwenden Sie das [Azure Stack Hub Readiness Checker](https://www.powershellgallery.com/packages/Microsoft.AzureStack.ReadinessChecker/1.2002.1111.69)-Tool, um Überprüfungen auszuführen, die die Bereitstellung und Wartung einer Azure Stack Hub-Umgebung unterstützen. Das Tool schreibt die Ergebnisse in eine JSON-Berichtsdatei. Der Bericht zeigt detaillierte und zusammengefasste Daten über den Stand der Voraussetzungen für die Bereitstellung von Azure Stack Hub. Darüber hinaus enthält er Informationen zur Geheimnisrotation für vorhandene Azure Stack Hub-Bereitstellungen.  

## <a name="where-to-find-the-report"></a>Hier finden Sie den Bericht

Wenn das Tool ausgeführt wird, protokolliert es die Ergebnisse in **AzsReadinessCheckerReport.json**. Das Tool erstellt auch ein Protokoll namens **AzsReadinessChecker.log**. Der Speicherort dieser Dateien wird zusammen mit den Überprüfungsergebnissen in PowerShell angezeigt:

![Ergebnisse der Überprüfung für das Azure Stack Hub Readiness Checker-Tool](./media/azure-stack-validation-report/validation.png)

Beide Dateien behalten die Ergebnisse der nachfolgenden Durchsicht der Überprüfung bei, wenn sie auf demselben Computer ausgeführt werden. Beispielsweise kann das Tool ausgeführt werden, um Zertifikate zu überprüfen, erneut ausgeführt werden, um die Azure-Identität zu überprüfen, und dann ein drittes Mal, um die Registrierung zu überprüfen. Die Ergebnisse aller drei Überprüfungen sind im sich daraus ergebenden JSON-Bericht verfügbar.  

Standardmäßig werden beide Dateien in `C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json` geschrieben.  

- Verwenden Sie den `-OutputPath <path>`-Parameter am Ende der Befehlszeile, um einen anderen Berichtsspeicherort anzugeben.
- Verwenden Sie den `-CleanReport`-Parameter am Ende der Befehlszeile, um Informationen zu früheren Ausführungen des Tools aus **AzsReadinessCheckerReport.json** zu löschen.

## <a name="view-the-report"></a>Anzeigen des Berichts

Um den Bericht in PowerShell anzuzeigen, geben Sie den Pfad zum Bericht als Wert für `-ReportPath` an. Dieser Befehl zeigt den Inhalt des Berichts an und identifiziert Überprüfungen, für die noch keine Ergebnisse vorhanden sind.

Führen Sie den folgenden Befehl aus, um beispielsweise den Bericht von einer PowerShell-Eingabeaufforderung aus anzuzeigen, die für den Speicherort geöffnet ist, an dem sich der Bericht befindet:

```powershell
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

############### Azure Stack Hub Graph Validation Summary ###############

Azure Stack Hub Graph Validation results not available.

############### Azure Stack Hub ADFS Validation Summary ###############

Azure Stack Hub ADFS Validation results not available.

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

############### Azure Stack Hub Graph Validation Summary ###############

Azure Stack Hub Graph Validation results not available.

############### Azure Stack Hub ADFS Validation Summary ###############

Azure Stack Hub ADFS Validation results not available.
```

## <a name="view-a-filtered-report"></a>Anzeigen eines gefilterten Berichts

Verwenden Sie zum Anzeigen eines Berichts, der nach einem bestimmten Überprüfungstyp gefiltert ist, den Parameter `-ReportSections` mit einem der folgenden Werte:

- Zertifikat
- AzureRegistration
- AzureIdentity
- Graph
- ADFS
- Aufträge
- All  

Um beispielsweise die Berichtzusammenfassung nur für Zertifikate anzuzeigen, verwenden Sie die folgende PowerShell-Befehlszeile:

```powershell
Read-AzsReadinessReport -ReportPath .\Contoso-AzsReadinessReport.json -ReportSections Certificate - Summary
```
