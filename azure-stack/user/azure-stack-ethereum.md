---
title: Bereitstellen eines Ethereum-Blockchainnetzwerks in Azure Stack Hub
description: Tutorial mit Verwendung von benutzerdefinierten Projektmappenvorlagen zum Bereitstellen und Konfigurieren eines Ethereum-Blockchainnetzwerks in Azure Stack Hub
author: PatAltimore
ms.author: patricka
ms.date: 10/07/2020
ms.topic: tutorial
ms.reviewer: seyadava
ms.lastreviewed: 10/07/2020
ms.openlocfilehash: f409a2629243c0eb1cfe15ad857aa3e229832bb5
ms.sourcegitcommit: 7b189e5317b8fe5f8ad825565da3607a39a1b899
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94493702"
---
# <a name="deploy-an-ethereum-blockchain-network-on-azure-stack-hub"></a>Bereitstellen eines Ethereum-Blockchainnetzwerks in Azure Stack Hub

Mit der Ethereum-Projektmappenvorlage lässt sich ein Multimember-Blockchainnetzwerk für Konsortien von Ethereum mit minimalen Kenntnissen im Bereich Azure und Ethereum schneller und einfacher bereitstellen und konfigurieren.

Mit einigen wenigen Benutzereingaben und einer Bereitstellung mit nur einem Klick über das Azure Stack Hub-Mandantenportal kann jedes Mitglied seinen Netzwerkfußabdruck bereitstellen. Der Netzerkfußabdruck jedes Mitglieds umfasst drei Dinge:

1. Eine Reihe von Transaktionsknoten mit Lastenausgleich, über die eine Anwendung oder ein Benutzer interaktiv Transaktionen übermitteln kann
2. Eine Reihe von Miningknoten zum Aufzeichnen von Transaktionen
3. Ein virtuelles Netzwerkgerät (Network Virtual Appliance, NVA)

Über einen später ausgeführten Verbindungsschritt werden die virtuellen Netzwerkgeräte verbunden, sodass ein vollständig konfiguriertes Multimember-Blockchainnetzwerk erstellt wird.

Einrichtung:

- Auswählen einer Bereitstellungsarchitektur
- Bereitstellen eines eigenständigen Netzwerks oder eines Netzwerks vom Typ „Konsortium-Leader“ oder „Konsortiumsmitglied“

## <a name="prerequisites"></a>Voraussetzungen

Laden Sie die aktuellen Komponenten vom [Marketplace](../operator/azure-stack-download-azure-marketplace-item.md) herunter:

- Ubuntu Server 16.04 LTS
- Windows Server 2016
- Custom Script for Linux 2.0
- CustomScript-Erweiterung für Windows

Weitere Informationen zu Blockchainszenarien finden Sie unter [Projektmappenvorlage für „Ethereum-PoA-Konsortium“](/azure/blockchain/templates/ethereum-poa-deployment).

## <a name="deployment-architecture"></a>Bereitstellungsarchitektur

Mit dieser Projektmappenvorlage können Single- oder Multimembernetzwerke für Ethereum-Konsortien bereitgestellt werden. Das virtuelle Netzwerk wird in einer Kettentopologie mithilfe von Ressourcen von virtuellen Netzwerkappliances und von Verbindungsressourcen verbunden.

Mit der Vorlage können Leader- und Memberverknüpfungen für Ethereum-Konsortien auf verschiedene Art und Weise bereitgestellt werden. Nachfolgend werden die von uns getesteten beschrieben:

- In einer Azure Stack Hub-Instanz mit mehreren Knoten und Azure AD oder AD FS stellen Sie Leader und Member unter Verwendung des gleichen Abonnements oder verschiedener Abonnements bereit.
- In einer Azure Stack Hub-Instanz mit einem Knoten (mit Azure AD) stellen Sie Leader und Mitglieder unter Verwendung des gleichen Abonnements bereit.

## <a name="standalone-and-consortium-leader-deployment"></a>Eigenständige und Konsortium-Leaderbereitstellung

Die Konsortium-Leadervorlage konfiguriert den Speicherbedarf für das erste Member im Netzwerk. 

1. Laden Sie die [Leadervorlage von GitHub](https://aka.ms/aa6z619) herunter.
1. Wählen Sie im Azure Stack Hub-Mandantenportal **+ Ressource erstellen > Vorlagenbereitstellung** aus, um die Bereitstellung über eine benutzerdefinierte Vorlage durchzuführen.
1. Wählen Sie unter **Build your own template in the editor** (Eigene Vorlage im Editor erstellen) aus, um die neue benutzerdefinierte Vorlage zu bearbeiten.
1. Wenn Sie sich im rechten Bearbeitungsbereich befinden, kopieren Sie die zuvor heruntergeladene Leader-JSON-Vorlagendatei, und fügen Sie sie ein.
    
    [![Bearbeiten der Vorlage mit eingefügter Leadervorlage](./media/azure-stack-ethereum/edit-leader-template.png)](./media/azure-stack-ethereum/edit-leader-template.png#lightbox)

1. Klicken Sie auf **Speichern**.
1. Füllen Sie auf der Registerkarte **Grundlegende Einstellungen** die folgenden Einstellungen aus:

    Parametername | BESCHREIBUNG | Beispielwert
    ---------------|-------------|-------------
    Subscription | Das Abonnement, für das das Konsortiumsnetzwerk bereitgestellt wird | Verbrauchsabonnement
    Ressourcengruppe | Die Ressourcengruppe, für die das Konsortiumsnetzwerk bereitgestellt wird. | EthereumResources
    Region | Die Azure-Region für Ressourcen | local
    Namenspräfix | Zeichenfolge, die als Grundlage für die Benennung der bereitgestellten Ressourcen verwendet wird. Verwenden Sie maximal sechs alphanumerische Zeichen. | eth
    Authentifizierungstyp | Die Methode zur Authentifizierung des virtuellen Computers Zulässige Werte sind das Kennwort oder der öffentliche SSH-Schlüssel. | Kennwort
    Administratorbenutzername | Benutzername des Administrators der einzelnen bereitgestellten virtuellen Computer Verwenden Sie 1 bis 64 Zeichen. | gethadmin
    Administratorkennwort (Authentifizierungstyp = Kennwort)| Das Kennwort für das Administratorkonto jedes bereitgestellten virtuellen Computers. Das Kennwort muss drei der folgenden Elemente enthalten: 1 Großbuchstaben, 1 Kleinbuchstaben, 1 Ziffer und 1 Sonderzeichen. <br />Alle VMs haben zunächst dasselbe Kennwort, das nach der Bereitstellung jedoch geändert werden kann. Verwenden Sie 12 bis 72 Zeichen. |
    SSH-Administratorschlüssel (Authentifizierungstyp = sshPublicKey) | Die für die Remoteanmeldung verwendete Zeichenfolge für den öffentlichen SSH RSA-Schlüssel |
    Genesis-Block | Die JSON-Zeichenfolge, die den benutzerdefinierten Genesis-Block darstellt.  Die Angabe eines Werts für diesen Parameter ist optional. |
    Ethereum-Kontokennwort | Das zum Schutz des Ethereum-Kontos verwendete Administratorkennwort |
    Ethereum-Kontopassphrase | Die zum Generieren des privaten Schlüssels verwendete Passphrase, die dem Ethereum-Konto zugeordnet ist. Verwenden Sie ein Kennwort mit ausreichender Zufälligkeit, um einen sicheren privaten Schlüssel sicherzustellen. |
    Ethereum-Netzwerk-ID | Die Netzwerk-ID des Konsortiums. Verwenden Sie einen beliebigen Wert zwischen 5 und 999.999.999. | 72
    ID des Konsortiumsmembers | Die ID jedes Members des Konsortiumnetzwerks. Diese ID muss im Netzwerk eindeutig sein. | 0
    Anzahl von Miningknoten | Anzahl von Miningknoten für die einzelnen Konsortiumsmember. Verwenden Sie einen Wert zwischen 2 und 15. | 2
    VM-Größe der Miningknoten | VM-Größe der Miningknoten. | Standard_A1
    Typ des Miningspeicherkontos | Speicherleistung der Miningknoten. | Standard_LRS
    Anzahl von TX-Knoten | Anzahl der Transaktionsknoten mit Lastenausgleich. Verwenden Sie einen Wert zwischen 1 und 5. | 1
    VM-Größe der TX-Knoten | VM-Größe der Transaktionsknoten. | Standard_A1
    TX-Speicherkontotyp | Speicherleistung der Transaktionsknoten. | Standard_LRS
    Basis-URL | Basis-URL zum Abrufen der Bereitstellungsvorlagen. Verwenden Sie den Standardwert, sofern Sie die Bereitstellungsvorlagen nicht anpassen möchten. |

1. Klicken Sie auf **Überprüfen + erstellen**. Wählen Sie nach erfolgreicher Überprüfung **Erstellen** aus.

Die Bereitstellung kann 20 Minuten oder länger dauern.

Nachdem die Bereitstellung abgeschlossen ist, überprüfen Sie die Zusammenfassung der Bereitstellung für **Microsoft.Template** im Abschnitt zur Bereitstellung der Ressourcengruppe. Die Zusammenfassung enthält die Ausgabewerte, die zum Verknüpfen von Konsortiumsmembern verwendet werden.

Navigieren Sie zum Überprüfen der Leaderbereitstellung zur Leader-Administratorwebsite. Sie finden die Adresse der Administratorwebsite im Ausgabebereich der **Microsoft.Template** -Bereitstellung.  

![Zusammenfassung der Leaderbereitstellung](./media/azure-stack-ethereum/ethereum-node-status.png)

## <a name="joining-consortium-member-deployment"></a>Verknüpfen der Konsortiums-Memberbereitstellung

1. Laden Sie die [Konsortiumsmembervorlage von GitHub](https://aka.ms/aa6zkua) herunter.
1. Wählen Sie im Azure Stack Hub-Mandantenportal **+ Ressource erstellen > Vorlagenbereitstellung** aus, um die Bereitstellung über eine benutzerdefinierte Vorlage durchzuführen.
1. Wählen Sie unter **Build your own template in the editor** (Eigene Vorlage im Editor erstellen) aus, um die neue benutzerdefinierte Vorlage zu bearbeiten.
1. Wenn Sie sich im rechten Bearbeitungsbereich befinden, kopieren Sie die zuvor heruntergeladene JSON-Vorlagendatei für Konsortiumsmitglieder, und fügen Sie sie ein.
1. Klicken Sie auf **Speichern**.
1. Füllen Sie auf der Registerkarte **Grundlegende Einstellungen** die folgenden Einstellungen aus:

    Parametername | BESCHREIBUNG | Beispielwert
    ---------------|-------------|-------------
    Subscription | Das Abonnement, für das das Konsortiumsnetzwerk bereitgestellt wird | Verbrauchsabonnement
    Ressourcengruppe | Die Ressourcengruppe, für die das Konsortiumsnetzwerk bereitgestellt wird. | EthereumResources
    Region | Die Azure-Region für Ressourcen | local
    Namenspräfix | Zeichenfolge, die als Grundlage für die Benennung der bereitgestellten Ressourcen verwendet wird. Verwenden Sie maximal sechs alphanumerische Zeichen. | eth
    Authentifizierungstyp | Die Methode zur Authentifizierung des virtuellen Computers Zulässige Werte sind das Kennwort oder der öffentliche SSH-Schlüssel. | Kennwort
    Administratorbenutzername | Benutzername des Administrators der einzelnen bereitgestellten virtuellen Computer Verwenden Sie 1 bis 64 Zeichen. | gethadmin
    Administratorkennwort (Authentifizierungstyp = Kennwort)| Das Kennwort für das Administratorkonto jedes bereitgestellten virtuellen Computers. Das Kennwort muss drei der folgenden Elemente enthalten: 1 Großbuchstaben, 1 Kleinbuchstaben, 1 Ziffer und 1 Sonderzeichen. <br />Alle VMs haben zunächst dasselbe Kennwort, das nach der Bereitstellung jedoch geändert werden kann. Verwenden Sie 12 bis 72 Zeichen. |
    SSH-Administratorschlüssel (Authentifizierungstyp = sshPublicKey) | Die für die Remoteanmeldung verwendete Zeichenfolge für den öffentlichen SSH RSA-Schlüssel |
    Genesis-Block | Die JSON-Zeichenfolge, die den benutzerdefinierten Genesis-Block darstellt. Die Angabe eines Werts für diesen Parameter ist optional. |
    Ethereum-Kontokennwort | Das zum Schutz des Ethereum-Kontos verwendete Administratorkennwort |
    Ethereum-Kontopassphrase | Die zum Generieren des privaten Schlüssels verwendete Passphrase, die dem Ethereum-Konto zugeordnet ist. Verwenden Sie ein Kennwort mit ausreichender Zufälligkeit, um einen sicheren privaten Schlüssel sicherzustellen. |
    ID des Konsortiumsmembers | Die ID jedes Members des Konsortiumnetzwerks. Diese ID muss im Netzwerk eindeutig sein. | 0
    Anzahl von Miningknoten | Anzahl von Miningknoten für die einzelnen Konsortiumsmember. Verwenden Sie einen Wert zwischen 2 und 15. | 2
    VM-Größe der Miningknoten | VM-Größe der Miningknoten. | Standard_A1
    Typ des Miningspeicherkontos | Speicherleistung der Miningknoten. | Standard_LRS
    Anzahl von TX-Knoten | Anzahl der Transaktionsknoten mit Lastenausgleich. Verwenden Sie einen Wert zwischen 1 und 5. | 1
    VM-Größe der TX-Knoten | VM-Größe der Transaktionsknoten. | Standard_A1
    TX-Speicherkontotyp | Speicherleistung der Transaktionsknoten. | Standard_LRS
    Konsortiumsdaten | Die URL, die auf die relevanten Konsortiumkonfigurationsdaten verweist, die durch die Bereitstellung eines anderen Mitglieds zur Verfügung gestellt werden. Der Wert befindet sich in der Bereitstellungsausgabe des Leaders. |
    VNET-Adressraum für Remotemember | Der VNET-Adressraum des Leaders. Der Wert befindet sich in der Bereitstellungsausgabe des Leaders. |
    Öffentliche NVA-IP-Adresse für Remotemember | Die NVA-IP-Adresse des Leaders. Der Wert befindet sich in der Bereitstellungsausgabe des Leaders. |
    Gemeinsam verwendeter Verbindungsschlüssel | Ein vorab festgelegtes Geheimnis zwischen zwei Membern des Konsortiumsnetzwerks, zwischen denen eine Gatewayverbindung hergestellt wird. |
    Basis-URL | Basis-URL zum Abrufen der Bereitstellungsvorlagen. Verwenden Sie den Standardwert, sofern Sie die Bereitstellungsvorlagen nicht anpassen möchten. |
1. Klicken Sie auf **Überprüfen + erstellen**. Wählen Sie nach erfolgreicher Überprüfung **Erstellen** aus.

Die Bereitstellung kann 20 Minuten oder länger dauern.

Nachdem die Bereitstellung abgeschlossen ist, überprüfen Sie die Zusammenfassung der Bereitstellung für **Microsoft.Template** im Abschnitt zur Bereitstellung der Ressourcengruppe. Die Zusammenfassung enthält die Ausgabewerte, die zum Verknüpfen von Konsortiumsmembern verwendet werden.

Navigieren Sie zum Überprüfen der Bereitstellung des Members zu dessen Administratorsite. Sie finden die Adresse der Administratorwebsite im Ausgabebereich der **Microsoft.Template** -Bereitstellung.

![Zusammenfassung der Memberbereitstellung](./media/azure-stack-ethereum/ethereum-node-status-2.png)

Wie in der Abbildung gezeigt wird, lautet der Knotenstatus des Members **Wird nicht ausgeführt**. Dieser Status ist darauf zurückzuführen, dass die Verbindung zwischen Leader und Member nicht hergestellt wurde. Die Verbindung zwischen Member und Leader ist bidirektional. Wenn Sie das Member bereitstellen, erstellt die Vorlage automatisch die Verbindung vom Member zum Leader. Zum Erstellen der Verbindung vom Leader zum Mitglied fahren Sie mit dem nächsten Schritt fort.

## <a name="connect-member-and-leader"></a>Verbinden von Member und Leader

Mit dieser Vorlage erstellen Sie eine Verbindung vom Leader zu einem Remotemember. 

1. Laden Sie die [Vorlage zum Verbinden von Member und Leader von GitHub](https://aka.ms/aa6zdyt) herunter.
1. Wählen Sie im Azure Stack Hub-Mandantenportal **+ Ressource erstellen > Vorlagenbereitstellung** aus, um die Bereitstellung über eine benutzerdefinierte Vorlage durchzuführen.
1. Wählen Sie unter **Build your own template in the editor** (Eigene Vorlage im Editor erstellen) aus, um die neue benutzerdefinierte Vorlage zu bearbeiten.
1. Wenn Sie sich im rechten Bearbeitungsbereich befinden, kopieren Sie die zuvor heruntergeladene JSON-Vorlagendatei für Konsortiumsmitglieder, und fügen Sie sie ein.
1. Klicken Sie auf **Speichern**.
1. Legen Sie die folgenden Einstellungen fest:

    Parametername | BESCHREIBUNG | Beispielwert
    ---------------|-------------|-------------
    Subscription | Das Abonnement, für das das Konsortiumsnetzwerk bereitgestellt wird | Verbrauchsabonnement
    Ressourcengruppe | Die Ressourcengruppe, für die das Konsortiumsnetzwerk bereitgestellt wird. | EthereumResources
    Region | Die Azure-Region für Ressourcen | local
    Membernamenspräfix | Zeichenfolge, die als Grundlage für die Benennung der bereitgestellten Ressourcen verwendet wird. Verwenden Sie maximal sechs alphanumerische Zeichen. | eth
    Name der Routingtabelle des Members | Name der Routentabelle des Leaders. Der Wert befindet sich in der Bereitstellungsausgabe des Leaders. |
    VNET-Adressraum für Remotemember | Adressraum des Members. Dieser Wert befindet sich in der Bereitstellungsausgabe des Members. |
    Öffentliche NVA-IP-Adresse für Remotemember | Die NVA-IP-Adresse für die Verbindung. Dieser Wert befindet sich in der Bereitstellungsausgabe des Members. |
    Gemeinsam verwendeter Verbindungsschlüssel | Ein vorab festgelegter, geheimer Schlüssel zwischen zwei Membern des Konsortiumsnetzwerks, zwischen denen eine Verbindung hergestellt wird.  |
    Private NVA-IP-Adresse des Members | Die NVA-IP-Adresse des Members. Dieser Wert befindet sich in der Bereitstellungsausgabe des Members. |
    Basis-URL | Basis-URL zum Abrufen der Bereitstellungsvorlagen. Verwenden Sie den Standardwert, sofern Sie die Bereitstellungsvorlagen nicht anpassen möchten. |
1. Klicken Sie auf **Überprüfen + erstellen**. Wählen Sie nach erfolgreicher Überprüfung **Erstellen** aus.

Nachdem die Bereitstellung abgeschlossen ist, dauert es einige Minuten, bis Leader und Member die Kommunikation starten. Aktualisieren Sie zum Überprüfen der Bereitstellung die Administratorsite des Members. Der Status der Knoten des Members muss „Wird ausgeführt“ lauten.

![Überprüfen der Bereitstellung](./media/azure-stack-ethereum/ethererum-node-status-3.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Ethereum und Azure finden Sie unter [Blockchain](https://azure.microsoft.com/solutions/blockchain/).
