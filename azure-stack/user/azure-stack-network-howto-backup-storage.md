---
title: Sichern Ihrer Speicherkonten in Azure Stack Hub
description: Erfahren Sie, wie Sie Ihre Speicherkonten in Azure Stack Hub sichern.
author: mattbriggs
ms.topic: how-to
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/19/2019
ms.openlocfilehash: a945800e999ce825c11d5300d02baa58bbfba9b8
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77703858"
---
# <a name="back-up-your-storage-accounts-on-azure-stack-hub"></a>Sichern Ihrer Speicherkonten in Azure Stack Hub

Dieser Artikel befasst sich mit dem Schutz und der Wiederherstellung von Speicherkonten in einem Azure Storage-Konto in Azure Stack Hub.

## <a name="elements-of-the-solution"></a>Elemente der Lösung

In diesem Abschnitt werden die allgemeine Struktur und die Hauptkomponenten der Lösung beschrieben.

![Speichersicherung in Azure Stack Hub](./media/azure-stack-network-howto-backup-storage/azure-stack-storage-backup.png)

### <a name="application-layer"></a>Anwendungsschicht

Daten können zwischen Speicherkonten in separaten Azure Stack Hub-Skalierungseinheiten repliziert werden, indem mehrere [Put Blob](https://docs.microsoft.com/rest/api/storageservices/put-blob)- oder [Put Block](https://docs.microsoft.com/rest/api/storageservices/put-block)-Vorgänge ausgegeben werden, um Objekte an mehreren Speicherorten zu schreiben. Alternativ kann die Anwendung den [Copy Blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob)-Vorgang ausgeben, um das Blob in ein Speicherkonto in einer separaten Skalierungseinheit zu kopieren, nachdem der Put-Vorgang für das primäre Konto abgeschlossen wurde.

### <a name="scheduled-copy-task"></a>Geplante Kopieraufgabe

Das Tool AzCopy ist ideal, um Daten aus lokalen Dateisystemen, dem Azure-Cloudspeicher, Azure Stack Hub-Speicher und S3 zu kopieren. Derzeit können mit AzCopy keine Daten zwischen zwei Azure Stack Hub-Speicherkonten kopiert werden. Zum Kopieren von Objekten aus einem Azure Stack Hub-Quellspeicherkonto in ein Azure Stack Hub-Zielspeicherkonto ist ein zwischengeschaltetes lokales Dateisystem erforderlich.

Weitere Informationen finden Sie im Abschnitt zu AzCopy im Artikel [Verwenden der Datenübertragungstools im Azure Stack Hub-Speicher](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer?view=azs-1908#azcopy).

### <a name="azure-stack-hub-source"></a>Azure Stack Hub (Quelle)

Dies ist die Quelle der Speicherkontodaten, die Sie sichern möchten.

Sie benötigen die URL und das SAS-Token des Quellspeicherkontos. Anweisungen zur Verwendung eines Speicherkontos finden Sie unter [Erste Schritte mit den Azure Stack Hub-Speicherentwicklungstools](azure-stack-storage-dev.md).

### <a name="azure-stack-hub-target"></a>Azure Stack Hub (Ziel)

Dies ist das Ziel, in dem die zu sichernden Kontodaten gespeichert werden. Die Azure Stack Hub-Zielinstanz muss sich an einem anderen Speicherort als die Azure Stack Hub-Quellinstanz befinden. Außerdem muss die Quellinstanz eine Verbindung mit der Zielinstanz herstellen können.

Sie benötigen die URL und das SAS-Token des Quellspeicherkontos. Anweisungen zur Verwendung eines Speicherkontos finden Sie unter [Erste Schritte mit den Azure Stack Hub-Speicherentwicklungstools](azure-stack-storage-dev.md).

### <a name="intermediary-local-filesystem"></a>Zwischengeschaltetes Dateisystem

Sie benötigen einen Ort, an dem Sie beim Kopieren von Daten aus der Azure Stack Hub-Quellinstanz und dem nachfolgenden Schreiben in die Azure Stack Hub-Zielinstanz AzCopy ausführen und Daten speichern können. Dabei handelt es sich um einen Zwischenserver in der Azure Stack Hub-Quellinstanz.

Sie können einen Linux- oder Windows-Server als Zwischenserver erstellen. Der Server muss über genügend Speicherplatz verfügen, damit alle Objekte in den Containern des Quellspeicherkontos gespeichert werden können.
- Anweisungen zum Einrichten eines Linux-Servers finden Sie unter [Erstellen eines virtuellen Linux-Servers mit dem Azure Stack Hub-Portal](azure-stack-quick-linux-portal.md).  
- Anweisungen zum Einrichten eines Windows-Servers finden Sie unter [Erstellen eines virtuellen Windows Server-Computers mit dem Azure Stack Hub-Portal](azure-stack-quick-windows-portal.md).  

Nach dem Einrichten des Windows-Servers müssen Sie [PowerShell für Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install?toc=https%3A%2F%2Fdocs.microsoft.com%2FFazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2FFazure-stack%2Fbreadcrumb%2Ftoc.json) und [Azure Stack Hub-Tools](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-download?toc=https%3A%2F%2Fdocs.microsoft.com%2FFazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2FFazure-stack%2Fbreadcrumb%2Ftoc.json) installieren.

## <a name="set-up-backup-for-storage-accounts"></a>Einrichten der Sicherung für Speicherkonten

1. Rufen Sie den Blobendpunkt für das Quell- und das Zielspeicherkonto ab.

    ![Speichersicherung in Azure Stack Hub](./media/azure-stack-network-howto-backup-storage/back-up-step1.png)

2. Erstellen Sie SAS-Token für das Quell- und das Zielspeicherkonto, und zeichnen Sie sie auf.

    ![Speichersicherung in Azure Stack Hub](./media/azure-stack-network-howto-backup-storage/back-up-step2.png)

3. Installieren Sie [AzCopy](https://github.com/Azure/azure-storage-azcopy) auf dem Zwischenserver, und legen Sie die API-Version entsprechend den Azure Stack Hub-Speicherkonten fest.

    - Für einen Windows-Server:

    ```PowerShell  
    set AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09 PowerShell use: $env:AZCOPY_DEFAULT_SERVICE_API_VERSION="2017-11-09"
    ```

    - Für einen Linux-Server (Ubuntu):

    ```bash  
    export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09
    ```

4. Erstellen Sie auf dem Zwischenserver das Skript. Aktualisieren Sie diesen Befehl mit Ihrem **Speicherkonto**, dem **SAS-Schlüssel** und dem **Pfad des lokalen Verzeichnisses**. Sie führen das Skript aus, um Daten inkrementell aus dem **Quellspeicherkonto** zu kopieren.

    ```
    azcopy sync "https:/<storagaccount>/<container>?<SAS Key>" "C:\\myFolder" --recursive=true --delete-destination=true
    ```

5.  Geben Sie das **Speicherkonto**, den **SAS-Schlüssel** und den **Pfad des lokalen Verzeichnisses** ein.  Diese werden zum inkrementellen Kopieren von Daten in das **Zielspeicherkonto** verwendet.
    
    ```
    azcopy sync "C:\\myFolder" "https:// <storagaccount>/<container>?<SAS Key>" --recursive=true --delete-destination=true
    ```

6.  Verwenden Sie Cron oder den Windows-Taskplaner, um das Kopieren von Daten aus dem Azure Stack Hub-Quellspeicherkonto in den lokalen Speicher auf dem Zwischenserver zu planen. Kopieren Sie die Daten dann aus dem lokalen Speicher auf dem Zwischenserver in das Azure Stack Hub-Zielspeicherkonto.

    Die RPO, die Sie mit dieser Lösung erreichen können, wird durch den Wert des „/MO“-Parameters und die Netzwerkbandbreite zwischen dem Quellkonto und dem Zwischenserver sowie zwischen dem Zwischenserver und dem Zielkonto bestimmt.

    - Für einen Linux-Server (Ubuntu):

    ```bash  
    schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\\&lt;script name>.bat
    ```

    | Parameter | Hinweis | 
    | ---- | ---- |
    | /SC | Verwenden eines Zeitplans in Minuten |
    | /MO | Intervall von *XX* Minuten |
    | /TN | Aufgabenname |
    | /TR | Pfad zur Datei `script.bat` |


    - Für einen Windows-Server:

    Informationen zur Verwendung der Windows-Aufgabenplanung finden Sie unter [Aufgabenplanung für Entwickler](https://docs.microsoft.com/windows/win32/taskschd/task-scheduler-start-page).
    

## <a name="use-your-storage-account-in-a-disaster"></a>Verwenden Ihres Speicherkontos in einem Notfall

Jedes Azure Stack Hub-Speicherkonto verfügt über einen eindeutigen DNS-Namen, der vom Namen der Azure Stack Hub-Region abgeleitet wird, z. B. `https://krsource.blob.east.asicdc.com/`. In Anwendungen, die in diesen DNS-Namen schreiben und aus diesem lesen, müssen Änderungen am DNS-Namen des Speicherkontos berücksichtigt werden, wenn das Zielkonto, z. B. `https://krtarget.blob.west.asicdc.com/`, bei einem Notfall verwendet werden muss.

Anwendungsverbindungszeichenfolgen können geändert werden, nachdem ein Notfall deklariert wurde, um die Verlagerung der Objekte zu berücksichtigen. Wenn ein CNAME-Datensatz vor einem Load Balancer-Front-End für das Quell- und das Zielspeicherkonto verwendet wird, kann der Load Balancer mit einem manuellen Failoveralgorithmus konfiguriert werden, sodass der Administrator das Ziel deklarieren kann.

Wenn in der Anwendung SAS anstelle von AAD oder AD FS verwendet wird, funktioniert die oben genannte Methode nicht und die Anwendungsverbindungszeichenfolgen müssen mit der URL des Zielspeicherkontos und dem bzw. den für das Zielspeicherkonto generierten SAS-Schlüsseln aktualisiert werden.

## <a name="next-steps"></a>Nächste Schritte

[Erste Schritte mit den Azure Stack Hub-Speicherentwicklungstools](azure-stack-storage-dev.md)