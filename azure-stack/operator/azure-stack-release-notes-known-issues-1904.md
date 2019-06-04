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
ms.date: 05/28/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 05/28/2019
ms.openlocfilehash: 9ebbdb19335db4f0c31d68c726f7b8c211d0f2e2
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66268328"
---
# <a name="azure-stack-1904-known-issues"></a>Azure Stack 1904: Bekannte Probleme

In diesem Artikel werden die bekannten Probleme in Version 1904 von Azure Stack aufgelistet. Die Liste wird aktualisiert, wenn neue Probleme identifiziert werden.

> [!IMPORTANT]  
> Lesen Sie diesen Abschnitt, bevor Sie das Update anwenden.

## <a name="update-process"></a>Updateprozess

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie versuchen, ein Azure Stack-Update zu installieren, wird für den Status des Updates möglicherweise ein Fehler ausgegeben und der Zustand in **PreparationFailed** geändert. Der Grund dafür ist, dass der Updateressourcenanbieter (Update Resource Provider, URP) die Dateien aus dem Speichercontainer nicht ordnungsgemäß auf eine Infrastrukturfreigabe zur Verarbeitung übertragen kann.
- Abhilfe: Ab Version 1901 (1.1901.0.95) können Sie dieses Problem umgehen, indem Sie auf **Jetzt aktualisieren** (nicht **Fortsetzen**) klicken. Der URP bereinigt dann die Dateien aus dem vorherigen Versuch und startet den Download erneut.
- Häufigkeit: Common

## <a name="portal"></a>Portal

### <a name="administrative-subscriptions"></a>Verwaltungsabonnements

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Die beiden Verwaltungsabonnements, die in Version 1804 eingeführt wurden, sollten nicht verwendet werden. Die Abonnementtypen sind **Messungsabonnement** und **Verbrauchsabonnement**.
- Abhilfe: Diese Abonnements werden ab Version 1905 gesperrt und später gelöscht. Wenn Sie unter diesen beiden Abonnements Ressourcen ausführen, müssen Sie sie unter Benutzerabonnements mit einer Version vor 1905 neu erstellen.
- Häufigkeit: Common

### <a name="subscription-resources"></a>Abonnementressourcen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Das Löschen von Benutzerabonnements führt zu verwaisten Ressourcen.
- Abhilfe: Löschen Sie zuerst die Benutzerressourcen oder die gesamte Ressourcengruppe und anschließend die Benutzerabonnements.
- Häufigkeit: Common

### <a name="subscription-permissions"></a>Abonnementberechtigungen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Sie können mit den Azure Stack-Portalen keine Berechtigungen für Ihr Abonnement anzeigen.
- Abhilfe: Verwenden Sie [PowerShell zum Überprüfen der Berechtigungen](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Häufigkeit: Common


### <a name="docker-extension"></a>Docker-Erweiterung
- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie im Administrator- oder Benutzerportal nach „Docker“ suchen, wird das Element nicht richtig zurückgegeben. Es ist in Azure Stack nicht verfügbar. Wenn Sie versuchen, sie zu erstellen, wird ein Blatt mit einem Fehlerhinweis angezeigt.
- Abhilfe: Keine Lösung.
- Häufigkeit: Common

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
- Ursache: Wenn Sie im Administrator- oder Benutzerportal nach **Docker** suchen, wird das Element nicht richtig zurückgegeben. Es ist in Azure Stack nicht verfügbar. Wenn Sie versuchen, es zu erstellen, wird ein Fehler angezeigt.
- Abhilfe: Keine Lösung.
- Häufigkeit: Common

### <a name="upload-blob"></a>Blob hochladen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie im Benutzerportal versuchen, ein Blob mit der Option „OAuth (Vorschau)“ hochzuladen, wird für die Aufgabe eine Fehlermeldung angezeigt.
- Abhilfe: Laden Sie das Blob mit der SAS-Option hoch.
- Häufigkeit: Common

## <a name="networking"></a>Netzwerk

### <a name="load-balancer"></a>Load Balancer

#### <a name="add-backend-pool"></a>Hinzufügen eines Back-End-Pools

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie im Benutzerportal versuchen, einen **Back-End-Pool** einem **Load Balancer** hinzuzufügen, wird für den Vorgang eine Fehlermeldung der Art **Fehler beim Aktualisieren des Lastenausgleichs...** angezeigt.
- Abhilfe: Verwenden Sie PowerShell, die Befehlszeilenschnittstelle (Command Line Interface, CLI) oder eine Azure Resource Manager-Vorlage, um den Back-End-Pool einer Lastenausgleichsressource zuzuordnen.
- Häufigkeit: Common

#### <a name="create-inbound-nat"></a>Erstellen von NAT-Regeln für eingehenden Datenverkehr

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie im Benutzerportal versuchen, eine **NAT-Regel für eingehenden Datenverkehr** für einen **Load Balancer** hinzuzufügen, wird für den Vorgang eine Fehlermeldung der Art **Fehler beim Aktualisieren des Lastenausgleichs...** angezeigt.
- Abhilfe: Verwenden Sie PowerShell, die Befehlszeilenschnittstelle (Command Line Interface, CLI) oder eine Azure Resource Manager-Vorlage, um den Back-End-Pool einer Lastenausgleichsressource zuzuordnen.
- Häufigkeit: Common

#### <a name="create-load-balancer"></a>Erstellen eines Load Balancers

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird im Fenster **Lastenausgleich erstellen** eine Option zum Erstellen einer Lastenausgleichs-SKU vom Typ **Standard** angezeigt. Diese Option wird in Azure Stack nicht unterstützt.
- Abhilfe: Verwenden Sie stattdessen die Lastenausgleichsoptionen vom Typ „Basic“.
- Häufigkeit: Common

#### <a name="public-ip-address"></a>Öffentliche IP-Adresse

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird im Fenster **Öffentliche IP-Adresse erstellen** eine Option zum Erstellen einer SKU vom Typ **Standard** angezeigt. Die SKU **Standard** wird in Azure Stack nicht unterstützt.
- Abhilfe: Verwenden Sie für öffentliche IP-Adressen stattdessen die SKU **Basic**.
- Häufigkeit: Common

## <a name="compute"></a>Compute

### <a name="vm-boot-diagnostics"></a>VM-Startdiagnose

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie einen neuen virtuellen Windows-Computer erstellen, wird möglicherweise der folgende Fehler angezeigt: **Fehler beim Starten des virtuellen Computers „vm-name“. Fehler Fehler beim Aktualisieren der Einstellungen für die serielle Ausgabe für den virtuellen Computer „vm-name“** .
Der Fehler tritt auf, wenn Sie die Startdiagnose bei einem virtuellen Computer aktivieren, aber Ihr Startdiagnose-Speicherkonto löschen.
- Abhilfe: Erstellen Sie das Speicherkonto unter dem zuvor verwendeten Namen neu.
- Häufigkeit: Common

### <a name="virtual-machine-scale-set"></a>VM-Skalierungsgruppe

#### <a name="centos"></a>CentOS

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Die Benutzeroberfläche zum Erstellen von VM-Skalierungsgruppen bietet „CentOS 7.2-basiert“ als Option für die Bereitstellung an. CentOS 7.2 ist in Azure Stack nicht verfügbar.
- Abhilfe: Wählen Sie entweder ein anderes Betriebssystem für Ihre Bereitstellung aus, oder verwenden Sie eine Azure Resource Manager-Vorlage mit einem anderen CentOS-Image, das vor der Bereitstellung vom Bediener aus dem Marketplace heruntergeladen wurde.
- Häufigkeit: Common

#### <a name="remove-scale-set"></a>Entfernen einer Skalierungsgruppe

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Sie können eine Skalierungsgruppe nicht über das Blatt **VM-Skalierungsgruppen** entfernen.
- Abhilfe: Wählen Sie die Skalierungsgruppe aus, die Sie entfernen möchten, und klicken Sie dann im Bereich **Übersicht** auf die Schaltfläche **Löschen**.
- Häufigkeit: Common

### <a name="ubuntu-ssh-access"></a>Ubuntu-SSH-Zugriff

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Ein virtueller Ubuntu 18.04-Computer, der mit aktivierter SSH-Autorisierung erstellt wurde, lässt nicht zu, dass Sie die SSH-Schlüssel für die Anmeldung verwenden.
- Abhilfe: Verwenden Sie VM-Zugriff für die Linux-Erweiterung, um SSH-Schlüssel nach der Bereitstellung zu implementieren, oder verwenden Sie die kennwortbasierte Authentifizierung.
- Häufigkeit: Common

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
- Häufigkeit: Common

## <a name="storage"></a>Storage

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk) wird in Azure Stack nicht unterstützt und führt zur Erstellung eines Datenträgers mit der ID **$null**. Dies ist der Grund, warum Sie für die VM keine Vorgänge, z. B. das Starten und Beenden, durchführen können. Der Datenträger wird nicht auf der Benutzeroberfläche und auch nicht über die API angezeigt. Die VM kann an diesem Punkt nicht repariert werden, sodass sie gelöscht werden muss.
- Abhilfe: Befolgen Sie die [Anleitung zum Konvertieren in verwaltete Datenträger](../user/azure-stack-managed-disk-considerations.md#convert-to-managed-disks), um Ihre Datenträger richtig zu konvertieren.

## <a name="app-service"></a>App Service

- Mandanten müssen den Speicherressourcenanbieter vor dem Erstellen ihrer ersten Azure-Funktion im Abonnement registrieren.
- Einige Benutzeroberflächen im Mandantenportal sind aufgrund einer Inkompatibilität mit dem Portalframework in 1903 fehlerhaft, z. B. die Benutzeroberfläche für Bereitstellungsslots, Tests in der Produktion und Websiteerweiterungen. Verwenden Sie das [Azure App Service PowerShell-Modul](/azure/app-service/deploy-staging-slots#automate-with-powershell) oder die [Azure CLI](/cli/azure/webapp/deployment/slot?view=azure-cli-latest), um dieses Problem zu umgehen. Die Portalumgebung wird im bevorstehenden Release von Azure App Service unter Azure Stack 1.6 (Update 6) wiederhergestellt.

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->

## <a name="next-steps"></a>Nächste Schritte

- [Überprüfen der Checkliste mit Updateaktivitäten](azure-stack-release-notes-checklist.md)
- [Überprüfen der Liste mit Sicherheitsupdates](azure-stack-release-notes-security-updates-1904.md)
