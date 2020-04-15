---
title: Bereitstellen eines Ethereum-Blockchainnetzwerks in Azure Stack Hub
description: Tutorial mit Verwendung von benutzerdefinierten Projektmappenvorlagen zum Bereitstellen und Konfigurieren eines Ethereum-Blockchainnetzwerks in Azure Stack Hub
author: PatAltimore
ms.author: patricka
ms.date: 06/03/2019
ms.topic: tutorial
ms.reviewer: seyadava
ms.lastreviewed: 06/03/2019
ms.openlocfilehash: a0a13af01fc539788d76377f357ca28db9b05234
ms.sourcegitcommit: bdd4d529bd3e115a9f76eece62b1613448d5d020
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/14/2020
ms.locfileid: "77703076"
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

## <a name="deployment-use-cases"></a>Anwendungsfälle für die Bereitstellung

Mit der Vorlage können Leader- und Memberverknüpfungen für Ethereum-Konsortien auf verschiedene Art und Weise bereitgestellt werden. Nachfolgend werden die von uns getesteten beschrieben:

- In einer Azure Stack Hub-Instanz mit mehreren Knoten und Azure AD oder AD FS stellen Sie Leader und Member unter Verwendung des gleichen Abonnements oder verschiedener Abonnements bereit.
- In einer Azure Stack Hub-Instanz mit einem Knoten (mit Azure AD) stellen Sie Leader und Mitglieder unter Verwendung des gleichen Abonnements bereit.

### <a name="standalone-and-consortium-leader-deployment"></a>Eigenständige und Konsortium-Leaderbereitstellung

Die Konsortium-Leadervorlage konfiguriert den Speicherbedarf für das erste Member im Netzwerk. 

1. Laden Sie die [Leadervorlage von GitHub](https://aka.ms/aa6z619) herunter.
2. Wählen Sie im Azure Stack Hub-Mandantenportal **+ Ressource erstellen > Vorlagenbereitstellung** aus, um die Bereitstellung über eine benutzerdefinierte Vorlage durchzuführen.
3. Klicken Sie auf **Vorlage bearbeiten**, um die neue benutzerdefinierte Vorlage zu bearbeiten.
4. Wenn Sie sich im rechten Bearbeitungsbereich befinden, kopieren Sie die zuvor heruntergeladene Leader-JSON-Vorlagendatei, und fügen Sie sie ein.
    
    ![Bearbeiten der Leadervorlage](./media/azure-stack-ethereum/edit-leader-template.png)

5. Wählen Sie **Speichern** aus.
6. Klicken Sie auf **Parameter bearbeiten**, um die Vorlagenparameter für Ihre Bereitstellung einzugeben.
    
    ![Bearbeiten der Leadervorlagenparameter](./media/azure-stack-ethereum/edit-leader-parameters.png)

    Parametername | BESCHREIBUNG | Zulässige Werte | Beispielwert
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | Zeichenfolge, die als Grundlage für die Benennung der bereitgestellten Ressourcen verwendet wird. | Alphanumerische Zeichen (1 bis 6) | eth
    AUTHTYPE | Die Methode zur Authentifizierung des virtuellen Computers | Kennwort oder öffentlicher SSH-Schlüssel | Kennwort
    ADMINUSERNAME | Benutzername des Administrators der einzelnen bereitgestellten virtuellen Computer | 1 bis 64 Zeichen | gethadmin
    ADMINPASSWORD (Authentifizierungstyp = Kennwort)| Das Kennwort für das Administratorkonto jedes bereitgestellten virtuellen Computers. Das Kennwort muss drei der folgenden Elemente enthalten: 1 Großbuchstaben, 1 Kleinbuchstaben, 1 Ziffer und 1 Sonderzeichen. <br />Alle VMs haben zunächst dasselbe Kennwort, das nach der Bereitstellung jedoch geändert werden kann.|12 bis 72 Zeichen |
    ADMINSSHKEY (Authentifizierungstyp = sshPublicKey) | Der Secure Shell-Schlüssel für die Remoteanmeldung. | |
    GENESISBLOCK | Die JSON-Zeichenfolge, die den benutzerdefinierten Genesis-Block darstellt.  Die Angabe eines Werts für diesen Parameter ist optional. | |
    ETHEREUMACCOUNTPSSWD | Das zum Schutz des Ethereum-Kontos verwendete Administratorkennwort | |
    ETHEREUMACCOUNTPASSPHRASE | Die zum Generieren des privaten Schlüssels verwendete Passphrase, die dem Ethereum-Konto zugeordnet ist. | |
    ETHEREUMNETWORKID | Die Netzwerk-ID des Konsortiums. | Verwenden Sie einen beliebigen Wert zwischen 5 und 999.999.999. | 72
    CONSORTIUMMEMBERID | Die ID jedes Members des Konsortiumnetzwerks.   | Diese ID muss im Netzwerk eindeutig sein. | 0
    NUMMININGNODES | Anzahl von Miningknoten. | Zwischen 2 und 15. | 2
    MNNODEVMSIZE | VM-Größe der Miningknoten. | | Standard_A1
    MNSTORAGEACCOUNTTYPE | Speicherleistung der Miningknoten. | | Standard_LRS
    NUMTXNODES | Anzahl von Transaktionsknoten. | Zwischen 1 und 5. | 1
    TXNODEVMSIZE | VM-Größe der Transaktionsknoten. | | Standard_A1
    TXSTORAGEACCOUNTTYPE | Speicherleistung der Transaktionsknoten. | | Standard_LRS
    BASEURL | Basis-URL zum Abrufen der abhängigen Vorlagen | Verwenden Sie den Standardwert, sofern Sie die Bereitstellungsvorlagen nicht anpassen möchten. | 

7. Klicken Sie auf **OK**.
8. Geben Sie unter **Benutzerdefinierte Bereitstellung** **Abonnement**, **Ressourcengruppe** und **Ressourcengruppenstandort** an.
    
    ![Leaderbereitstellungsparameter](./media/azure-stack-ethereum/leader-deployment-parameters.png)

    Parametername | BESCHREIBUNG | Zulässige Werte | Beispielwert
    ---------------|-------------|----------------|-------------
    Subscription | Das Abonnement, für das das Konsortiumsnetzwerk bereitgestellt wird | | Verbrauchsabonnement
    Ressourcengruppe | Die Ressourcengruppe, für die das Konsortiumsnetzwerk bereitgestellt wird. | | EthereumResources
    Position | Die Azure-Region für die Ressourcengruppe. | | local

8. Klicken Sie auf **Erstellen**.

Die Bereitstellung kann 20 Minuten oder länger dauern.

Nachdem die Bereitstellung abgeschlossen ist, überprüfen Sie die Zusammenfassung der Bereitstellung für **Microsoft.Template** im Abschnitt zur Bereitstellung der Ressourcengruppe. Die Zusammenfassung enthält die Ausgabewerte, die zum Verknüpfen von Konsortiumsmembern verwendet werden.

Navigieren Sie zum Überprüfen der Leaderbereitstellung zur Leader-Administratorwebsite. Sie finden die Adresse der Administratorwebsite im Ausgabebereich der **Microsoft.Template**-Bereitstellung.  

![Zusammenfassung der Leaderbereitstellung](./media/azure-stack-ethereum/ethereum-node-status.png)

### <a name="joining-consortium-member-deployment"></a>Verknüpfen der Konsortiums-Memberbereitstellung

1. Laden Sie die [Konsortiumsmembervorlage von GitHub](https://aka.ms/aa6zkua) herunter.
2. Wählen Sie im Azure Stack Hub-Mandantenportal **+ Ressource erstellen > Vorlagenbereitstellung** aus, um die Bereitstellung über eine benutzerdefinierte Vorlage durchzuführen.
3. Klicken Sie auf **Vorlage bearbeiten**, um die neue benutzerdefinierte Vorlage zu bearbeiten.
4. Wenn Sie sich im rechten Bearbeitungsbereich befinden, kopieren Sie die zuvor heruntergeladene Leader-JSON-Vorlagendatei, und fügen Sie sie ein.
5. Wählen Sie **Speichern** aus.
6. Klicken Sie auf **Parameter bearbeiten**, um die Vorlagenparameter für Ihre Bereitstellung einzugeben.

    Parametername | BESCHREIBUNG | Zulässige Werte | Beispielwert
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | Zeichenfolge, die als Grundlage für die Benennung der bereitgestellten Ressourcen verwendet wird. | Alphanumerische Zeichen (1 bis 6) | eth
    AUTHTYPE | Die Methode zur Authentifizierung des virtuellen Computers | Kennwort oder öffentlicher SSH-Schlüssel | Kennwort
    ADMINUSERNAME | Benutzername des Administrators der einzelnen bereitgestellten virtuellen Computer | 1 bis 64 Zeichen | gethadmin
    ADMINPASSWORD (Authentifizierungstyp = Kennwort)| Das Kennwort für das Administratorkonto jedes bereitgestellten virtuellen Computers. Das Kennwort muss drei der folgenden Elemente enthalten: 1 Großbuchstaben, 1 Kleinbuchstaben, 1 Ziffer und 1 Sonderzeichen. <br />Alle VMs haben zunächst dasselbe Kennwort, das nach der Bereitstellung jedoch geändert werden kann.|12 bis 72 Zeichen |
    ADMINSSHKEY (Authentifizierungstyp = sshPublicKey) | Der Secure Shell-Schlüssel für die Remoteanmeldung. | |
    CONSORTIUMMEMBERID | Die ID jedes Members des Konsortiumnetzwerks.   | Diese ID muss im Netzwerk eindeutig sein. | 0
    NUMMININGNODES | Anzahl von Miningknoten. | Zwischen 2 und 15. | 2
    MNNODEVMSIZE | VM-Größe der Miningknoten. | | Standard_A1
    MNSTORAGEACCOUNTTYPE | Speicherleistung der Miningknoten. | | Standard_LRS
    NUMTXNODES | Anzahl von Transaktionsknoten. | Zwischen 1 und 5. | 1
    TXNODEVMSIZE | VM-Größe der Transaktionsknoten. | | Standard_A1
    TXSTORAGEACCOUNTTYPE | Speicherleistung der Transaktionsknoten. | | Standard_LRS
    CONSORTIUMDATA | Die URL, die auf die relevanten Konsortiumkonfigurationsdaten verweist, die durch die Bereitstellung eines anderen Mitglieds zur Verfügung gestellt werden. Der Wert befindet sich in der Bereitstellungsausgabe des Leaders. | |
    REMOTEMEMBERVNETADDRESSSPACE | Die NVA-IP-Adresse des Leaders. Der Wert befindet sich in der Bereitstellungsausgabe des Leaders. | | 
    REMOTEMEMBERNVAPUBLICIP | Die NVA-IP-Adresse des Leaders. Der Wert befindet sich in der Bereitstellungsausgabe des Leaders. | | 
    CONNECTIONSHAREDKEY | Ein vorab festgelegter, geheimer Schlüssel zwischen zwei Membern des Konsortiumsnetzwerks, zwischen denen eine Verbindung hergestellt wird. | |
    BASEURL | Basis-URL für die Vorlage. | Verwenden Sie den Standardwert, sofern Sie die Bereitstellungsvorlagen nicht anpassen möchten. | 

7. Klicken Sie auf **OK**.
8. Geben Sie unter **Benutzerdefinierte Bereitstellung** **Abonnement**, **Ressourcengruppe** und **Ressourcengruppenstandort** an.

    Parametername | BESCHREIBUNG | Zulässige Werte | Beispielwert
    ---------------|-------------|----------------|-------------
    Subscription | Das Abonnement, für das das Konsortiumsnetzwerk bereitgestellt wird | | Verbrauchsabonnement
    Ressourcengruppe | Die Ressourcengruppe, für die das Konsortiumsnetzwerk bereitgestellt wird. | | MemberResources
    Position | Die Azure-Region für die Ressourcengruppe. | | local

8. Klicken Sie auf **Erstellen**.

Die Bereitstellung kann 20 Minuten oder länger dauern.

Nachdem die Bereitstellung abgeschlossen ist, überprüfen Sie die Zusammenfassung der Bereitstellung für **Microsoft.Template** im Abschnitt zur Bereitstellung der Ressourcengruppe. Die Zusammenfassung enthält die Ausgabewerte, die zum Verknüpfen von Konsortiumsmembern verwendet werden.

Navigieren Sie zum Überprüfen der Bereitstellung des Members zu dessen Administratorsite. Sie finden die Adresse der Administratorwebsite im Ausgabebereich der **Microsoft.Template**-Bereitstellung.

![Zusammenfassung der Memberbereitstellung](./media/azure-stack-ethereum/ethereum-node-status-2.png)

Wie in der Abbildung gezeigt wird, lautet der Knotenstatus des Members **Wird nicht ausgeführt**. Dieser Status ist darauf zurückzuführen, dass die Verbindung zwischen Leader und Member nicht hergestellt wurde. Die Verbindung zwischen Member und Leader ist bidirektional. Wenn Sie das Member bereitstellen, erstellt die Vorlage automatisch die Verbindung vom Member zum Leader. Zum Erstellen der Verbindung vom Leader zum Mitglied fahren Sie mit dem nächsten Schritt fort.

### <a name="connect-member-and-leader"></a>Verbinden von Member und Leader

Mit dieser Vorlage erstellen Sie eine Verbindung vom Leader zu einem Remotemember. 

1. Laden Sie die [Vorlage zum Verbinden von Member und Leader von GitHub](https://aka.ms/aa6zdyt) herunter.
2. Wählen Sie im Azure Stack Hub-Mandantenportal **+ Ressource erstellen > Vorlagenbereitstellung** aus, um die Bereitstellung über eine benutzerdefinierte Vorlage durchzuführen.
3. Klicken Sie auf **Vorlage bearbeiten**, um die neue benutzerdefinierte Vorlage zu bearbeiten.
4. Wenn Sie sich im rechten Bearbeitungsbereich befinden, kopieren Sie die zuvor heruntergeladene Leader-JSON-Vorlagendatei, und fügen Sie sie ein.
    
    ![Bearbeiten der Verbindungsvorlage](./media/azure-stack-ethereum/edit-connect-template.png)

5. Wählen Sie **Speichern** aus.
6. Klicken Sie auf **Parameter bearbeiten**, um die Vorlagenparameter für Ihre Bereitstellung einzugeben.
    
    ![Bearbeiten der Vorlagenparameterdatei](./media/azure-stack-ethereum/edit-connect-parameters.png)

    Parametername | BESCHREIBUNG | Zulässige Werte | Beispielwert
    ---------------|-------------|----------------|-------------
    MEMBERNAMEPREFIX | Namenspräfix des Leaders. Der Wert befindet sich in der Bereitstellungsausgabe des Leaders.  | Alphanumerische Zeichen (1 bis 6) | |
    MEMBERROUTETABLENAME | Name der Routentabelle des Leaders. Der Wert befindet sich in der Bereitstellungsausgabe des Leaders. |  | 
    REMOTEMEMBERVNETADDRESSSPACE | Adressraum des Members. Dieser Wert befindet sich in der Bereitstellungsausgabe des Members. | |
    CONNECTIONSHAREDKEY | Ein vorab festgelegter, geheimer Schlüssel zwischen zwei Membern des Konsortiumsnetzwerks, zwischen denen eine Verbindung hergestellt wird.  | |
    REMOTEMEMBERNVAPUBLICIP | Die NVA-IP-Adresse des Members. Dieser Wert befindet sich in der Bereitstellungsausgabe des Members. | |
    MEMBERNVAPRIVATEIP | Die private NVA-IP-Adresse des Leaders. Der Wert befindet sich in der Bereitstellungsausgabe des Leaders. | |
    LOCATION | Standort Ihrer Azure Stack Hub-Umgebung | | local
    BASEURL | Basis-URL für die Vorlage. | Verwenden Sie den Standardwert, sofern Sie die Bereitstellungsvorlagen nicht anpassen möchten. | 

7. Klicken Sie auf **OK**.
8. Geben Sie unter **Benutzerdefinierte Bereitstellung** **Abonnement**, **Ressourcengruppe** und **Ressourcengruppenstandort** an.
    
    ![Verbinden der Bereitstellungsparameter](./media/azure-stack-ethereum/connect-deployment-parameters.png)

    Parametername | BESCHREIBUNG | Zulässige Werte | Beispielwert
    ---------------|-------------|----------------|-------------
    Subscription | Das Abonnement des Leaders. | | Verbrauchsabonnement
    Ressourcengruppe | Ressourcengruppe des Leaders. | | EthereumResources
    Position | Die Azure-Region für die Ressourcengruppe. | | local

8. Klicken Sie auf **Erstellen**.

Nachdem die Bereitstellung abgeschlossen ist, dauert es einige Minuten, bis Leader und Member die Kommunikation starten. Aktualisieren Sie zum Überprüfen der Bereitstellung die Administratorsite des Members. Der Status der Knoten des Members muss „Wird ausgeführt“ lauten.

![Überprüfen der Bereitstellung](./media/azure-stack-ethereum/ethererum-node-status-3.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Ethereum und Azure finden Sie unter [Blockchain](https://azure.microsoft.com/solutions/blockchain/).
