---
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: include
ms.date: 12/9/2020
ms.reviewer: sijuman
ms.lastreviewed: 12/9/2020
ms.openlocfilehash: e456f17c75b129a3f45c64b9b55e75d21ecb2973
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96935146"
---
### <a name="when-installing-az-module-falsely-throws-admin-rights-required-error"></a>Bei der Installation des Az-Moduls wird fälschlicherweise ein Fehler vom Typ „Administratorrechte erforderlich“ ausgelöst

- Geltungsbereich: Dieses Problem betrifft das Release 2002 und höhere Releases
- Ursache: Wenn Sie das Modul über eine Eingabeaufforderung mit erhöhten Rechten installieren, wird ein Fehler ausgelöst. Der Fehler lautet `Administrator rights required`.
- Abhilfe: Schließen Sie die Sitzung, und starten Sie eine neue PowerShell-Sitzung mit erhöhten Rechten. Stellen Sie sicher, dass Az nicht bereits vorhanden ist. Das Kontomodul wurde in die Sitzung geladen.
- Häufigkeit: Allgemein