---
title: Unterschiede zwischen Azure Stack Hub und Azure beim Verwenden von Diensten und Erstellen von Apps
description: Hier finden Sie grundlegende Informationen zu den Unterschieden zwischen Azure und Azure Stack Hub beim Verwenden von Diensten und Erstellen von Apps.
author: sethmanheim
ms.topic: overview
ms.date: 06/11/2020
ms.author: sethm
ms.lastreviewed: 12/27/2019
ms.openlocfilehash: fb5f30d97d1c12a94b5366a358dc21450965fd93
ms.sourcegitcommit: 5f4f0ee043ff994efaad44129ce49be43c64d5dc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/16/2020
ms.locfileid: "84819533"
---
# <a name="differences-between-azure-stack-hub-and-azure-when-using-services-and-building-apps"></a>Unterschiede zwischen Azure Stack Hub und Azure beim Verwenden von Diensten und Erstellen von Apps

Machen Sie sich vor der Verwendung von Diensten oder der Erstellung von Apps für Azure Stack Hub mit den Unterschieden zwischen Azure Stack Hub und Azure vertraut. In diesem Artikel wird auf verschiedene Features und wichtige Aspekte der Verwendung von Azure Stack Hub als Hybrid Cloud-Entwicklungsumgebung eingegangen.

## <a name="overview"></a>Übersicht

Azure Stack Hub ist eine Hybrid Cloud-Plattform, mit der Sie Azure-Dienste aus dem Datencenter Ihres Unternehmens oder Ihres Service Providers verwenden können. Sie können eine Anwendung in Azure Stack Hub erstellen und anschließend in Azure Stack Hub, in Azure oder in Ihrer Azure Hybrid Cloud bereitstellen.

Ihr Azure Stack Hub-Betreiber teilt Ihnen mit, welche Dienste für Sie verfügbar sind und wie Sie Support erhalten. Diese Dienste sind jeweils über die angepassten Pläne und Angebote erhältlich.

Beim [Inhalt der technischen Dokumentation von Azure](/azure) wird davon ausgegangen, dass Apps nicht für Azure Stack Hub, sondern für einen Azure-Dienst entwickelt werden. Für die Erstellung und Bereitstellung von Apps für Azure Stack Hub müssen Sie einige wichtige Unterschiede kennen, z. B.:

* Azure Stack Hub stellt eine Teilmenge dieser Dienste und Features bereit, die in Azure verfügbar sind.
* Ihr Unternehmen oder Service Provider kann auswählen, welche Dienste angeboten werden sollen. Die verfügbaren Optionen schließen unter Umständen benutzerdefinierte Dienste oder Anwendungen mit ein. Sie enthalten unter Umständen eine eigene angepasste Dokumentation.
* Verwenden Sie die richtigen Azure Stack Hub-spezifischen Endpunkte (z. B. die URLs für die Portaladresse und den Azure Resource Manager-Endpunkt).
* Sie müssen PowerShell- und API-Versionen verwenden, die von Azure Stack Hub unterstützt werden. Mit der Verwendung von unterstützten Versionen wird sichergestellt, dass Ihre Apps sowohl in Azure Stack Hub als auch in Azure funktionieren.

## <a name="cheat-sheet-high-level-differences"></a>Cheat Sheet: Allgemeine Unterschiede

In der folgenden Tabelle sind die allgemeinen Unterschiede zwischen Azure Stack Hub und Azure beschrieben. Diese Unterschiede müssen bei der Entwicklung für Azure Stack Hub sowie bei der Verwendung von Azure Stack Hub-Diensten berücksichtigt werden.

| Bereich | Azure (global) | Azure Stack Hub |
| -------- | ------------- | ----------|
| Wer ist der Betreiber? | Microsoft | Ihre Organisation bzw. Ihr Dienstanbieter.|
| An wen können Sie sich wenden, um Support zu erhalten? | Microsoft | Wenden Sie sich bei einem integrierten System an Ihren Azure Stack Hub-Betreiber (in Ihrer Organisation oder bei Ihrem Dienstanbieter), um Unterstützung zu erhalten.<br><br>Support zum Azure Stack Development Kit (ASDK) erhalten Sie in den [Microsoft-Foren](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack). Da das Development Kit eine Evaluierungsumgebung ist, wird über den Microsoft-Support kein offizieller Support angeboten.
| Verfügbare Dienste | Sehen Sie sich die Liste mit den [Azure-Produkten](https://azure.microsoft.com/services/?b=17.04b) an. Die verfügbaren Dienste variieren je nach Azure-Region. | Azure Stack Hub unterstützt eine Teilgruppe der Azure-Dienste. Die Dienste variieren in Abhängigkeit davon, was von Ihrer Organisation oder Ihrem Dienstanbieter angeboten wird.
| Azure Resource Manager-Endpunkt* | `https://management.azure.com` | Verwenden Sie bei einem integrierten Azure Stack Hub-System den vom Azure Stack Hub-Betreiber bereitgestellten Endpunkt.<br><br>Verwenden Sie für das Development Kit den folgenden Link: `https://management.local.azurestack.external`.
| Portal-URL* | [https://portal.azure.com](https://portal.azure.com) | Verwenden Sie bei einem integrierten Azure Stack Hub-System die vom Azure Stack Hub-Betreiber bereitgestellte URL.<br><br>Verwenden Sie für das Development Kit den folgenden Link: `https://portal.local.azurestack.external`.
| Region | Sie können die Region für die Bereitstellung auswählen. | Verwenden Sie bei einem integrierten Azure Stack Hub-System die im System verfügbare Region.<br><br>Für das Azure Stack Development Kit (ASDK) lautet die Region immer **local**.
| Ressourcengruppen | Eine Ressourcengruppe kann mehrere Regionen umfassen. | Sowohl für integrierte Systeme als auch für das Development Kit gibt es nur eine Region.
|Unterstützte Namespaces, Ressourcentypen und API-Versionen | Aktuelle Versionen (oder frühere Versionen, die noch nicht als veraltet eingestuft sind). | Azure Stack Hub unterstützt bestimmte Versionen. Informationen hierzu finden Sie im Abschnitt [Versionsanforderungen](#version-requirements) dieses Artikels.
| | |

*Wenn Sie Azure Stack Hub-Betreiber sind, helfen Ihnen die Informationen unter [Verwenden des Administratorportals in Azure Stack Hub](../operator/azure-stack-manage-portals.md) und [Grundlagen zur Verwaltung von Azure Stack Hub](../operator/azure-stack-manage-basics.md) weiter.

## <a name="helpful-tools-and-best-practices"></a>Hilfreiche Tools und bewährte Methoden

Microsoft stellt Tools und Anleitungen für die Azure Stack Hub-Entwicklung bereit.

| Empfehlung | References |
| -------- | ------------- |
| Installieren Sie die richtigen Tools auf der Entwicklerarbeitsstation. | - [Installieren von PowerShell](../operator/azure-stack-powershell-install.md)<br>- [Herunterladen von Tools](../operator/azure-stack-powershell-download.md)<br>- [Konfigurieren von PowerShell](azure-stack-powershell-configure-user.md)<br>- [Installieren von Visual Studio](azure-stack-install-visual-studio.md)
| Lesen Sie sich die Informationen zu folgenden Punkten durch:<br>- Aspekte zu Azure Resource Manager-Vorlagen<br>- Suchen nach Schnellstartvorlagen<br>- Verwenden eines Richtlinienmoduls als Hilfe zur Verwendung von Azure für die Azure Stack Hub-Entwicklung | [Entwickeln für Azure Stack Hub](azure-stack-developer.md) |
| Lesen Sie sich die bewährten Methoden für Vorlagen durch, und befolgen Sie diese Methoden. | [Resource Manager-Schnellstartvorlagen](https://aka.ms/aa6yz42)
| | |

## <a name="version-requirements"></a>Versionsanforderungen

Azure Stack Hub unterstützt bestimmte Versionen von Azure PowerShell und Azure-Dienst-APIs. Verwenden Sie unterstützte Versionen, um sicherzustellen, dass Ihre App sowohl für Azure Stack Hub als auch für Azure bereitgestellt werden kann.

Verwenden Sie [API-Versionsprofile](azure-stack-version-profiles.md), damit sichergestellt ist, dass Sie eine richtige Version von Azure PowerShell nutzen. Ermitteln Sie den verwendeten Azure Stack Hub-Build, um das neueste verwendbare API-Versionsprofil zu bestimmen. Sie erhalten diese Informationen von Ihrem Azure Stack Hub-Administrator.

> [!NOTE]
> Wenn Sie das Azure Stack Development Kit nutzen und über Administratorzugriff verfügen, können Sie den Azure Stack Hub-Build ermitteln, indem Sie im Abschnitt [Bestimmen der aktuellen Version](../operator/azure-stack-updates.md) die entsprechenden Schritte ausführen.

Führen Sie für die anderen APIs den folgenden PowerShell-Befehl aus, um die Namespaces, Ressourcentypen und API-Versionen auszugeben, die in Ihrem Azure Stack Hub-Abonnement unterstützt werden (unter Umständen bestehen auf Eigenschaftsebene noch Unterschiede). Damit dieser Befehl funktioniert, müssen Sie PowerShell für eine Azure Stack Hub-Umgebung bereits [installiert](../operator/azure-stack-powershell-install.md) und [konfiguriert](azure-stack-powershell-configure-user.md) haben. Sie müssen auch über ein Abonnement für ein Azure Stack Hub-Angebot verfügen.

```powershell
Get-AzureRmResourceProvider | Select ProviderNamespace -Expand ResourceTypes | Select * -Expand ApiVersions | `
Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} 
```

Beispielausgabe (gekürzt): ![Beispielausgabe für den Befehl „Get-AzureRmResourceProvider“](media/azure-stack-considerations/image1.png)

## <a name="next-steps"></a>Nächste Schritte

Ausführlichere Informationen zu den Unterschieden auf Dienstebene finden Sie unter:

* [Features von Azure Stack-VMs](azure-stack-vm-considerations.md)
* [Azure Stack Hub-Speicher: Unterschiede und Überlegungen](azure-stack-acs-differences.md)
* [Azure Stack Hub-Netzwerke: Unterschiede und Überlegungen](azure-stack-network-differences.md)
