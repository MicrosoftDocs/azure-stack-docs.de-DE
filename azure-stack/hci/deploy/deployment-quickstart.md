---
title: Schnellstart zum Erstellen eines Azure Stack HCI-Clusters und Registrieren des Clusters bei Azure
description: Hier erfahren Sie, wie Sie Azure Stack HCI bereitstellen, einen Cluster mithilfe des Windows Admin Center erstellen und diesen bei Azure registrieren.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 12/10/2020
ms.openlocfilehash: 1787218c32feaa0e944946b6b36614432ea529f1
ms.sourcegitcommit: 97ecba06aeabf2f30de240ac283b9bb2d49d62f0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97011772"
---
# <a name="quickstart-create-an-azure-stack-hci-cluster-and-register-it-with-azure"></a>Schnellstart: Erstellen eines Azure Stack HCI-Clusters und Registrieren des Clusters bei Azure

> Gilt für: Azure Stack HCI, Version 20H2

In dieser Schnellstartanleitung erfahren Sie, wie Sie einen Azure Stack HCI-Cluster mit zwei Servern und einem einzelnen Standort bereitstellen und bei Azure registrieren. Informationen zu Bereitstellungen an mehreren Standorten finden Sie unter Übersicht über [Stretched Cluster: Übersicht](../concepts/stretched-clusters.md).

## <a name="before-you-start"></a>Vorbereitung

Vor dem Erstellen eines Clusters gehen Sie wie folgt vor:

* Erwerben Sie über Ihren bevorzugten Microsoft-Hardwarepartner zwei Server aus dem [Azure Stack HCI-Katalog](https://azure.microsoft.com/products/azure-stack/hci/catalog/) mit vorinstalliertem Azure Stack HCI-Betriebssystem. Überprüfen Sie die [Systemanforderungen](../concepts/system-requirements.md), um sicherzustellen, dass die von Ihnen ausgewählte Hardware die Workloads unterstützt, die Sie auf dem Cluster ausführen möchten.
* Erstellen Sie ein Benutzerkonto, das auf jedem Server Mitglied der lokalen Gruppe „Administratoren“ ist.
* [Registrieren Sie sich für ein Azure-Abonnement](https://azure.microsoft.com/), falls Sie noch keins besitzen.
* [Installieren Sie das Windows Admin Center](/windows-server/manage/windows-admin-center/deploy/install) auf einem Verwaltungs-PC, und [registrieren Windows Admin Center bei Azure](../manage/register-windows-admin-center.md). Beachten Sie, dass Ihr Verwaltungscomputer Mitglied der gleichen Active Directory-Domäne, in der Sie den Cluster erstellen, oder einer Domäne mit voller Vertrauensstellung sein muss.
* Notieren Sie sich die Servernamen, Domänennamen, IP-Adressen sowie die VLAN-ID für Ihre Bereitstellung.

## <a name="create-the-cluster"></a>Erstellen Sie den Cluster.

Führen Sie die folgenden Schritte aus, um einen einfachen Cluster mit zwei Knoten und einem einzelnen Standort zu erstellen. Weitere Informationen, auch zum Erstellen eines Stretched Cluster, finden Sie unter [Erstellen eines Azure Stack HCI-Clusters mithilfe von Windows Admin Center](create-cluster.md).

1. Klicken Sie in Windows Admin Center unter **Alle Verbindungen** auf **Hinzufügen**.
1. Wählen Sie im Bereich **Ressourcen hinzufügen** unter **Windows Server-Cluster** **Neuen erstellen** aus.
1. Wählen Sie unter **Clustertyp auswählen** die Option **Azure Stack HCI** aus.
1. Wählen Sie unter **Serverstandorte auswählen** die Option **Alle Server an einem Standort** aus.
1. Klicken Sie auf **Erstellen**. Jetzt wird der Clustererstellungs-Assistent angezeigt. Wenn das **Credential Security Service Provider (CredSSP)** -Popup angezeigt wird, wählen Sie **Ja** aus, um diesen vorübergehend zu aktivieren. 

Der Clustererstellungs-Assistent verfügt über fünf Abschnitte mit jeweils mehreren Schritten.

1. **Erste Schritte.** In diesem Abschnitt überprüfen Sie die Voraussetzungen, fügen Server hinzu, verknüpfen diese mit einer Domäne, installieren erforderliche Features und Updates und starten die Server neu. 
2. **Netzwerk.** In diesem Abschnitt des Assistenten wird überprüft, ob die richtigen Netzwerkadapter aktiviert sind, und alle nicht verwendeten Netzwerkadapter werden deaktiviert. Sie wählen Verwaltungsadapter aus, richten eine Konfiguration für den virtuellen Switch ein und definieren Ihr Netzwerk, indem Sie IP-Adressen angeben. Zur Vereinfachung wählen Sie RDMA für diesen Cluster nicht aus. 
3. **Clustering.** In diesem Abschnitt wird überprüft, ob die Server über eine konsistente Konfiguration verfügen und für Clustering geeignet sind, und der eigentliche Cluster wird erstellt.
4. **Speicher**: Als Nächstes werden Sie die Laufwerke bereinigen, den Speicher überprüfen und direkte Speicherplätze aktivieren.
5. **SDN.** Sie können Abschnitt 5 überspringen, da wir für diesen Cluster kein Software-Defined Networking (SDN) verwenden.

Wenn Sie das CredSSP-Protokoll im Assistenten aktiviert haben, sollten Sie es aus Sicherheitsgründen auf allen Servern deaktivieren.

1. Wählen Sie in Windows Admin Center unter **Alle Verbindungen** den Cluster aus, den Sie soeben erstellt haben.
1. Wählen Sie unter **Tools** die Option **Server** aus.
1. Wählen Sie im rechten Bereich den ersten Server im Cluster aus.
1. Wählen Sie unter **Übersicht** die Option **Disable CredSSP** (CredSSP deaktivieren) aus. Sie können dann sehen, dass das rote Banner **CredSSP ENABLED** (CredSSP AKTIVIERT) oben ausgeblendet wird.
1. Wiederholen Sie die Schritte 3 und 4 für den zweiten Server im Cluster.

## <a name="set-up-a-cluster-witness"></a>Einrichten eines Clusterzeugen

Das Einrichten einer Zeugenressource ist erforderlich, damit beim Ausfall eines der Server im Cluster nicht auch der andere Knoten nicht mehr verfügbar ist. In dieser Schnellstartanleitung verwenden wir eine SMB-Dateifreigabe auf einem anderen Server als Zeugen. Sie können auch einen Azure-Cloudzeugen verwenden, vorausgesetzt, alle Serverknoten im Cluster verfügen über eine zuverlässige Internetverbindung. Weitere Informationen zu Zeugenoptionen finden Sie unter [Einrichten eines Clusterzeugen](witness.md).

1. Wählen Sie in der oberen Dropdownliste in Windows Admin Center die Option **Cluster-Manager** aus.
1. Wählen Sie unter **Clusterverbindungen** den Cluster aus.
1. Wählen Sie unter **Extras** die Option **Einstellungen** aus.
1. Wählen Sie im rechten Bereich **Zeuge** aus.
1. Wählen Sie unter **Zeugentyp**, die Option **Dateifreigabenzeuge** aus.
1. Geben Sie einen Pfad zur Dateifreigabe an, z. B. **\\servername.domain.com\Witness$** , und stellen Sie bei Bedarf Anmeldeinformationen bereit.
1. Klicken Sie auf **Speichern**.

## <a name="register-with-azure"></a>Registrieren bei Azure

Azure Stack HCI erfordert eine Verbindung mit Azure, und Sie benötigen Azure Active Directory-Berechtigungen, um die Registrierung abzuschließen. Wenn Sie diese noch nicht besitzen, bitten Sie Ihren Azure AD-Administrator, Ihnen Berechtigungen zu erteilen oder an Sie zu delegieren. Weitere Informationen finden Sie unter [Herstellen einer Verbindung von Azure Stack HCI mit Azure](register-with-azure.md). Nach der Registrierung wird der Cluster im Hintergrund automatisch verbunden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie einen Stack HCI-Cluster erstellt und bei Azure registriert. Nun können Sie [Volumes erstellen](../manage/create-volumes.md) und dann [virtuelle Computer erstellen](../manage/vm.md).