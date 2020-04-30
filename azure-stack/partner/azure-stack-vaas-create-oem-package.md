---
title: Erstellen eines OEM-Erweiterungspakets
titleSuffix: Azure Stack Hub
description: Hier erfahren Sie, wie Sie in Azure Stack Hub ein OEM-Erweiterungspaket erstellen.
author: mattbriggs
ms.topic: article
ms.date: 04/20/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: c38a2738fcdfcb61c032982ce4ae0d2ea88d6e3f
ms.sourcegitcommit: 32834e69ef7a804c873fd1de4377d4fa3cc60fb6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661467"
---
# <a name="create-an-oem-package"></a>Erstellen eines OEM-Pakets

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Das Azure Stack Hub-OEM-Erweiterungspaket ist der Mechanismus, über den Ihrer Azure Stack Hub-Infrastruktur OEM-spezifische Inhalte hinzugefügt werden. Der Inhalt wird bei der Bereitstellung sowie für operative Vorgänge wie Update, Erweiterung und Austausch im Betrieb verwendet.

## <a name="creating-the-package"></a>Erstellen des Pakets

Nach dem Erstellen und Überprüfen kann das OEM-Erweiterungspaket in VaaS verwendet werden. Stellen Sie vor dem Fortfahren sicher, dass Sie die Schritte zum [Erstellen eines OEM-Pakets](https://microsoft.sharepoint.com/:w:/r/teams/cloudsolutions/Sacramento/_layouts/15/Doc.aspx?sourcedoc=%7BD7406069-7661-419C-B3B1-B6A727AB3972%7D&file=Azure%20Stack%20OEM%20Extension%20Package.docx&action=default&mobileredirect=true) abgeschlossen haben. Das Paket wird dann zusammen mit VaaS-Testergebnissen für die Anmeldung beim Paketvalidierungsworkflow an Microsoft übermittelt. Die folgenden Schritte beschreiben, wie Sie die generierten Dateien in einer einzelnen ZIP-Datei bündeln können, die von VaaS verwendet werden kann.

1. Identifizieren Sie den folgenden Inhalt für das Paket:
    - Eine ZIP-Datei mit dem Paketinhalt
    - Eine Manifestdatei namens `oemMetadata.xml`, die inhaltlich mit der Datei `metadata.xml` im Stammverzeichnis des Paketinhalts identisch sein sollte

2. Wählen Sie die Inhaltsdateien aus, und erstellen Sie eine ZIP-Datei:

    ![Inhalt der ZIP-Datei beim Erstellen eines OEM-Erweiterungspakets](media/vaas-create-oem-package-1.png) ![Komprimieren des Elementinhalts beim Erstellen eines OEM-Erweiterungspakets](media/vaas-create-oem-package-2.png)

3. Benennen Sie die resultierende Datei um, damit Sie sie anhand des aussagekräftigen Namens identifizieren können.

## <a name="verifying-the-contents"></a>Überprüfen des Inhalts

Um die Struktur der ZIP-Datei zu überprüfen, untersuchen Sie sie, und vergewissern Sie sich, dass keine Unterordner vorhanden sind. Die TLD enthält den gezippten Inhalt. Hier sehen Sie eine gültige Paketstruktur:

> [!IMPORTANT]
> Wenn Sie den übergeordneten Ordner anstelle des Inhalts zippen, tritt bei der Paketsignierung ein Fehler auf.

![Ordnungsgemäß gezippter Paketinhalt beim Erstellen eines OEM-Erweiterungspakets](media/vaas-create-oem-package-3.png)

Die ZIP-Datei kann jetzt in VaaS hochgeladen und im Rahmen des Paketvalidierungsworkflow von Microsoft signiert werden.

## <a name="next-steps"></a>Nächste Schritte

- [Validieren eines OEM-Pakets](azure-stack-vaas-validate-oem-package.md)
