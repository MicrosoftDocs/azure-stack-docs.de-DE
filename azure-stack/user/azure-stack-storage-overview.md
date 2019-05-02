---
title: Einführung zu Azure Stack-Speicher
description: Informationen zu Azure Stack-Speicher
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: 092aba28-04bc-44c0-90e1-e79d82f4ff42
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 10cb9a2b1449c8cb280bf4787b009b6d2eb21619
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "64310752"
---
# <a name="introduction-to-azure-stack-storage"></a>Einführung zu Azure Stack-Speicher

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

## <a name="overview"></a>Übersicht

Azure Stack-Speicher ist ein Satz von Cloudspeicherdiensten, einschließlich Diensten für Blobs, Tabellen und Warteschlangen, die mit Azure Storage-Diensten übereinstimmen.

## <a name="azure-stack-storage-services"></a>Azure Stack-Speicherdienste

Azure Stack-Speicher bietet die folgenden drei Dienste:

- **Blob Storage**

    Blob Storage dient zum Speichern unstrukturierter Objektdaten. Bei einem Blob kann es sich um einen beliebigen Text- oder Binärdatentyp handeln – also beispielsweise um ein Dokument, eine Mediendatei oder einen Anwendungs-Installer.

- **Table Storage**

    Table Storage speichert strukturierte Datasets. Table Storage ist ein Schlüssel-/Attribut-basierter NoSQL-Datenspeicher, der eine rasche Entwicklung und schnellen Zugriff auf große Datenmengen erlaubt.

- **Queue Storage**

    Queue Storage bietet zuverlässiges Messaging für die Workflowverarbeitung und für die Kommunikation zwischen Komponenten von Clouddiensten.

Ein Azure Stack-Speicherkonto ist ein sicheres Konto, mit dem Sie auf Dienste in Azure Stack-Speicher zugreifen können. Ihr Speicherkonto stellt den eindeutigen Namespace für Ihre Speicherressourcen bereit. Das folgende Diagramm zeigt die Beziehungen zwischen den Azure Stack-Speicherressourcen in einem Speicherkonto:

![Übersicht zu Azure Stack-Speicher](media/azure-stack-storage-overview/AzureStackStorageOverview.png)

### <a name="blob-storage"></a>Blob Storage

Blob Storage ist eine effektive und skalierbare Lösung für Benutzer, die große Mengen an unstrukturierten Objektdaten in der Cloud speichern möchten. Mit Blob Storage lassen sich unter anderem folgende Inhalte speichern:

- Dokumente
- Daten aus sozialen Netzwerken (etwa Fotos, Videos, Musik und Blogs)
- Sicherungskopien von Dateien, Computern, Datenbanken und Geräten
- Bilder und Texte für Webanwendungen
- Konfigurationsdaten für Cloudanwendungen
- Big Data wie Protokolle und andere umfangreiche Datasets

Jedes Blob ist in einem Container organisiert. Container sind auch eine praktische Möglichkeit, um Gruppen von Objekten Sicherheitsrichtlinien zuzuordnen. Ein Speicherkonto kann eine beliebige Anzahl von Containern enthalten, und ein Container kann eine beliebige Anzahl von Blobs enthalten, bis die Kapazität des Speicherkontos erreicht ist.

Blob Storage bietet drei Typen von Blobs:

- **Blockblobs**

    Blockblobs sind für das Streamen und Speichern von Cloudobjekten optimiert und eine gute Wahl für die Speicherung von Dokumenten, Mediendateien, Sicherungskopien und Ähnlichem.

- **Anfügeblobs**

    Anfügeblobs ähneln Blockblobs, sind aber für Anfügevorgänge optimiert. Zum Aktualisieren eines Anfügeblobs muss an dessen Ende ein neuer Block hinzugefügt werden. Somit eignen sich Anfügeblobs sehr gut für Szenarien wie die Protokollierung, da hier neue Daten nur am Ende des Blobs geschrieben werden müssen.

- **Seitenblobs**

    Seitenblobs sind für die Darstellung von IaaS-Festplatten und für die Unterstützung zufallsgesteuerter Schreibzugriffe optimiert, die bis zu 1 TB groß sein können. Bei einer mit dem virtuellen Azure Stack-Computer verbundenen IaaS-Festplatte handelt es sich um eine als Seitenblob gespeicherte virtuelle Festplatte.

### <a name="table-storage"></a>Table Storage

Moderne Anwendungen sind im Vergleich zu früheren Softwaregenerationen häufig auf Datenspeicher mit höherer Skalierbarkeit und Flexibilität angewiesen. Table Storage bietet eine hoch verfügbare und äußerst flexibel skalierbare Speicherung, sodass sich Ihre Anwendung automatisch auf die Anforderungen der Benutzer einstellen kann. Table Storage ist ein NoSQL-Schlüssel-/Attributspeicher von Microsoft, der sich durch sein schemaloses Design von herkömmlichen relationalen Datenbanken unterscheidet. Mit einem schemalosen Datenspeicher können Sie Ihre Daten problemlos an die sich verändernden Anforderungen Ihrer Anwendung anpassen. Die hohe Benutzerfreundlichkeit von Table Storage ermöglicht Entwicklern eine rasche Anwendungserstellung.

Bei Table Storage handelt es sich um einen Schlüssel-/Attribut-basierten Speicher. Das bedeutet, dass jeder Wert in einer Tabelle mit einem typisierten Eigenschaftennamen gespeichert wird. Der Eigenschaftenname kann für Filter und Auswahlkriterien verwendet werden. Eine Sammlung mit Eigenschaften und deren Werte bilden eine Entität. Da Tabellenspeicher schemalos ist, können zwei Entitäten in der gleichen Tabelle unterschiedliche Sammlungen von Eigenschaften enthalten, und diese Eigenschaften können jeweils einen anderen Typ haben.

Mit Tabellenspeicher können Sie flexible Datasets wie Benutzerdaten für Webanwendungen, Adressbücher, Geräteinformationen und jegliche Art von Metadaten speichern, die Ihr Dienst erfordert. Für moderne internetbasierte Anwendungen stellen NoSQL-Datenbanken wie Tabellenspeicher eine beliebte Alternative zu herkömmlichen relationalen Datenbanken dar.

Ein Speicherkonto kann eine beliebige Anzahl von Tabellen enthalten, und eine Tabelle kann eine beliebige Anzahl von Entitäten enthalten – bis zur Speicherkapazitätsgrenze des Speicherkontos.

### <a name="queue-storage"></a>Queue Storage

Bei der Entwicklung skalierbarer Anwendungen werden häufig einzelne Anwendungskomponenten entkoppelt, um eine unabhängige Skalierung zu ermöglichen. Queue Storage bietet eine zuverlässige Messaginglösung für die asynchrone Kommunikation zwischen Anwendungskomponenten – ganz gleich, ob diese in der Cloud, auf dem Desktop, auf einem lokalen Server oder auf einem mobilen Gerät ausgeführt werden. Queue Storage unterstützt auch die Verwaltung asynchroner Aufgaben und den Aufbau von Prozessworkflows.

Ein Speicherkonto kann eine beliebige Anzahl von Warteschlangen enthalten, und eine Warteschlange kann eine beliebige Anzahl von Nachrichten enthalten – bis zur Speicherkapazitätsgrenze des Speicherkontos. Einzelne Nachrichten können bis zu 64 KB groß sein.

## <a name="next-steps"></a>Nächste Schritte

- [Azure Stack-Speicher: Unterschiede und Überlegungen](azure-stack-acs-differences.md)

- Weitere Informationen zu Azure Storage finden Sie unter [Einführung in Microsoft Azure Storage](/azure/storage/common/storage-introduction).
