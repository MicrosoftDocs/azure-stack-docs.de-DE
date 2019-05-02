---
title: Kapazitätsplanungstabelle für Azure Stack | Microsoft-Dokumentation
description: Informationen zur Kapazitätsplanungstabelle für Azure Stack-Bereitstellungen.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2018
ms.author: jeffgilb
ms.reviewer: prchint
ms.lastreviewed: 12/11/2018
ms.openlocfilehash: 459398f5f40ae5fb28dfa4ac73b94aecca7304eb
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "64309684"
---
# <a name="azure-stack-capacity-planner"></a>Azure Stack Capacity Planner
Der Azure Stack-Kapazitätsplaner ist eine Kalkulationstabelle, die für die Ressourcenplanung von Azure Stack verwendet wird. Der Kapazitätsplaner bietet Ihnen die Möglichkeit, verschiedene Zuordnungen von Computeressourcen zu entwerfen und herauszufinden, wie diese für eine Auswahl von Hardwareangeboten passen würden. Detaillierte Anweisungen für die Verwendung der Azure Stack-Berechnung finden Sie unten.

## <a name="worksheet-descriptions"></a>Arbeitsblattbeschreibungen
Nachfolgend finden Sie eine kurze Zusammenfassung der Arbeitsblätter, die in der Kalkulationstabelle des Azure Stack-Kapazitätsplaners enthalten sind und die unter [https://aka.ms/azstackcapacityplanner](https://aka.ms/azstackcapacityplanner) heruntergeladen werden können:

|Registerkartenname|BESCHREIBUNG|
|-----|-----|
|Version-Disclaimer|Kurze Übersicht über den Zweck der Berechnung, die Versionsnummer und das Releasedatum.|
|Anleitung|Enthält detaillierte Anweisungen für die Verwendung des Azure Stack-Kapazitätsplaners.|
|DefinedSolutionSKUs|Eine Tabelle mit mehreren Spalten, die bis zu fünf Hardwaredefinitionen enthält. Die Einträge in dieser Tabelle sind Beispiele. Der Benutzer muss die Details ändern, um die Systemkonfigurationen anzupassen, die für die Verwendung oder den Kauf in Betracht gezogen werden.|
|DefineByVMFootprint|Ermitteln Sie die geeignete Hardware-SKU, indem Sie eine Sammlung von virtuellen Computern unterschiedlicher Größe und Anzahl erstellen.|
|DefineByWorkloadFootprint|Ermitteln Sie die geeignete Hardware-SKU, indem Sie eine Sammlung von Azure Stack-Workloads erstellen.|
|  |  |

## <a name="definedsolutionskus-instructions"></a>DefinedSolutionSKUs-Anweisungen
Dieses Arbeitsblatt enthält Beispiele für bis zu fünf Hardwaredefinitionen. Ändern Sie die Details, um die Systemkonfigurationen anzupassen, die für die Verwendung oder den Kauf in Betracht gezogen werden.

### <a name="hardware-selections-provided-by-authorized-hardware-partners"></a>Hardwareauswahl durch autorisierte Hardwarepartner
Azure Stack wird als integriertes System bereitgestellt: Die Software wird durch unsere Lösungspartner installiert. Diese Lösungspartner verfügen über ihre eigenen, verbindlichen Versionen der Azure Stack-Kapazitätsplanungstools, und diese Tools sollten verwendet werden, um die Beratungen über die Lösungskapazität abzuschließen.

### <a name="multiple-ways-to-model-computing-resources"></a>Mehrere Möglichkeiten zum Modellieren von Computeressourcen
Die grundlegenden Bausteine für die Ressourcenmodellierung im Azure Stack-Planer sind die verschiedenen Größen der virtuellen Azure Stack-Computer (VMs). Diese VMs reichen von der kleinsten Größe „Basic 0“ bis hin zu dem aktuell größten virtuellen Computer „Standard_Fsv2“. Je nach Bedarf können verschiedene Mengen unterschiedlicher VMs verwendet werden, um mit diesem Tool auf zwei verschiedene Arten Computeressourcenzuordnungen zu erstellen.

1. Der planende Benutzer wählt ein bestimmtes Hardwareangebot aus und sieht dann, welche Kombinationen verschiedener Ressourcen in diese Hardwareressource passen. 

2. Der planende Benutzer erstellt eine bestimmte Kombination von VM-Zuordnungen und lässt dann den Azure Ressourcenrechner anzeigen, welche verfügbaren Hardware-SKUs diese VM-Konfiguration unterstützen können.

Dieses Tool bietet zwei Methoden für die Zuordnung von VM-Ressourcen: entweder als eine einzige Sammlung von VM-Ressourcenzuordnungen oder als eine Sammlung von bis zu sechs verschiedenen Workloadkonfigurationen. Jede Workloadkonfiguration kann eine unterschiedliche Zuordnung der verfügbaren VM-Ressourcen enthalten. Ausführliche Informationen zum Erstellen und Verwenden jedes dieser Zuordnungsmodelle finden Sie weiter unten. Nur Werte, die in Zellen ohne Hintergrundschattierung oder in SKU-Pulldownlisten auf diesem Arbeitsblatt enthalten sind, sollten geändert werden. Änderungen, die innerhalb schattierter Zellen vorgenommen werden, können die Ressourcenberechnung beeinträchtigen.


## <a name="definebyvmfootprint-instructions"></a>DefineByVMFootprint-Anweisungen
Um ein Modell mit einer einzigen Sammlung von VMs unterschiedlicher Größe und Anzahl zu erstellen, wählen Sie die Registerkarte „DefineByVMFootprint“ aus und befolgen dann die aufgeführten Schritte.

1. Verwenden Sie in der oberen rechten Ecke dieses Arbeitsblatts die bereitgestellten Pulldown-Listenfeldsteuerelemente, um eine anfängliche Anzahl von Servern (zwischen 4 und 16) auszuwählen, die Sie in jedem Hardwaresystem (SKU) installieren möchten. Diese Anzahl von Servern kann während des Modellierungsprozesses jederzeit geändert werden, um zu sehen, wie sich dies auf die insgesamt verfügbaren Ressourcen für Ihr Ressourcenzuordnungsmodell auswirkt.
2. Wenn Sie verschiedene VM-Ressourcenzuordnungen für eine bestimmte Hardwarekonfiguration modellieren möchten, finden Sie das blaue Pulldownlistenfeld direkt unter der Bezeichnung „Current SKU“ (Aktuelle SKU) in der oberen rechten Ecke der Seite. Öffnen Sie dieses Listenfeld, und wählen Sie die gewünschte Hardware-SKU aus.
3. Sie können nun damit beginnen, Ihrem Modell virtuelle Computer mit verschiedener Größe hinzuzufügen. Um einen bestimmten VM-Typ einzuschließen, geben Sie einen Mengenwert in das blau umrandete Feld links neben diesem VM-Eintrag ein.

   > [!NOTE]
   > „Total VM Storage“ (VM-Gesamtspeicher) bezieht sich auf die Gesamtkapazität des Datenträgers für den virtuellen Computer (Anzahl der unterstützten Datenträger * maximale Kapazität eines einzelnen Datenträgers (1 TB)). Basierend auf den Konfigurationsindikatoren haben wir die Tabelle „Available Storage Configurations“ (Verfügbare Speicherkonfigurationen) aufgefüllt, damit Sie Ihre gewünschte Speicherressourcenebene für jeden virtuellen Azure Stack-Computer auswählen können. Wichtig zu wissen: Sie können die Tabelle „Available Storage Configurations“ nach Bedarf hinzufügen oder ändern.<br><br>Jeder virtuelle Computer beginnt mit einem anfangs zugewiesenen lokalen Speicher. Um die schlanke Bereitstellung von temporärem Speicher widerzuspiegeln, kann der Wert von „local-temp“ in jede beliebige Angabe im Dropdownmenü geändert werden, einschließlich der maximal zulässigen Menge an temporärem Speicher.

4. Wenn Sie virtuelle Computer hinzufügen, sehen Sie die Diagramme, die zeigen, wie sich die verfügbaren SKU-Ressourcen ändern. Auf diese Weise können Sie die Auswirkungen des Hinzufügens verschiedener Größen und Mengen von VMs während des Modellierungsprozesses erkennen. Eine weitere Möglichkeit, die Auswirkungen von Änderungen zu erkennen, besteht darin, die Werte für „Consumed“ (Genutzt) und „Still Available“ (Noch Verfügbar) direkt unter der Liste der verfügbaren VMs anzuzeigen. Diese Zahlen entsprechen geschätzten Werten auf der Grundlage der aktuell ausgewählten Hardware-SKU.
5. Nachdem Sie Ihre VM-Gruppe erstellt haben, können Sie die vorgeschlagene Hardware-SKU ermitteln, indem Sie auf die Schaltfläche „Suggested SKU“ (Empfohlene SKU) in der oberen rechten Ecke der Seite direkt unter der Bezeichnung „Current SKU“ (Aktuelle SKU) klicken. Mit dieser Schaltfläche können Sie dann Ihre VM-Konfigurationen ändern und sehen, welche Hardware jede Konfiguration unterstützt.


## <a name="definebyworkloadfootprint-instructions"></a>DefineByWorkloadFootprint-Anweisungen
Um ein Modell mit einer Sammlung von Azure Stack-Workloads zu erstellen, wählen Sie die Registerkarte „DefineByWorkloadFootprint“ aus und befolgen die aufgeführten Schritte. Azure Stack-Workloads werden mithilfe der verfügbare VM-Ressourcen erstellt.   

> [!TIP]
> Um die Größe des bereitgestellten Speichers für eine Azure Stack-VM zu ändern, beachten Sie den Hinweis aus Schritt 3 im vorherigen Abschnitt.

1. Verwenden Sie in der oberen rechten Ecke dieser Seite die bereitgestellten Pulldown-Listenfeldsteuerelemente, um eine anfängliche Anzahl von Servern (zwischen 4 und 16) auszuwählen, die Sie in jedem Hardwaresystem (SKU) installieren möchten.
2. Wenn Sie verschiedene VM-Ressourcenzuordnungen für eine bestimmte Hardwarekonfiguration modellieren möchten, finden Sie das blaue Pulldownlistenfeld direkt unter der Bezeichnung „Current SKU“ (Aktuelle SKU) in der oberen rechten Ecke der Seite. Öffnen Sie dieses Listenfeld, und wählen Sie die gewünschte Hardware-SKU aus.
3. Wählen Sie auf der Seite „DefineByVMFootprint“ die passende Speichergröße für jede Ihrer gewünschten Azure Stack-VMs aus, wie oben in Schritt drei der Anweisungen für „DefineByVMFootprint“ beschrieben. Die Speichergröße pro virtuellem Computer wird auf dem Blatt „DefineByVMFootprint“ definiert.
4. Beginnen Sie oben links auf der Seite „DefineByWorkloadFootprint“, und erstellen Sie Konfigurationen für bis zu sechs verschiedene Workloadtypen, indem Sie die Menge jedes VM-Typs eingeben, der in dieser Workload enthalten ist. Dies erfolgt durch Platzieren von numerischen Werten in der Spalte direkt unterhalb des Namens der Workload. Die Workloadnamen können geändert werden, um die Art der Workloads widerzuspiegeln, die von dieser speziellen Konfiguration unterstützt werden.
5. Sie können eine bestimmte Menge jedes Workloadtyps einschließen, indem Sie einen Wert am unteren Rand dieser Spalte direkt unter der Bezeichnung „Quantity“ (Menge) eingeben.
6. Sobald Workloadtypen und -mengen erstellt wurden, wird durch Klicken auf die Schaltfläche „Suggested SKU“ (Empfohlene SKU) in der oberen rechten Ecke der Seite direkt unter der Bezeichnung „Current SKU“ (Aktuelle SKU) die kleinste SKU mit genügend Ressourcen zur Unterstützung dieser Gesamtkonfiguration von Workloads angezeigt.
7. Eine weitere Modellierung kann durch Ändern der Anzahl der für eine Hardware-SKU ausgewählten Server oder durch Ändern der VM-Zuordnungen oder -Mengen innerhalb Ihrer Workloadkonfigurationen erfolgen. Die zugehörigen Diagramme zeigen ein sofortiges Feedback an, das angibt, wie sich Ihre Änderungen auf die gesamte Ressourcennutzung auswirken.
8. Wenn Sie mit Ihren Änderungen zufrieden sind, wird durch erneutes Klicken auf die Schaltfläche „Suggested SKU“ (Empfohlene SKU) die für Ihre neue Konfiguration vorgeschlagene SKU angezeigt.


## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie mehr zu den [Überlegungen zur Integration von Rechenzentren für Azure Stack](azure-stack-datacenter-integration.md).
