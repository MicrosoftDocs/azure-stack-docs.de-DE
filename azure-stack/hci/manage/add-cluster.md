---
title: Hinzufügen oder Entfernen von Servern für einen Azure Stack HCI-Cluster
description: Hier erfahren Sie, wie Sie Serverknoten in Azure Stack HCI in einem Cluster hinzufügen oder entfernen.
ms.topic: how-to
author: v-dasis
ms.author: v-dasis
ms.reviewer: jgerend
ms.date: 05/20/2020
ms.openlocfilehash: 81b78e542fe14981e0b0ba501a326a537a2a54cb
ms.sourcegitcommit: 76af742a42e807c400474a337e29d088ede8a60d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/22/2020
ms.locfileid: "85196816"
---
# <a name="add-or-remove-servers-for-an-azure-stack-hci-cluster"></a>Hinzufügen oder Entfernen von Servern für einen Azure Stack HCI-Cluster

> Gilt für: Windows Server 2019

Sie können in Azure Stack HCI problemlos Server zu einem Cluster hinzufügen oder daraus entfernen. Beachten Sie, dass jeder neue physische Server genau mit den anderen Servern im Cluster hinsichtlich CPU-Typ, Arbeitsspeicher, Anzahl von Laufwerken und Typ und Größe der Laufwerke übereinstimmen muss.

Wenn Sie einen Server hinzufügen oder entfernen, müssen Sie anschließend auch eine Clusterüberprüfung durchführen, um sicherzustellen, dass der Cluster ordnungsgemäß funktioniert.

## <a name="obtain-server-hardware-from-your-oem"></a>Beziehen von Serverhardware vom OEM

Der erste Schritt besteht darin, neue HCI-Hardware vom ursprünglichen OEM zu erwerben. Wenn Sie neue Serverhardware für die Verwendung in Ihrem Cluster hinzufügen, lesen Sie stets die vom OEM bereitgestellte Dokumentation.

1. Platzieren Sie den neuen physischen Server im Rack, und schließen Sie ihn ordnungsgemäß an.
1. Aktivieren Sie gegebenenfalls physische Switchports, und passen Sie bei Bedarf Zugriffssteuerungslisten (ACLs) und VLAN-IDs an.
1. Konfigurieren Sie die richtige IP-Adresse im Baseboard-Verwaltungscontroller, und wenden Sie alle BIOS-Einstellungen entsprechend den Anweisungen des OEM an.
1. Übernehmen Sie die aktuelle Firmwarebaseline für alle Komponenten, indem Sie die Tools des OEM verwenden.
1. Führen Sie OEM-Validierungstests aus, um die Homogenität mit den vorhandenen Clusterservern sicherzustellen.

## <a name="add-the-server-to-the-cluster"></a>Hinzufügen des Servers zum Cluster

Nachdem der Server ordnungsgemäß hochgefahren wurde, können Sie den Server mit dem Windows Admin Center dem Cluster hinzufügen.

:::image type="content" source="media/manage-cluster/add-server.png" alt-text="Bildschirm „Server hinzufügen“":::

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

:::image type="content" source="media/manage-cluster/remove-server.png" alt-text="Dialogfeld „Server entfernen“":::

1. Wählen Sie in der oberen Dropdownliste in **Windows Admin Center**die Option **Cluster-Manager** aus.
1. Wählen Sie unter **Clusterverbindungen** den Cluster aus.
1. Wählen Sie unter **Tools** die Option **Server** aus.
1. Wählen Sie unter **Server**, die Registerkarte **Inventar** aus.
1. Wählen Sie auf der Registerkarte **Inventur** den Server aus, den Sie entfernen möchten, und wählen Sie dann **Entfernen** aus.
1. Um auch alle Serverlaufwerke aus dem Speicherpool zu entfernen, aktivieren Sie das entsprechende Kontrollkästchen.
1. Vergewissern Sie sich, dass der Server erfolgreich aus Ihrem Cluster entfernt wurde.

## <a name="validate-the-cluster"></a>Überprüfen des Clusters

Wenn Sie einen Server zu einem Cluster hinzugefügt oder daraus entfernt haben, müssen Sie den Cluster überprüfen. Die Clusterüberprüfung muss ohne Fehler bestanden werden, damit Sie von Microsoft unterstützt wird.

:::image type="content" source="media//manage-cluster/validate-cluster.png" alt-text="Dialogfeld „Cluster überprüfen“":::

1. Wählen Sie in der oberen Dropdownliste in **Windows Admin Center**die Option **Cluster-Manager** aus.
1. Wählen Sie unter **Tools** die Option **Server** aus.
1. Wählen Sie unter **Server** die Registerkarte **Inventar**, die Option **Mehr** und dann **Cluster überprüfen** aus.
1. Vergewissern Sie sich, dass alle Überprüfungsschritte erfolgreich bestanden wurden.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur Clusterüberprüfung finden Sie unter [Überprüfen der Hardware für einen Failovercluster](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134244(v=ws.11)).