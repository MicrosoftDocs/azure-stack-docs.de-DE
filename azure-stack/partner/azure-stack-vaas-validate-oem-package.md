---
title: Überprüfen von OEM-Paketen mit Validation-as-a-Service in Azure Stack Hub
description: Hier erfahren Sie, wie Sie OEM-Pakete (Original Equipment Manufacturer, Originalgerätehersteller) mit Validation-as-a-Service überprüfen.
author: mattbriggs
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 8bb39f3aae4031402e38023f92ebdb91feb9bf92
ms.sourcegitcommit: a76301a8bb54c7f00b8981ec3b8ff0182dc606d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/11/2020
ms.locfileid: "77143740"
---
# <a name="validate-oem-packages"></a>Überprüfen von OEM-Paketen

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Sie können ein neues OEM-Paket testen, wenn für eine abgeschlossene Lösungsvalidierung eine Änderung an der Firmware oder an Treibern vorgenommen wurde. Wenn Ihr Paket den Test bestanden hat, wird es von Microsoft signiert. Der Test muss das aktualisierte OEM-Erweiterungspaket mit den Treibern und der Firmware enthalten, die die Windows Server-Logo- und PCS-Tests bestanden haben.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

> [!IMPORTANT]
> Informieren Sie sich vor dem Hochladen oder Übermitteln von Paketen unter [Erstellen eines OEM-Pakets](azure-stack-vaas-create-oem-package.md) über das erwartete Paketformat und die erwarteten Inhalte.

## <a name="managing-packages-for-validation"></a>Verwalten von Paketen für die Überprüfung

Wenn Sie ein Paket unter Verwendung des Workflows **Paketvalidierung** überprüfen, müssen Sie eine URL zu einem **Azure Storage-Blob** angeben. Dieses Blob ist das signierte OEM-Testpaket, das im Rahmen des Updateprozesses installiert wird. Erstellen Sie das Blob unter Verwendung des Azure Storage-Kontos, das Sie im Rahmen der Einrichtung erstellt haben. (Weitere Informationen finden Sie unter [Tutorial: Einrichten von Ressourcen für Validation-as-a-Service](azure-stack-vaas-set-up-resources.md).)

### <a name="prerequisite-provision-a-storage-container"></a>Voraussetzung: Bereitstellen eines Speichercontainers

Erstellen Sie in Ihrem Speicherkonto einen Container für Paketblobs. Dieser Container kann für alle Paketvalidierungsdurchläufe verwendet werden.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu dem Speicherkonto, das Sie in [Tutorial: Einrichten von Ressourcen für Validation-as-a-Service](azure-stack-vaas-set-up-resources.md) erstellt haben.

2. Wählen Sie auf dem linken Blatt unter **Blobdienst** die Option **Container** aus.

3. Wählen Sie **+ Container** in der Menüleiste aus.
    1. Geben Sie einen Namen für den Container ein, z.B. `vaaspackages`.
    1. Wählen Sie die gewünschte Zugriffsebene für nicht authentifizierte Clients wie z.B. VaaS aus. Weitere Informationen dazu, wie Sie in den einzelnen Szenarios VaaS Zugriff auf Pakete gewähren, finden Sie unter [Behandeln der Containerzugriffsebene](#handling-container-access-level).

### <a name="upload-package-to-storage-account"></a>Hochladen des Pakets in das Speicherkonto

1. Bereiten Sie das Paket vor, das Sie überprüfen möchten. Dies ist eine `.zip`-Datei, deren Inhalt mit der in [Erstellen eines OEM-Pakets](azure-stack-vaas-create-oem-package.md) beschriebenen Struktur übereinstimmen muss.

    > [!NOTE]
    > Wichtig: Die Inhalte der ZIP-Datei (`.zip`) müssen im Stammverzeichnis der Datei platziert `.zip` werden. Das Paket darf keine Unterordner enthalten.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) den Paketcontainer aus, und laden Sie das Paket hoch, indem Sie auf der Menüleiste auf **Hochladen** klicken.

1. Wählen Sie die hochzuladende Paketdatei (`.zip`) aus. Behalten Sie die Standardwerte für **BLOB-Typ** (d.h. **Blockblob**) und **Blockgröße** bei.

### <a name="generate-package-blob-url-for-vaas"></a>Generieren der Paketblob-URL für VaaS

Wenn Sie im VaaS-Portal einen Workflow vom Typ **Paketvalidierung** erstellen, müssen Sie eine URL für das Azure Storage-Blob angeben, das Ihr Paket enthält. Einige *interaktive* Tests (u. a. **Monatliche Azure Stack Hub-Updateüberprüfung** und **Überprüfung des OEM-Erweiterungspakets**) benötigen auch eine URL zu Paketblobs.

#### <a name="handling-container-access-level"></a>Behandeln der Containerzugriffsebene

Welche Zugriffsebene VaaS mindestens benötigt, hängt davon ab, ob Sie einen Workflow für die Paketvalidierung erstellen oder einen *interaktiven* Test planen.

Im Fall der Zugriffsebenen **Private** und **Blob** müssen Sie vorübergehenden Zugriff auf das Paketblob gewähren, indem Sie VaaS eine [Shared Access Signature](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1?) (SAS) geben. Die **Container**-Zugriffsebene erfordert nicht, dass Sie SAS-URLs generieren, aber ermöglicht nicht authentifizierten Zugriff auf den Container und seine Blobs.

|Zugriffsebene | Workflowanforderung | Testanforderung |
|---|---------|---------|
|Privat | Generieren Sie eine SAS-URL pro Paketblob ([Option 1](#option-1-generate-a-blob-sas-url)). | Generieren Sie eine SAS-URL auf der Kontoebene, und fügen Sie den Paketblobnamen manuell hinzu ([Option 2](#option-2-construct-a-container-sas-url)). |
|Blob | Geben Sie die Blob-URL-Eigenschaft an ([Option 3](#option-3-grant-public-read-access)). | Generieren Sie eine SAS-URL auf der Kontoebene, und fügen Sie den Paketblobnamen manuell hinzu ([Option 2](#option-2-construct-a-container-sas-url)). |
|Container | Geben Sie die Blob-URL-Eigenschaft an ([Option 3](#option-3-grant-public-read-access)). | Geben Sie die Blob-URL-Eigenschaft an ([Option 3](#option-3-grant-public-read-access)).

Die Optionen zum Gewähren des Zugriffs auf Ihre Pakete sind vom minimalen bis zum maximalen Zugriff geordnet.

#### <a name="option-1-generate-a-blob-sas-url"></a>Option 1: Generieren einer Blob-SAS-URL

Verwenden Sie diese Option, wenn für die Zugriffsebene Ihres Speichercontainers **Private** festgelegt ist, wobei der Container keinen öffentlichen Lesezugriff auf den Container oder seine Blobs zulässt.

> [!NOTE]
> Diese Methode funktioniert nicht bei *interaktiven* Tests. Siehe [Option 2: Erstellen einer Container-SAS-URL](#option-2-construct-a-container-sas-url).

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrem Speicherkonto, und navigieren Sie zu der ZIP-Datei, die das Paket enthält.

2. Wählen Sie im Kontextmenü **SAS generieren** aus.

3. Wählen Sie unter **Berechtigungen** die Option **Lesen** aus.

4. Legen Sie die **Startzeit** auf die aktuelle Zeit und die **Endzeit** auf mindestens 48 Stunden nach der **Startzeit** fest. Falls Sie andere Workflows mit dem gleichen Paket erstellen, können Sie die **Endzeit** ggf. für die Dauer des Tests erhöhen.

5. Wählen Sie **Blob-SAS-Token und URL generieren**.

Verwenden Sie die **Blob-SAS-URL**, wenn Sie Paket-Blob-URLs im Portal bereitstellen.

#### <a name="option-2-construct-a-container-sas-url"></a>Option 2: Erstellen einer Container-SAS-URL

Verwenden Sie diese Option, wenn für die Zugriffsebene Ihres Speichercontainers **Private** festgelegt ist und Sie eine Paketblob-URL für einen *interaktiven* Test angeben müssen. Diese URL kann auch auf Workflowebene verwendet werden.

1. [!INCLUDE [azure-stack-vaas-sas-step_navigate](includes/azure-stack-vaas-sas-step_navigate.md)]

1. Aktivieren Sie die Option **Blob** unter **Zulässige Dienste**. Deaktivieren Sie alle verbleibenden Optionen.

1. Aktivieren Sie unter **Zugelassene Ressourcentypen** die Optionen **Container** und **Objekt**.

1. Aktivieren Sie unter **Zugelassene Berechtigungen** die Optionen **Lesen** und **Auflisten**. Deaktivieren Sie alle verbleibenden Optionen.

1. Wählen Sie als **Startzeit** die aktuelle Zeit aus und als **Endzeit** einen Zeitpunkt, der mindestens 14 Stunden nach der **Startzeit** liegt. Falls Sie andere Tests mit dem gleichen Paket ausführen, können Sie die **Endzeit** ggf. für die Dauer des Tests erhöhen. Bei allen Tests, die über VaaS nach der **Endzeit** geplant werden, tritt ein Fehler auf, und es muss eine neue SAS generiert werden.

1. [!INCLUDE [azure-stack-vaas-sas-step_generate](includes/azure-stack-vaas-sas-step_generate.md)]
    Das Format sollte wie folgt aussehen: `https://storageaccountname.blob.core.windows.net/?sv=2016-05-31&ss=b&srt=co&sp=rl&se=2017-05-11T21:41:05Z&st=2017-05-11T13:41:05Z&spr=https`

1. Ändern Sie die generierte SAS-URL, um den Paketcontainer (`{containername}`) und den Namen Ihres Paketblobs (`{mypackage.zip}`) einzuschließen: `https://storageaccountname.blob.core.windows.net/{containername}/{mypackage.zip}?sv=2016-05-31&ss=b&srt=co&sp=rl&se=2017-05-11T21:41:05Z&st=2017-05-11T13:41:05Z&spr=https`

    Verwenden Sie diesen Wert, wenn Sie im Portal Paketblob-URLs bereitstellen.

#### <a name="option-3-grant-public-read-access"></a>Option 3: Gewähren des öffentlichen Lesezugriffs

Verwenden Sie diese Option, wenn es akzeptabel ist, nicht authentifizierten Clients den Zugriff auf einzelne Blobs oder – im Fall *interaktiver* Tests – den Container zu ermöglichen.

> [!CAUTION]
> Diese Option ermöglicht anonymen Lesezugriff auf Ihre Blobs.

1. Legen Sie die Zugriffsebene des Paketcontainers wie im Abschnitt [Erteilen von anonymen Benutzerberechtigungen für Container und Blobs](https://docs.microsoft.com/azure/storage/storage-manage-access-to-resources#grant-anonymous-users-permissions-to-containers-and-blobs) beschrieben auf **Blob** oder **Container** fest.

    > [!NOTE]
    > Wenn Sie eine Paket-URL für einen *interaktiven* Test angeben, müssen Sie **vollständigen öffentlichen Lesezugriff** auf den Container gewähren, um mit dem Testen fortzufahren.

1. Wählen Sie im Paketcontainer das Paketblob aus, um den Eigenschaftenbereich zu öffnen.

1. Kopieren Sie die **URL**. Verwenden Sie diesen Wert, wenn Sie im Portal Paketblob-URLs bereitstellen.

## <a name="create-a-package-validation-workflow"></a>Erstellen eines Paketvalidierungsworkflows

1. Melden Sie sich beim [VaaS-Portal](https://azurestackvalidation.com) an.

2. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. Klicken Sie auf der Kachel **Paketvalidierung** auf **Starten**.

    ![Kachel des Paketvalidierungsworkflows](media/tile_validation-package.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. Geben Sie die Azure Storage-Blob-URL für das signierte OEM-Testpaket ein, für das eine Signatur von Microsoft benötigt wird. Eine entsprechende Anleitung finden Sie unter [Generieren der Paketblob-URL für VaaS](#generate-package-blob-url-for-vaas).

6. Kopieren Sie den Azure Stack Hub-Updatepaketordner in ein lokales Verzeichnis auf der DVM. Geben Sie für den Pfad des AzureStack-Updatepaketordners den Pfad zum **Ordner mit der ZIP-Paketdatei und der Metadatendatei** ein.

7. Kopieren Sie den oben erstellten OEM-Updatepaketordner in ein lokales Verzeichnis auf der DVM. Geben Sie für den Pfad des OEM-Updatepaketordners den Pfad zum **Ordner mit der ZIP-Paketdatei und der Metadatendatei** ein.

    > [!NOTE]
    > Kopieren Sie das Azure Stack Hub-Update und das OEM-Update in **zwei separate** Verzeichnisse.

8. RequireDigitalSignature: Geben Sie **true** an, wenn das Paket von Microsoft signiert sein muss (Ausführen des OEM-Validierungsworkflows). Wenn Sie ein von Microsoft signiertes Paket für das aktuelle AzureStack Hub-Update überprüfen, geben Sie für diesen Wert „false“ an (Ausführen der monatlichen Azure Stack Hub-Updateüberprüfung).

9. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > Umgebungsparameter können nach der Workflowerstellung nicht mehr geändert werden.

10. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]

11. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    Sie werden auf die Seite mit der Testzusammenfassung umgeleitet.

## <a name="required-tests"></a>Erforderliche Tests

Für die OEM-Paketvalidierung müssen die folgenden Tests ausgeführt werden:

- OEM-Validierungsworkflow

## <a name="run-package-validation-tests"></a>Ausführen von Paketvalidierungstests

1. Auf der Seite mit der **Package Validation tests summary** (Zusammenfassung der Paketvalidierungstests) führen Sie eine Teilmenge der aufgelisteten Tests durch, die für Ihr Szenario geeignet sind.

    In den Validierungsworkflows werden beim **Planen** eines Tests die allgemeinen Parameter auf der Workflowebene verwendet, die Sie bei der Workflowerstellung angegeben haben (siehe [Allgemeine Workflowparameter für Validation-as-a-Service in Azure Stack Hub](azure-stack-vaas-parameters.md)). Sollt einer der Testparameterwerte ungültig werden, muss er gemäß den Anweisungen unter [Ändern von Workflowparametern](azure-stack-vaas-monitor-test.md#change-workflow-parameters) erneut angegeben werden.

    > [!NOTE]
    > Wenn Sie einen Validierungstest für eine bereits vorhandene Instanz planen, wird die alte Instanz im Portal durch eine neu erstellte Instanz ersetzt. Die Protokolle für die alte Instanz bleiben erhalten, können aber nicht über das Portal verwendet werden.  
    > Nach erfolgreicher Ausführung eines Tests wird die **Planungsoption** deaktiviert.

2. Wählen Sie den Agent aus, der den Test ausführt. Informationen zum Hinzufügen lokaler Agents für die Ausführung von Tests finden Sie unter [Bereitstellen des lokalen Agents](azure-stack-vaas-local-agent.md).

3. Wählen Sie zum Planen des Testlaufs im Kontextmenü **Planen** aus, um eine Eingabeaufforderung für die Planung der Testinstanz zu öffnen.

4. Überprüfen Sie die Testparameter, und wählen Sie anschließend **Absenden** aus, um den Test zu planen.

5. Überprüfen Sie die Ergebnisse für die **erforderlichen** Tests.

Um eine Anforderung zur Paketsignierung zu übermitteln, senden Sie den Lösungsnamen und Paketvalidierungsnamen, die dieser Ausführung zugeordnet sind, an [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com).

## <a name="next-steps"></a>Nächste Schritte

- [Überwachen und Verwalten von Tests im VaaS-Portal](azure-stack-vaas-monitor-test.md)
