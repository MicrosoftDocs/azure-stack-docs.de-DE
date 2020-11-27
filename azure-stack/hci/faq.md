---
title: Häufig gestellte Fragen zur Azure Stack HCI
description: Häufig gestellte Fragen zur Azure Stack HCI
ms.topic: conceptual
author: JohnCobb1
ms.author: v-johcob
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 11/24/2020
ms.openlocfilehash: 3606c62c562cb7f402aaafae33a55336345b5e62
ms.sourcegitcommit: af4374755cb4875a7cbed405b821f5703fa1c8cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95812627"
---
# <a name="azure-stack-hci-faq"></a>Häufig gestellte Fragen zur Azure Stack HCI
Die häufig gestellten Fragen (FAQ) zur Azure Stack HCI enthalten einen Abschnitt zur Azure Stack HCI-Konnektivität und einen Abschnitt mit allgemeinen Fragen.

## <a name="azure-stack-hci-connectivity"></a>Azure Stack HCI-Konnektivität
Die Azure Stack HCI ist ein lokaler hyperkonvergenter Infrastrukturstapel, der als Azure-Hybriddienst bereitgestellt wird. Sie installieren die Azure Stack HCI-Software auf physischen Servern, die Sie lokal steuern. Anschließend stellen Sie eine Verbindung mit Azure her, um Überwachung, Support und Abrechnung sowie optionale Verwaltungs- und Sicherheitsfeatures cloudbasiert zu erhalten. In diesem FAQ-Abschnitt wird erläutert, wie die Azure Stack HCI die Cloud nutzt, indem häufig gestellte Fragen zu Konnektivitätsanforderungen und -verhalten beantwortet werden.

### <a name="your-data-stays-on-premises"></a>Ihre Daten bleiben am lokalen Speicherort

**Werden meine in der Azure Stack HCI gespeicherten Daten an die Cloud gesendet?**

Nein. Die Namen, Metadaten, Konfigurationen und Inhalte Ihrer lokalen virtuellen Computer (VMs) werden nie an die Cloud gesendet, es sei denn, Sie aktivieren zusätzliche Dienste ausdrücklich zu diesem Zweck, z. B. Azure Backup oder Azure Site Recovery, oder registrieren diese VMs einzeln in Cloudverwaltungsdiensten wie Azure Arc.

### <a name="edge-local-management-and-control"></a>Lokale Verwaltung und Steuerung am Edge

**Verwendet die Steuerungsebene für die Azure Stack HCI die Cloud?**

Nein. Sie können lokale Tools am Edge wie Windows Admin Center, PowerShell oder System Center verwenden, um die Hostinfrastruktur und die VMs direkt zu verwalten, auch wenn die Netzwerkverbindung mit der Cloud ausgefallen oder stark eingeschränkt ist. Für häufige, alltägliche Vorgänge wie das Verschieben einer VM zwischen Hosts, das Ersetzen eines ausgefallenen Laufwerks oder das Konfigurieren von IP-Adressen ist keine Nutzung der Cloud erforderlich. Cloudkonnektivität ist jedoch erforderlich, um drahtlose Softwareupdates zu erhalten, Ihre Azure-Registrierung zu ändern oder Features zu nutzen, die Clouddienste direkt für die Sicherung, Überwachung und mehr nutzen.

**Gibt es Anforderungen in Bezug auf die Bandbreite oder Latenz für die Verbindung zwischen der Azure Stack HCI und der Cloud?**

Nein. Verbindungen mit eingeschränkter Bandbreite, z. B. T1-Leitungen auf dem Land oder Satelliten-/Mobilfunkverbindungen, sind ausreichend für die Synchronisierung der Azure Stack HCI. Die erforderliche Mindestkonnektivität beträgt nur einige Kilobyte pro Tag. Zusätzliche Dienste erfordern möglicherweise zusätzliche Bandbreite, insbesondere zum Replizieren oder Sichern ganzer VMs, zum Herunterladen großer Softwareupdates oder zum Hochladen ausführlicher Protokolle für Analyse und Überwachung in der Cloud.

### <a name="designed-for-intermittent-and-limited-connectivity"></a>Konzipiert für zeitweilige und eingeschränkte Konnektivität

**Erfordert die Azure Stack HCI fortlaufende Konnektivität mit der Cloud?**

Nein. Die Azure Stack HCI ist so konzipiert, dass sie Zeiträume mit eingeschränkter oder keiner Konnektivität bewältigen kann.

**Was passiert, wenn die Netzwerkverbindung mit der Cloud vorübergehend ausfällt?**

Während die Verbindung unterbrochen ist, werden die gesamte Hostinfrastruktur und alle VMs weiterhin normal ausgeführt, und Sie können lokale Tools am Edge für die Verwaltung verwenden. Sie können jedoch keine Features verwenden, die Clouddienste direkt nutzen. Darüber hinaus sind die Informationen im Azure-Portal veraltet, bis die Azure Stack HCI wieder synchronisiert werden kann.

**Wie lang kann die Azure Stack HCI bei ausgefallener Verbindung ausgeführt werden?**

Die Azure Stack HCI muss mindestens einmal pro 30 aufeinanderfolgende Tage erfolgreich mit Azure synchronisiert werden.

**Was passiert, wenn die Grenze von 30 Tagen überschritten wird?**

Wenn die Azure Stack HCI mehr als 30 aufeinanderfolgende Tage lang nicht mit Azure synchronisiert wurde, wird der Verbindungsstatus des Clusters im Azure-Portal und anderen Tools als **Out of policy** (Ohne Richtlinie) angezeigt, und der Cluster wechselt in einen Modus mit eingeschränkter Funktionalität. In diesem Modus bleibt die Hostinfrastruktur in Betrieb, und alle aktuellen VMs werden weiterhin normal ausgeführt. Neue VMs können jedoch erst erstellt werden, wenn die Azure Stack HCI wieder synchronisiert werden kann. Der interne technische Grund hierfür ist, dass die cloudgenerierte Lizenz des Clusters abgelaufen ist und durch die Synchronisierung mit Azure erneuert werden muss.

### <a name="understanding-sync"></a>Grundlegendes zur Synchronisierung

**Welche Inhalte synchronisiert die Azure Stack HCI mit der Cloud?**

Dies hängt davon ab, welche Features Sie verwenden. Die Azure Stack HCI synchronisiert mindestens grundlegende Clusterinformationen, die im Azure-Portal angezeigt werden (z. B. die Liste der Clusterknoten, das Hardwaremodell und die Softwareversion), Abrechnungsinformationen, die die aufgelaufenen Kerntage seit der letzten Synchronisierung zusammenfassen, und die minimalen erforderlichen Diagnoseinformationen, die Microsoft dabei helfen, sicherzustellen, dass Ihre Azure Stack HCI-Instanz sicher und auf dem neuesten Stand ist und ordnungsgemäß funktioniert. Die Gesamtgröße ist klein – einige Kilobyte. Wenn Sie zusätzliche Dienste aktivieren, laden diese möglicherweise noch weitere Daten hoch. Azure Log Analytics lädt beispielsweise Protokolle und Leistungsindikatoren für die Überwachung hoch.

**Wie oft synchronisiert die Azure Stack HCI sich mit der Cloud?**

Dies hängt davon ab, welche Features Sie verwenden. Die Azure Stack HCI versucht mindestens alle 12 Stunden, eine Synchronisierung durchzuführen. Wenn die Synchronisierung nicht erfolgreich ist, werden die Inhalte lokal aufbewahrt und bei der nächsten erfolgreichen Synchronisierung gesendet. Zusätzlich zu diesem regulären Timer können Sie jederzeit eine manuelle Synchronisierung durchführen, entweder über das PowerShell-Cmdlet `Sync-AzureStackHCI` oder über das Windows Admin Center. Wenn Sie zusätzliche Dienste aktivieren, laden diese möglicherweise häufiger Daten hoch. Azure Log Analytics lädt beispielsweise alle 5 Minuten Daten für die Überwachung hoch.

### <a name="data-residency"></a>Datenresidenz

**Wo werden die synchronisierten Informationen tatsächlich gespeichert?**

Azure Stack HCI synchronisiert mit Azure und speichert Daten in einem sicheren, von Microsoft betriebenen Rechenzentrum. Weitere Informationen finden Sie unter [Data Residency in Azure](https://azure.microsoft.com/global-infrastructure/data-residency/).

### <a name="disconnected-or-air-gapped"></a>Getrennt oder mit Air Gap

**Kann ich die Azure Stack HCI verwenden und nie eine Verbindung mit Azure herstellen?**

Nein. Die Azure Stack HCI muss einmal pro 30 aufeinanderfolgende Tage erfolgreich mit Azure synchronisiert werden.

**Kann ich Daten offline zwischen einer Azure Stack HCI-Instanz mit Air Gap und Azure übertragen?**

Nein. Derzeit gibt es keinen Mechanismus zum Registrieren und Synchronisieren zwischen lokalen Systemen und Azure ohne Netzwerkkonnektivität. Beispielsweise können Sie Zertifikate oder Abrechnungsdaten nicht mithilfe von Wechselmedien übertragen. Wenn eine ausreichende kundenseitige Nachfrage diesbezüglich besteht, sind wir offen dafür, zu versuchen, ein solches Feature in Zukunft zu entwickeln. Informieren Sie uns darüber im [Feedbackforum für die Azure Stack HCI](https://feedback.azure.com/forums/929833-azure-stack-hci).

## <a name="azure-stack-hci-general-faqs"></a>Allgemeine FAQ zur Azure Stack HCI

**Welche Beziehung besteht zwischen Azure Stack HCI und Windows Server?**

Windows Server ist die Grundlage nahezu aller Azure-Produkte, und alle Features, die für Sie wichtig sind, werden weiterhin mit Unterstützung in Windows Server veröffentlicht. Das ursprüngliche Angebot von Azure Stack HCI hat auf Windows Server 2019 basiert, und es wurde das herkömmliche Windows Server-Lizenzierungsmodell genutzt. Heute verfügt Azure Stack HCI über ein eigenes Betriebssystem und ein abonnementbasiertes Lizenzierungsmodell. Azure Stack HCI ist die empfohlene Anwendung zur lokalen Bereitstellung von HCI, indem die von Microsoft validierte Hardware unserer Partner verwendet wird.

**Kann ich ein Upgrade von Windows Server 2019 auf Azure Stack HCI durchführen?**

Derzeit gibt es kein direktes Upgrade von Windows Server auf Azure Stack HCI. Bleiben Sie in Bezug auf spezielle Migrationshinweise für Kunden mit hyperkonvergenten Clustern, die auf Windows Server 2019 und 2016 basieren, auf dem neuesten Stand.

**Für welche Azure-Dienste kann ich eine Verbindung mit Azure Stack HCI herstellen?**

Eine aktualisierte Liste mit Azure-Diensten, mit denen Sie für Azure Stack HCI eine Verbindung herstellen können, finden Sie unter [Connecting Windows Server to Azure hybrid services](/windows-server/manage/windows-admin-center/azure/index) (Verbinden von Windows Server mit Azure-Hybriddiensten).

**Welche Gemeinsamkeiten haben Azure Stack Hub- und Azure Stack HCI-Lösungen?**

Azure Stack HCI verfügt über die gleichen Hyper-V-basierten, softwaredefinierten Compute-, Speicher- und Netzwerktechnologien wie Azure Stack Hub. Beide Angebote erfüllen strenge Test- und Validierungsvorgaben, um die Zuverlässigkeit und Kompatibilität mit der zugrunde liegenden Hardwareplattform sicherzustellen.

**Inwiefern unterscheiden sie sich?**

Mit Azure Stack Hub führen Sie Clouddienste lokal aus. Sie können IaaS- und -PaaS-Dienste von Azure lokal ausführen, um Cloud-Apps unabhängig vom Standort konsistent erstellen und ausführen zu können. Die Verwaltung wird lokal über das Azure-Portal durchgeführt.

Mit Azure Stack HCI führen Sie virtualisierte Workloads lokal aus, und für die Verwaltung nutzen Sie Windows Admin Center und vertraute Windows Server-Tools. Sie können auch eine Verbindung mit Azure für Hybridszenarien herstellen, z. B. cloudbasierte Sitewiederherstellung, Überwachung und mehr.

**Kann ich ein Upgrade von Azure Stack HCI auf Azure Stack Hub durchführen?**

Nein. Kunden können Ihre Workloads aber von Azure Stack HCI zu Azure Stack Hub oder Azure migrieren.

**Wie ermittle ich einen Azure Stack HCI-Server?**

In Windows Admin Center wird das Betriebssystem in der Liste „Alle Verbindungen“ und an verschiedenen anderen Orten aufgeführt. Alternativ können Sie den folgenden PowerShell-Befehl verwenden, um den Namen und die Version des Betriebssystems abzufragen.

```PowerShell
Get-ComputerInfo -Property 'osName', 'osDisplayVersion'
```

Hier ist eine Beispielausgabe angegeben:

```
OsName                    OSDisplayVersion
------                    ----------------
Microsoft Azure Stack HCI 20H2
```
