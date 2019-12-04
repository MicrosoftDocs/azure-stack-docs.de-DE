---
title: Ändern bestimmter Einstellungen in der Azure Stack-Switchkonfiguration | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, welche Einstellungen Sie in der Azure Stack Switchkonfiguration anpassen können. Nachdem der Originalgerätehersteller (OEM) die Konfiguration erstellt hat, ändern Sie diese nicht ohne Zustimmung des OEM oder des Microsoft Azure Stack-Technikteams.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: Femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: wamota
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 99d52dc7165fdd37a614c1396d7e5c1f0165e235
ms.sourcegitcommit: cefba8d6a93efaedff303d3c605b02bd28996c5d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74299124"
---
#  <a name="modify-specific-settings-on-your-azure-stack-switch-configuration"></a>Ändern bestimmter Einstellungen in der Azure Stack-Switchkonfiguration

Sie können einige Umgebungseinstellungen für Ihre Azure Stack-Switchkonfiguration ändern. Sie können ermitteln, welche der Einstellungen in der Vorlage geändert werden können, die von Ihrem Originalgerätehersteller (Original Equipment Manufacturer, OEM) erstellt wurde. In diesem Artikel werden die einzelnen anpassbaren Einstellungen beschrieben. Zudem wird erläutert, wie sich die Änderungen auf Azure Stack auswirken können. Zu diesen Einstellungen gehören die Aktualisierung von Kennwörtern, Syslog-Server, SNMP-Überwachung, Authentifizierung und die Zugriffssteuerungsliste. 

Während der Bereitstellung der Azure Stack-Lösung erstellt der Originalgerätehersteller (Original Equipment Manufacturer, OEM) die Switchkonfiguration sowohl für TOR-Instanzen als auch für BMC. Der OEM verwendet das Azure Stack-Automatisierungstool, um zu überprüfen, ob die erforderlichen Konfigurationen auf diesen Geräten ordnungsgemäß festgelegt sind. Die Konfigurationen basieren auf den Informationen in Ihrem [Bereitstellungsarbeitsblatt](azure-stack-deployment-worksheet.md) für Azure Stack. Nachdem der OEM die Konfiguration erstellt hat, ändern Sie diese **nicht** ohne die Zustimmung des OEM oder des Microsoft Azure Stack-Technikteams. Eine Änderung an der Netzwerkgerätekonfiguration kann sich erheblich auf den Betrieb oder die Behebung von Netzwerkproblemen in ihrer Azure Stack-Instanz auswirken.

Es gibt jedoch einige Werte, die in der Konfiguration der Netzwerkswitches hinzugefügt, entfernt oder geändert werden können.

>[!Warning]  
> Ändern Sie die Konfiguration **nicht** ohne Zustimmung des OEM oder des Microsoft Azure Stack-Technikteams. Eine Änderung an der Netzwerkgerätekonfiguration kann sich erheblich auf den Betrieb oder die Behebung von Netzwerkproblemen in ihrer Azure Stack-Instanz auswirken.
>
> Weitere Informationen zu diesen Funktionen in Ihrem Netzwerkgerät und dazu, wie diese Änderungen vorgenommen werden, erhalten Sie bei Ihrem OEM-Hardwareanbieter oder beim Microsoft-Support. Der OEM hat die Konfigurationsdatei mithilfe des Automatisierungstools auf der Grundlage Ihres Bereitstellungsarbeitsblatts für Azure Stack erstellt. 

## <a name="password-update"></a>Aktualisieren von Kennwörtern

Der Operator kann das Kennwort jedes Benutzers auf den Netzwerkswitches jederzeit aktualisieren. Es ist nicht erforderlich, Informationen im Azure Stack-System zu ändern oder die Schritte zum [Rotieren von Geheimnissen in Azure Stack](azure-stack-rotate-secrets.md) auszuführen.

## <a name="syslog-server"></a>Syslog-Server

Operatoren können die Switchprotokolle an einen Syslog-Server in Ihrem Datencenter umleiten. Stellen Sie mit dieser Konfiguration sicher, dass die Protokolle für einen bestimmten Zeitpunkt für die Problembehandlung verwendet werden können. In der Standardeinstellung werden die Protokolle auf den Switches gespeichert. Deren Kapazität zum Speichern von Protokollen ist jedoch beschränkt. Im Abschnitt [Aktualisierungen von Zugriffssteuerungslisten](#access-control-list-updates) finden Sie eine Übersicht dazu, wie die Berechtigungen für den Verwaltungszugriff auf Switches konfiguriert werden.

## <a name="snmp-monitoring"></a>SNMP-Überwachung

Der Operator kann das Simple Network Management Protocol (SNMP) v2 oder v3 konfigurieren, um die Netzwerkgeräte zu überwachen und Traps an eine Netzwerküberwachungsanwendung im Datencenter zu senden. Verwenden Sie aus Sicherheitsgründen SNMPv3, da es höhere Sicherheit als v2 bietet. Informationen zu den MIBs und der erforderlichen Konfiguration erhalten Sie bei Ihrem OEM-Hardwareanbieter. Im Abschnitt [Aktualisierungen von Zugriffssteuerungslisten](#access-control-list-updates) finden Sie eine Übersicht dazu, wie die Berechtigungen für den Verwaltungszugriff auf Switches konfiguriert werden.

## <a name="authentication"></a>Authentication

Der Operator kann entweder RADIUS oder TACACS konfigurieren, um die Authentifizierung auf den Netzwerkgeräten zu verwalten. Informationen zu den unterstützten Methoden und der erforderlichen Konfiguration erhalten Sie bei Ihrem OEM-Hardwareanbieter.  Im Abschnitt [Aktualisierungen von Zugriffssteuerungslisten](#access-control-list-updates) finden Sie eine Übersicht dazu, wie die Berechtigungen für den Verwaltungszugriff auf Switches konfiguriert werden.

## <a name="access-control-list-updates"></a>Aktualisierungen von Zugriffssteuerungslisten

> [!NOTE]
> Ab 1910 befindet sich auf dem Arbeitsblatt für die Bereitstellung ein neues Feld für **zugelassene Netzwerke**, das die manuellen Schritte ersetzt, die für das Zulassen des Zugriffs auf Verwaltungsschnittstellen für Netzwerkgeräte und den Hardwarelebenszyklus-Host (HLH) von einem Netzwerkbereich vertrauenswürdiger Datencenter erforderlich sind. Weitere Informationen zu dieser neuen Funktion finden Sie unter [Planen der Netzwerkintegration für Azure Stack](azure-stack-network.md#permitted-networks).

Der Operator kann einige Zugriffssteuerungslisten (ACLs) so ändern, dass der Zugriff auf Verwaltungsschnittstellen für Netzwerkgeräte und den Hardwarelebenszyklus-Host (HLH) von einem Netzwerkbereich vertrauenswürdiger Datencenter gestattet wird. Mithilfe der Zugriffssteuerungsliste kann der Operator seinen Jumpbox-VMs für die Verwaltung innerhalb eines bestimmten Netzwerkbereichs den Zugriff auf die Schnittstelle für die Switch-Verwaltung, das HLH-Betriebssystem und den HLH-BMC gewähren.

## <a name="next-steps"></a>Nächste Schritte

[Integration des Azure Stack-Datencenters – DNS](azure-stack-integrate-dns.md)
