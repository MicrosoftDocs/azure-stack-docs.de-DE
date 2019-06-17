---
title: 'Azure Stack 1905: Bekannte Probleme | Microsoft-Dokumentation'
description: Enthält Informationen zu bekannten Problemen in Azure Stack 1905.
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
ms.date: 06/05/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 06/05/2019
ms.openlocfilehash: 38c7ec337ba8cdb73925b1c07f77331c05b25d8a
ms.sourcegitcommit: e51cdc84a09250e8fa701bb2cb09de38d7de2c07
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/11/2019
ms.locfileid: "66836660"
---
# <a name="azure-stack-1905-known-issues"></a>Azure Stack 1905: Bekannte Probleme

In diesem Artikel werden die bekannten Probleme in Version 1905 von Azure Stack aufgelistet. Die Liste wird aktualisiert, wenn neue Probleme identifiziert werden.

> [!IMPORTANT]  
> Lesen Sie diesen Abschnitt, bevor Sie das Update anwenden.

## <a name="update-process"></a>Updateprozess

### <a name="host-node-update-prerequisite-failure"></a>Update von Hostknoten: Fehler bei Voraussetzungen

- Geltungsbereich: Dieses Problem gilt für das Update 1905.
- Ursache: Beim Versuch, das Azure Stack-Update 1905 zu installieren, ist das Update aufgrund eines Fehlers bei den **Voraussetzungen für das Hostknoten-Update** unter Umständen nicht erfolgreich. Dies wird meist dadurch verursacht, dass ein Hostknoten nicht über genügend freien Speicherplatz verfügt.
- Abhilfe: Wenden Sie sich an den Azure Stack-Support, um Hilfe beim Freigeben von Speicherplatz auf dem Hostknoten zu erhalten.
- Häufigkeit: Selten

### <a name="preparation-failed"></a>Fehler bei der Vorbereitung

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie versuchen, das Azure Stack-Update 1905 zu installieren, wird für den Status des Updates möglicherweise ein Fehler ausgegeben und der Zustand in **PreparationFailed** geändert. Der Grund dafür ist, dass der Updateressourcenanbieter (Update Resource Provider, URP) die Dateien aus dem Speichercontainer nicht ordnungsgemäß auf eine Infrastrukturfreigabe zur Verarbeitung übertragen kann. Da das Updatepaket 1905 größer als vorherige Updatepakete ist, kann dieses Problem häufiger auftreten.
- Abhilfe: Ab Version 1901 (1.1901.0.95) können Sie dieses Problem umgehen, indem Sie auf **Jetzt aktualisieren** (nicht **Fortsetzen**) klicken. Der URP bereinigt dann die Dateien aus dem vorherigen Versuch und startet den Download neu. Falls das Problem weiterhin besteht, empfehlen wir Ihnen das manuelle Hochladen des Updatepakets gemäß der Anleitung im Abschnitt [Importieren und Installieren von Updates](azure-stack-apply-updates.md#import-and-install-updates).
- Häufigkeit: Common

## <a name="portal"></a>Portal

### <a name="administrative-subscriptions"></a>Verwaltungsabonnements

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Die beiden Verwaltungsabonnements, die in Version 1804 eingeführt wurden, sollten nicht verwendet werden. Die Abonnementtypen sind **Messungsabonnement** und **Verbrauchsabonnement**.
- Abhilfe: Diese Abonnements werden ab Version 1906 gesperrt und später gelöscht. Wenn Sie unter diesen beiden Abonnements Ressourcen ausführen, müssen Sie sie mit Benutzerabonnements neu erstellen, die eine ältere Version als 1906 aufweisen.
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

### <a name="marketplace-management"></a>Marketplace-Verwaltung

- Geltungsbereich: Dieses Problem gilt für 1904 und 1905.
- Ursache: Der Marketplace-Verwaltungsbildschirm ist nicht sichtbar, wenn Sie sich beim Administratorportal anmelden.
- Abhilfe: Aktualisieren Sie den Browser, oder navigieren Sie zu **Einstellungen**, und wählen Sie die Option **Auf Standard zurücksetzen**.
- Häufigkeit: Zeitweilig

### <a name="docker-extension"></a>Docker-Erweiterung

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie im Administrator- oder Benutzerportal nach **Docker** suchen, wird das Element nicht richtig zurückgegeben. Sie ist in Azure Stack nicht verfügbar. Wenn Sie versuchen, es zu erstellen, wird ein Fehler angezeigt.
- Abhilfe: Keine Lösung.
- Häufigkeit: Common

### <a name="upload-blob"></a>Blob hochladen

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie im Benutzerportal versuchen, ein Blob mit der Option **OAuth (Vorschau)** hochzuladen, wird für die Aufgabe eine Fehlermeldung angezeigt.
- Abhilfe: Laden Sie das Blob mit der SAS-Option hoch.
- Häufigkeit: Common

## <a name="networking"></a>Netzwerk

### <a name="load-balancer"></a>Load Balancer

#### <a name="add-backend-pool"></a>Hinzufügen eines Back-End-Pools

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie im Benutzerportal versuchen, einen **Back-End-Pool** einem **Load Balancer** hinzuzufügen, wird für den Vorgang eine Fehlermeldung der Art **Fehler beim Aktualisieren des Lastenausgleichs...** angezeigt.
- Abhilfe: Verwenden Sie PowerShell, die CLI oder eine Resource Manager-Vorlage, um den Back-End-Pool einer Lastenausgleichsressource zuzuordnen.
- Häufigkeit: Common

#### <a name="create-inbound-nat"></a>Erstellen von NAT-Regeln für eingehenden Datenverkehr

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie im Benutzerportal versuchen, eine **NAT-Regel für eingehenden Datenverkehr** für einen **Load Balancer** hinzuzufügen, wird für den Vorgang eine Fehlermeldung der Art **Fehler beim Aktualisieren des Lastenausgleichs...** angezeigt.
- Abhilfe: Verwenden Sie PowerShell, die CLI oder eine Resource Manager-Vorlage, um den Back-End-Pool einer Lastenausgleichsressource zuzuordnen.
- Häufigkeit: Common

#### <a name="create-load-balancer"></a>Erstellen eines Load Balancers

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird im Fenster **Lastenausgleich erstellen** eine Option zum Erstellen einer Lastenausgleichs-SKU vom Typ **Standard** angezeigt. Diese Option wird in Azure Stack nicht unterstützt.
- Abhilfe: Verwenden Sie stattdessen die Lastenausgleichsoptionen vom Typ **Basic**.
- Häufigkeit: Common

### <a name="public-ip-address"></a>Öffentliche IP-Adresse

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird im Fenster **Öffentliche IP-Adresse erstellen** eine Option zum Erstellen einer SKU vom Typ **Standard** angezeigt. Die SKU **Standard** wird in Azure Stack nicht unterstützt.
- Abhilfe: Verwenden Sie die SKU **Basic** für die öffentliche IP-Adresse.
- Häufigkeit: Common

## <a name="compute"></a>Compute

### <a name="vm-boot-diagnostics"></a>VM-Startdiagnose

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie einen neuen virtuellen Windows-Computer (VM) erstellen, wird unter Umständen der folgende Fehler angezeigt: **Fehler beim Starten des virtuellen Computers „vm-name“. Fehler Fehler beim Aktualisieren der Einstellungen für die serielle Ausgabe für den virtuellen Computer „vm-name“** .
Der Fehler tritt auf, wenn Sie die Startdiagnose bei einem virtuellen Computer aktivieren, aber Ihr Startdiagnose-Speicherkonto löschen.
- Abhilfe: Erstellen Sie das Speicherkonto unter dem zuvor verwendeten Namen neu.
- Häufigkeit: Common

### <a name="virtual-machine-scale-set"></a>VM-Skalierungsgruppe

#### <a name="centos"></a>CentOS

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Auf der Benutzeroberfläche zum Erstellen von VM-Skalierungsgruppen wird „CentOS 7.2-basiert“ als Option für die Bereitstellung angeboten. CentOS 7.2 ist im Azure Stack-Marketplace nicht verfügbar, sodass es zu Bereitstellungsfehlern mit dem Hinweis kommt, dass das Image nicht gefunden wurde.
- Abhilfe: Wählen Sie entweder ein anderes Betriebssystem für Ihre Bereitstellung aus, oder verwenden Sie eine Azure Resource Manager-Vorlage mit einem anderen CentOS-Image, das vor der Bereitstellung vom Bediener aus dem Marketplace heruntergeladen wurde.
- Häufigkeit: Common

#### <a name="remove-scale-set"></a>Entfernen einer Skalierungsgruppe

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Sie können eine Skalierungsgruppe nicht über das Blatt **VM-Skalierungsgruppen** entfernen.
- Abhilfe: Wählen Sie die Skalierungsgruppe aus, die Sie entfernen möchten, und klicken Sie dann im Bereich **Übersicht** auf die Schaltfläche **Löschen**.
- Häufigkeit: Common

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Erstellungsfehler beim Patchen und Aktualisieren in Azure Stack-Umgebungen mit vier Knoten

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Bei der Erstellung von VMs in einer Verfügbarkeitsgruppe mit drei Fehlerdomänen und der Erstellung einer Instanz einer VM-Skalierungsgruppe tritt während des Updatevorgangs in einer Azure Stack-Umgebung mit vier Knoten der Fehler **FabricVmPlacementErrorUnsupportedFaultDomainSize** auf.
- Abhilfe: Sie können einzelne VMs in einer Verfügbarkeitsgruppe mit zwei Fehlerdomänen erfolgreich durchführen. Die Erstellung der Skalierungsgruppeninstanz ist während des Updatevorgangs in einer Azure Stack-Umgebung mit vier Knoten aber immer noch nicht verfügbar.

#### <a name="vmss-instance-view-blade-doesnt-load"></a>Blatt mit VMSS-Instanzansicht wird nicht geladen
 
- Geltungsbereich: Dieses Problem gilt für das Release 1904 und 1905.
- Ursache: Das Blatt mit der Instanzansicht einer Skalierungsgruppe im Azure Stack-Portal unter „Dashboard“ > „VM-Skalierungsgruppen“ > „AnyScaleSet – Instanzen“ > „AnyScaleSetInstance“ kann mit Crying Cloud nicht geladen werden.
- Abhilfe: Es gibt derzeit keine Abhilfe, und wir arbeiten an der Behebung des Fehlers. Verwenden Sie in der Zwischenzeit das CLI-Cmdlet „az vmss get-instance-view“, um die VMSS-Instanzansicht zu erhalten.

### <a name="ubuntu-ssh-access"></a>Ubuntu-SSH-Zugriff

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Ein virtueller Ubuntu 18.04-Computer, der mit aktivierter SSH-Autorisierung erstellt wurde, lässt nicht zu, dass Sie die SSH-Schlüssel für die Anmeldung verwenden.
- Abhilfe: Verwenden Sie VM-Zugriff für die Linux-Erweiterung, um SSH-Schlüssel nach der Bereitstellung zu implementieren, oder verwenden Sie die kennwortbasierte Authentifizierung.
- Häufigkeit: Common

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->

## <a name="next-steps"></a>Nächste Schritte

- [Überprüfen der Checkliste mit Updateaktivitäten](azure-stack-release-notes-checklist.md)
- [Überprüfen der Liste mit Sicherheitsupdates](azure-stack-release-notes-security-updates-1905.md)
