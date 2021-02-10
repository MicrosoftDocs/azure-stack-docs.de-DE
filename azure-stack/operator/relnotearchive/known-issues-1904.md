---
title: 'Azure Stack 1904: Bekannte Probleme | Microsoft-Dokumentation'
description: Enthält Informationen zu bekannten Problemen in Azure Stack 1904.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 05/31/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f977c1671468257ac205c494f33ad46965f1f2eb
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248731"
---
# <a name="azure-stack-1904-known-issues"></a>Azure Stack 1904: Bekannte Probleme

In diesem Artikel werden die bekannten Probleme in Version 1904 von Azure Stack aufgelistet. Die Liste wird aktualisiert, wenn neue Probleme identifiziert werden.

> [!IMPORTANT]  
> Lesen Sie diesen Abschnitt, bevor Sie das Update anwenden.

## <a name="update-process"></a>Updateprozess

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie versuchen, ein Azure Stack-Update zu installieren, wird für den Status des Updates möglicherweise ein Fehler ausgegeben und der Zustand in **PreparationFailed** geändert. Der Grund dafür ist, dass der Updateressourcenanbieter (Update Resource Provider, URP) die Dateien aus dem Speichercontainer nicht ordnungsgemäß auf eine Infrastrukturfreigabe zur Verarbeitung übertragen kann.
- Abhilfe: Ab Version 1901 (1.1901.0.95) können Sie dieses Problem umgehen, indem Sie auf **Jetzt aktualisieren** (nicht **Fortsetzen**) klicken. Der URP bereinigt dann die Dateien aus dem vorherigen Versuch und startet den Download erneut.
- Häufigkeit: Allgemein

## <a name="portal"></a>Portal

### <a name="administrative-subscriptions"></a>Verwaltungsabonnements

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Die beiden Verwaltungsabonnements, die in Version 1804 eingeführt wurden, sollten nicht verwendet werden. Die Abonnementtypen sind **Messungsabonnement** und **Verbrauchsabonnement**.
- Abhilfe: Wenn Sie unter diesen beiden Abonnements Ressourcen ausführen, erstellen Sie sie in Benutzerabonnements neu.
- Häufigkeit: Allgemein

### <a name="subscription-resources"></a>Abonnementressourcen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Das Löschen von Benutzerabonnements führt zu verwaisten Ressourcen.
- Abhilfe: Löschen Sie zuerst die Benutzerressourcen oder die gesamte Ressourcengruppe und anschließend die Benutzerabonnements.
- Häufigkeit: Allgemein

### <a name="subscription-permissions"></a>Abonnementberechtigungen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Sie können mit den Azure Stack-Portalen keine Berechtigungen für Ihr Abonnement anzeigen.
- Abhilfe: Verwenden Sie [PowerShell zum Überprüfen der Berechtigungen](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Häufigkeit: Allgemein

### <a name="docker-extension"></a>Docker-Erweiterung

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie im Administrator- oder Benutzerportal nach **Docker** suchen, wird das Element nicht richtig zurückgegeben. Sie ist in Azure Stack nicht verfügbar. Wenn Sie versuchen, es zu erstellen, wird ein Fehler angezeigt.
- Abhilfe: Keine Lösung.
- Häufigkeit: Allgemein

### <a name="marketplace-management"></a>Marketplace-Verwaltung

- Geltungsbereich: Dies ist ein neues Problem in Version 1904.
- Ursache: Der Marketplace-Verwaltungsbildschirm ist nicht sichtbar, wenn Sie sich beim Administratorportal anmelden.
- Abhilfe: Aktualisieren Sie den Browser.
- Häufigkeit: Zeitweilig

### <a name="marketplace-management"></a>Marketplace-Verwaltung

- Geltungsbereich: Dieses Problem gilt für 1904.
- Ursache: Wenn Sie im Administratorportal auf der Registerkarte für die Marketplace-Verwaltung Ergebnisse auf dem Blatt **Add from Azure** (Aus Azure hinzufügen) filtern, werden möglicherweise falsche gefilterte Ergebnisse angezeigt.
- Abhilfe: Sortieren Sie Ergebnisse nach der Spalte „Name“. Die Ergebnisse werden dann korrigiert.
- Häufigkeit: Zeitweilig

### <a name="marketplace-management"></a>Marketplace-Verwaltung

- Geltungsbereich: Dieses Problem gilt für 1904.
- Ursache: Wenn Sie im Administratorportal Ergebnisse in der Marketplace-Verwaltung filtern, werden in der Dropdownliste „Herausgeber“ doppelte Herausgebernamen angezeigt. 
- Abhilfe: Wählen Sie alle doppelten Namen aus, um die korrekte Liste aller Marketplace-Produkte anzuzeigen, die unter dem jeweiligen Herausgeber verfügbar sind.
- Häufigkeit: Zeitweilig

### <a name="docker-extension"></a>Docker-Erweiterung

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie im Administrator- oder Benutzerportal nach **Docker** suchen, wird das Element nicht richtig zurückgegeben. Sie ist in Azure Stack nicht verfügbar. Wenn Sie versuchen, es zu erstellen, wird ein Fehler angezeigt.
- Abhilfe: Keine Lösung.
- Häufigkeit: Allgemein

### <a name="upload-blob"></a>Blob hochladen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie im Benutzerportal versuchen, ein Blob mit der Option OAuth (Vorschau) hochzuladen, wird für die Aufgabe eine Fehlermeldung angezeigt.
- Abhilfe: Laden Sie das Blob mit der SAS-Option hoch.
- Häufigkeit: Allgemein

### <a name="template"></a>Vorlage

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal füllt die Benutzeroberfläche für die Vorlagenbereitstellung Parameter für die Vorlagennamen, die mit „_“ (Unterstrich) beginnen, nicht auf.
- Abhilfe: Entfernen Sie den „_“ (Unterstrich) aus dem Vorlagennamen.
- Häufigkeit: Allgemein

## <a name="networking"></a>Netzwerk

### <a name="load-balancer"></a>Load Balancer

#### <a name="add-backend-pool"></a>Hinzufügen eines Back-End-Pools

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie im Benutzerportal versuchen, einen **Back-End-Pool** einem **Load Balancer** hinzuzufügen, wird für den Vorgang eine Fehlermeldung der Art **Fehler beim Aktualisieren des Lastenausgleichs...** angezeigt.
- Abhilfe: Verwenden Sie PowerShell, die Befehlszeilenschnittstelle (Command Line Interface, CLI) oder eine Azure Resource Manager-Vorlage, um den Back-End-Pool einer Lastenausgleichsressource zuzuordnen.
- Häufigkeit: Allgemein

#### <a name="create-inbound-nat"></a>Erstellen von NAT-Regeln für eingehenden Datenverkehr

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie im Benutzerportal versuchen, eine **NAT-Regel für eingehenden Datenverkehr** für einen **Load Balancer** hinzuzufügen, wird für den Vorgang eine Fehlermeldung der Art **Fehler beim Aktualisieren des Lastenausgleichs...** angezeigt.
- Abhilfe: Verwenden Sie PowerShell, die Befehlszeilenschnittstelle (Command Line Interface, CLI) oder eine Azure Resource Manager-Vorlage, um den Back-End-Pool einer Lastenausgleichsressource zuzuordnen.
- Häufigkeit: Allgemein

#### <a name="create-load-balancer"></a>Erstellen eines Load Balancers

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird im Fenster **Lastenausgleich erstellen** eine Option zum Erstellen einer Lastenausgleichs-SKU vom Typ **Standard** angezeigt. Diese Option wird in Azure Stack nicht unterstützt.
- Abhilfe: Verwenden Sie stattdessen die Lastenausgleichsoptionen vom Typ „Basic“.
- Häufigkeit: Allgemein

#### <a name="public-ip-address"></a>Öffentliche IP-Adresse

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird im Fenster **Öffentliche IP-Adresse erstellen** eine Option zum Erstellen einer SKU vom Typ **Standard** angezeigt. Die SKU **Standard** wird in Azure Stack nicht unterstützt.
- Abhilfe: Verwenden Sie für öffentliche IP-Adressen stattdessen die SKU **Basic**.
- Häufigkeit: Allgemein

## <a name="compute"></a>Compute

### <a name="vm-boot-diagnostics"></a>VM-Startdiagnose

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie einen neuen virtuellen Windows-Computer erstellen, wird möglicherweise der folgende Fehler angezeigt: **Fehler beim Starten des virtuellen Computers „vm-name“. Error: Fehler beim Aktualisieren der Einstellungen für die serielle Ausgabe für den virtuellen Computer „vm-name“** .
Der Fehler tritt auf, wenn Sie die Startdiagnose bei einem virtuellen Computer aktivieren, aber Ihr Startdiagnose-Speicherkonto löschen.
- Abhilfe: Erstellen Sie das Speicherkonto unter dem zuvor verwendeten Namen neu.
- Häufigkeit: Allgemein

### <a name="virtual-machine-scale-set"></a>VM-Skalierungsgruppe

#### <a name="centos"></a>CentOS

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Auf der Benutzeroberfläche zum Erstellen von VM-Skalierungsgruppen wird „CentOS 7.2-basiert“ als Option für die Bereitstellung angeboten. CentOS 7.2 ist im Azure Stack-Marketplace nicht verfügbar, sodass es zu Bereitstellungsfehlern mit dem Hinweis kommt, dass das Image nicht gefunden wurde.
- Abhilfe: Wählen Sie entweder ein anderes Betriebssystem für Ihre Bereitstellung aus, oder verwenden Sie eine Azure Resource Manager-Vorlage mit einem anderen CentOS-Image, das vor der Bereitstellung vom Bediener aus dem Marketplace heruntergeladen wurde.
- Häufigkeit: Allgemein

#### <a name="remove-scale-set"></a>Entfernen einer Skalierungsgruppe

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Sie können eine Skalierungsgruppe nicht über das Blatt **VM-Skalierungsgruppen** entfernen.
- Abhilfe: Wählen Sie die Skalierungsgruppe aus, die Sie entfernen möchten, und klicken Sie dann im Bereich **Übersicht** auf die Schaltfläche **Löschen**.
- Häufigkeit: Allgemein

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Erstellungsfehler beim Patchen und Aktualisieren in Azure Stack-Umgebungen mit vier Knoten

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Bei der Erstellung von VMs in einer Verfügbarkeitsgruppe mit drei Fehlerdomänen und der Erstellung einer Instanz einer VM-Skalierungsgruppe tritt während des Updatevorgangs in einer Azure Stack-Umgebung mit vier Knoten der Fehler **FabricVmPlacementErrorUnsupportedFaultDomainSize** auf.
- Abhilfe: Sie können einzelne VMs in einer Verfügbarkeitsgruppe mit zwei Fehlerdomänen erfolgreich durchführen. Die Erstellung der Skalierungsgruppeninstanz ist während des Updatevorgangs in einer Azure Stack-Umgebung mit vier Knoten aber immer noch nicht verfügbar.

### <a name="ubuntu-ssh-access"></a>Ubuntu-SSH-Zugriff

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Ein virtueller Ubuntu 18.04-Computer, der mit aktivierter SSH-Autorisierung erstellt wurde, lässt nicht zu, dass Sie die SSH-Schlüssel für die Anmeldung verwenden.
- Abhilfe: Verwenden Sie VM-Zugriff für die Linux-Erweiterung, um SSH-Schlüssel nach der Bereitstellung zu implementieren, oder verwenden Sie die kennwortbasierte Authentifizierung.
- Häufigkeit: Allgemein

### <a name="compute-host-agent-alert"></a>Warnung zum Computehost-Agent

- Geltungsbereich: Dies ist ein neues Problem in Version 1904.
- Ursache: Nach dem Neustarten eines Knotens in der Skalierungseinheit wird eine Warnung zum **Computehost-Agent** angezeigt. Durch den Neustart wird die Standardstarteinstellung für den Computehost-Agent-Dienst geändert. Diese Warnung sieht in etwa wie im folgenden Beispiel aus:

   ```shell
   NAME  
   Compute Host Agent is not responding to calls.
   SEVERITY  
   Warning
   STATE  
   Active
   CREATED TIME  
   5/16/2019, 10:08:23 AM
   UPDATED TIME  
   5/22/2019, 12:27:27 PM
   COMPONENT  
   M#####-NODE02
   DESCRIPTION  
   Could not communicate with the Compute Host Agent running on node: M#####-NODE02
   REMEDIATION  
   Please disable Compute Host Agent feature flag and collect logs for further diagnosis.
   ```

- Abhilfe:
  - Diese Warnung kann ignoriert werden. Wenn der Agent nicht reagiert, hat dies keine Auswirkungen auf Bediener- und Benutzervorgänge oder Benutzeranwendungen. Die Warnung wird nach 24 Stunden erneut angezeigt, wenn sie manuell geschlossen wird.
  - Das Problem wurde im letzten [Azure Stack-Hotfix für 1904](https://support.microsoft.com/help/4505688) behoben.
- Häufigkeit: Allgemein

### <a name="virtual-machine-scale-set-instance-view"></a>Ansicht der Instanz einer VM-Skalierungsgruppe

- Geltungsbereich: Dieses Problem betrifft die Versionen 1904 und 1905.
- Ursache: Das Blatt mit der Instanzenansicht einer Skalierungsgruppe im Azure Stack-Portal unter **Dashboard** > **VM-Skalierungsgruppen** > **AnyScaleSet – Instanzen** > **AnyScaleSetInstance** kann nicht geladen werden.
- Abhilfe: Es gibt derzeit keine Abhilfe, und wir arbeiten an der Behebung des Fehlers. Verwenden Sie bis dahin das CLI-Cmdlet `az vmss get-instance-view`, um die Instanzansicht einer VM-Skalierungsgruppe abzurufen.

### <a name="user-image-service"></a>Dienst für Benutzerimages
- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Ein Fehler bei der Erstellung eines Benutzerimages sorgt dafür, dass sich der Benutzerimagedienst in einem fehlerhaften Zustand befindet. Das Erstellen von Benutzerimages und Löschvorgänge schlagen fehl. Das Löschen von Benutzerimages schlägt fehl und gibt möglicherweise folgenden Fehler aus: „Fehler: An internal disk management error occurred." (Interner Fehler bei der Datenträgerverwaltung).
- Abhilfe: Keine Lösung. Öffnen Sie ein Supportticket für Microsoft.

## <a name="storage"></a>Storage

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk) wird in Azure Stack nicht unterstützt und führt zur Erstellung eines Datenträgers mit der ID **$null**. Dies ist der Grund, warum Sie für die VM keine Vorgänge, z. B. das Starten und Beenden, durchführen können. Der Datenträger wird nicht auf der Benutzeroberfläche und auch nicht über die API angezeigt. Die VM kann an diesem Punkt nicht repariert werden, sodass sie gelöscht werden muss.
- Abhilfe: Befolgen Sie die [Anleitung zum Konvertieren in verwaltete Datenträger](../../user/azure-stack-managed-disk-considerations.md#convert-to-managed-disks), um Ihre Datenträger richtig zu konvertieren.

## <a name="app-service"></a>App Service

- Mandanten müssen den Speicherressourcenanbieter vor dem Erstellen ihrer ersten Azure-Funktion im Abonnement registrieren.
- Einige Benutzeroberflächen im Mandantenportal sind aufgrund einer Inkompatibilität mit dem Portalframework in 1903 fehlerhaft, z. B. die Benutzeroberfläche für Bereitstellungsslots, Tests in der Produktion und Websiteerweiterungen. Verwenden Sie das [Azure App Service PowerShell-Modul](/azure/app-service/deploy-staging-slots#automate-with-powershell) oder die [Azure CLI](/cli/azure/webapp/deployment/slot?view=azure-cli-latest&preserve-view=true), um dieses Problem zu umgehen. Die Portalumgebung wird wiederhergestellt, indem für Ihre Bereitstellung von [Azure App Service unter Azure Stack ein Update auf Version 1.6 (Update 6) durchgeführt wird](../azure-stack-app-service-release-notes-update-six.md).

## <a name="next-steps"></a>Nächste Schritte

- [Überprüfen der Checkliste mit Updateaktivitäten](../release-notes-checklist.md)
- [Überprüfen der Liste mit Sicherheitsupdates](../release-notes-security-updates.md)
