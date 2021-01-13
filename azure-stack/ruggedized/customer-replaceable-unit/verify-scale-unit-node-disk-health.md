---
title: Überprüfen der Datenträgerintegrität des Skalierungseinheitknotens
description: Hier erhalten Sie Informationen zum Überprüfen der Datenträgerintegrität von Skalierungseinheitknoten.
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 35deff6c48c02c1deda13bb3cd8dec87e54f52ef
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97909870"
---
# <a name="verifying-scale-unit-node-disk-health"></a>Überprüfen der Datenträgerintegrität des Skalierungseinheitknotens

1.  Stellen Sie eine Verbindung mit dem privilegierten Endpunkt (PEP) her.

    1.  Anweisungen zum Herstellen einer Verbindung mit dem PEP finden Sie unter „Arbeitsstation mit privilegiertem Zugriff und privilegierter Endpunktzugriff“.

    1.  Nachdem die Verbindung hergestellt ist, geben Sie die PEP-Sitzung `Enter-PSSession -Session $pep` ein.

2.  Rufen Sie den Zustand des virtuellen Datenträgers ab.

    1.  Führen Sie `Get-VirtualDisk -cimsession "S-Cluster"` aus, um die Integrität der virtuellen Festplatte zu überprüfen.

        Wenn das System nicht **OK** für den **OperationalStatus** und **Healthy** (Fehlerfrei) für **HealthStatus** zurückgibt, warten Sie ein paar Minuten, und führen Sie den Befehl noch mal aus.
        
        ![Screenshot, in dem Windows PowerShell mit hervorgehobenen Spalten „OperationsStatus“ und „HealthStatus“ gezeigt wird](media/image-57.png)
        
    1.  Führen Sie `Get-VirtualDisk -cimsession "S-Cluster" | Get-StorageJob` aus, um sicherzustellen, dass alle laufenden Speicheraufträge abgeschlossen sind.
    
    1.  Vergewissern Sie sich, dass keine Ergebnisse zurückgegeben werden. Wenn Aufträge noch ausgeführt werden, wie von **JobState** angezeigt wird, oder alle Aufträge als abgeschlossen gekennzeichnet sind, warten Sie weitere zehn Minuten, und führen Sie denselben Befehl noch mal aus. Der endgültige Status sollte lauten, dass keine Aufträge aufgelistet sind.
    
    1.  Bei Bedarf finden Sie weitere Schritte zur Überprüfung der Speicherintegrität unter [Überprüfen des Status der Reparatur von virtuellen Datenträgern mit Azure Stack Hub PowerShell](https://docs.microsoft.com/azure-stack/operator/azure-stack-replace-disk?view=azs-2002&check-the-status-of-virtual-disk-repair-using-azure-stack-hub-powershell).
        
