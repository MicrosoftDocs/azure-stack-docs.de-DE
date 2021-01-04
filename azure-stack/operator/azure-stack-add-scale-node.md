---
title: Hinzufügen von Knoten einer Skalierungseinheit in Azure Stack Hub
description: Erfahren Sie, wie Sie Knoten einer Skalierungseinheit in Azure Stack Hub zu Skalierungseinheiten hinzufügen.
author: mattbriggs
ms.topic: article
ms.date: 11/05/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 11/05/2020
ms.openlocfilehash: fac60db9ad1f3ae8be248b4f61a3c16179763a7e
ms.sourcegitcommit: 79e8df69b139bfa21eb83aceb824b97e7f418c03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97364199"
---
# <a name="add-additional-scale-unit-nodes-in-azure-stack-hub"></a>Hinzufügen zusätzlicher Knoten einer Skalierungseinheit in Azure Stack Hub

Sie können die Gesamtkapazität einer vorhandenen Skalierungseinheit durch Hinzufügen eines zusätzlichen physischen Computers erhöhen. Der physische Computer wird auch als Knoten einer Skalierungseinheit bezeichnet. Jeder neue Knoten einer Skalierungseinheit, den Sie hinzufügen, muss in CPU-Typ, Speicher sowie Datenträgernummer und -größe mit den Knoten übereinstimmen, die bereits in der Skalierungseinheit vorhanden sind. Das Entfernen von Skalierungseinheitknoten zum Herunterskalieren aufgrund von architektonischen Einschränkungen wird von Azure Stack Hub nicht unterstützt. Es ist nur möglich, die Kapazität durch das Hinzufügen von Knoten zu erweitern.

Um einen Knoten einer Skalierungseinheit hinzuzufügen, melden Sie sich bei Azure Stack Hub an, und führen Sie Tools Ihres Originalgeräteherstellers (OEM) aus. Die OEM-Tools werden auf dem Hardwarelebenszyklushost (HLH) ausgeführt, um sicherzustellen, dass der neue physische Computer die gleiche Firmwareebene wie die vorhandenen Knoten aufweist.

Das folgende Flussdiagramm zeigt den allgemeinen Prozess des Hinzufügens eines Knotens zu einer Skalierungseinheit:

![Flow zum Hinzufügen zu Skalierungseinheiten](media/azure-stack-add-scale-node/add-node-flow.svg)
<br> *Ob Ihr OEM-Hardwarehersteller die Platzierung des physischen Serverracks vornimmt und die Firmware aktualisiert, hängt von Ihrem Supportvertrag ab.*

Das Hinzufügen eines neuen Knotens kann mehrere Stunden oder Tage dauern. Es gibt keine Auswirkungen auf Workloads, die auf dem System ausgeführt werden, während ein zusätzlicher Skalierungseinheitknoten hinzugefügt wird.

> [!NOTE]  
> Führen Sie keinen der folgenden Vorgänge durch, während bereits ein Vorgang zum Hinzufügen eines Knotens zu einer Skalierungseinheit ausgeführt wird:
>
>  - Aktualisieren von Azure Stack Hub
>  - Rotieren von Zertifikaten
>  - Beenden von Azure Stack Hub
>  - Reparieren von Skalierungseinheitknoten
>  - Einen weiteren Knoten hinzufügen (der vorherige Fehler bei der Aktion zum Hinzufügen eines Knotens wird auch als in Bearbeitung angesehen)

## <a name="add-scale-unit-nodes"></a>Hinzufügen von Skalierungseinheitknoten

Die folgenden Schritte bieten einen allgemeinen Überblick über das Hinzufügen eines Knotens. Befolgen Sie diese Schritte nicht, ohne zuvor die vom Originalgerätehersteller bereitgestellte Dokumentation zur Kapazitätserweiterung zu Rate gezogen zu haben.

1. Platzieren Sie den neuen physischen Server im Rack, und schließen Sie ihn ordnungsgemäß an. 
2. Aktivieren Sie gegebenenfalls physische Switchports, und passen Sie bei Bedarf Zugriffssteuerungslisten (ACLs) an.
3. Konfigurieren Sie die richtige IP-Adresse im Baseboard-Verwaltungscontroller, und übernehmen Sie alle BIOS-Einstellungen entsprechend der Dokumentation des Originalgeräteherstellers.
4. Übernehmen Sie die aktuelle Firmwarebaseline für alle Komponenten, indem Sie die Tools des Hardwareherstellers verwenden, die auf dem HLH ausgeführt werden.
5. Führen Sie den Vorgang zum Hinzufügen eines Knotens im Azure Stack Hub-Administratorportal aus.
6. Überprüfen Sie, ob der Knoten erfolgreich hinzugefügt wurde. Hierzu prüfen Sie den [**Status** der Skalierungseinheit](#monitor-add-node-operations). 

## <a name="add-the-node"></a>Hinzufügen des Knotens

Zum Hinzufügen neuer Knoten können Sie das Administratorportal oder PowerShell verwenden. Bei diesem Vorgang wird zunächst der neue Knoten zur Skalierungseinheit als verfügbare Computekapazität hinzugefügt, und dann die Speicherkapazität automatisch erweitert. Die Kapazität wird automatisch erweitert, da es sich bei Azure Stack Hub um ein hyperkonvergentes System handelt, in dem *Compute-* und *Speicherressourcen* gemeinsam skaliert werden.

### <a name="administrator-portal"></a>[Administratorportal](#tab/portal)

1. Melden Sie sich beim Azure Stack Hub-Administratorportal als Azure Stack-Operator an.
2. Navigieren Sie zu **+Ressource erstellen** > **Kapazität** > **Skalierungseinheitknoten**.
   ![Scale Unit Node](media/azure-stack-add-scale-node/select-node1.png) (Skalierungseinheitknoten)
3. Wählen Sie im Bereich **Knoten hinzufügen** die *Region* und dann die *Skalierungseinheit* aus, der der Knoten hinzugefügt werden soll. Geben Sie auch die *BMC-IP-ADRESSE* für den hinzugefügten Skalierungseinheitenknoten an. Sie können immer nur jeweils einen Knoten hinzufügen.
   ![Hinzufügen von Knotendetails](media/azure-stack-add-scale-node/select-node2.png)
 

### <a name="powershell-az"></a>[PowerShell Az](#tab/Az)

Fügen Sie mithilfe des Cmdlets **Add-AzsScaleUnitNode** einen Knoten hinzu.  

Ersetzen Sie vor der Verwendung der folgenden PowerShell-Beispielskripts die Werte *name_of_new_node*, *name_of_scale_unit_cluster* und *BMCIP_address_of_new_node* durch Werte aus Ihrer Azure Stack Hub-Umgebung.

  > [!Note]  
  > Bei der Benennung eines Knotens müssen Sie den Namen auf weniger als 15 Zeichen beschränken. Außerdem können Sie keinen Namen verwenden, der ein Leerzeichen oder eines der folgenden Zeichen enthält: `\`, `/`, `:`, `*`, `?`, `"`, `<`, `>`, `|`, `\`, `~`, `!`, `@`, `#`, `$`, `%`, `^`, `&`, `(`, `)`, `{`, `}`, `_`.

**Hinzufügen eines Knotens:**
  ```powershell
  ## Add a single Node 
    Add-AzsScaleUnitNode -BMCIPv4Address "<BMCIP_address_of_new_node>" -computername "<name_of_new_node>" -ScaleUnit "<name_of_scale_unit_cluster>" 
  ```  

### <a name="powershell-azurerm"></a>[PowerShell AzureRM](#tab/AzureRM)

Fügen Sie mithilfe des Cmdlets **New-AzsScaleUnitNodeObject** einen Knoten hinzu.  

Bevor Sie eines der folgenden PowerShell-Beispielskripts verwenden, ersetzen Sie die Werte *Knotennamen* und *IP-Adressen* durch Werte aus Ihrer Azure Stack Hub-Umgebung.

  > [!Note]  
  > Bei der Benennung eines Knotens müssen Sie den Namen auf weniger als 15 Zeichen beschränken. Außerdem können Sie keinen Namen verwenden, der ein Leerzeichen oder eines der folgenden Zeichen enthält: `\`, `/`, `:`, `*`, `?`, `"`, `<`, `>`, `|`, `\`, `~`, `!`, `@`, `#`, `$`, `%`, `^`, `&`, `(`, `)`, `{`, `}`, `_`.

**Hinzufügen eines Knotens:**
  ```powershell
  ## Add a single Node 
  $NewNode=New-AzsScaleUnitNodeObject -computername "<name_of_new_node>" -BMCIPv4Address "<BMCIP_address_of_new_node>" 
 
  Add-AzsScaleUnitNode -NodeList $NewNode -ScaleUnit "<name_of_scale_unit_cluster>" 
  ```  

---

## <a name="monitor-add-node-operations"></a>Überwachen des Vorgangs zum Hinzufügen eines Knotens 
Über das Administratorportal oder PowerShell können Sie den Status des Vorgangs zum Hinzufügen eines Knotens abrufen. Vorgänge zum Hinzufügen eines Knoten können mehrere Stunden oder sogar Tage in Anspruch nehmen.

### <a name="use-the-administrator-portal"></a>Verwenden des Administratorportals 
Um das Hinzufügen eines neuen Knotens zu überwachen, überprüfen Sie im Administratorportal die Objekte für die Skalierungseinheit bzw. den Skalierungseinheitenknoten. Wechseln Sie zu **Regionsverwaltung** > **Skalierungseinheiten**. Wählen Sie anschließend die Skalierungseinheit bzw. den Skalierungseinheitknoten aus, die Sie überprüfen möchten. 

### <a name="use-powershell"></a>Verwenden von PowerShell
Der Status für Skalierungseinheit und Skalierungseinheitknoten kann mit PowerShell wie folgt abgerufen werden:
  ```powershell
  #Retrieve Status for the Scale Unit
  Get-AzsScaleUnit|select name,state
 
  #Retrieve Status for each Scale Unit Node
  Get-AzsScaleUnitNode |Select Name, ScaleUnitNodeStatus
```

### <a name="status-for-the-add-node-operation"></a>Status für den Vorgang zum Hinzufügen eines Knoten 
**Für eine Skalierungseinheit:**

|Status               |BESCHREIBUNG  |
|---------------------|---------|
|Wird ausgeführt              |Alle Knoten sind aktiv an der Skalierungseinheit beteiligt.|
|Beendet              |Der Skalierungseinheitknoten ist entweder ausgefallen oder nicht erreichbar.|
|Wird erweitert            |Mindestens ein Skalierungseinheitknoten wird derzeit als Computekapazität hinzugefügt.|
|Konfigurieren von Speicher  |Die Computekapazität wurde erweitert, und die Speicherkonfiguration wird ausgeführt.|
|Korrektur erforderlich |Ein Fehler wurde ermittelt, der die Reparatur von mindestens einem Skalierungseinheitknoten erfordert.|


**Für einen Skalierungseinheitknoten:**

|Status                |BESCHREIBUNG  |
|----------------------|---------|
|Wird ausgeführt               |Der Knoten ist aktiv an der Skalierungseinheit beteiligt.|
|Beendet               |Der Knoten ist nicht verfügbar.|
|Wird hinzugefügt                |Der Knoten wird aktiv zur Skalierungseinheit hinzugefügt.|
|Wird repariert             |Der Knoten wird aktiv repariert.|
|Wartung           |Der Knoten wurde angehalten, und es wird kein aktiver Benutzerworkload ausgeführt. |
|Korrektur erforderlich  |Ein Fehler wurde ermittelt, der eine Reparatur des Knotens erfordert.|


## <a name="troubleshooting"></a>Problembehandlung
Die folgenden Probleme treten häufig beim Hinzufügen eines Knotens auf. 

**Szenario 1:** Beim Vorgang zum Hinzufügen eines Knotens zur Skalierungseinheit tritt ein Fehler auf, aber mindestens ein Knoten wird mit dem Status „Angehalten“ aufgeführt.  
- Problemlösung: Verwenden Sie den Reparaturvorgang, um einen oder mehrere Knoten zu reparieren. Es kann immer nur ein einziger Reparaturvorgang gleichzeitig ausgeführt werden.

**Szenario 2:** Ein oder mehrere Skalierungseinheitknoten wurden hinzugefügt, aber die Speichererweiterung war nicht erfolgreich. In diesem Szenario meldet das Objekt des Skalierungseinheitknotens den Status „Wird ausgeführt“, aber die Aufgabe „Speicher wird konfiguriert“ wird nicht gestartet.  
- Problemlösung: Verwenden Sie den privilegierten Endpunkt, um die Speicherintegrität zu überprüfen, indem Sie das folgende PowerShell-Cmdlet ausführen:
  ```powershell
     Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
  ```
 
**Szenario 3:** Sie haben eine Warnung erhalten, die darauf hinweist, dass ein Fehler bei der horizontalen Skalierung des Speichers aufgetreten ist.  
- Problemlösung: In diesem Fall war die Aufgabe der Speicherkonfiguration nicht erfolgreich. Bitte wenden Sie sich bei diesem Problem an den Support.


## <a name="next-steps"></a>Nächste Schritte 
[Hinzufügen öffentlicher IP-Adressen](azure-stack-add-ips.md) 
