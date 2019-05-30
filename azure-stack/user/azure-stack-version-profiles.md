---
title: Verwalten von API-Versionsprofilen in Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie mehr zu API-Versionsprofilen in Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 9ebb4759ce8145ec190b65ced610dec533ed181f
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66269460"
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>Verwalten von API-Versionsprofilen in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

API-Profile geben den Azure-Ressourcenanbieter und die API-Version für Azure-REST-Endpunkte an. Mithilfe von API-Profilen können Sie benutzerdefinierte Clients in verschiedenen Sprachen erstellen. Jeder Client nutzt ein API-Profil, um eine Verbindung mit dem richtigen Ressourcenanbieter und der richtigen API-Version für Azure Stack herzustellen.

Sie können eine App für die Verwendung mit Azure-Ressourcenanbietern erstellen, ohne genau ermitteln zu müssen, welche Version der jeweiligen Ressourcenanbieter-API mit Azure Stack kompatibel ist. Es reicht, wenn Sie Ihre Anwendung auf ein Profil ausrichten. Das SDK wählt dann automatisch die richtige API-Version.

Dieses Thema enthält folgende Informationen:

 - Grundlegende Informationen zu API-Profilen für Azure Stack
 - Informationen zur Verwendung von API-Profilen für die Entwicklung Ihrer Lösungen
 - Informationen dazu, wo Sie codespezifische Anleitungen finden

## <a name="summary-of-api-profiles"></a>Zusammenfassung zu API-Profilen

- API-Profile werden zur Darstellung mehrerer Azure-Ressourcenanbieter und ihrer API-Versionen verwendet.
- API-Profile wurden erstellt, damit Sie Vorlagen für mehrere Azure-Clouds generieren können. Profile bieten eine kompatible und stabile Schnittstelle.
- Profile werden viermal pro Jahr veröffentlicht.
- Drei Benennungskonventionen für Profile werden verwendet:
    - **Neueste**  
        Enthält die neuesten in der globalen Azure-Umgebung veröffentlichen API-Versionen.
    - **jjjj-mm-tt-hybrid**  
    Halbjährliche Veröffentlichung. Bei dieser Veröffentlichung stehen die cloudübergreifende Konsistenz und Stabilität im Vordergrund. Das Ziel dieses Profils ist die optimale Kompatibilität mit Azure Stack.
    - **jjjj-mm-tt-profile** sorgt für ein ausgewogenes Verhältnis zwischen der optimalen Stabilität und den neuesten Features.

## <a name="azure-api-profiles-and-azure-stack-compatibility"></a>Azure-API-Profile und Azure Stack-Kompatibilität

Die neuesten Azure-API-Profile sind nicht mit Azure Stack kompatibel. Sie können die folgenden Benennungskonventionen verwenden, um die Profile für Ihre Azure Stack-Lösungen zu identifizieren.

**Neueste**  
Dieses Profil enthält die aktuellsten API-Versionen in der globalen Azure-Umgebung, die in Azure Stack nicht funktionieren. **Neueste** weist die größte Anzahl von aktuellen Änderungen auf. Das Profil vernachlässigt die Stabilität und Kompatibilität mit anderen Clouds. Wenn Sie versuchen, die aktuellsten API-Versionen zu verwenden, ist **Neueste** das Profil, das Sie verwenden sollten.

**jjjj-mm-tt-hybrid**  
Dieses Profil wird jedes Jahr im März und im September veröffentlicht. Es bietet optimale Stabilität und Kompatibilität mit verschiedenen Clouds. **jjjj-mm-tt-hybrid** eignet sich für die globale Azure-Umgebung und Azure Stack. Die in diesem Profil aufgeführten Azure-API-Versionen sind mit den Versionen identisch, die für Azure Stack aufgeführt werden. Sie können dieses Profil verwenden, um Code für Hybridcloudlösungen zu entwickeln.

**jjjj-mm-tt-profile**  
Dieses Profil wird für die globale Azure-Umgebung im Juni und Dezember veröffentlicht. Es funktioniert nicht für Azure Stack. Üblicherweise werden zahlreiche wichtige Änderungen vorgenommen. Das Profil sorgt für ein ausgewogenes Verhältnis zwischen optimaler Stabilität und den neuesten Features. Der Unterschied zwischen **Neueste** und diesem Profil besteht darin, dass **Neueste** immer aus den neuesten API-Versionen besteht, und zwar unabhängig davon, wann die API veröffentlicht wurde. Wenn beispielsweise morgen eine neue API-Version für die Compute-API erstellt wird, wird diese API-Version im Profil **Neueste** aufgeführt, aber nicht im Profil **jjjj-mm-tt-profile**, weil dieses Profil bereits vorhanden ist. **jjjj-mm-tt-profile** umfasst die neuesten Versionen, die vor Juni oder vor Dezember veröffentlicht wurden.

## <a name="azure-resource-manager-api-profiles"></a>Azure Resource Manager-API-Profile

Azure Stack verwendet nicht die aktuellste Version der API-Versionen in der globalen Azure-Umgebung. Wenn Sie Ihre eigene Lösung erstellen, müssen Sie für jeden Azure-Ressourcenanbieter die API-Version ermitteln, die mit Azure Stack kompatibel ist.

Sie können ein API-Profil verwenden, um nicht die einzelnen Ressourcenanbieter und die jeweils von Azure Stack unterstützten spezifischen Versionen ermitteln zu müssen. Das Profil gibt verschiedene Ressourcenanbieter und API-Versionen an. Das SDK bzw. ein mit dem SDK erstelltes Tool wählt dann das im Profil angegebene `api-version`-Zielelement aus. Bei Verwendung von API-Profilen können Sie eine Profilversion angeben, die für eine gesamte Vorlage gilt. Zur Laufzeit wählt Azure Resource Manager die richtige Version der Ressource aus.

API-Profile können mit Tools verwendet werden, die Azure Resource Manager nutzen, beispielsweise mit PowerShell, der Azure CLI, im SDK bereitgestelltem Code und Microsoft Visual Studio. Tools und SDKs können mithilfe von Profilen lesen, welche Version der Module und Bibliotheken beim Erstellen einer Anwendung aufgenommen werden soll.

Beispiel: Wenn Sie ein Speicherkonto mit PowerShell und dem Ressourcenanbieter **Microsoft.Storage**, der die **API-Version** 2016-03-30 unterstützt, und einen virtuellen Computer mit dem Ressourcenanbieter **Microsoft.Compute** mit **API-Version** 2015-12-01 erstellen, müssten Sie nachsehen, welches PowerShell-Modul 2016-03-30 für Speicher und 2015-02-01 für Compute unterstützt und die jeweiligen Module installieren. Stattdessen können Sie ein Profil verwenden. Verwenden Sie das Cmdlet `Install-Profile <profilename>`, und PowerShell lädt die richtige Version der Module.

Analog dazu können Sie das Profil angeben, wenn Sie das Python SDK zum Erstellen einer Python-basierten Anwendung nutzen. Das SDK lädt die richtigen Module für die Ressourcenanbieter, die Sie in Ihrem Skript angegeben haben.

Als Entwickler können Sie sich auf das Schreiben Ihrer Lösung konzentrieren. Statt zu ermitteln, welche API-Versionen, Ressourcenanbieter und Clouds zusammen verwendet werden können, verwenden Sie ein Profil und können sicher sein, dass Ihr Code in allen Clouds funktioniert, die dieses Profil unterstützen.

## <a name="api-profile-code-samples"></a>Codebeispiele für API-Profile

Es gibt Codebeispiele, die Sie dabei unterstützen, mithilfe von Profilen Ihre Lösung mit Ihrer bevorzugten Sprache in Azure Stack zu integrieren. Derzeit stehen Anleitungen und Beispiele für die folgenden Sprachen zur Verfügung:

- **.NET** Sie können das API-Profil verwenden, um die aktuelle, stabilste Version der einzelnen Ressourcentypen in einem Ressourcenanbieterpaket abzurufen. Weitere Informationen finden Sie unter [Verwenden von API-Versionsprofilen mit .NET in Azure Stack](azure-stack-version-profiles-net.md).
- **PowerShell**  
Sie können mit dem über den PowerShell-Katalog verfügbaren Modul **AzureRM.Bootstrapper** die PowerShell-Cmdlets abrufen, die für die Arbeit mit API-Versionsprofilen erforderlich sind. Informationen finden Sie unter [Use API version profiles for PowerShell in Azure Stack](azure-stack-version-profiles-powershell.md) (Verwenden von API-Versionsprofilen für PowerShell in Azure Stack).
- **Azure-Befehlszeilenschnittstelle**  
Sie können Ihre Umgebungskonfiguration so aktualisieren, dass das spezifische API-Versionsprofil für Azure Stack verwendet wird. Weitere Informationen finden Sie unter [Verwenden von API-Versionsprofilen für die Azure CLI](azure-stack-version-profiles-azurecli2.md).
- **Go**  
Im Go SDK ist ein Profil eine Kombination aus verschiedenen Ressourcentypen mit unterschiedlichen Versionen aus verschiedenen Diensten. Profile sind unter dem Pfad „profiles/“ verfügbar. Ihre Version ist im Format **JJJJ-MM-TT** angegeben. Weitere Informationen finden Sie unter [Verwenden von API-Versionsprofilen mit Go in Azure Stack](azure-stack-version-profiles-go.md).
- **Ruby**  
Das Ruby-SDK für Azure Stack Resource Manager umfasst Tools zum Erstellen und Verwalten Ihrer Infrastruktur. Im SDK enthaltene Ressourcenanbieter sind z.B. Compute, virtuelle Netzwerke und Speicher mit der Sprache Ruby. Weitere Informationen finden Sie unter [Verwenden von API-Versionsprofilen mit Ruby](azure-stack-version-profiles-ruby.md).
- **Python**  
Das Python SDK unterstützt API-Versionsprofile für unterschiedliche Cloudplattformen, z.B. Azure Stack und globales Azure. Sie können API-Profile zum Erstellen von Lösungen für Hybrid Cloud nutzen. Weitere Informationen finden Sie unter [Verwenden von API-Versionsprofilen mit Python](azure-stack-version-profiles-python.md).

## <a name="next-steps"></a>Nächste Schritte

* [Installieren von PowerShell für Azure Stack](../operator/azure-stack-powershell-install.md)
* [Konfigurieren der PowerShell-Umgebung des Azure Stack-Benutzers](azure-stack-powershell-configure-user.md)
* [Lesen Sie ausführliche Informationen zu den von den Profilen unterstützten API-Versionen für Ressourcenanbieter.](azure-stack-profiles-azure-resource-manager-versions.md)
