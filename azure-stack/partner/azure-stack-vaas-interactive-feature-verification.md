---
title: Testen der interaktiven Featureüberprüfung
titleSuffix: Azure Stack Hub
description: Hier erfahren Sie, wie Sie Tests für die interaktive Featureüberprüfung für Azure Stack Hub mit Validation-as-a-Service erstellen.
author: mattbriggs
ms.topic: tutorial
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ea2193b29dce09db47d87444400f0d6c5b22dbae
ms.sourcegitcommit: 4e1c948ae4a498bd730543b0704bbc2b0d88e1ec
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77625338"
---
# <a name="interactive-feature-verification-testing"></a>Testen der interaktiven Featureüberprüfung  

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Mit dem Testframework für die interaktive Featureüberprüfung können Sie Tests für Ihr System anfordern. Wenn Sie einen Test anfordern, bereitet Microsoft mithilfe des Frameworks Tests vor, die manuelle interaktive Schritte erfordern. Über das Framework kann Microsoft mehrere eigenständige automatisierte Tests verketten.

In diesem Artikel wird ein einfaches manuelles Szenario beschrieben. Der Test überprüft das Ersetzen eines Datenträgers in Azure Stack Hub. Das Framework erfasst bei jedem Schritt Diagnoseprotokolle. Sie können Probleme debuggen, sobald sie gefunden werden. Das Framework ermöglicht auch die Weitergabe von Protokollen, die von anderen Tools oder Prozessen erzeugt wurden, sowie die Abgabe von Feedback für das Szenario.

> [!Important]  
> In diesem Artikel wird auf die Schritte für einen Datenträgeridentifizierungstest verwiesen. Hierbei handelt es sich lediglich um eine Demonstration, und die Ergebnisse des Testdurchlaufworkflows können nicht für die Überprüfung neuer Lösungen verwendet werden.

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

    ![Datenträgeridentifizierungstest: interaktive Tests in Azure Stack Hub](media/azure-stack-vaas-interactive-feature-verification/image4.png)

2. Wählen Sie **Bearbeiten** aus, um den Namen und das Kennwort des Domänenadministratorbenutzers anzugeben.

3. Wählen Sie den passenden Testausführungs-Agent/die DVM aus, auf der der Test gestartet werden soll.

    ![Auswählen eines Testausführungs-Agents: interaktive Tests in Azure Stack Hub](media/azure-stack-vaas-interactive-feature-verification/image5.png)

4. Wählen **Übermitteln** aus, um den Test zu starten.

    ![Überprüfen und Übermitteln eines Tests: interaktive Tests in Azure Stack Hub](media/azure-stack-vaas-interactive-feature-verification/image6.png)

5. Greifen Sie über den Agent, den Sie im vorherigen Schritt ausgewählt haben, auf die Benutzeroberfläche für die interaktive Prüfung zu.

    ![Datenträgeridentifizierungstest: interaktive Tests in Azure Stack Hub](media/azure-stack-vaas-interactive-feature-verification/image8.png)

6. Unter den Links **Dokumentation** und **Überprüfung** finden Sie Anweisungen von Microsoft für dieses Szenario.

    ![Links „Dokumentation“ und „Überprüfung“ im Datenträgeridentifizierungstest](media/azure-stack-vaas-interactive-feature-verification/image9.png)

7. Wählen Sie **Weiter** aus.

    ![Auswählen von „Weiter“: interaktive Tests in Azure Stack Hub](media/azure-stack-vaas-interactive-feature-verification/image10.png)

8. Befolgen Sie die Anweisungen, um das Skript für die Vorüberprüfung auszuführen.

    ![Ausführen des Vorabtestskripts: interaktive Tests in Azure Stack Hub](media/azure-stack-vaas-interactive-feature-verification/image11.png)

9. Führen Sie nach erfolgreicher Ausführung des Vorabtestskripts das manuelle Szenario (Datenträgeraustausch) gemäß der Beschreibung unter den Links **Dokumentation** und **Überprüfung** auf der Registerkarte **Informationen** durch.

    ![Ausführen des manuellen Szenarios: interaktive Tests in Azure Stack Hub](media/azure-stack-vaas-interactive-feature-verification/image12.png)

    > [!Important]  
    > Lassen Sie das Dialogfeld während der Durchführung des manuellen Szenarios geöffnet.

10. Folgen Sie nach Abschluss des manuellen Szenarios den Anweisungen zum Ausführen des Skripts für die Nachüberprüfung.

    ![Ausführen des Skripts für die Nachüberprüfung: interaktive Tests in Azure Stack Hub](media/azure-stack-vaas-interactive-feature-verification/image13.png)

11. Wählen Sie nach erfolgreichem Abschluss des manuellen Szenarios (Datenträgeraustausch) die Option **Übermitteln** aus.

    ![Übermitteln des Datenträgeridentifizierungstests: interaktive Tests in Azure Stack Hub](media/azure-stack-vaas-interactive-feature-verification/image14.png)

    > [!Important]  
    > Wenn Sie das Fenster schließen, wird der Test vorzeitig beendet.

12. Geben Sie Feedback zur Testerfahrung. Diese Fragen helfen Microsoft dabei, die Erfolgsrate und die Veröffentlichungsqualität des Szenarios zu beurteilen.

    ![Bereitstellen von Feedback zu interaktiven Tests in Azure Stack Hub](media/azure-stack-vaas-interactive-feature-verification/image15.png)

13. Fügen Sie alle Protokolldateien an, die Sie an Microsoft übermitteln möchten.

    ![Anfügen von Protokolldateien: interaktive Tests in Azure Stack Hub](media/azure-stack-vaas-interactive-feature-verification/image16.png)

14. Akzeptieren Sie die Lizenzbedingungen für die Feedbackübermittlung.

15. Wählen Sie **Übermitteln** aus, um die Ergebnisse an Microsoft zu senden.

## <a name="next-steps"></a>Nächste Schritte

- [Überwachen und Verwalten von Tests im Azure Stack Hub-Validierungsportal](azure-stack-vaas-monitor-test.md)
