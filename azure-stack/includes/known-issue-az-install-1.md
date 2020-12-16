---
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: include
ms.date: 12/9/2020
ms.reviewer: sijuman
ms.lastreviewed: 12/9/2020
ms.openlocfilehash: 003f6801209d5d5ab1c9c4bd1df0fa47578004ee
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96935147"
---
### <a name="error-thrown-when-installing-the-az-modules"></a>Fehler bei der Installation der Az-Module

- Geltungsbereich: Dieses Problem betrifft das Release 2002 und höhere Releases
- Ursache: Bei der Installation des Moduls wird ein Fehler ausgelöst. Die Fehlermeldung beginnt wie folgt: `Register-PacakgeSource : A parameter cannot be found that matches parameter name. 'PackageManagementProvider'.` Oder die Fehlermeldung enthält den folgenden Text: `PackageManagement\Install-Package : Cannot convert value "2.0.1-preview" to type "System.Version". Error: "Input string was not in a correct format."`
- Abhilfe: Führen Sie das folgende Cmdlet in der gleichen Sitzung aus:  
    `Install-Module PowershellGet -MinimumumVersion 2.3.0 -Force`  
Schließen Sie die Sitzung, und starten Sie eine neue PowerShell-Sitzung mit erhöhten Rechten.
- Häufigkeit: Allgemein