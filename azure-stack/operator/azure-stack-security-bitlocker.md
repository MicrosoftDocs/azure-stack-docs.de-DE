---
title: Verschlüsselung für ruhende Daten
titleSuffix: Azure Stack
description: Hier erfahren Sie, wie Azure Stack Ihre Daten mit Verschlüsselung ruhender Daten schützt.
services: azure-stack
author: PatAltimore
ms.service: azure-stack
ms.topic: article
ms.date: 06/17/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 03/11/2019
keywords: ''
ms.openlocfilehash: 13455668330571e9190d37ea0abb4de2a7b88a5d
ms.sourcegitcommit: 62283e9826ea78b218f5d2c6c555cc44196b085d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74780710"
---
# <a name="data-at-rest-encryption-in-azure-stack"></a>Verschlüsselung für ruhende Daten in Azure Stack

Azure Stack schützt Benutzer- und Infrastrukturdaten auf der Ebene des Speichersubsystems unter Verwendung von Verschlüsselung ruhender Daten. Das Speichersubsystem von Azure Stack wird mithilfe von BitLocker mit 128-Bit-AES-Verschlüsselung verschlüsselt. BitLocker-Schlüssel werden in einem internen, geheimen Speicher dauerhaft gespeichert.

Verschlüsselung für ruhende Daten ist eine häufige Anforderung vieler der wichtigsten Konformitätsstandards (z. B. PCI-DSS, FedRAMP, HIPAA). Azure Stack ermöglicht Ihnen, diese Anforderungen ohne zusätzlichen Aufwand oder Konfigurationen zu erfüllen. Weitere Informationen dazu, wie Azure Stack Ihnen bei der Erfüllung von Konformitätsstandards zu erfüllen finden Sie im [Microsoft Service Trust Portal](https://aka.ms/AzureStackCompliance).

> [!NOTE]
> Verschlüsselung für ruhende Daten schützt Ihre Daten vor dem Zugriff durch Personen, die eine oder mehrere Festplatten physisch entwendet haben. Verschlüsselung für ruhende Daten schützt nicht davor, dass Daten über das Netzwerk abgefangen werden (Daten während der Übertragung), dass Daten aktuell verwendet werden (Daten im Arbeitsspeicher), oder genereller, dass Daten ausgefiltert werden, während das System aktiv ist und ausgeführt wird.

## <a name="retrieving-bitlocker-recovery-keys"></a>Abrufen von BitLocker-Wiederherstellungsschlüsseln

Die Azure Stack-BitLocker-Schlüssel für ruhende Daten werden intern verwaltet. Sie müssen sie im normalen Betrieb oder während des Systemstarts nicht bereitstellen. Bei manchen Supportszenarios können BitLocker-Wiederherstellungsschlüssel jedoch erforderlich sein, um das System wieder online zu bringen.  

> [!WARNING]
> Rufen Sie Ihre BitLocker-Wiederherstellungsschlüssel ab, und speichern Sie sie an einem sicheren Ort außerhalb von Azure Stack. Wenn Sie bei bestimmten Supportszenarios keine Wiederherstellungsschlüssel haben, kann dies zu Datenverlusten führen und die Wiederherstellung des Systems aus einem Sicherungsimage erforderlich machen.

Für das Abrufen der BitLocker-Wiederherstellungsschlüssel benötigen Sie Zugriff auf den [privilegierten Endpunkt](azure-stack-privileged-endpoint.md) (PEP). Führen Sie in einer PEP-Sitzung das Cmdlet „Get-AzsRecoveryKeys“ aus.

```powershell
##This cmdlet retrieves the recovery keys for all the volumes that are encrypted with BitLocker.
Get-AzsRecoveryKeys
```

Optionale Parameter für das Cmdlet *Get-AzsRecoveryKeys*:

| Parameter | BESCHREIBUNG | type | Erforderlich |
|---------|---------|---------|---------|
|*raw* | Gibt Rohdaten der Zuordnung zwischen Wiederherstellungsschlüssel, Computername und Kennwort-ID(s) jedes verschlüsselten Volumes zurück.  | Switch | Nein (entwickelt für Suppportszenarien)|

## <a name="troubleshoot-issues"></a>Behandeln von Problemen

In extremen Fällen kann eine BitLocker-Entsperranforderung fehlschlagen und dazu führen, dass ein bestimmtes Volume nicht gestartet werden kann. Abhängig von der Verfügbarkeit einiger der Komponenten der Architektur kann dieser Fehler zu Ausfallzeiten und potenziellen Datenverlusten führen, wenn Sie nicht über Ihre BitLocker-Wiederherstellungsschlüssel verfügen.

> [!WARNING]
> Rufen Sie Ihre BitLocker-Wiederherstellungsschlüssel ab, und speichern Sie sie an einem sicheren Ort außerhalb von Azure Stack. Wenn Sie bei bestimmten Supportszenarios keine Wiederherstellungsschlüssel haben, kann dies zu Datenverlusten führen und die Wiederherstellung des Systems aus einem Sicherungsimage erforderlich machen.

Wenn Sie vermuten, dass Ihr System Probleme mit BitLocker hat, z. B. dass Azure Stack nicht starten kann, wenden Sie sich an den Support. Für Support benötigen Sie Ihre BitLocker-Wiederherstellungsschlüssel. Der Großteil der BitLocker-bezogenen Probleme lässt sich durch einen FRU-Vorgang für den/das spezifische/n VM/Host/Volume beheben. In den anderen Fällen kann ein manuelles Entsperrverfahren mithilfe der BitLocker-Wiederherstellungsschlüssel ausgeführt werden. Wenn die BitLocker-Wiederherstellungsschlüssel nicht verfügbar sind, ist die einzige Möglichkeit die Wiederherstellung aus einem Sicherungsimage. Je nachdem, wann die letzte Sicherung durchgeführt wurde, können Datenverluste auftreten.

## <a name="next-steps"></a>Nächste Schritte

- [Weitere Informationen zur Sicherheit von Azure Stack](azure-stack-security-foundations.md)
- Weitere Informationen dazu, wie BitLocker CSVs schützt, finden Sie unter [Schützen von freigegebenen Clustervolumes (Cluster Shared Volumes, CSVs) und Storage Area Networks (SANs) mit BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/protecting-cluster-shared-volumes-and-storage-area-networks-with-bitlocker).