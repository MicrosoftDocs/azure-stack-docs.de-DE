---
title: Testen der interaktiven Featureüberprüfung mit Validation-as-a-Service in Azure Stack | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Tests für die interaktive Featureüberprüfung für Azure Stack mit Validation-as-a-Service erstellen.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 5da64bbd13e2bfde5b469c1ae0de89d85a054e3c
ms.sourcegitcommit: cc3534e09ad916bb693215d21ac13aed1d8a0dde
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73167248"
---
# <a name="interactive-feature-verification-testing"></a>Testen der interaktiven Featureüberprüfung  

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Mit dem Testframework für die interaktive Featureüberprüfung können Sie Tests für Ihr System anfordern. Wenn Sie einen Test anfordern, bereitet Microsoft mithilfe des Frameworks Tests vor, die manuelle interaktive Schritte erfordern. Über das Framework kann Microsoft mehrere eigenständige automatisierte Tests verketten.

In diesem Artikel wird ein einfaches manuelles Szenario beschrieben. Der Test überprüft das Ersetzen eines Datenträgers in Azure Stack. Das Framework erfasst bei jedem Schritt Diagnoseprotokolle. Sie können Probleme debuggen, sobald sie gefunden werden. Das Framework ermöglicht auch die Weitergabe von Protokollen, die von anderen Tools oder Prozessen erzeugt wurden, sowie die Abgabe von Feedback für das Szenario.

> [!Important]  
> In diesem Artikel wird auf die Schritte für die Datenträgeridentifizierung verwiesen. Hierbei handelt es sich lediglich um eine Demonstration, da jegliche Ergebnisse des Testdurchlauf-Workflows nicht für die Überprüfung neuer Lösungen verwendet werden dürfen.

## <a name="overview-of-interactive-testing"></a>Übersicht über interaktives Testen

Ein Test für den Austausch eines Datenträgers ist ein gängiges Szenario. In diesem Beispiel umfasst der Test fünf Schritte:

1. Erstellen eines neuen Workflows vom Typ **Testdurchlauf**
2. Auswählen von **Disk Identification Test** (Datenträgeridentifizierungstest)
3. Ausführen des manuellen Schritts, wenn Sie dazu aufgefordert werden
4. Überprüfen des Ergebnisses des Szenarios
5. Senden des Testergebnisses an Microsoft

## <a name="create-a-new-test-pass"></a>Erstellen eines neuen Testdurchlaufs

Falls Sie über keinen vorhandenen Testdurchlauf verfügen, führen Sie die [Schritte zum Planen eines Tests](azure-stack-vaas-schedule-test-pass.md) aus.

## <a name="schedule-the-test"></a>Planen des Tests

1. Wählen Sie **Disk Identification Test** (Datenträgeridentifizierungstest) aus.

    > [!Note]  
    > Die Version des Tests erhöht sich, wenn Verbesserungen am Testbegleitmaterial vorgenommen werden. Sofern von Microsoft nicht anders angegeben, sollte immer die höchste Version verwendet werden.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image4.png)

1. Wählen Sie **Bearbeiten** aus, um den Namen und das Kennwort des Domänenadministratorbenutzers anzugeben.

1. Wählen Sie den passenden Testausführungs-Agent/die DVM aus, auf der der Test gestartet werden soll.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image5.png)

1. Wählen **Übermitteln** aus, um den Test zu starten.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image6.png)

1. Greifen Sie über den Agent, den Sie im vorherigen Schritt ausgewählt haben, auf die Benutzeroberfläche für die interaktive Prüfung zu.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image8.png)

1. Unter den Links **Dokumentation** und **Überprüfung** finden Sie Anweisungen von Microsoft für dieses Szenario.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image9.png)

1. Klicken Sie auf **Weiter**.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image10.png)

1. Befolgen Sie die Anweisungen, um das Skript für die Vorüberprüfung auszuführen.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image11.png)

1. Führen Sie nach erfolgreicher Ausführung des Skripts für die Vorüberprüfung das manuelle Szenario (Datenträgeraustausch) gemäß den Links **Dokumentation** und **Überprüfung** auf der Registerkarte **Informationen** durch.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image12.png)

    > [!Important]  
    > Lassen Sie das Dialogfeld während der Durchführung des manuellen Szenarios geöffnet.

1. Folgen Sie nach Abschluss des manuellen Szenarios den Anweisungen zum Ausführen des Skripts für die Nachüberprüfung.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image13.png)

1. Wählen Sie nach erfolgreichem Abschluss des manuellen Szenarios (Datenträgeraustausch) die Option **Weiter** aus.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image14.png)

    > [!Important]  
    > Wenn Sie das Fenster schließen, wird der Test vorzeitig beendet.

1. Geben Sie Feedback zur Testerfahrung. Diese Fragen helfen Microsoft dabei, die Erfolgsrate und die Veröffentlichungsqualität des Szenarios zu beurteilen.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image15.png)

1. Fügen Sie alle Protokolldateien an, die Sie an Microsoft übermitteln möchten.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image16.png)

1. Akzeptieren Sie die Lizenzbedingungen für die Feedbackübermittlung.

1. Wählen Sie **Übermitteln** aus, um die Ergebnisse an Microsoft zu senden.

## <a name="next-steps"></a>Nächste Schritte

- [Überwachen und Verwalten von Tests im VaaS-Portal](azure-stack-vaas-monitor-test.md)
