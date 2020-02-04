---
title: Replizieren von Ressourcen in mehreren Azure Stack Hub-Abonnements
description: Erfahren Sie, wie Sie Ressourcen mit den Replikatorskripts für Azure Stack Hub-Abonnements replizieren.
author: mattbriggs
ms.topic: how-to
ms.date: 11/07/2019
ms.author: mabrigg
ms.reviewer: rtiberiu
ms.lastreviewed: 11/07/2019
ms.openlocfilehash: 095de73dea95a4ed3dec04d43e70c1a0554906ca
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884621"
---
# <a name="replicate-resources-using-the-azure-stack-hub-subscription-replicator"></a>Replizieren von Ressourcen mit dem Replikator für Azure Stack Hub-Abonnements

Sie können mit dem PowerShell-Skript des Replikators für Azure Stack Hub-Abonnements die Ressourcen zwischen Azure Stack Hub-Abonnements, Azure Stack Hub-Stamps oder Azure Stack Hub und Azure kopieren. Das Replikatorskript liest und erstellt die Azure Resource Manager-Ressourcen aus anderen Azure- und Azure Stack Hub-Abonnements neu. In diesem Artikel wird die Funktionsweise des Skripts erläutert. Sie erfahren, wie Sie das Skript verwenden können. Außerdem finden Sie eine Referenz für Skriptvorgänge.

Die in diesem Artikel verwendeten Skripts finden Sie im GitHub-Repository [Azure Intelligent Edge Patterns](https://github.com/Azure-Samples/azure-intelligent-edge-patterns). Die Skripts befinden sich im Ordner [Abonnementreplikator](https://github.com/Azure-Samples/azure-intelligent-edge-patterns/tree/master/subscription%20replicator).

## <a name="subscription-replicator-overview"></a>Übersicht über den Abonnementreplikator

Der Azure-Abonnementreplikator ist modular aufgebaut. Dieses Tool verwendet einen Core-Prozessor, der die Replikation von Ressourcen orchestriert. Außerdem unterstützt das Tool anpassbare Prozessoren, die als Vorlagen für das Kopieren unterschiedlicher Ressourcentypen fungieren. 

Der Core-Prozessor setzt sich aus den folgenden drei Skripts zusammen:

- **resource_retriever.ps1**

    - Generiert Ordner zum Speichern von Ausgabedateien.

    - Legt den Kontext auf das Quellabonnement fest.

    - Ruft die Ressourcen ab und übergibt sie an **resource_processor.ps1**.

- **resource_processor.ps1**

    - Verarbeitet die von **resource_retriever.ps1** übergebene Ressource.

    - Bestimmt den zu verwendenden angepassten Prozessor und übergibt die Ressourcen.

- **post_process.ps1**

    - Führt die Nachbearbeitung der vom angepassten Prozessor generierten Ausgabe aus, um deren Bereitstellung im Zielabonnement vorzubereiten.

    - Generiert den Bereitstellungscode zum Bereitstellen der Ressourcen im Zielabonnement.

Die drei Skripts steuern den Informationsfluss auf standardmäßige Weise, sodass größere Flexibilität gegeben ist. Wenn Sie z. B. Unterstützung für zusätzliche Ressourcen hinzufügen, müssen Sie dazu den Code im Core-Prozessor nicht bearbeiten.

Die oben erwähnten angepassten Prozessoren sind `ps1`-Dateien, die vorgeben, wie bestimmte Ressourcentypen verarbeitet werden müssen. Ein angepasster Prozessor wird immer entsprechend den Typdaten in einer Ressource benannt. Wenn `$vm` beispielsweise ein VM-Objekt enthält, wird durch Ausführen von `$vm`.Type `Microsoft.Compute/virtualMachines` erhalten. Das heißt, ein Prozessor für eine VM wird `virtualMachines_processor.ps1` benannt. Der Name muss exakt wie in den Ressourcenmetadaten lauten, da der Core-Prozessor auf dieser Grundlage bestimmt, welcher angepasste Prozessor zu verwenden ist.

Ein angepasster Prozessor schreibt vor, wie eine Ressource zu replizieren ist. Dazu bestimmt er die relevanten Informationen und legt fest, wie diese Informationen aus den Ressourcenmetadaten abgerufen werden. Der angepasste Prozessor empfängt die extrahierten Daten und generiert anhand der Daten eine Parameterdatei, die zusammen mit einer Azure Resource Manager-Vorlage verwendet wird, um die Ressource im Zielabonnement bereitzustellen. Diese Parameterdatei wird nach der Nachbearbeitung durch „post_process.ps1“ in **Parameter_Files** gespeichert.

In der Dateistruktur des Replikators ist ein Ordner namens **Standardized_ARM_Templates** vorhanden. Je nach Quellumgebung verwenden die Bereitstellungen eine dieser standardisierten Azure Resource Manager-Vorlagen, oder eine angepasste Azure Resource Manager-Vorlage muss generiert werden. In diesem Fall muss ein angepasster Prozessor einen Azure Resource Manager-Vorlagengenerator aufrufen. Im obigen Beispiel würde der Name eines Azure Resource Manager-Vorlagengenerators **virtualMachines_ARM_Template_Generator.ps1** lauten. Der Azure Resource Manager-Vorlagengenerator erstellt eine angepasste Azure Resource Manager-Vorlage anhand der Informationen in den Metadaten einer Ressource. Wenn die Metadaten der VM-Ressource beispielsweise angeben, dass die VM-Ressource Mitglied einer Verfügbarkeitsgruppe ist, erstellt der Azure Resource Manager-Vorlagengenerator eine Azure Resource Manager-Vorlage mit Code, der die ID der Verfügbarkeitsgruppe angibt, zu der die VM gehört. Auf diese Weise wird die VM bei der Bereitstellung im neuen Abonnement automatisch der Verfügbarkeitsgruppe hinzugefügt. Diese angepassten Azure Resource Manager-Vorlagen werden im Ordner **Custom_ARM_Templates** innerhalb des Ordners **Standardized_ARM_Templates** gespeichert. Das Skript „post_processor.ps1“ bestimmt, ob eine Bereitstellung eine standardisierte oder eine angepasste Azure Resource Manager-Vorlage verwendet und generiert den entsprechenden Bereitstellungscode.

Das Skript **post-process.ps1** bereinigt die Parameterdateien und erstellt die Skripts, mit denen der Benutzer die neuen Ressourcen bereitstellt. Während der Bereinigungsphase ersetzt das Skript alle Verweise auf die D, die Mandanten-ID und den Ort des Quellabonnements durch die entsprechenden Zielwerte. Anschließend gibt es die Parameterdatei in den Ordner **Parameter_Files** aus. Dann wird bestimmt, ob die verarbeitete Ressource eine angepasste Azure Resource Manager-Vorlage verwendet, und der entsprechende Bereitstellungscode wird generiert, wobei das Cmdlet **New-AzureRmResourceGroupDeployment** verwendet wird. Der Bereitstellungscode wird der Datei **DeployResources.ps1** im Ordner **Deployment_Files** hinzugefügt. Schließlich bestimmt das Skript die Ressourcengruppe, zu der die Ressource gehört, und überprüft das Skript **DeployResourceGroups.ps1**, um festzustellen, ob der Bereitstellungscode zum Bereitstellen der Ressourcengruppe bereits vorhanden ist. Ist dies nicht der Fall, fügt es dem betreffenden Skript Code zum Bereitstellen der Ressourcengruppe hinzu. Andernfalls wird kein Vorgang ausgeführt.

### <a name="dynamic-api-retrieval"></a>Dynamischer API-Abruf

Der dynamische API-Abruf ist in das Tool integriert, sodass die neueste verfügbare Ressourcenanbieter-API-Version im Quellabonnement verwendet wird, um die Ressourcen im Zielabonnement bereitzustellen:

![Abbildung: API-Abruf](./media/azure-stack-network-howto-backup-replicator/image1.png)

Abbildung: API-Abruf in **resource_processor.ps1**.

Möglicherweise ist jedoch die Ressourcenanbieter-API-Version des Zielabonnements älter als die des Quellabonnements, und sie unterstützt daher nicht die vom Quellabonnement bereitgestellte Version. In diesem Fall wird beim Ausführen der Bereitstellung ein Fehler ausgelöst. Beheben Sie dieses Problem, indem Sie die Ressourcenanbieter im Zielabonnement aktualisieren, sodass sie denen im Quellabonnement entsprechen.

### <a name="parallel-deployments"></a>Parallele Bereitstellungen

Das Tool erfordert einen Parameter namens **parallel**. Dieser Parameter nimmt einen booleschen Wert an, der angibt, ob die abgerufenen Ressourcen parallel bereitgestellt werden sollen oder nicht. Wenn der Wert auf **true** festgelegt ist, weist jeder Aufruf von **New-AzureRmResourceGroupDeployment** das Flag **-asJob** auf, und Codeblöcke zum Abwarten der Fertigstellung von Parallelaufträgen werden je nach Ressourcentypen zwischen Gruppen von Ressourcenbereitstellungen hinzugefügt. Dadurch wird sichergestellt, dass alle Ressourcen eines Typs bereitgestellt wurden, bevor der nächste Ressourcentyp bereitgestellt wird. Wenn der Wert des Parameters **parallel** auf **false** festgelegt ist, werden sämtliche Ressourcen nacheinander bereitgestellt.

## <a name="add-additional-resource-types"></a>Hinzufügen zusätzlicher Ressourcentypen

Neue Ressourcentypen können auf einfache Weise hinzugefügt werden. Der Entwickler muss einen angepassten Prozessor und entweder eine Azure Resource Manager-Vorlage oder einen Azure Resource Manager-Vorlagengenerator erstellen. Nach Abschluss dieses Vorgangs muss der Entwickler den Ressourcentyp dem ValidateSet für den Parameter **$resourceType** und das Array **$resourceTypes** in „resource_retriever.ps1“ hinzufügen. Beim Hinzufügen des Ressourcentyps zum Array **$resourceTypes** muss die richtige Reihenfolge beachtet werden. Die Reihenfolge des Arrays bestimmt die Reihenfolge, in der Ressourcen bereitgestellt werden; beachten Sie daher vorhandene Abhängigkeiten. Wenn der angepasste Prozessor einen Azure Resource Manager-Vorlagengenerator nutzt, muss schließlich der Name des Ressourcentyps dem Array **$customTypes** in **post_process.ps1** hinzugefügt werden.

## <a name="run-azure-subscription-replicator"></a>Ausführen des Azure-Abonnementreplikators

Zum Ausführen des Azure-Abonnementreplikators (v3) müssen Sie „resource_retriever.ps1“ starten und alle Parameter angeben. Für den Parameter **resourceType** kann eine Option für die Auswahl von **All** anstelle eines Ressourcentyps ausgewählt werden. Bei Auswahl von **All** verarbeitet „resource_retriever.ps1“ alle Ressourcen in einer bestimmten Reihenfolge, sodass beim Ausführen der Bereitstellung abhängige Ressourcen zuerst bereitgestellt werden. VNETs werden beispielsweise vor VMs bereitgestellt, da für die ordnungsgemäße Bereitstellung von VMs ein VNET vorhanden sein muss.

Wenn die Ausführung des Skripts abgeschlossen ist, sind die drei neuen Ordner **Deployment_Files**, **Parameter_Files** und **Custom_ARM_Templates** vorhanden.

 > [!Note]  
 > Vor dem Ausführen eines der generierten Skripts müssen Sie die richtige Umgebung festlegen und sich beim Zielabonnement anmelden (beispielsweise in der neuen Azure Stack Hub-Instanz) sowie das Arbeitsverzeichnis auf den Ordner **Deployment_Files** festlegen.

„Deployment_Files“ enthält die beiden Dateien **DeployResourceGroups.ps1** und **DeployResources.ps1**. Durch das Ausführen von „DeployResourceGroups.ps1“ werden die Ressourcengruppen bereitgestellt. Durch das Ausführen von „DeployResources.ps1“ werden alle verarbeiteten Ressourcen bereitgestellt. Wenn das Tool mit **All** oder **Microsoft.Compute/virtualMachines** als Ressourcentyp ausgeführt wurde, fordert „DeployResources.ps1“ den Benutzer zur Eingabe eines VM-Administratorkennworts auf, mit dem alle VMs erstellt werden.

### <a name="example"></a>Beispiel

1.  Führen Sie das Skript aus.

    ![Führen Sie das Skript aus.](./media/azure-stack-network-howto-backup-replicator/image2.png)

    > [!Note]  
    > Vergessen Sie nicht, die Quellumgebung und den Abonnementkontext für die PS-Instanz zu konfigurieren. 

2.  Überprüfen Sie die neu erstellten Ordner:

    ![Überprüfen der Ordner](./media/azure-stack-network-howto-backup-replicator/image4.png)

3.  Legen Sie den Kontext auf das Zielabonnement fest, ändern Sie den Ordner in **Deployment_Files**, verteilen Sie die Ressourcengruppen, und starten Sie dann die Ressourcenbereitstellung.

    ![Konfigurieren und Starten der Bereitstellung](./media/azure-stack-network-howto-backup-replicator/image6.png)

4.  Führen Sie `Get-Job` aus, um den Status zu überprüfen. Get-Job | Receive-Job gibt die Ergebnisse zurück.

## <a name="clean-up"></a>Bereinigung

Im Ordner „replicatorV3“ befindet sich die Datei **cleanup_generated_items.ps1**. Diese entfernt die Ordner **Deployment_Files**, **Parameter_Files** und **Custom_ARM_Templates** und deren gesamte Inhalte.

## <a name="subscription-replicator-operations"></a>Vorgänge des Abonnementreplikators

Der Azure-Abonnementreplikator (v3) kann derzeit die folgenden Ressourcentypen replizieren:

- Microsoft.Compute/availabilitySets

- Microsoft.Compute/virtualMachines

- Microsoft.Network/loadBalancers

- Microsoft.Network/networkSecurityGroups

- Microsoft.Network/publicIPAddresses

- Microsoft.Network/routeTables

- Microsoft.Network/virtualNetworks

- Microsoft.Network/virtualNetworkGateways

- Microsoft.Storage/storageAccounts

Wenn Sie das Tool mit **All** als Ressourcentyp ausführen, wird beim Replizieren und Bereitstellen die folgende Reihenfolge beachtet (im Beispiel unten wird die Konfiguration jeder Ressource repliziert, d. h. SKU, Angebot usw.):

- Microsoft.Network/virtualNetworks

    - Repliziert:       - Alle Adressräume       - Alle Subnetze

- Microsoft.Network/virtualNetworkGateways

    - Repliziert:       - Konfiguration der öffentlichen IP-Adresse       - Subnetzkonfiguration       - VPN-Typ       - Gatewaytyp

- Microsoft.Network/routeTables

- Microsoft.Network/networkSecurityGroups

    - Repliziert:       - Alle Sicherheitsregeln, eingehend und ausgehend

- Microsoft.Network/publicIPAddresses

- Microsoft.Network/loadBalancers

    - Repliziert:       - Private IP-Adressen       - Konfiguration der öffentlichen IP-Adresse       - Subnetzkonfiguration
    
- Microsoft.Compute/availabilitySets

    - Repliziert:       - Anzahl der Fehlerdomänen       - Anzahl der Updatedomänen

- Microsoft.Storage/storageAccounts

- Microsoft.Compute/virtualMachines
    - Repliziert:  
            - Datenträger (ohne Daten)  
            - VM-Größe  
            - Betriebssystem  
            - Konfiguration des Diagnosespeicherkontos  
            - Konfiguration der öffentlichen IP-Adresse  
            - Netzwerkschnittstelle  
            - Private IP-Adresse der Netzwerkschnittstelle  
            - Konfiguration der Netzwerksicherheitsgruppe  
            - Konfiguration der Verfügbarkeitsgruppe  

> [!Note]  
> Erstellt nur verwaltete Datenträger für Betriebssystem- und andere Datenträger. Speicherkonten werden derzeit nicht unterstützt. 

### <a name="limitations"></a>Einschränkungen

Das Tool kann Ressourcen aus einem Abonnement in ein anderes replizieren, sofern die Ressourcenanbieter des Zielabonnements alle Ressourcen und Optionen unterstützen, die aus dem Quellabonnement repliziert werden.

Stellen Sie für eine erfolgreiche Replikation sicher, dass die Versionen der Ressourcenanbieter des Zielabonnements denen des Quellabonnements entsprechen.

Beim Replizieren aus kommerziellem Azure zu kommerziellem Azure oder aus einem Abonnement in Azure Stack Hub zu einem anderen Abonnement innerhalb desselben Azure Stack Hub treten beim Replizieren von Speicherkonten Probleme auf. Dies ist auf die Benennungsanforderung für Speicherkonten zurückzuführen, dass alle Namen von Speicherkonten im gesamten kommerziellem Azure bzw. in allen Abonnements in einer Azure Stack Hub-Region/-Instanz eindeutig sein müssen. Das Replizieren von Speicherkonten über verschiedene Azure Stack Hub-Instanzen ist erfolgreich, da die Stacks separate Regionen/Instanzen darstellen.



## <a name="next-steps"></a>Nächste Schritte

[Azure Stack Hub-Netzwerke: Unterschiede und Überlegungen](azure-stack-network-differences.md)  
