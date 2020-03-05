---
title: Azure Stack Hub Capacity Planner
description: Hier erfahren Sie mehr über die Verwendung von Azure Stack Hub Capacity Planner-Tabellen für Bereitstellungen.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 05/31/2019
ms.author: inhenkel
ms.reviewer: prchint
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: 51b9530fdd67826616f6117c3c95dbe48b45303f
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77701637"
---
# <a name="azure-stack-hub-capacity-planner"></a>Azure Stack Hub Capacity Planner

Beim Azure Stack Hub Capacity Planner handelt es sich um eine Tabelle, in der dargestellt ist, wie unterschiedliche Zuordnungen von Computeressourcen zu einer Auswahl von Hardwareangeboten passen.

## <a name="worksheet-descriptions"></a>Arbeitsblattbeschreibungen

In der folgenden Tabelle wird jedes Arbeitsblatt im Azure Stack Hub Capacity Planner beschrieben, der unter [https://aka.ms/azstackcapacityplanner](https://aka.ms/azstackcapacityplanner) heruntergeladen werden kann.

|Name des Arbeitsblatts|BESCHREIBUNG|
|-----|-----|
|Version-Disclaimer|Zweck des Rechners, der Versionsnummer und des Veröffentlichungsdatums.|
|Instructions|Schritt-für-Schritt-Anleitung zum Modellieren der Kapazitätsplanung für eine Sammlung mit VMs.|
|DefinedSolutionSKUs|Tabelle mit bis zu fünf Hardwaredefinitionen. Die Einträge sind Beispiele. Passen Sie die Details an die entsprechenden Systemkonfigurationen an.|
|DefineByVMFootprint|Suchen Sie nach der entsprechenden Hardware-SKU, indem Sie Konfigurationen mit unterschiedlichen Größen und Mengen von VMs vergleichen.|
|DefineByWorkloadFootprint|Ermitteln Sie die geeignete Hardware-SKU, indem Sie eine Sammlung von Azure Stack Hub-Workloads erstellen.|
|  |  |

## <a name="definedsolutionskus-instructions"></a>DefinedSolutionSKUs-Anweisungen

Dieses Arbeitsblatt enthält Beispiele für bis zu fünf Hardwaredefinitionen. Passen Sie die Details an die entsprechenden Systemkonfigurationen an.

### <a name="hardware-selections-provided-by-authorized-hardware-partners"></a>Hardwareauswahl durch autorisierte Hardwarepartner

Azure Stack Hub wird als integriertes System bereitgestellt: Die Software wird durch unsere Lösungspartner installiert. Lösungspartner stellen ihre eigenen autoritativen Versionen von Tools für die Azure Stack Hub-Kapazitätsplanung bereit. Verwenden Sie diese Tools für die abschließenden Diskussionen über die Lösungskapazität.

### <a name="multiple-ways-to-model-computing-resources"></a>Mehrere Möglichkeiten zum Modellieren von Computeressourcen

Die Ressourcenmodellierung im Azure Stack Hub Capacity Planner hängt von den unterschiedlichen Größen der Azure Stack Hub-VMs ab. Die Größe von VMs reicht von der kleinsten Größe „Basic 0“ bis zur größten Größe „Standard_Fsv2“. Sie können Zuordnungen von Computeressourcen auf zwei Arten modellieren:

- Wählen Sie ein bestimmtes Hardwareangebot aus, und ermitteln Sie, welche Kombinationen der verschiedenen Ressourcen geeignet sind.

- Erstellen Sie eine bestimmte Kombination von VM-Zuordnungen, und lassen Sie den Ressourcenrechner von Azure anzeigen, welche verfügbaren Hardware-SKUs diese VM-Konfiguration unterstützen können.

Dieses Tool verfügt über zwei Methoden für die Zuordnung von VM-Ressourcen: entweder als einzelne Sammlung mit VM-Ressourcenzuordnungen oder als Sammlung mit bis zu sechs verschiedenen Workloadkonfigurationen. Jede Workloadkonfiguration kann eine unterschiedliche Zuordnung der verfügbaren VM-Ressourcen enthalten. Die nächsten Abschnitte enthalten Schritt-für-Schritt-Anleitungen zum Erstellen und Verwenden dieser Zuordnungsmodelle. Nur Werte, die in Zellen ohne Hintergrundschattierung oder in SKU-Pulldownlisten auf diesem Arbeitsblatt enthalten sind, sollten geändert werden. Änderungen, die innerhalb schattierter Zellen vorgenommen werden, können die Ressourcenberechnung beeinträchtigen.

## <a name="definebyvmfootprint-instructions"></a>DefineByVMFootprint-Anweisungen

Klicken Sie auf die Registerkarte **DefineByVMFootprint**, und führen dann diese Schritte aus, um mit einer einzelnen Sammlung von VMs unterschiedlicher Größe und Anzahl ein Modell zu erstellen:

1. Verwenden Sie in der oberen rechten Ecke dieses Arbeitsblatts die bereitgestellten Pulldown-Listenfeldsteuerelemente, um eine anfängliche Anzahl von Servern (zwischen 4 und 16) auszuwählen, die Sie in jedem Hardwaresystem (SKU) installieren möchten. Diese Anzahl von Servern kann während des Modellierungsprozesses jederzeit geändert werden, um zu sehen, wie sich dies auf die insgesamt verfügbaren Ressourcen für Ihr Ressourcenzuordnungsmodell auswirkt.
2. Wenn Sie verschiedene VM-Ressourcenzuordnungen für eine bestimmte Hardwarekonfiguration modellieren möchten, finden Sie das blaue Pulldownlistenfeld direkt unter der Bezeichnung **Current SKU** (Aktuelle SKU) in der oberen rechten Ecke der Seite. Öffnen Sie dieses Listenfeld, und wählen Sie die gewünschte Hardware-SKU aus.
3. Sie können nun damit beginnen, Ihrem Modell VMs verschiedener Größen hinzuzufügen. Um einen bestimmten VM-Typ einzuschließen, geben Sie einen Mengenwert in das blau umrandete Feld links neben diesem VM-Eintrag ein.

   > [!NOTE]
   > „Total VM Storage“ (VM-Gesamtspeicher) bezieht sich auf die Gesamtkapazität des Datenträgers für den virtuellen Computer (die Anzahl der unterstützten Datenträger multipliziert mit der maximalen Kapazität eines einzelnen Datenträgers [1 TB]). Basierend auf den Konfigurationsindikatoren haben wir die Tabelle „Available Storage Configurations“ (Verfügbare Speicherkonfigurationen) aufgefüllt, damit Sie Ihre gewünschte Speicherressourcenebene für jede Azure Stack Hub-VM auswählen können. Wichtig zu wissen: Sie können die Tabelle „Available Storage Configurations“ nach Bedarf hinzufügen oder ändern. <br><br>Jeder virtuelle Computer beginnt mit einem anfangs zugewiesenen lokalen Speicher. Um die schlanke Bereitstellung von temporärem Speicher widerzuspiegeln, können Sie den Wert von „local-temp“ in jede beliebige Angabe im Dropdownmenü ändern, einschließlich der maximal zulässigen Menge an temporärem Speicher.

4. Wenn Sie virtuelle Computer hinzufügen, sehen Sie die Diagramme, die zeigen, wie sich die verfügbaren SKU-Ressourcen ändern. Mit diesen Diagrammen erkennen Sie die Auswirkungen des Hinzufügens verschiedener Größen und Mengen von VMs während des Modellierungsprozesses. Eine weitere Möglichkeit, die Auswirkungen von Änderungen zu erkennen, besteht darin, die Werte für **Consumed** (Genutzt) und **Still Available** (Noch Verfügbar) direkt unter der Liste der verfügbaren VMs anzuzeigen. Diese Zahlen entsprechen geschätzten Werten auf der Grundlage der aktuell ausgewählten Hardware-SKU.
5. Wenn Sie die VMs erstellt haben, können Sie die empfohlene Hardware-SKU ermitteln, indem Sie auf **Suggested SKU** (Vorgeschlagene SKU) klicken. Diese Schaltfläche befindet sich in der oberen rechten Ecke der Seite direkt unter der Bezeichnung **Current SKU** (Aktuelle SKU). Mit dieser Schaltfläche können Sie dann Ihre VM-Konfigurationen ändern und sehen, welche Hardware jede Konfiguration unterstützt.

## <a name="definebyworkloadfootprint-instructions"></a>DefineByWorkloadFootprint-Anweisungen

Klicken Sie auf die Registerkarte **DefineByWorkloadFootprint**, und befolgen Sie die aufgeführten Schritte, um ein Modell mit einer Sammlung von Azure Stack Hub-Workloads zu erstellen. Sie erstellen Azure Stack Hub-Workloads mithilfe verfügbarer VM-Ressourcen.

> [!TIP]
> Beachten Sie den Hinweis aus Schritt 3 im vorherigen Abschnitt, um die Größe des bereitgestellten Speichers für eine Azure Stack Hub-VM zu ändern.

1. Verwenden Sie in der oberen rechten Ecke dieses Arbeitsblatts die bereitgestellten Pulldown-Listenfeldsteuerelemente, um eine anfängliche Anzahl von Servern (zwischen 4 und 16) auszuwählen, die Sie in jedem Hardwaresystem (SKU) installieren möchten.
2. Wenn Sie verschiedene VM-Ressourcenzuordnungen für eine bestimmte Hardwarekonfiguration modellieren möchten, finden Sie das blaue Pulldownlistenfeld direkt unter der Bezeichnung **Current SKU** (Aktuelle SKU) in der oberen rechten Ecke der Seite. Öffnen Sie dieses Listenfeld, und wählen Sie die gewünschte Hardware-SKU aus.
3. Wählen Sie auf der Seite **DefineByVMFootprint** die passende Speichergröße für jede Ihrer gewünschten Azure Stack Hub-VMs aus. Dieser Prozess wird in Schritt 3 im vorherigen Abschnitt beschrieben. Die Speichergröße pro virtuellem Computer wird auf dem Blatt „DefineByVMFootprint“ definiert.
4. Beginnen Sie oben links auf der Seite **DefineByWorkloadFootprint**, und erstellen Sie Konfigurationen für bis zu sechs verschiedene Workloadtypen. Geben Sie die Menge jedes VM-Typs ein, der in dieser Workload enthalten ist. Dies erfolgt durch Platzieren von numerischen Werten in der Spalte direkt unterhalb des Namens der Workload. Sie können die Workloadnamen ändern, um die Art der Workloads widerzuspiegeln, die von dieser speziellen Konfiguration unterstützt werden.
5. Sie können eine bestimmte Menge jedes Workloadtyps einschließen, indem Sie einen Wert am unteren Rand dieser Spalte direkt unter der Bezeichnung **Quantity** (Menge) eingeben.
6. Nachdem Sie Workloadtypen und -mengen erstellt haben, wählen Sie **Suggested SKU** (Empfohlene SKU) in der oberen rechten Ecke der Seite direkt unter der Bezeichnung **Current SKU** (Aktuelle SKU) aus. Die kleinste SKU mit ausreichenden Ressourcen zur Unterstützung dieser Gesamtkonfiguration von Workloads wird angezeigt.
7. Eine weitere Modellierung können Sie durch Ändern der Anzahl der für eine Hardware-SKU ausgewählten Server oder durch Ändern der VM-Zuordnungen oder -Mengen innerhalb Ihrer Workloadkonfigurationen erreichen. Die zugehörigen Diagramme zeigen ein sofortiges Feedback an, das angibt, wie sich Ihre Änderungen auf die gesamte Ressourcennutzung auswirken.
8. Wenn Sie mit Ihren Änderungen zufrieden sind, wählen Sie erneut **Suggested SKU** (Empfohlene SKU) aus, um die für Ihre neue Konfiguration vorgeschlagene SKU anzuzeigen. Sie können auch das Dropdownmenü auswählen, um Ihre gewünschte SKU auszuwählen.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu den [Überlegungen zur Integration von Rechenzentren für Azure Stack Hub](azure-stack-datacenter-integration.md).
