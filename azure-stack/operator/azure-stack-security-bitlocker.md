---
title: Verschlüsselung für ruhende Daten
titleSuffix: Azure Stack Hub
description: Hier erfahren Sie, wie Azure Stack Hub Ihre Daten mit Verschlüsselung ruhender Daten schützt.
author: BryanLa
ms.topic: how-to
ms.date: 03/04/2020
ms.author: bryanla
ms.reviewer: fiseraci
ms.lastreviewed: 08/06/2020
ms.openlocfilehash: 147da85fc75ffbe76c1216c402fcec10ba0eb60f
ms.sourcegitcommit: ccc4ee05d71496653b6e27de1bb12e4347e20ba4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102231199"
---
# <a name="data-at-rest-encryption-in-azure-stack-hub"></a>Verschlüsselung für ruhende Daten in Azure Stack Hub

Azure Stack Hub schützt Benutzer- und Infrastrukturdaten auf der Ebene des Speichersubsystems unter Verwendung von Verschlüsselung ruhender Daten. Das Speichersubsystem von Azure Stack Hub wird standardmäßig mithilfe von BitLocker mit 128-Bit-AES-Verschlüsselung verschlüsselt. BitLocker-Schlüssel werden in einem internen, geheimen Speicher dauerhaft gespeichert. Zum Zeitpunkt der Bereitstellung ist es auch möglich, BitLocker für die Verwendung der 256-Bit-AES-Verschlüsselung zu konfigurieren.

Verschlüsselung für ruhende Daten ist eine häufige Anforderung vieler der wichtigsten Konformitätsstandards (z. B. PCI-DSS, FedRAMP, HIPAA). Azure Stack Hub ermöglicht Ihnen, diese Anforderungen ohne zusätzlichen Aufwand oder zusätzliche Konfigurationen zu erfüllen. Weitere Informationen dazu, wie Azure Stack Hub Sie dabei unterstützt, Konformitätsstandards zu erfüllen, finden Sie im [Microsoft Service Trust Portal](https://aka.ms/AzureStackCompliance).

> [!NOTE]
> Verschlüsselung für ruhende Daten schützt Ihre Daten vor dem Zugriff durch Personen, die eine oder mehrere Festplatten physisch entwendet haben. Verschlüsselung für ruhende Daten schützt nicht davor, dass Daten über das Netzwerk abgefangen werden (Daten während der Übertragung), dass Daten aktuell verwendet werden (Daten im Arbeitsspeicher), oder genereller, dass Daten ausgefiltert werden, während das System aktiv ist und ausgeführt wird.

## <a name="retrieving-bitlocker-recovery-keys"></a>Abrufen von BitLocker-Wiederherstellungsschlüsseln

Die Azure Stack Hub-BitLocker-Schlüssel für ruhende Daten werden intern verwaltet. Sie müssen sie im normalen Betrieb oder während des Systemstarts nicht bereitstellen. Bei manchen Supportszenarios können BitLocker-Wiederherstellungsschlüssel jedoch erforderlich sein, um das System wieder online zu bringen.  

> [!WARNING]
> Rufen Sie Ihre BitLocker-Wiederherstellungsschlüssel ab, und speichern Sie sie an einem sicheren Ort außerhalb von Azure Stack Hub. Wenn Sie bei bestimmten Supportszenarios keine Wiederherstellungsschlüssel haben, kann dies zu Datenverlusten führen und die Wiederherstellung des Systems aus einem Sicherungsimage erforderlich machen.

Für das Abrufen der BitLocker-Wiederherstellungsschlüssel benötigen Sie Zugriff auf den [privilegierten Endpunkt](azure-stack-privileged-endpoint.md) (PEP). Führen Sie in einer PEP-Sitzung das Cmdlet „Get-AzsRecoveryKeys“ aus.

```powershell
##This cmdlet retrieves the recovery keys for all the volumes that are encrypted with BitLocker.
Get-AzsRecoveryKeys -raw
```

Parameter für das Cmdlet *Get-AzsRecoveryKeys*:

| Parameter | BESCHREIBUNG | Typ | Erforderlich |
|---------|---------|---------|---------|
|*raw* | Gibt die Datenzuordnung zwischen Wiederherstellungsschlüssel, Computername und Kennwort-ID(s) jedes verschlüsselten Volumes zurück.  | Schalter | Nein, aber empfohlen |

## <a name="troubleshoot-issues"></a>Behandeln von Problemen

In extremen Fällen kann eine BitLocker-Entsperranforderung fehlschlagen und dazu führen, dass ein bestimmtes Volume nicht gestartet werden kann. Abhängig von der Verfügbarkeit einiger der Komponenten der Architektur kann dieser Fehler zu Ausfallzeiten und potenziellen Datenverlusten führen, wenn Sie nicht über Ihre BitLocker-Wiederherstellungsschlüssel verfügen.

> [!WARNING]
> Rufen Sie Ihre BitLocker-Wiederherstellungsschlüssel ab, und speichern Sie sie an einem sicheren Ort außerhalb von Azure Stack Hub. Wenn Sie bei bestimmten Supportszenarios keine Wiederherstellungsschlüssel haben, kann dies zu Datenverlusten führen und die Wiederherstellung des Systems aus einem Sicherungsimage erforderlich machen.

Wenn Sie vermuten, dass Ihr System Probleme mit BitLocker hat, z. B. dass Azure Stack Hub nicht starten kann, wenden Sie sich an den Support. Für Support benötigen Sie Ihre BitLocker-Wiederherstellungsschlüssel. Der Großteil der BitLocker-bezogenen Probleme lässt sich durch einen FRU-Vorgang für den/das spezifische/n VM/Host/Volume beheben. In den anderen Fällen kann ein manuelles Entsperrverfahren mithilfe der BitLocker-Wiederherstellungsschlüssel ausgeführt werden. Wenn die BitLocker-Wiederherstellungsschlüssel nicht verfügbar sind, ist die einzige Möglichkeit die Wiederherstellung aus einem Sicherungsimage. Je nachdem, wann die letzte Sicherung durchgeführt wurde, können Datenverluste auftreten.

## <a name="next-steps"></a>Nächste Schritte

- [Weitere Informationen zur Sicherheit von Azure Stack Hub](azure-stack-security-foundations.md).
- Weitere Informationen dazu, wie BitLocker CSVs schützt, finden Sie unter [Schützen von freigegebenen Clustervolumes (Cluster Shared Volumes, CSVs) und Storage Area Networks (SANs) mit BitLocker](/windows/security/information-protection/bitlocker/protecting-cluster-shared-volumes-and-storage-area-networks-with-bitlocker).
