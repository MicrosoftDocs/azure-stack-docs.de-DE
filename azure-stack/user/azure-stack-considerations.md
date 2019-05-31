---
title: Unterschiede zwischen Azure Stack und Azure beim Verwenden von Diensten und Erstellen von Apps | Microsoft-Dokumentation
description: Enthält grundlegende Informationen zu den Unterschieden zwischen Azure und Azure Stack beim Verwenden von Diensten und Erstellen von Apps.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: c81f551d-c13e-47d9-a5c2-eb1ea4806228
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/08/2019
ms.author: sethm
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: 9fcf27c8ebbde86e775b54eda593b25fcd03979c
ms.sourcegitcommit: be5382f715a9c1c18c660b630d8fcd823f13aae3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/24/2019
ms.locfileid: "66197286"
---
# <a name="differences-between-azure-stack-and-azure-when-using-services-and-building-apps"></a>Unterschiede zwischen Azure Stack und Azure beim Verwenden von Diensten und Erstellen von Apps

Machen Sie sich vor der Verwendung von Diensten oder der Erstellung von Apps für Azure Stack mit den Unterschieden zwischen Azure Stack und Azure vertraut. In diesem Artikel wird auf verschiedene Features und wichtige Aspekte der Verwendung von Azure Stack als Hybrid Cloud-Entwicklungsumgebung eingegangen.

## <a name="overview"></a>Übersicht

Azure Stack ist eine Hybrid Cloud-Plattform, mit der Sie Azure-Dienste aus dem Datencenter Ihres Unternehmens oder Ihres Service Providers verwenden können. Sie können eine Anwendung in Azure Stack erstellen und anschließend in Azure Stack, in Azure oder in Ihrer Azure Hybrid Cloud bereitstellen.

Ihr Azure Stack-Betreiber teilt Ihnen mit, welche Dienste für Sie verfügbar sind und wie Sie Support erhalten. Diese Dienste sind jeweils über die angepassten Pläne und Angebote erhältlich.

Beim technischen Inhalt von Azure wird davon ausgegangen, dass Apps nicht für Azure Stack, sondern für einen Azure-Dienst entwickelt werden. Für die Erstellung und Bereitstellung von Apps für Azure Stack müssen Sie einige wichtige Unterschiede kennen, z.B.:

* Azure Stack stellt eine Teilmenge dieser Dienste und Features bereit, die in Azure verfügbar sind.
* Ihr Unternehmen oder Service Provider kann auswählen, welche Dienste angeboten werden sollen. Die verfügbaren Optionen schließen unter Umständen benutzerdefinierte Dienste oder Anwendungen mit ein. Sie enthalten unter Umständen eine eigene angepasste Dokumentation.
* Sie müssen die richtigen Azure Stack-spezifischen Endpunkte verwenden (z.B. die URLs für die Portaladresse und den Azure Resource Manager-Endpunkt).
* Sie müssen PowerShell- und API-Versionen verwenden, die von Azure Stack unterstützt werden. Mit der Verwendung von unterstützten Versionen wird sichergestellt, dass Ihre Apps sowohl in Azure Stack als auch in Azure funktionieren.

## <a name="cheat-sheet-high-level-differences"></a>Cheat Sheet: Allgemeine Unterschiede

In der folgenden Tabelle sind die allgemeinen Unterschiede zwischen Azure Stack und Azure beschrieben. Diese Unterschiede müssen bei der Entwicklung für Azure Stack sowie bei der Verwendung von Azure Stack-Diensten berücksichtigt werden.

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

| Bereich | Azure (global) | Azure Stack |
| -------- | ------------- | ----------|
| Wer ist der Betreiber? | Microsoft | Ihre Organisation bzw. Ihr Dienstanbieter.|
| An wen können Sie sich wenden, um Support zu erhalten? | Microsoft | Wenden Sie sich bei einem integrierten System an Ihren Azure Stack-Betreiber (in Ihrer Organisation oder bei Ihrem Dienstanbieter), um Unterstützung zu erhalten.<br><br>Support zum Azure Stack Development Kit erhalten Sie in den [Microsoft-Foren](https://social.msdn.microsoft.com/Forums/home?forum=azurestack). Da das Development Kit eine Evaluierungsumgebung ist, wird über den Microsoft-Kundensupport (Customer Support Services, CSS) kein offizieller Support angeboten.
| Verfügbare Dienste | Sehen Sie sich die Liste mit den [Azure-Produkten](https://azure.microsoft.com/services/?b=17.04b) an. Die verfügbaren Dienste variieren je nach Azure-Region. | Azure Stack unterstützt eine Teilgruppe der Azure-Dienste. Die Dienste variieren in Abhängigkeit davon, was von Ihrer Organisation oder Ihrem Dienstanbieter angeboten wird.
| Azure Resource Manager-Endpunkt* | https://management.azure.com | Verwenden Sie bei einem integrierten Azure Stack-System den vom Azure Stack-Betreiber bereitgestellten Endpunkt.<br><br>Verwenden Sie für das Development Kit den folgenden Link: https://management.local.azurestack.external.
| Portal-URL* | [https://portal.azure.com](https://portal.azure.com) | Verwenden Sie bei einem integrierten Azure Stack-System die vom Azure Stack-Betreiber bereitgestellte URL.<br><br>Verwenden Sie für das Development Kit den folgenden Link: https://portal.local.azurestack.external.
| Region | Sie können die Region für die Bereitstellung auswählen. | Verwenden Sie bei einem integrierten Azure Stack-System die im System verfügbare Region.<br><br>Für das Development Kit lautet die Region immer **local**.
| Ressourcengruppen | Eine Ressourcengruppe kann mehrere Regionen umfassen. | Sowohl für integrierte Systeme als auch für das Development Kit gibt es nur eine Region.
|Unterstützte Namespaces, Ressourcentypen und API-Versionen | Aktuelle Versionen (oder frühere Versionen, die noch nicht als veraltet eingestuft sind). | Azure Stack unterstützt bestimmte Versionen. Informationen hierzu finden Sie im Abschnitt [Versionsanforderungen](#version-requirements) dieses Artikels.
| | |

*Wenn Sie Azure Stack-Betreiber sind, helfen Ihnen die Informationen unter [Using the administrator portal](../operator/azure-stack-manage-portals.md) (Verwenden des Administratorportals) und [Azure Stack administration basics](../operator/azure-stack-manage-basics.md) (Grundlagen zur Verwaltung von Azure Stack) weiter.

## <a name="helpful-tools-and-best-practices"></a>Hilfreiche Tools und bewährte Methoden

Microsoft stellt Tools und Anleitungen für die Azure Stack-Entwicklung bereit.

| Empfehlung | Referenzen |
| -------- | ------------- |
| Installieren Sie die richtigen Tools auf der Entwicklerarbeitsstation. | - [Installieren von PowerShell](../operator/azure-stack-powershell-install.md)<br>- [Herunterladen von Tools](../operator/azure-stack-powershell-download.md)<br>- [Konfigurieren von PowerShell](azure-stack-powershell-configure-user.md)<br>- [Installieren von Visual Studio](azure-stack-install-visual-studio.md) 
| Lesen Sie sich die Informationen zu folgenden Punkten durch:<br>- Aspekte zu Azure Resource Manager-Vorlagen<br>- Suchen nach Schnellstartvorlagen<br>- Verwenden eines Richtlinienmoduls als Hilfe zur Verwendung von Azure für die Azure Stack-Entwicklung | [Entwickeln für Azure Stack](azure-stack-developer.md) | 
| Lesen Sie sich die bewährten Methoden für Vorlagen durch, und befolgen Sie diese Methoden. | [Resource Manager-Schnellstartvorlagen](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md#best-practices)
| | |

## <a name="version-requirements"></a>Versionsanforderungen

Azure Stack unterstützt bestimmte Versionen von Azure PowerShell und Azure-Dienst-APIs. Verwenden Sie unterstützte Versionen, um sicherzustellen, dass Ihre App sowohl für Azure Stack als auch für Azure bereitgestellt werden kann.

Verwenden Sie [API-Versionsprofile](azure-stack-version-profiles.md), damit sichergestellt ist, dass Sie eine richtige Version von Azure PowerShell nutzen. Ermitteln Sie den verwendeten Azure Stack-Build, um das neueste verwendbare API-Versionsprofil zu bestimmen. Sie erhalten diese Informationen von Ihrem Azure Stack-Administrator.

> [!NOTE]
> Wenn Sie das Azure Stack Development Kit nutzen und über Administratorzugriff verfügen, können Sie den Azure Stack-Build ermitteln, indem Sie im Abschnitt [Bestimmen der aktuellen Version](../operator/azure-stack-updates.md#determine-the-current-version) die entsprechenden Schritte ausführen.

Führen Sie für die anderen APIs den folgenden PowerShell-Befehl aus, um die Namespaces, Ressourcentypen und API-Versionen auszugeben, die in Ihrem Azure Stack-Abonnement unterstützt werden (unter Umständen bestehen auf Eigenschaftsebene noch Unterschiede). Damit dieser Befehl funktioniert, müssen Sie PowerShell für eine Azure Stack-Umgebung bereits [installiert](../operator/azure-stack-powershell-install.md) und [konfiguriert](azure-stack-powershell-configure-user.md) haben. Sie müssen auch über ein Abonnement für ein Azure Stack-Angebot verfügen.

```powershell
Get-AzureRmResourceProvider | Select ProviderNamespace -Expand ResourceTypes | Select * -Expand ApiVersions | `
Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} 
```

Beispielausgabe (gekürzt): ![Beispielausgabe für den Befehl „Get-AzureRmResourceProvider“](media/azure-stack-considerations/image1.png)

## <a name="next-steps"></a>Nächste Schritte

Ausführlichere Informationen zu den Unterschieden auf Dienstebene finden Sie unter:

* [Überlegungen zur Verwendung von virtuellen Computern in Azure Stack](azure-stack-vm-considerations.md)
* [Azure Stack-Speicher: Unterschiede und Überlegungen](azure-stack-acs-differences.md)
* [Überlegungen zu Azure Stack-Netzwerken](azure-stack-network-differences.md)
