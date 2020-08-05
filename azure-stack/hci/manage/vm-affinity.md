---
title: Einrichten von VM-Affinitätsregeln mithilfe von Windows PowerShell
description: Erfahren Sie, wie Sie mithilfe von Windows PowerShell VM-Affinitätsregeln einrichten.
author: v-dasis
ms.topic: how-to
ms.date: 07/21/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: b53ed05d8ce255b4fd68d40faf37b44f11bee059
ms.sourcegitcommit: a15a0f955bac922cebb7bf90a72384fd84ddfe56
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2020
ms.locfileid: "86947179"
---
# <a name="create-server-and-site-affinity-rules-for-vms"></a>Erstellen von Server- und Standortaffinitätsregeln für VMs

> Gilt für Azure Stack HCI, Version 20H2

Mit Windows PowerShell können Sie ganz einfach Affinitäts- und Antiaffinitätsregeln für Ihre VMs in einem Cluster erstellen.

Affinität ist eine Regel, die eine Beziehung zwischen zwei oder mehr Ressourcengruppe oder Rollen (z. B. VMs) einrichtet, um sie in einem Azure Stack HCI-Cluster zusammenzuhalten. Antiaffinität ist das Gegenteil: Die angegebenen Ressourcengruppen sollen voneinander getrennt bleiben.

Affinitäts- und Antiaffinitätsregeln werden auf ähnliche Weise verwendet wie Verfügbarkeitszonen in Azure. In Azure können Sie Verfügbarkeitszonen konfigurieren, um VMs getrennt voneinander in separaten Zonen oder zusammen in ein und derselben Zone zu platzieren.  

Affinitäts- und Antiaffinitätsregeln sorgen dafür, dass gruppierte VMs entweder auf demselben Clusterknoten bleiben oder daran gehindert werden, sich auf demselben Clusterknoten zu befinden.  In einem solchen Setup kann eine VM nur manuell aus einem Knoten verschoben werden.  Sie können VMs auch in einem freigegebenen Clustervolume (Cluster Shared Volume, CSV) zusammenhalten, in dem sich die zugehörige VHDX befindet.

Durch Kombination von Affinitäts- und Antiaffinitätsregeln können Sie auch einen Stretchingcluster über zwei Standorte (Fehlerdomänen) hinweg konfigurieren und die VMs in dem Standort zusammenhalten, in dem sie sich befinden müssen.

Üblicherweise richten Sie Regeln auf einem Remotecomputer ein, nicht auf einem Hostserver in einem Cluster. Dieser Remotecomputer wird als Verwaltungscomputer bezeichnet.

> [!NOTE]
> Beim Ausführen von PowerShell-Befehlen von einem Verwaltungscomputer schließen Sie die Parameter „-Name“ oder „-Cluster“ mit dem Namen des verwalteten Clusters ein. Ggf. müssen Sie auch den vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) angeben, wenn Sie den Parameter „-ComputerName“ für einen Serverknoten verwenden.

## <a name="new-powershell-cmdlets"></a>Neue PowerShell-Cmdlets

Zum Erstellen von Affinitätsregeln für Azure Stack HCI-Clustern verwenden Sie die folgenden PowerShell-Cmdlets.

### <a name="new-clusteraffinityrule"></a>New-ClusterAffinityRule

Das Cmdlet **`New-ClusterAffinityRule`** dient zum Erstellen neuer Regeln.  Zusammen mit diesem Befehl geben Sie den Namen sowie den Typ der Regel an, wobei Folgendes gilt:

**`-Name`** ist der Name der Regel.

**`-RuleType`** -Werte: `SameFaultDomain` | `SameNode` | `DifferentFaultDomain` | `DifferentNode`.

Beispiel:

```powershell
New-ClusterAffinityRule -Name -RuleType SameFaultDomain -Cluster Cluster1
```

### <a name="set-clusteraffinityrule"></a>Set-ClusterAffinityRule

Das Cmdlet **`Set-ClusterAffinityRule`** dient zum Aktivieren oder Deaktivieren einer Regel, wobei Folgendes gilt:

**`-Name`** ist der Name der Regel, die aktiviert oder deaktiviert werden soll.

**`-Enabled`**  |  **`Disabled`** aktiviert oder deaktiviert die Regel.

Beispiel:

```powershell
Set-ClusterAffinityRule -Name -Enabled -Cluster Cluster1
```

### <a name="get-clusteraffinityrule"></a>Get-ClusterAffinityRule

Das Cmdlet **`Get-ClusterAffinityRule`** dient zum Anzeigen der angegebenen Regel und des zugehörigen Typs.  Wenn **`-Name`** nicht angegeben ist, werden alle Regeln aufgelistet.

Beispiel:

```powershell
Get-ClusterAffinityRule -Name -Cluster Cluster1
```

### <a name="add-clustergrouptoaffinityrule"></a>Add-ClusterGroupToAffinityRule

Das Cmdlet **`Add-ClusterGroupToAffinityRule`** dient zum Hinzufügen einer VM-Rolle oder eines Gruppennamens zu einer bestimmten Affinitätsregel, wobei Folgendes gilt:

**`-Groups`** ist der Name der Gruppe oder Rolle, die zur Regel hinzugefügt werden soll.

**`-Name`** ist der Name der Regel, zu der die Gruppe oder Rolle hinzugefügt werden soll.

Beispiel:

```powershell
Add-ClusterGroupToAffinityRule -Groups -Name -Cluster Cluster1
```

### <a name="add-clustersharedvolumetoaffinityrule"></a>Add-ClusterSharedVolumeToAffinityRule

Das Cmdlet **`Add-ClusterSharedVolumeToAffinityRule`** dient zum Zusammenhalten der VMs mit dem freigegebenen Clustervolume, in dem sich die VHDX befindet, wobei Folgendes gilt:

**`-ClusterSharedVolumes`** sind die CSV-Datenträger, die Sie zur Regel hinzufügen möchten.

**`-Name`** ist der Name der Regel, zu der die Datenträger hinzugefügt werden sollen.

Beispiel:

```powershell
Add-ClusterSharedVolumeToAffinityRule  -ClusterSharedVolumes -Name -Cluster Cluster1
```

### <a name="remove-clusteraffinityrule"></a>Remove-ClusterAffinityRule

Das Cmdlet **`Remove-ClusterAffinityRule`** löscht die angegebene Regel, wobei **`-Name`** der Name der Regel ist.

Beispiel:

```powershell
Remove-ClusterAffinityRule -Name -Cluster Cluster1
```

### <a name="remove-clustergroupfromaffinityrule"></a>Remove-ClusterGroupFromAffinityRule

Das Cmdlet **`Remove-ClusterGroupFromAffinityRule`** entfernt eine VM-Gruppe oder -Rolle aus einer bestimmten Regel, deaktiviert oder löscht die Regel aber nicht. Dabei gilt Folgendes:

**`-Name`** ist der Name der Regel.

**`-Groups`** sind die Gruppen oder Rollen, die Sie aus der Regel entfernen möchten.

Beispiel:

```powershell
Remove-ClusterGroupFromAffinityRule -Name -Groups -Cluster Cluster1
```

### <a name="remove-clustersharedvolumefromaffinityrule"></a>Remove-ClusterSharedVolumeFromAffinityRule

Das Cmdlet **`Remove-ClusterSharedVolumeFromAffinityRule`** entfernt die freigegebenen Clustervolumes aus einer bestimmten Regel, deaktiviert oder löscht die Regel aber nicht. Dabei gilt Folgendes:

**`-ClusterSharedVolumes`** sind die CSV-Datenträger, die Sie aus der Regel entfernen möchten.

**`-Name`** ist der Name der Regel, aus der die Datenträger entfernt werden sollen.

Beispiel:

```powershell
Remove-ClusterSharedVolumeFromAffinityRule -ClusterSharedVolumes -Name -Cluster Cluster1
```

## <a name="existing-powershell-cmdlets"></a>Vorhandene PowerShell-Cmdlets

Zusätzlich zu den neuen Cmdlets haben wir einigen vorhandenen Cmdlets auch neue zusätzliche Optionen hinzugefügt.

### <a name="move-clustergroup"></a>Move-ClusterGroup

Die neue Option `-IgnoreAffinityRule` ignoriert die Regel und verschiebt die Clusterressourcengruppe auf einen anderen Clusterknoten. Weitere Informationen zu diesem Cmdlet finden Sie unter [Move-ClusterGroup](/powershell/module/failoverclusters/move-clustergroup?view=win10-ps).

Beispiel:

```powershell
Move-ClusterGroup -IgnoreAffinityRule -Cluster Cluster1
```

> [!NOTE]
> Wenn eine Verschiebungsregel gültig ist (unterstützt wird), werden alle betroffenen Gruppen und Regeln ebenfalls verschoben.  Wenn die Verschiebung einer VM gegen eine Regel verstößt, aber vorübergehend und einmalig notwendig ist, verwenden Sie die Option `-IgnoreAffinityRule`, um die Verschiebung zuzulassen. In diesem Fall wird für die VM eine Warnung zum Verstoß angezeigt. Sie können die Regel danach wieder aktivieren, wenn Sie dies wünschen.

### <a name="start-clustergroup"></a>Start-ClusterGroup

Die neue Option `-IgnoreAffinityRule` ignoriert die Regel und schaltet die Clusterressourcengruppe am aktuellen Speicherort online. Weitere Informationen zu diesem Cmdlet finden Sie unter [Start-ClusterGroup](/powershell/module/failoverclusters/start-clustergroup?view=win10-ps).

Beispiel:

```powershell
Start-ClusterGroup -IgnoreAffinityRule -Cluster Cluster1
```

## <a name="anti-affinity-rules"></a>Antiaffinitätsregeln

### <a name="scenario-1"></a>Szenario 1
Sie verfügen über zwei VMs, auf denen SQL Server ausgeführt wird, im selben Azure Stack HCI-Cluster mit mehreren Standorten.  Beide VMs verbrauchen viele Arbeitsspeicher-, CPU- und Massenspeicherressourcen.  Wenn sich beide VMs auf demselben Knoten befänden, könnte dies zu Leistungsproblemen bei einer oder beiden führen, da sie um Arbeitsspeicher, CPU und Speicherzyklen konkurrieren würden.  Indem Sie eine Antiaffinitätsregel mit dem Regeltyp `DifferentNode` verwenden, bleiben diese VMs immer auf unterschiedlichen Clusterknoten.  

Die Befehle für dieses Beispiel sehen wie folgt aus:

```powershell
New-ClusterAffinityRule -Name SQL -Ruletype DifferentNode -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL1,SQL2 –Name SQL -Cluster Cluster1

Set-ClusterAffinityRule -Name SQL -Enabled 1 -Cluster Cluster1
```

Um die Regel und ihre Konfiguration anzuzeigen, verwenden Sie das Cmdlet **`Get-ClusterAffinityRule`** . Sie erhalten folgende Ausgabe:

```powershell
Get-ClusterAffinityRule -Name SQL -Cluster Cluster1

Name    RuleType        Groups        Enabled
----    -----------     -------       -------
SQL     DifferentNode   {SQL1, SQL2}     1
```

### <a name="scenario-2"></a>Szenario 2

Angenommen, Sie verfügen über einen Azure Stack HCI-Stretchingcluster mit zwei Standorten (Fehlerdomänen). Sie verfügen zudem über zwei Domänencontroller, die sich immer in verschiedenen Standorten befinden sollen. Indem Sie eine Antiaffinitätsregel mit dem Regeltyp `DifferentFaultDomain` verwenden, bleiben diese Domänencontroller immer in unterschiedlichen Standorten.  Die Befehle für dieses Beispiel sehen wie folgt aus:

```powershell
New-ClusterAffinityRule -Name DC -Ruletype DifferentFaultDomain -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups DC1,DC2 –Name DC -Cluster Cluster1

Set-ClusterAffinityRule -Name DC -Enabled 1 -Cluster Cluster1
```

Um diese Regel und ihre Konfiguration anzuzeigen, verwenden Sie das Cmdlet **`Get-ClusterAffinityRule`** . Sie erhalten folgende Ausgabe:

```powershell
Get-ClusterAffinityRule -Name DC -Cluster Cluster1

Name    RuleType                Groups        Enabled
----    --------                -------       -------
DC      DifferentFaultDomain    {DC1, DC2}       1
```

## <a name="affinity-rules"></a>Affinitätsregeln

Im Folgenden finden Sie einige gängige Szenarien für das Einrichten von Affinitätsregeln.

### <a name="scenario-1"></a>Szenario 1

Angenommen, Sie verfügen über eine SQL Server-VM und eine Webserver-VM. Diese beiden VMs müssen sich immer im selben Standort befinden, aber nicht notwendigerweise auf demselben Clusterknoten im Standort.  Mit `SameFaultDomain` ist dies möglich, wie hier gezeigt:

```powershell
New-ClusterAffinityRule -Name WebData -Ruletype SameFaultDomain -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL1,WEB1 –Name WebData -Cluster Cluster1

Set-ClusterAffinityRule -Name WebData -Enabled 1 -Cluster Cluster1
```

Um diese Regel und ihre Konfiguration anzuzeigen, verwenden Sie das Cmdlet **`Get-ClusterAffinityRule`** . Sie erhalten folgende Ausgabe:

```powershell
Get-ClusterAffinityRule -Name WebData -Cluster Cluster1

Name        RuleType          Groups        Enabled
----        ---------         ------        -------
WebData     SameFaultDomain   {SQL1, WEB1}     1
```

### <a name="scenario-2"></a>Szenario 2
Wir verwenden dasselbe Szenario wie oben, geben jetzt aber an, dass die VMs sich auf demselben Clusterknoten befinden müssen, aber nicht unbedingt im selben Standort. Dieses Szenario können Sie mit `SameNode` wie folgt einrichten:

```powershell
New-ClusterAffinityRule -Name WebData1 -Ruletype SameNode -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL1,WEB1 –Name WebData1 -Cluster Cluster1

Set-ClusterAffinityRule -Name WebData1 -Enabled 1 -Cluster Cluster1
```

Um die Regel und ihre Konfiguration anzuzeigen, verwenden Sie das Cmdlet **`Get-ClusterAffinityRule`** . Sie erhalten folgende Ausgabe:

```powershell
Get-ClusterAffinityRule -Name WebData1 -Cluster Cluster1

Name    RuleType    Groups        Enabled
----    --------    ------        -------
DC      SameNode    {SQL1, WEB1}     1
```

## <a name="combined-rules"></a>Kombinierte Regeln

Durch Kombinieren von Affinitäts- und Antiaffinitätsregeln können Sie ganz einfach verschiedene VM-Kombinationen in einem Cluster mit mehreren Standorten konfigurieren.  In diesem Szenario befinden sich drei VMs in jedem Standort: SQL Server (SQL), Webserver (WEB) und Domänencontroller (DC).  Für jede der Kombinationen können Sie Affinitätsregeln mit `SameFaultDomain` verwenden, um alle VMs im selben Standort zusammenzuhalten.  Sie können auch die Domänencontroller für jeden Standort mit Antiaffinitätsregeln und `DifferentFaultDomain` einrichten, um dafür zu sorgen, dass die Domänencontroller-VMs sich immer in separaten Standorten befinden:

```powershell
New-ClusterAffinityRule -Name Site1Trio -Ruletype SameFaultDomain -Cluster Cluster1

New-ClusterAffinityRule -Name Site2Trio -Ruletype SameFaultDomain -Cluster Cluster1

New-ClusterAffinityRule -Name TrioApart -Ruletype DifferentFaultDomain -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL1,WEB1,DC1 –Name Site1Trio -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL2,WEB2,DC2 –Name Site2Trio -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups DC1,DC2 –Name TrioApart -Cluster Cluster1

Set-ClusterAffinityRule -Name Site1Trio -Enabled 1 -Cluster Cluster1

Set-ClusterAffinityRule -Name Site2Trio -Enabled 1 -Cluster Cluster1

Set-ClusterAffinityRule -Name TrioApart -Enabled 1 -Cluster Cluster1
```

Um die Regeln und ihre Konfiguration anzuzeigen, verwenden Sie das Cmdlet **`Get-ClusterAffinityRule`** ohne die Option `-Name`. So erhalten Sie alle erstellten Regeln sowie die zugehörigen Ausgaben.

```powershell
Get-ClusterAffinityRule -Cluster Cluster1

Name        RuleType               Groups            Enabled
----        --------               ------            -------
Site1Trio   SameFaultDomain        {SQL1, WEB1, DC1}    1
Site2Trio   SameFaultDomain        {SQL2, WEB2, DC2}    1
TrioApart   DifferentFaultDomain   {DC1, DC2}           1
```

## <a name="storage-affinity-rules"></a>Speicheraffinitätsregeln

Sie können auch eine VM und die zugehörige VHDX in einem freigegebenen Clustervolume auf demselben Clusterknoten zusammenhalten. So lassen sich Sie CSV-Weiterleitungen verhindern, die das Starten oder Beenden einer VM verlangsamen können.  Sie können die SQL-VM und das freigegebene Clustervolume auf demselben Clusterknoten speichern, wenn Sie das oben beschriebene Szenario mit kombinierten Affinitäts- und Antiaffinitätsregeln berücksichtigen.  Verwenden Sie dazu die folgenden Befehle:

```powershell
New-ClusterAffinityRule -Name SQL1CSV1 -Ruletype SameNode -Cluster Cluster1

New-ClusterAffinityRule -Name SQL2CSV2 -Ruletype SameNode -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL1 –Name SQL1CSV1 -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL2 –Name SQL2CSV2 -Cluster Cluster1

Add-ClusterSharedVolumeToAffinityRule -ClusterSharedVolumes CSV1 -Name SQL1CSV1 -Cluster Cluster1

Add-ClusterSharedVolumeToAffinityRule -ClusterSharedVolumes CSV2 -Name SQL2CSV2 -Cluster Cluster1

Set-ClusterAffinityRule -Name SQL1CSV1 -Enabled 1 -Cluster Cluster1

Set-ClusterAffinityRule -Name SQL2CSV2 -Enabled 1 -Cluster Cluster1
```

Um die Regeln und ihre Konfiguration anzuzeigen, verwenden Sie das Cmdlet **`Get-ClusterAffinityRule`** ohne die Option „-Name“, und sehen Sie sich die Ausgabe an.

```powershell
Get-ClusterAffinityRule -Cluster Cluster1

Name        RuleType               Groups            Enabled
----        --------               ------            -------
Site1Trio   SameFaultDomain        {SQL1, WEB1, DC1}    1
Site2Trio   SameFaultDomain        {SQL2, WEB2, DC2}    1
TrioApart   DifferentFaultDomain   {DC1, DC2}           1
SQL1CSV1    SameNode               {SQL1, <CSV1-GUID>}  1
SQL2CSV2    SameNode               {SQL2, <CSV2-GUID>}  1
```

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie Ihre VMs verwalten. Mehr dazu finden Sie unter [Verwalten von VMs in Azure Stack HCI mithilfe von Windows Admin Center](../manage/vm.md).
