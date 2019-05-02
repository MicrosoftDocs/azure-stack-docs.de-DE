---
title: Verwenden von Azure Resource Manager-Vorlagen in Azure Stack | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Azure Resource Manager-Vorlagen in Azure Stack zum Bereitstellen von Ressourcen verwenden.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2019
ms.author: sethm
ms.reviewer: justini
ms.lastreviewed: 11/14/2018
ms.openlocfilehash: b2f2427f7dcf9265f379995f1e854c382e24a9c7
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "64310080"
---
# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Verwenden von Azure-Ressourcen-Manager-Vorlagen in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Mit Azure Resource Manager-Vorlagen können Sie alle Ressourcen für Ihre Anwendung in einem einzigen, koordinierten Vorgang bereitstellen. Sie können Vorlagen auch erneut bereitstellen, um Änderungen an den Ressourcen in einer Ressourcengruppe vorzunehmen.

Diese Vorlagen können mit dem Microsoft Azure Stack-Portal, PowerShell, über die Befehlszeile und Visual Studio bereitgestellt werden.

Die folgenden Schnellstartvorlagen sind auf [GitHub](https://aka.ms/azurestackgithub)verfügbar:

## <a name="deploy-sharepoint-server-non-high-availability-deployment"></a>Bereitstellen von SharePoint Server (Bereitstellung ohne Hochverfügbarkeit)

Verwenden Sie die PowerShell DSC-Erweiterung, um eine [SharePoint Server 2013-Farm zu erstellen](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sharepoint-2013-non-ha), die folgende Ressourcen enthält:

* Ein virtuelles Netzwerk
* Drei Speicherkonten
* Zwei externe Load Balancer
* Einen virtuellen Computer (VM), der als Domänencontroller für eine neue Gesamtstruktur mit einer einzelnen Domäne konfiguriert ist
* Einen virtuellen Computer, der als eigenständiger Server für SQL Server 2014 konfiguriert ist
* Eine VM, die als SharePoint Server 2013-Farm mit einem Computer konfiguriert ist

## <a name="deploy-ad-non-high-availability-deployment"></a>Bereitstellen von AD (Bereitstellung ohne Hochverfügbarkeit)

Verwenden Sie die PowerShell DSC-Erweiterung, um [einen Server als AD-Domänencontroller zu erstellen](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/ad-non-ha), der folgende Ressourcen enthält:

* Ein virtuelles Netzwerk
* Ein Speicherkonto
* Einen externen Load Balancer
* Einen virtuellen Computer (VM), der als Domänencontroller für eine neue Gesamtstruktur mit einer einzelnen Domäne konfiguriert ist

## <a name="deploy-adsql-non-high-availability-deployment"></a>Bereitstellen von AD/SQL (Bereitstellung ohne Hochverfügbarkeit)

Verwenden Sie die PowerShell DSC-Erweiterung, um [einen eigenständigen Server für SQL Server 2014 zu erstellen](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2014-non-ha), der folgende Ressourcen enthält:

* Ein virtuelles Netzwerk
* Zwei Speicherkonten
* Einen externen Load Balancer
* Einen virtuellen Computer (VM), der als Domänencontroller für eine neue Gesamtstruktur mit einer einzelnen Domäne konfiguriert ist
* Einen virtuellen Computer, der als eigenständiger Server für SQL Server 2014 konfiguriert ist

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server

Verwenden Sie die PowerShell DSC-Erweiterung, um einen vorhandenen lokalen Konfigurations-Manager (LCM) für virtuelle Computer zu konfigurieren und auf einem DSC Pull-Server im Azure Automation-Konto zu registrieren.

## <a name="create-a-virtual-machine-from-a-user-image"></a>Erstellen eines virtuellen Computers von einem Benutzerimage

[Erstellen Sie einen virtuellen Computer aus einem Benutzerimage.](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-create-from-customimage) Diese Vorlage stellt auch ein virtuelles Netzwerk (mit DNS), eine öffentliche IP-Adresse und eine Netzwerkschnittstelle bereit.

## <a name="basic-virtual-machine"></a>Virtueller Computer im Tarif „Basic“

[Stellen Sie einen virtuellen Windows-Computer bereit](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-simple-windows-vm), der ein virtuelles Netzwerk (mit DNS), eine öffentliche IP-Adresse und eine Netzwerkschnittstelle umfasst.

## <a name="cancel-a-running-template-deployment"></a>Abbrechen der Bereitstellung einer Vorlage

Verwenden Sie zum Abbrechen der Ausführung einer Vorlagenbereitstellung das PowerShell-Cmdlet [Stop-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/stop-azurermresourcegroupdeployment).

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen von Vorlagen mit dem Portal](azure-stack-deploy-template-portal.md)
* [Übersicht über den Azure-Ressourcen-Manager](/azure/azure-resource-manager/resource-group-overview)
