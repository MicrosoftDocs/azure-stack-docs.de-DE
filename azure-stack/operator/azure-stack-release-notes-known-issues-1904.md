---
title: Azure Stack-Versionshinweise – Bekannte Probleme in 1904 | Microsoft-Dokumentation
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
ms.date: 05/07/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 782e0aaea0eadddae24795616aaba6053b728134
ms.sourcegitcommit: 39ba6d18781aed98b29ac5e08aac2d75c37bf18c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65387169"
---
# <a name="azure-stack-1904-known-issues"></a>Azure Stack 1904: Bekannte Probleme

In diesem Artikel werden die bekannten Probleme in Version 1904 von Azure Stack aufgelistet. Die Liste wird aktualisiert, wenn neue Probleme identifiziert werden.

> [!IMPORTANT]  
> Lesen Sie diesen Abschnitt, bevor Sie das Update anwenden.

## <a name="portal"></a>Portal

### <a name="add-on-plans"></a>Add-On-Pläne

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Pläne, die einem Benutzerabonnement als Add-On-Plan hinzugefügt wurden, können nicht gelöscht werden, auch wenn Sie den Plan aus dem Benutzerabonnement entfernen. Der Plan ist so lange vorhanden, bis die Abonnements gelöscht werden, die auf den Add-On-Plan verweisen.
- Abhilfe: Keine Lösung.
- Häufigkeit: Common

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

### <a name="marketplace-management"></a>Marketplace-Verwaltung

- Geltungsbereich: Dieses Problem gilt für 1904.
- Ursache: Der Marketplace-Verwaltungsbildschirm ist nicht sichtbar, wenn Sie sich am Administratorportal anmelden.
- Abhilfe: Aktualisieren Sie den Browser.
- Häufigkeit: Zeitweilig

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
- Abhilfe: Verwenden Sie PowerShell, die CLI oder eine Resource Manager-Vorlage, um den Back-End-Pool einer Lastenausgleichsressource zuzuordnen.
- Häufigkeit: Common

#### <a name="create-inbound-nat"></a>Erstellen von NAT-Eingangsregeln

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie im Benutzerportal versuchen, eine **NAT-Regel für eingehenden Datenverkehr** für einen **Load Balancer** hinzuzufügen, wird für den Vorgang eine Fehlermeldung der Art **Fehler beim Aktualisieren des Lastenausgleichs...** angezeigt.
- Abhilfe: Verwenden Sie PowerShell, die CLI oder eine Resource Manager-Vorlage, um den Back-End-Pool einer Lastenausgleichsressource zuzuordnen.
- Häufigkeit: Common

#### <a name="create-load-balancer"></a>Erstellen oder Aktualisieren eines Lastenausgleichs

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird im Fenster **Lastenausgleich erstellen** eine Option zum Erstellen einer Lastenausgleichs-SKU vom Typ **Standard** angezeigt. Diese Option wird in Azure Stack nicht unterstützt.
- Abhilfe: Verwenden Sie stattdessen die Lastenausgleichsoptionen vom Typ „Basic“.
- Häufigkeit: Common

#### <a name="public-ip-address"></a>Öffentliche IP-Adresse

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Im Benutzerportal wird im Fenster **Öffentliche IP-Adresse erstellen** eine Option zum Erstellen einer SKU vom Typ **Standard** angezeigt. Die SKU **Standard** wird in Azure Stack nicht unterstützt.
- Abhilfe: Verwenden Sie für die öffentliche IP-Adresse stattdessen die SKU vom Typ „Basic“.
- Häufigkeit: Common

## <a name="compute"></a>Compute

### <a name="vm-boot-diagnostics"></a>VM-Startdiagnose

- Geltungsbereich: Dieses Problem gilt für alle unterstützten Versionen.
- Ursache: Wenn Sie einen neuen virtuellen Windows-Computer erstellen, wird möglicherweise der folgende Fehler angezeigt: **Fehler beim Starten des virtuellen Computers „vm-name“. Fehler Fehler beim Aktualisieren der Einstellungen für die serielle Ausgabe für den virtuellen Computer „vm-name“**.
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

## <a name="infrastructure-backup"></a>Infrastructure Backup

<!--Bug 3615401 - scheduler config lost; new issue in YYMM;  hectorl-->
Nach dem Aktivieren automatischer Sicherungen wechselt der Planungsdienst unerwartet in den deaktivierten Zustand. Der Sicherungscontrollerdienst erkennt, dass automatische Sicherungen deaktiviert sind und löst eine Warnung im Administratorportal aus. Diese Warnung wird erwartet, wenn automatische Sicherungen deaktiviert sind.

- Geltungsbereich: Dies ist ein neues Problem in Version 1904.
- Ursache: Dieses Problem tritt aufgrund eines Fehlers im Dienst auf, der zum Verlust der Planerkonfiguration führt. Dieser Fehler ändert weder den Speicherort noch den Benutzernamen, das Kennwort oder den Verschlüsselungsschlüssel.
- Abhilfe: Um dieses Problem zu beheben, öffnen Sie das Blatt mit den Einstellungen des Sicherungscontrollers im Infrastructure Backup-Ressourcenanbieter, und wählen Sie **Automatische Sicherungen aktivieren** aus. Stellen Sie sicher, dass Sie die gewünschte Häufigkeit und den Aufbewahrungszeitraum festlegen.
- Häufigkeit: Niedrig

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->

## <a name="next-steps"></a>Nächste Schritte

- [Überprüfen der Checkliste mit Updateaktivitäten](azure-stack-release-notes-checklist.md)
- [Überprüfen der Liste mit Sicherheitsupdates](azure-stack-release-notes-security-updates-1904.md)
