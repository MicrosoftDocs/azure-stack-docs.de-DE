---
title: Hinzufügen oder Entfernen von Servern für einen Azure Stack HCI-Cluster
description: Hier erfahren Sie, wie Sie Serverknoten in Azure Stack HCI in einem Cluster hinzufügen oder entfernen.
ms.topic: how-to
author: v-dasis
ms.author: v-dasis
ms.reviewer: jgerend
ms.date: 07/21/2020
ms.openlocfilehash: 9dfdbcab43694146c4190db8ef29905626a4d597
ms.sourcegitcommit: 0e52f460295255b799bac92b40122a22bf994e27
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/21/2020
ms.locfileid: "86866643"
---
# <a name="add-or-remove-servers-for-an-azure-stack-hci-cluster"></a>Hinzufügen oder Entfernen von Servern für einen Azure Stack HCI-Cluster

> Gilt für: Azure Stack HCI, Version 20H2; Windows Server 2019

Sie können in Azure Stack HCI problemlos Server zu einem Cluster hinzufügen oder daraus entfernen. Beachten Sie, dass jeder neue physische Server genau mit den anderen Servern im Cluster hinsichtlich CPU-Typ, Arbeitsspeicher, Anzahl von Laufwerken und Typ und Größe der Laufwerke übereinstimmen muss.

Wenn Sie einen Server hinzufügen oder entfernen, müssen Sie anschließend auch eine Clusterüberprüfung durchführen, um sicherzustellen, dass der Cluster ordnungsgemäß funktioniert.

## <a name="obtain-oem-hardware"></a>Beziehen von OEM-Hardware

Der erste Schritt besteht darin, neue HCI-Hardware vom ursprünglichen OEM zu erwerben. Wenn Sie neue Serverhardware für die Verwendung in Ihrem Cluster hinzufügen, lesen Sie stets die vom OEM bereitgestellte Dokumentation.

1. Platzieren Sie den neuen physischen Server im Rack, und schließen Sie ihn ordnungsgemäß an.
1. Aktivieren Sie gegebenenfalls physische Switchports, und passen Sie bei Bedarf Zugriffssteuerungslisten (ACLs) und VLAN-IDs an.
1. Konfigurieren Sie die richtige IP-Adresse im Baseboard-Verwaltungscontroller, und wenden Sie alle BIOS-Einstellungen entsprechend den Anweisungen des OEM an.
1. Übernehmen Sie die aktuelle Firmwarebaseline für alle Komponenten, indem Sie die Tools des OEM verwenden.
1. Führen Sie OEM-Validierungstests aus, um die Homogenität mit den vorhandenen Clusterservern sicherzustellen.

## <a name="add-a-server-to-the-cluster"></a>Hinzufügen eines Servers zum Cluster

Nachdem der Server ordnungsgemäß hochgefahren wurde, können Sie den Server mit dem Windows Admin Center dem Cluster hinzufügen.

:::image type="content" source="media/manage-cluster/add-server.png" alt-text="Bildschirm „Server hinzufügen“" lightbox="media/manage-cluster/add-server.png":::

1. Wählen Sie in der oberen Dropdownliste in **Windows Admin Center**die Option **Cluster-Manager** aus.
1. Wählen Sie unter **Clusterverbindungen** den Cluster aus.
1. Wählen Sie unter **Tools** die Option **Server** aus.
1. Wählen Sie unter **Server**, die Registerkarte **Inventar** aus.
1. Wählen Sie rechts auf der Registerkarte **Inventar** die Option **Hinzufügen** aus.
1. Geben Sie unter **Servername** den voll qualifizierten Domänennamen des Servers ein, den Sie hinzufügen möchten, klicken Sie auf **Hinzufügen**, und klicken Sie dann unten auf **Hinzufügen**.
1. Vergewissern Sie sich, dass der Server erfolgreich zu Ihrem Cluster hinzugefügt wurde.

## <a name="remove-a-server-from-the-cluster"></a>Entfernen eines Servers aus dem Cluster

Die Schritte zum Entfernen eines Servers aus dem Cluster ähneln denen zum Hinzufügen eines Servers zu einem Cluster.

Beachten Sie, dass beim Entfernen eines Servers auch alle virtuellen Computer, Laufwerke und Workloads entfernt werden, die dem Server zugeordnet sind.

:::image type="content" source="media/manage-cluster/remove-server.png" alt-text="Dialogfeld „Server entfernen“" lightbox="media/manage-cluster/remove-server.png":::

1. Wählen Sie in der oberen Dropdownliste in **Windows Admin Center**die Option **Cluster-Manager** aus.
1. Wählen Sie unter **Clusterverbindungen** den Cluster aus.
1. Wählen Sie unter **Tools** die Option **Server** aus.
1. Wählen Sie unter **Server**, die Registerkarte **Inventar** aus.
1. Wählen Sie auf der Registerkarte **Inventur** den Server aus, den Sie entfernen möchten, und wählen Sie dann **Entfernen** aus.
1. Um auch alle Serverlaufwerke aus dem Speicherpool zu entfernen, aktivieren Sie das entsprechende Kontrollkästchen.
1. Vergewissern Sie sich, dass der Server erfolgreich aus Ihrem Cluster entfernt wurde.

Achten Sie bei jedem Hinzufügen oder Entfernen eines Serverknotens zu bzw. aus einem Cluster unbedingt darauf, anschließend einen Test zur Clustervalidierung durchzuführen.

## <a name="next-steps"></a>Nächste Schritte

- Sie sollten den Cluster nach dem Hinzufügen oder Entfernen eines Knotens überprüfen. Weitere Informationen finden Sie unter [Überprüfen des Clusters](../deploy/validate.md).