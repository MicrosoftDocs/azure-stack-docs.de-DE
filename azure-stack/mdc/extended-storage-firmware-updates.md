---
title: 'Azure Stack: Aktualisieren der Firmware für den erweiterten Speicher des Modular Data Center'
description: Dieser Artikel enthält Anleitungen zum Durchführen von Firmwareupdates im erweiterten Speicher für den Modular Data Center-Blobspeicher.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2020
ms.author: justinha
ms.reviewer: karlt
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: 23430fd05254b1509677bf2d7ddedab75c7c2355
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2020
ms.locfileid: "92183423"
---
# <a name="updating-onefs-and-firmware-for-the-datacenter-extended-storage"></a>Aktualisieren von OneFS und Firmware für den erweiterten Speicher des Rechenzentrums

Dieser Artikel enthält Anleitungen zum Aktualisieren von OneFS und der Firmware der Hardware für den erweiterten Speicher des Rechenzentrums. 

Die letzten Schritte bei der Integration und Bereitstellung der Hardware für den erweiterten Speicher sollen sicherstellen, dass die Hardware fehlerfrei und bereit zur Konfiguration ist. Die Schritte werden unter [Integritätsüberprüfungen im erweiterten Speicher](extended-storage-health-checks.md) beschrieben.

## <a name="update-onefs"></a>Aktualisieren von OneFS

Aktualisieren Sie OneFS, damit aktuelle Betriebssystempatches angewandt werden, bevor Sie die Firmware aktualisieren. 

1. Vergewissern Sie sich, dass der Cluster über ausreichend freien Speicherplatz verfügt, um diesen Patch zu installieren
1. Öffnen Sie eine SSH-Verbindung auf einem beliebigen Knoten im Cluster, und melden Sie sich mit dem root-Konto an.

   >[!NOTE]
   >Wenn der Patch in einem Cluster im Konformitätsmodus installiert wird, melden Sie sich mit dem Konto „compadmin“ an.

1. Kopieren Sie die Datei „8.2.2_GA-RUP_2020-07_PSP-40.tgz“ in das Verzeichnis „/ifs/data/Isilon_Support“ im Cluster.
1. Führen Sie den folgenden Befehl aus, um zum Verzeichnis „/ifs/data/Isilon_Support“ zu wechseln: 

   ```
   /ifs/data/Isilon_Support
   pwd
   ```

1. Führen Sie den folgenden Befehl aus, um die Patchdatei zu extrahieren:

   ```
   -zxvf 8.2.2_GA-RUP_2020-07_PSP-40.tgz
   ```

1. Führen Sie den folgenden Befehl aus, um den Patch zu installieren:
   >[!WARNING]
   >Mit dem folgenden Befehl werden alle Knoten im Cluster gleichzeitig neu gestartet. Um den Patch zu installieren und dann jeden Knoten nacheinander neu zu starten, lassen Sie den Parameter `-simultaneous` aus. Weitere Informationen finden Sie in der Infodatei und im Abschnitt zu den Auswirkungen von Installation und Entfernung.

   ```
   isi upgrade patches install 8.2.2_GA-RUP_2020-07_PSP-40.pkg --simultaneous
   ```

1. Führen Sie den folgenden Befehl aus, um sich zu vergewissern, dass der Patch installiert wurde:

   ```
   isi upgrade patches list
   ```

1. Vergewissern Sie sich, dass „8.2.2_GA-RUP_2020-07_PSP-40“ in der Liste der installierten Pakete angezeigt wird und der Status „Installiert“ lautet.

## <a name="update-firmware-on-nodes"></a>Aktualisieren der Firmware auf den Knoten

   1. Vergewissern Sie sich, dass die aktuelle Firmware und das eigentliche Laufwerk die [Paketreleases](https://aka.ms/currentextstoragerelease) unterstützen.
   1. Laden Sie die Dateien auf Ihren lokalen Computer herunter.  
   1. Laden Sie die Firmwarepakete für Laufwerke und Knoten in die Dateifreigabe „Isilon“ hoch, und platzieren Sie die Dateien in den folgenden Verzeichnissen:
    
        * Drive_Support_*.tgz: /ifs/data/Isilon_Support/dsp/
        * IsiFw_Package_*.tar: /ifs/data 

## <a name="verify-disk-drive-firmware"></a>Überprüfen der Laufwerkfirmware

Aktualisieren und überprüfen Sie zunächst die Laufwerkunterstützung (Drive_Support). Dabei wird überprüft, ob die Firmware des Laufwerks auf dem Stand des bereitgestellten Pakets ist. Andernfalls können Sie mithilfe des Pakets die erforderlichen Updates ausführen:

   1. Führen Sie in einer aktiven SSH-Sitzung Folgendes aus:

       ```
       cd /ifs/data/Isilon_Support/dsp/
       ```

   1. Vergewissern Sie sich, dass sich die Datei „Drive_Support_*.tgz“ in diesem Verzeichnis befindet, indem Sie den Befehl „ls“ ausführen.
   1. Führen Sie den folgenden Befehl aus, um die TGZ-Datei zu erweitern. Verwenden Sie dabei den vollständigen Dateinamen, und ersetzen Sie das Sternchen (*) durch die tatsächliche Versionsnummer des Pakets:
       ```
       tar -zxvf Drive_Support_v1.*.tgz
       ```
       Wenn die Paketversion z. B. „Drive_Support_v1.33.tgz“ lautet, führen Sie den folgenden Befehl aus:
       ```
       tar -zxvf Drive_Support_v1.33.tgz
       ```
   1. Nach der Erweiterung stehen drei Dateien bereit:
    
        * Drive_Support_v1.33.tar
        * Drive_Support_v1.33.tar.md5
        * Drive_Support_v1.33.tar.sha256
   1. Führen Sie den folgenden Befehl aus, um die Software für die Laufwerksunterstützung zu installieren und zu überprüfen:
       ```
       isi_dsp_install Drive_Support_v1.*.tar
       ```
       Wenn die Paketversion z. B. „Drive_Support_v1.33.tgz“ lautet, führen Sie den folgenden Befehl aus:
       ```
       isi_dsp_install Drive_Support_v1.33.tar
       ```
   1. Dieser Befehl führt Überprüfungen durch und fordert eine Installation des Pakets für die Laufwerksunterstützung an. Ist das Paket erforderlich, wird es installiert. Andernfalls wird angegeben, dass es bereits installiert ist, und alle weiteren Updates für die Software der Laufwerkunterstützung werden übersprungen. Beispielausgabe für Version 1.33:
    
        ```
        Running local CHECKS for patch Drive_Support_v1.33
        INSTALL operation has been requested for patch Drive_Support_v1.33
        Installed Drive_Support_v1.33
        DELETE operation has been requested for patch
        Drive_Support_v1.33
        Removed Drive_Support_v1.33 package files
        DSP Install Succeeded, 170.605088949 sec elapsed
        ```
        
   1. Mit dem folgenden Befehl erhalten Sie eine abschließende Bestätigung. Ein anderer Wert als „-“ in der Spalte „Desired“ (Erforderlich) gibt an, dass das Laufwerk aktualisiert werden muss:
       ```
       isi devices drive firmware list --node-lnn all
       ```
   1. Wenn mehrere Laufwerke aktualisiert werden müssen, sollten Sie über den folgenden Befehl die Updates der Datenträgerfirmware durchführen:
       ```
       isi devices drive firmware update start all --node-lnn all
       ```
   1. Dieser Vorgang kann mit dem folgenden Befehl überwacht werden:
       ```
       isi devices drive firmware update list
       ```
   1. Anschließend können Sie die Integrität der Laufwerke mit dem folgenden Befehl überprüfen:
       ```
       isi devices drive list --node-lnn all
       ```
## <a name="install-node-firmware"></a>Installieren der Knotenfirmware

Nachdem die Updates der Laufwerkunterstützung abgeschlossen und überprüft wurden, kann die Knotenfirmware mithilfe der Datei „IsiFw_Package_*.tar“ installiert werden.

   1. Führen Sie in einer aktiven SSH-Sitzung Folgendes aus:
        ```
        cd /ifs/data/
        ```
   1. Stellen Sie sicher, dass sich die Datei „IsiFw_Package_*.tar“ in diesem Verzeichnis befindet, indem Sie den Befehl „ls“ ausführen.
   1. Führen Sie den folgenden Befehl aus, um die Firmware auf allen Knoten bereitzustellen und zu installieren. Verwenden Sie dabei den vollständigen Dateinamen, und ersetzen Sie das Sternchen (*) durch die tatsächliche Versionsnummer des Pakets:
       ```
       isi upgrade patches install IsiFw_Package_v*.tar --rolling=false
       ```
       Wenn die Versionsnummer z. B. „v10.3.3“ lautet, führen Sie den folgenden Befehl aus:
       ```
       isi upgrade patches install IsiFw_Package_v10.3.3.tar --rolling=false
       ```
   1. Nach der Ausführung wird als Bestätigung die folgende Meldung ausgegeben:
       ```
        Requested install of patch IsiFw_Package_v10.3.3.
       ```
   1. Überprüfen Sie den Status von Stage und Installation mit dem folgenden Befehl:
       ```
       isi upgrade patches list
       ```
   1. Die Spalte „Status“ wird aktualisiert, sobald der Vorgang abgeschlossen ist. Folgender Status wird angezeigt: „Pending“ (Ausstehend), „Unknown“ (Unbekannt) und „Installed“ (Installiert), wie in der folgenden Beispielausgabe gezeigt:
        
       ![Status des Firmwareupdates](media/extended-storage-firmware-updates/firmware-update-status.png)
        
   1. Nachdem die Datei „IsiFw_Package*.tar“ gestaget und installiert wurde, kann die Bereitstellung der aktualisierten Firmware auf allen Knoten gestartet werden. Verwenden Sie dazu den folgenden Befehl:
        ```
        isi upgrade cluster firmware start --no-verify
        ```
   1. Anschließend wird die folgende Bestätigung angezeigt. Geben Sie „yes“ (Ja) ein, um den Vorgang fortzusetzen:
       ```
       You are about to start a Rolling Firmware UPGRADE, are you sure? (yes/[no]):
       ```
        
       > [!NOTE]
       > Das Update der Knotenfirmware kann mehrere Stunden dauern. Die Knoten werden während des Prozesses offline geschaltet. Wenn die Ausführung aller dieser Befehle von Knoten 1 aus ausgeführt wurde, wird die Verbindung getrennt, da dies der erste zu aktualisierende Knoten ist. Dasselbe gilt auch für das OneFS-Portal: Warten Sie 5–10 Minuten, bevor Sie die Verbindung wiederherstellen, um den Status zu überprüfen. (Bei Bedarf können Sie eine Verbindung über eine andere Verwaltungsschnittstelle herstellen, die nicht aktualisiert wird.)
   1. Über eine aktive Verwaltungsschnittstelle/-verbindung können Sie mit dem folgenden Befehl den aktuellen Status dieses Firmwareupdates nachverfolgen:
       ```
       isi upgrade cluster nodes firmware progress list
       ```
       Sie können auch das OneFS-Portal verwenden – unter „Cluster Management“ (Clusterverwaltung) > „Patches and firmware“:
        
       ![Clusterverwaltung](media/extended-storage-firmware-updates/cluster-management.png)
        
       Nach Abschluss des Vorgangs ändert sich der Status von **Unknown** (Unbekannt) in **Installed** (Installiert).

## <a name="remove-firmware-packages"></a>Entfernen von Firmwarepaketen

Entfernen Sie die Firmwarepakete, nachdem sie vollständig auf den Laufwerken und Knoten installiert wurden. Verwenden Sie dazu die folgenden Befehle. Verwenden Sie dabei den vollständigen Dateinamen, und ersetzen Sie das „v*“ durch die tatsächliche Paketversion:
   ```
   isi upgrade patches uninstall IsiFw_Package_ v*.tar --rolling=false
   ```
   Wenn die Paketversion z. B. „IsiFw_Package_v10.3.1.tar“ lautet, führen Sie den folgenden Befehl aus:
   ```
   isi upgrade patches uninstall IsiFw_Package_ v10.3.1.tar --rolling=false
   ```
Führen Sie in einer aktiven SSH-Sitzung Folgendes aus:
   ```
   cd /ifs/data/
   rm -f Drive_Support_v*.tgz
   rm -f Drive_Support_v*.tar
   rm -f Drive_Support_v*.tar.md5
   rm -f Drive_Support_v*.tar.sha256
   ```
   Zum Beispiel:
   ```
   cd /ifs/data/
   rm -f Drive_Support_v10.3.1.tgz
   rm -f Drive_Support_v10.3.1.tar
   rm -f Drive_Support_v10.3.1.tar.md5
   rm -f Drive_Support_v10.3.1.tar.sha256
   ```


        
<!-- ## Contact Dell EMC for support -->

Wenden Sie sich bei Problemen an den Microsoft-Support, um Hilfe zu erhalten.

## <a name="next-steps"></a>Nächste Schritte

- [Integritätsüberprüfungen im erweiterten Speicher](extended-storage-health-checks.md)
