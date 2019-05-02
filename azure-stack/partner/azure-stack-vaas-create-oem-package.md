---
title: Bewährte Methoden für die Azure Stack-Validierung | Microsoft-Dokumentation
description: In diesem Artikel werden bewährte Methoden für die Verwendung von Validation-as-a-Service beschrieben.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 568817c6e25952f15a396e5748d314187345945b
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "64310386"
---
# <a name="create-an-oem-package"></a>Erstellen eines OEM-Pakets

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Das Azure Stack-OEM-Erweiterungspaket ist der Mechanismus, über den Ihrer Azure Stack-Infrastruktur OEM-spezifische Inhalte hinzugefügt werden, die Sie bei der Bereitstellung sowie für operative Vorgänge wie Update, Erweiterung und Austausch im Betrieb verwenden können.

## <a name="creating-the-package"></a>Erstellen des Pakets

Nach dem Erstellen und Überprüfen kann das OEM-Erweiterungspaket in VaaS verwendet werden.  Stellen Sie vor dem Fortfahren sicher, dass Sie die Schritte zum [Erstellen eines OEM-Pakets](https://microsoft.sharepoint.com/:w:/r/teams/cloudsolutions/Sacramento/_layouts/15/Doc.aspx?sourcedoc=%7BD7406069-7661-419C-B3B1-B6A727AB3972%7D&file=Azure%20Stack%20OEM%20Extension%20Package.docx&action=default&mobileredirect=true) abgeschlossen haben. Das Paket wird dann zusammen mit VaaS Testergebnissen für die Anmeldung beim Workflow „Paketvalidierung“ an Microsoft übermittelt. Die folgenden Schritte beschreiben, wie Sie die generierten Dateien in einer einzelnen ZIP-Datei bündeln können, die von VaaS verwendet werden kann.

1. Identifizieren Sie den folgenden Inhalt für das Paket:
    - Eine ZIP-Datei mit dem Paketinhalt.
    - Eine Manifestdatei namens `oemMetadata.xml`, die inhaltlich mit der Datei „metadata.xml“ im Stammverzeichnis des Paketinhalts identisch sein sollte.

2. Wählen Sie die Inhaltsdateien aus, und erstellen Sie aus dem Inhalt eine ZIP-Datei:

    ![ZIP-Dateiinhalt](media/vaas-create-oem-package-1.png) ![Komprimieren des Elementinhalts](media/vaas-create-oem-package-2.png)

3. Benennen Sie die resultierende Datei um, damit Sie sie anhand des aussagekräftigen Namens identifizieren können.

## <a name="verifying-the-contents"></a>Überprüfen des Inhalts

Um die Struktur der ZIP-Datei zu überprüfen, untersuchen Sie sie, und vergewissern Sie sich, dass keine Unterordner vorhanden sind. Die TLD enthält den gezippten Inhalt. Eine gültige Paketstruktur ist unten dargestellt.
> [!IMPORTANT]
> Wenn Sie den übergeordneten Ordner anstelle des Inhalts zippen, tritt bei der Paketsignierung ein Fehler auf.

![Ordnungsgemäß gezippter Paketinhalt](media/vaas-create-oem-package-3.png)

Die ZIP-Datei kann jetzt zu VaaS hochgeladen und im Rahmen des Workflows „Paketvalidierung“ von Microsoft signiert werden.

## <a name="next-steps"></a>Nächste Schritte

- [Validieren eines OEM-Pakets](azure-stack-vaas-validate-oem-package.md)
