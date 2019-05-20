---
title: Erstellen einer bereitgestellten Lösung für die Datenanalyse mit Azure und Azure Stack | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie eine bereitgestellte Lösung für die Datenanalyse mit Azure und Azure Stack erstellen.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 12/01/2018
ms.openlocfilehash: 2c2b06db3bb6144620b5fce1a7e3e76fdd06589a
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "63448558"
---
# <a name="tutorial-create-a-staged-data-analytics-solution-with-azure-and-azure-stack"></a>Tutorial: Erstellen einer gestaffelten Lösung für die Datenanalyse mit Azure und Azure Stack 

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Es wird beschrieben, wie Sie sowohl lokale als auch öffentliche Cloudumgebungen verwenden, um die Anforderungen von Unternehmen mit mehreren Einrichtungen bzw. Gebäuden zu erfüllen. Azure Stack stellt eine schnelle, sichere und flexible Lösung zum Sammeln, Verarbeiten, Speichern und Verteilen von lokalen Daten und Remotedaten dar. Dies gilt besonders, wenn sich die Anforderungen in Bezug auf Sicherheit, Vertraulichkeit, Unternehmensrichtlinien und Bestimmungen zwischen Standorten und Benutzern ggf. unterscheiden.

Hierbei sammeln Ihre Kunden Daten, die gleich nach der Erfassung analysiert werden müssen, damit schnelle Entscheidungen getroffen werden können. Häufig wird diese Datensammlung ohne Internetzugriff durchgeführt. Nachdem die Konnektivität hergestellt wurde, müssen Sie ggf. eine ressourcenintensive Analyse der Daten durchführen, um weitere Erkenntnisse zu gewinnen. Sie können Daten auch dann analysieren, wenn die Nutzung einer öffentlichen Cloud zu lange dauert oder diese nicht verfügbar ist.

In diesem Tutorial erstellen Sie eine Beispielumgebung, die Folgendes ermöglicht:

> [!div class="checklist"]
> - Erstellen des Rohdaten-Speicherblobs
> - Erstellen einer neuen Azure Stack-Funktion, um bereinigte Daten aus Azure Stack nach Azure zu verschieben
> - Erstellen einer Funktion, die durch Blob Storage ausgelöst wird
> - Erstellen eines Azure Stack-Speicherkontos mit einem Blob und einer Warteschlange
> - Erstellen einer Funktion mit Auslösung per Warteschlange
> - Testen der Funktion mit Auslösung per Warteschlange

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack ist eine Erweiterung von Azure. Mit Azure Stack holen Sie sich die Agilität und Innovation von Cloud Computing in Ihre lokale Umgebung. Sie erhalten die einzige Hybrid Cloud, mit der Sie Hybrid-Apps überall entwickeln und bereitstellen können.  
> 
> Im Whitepaper [Design Considerations for Hybrid Applications](https://aka.ms/hybrid-cloud-applications-pillars) (Überlegungen zum Entwurf für Hybridanwendungen) werden die wichtigen Aspekte in Bezug auf die Softwarequalität (Platzierung, Skalierbarkeit, Verfügbarkeit, Resilienz, Verwaltbarkeit und Sicherheit) beschrieben, die für das Entwerfen, Bereitstellen und Betreiben von Hybridanwendungen erforderlich sind. Die Überlegungen zum Entwurf dienen als Hilfe beim Optimieren des Designs von Hybridanwendungen, um für Produktionsumgebungen das Auftreten von Problemen zu minimieren.

## <a name="prerequisites"></a>Voraussetzungen

Es sind einige Vorbereitungsschritte erforderlich, um diese Lösung zu erstellen:

-   Installierte und funktionierende Azure Stack-Instanz (weitere Informationen finden Sie hier: [Übersicht über Azure Stack](azure-stack-storage-overview.md)

-   Ein Azure-Abonnement. (Erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).)

-   Laden Sie den [Microsoft Azure Storage-Explorer](https://storageexplorer.com/) herunter, und installieren Sie ihn.

-   Sie müssen Ihre eigenen Daten angeben, die von den Funktionen verarbeitet werden. Daten müssen generiert werden und für den Upload in den Azure Stack-Speicherblobcontainer verfügbar sein.

## <a name="issues-and-considerations"></a>Probleme und Überlegungen

### <a name="scalability-considerations"></a>Überlegungen zur Skalierbarkeit

Azure-Funktionen und -Speicherlösungen können skaliert werden, um Anforderungen in Bezug auf die Datenmenge und -verarbeitung zu erfüllen. Informationen und Ziele für die Azure-Skalierbarkeit finden Sie in der [Dokumentation zur Azure-Skalierbarkeit](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).

### <a name="availability-considerations"></a>Überlegungen zur Verfügbarkeit

Der Speicher ist für dieses Muster der wichtigste Verfügbarkeitsaspekt. Eine Verbindung über schnelle Links ist für die Verarbeitung und Verteilung von großen Datenmengen erforderlich.

### <a name="manageability-considerations"></a>Überlegungen zur Verwaltbarkeit

Überlegen Sie sich, wie Sie Ihre Lösung mit Ihren Entwicklungstools und der Quellcodeverwaltung verwalten können.

## <a name="create-the-raw-data-storage-blob"></a>Erstellen des Rohdaten-Speicherblobs

Das Speicherkonto und der Blobcontainer enthalten alle Originaldaten, die von lokalen Aktivitäten generiert werden, z.B. Computer- und Mitarbeiteraktivitäten, Daten von Einrichtungen, Produktionsmetriken und andere Berichte.

1.  Melden Sie sich am [*Azure Stack-Portal*](https://portal.local.azurestack.external/) an.

2.  Erweitern Sie im Azure Stack-Portal das Menü auf der linken Seite, um das Menü mit den Diensten zu öffnen, und klicken Sie auf **Alle Dienste**. Scrollen Sie nach unten zu **Speicher**, und wählen Sie **Speicherkonten**. Wählen Sie im Fenster Speicherkonten die Option **Hinzufügen**.

3.  Verwenden Sie die folgenden Informationen für das Konto:

    a.  Name: **Beliebig**

    b.  Bereitstellungsmodell: **Ressourcen-Manager**

    c.  Kontoart: **Speicher (general-purpose V1)**

    d.  Standort: **USA, Westen**

    e.  Replikation: **Lokal redundanter Speicher (LRS)**

    f.  Leistung: **Standard**

    g.  Sichere Übertragung erforderlich: **Disabled**

    h.  Abonnement: Eines auswählen

    i.  Ressourcengruppe: Geben Sie eine neue Ressourcengruppe an, oder wählen Sie eine vorhandene Ressourcengruppe aus.

    j.  Konfigurieren virtueller Netzwerke: **Disabled**

4.  Wählen Sie **Erstellen**, um das Speicherkonto zu erstellen.

    ![Alt text](media/azure-stack-solution-staged-data-analytics/image1.png)

5.  Wählen Sie nach der Erstellung den Namen des Speicherkontos aus.

6.  Wählen Sie auf dem Kontoblatt unter der Überschrift **BLOB-DIENST** die Option **Container**.

7.  Wählen Sie oben auf dem Blatt **+ Container** und dann **Container** aus.

    ![Alt text](media/azure-stack-solution-staged-data-analytics/image2.png)

8.  Name: **Beliebig**

9.  Zugriffsebene „Öffentlich“: **Container** (anonymer Lesezugriff für Container und Blobs)

10.  Klicken Sie auf **OK**.

## <a name="create-an-azure-stack-function"></a>Erstellen einer Azure Stack-Funktion

Erstellen Sie eine neue Azure Stack-Funktion, um bereinigte Daten aus Azure Stack nach Azure zu verschieben.

### <a name="create-the-azure-stack-function-app"></a>Erstellen der Azure Stack-Funktions-App

1. Melden Sie sich am [Azure Stack-Portal](https://portal.local.azurestack.external) an.
2. Wählen Sie **Alle Dienste** aus.
3. Wählen Sie in der Gruppe **Web und mobil** die Option **Funktionen-Apps**.

4.  Erstellen Sie die Funktions-App, indem Sie die in der Tabelle unter der Abbildung angegebenen Einstellungen verwenden.

    | Einstellung | Empfohlener Wert | BESCHREIBUNG |
    | ---- | ---- | ---- |
    | App-Name | Global eindeutiger Name | Der Name, der Ihre neue Funktionen-App bezeichnet Gültige Zeichen sind `a`-`z`, `0``-9` und `-`. |
    | Abonnement | Ihr Abonnement | Das Abonnement, unter dem diese neue Funktions-App erstellt wird. |
    | **Ressourcengruppe** |  |  |
    | myResourceGroup | Der Name der neuen Ressourcengruppe, in der die Funktionen-App erstellt wird |  |
    | Betriebssystem | Windows | Das serverlose Hosting ist derzeit nur bei der Ausführung unter Windows verfügbar. |
    | **Hostingplan** |  |  |
    | Verbrauchsplan | Der Hostingplan, der definiert, wie Ihre Ressourcen der Funktionen-App zugewiesen werden Im Standard-Verbrauchstarif werden Ressourcen je nach Bedarf der Funktionen dynamisch hinzugefügt. Beim serverlosen Hosting bezahlen Sie nur die Zeit, in der Ihre Funktionen ausgeführt werden. |  |
    | Location | Nächstgelegene Region | Wählen Sie eine Region in Ihrer Nähe oder in der Nähe von anderen Diensten aus, auf die Ihre Funktionen zugreifen. |
    | **Speicherkonto** |  |  |
    | \<oben erstelltes Speicherkonto> | Der Name des neuen Speicherkontos, das von Ihrer Funktionen-App verwendet wird. Speicherkontonamen müssen eine Länge von 3 bis 24 Zeichen haben. Für den Namen dürfen nur Zahlen und Kleinbuchstaben verwendet werden. Sie können außerdem ein vorhandenes Konto verwenden. |  |

    **Beispiel:**

    ![Definieren neuer Funktions-App-Einstellungen](media/azure-stack-solution-staged-data-analytics/image6.png)

5.  Klicken Sie auf **Erstellen**, um die Funktionen-App bereitzustellen.

6.  Wählen Sie oben rechts im Portal das Benachrichtigungssymbol aus, und achten Sie auf die Meldung **Bereitstellung erfolgreich**.

    ![Definieren neuer Funktions-App-Einstellungen](media/azure-stack-solution-staged-data-analytics/image7.png)

7.  Wählen Sie **Zu Ressource wechseln**, um die neue Funktions-App anzuzeigen.

![Die Funktionen-App wurde erfolgreich erstellt.](media/azure-stack-solution-staged-data-analytics/image8.png)

### <a name="add-a-function-to-the-azure-stack-function-app"></a>Hinzufügen einer Funktion zur Azure Stack-Funktions-App

1.  Erstellen Sie eine neue Funktion, indem Sie auf **Funktionen** und dann auf die Schaltfläche **+ Neue Funktion** klicken.

    ![Alt text](media/azure-stack-solution-staged-data-analytics/image3.png)

2.  Wählen Sie **Zeitgebertrigger**.

    ![Alt text](media/azure-stack-solution-staged-data-analytics/image4.png)

3.  Wählen Sie **C\#** als Sprache und den Namen der Funktion aus: `upload-to-azure`  Legen Sie den Zeitplan auf `0 0 * * * *` fest, das ist in der CRON-Schreibweise einmal pro Stunde.

    ![Alt text](media/azure-stack-solution-staged-data-analytics/image5.png)

## <a name="create-a-blob-storage-triggered-function"></a>Erstellen einer Funktion, die durch Blob Storage ausgelöst wird

1. Erweitern Sie die Funktions-App, und wählen Sie die Schaltfläche **+** neben **Functions**.

2. Geben Sie im Suchfeld `blob` ein, und wählen Sie dann die gewünschte Sprache für die Vorlage **Blobtrigger** aus.

   ![Wählen Sie die Blob Storage-Triggervorlage aus.](media/azure-stack-solution-staged-data-analytics/image10.png)

3. Verwenden Sie die Einstellungen wie in der Tabelle unten angegeben:

   | Einstellung | Empfohlener Wert | BESCHREIBUNG |
   | ------- | ------- | ------- |
   | NAME | Eindeutig in Ihrer Funktionen-App | Der Name dieser durch Blobs ausgelösten Funktion. |
   | `Path` | \<Pfad aus dem obigen Speicherort> | Der Speicherort in Blob Storage, der überwacht wird. Der Dateiname des Blobs wird in der Bindung als name-Parameter übergeben. |
   | Speicherkontoverbindung | Funktions-App-Verbindung | Sie können die Speicherkontoverbindung verwenden, die bereits von Ihrer Funktions-App verwendet wird, oder eine neue erstellen. |

   **Beispiel:**

   ![Erstellen Sie die Funktion, die durch Blob Storage ausgelöst wird.](media/azure-stack-solution-staged-data-analytics/image11.png)

4. Wählen Sie **Erstellen** aus, um die Funktion zu erstellen.

### <a name="test-the-function"></a>Testen der Funktion

1.  Navigieren Sie im Azure-Portal zur Funktion. Erweitern Sie unten auf der Seite die Option **Protokolle**, und stellen Sie sicher, dass das Protokollstreaming nicht angehalten ist.

2.  Öffnen Sie Storage-Explorer, und stellen Sie eine Verbindung mit dem Speicherkonto her, das Sie am Anfang dieses Abschnitts erstellt haben.

3.  Erweitern Sie das Speicherkonto, die Option **Blobcontainer** und dann das zuvor erstellte Blob. Wählen Sie **Hochladen** und dann **Dateien hochladen**.

    ![Laden Sie eine Datei in den Blob-Container hoch.](media/azure-stack-solution-staged-data-analytics/image12.png)

4.  Wählen Sie im Dialogfeld „Dateien hochladen“ das Feld „Dateien“. Navigieren Sie zu einer Datei auf einem lokalen Computer, z.B. zu einer Bilddatei, und wählen Sie sie aus. Wählen Sie **Öffnen** und dann **Hochladen**.

5.  Navigieren Sie zurück zu den Funktionsprotokollen, und überprüfen Sie, ob das Blob gelesen wurde.

    **Beispiel:**

    ![Zeigen Sie die Meldung in den Protokollen an.](media/azure-stack-solution-staged-data-analytics/image13.png)

## <a name="create-an-azure-stack-storage-account"></a>Erstellen eines Azure Stack-Speicherkontos

Erstellen eines Azure Stack-Speicherkontos mit einem Blob und einer Warteschlange

### <a name="storage-blob--data-archiving"></a>Archivieren von Speicherblobdaten

Dieses Speicherkonto soll zwei Container enthalten. Diese Container umfassen ein Blob für Archivdaten und eine Warteschlange für die Verarbeitung von Daten, die für die Verteilung in der Hauptniederlassung zugewiesen werden.

Nutzen Sie die oben beschriebenen Schritte und Einstellungen, um ein anderes Speicherkonto und einen Blobcontainer als Archivspeicher zu erstellen.

### <a name="storage-queue--filtered-data-holding"></a>Speicherwarteschlange für gefilterte Daten

1.  Nutzen Sie die oben beschriebenen Schritte und Einstellungen, um auf das neue Speicherkonto zuzugreifen.

2.  Wählen Sie im Abschnitt mit der Speicherkontoübersicht die Option **Warteschlange**.

3.  Wählen Sie **+ Warteschlange**, und fügen Sie im Feld **Name** einen Namen für die neue Warteschlange ein.

4.  Wählen Sie **OK**.

    ![Alt text](media/azure-stack-solution-staged-data-analytics/image14.png)

    ![Alt text](media/azure-stack-solution-staged-data-analytics/image15.png)

## <a name="create-a-queue-triggered-function"></a>Erstellen einer Funktion mit Auslösung per Warteschlange

1.  Verwenden Sie die Schritte aus dem obigen Abschnitt zur Funktionserstellung, um eine zusätzliche Azure Stack-Funktion mit einem Warteschlangentrigger zu erstellen, anstatt eines Blobtriggers.

2.  Verwenden Sie die Einstellungen wie in der Tabelle unten angegeben:

    | Einstellung | Empfohlener Wert | BESCHREIBUNG |
    | ------- | ------- | ------- |
    | NAME | Eindeutig in Ihrer Funktionen-App | Der Name dieser Funktion mit Auslösung durch Warteschlange. |
    | `Path` | \<Pfad aus dem obigen Speicherort> | Der Speicherort im Speicher, der überwacht wird. Der Dateiname der Warteschlange wird in der Bindung als name-Parameter übergeben. |
    | Speicherkontoverbindung | Funktions-App-Verbindung | Sie können die Speicherkontoverbindung verwenden, die bereits von Ihrer Funktions-App verwendet wird, oder eine neue erstellen. |

3.  Wählen Sie **Erstellen** aus, um die Funktion zu erstellen.

## <a name="test-the-queue-triggered-function"></a>Testen der Funktion mit Auslösung per Warteschlange

1. Navigieren Sie im Azure Stack-Portal zur Funktion. Erweitern Sie unten auf der Seite die Option **Protokolle**, und stellen Sie sicher, dass das Protokollstreaming nicht angehalten ist.

2. Öffnen Sie Storage-Explorer, und stellen Sie eine Verbindung mit dem Speicherkonto her, das Sie am Anfang dieses Abschnitts erstellt haben.

3. Erweitern Sie das Speicherkonto, die Option **Blobcontainer** und dann das zuvor erstellte Blob. Wählen Sie **Hochladen** und dann **Dateien hochladen**.

   ![Laden Sie eine Datei in den Blob-Container hoch.](media/azure-stack-solution-staged-data-analytics/image12.png)

4. Wählen Sie im Dialogfeld „Dateien hochladen“ das Feld „Dateien“. Navigieren Sie zu einer Datei auf einem lokalen Computer, z.B. zu einer Bilddatei, und wählen Sie sie aus. Wählen Sie **Öffnen** und dann **Hochladen**.

5. Navigieren Sie zurück zu den Funktionsprotokollen, und überprüfen Sie, ob das Blob gelesen wurde.

   **Beispiel:**

   ![Zeigen Sie die Meldung in den Protokollen an.](media/azure-stack-solution-staged-data-analytics/image13.png)

## <a name="securely-stored-and-accessed-compliant-data"></a>Sichere Speicherung und sicherer Zugriff auf konforme Daten

Die Daten von globalen Unternehmen werden mit der von Azure und Azure Stack bereitgestellten Datenanalyse und benutzerdefinierten Endpunktdirektiven sicher gespeichert, verarbeitet und verteilt. Außerdem ist der sichere Zugriff darauf möglich. Die Aktivitäten von Mitarbeitern und Computern bzw. Maschinen, Einrichtungsdaten und Geschäftsmetriken werden kontinuierlich kompiliert, gespeichert, auf Konformität getestet und verteilt – gemäß der Unternehmensrichtlinie und den regionalen Bestimmungen.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Azure-Cloudmustern finden Sie unter [Cloudentwurfsmuster](https://docs.microsoft.com/azure/architecture/patterns).
