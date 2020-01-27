---
title: Starten und Beenden
titleSuffix: Azure Stack Hub
description: Hier erfahren Sie, wie Sie Azure Stack Hub starten und beenden.
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
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: b78b87065977b3517e91fd27ba5208333de2a77a
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75814268"
---
# <a name="start-and-stop-azure-stack-hub"></a>Starten und Beenden von Azure Stack Hub

Gehen Sie wie in diesem Artikel beschrieben vor, um Azure Stack Hub-Dienste ordnungsgemäß herunterzufahren und neu zu starten. Beim *Beenden* wird die gesamte Azure Stack Hub-Umgebung physisch heruntergefahren und ausgeschaltet. Beim *Starten* werden alle Infrastrukturrollen aktiviert, und die Mandantenressourcen werden wieder in den Betriebszustand versetzt, in dem Sie sich vor dem Herunterfahren befunden haben.

## <a name="stop-azure-stack-hub"></a>Beenden von Azure Stack Hub

Gehen Sie zum Beenden oder Herunterfahren von Azure Stack Hub wie folgt vor:

1. Bereiten Sie alle Workloads, die auf den Mandantenressourcen in Ihrer Azure Stack Hub-Umgebung ausgeführt werden, auf das bevorstehende Herunterfahren vor.

2. Öffnen Sie auf einem Computer, der über Netzwerkzugriff auf die Azure Stack Hub-ERCS-VMs verfügt, eine PEP-Sitzung (privilegierter Endpunkt). Anweisungen finden Sie unter [Verwenden des privilegierten Endpunkts in Azure Stack Hub](azure-stack-privileged-endpoint.md).

3. Führen Sie in der PEP-Sitzung Folgendes aus:

    ```powershell
      Stop-AzureStack
    ```

4. Warten Sie, bis alle physischen Azure Stack Hub-Knoten ausgeschaltet sind.

> [!Note]
> Sie können den Status eines physischen Knotens überprüfen, indem Sie die Anweisungen des Originalgeräteherstellers (Original Equipment Manufacturer, OEM) befolgen, der Ihre Azure Stack Hub-Hardware bereitgestellt hat.

## <a name="start-azure-stack-hub"></a>Starten von Azure Stack Hub

Starten Sie Azure Stack Hub mithilfe der folgenden Schritte. Führen Sie diese Schritte unabhängig davon aus, wie Azure Stack Hub beendet wurde.

1. Schalten Sie jeden der physischen Knoten in Ihrer Azure Stack Hub-Umgebung ein. Halten Sie sich zum Einschalten der physischen Knoten an die Anweisungen des OEM, der die Hardware für Azure Stack Hub bereitgestellt hat.

2. Warten Sie, bis die Azure Stack Hub-Infrastrukturdienste gestartet wurden. Es kann zwei Stunden dauern, bis der Startvorgang der Azure Stack Hub-Infrastrukturdienste abgeschlossen ist. Sie können den Status von Azure Stack Hub mit dem Cmdlet [**Get-ActionStatus**](#get-the-startup-status-for-azure-stack-hub) überprüfen.

3. Vergewissern Sie sich, dass sich alle Ihre Mandantenressourcen wieder in dem Zustand befinden, in dem sie sich vor dem Herunterfahren befunden haben. Workloads, die auf Mandantenressourcen ausgeführt werden, müssen möglicherweise nach dem Start vom Workload-Manager neu konfiguriert werden.

## <a name="get-the-startup-status-for-azure-stack-hub"></a>Abrufen des Startstatus für Azure Stack Hub

Rufen Sie den Startstatus für die Azure Stack Hub-Startroutine mit den folgenden Schritten ab:

1. Öffnen Sie auf einem Computer, der über Netzwerkzugriff auf die Azure Stack Hub-ERCS-VMs verfügt, eine PEP-Sitzung.

2. Führen Sie in der PEP-Sitzung Folgendes aus:

    ```powershell
      Get-ActionStatus Start-AzureStack
    ```

## <a name="troubleshoot-startup-and-shutdown-of-azure-stack-hub"></a>Problembehandlung für das Starten und Herunterfahren von Azure Stack Hub

Führen Sie die folgenden Schritte aus, wenn die Infrastruktur- und Mandantendienste zwei Stunden nach dem Einschalten der Azure Stack Hub-Umgebung nicht erfolgreich gestartet werden.

1. Öffnen Sie auf einem Computer, der über Netzwerkzugriff auf die Azure Stack Hub-ERCS-VMs verfügt, eine PEP-Sitzung.

2. Führen Sie folgenden Befehl aus:

    ```powershell
      Test-AzureStack
      ```

3. Überprüfen Sie die Ausgabe, und lösen Sie alle Probleme in Bezug auf die Integrität. Weitere Informationen finden Sie unter [Ausführen eines Validierungstests für Azure Stack Hub](azure-stack-diagnostic-test.md).

4. Führen Sie folgenden Befehl aus:

    ```powershell
      Start-AzureStack
    ```

5. Wenn die Ausführung von **Start-AzureStack** zu einem Fehler führt, wenden Sie sich an den Microsoft-Support.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu [Azure Stack Hub-Diagnosetools](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs)
