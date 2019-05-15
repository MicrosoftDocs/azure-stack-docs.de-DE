---
title: Starten und Beenden von Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure Stack starten und herunterfahren.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 43BF9DCF-F1B7-49B5-ADC5-1DA3AF9668CA
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/19/2019
ms.author: mabrigg
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: e4806dffd30786384adf92a50c3ac6879d8d4526
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618545"
---
# <a name="start-and-stop-azure-stack"></a>Starten und Beenden von Azure Stack
Sie sollten die Verfahren in diesem Artikel befolgen, um Azure Stack-Dienste ordnungsgemäß herunterzufahren und neu zu starten. Durch das Herunterfahren wird die gesamte Azure Stack-Umgebung physisch ausgeschaltet. Beim Starten werden alle Infrastrukturrollen eingeschaltet, und die Mandantenressourcen werden wieder in den Betriebszustand versetzt, in dem Sie sich vor dem Herunterfahren befunden haben.

## <a name="stop-azure-stack"></a>Beenden von Azure Stack 

Fahren Sie Azure Stack mithilfe der folgenden Schritte herunter:

1. Bereiten Sie alle Workloads, die auf den Mandantenressourcen in Ihrer Azure Stack-Umgebung ausgeführt werden, auf das bevorstehende Herunterfahren vor. 

2. Öffnen Sie auf einem Computer mit Netzwerkzugriff auf die Azure Stack ERCS-VMs eine PEP-Sitzung (privilegierter Endpunkt). Anweisungen finden Sie unter [Verwenden des privilegierten Endpunkts in Azure Stack](azure-stack-privileged-endpoint.md).

3. Führen Sie in der PEP-Sitzung Folgendes aus:

    ```powershell
      Stop-AzureStack
    ```

4. Warten Sie, bis alle physischen Azure Stack-Knoten ausgeschaltet sind.

> [!Note]  
> Sie können den Status eines physischen Knotens überprüfen, indem Sie die Anweisungen des Originalgeräteherstellers befolgen, der Ihre Azure Stack-Hardware bereitgestellt hat. 

## <a name="start-azure-stack"></a>Starten von Azure Stack 

Starten Sie Azure Stack mithilfe der folgenden Schritte. Führen Sie diese Schritte unabhängig davon aus, wie Azure Stack beendet wurde.

1. Schalten Sie jeden der physischen Knoten in Ihrer Azure Stack-Umgebung ein. Lesen Sie zum Einschalten der physischen Knoten die Anweisungen des Originalgeräteherstellers, der die Hardware für Azure Stack bereitgestellt hat.

2. Warten Sie, bis die Azure Stack-Infrastrukturdienste gestartet wurden. Es kann zwei Stunden dauern, bis der Startvorgang der Azure Stack-Infrastrukturdienste abgeschlossen ist. Sie können den Status von Azure Stack mit dem Cmdlet [**Get-ActionStatus**](#get-the-startup-status-for-azure-stack) überprüfen.

3. Stellen Sie sicher, dass sich alle Ihre Mandantenressourcen wieder in dem Zustand befinden, in dem sie sich vor dem Herunterfahren befunden haben. Workloads, die auf Mandantenressourcen ausgeführt werden, müssen möglicherweise nach dem Start vom Workload-Manager neu konfiguriert werden.

## <a name="get-the-startup-status-for-azure-stack"></a>Abrufen des Startstatus für Azure Stack

Rufen Sie den Startstatus für die Azure Stack-Startroutine mit den folgenden Schritten ab:

1. Öffnen Sie auf einem Computer mit Netzwerkzugriff auf die Azure Stack ERCS-VMs eine PEP-Sitzung (privilegierter Endpunkt).

2. Führen Sie in der PEP-Sitzung Folgendes aus:

    ```powershell
      Get-ActionStatus Start-AzureStack
    ```

## <a name="troubleshoot-startup-and-shutdown-of-azure-stack"></a>Problembehandlung für das Starten und Herunterfahren von Azure Stack

Führen Sie die folgenden Schritte aus, wenn die Infrastruktur- und Mandantendienste zwei Stunden nach dem Einschalten der Azure Stack-Umgebung nicht erfolgreich gestartet werden. 

1. Öffnen Sie auf einem Computer mit Netzwerkzugriff auf die Azure Stack ERCS-VMs eine PEP-Sitzung (privilegierter Endpunkt).

2. Führen Sie folgenden Befehl aus: 

    ```powershell
      Test-AzureStack
      ```

3. Überprüfen Sie die Ausgabe, und lösen Sie alle Probleme in Bezug auf die Integrität. Weitere Informationen finden Sie unter [Ausführen eines Validierungstests für Azure Stack](azure-stack-diagnostic-test.md).

4. Führen Sie folgenden Befehl aus:

    ```powershell
      Start-AzureStack
    ```

5. Wenn die Ausführung von **Start-AzureStack** zu einem Fehler führt, wenden Sie sich an den Microsoft-Kundendienst. 

## <a name="next-steps"></a>Nächste Schritte 

Weitere Informationen zu [Azure Stack-Diagnosetools](azure-stack-diagnostics.md)
