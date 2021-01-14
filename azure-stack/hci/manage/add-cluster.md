---
title: Hinzufügen oder Entfernen von Servern für einen Azure Stack HCI-Cluster
description: Hier erfahren Sie, wie Sie Serverknoten in Azure Stack HCI in einem Cluster hinzufügen oder entfernen.
ms.topic: how-to
author: v-dasis
ms.author: v-dasis
ms.reviewer: jgerend
ms.date: 01/06/2021
ms.openlocfilehash: 8b27859b7afab0a6e279774e43d0269f6d58065a
ms.sourcegitcommit: 1465bca8b7f87ea6f24faf47e86c2ba497943b28
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2021
ms.locfileid: "98103131"
---
# <a name="add-or-remove-servers-for-an-azure-stack-hci-cluster"></a>Hinzufügen oder Entfernen von Servern für einen Azure Stack HCI-Cluster

> Gilt für: Azure Stack HCI, Version 20H2

Sie können in Azure Stack HCI problemlos Server zu einem Cluster hinzufügen oder daraus entfernen. Beachten Sie, dass jeder neue physische Server genau mit den anderen Servern im Cluster hinsichtlich CPU-Typ, Arbeitsspeicher, Anzahl von Laufwerken und Typ und Größe der Laufwerke übereinstimmen muss.

Wenn Sie einen Server hinzufügen oder entfernen, müssen Sie anschließend auch eine Clusterüberprüfung durchführen, um sicherzustellen, dass der Cluster ordnungsgemäß funktioniert. Dies gilt sowohl für Nicht-Stretched Cluster als auch für Stretched Cluster.

## <a name="obtain-oem-hardware"></a>Beziehen von OEM-Hardware

Der erste Schritt besteht darin, neue HCI-Hardware vom ursprünglichen OEM zu erwerben. Wenn Sie neue Serverhardware für die Verwendung in Ihrem Cluster hinzufügen, lesen Sie stets die vom OEM bereitgestellte Dokumentation.

1. Platzieren Sie den neuen physischen Server im Rack, und schließen Sie ihn ordnungsgemäß an.
1. Aktivieren Sie gegebenenfalls physische Switchports, und passen Sie bei Bedarf Zugriffssteuerungslisten (ACLs) und VLAN-IDs an.
1. Konfigurieren Sie die richtige IP-Adresse im Baseboard-Verwaltungscontroller, und wenden Sie alle BIOS-Einstellungen entsprechend den Anweisungen des OEM an.
1. Übernehmen Sie die aktuelle Firmwarebaseline für alle Komponenten, indem Sie die Tools des OEM verwenden.
1. Führen Sie OEM-Validierungstests aus, um die Homogenität mit den vorhandenen Clusterservern sicherzustellen.

## <a name="add-a-server-to-a-cluster"></a>Hinzufügen eines Servers zu einem Cluster

Nachdem der Server ordnungsgemäß hochgefahren wurde, können Sie den Server mit dem Windows Admin Center dem Cluster hinzufügen.

:::image type="content" source="media/manage-cluster/add-server.png" alt-text="Bildschirm „Server hinzufügen“" lightbox="media/manage-cluster/add-server.png":::

1. Wählen Sie in der oberen Dropdownliste in **Windows Admin Center** die Option **Cluster-Manager** aus.
1. Wählen Sie unter **Clusterverbindungen** den Cluster aus.
1. Wählen Sie unter **Tools** die Option **Server** aus.
1. Wählen Sie unter **Server**, die Registerkarte **Inventar** aus.
1. Wählen Sie rechts auf der Registerkarte **Inventar** die Option **Hinzufügen** aus.
1. Geben Sie unter **Servername** den voll qualifizierten Domänennamen des Servers ein, den Sie hinzufügen möchten, klicken Sie auf **Hinzufügen**, und klicken Sie dann unten auf **Hinzufügen**.
1. Vergewissern Sie sich, dass der Server erfolgreich zu Ihrem Cluster hinzugefügt wurde.

## <a name="remove-a-server-from-a-cluster"></a>Entfernen eines Servers aus einem Cluster

Die Schritte zum Entfernen eines Servers aus dem Cluster ähneln denen zum Hinzufügen eines Servers zu einem Cluster.

Beachten Sie, dass beim Entfernen eines Servers auch alle virtuellen Computer, Laufwerke und Workloads entfernt werden, die dem Server zugeordnet sind.

:::image type="content" source="media/manage-cluster/remove-server.png" alt-text="Dialogfeld „Server entfernen“" lightbox="media/manage-cluster/remove-server.png":::

1. Wählen Sie in der oberen Dropdownliste in **Windows Admin Center** die Option **Cluster-Manager** aus.
1. Wählen Sie unter **Clusterverbindungen** den Cluster aus.
1. Wählen Sie unter **Tools** die Option **Server** aus.
1. Wählen Sie unter **Server**, die Registerkarte **Inventar** aus.
1. Wählen Sie auf der Registerkarte **Inventur** den Server aus, den Sie entfernen möchten, und wählen Sie dann **Entfernen** aus.
1. Um auch alle Serverlaufwerke aus dem Speicherpool zu entfernen, aktivieren Sie das entsprechende Kontrollkästchen.
1. Vergewissern Sie sich, dass der Server erfolgreich aus Ihrem Cluster entfernt wurde.

Achten Sie bei jedem Hinzufügen oder Entfernen von Servern zu bzw. aus einem Cluster unbedingt darauf, anschließend einen Test zur Clustervalidierung durchzuführen.

## <a name="add-server-pairs-to-a-stretched-cluster"></a>Hinzufügen von Serverpaaren zu einem Stretched Cluster

Stretched Cluster erfordern dieselbe Anzahl von Serverknoten und dieselbe Anzahl von Laufwerken an jedem Standort. Wird einem Stretched Cluster ein Serverpaar hinzugefügt, werden dessen Laufwerke sofort dem Speicherpool an beiden Standorten des Stretched Clusters hinzugefügt. Haben die Speicherpools an beiden Standorten zum Zeitpunkt des Hinzufügens nicht dieselbe Größe, wird das Hinzufügen abgelehnt. Der Grund dafür ist, dass die Größe der Speicherpools an beiden Standorten identisch sein muss.

Nehmen Sie sich einige Minuten Zeit, um sich das Video zum Hinzufügen von Serverknoten zu einem Stretched Cluster anzusehen:

> [!VIDEO https://www.youtube.com/embed/AVHPkRmsZ5Y]

Server werden mit Windows PowerShell zu einem Stretched Cluster hinzugefügt oder daraus entfernt. Mit den Cmdlets [Get-ClusterFaultDomainXML](https://docs.microsoft.com/powershell/module/failoverclusters/get-clusterfaultdomainxml) und [Set-ClusterFaultDomainXML](https://docs.microsoft.com/powershell/module/failoverclusters/set-clusterfaultdomainxml) ändern Sie zunächst die Standortinformationen (Fehlerdomäneninformationen), bevor Sie die Server hinzufügen.

Anschließend können Sie das Serverpaar mit dem Cmdlet [Add-ClusterNode](https://docs.microsoft.com/powershell/module/failoverclusters/add-clusternode) jedem Standort gleichzeitig hinzufügen, wodurch es ermöglicht wird, auch die Laufwerke jedes neuen Servers gleichzeitig hinzuzufügen.

Normalerweise verwalten Sie Cluster über einen Remotecomputer statt über einen Server in einem Cluster. Dieser Remotecomputer wird als Verwaltungscomputer bezeichnet.

> [!NOTE]
> Wenn Sie PowerShell-Befehle auf einem Verwaltungscomputer ausführen, geben Sie den `-Cluster`-Parameter mit dem Namen des Clusters an, den Sie verwalten.

OK, fangen wir an:

1. Verwenden Sie die folgenden PowerShell-Cmdlets, um den Zustand des Clusters zu ermitteln:

    Gibt die Liste der aktiven Server im Cluster zurück:

     ```powershell
    Get-ClusterNode
    ```

    Gibt den Zustand des Clusterspeicherpools zurück:

    ```powershell
    Get-StoragePool pool*
    ```

    Listet auf, welche Server sich an welchem Standort befinden (Fehlerdomäne):

    ```powershell
    Get-ClusterFaultDomain
    ```

1. Öffnen Sie die Datei `Sites.xml` in Editor oder in einem anderen Text-Editor:

    ```powershell
    Get-ClusterFaultDomainXML | out-file sites.xml
    ```
 
    ```powershell
    notepad
    ```

1. Navigieren Sie zu dem Speicherort, in dem sich die Datei `Sites.xml` lokal auf Ihrem Verwaltungs-PC befindet, und öffnen Sie die Datei. Die Datei `Sites.xml` sieht in etwa wie folgt aus:

    ```
    <Topology>
        <Site Name="Site1" Description="" Location="">
            <Node Name="Server1" Description="" Location="">
            <Node Name="Server2" Description="" Location="">
        </Site>
        <Site Name="Site2" Description="" Location="">
            <Node Name="Server3" Description="" Location="">
            <Node Name="Server4" Description="" Location="">
        </Site>
    <Topology>
    ```

1. Wenn Sie dieses Beispiel verwenden, fügen Sie jedem Standort wie folgt jeweils einen Server (`Server5`, `Server6`) hinzu:

    ```
    <Topology>
        <Site Name="Site1" Description="" Location="">
            <Node Name="Server1" Description="" Location="">
            <Node Name="Server2" Description="" Location="">
            <Node Name="Server5" Description="" Location="">
        </Site>
        <Site Name="Site2" Description="" Location="">
            <Node Name="Server3" Description="" Location="">
            <Node Name="Server4" Description="" Location="">
            <Node Name="Server6" Description="" Location="">
        </Site>
    <Topology>
    ```

1. Ändern Sie die aktuellen Standortinformationen (Fehlerdomäneninformationen).  Mit dem ersten Befehl wird eine Variable festgelegt, die den Inhalt der Datei `Sites.xml` aufnimmt, und dieser Inhalt wird ausgegeben. Mit dem zweiten Befehl wird die Änderung anhand der Variablen `$XML` festgelegt.

    ```
    $XML = Get-Content .\sites.xml | out-string
    Set-ClusterFaultDomainXML -xml $XML
    ```

1. Vergewissern Sie sich, dass die von Ihnen vorgenommenen Änderungen korrekt sind:

    ```
    Get-ClusterFaultDomain
    ```

1. Verwenden Sie das Cmdlet `Add-ClusterNode`, um Ihrem Cluster das Serverpaar hinzuzufügen:

    ```
    Add-ClusterNode -Name Server5,Server6
    ```

Sobald die Server erfolgreich hinzugefügt wurden, werden die zugehörigen Laufwerke automatisch den Speicherpools jedes Standorts hinzugefügt. Schließlich erstellt der Integritätsdienst einen Speicherauftrag, mit dem die neuen Laufwerke eingebunden werden.

## <a name="remove-server-pairs-from-a-stretched-cluster"></a>Entfernen von Serverpaaren aus einem Stretched Cluster

Das Entfernen eines Serverpaars aus einem Stretched Cluster erfolgt ähnlich wie das Hinzufügen eines Serverpaars, jedoch wird hierfür das Cmdlet [Remove-ClusterNode](https://docs.microsoft.com/powershell/module/failoverclusters/remove-clusternode) verwendet.

1. Verwenden Sie die folgenden PowerShell-Cmdlets, um den Zustand des Clusters zu ermitteln:

    Gibt die Liste der aktiven Server im Cluster zurück:

     ```powershell
    Get-ClusterNode
    ```

    Gibt den Zustand des Clusterspeicherpools zurück:

    ```powershell
    Get-StoragePool pool*
    ```

    Listet auf, welche Server sich an welchem Standort befinden (Fehlerdomäne):

    ```powershell
    Get-ClusterFaultDomain
    ```

1. Öffnen Sie die Datei `Sites.xml` in Editor oder in einem anderen Text-Editor:

    ```powershell
    Get-ClusterFaultDomainXML | out-file sites.xml
    ```
 
    ```powershell
    notepad
    ```

1. Verwenden Sie wieder das vorherige Beispiel, und entfernen Sie für jeden Standort den XML-Eintrag `<Node Name="Server5" Description="" Location="">` bzw. `<Node Name="Server6" Description="" Location="">` aus der Datei `Sites.xml`.
1. Ändern Sie mit den beiden folgenden Cmdlets die aktuellen Standortinformationen (Fehlerdomäneninformationen):

    ```
    $XML = Get-Content .\sites.xml | out-string
    Set-ClusterFaultDomainXML -xml $XML
    ```

1. Vergewissern Sie sich, dass die von Ihnen vorgenommenen Änderungen korrekt sind:

    ```
    Get-ClusterFaultDomain
    ```
1. Verwenden Sie das Cmdlet `Remove-ClusterNode`, um die Serverpaare aus dem Cluster zu entfernen:

    ```
    Remove-ClusterNode -Name Server5,Server6
    ```

Sobald die Server erfolgreich entfernt wurden, werden die zugehörigen Laufwerke automatisch aus den Standortpools entfernt. Schließlich erstellt der Integritätsdienst einen Speicherauftrag, mit dem diese Laufwerke entfernt werden.

## <a name="next-steps"></a>Nächste Schritte

- Sie sollten den Cluster nach dem Hinzufügen oder Entfernen eines Servers überprüfen. Weitere Informationen finden Sie unter [Überprüfen eines Azure Stack HCI-Clusters](../deploy/validate.md).