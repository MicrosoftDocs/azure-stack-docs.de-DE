---
title: Ändern bestimmter Einstellungen in der Azure Stack Hub-Switchkonfiguration
description: In diesem Artikel erfahren Sie, welche Einstellungen Sie in der Azure Stack Hub-Switchkonfiguration anpassen können. Nachdem der Originalgerätehersteller (OEM) die Konfiguration erstellt hat, ändern Sie diese nicht ohne Zustimmung des OEM oder des Microsoft Azure Stack Hub-Technikteams.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: wamota
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 19964b761478bb210d3377b177f05f708697167b
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "78368718"
---
#  <a name="modify-specific-settings-on-your-azure-stack-hub-switch-configuration"></a>Ändern bestimmter Einstellungen in der Azure Stack Hub-Switchkonfiguration

Sie können einige Umgebungseinstellungen für Ihre Azure Stack Hub-Switchkonfiguration ändern. Sie können ermitteln, welche der Einstellungen in der Vorlage geändert werden können, die von Ihrem Originalgerätehersteller (Original Equipment Manufacturer, OEM) erstellt wurde. In diesem Artikel werden die einzelnen anpassbaren Einstellungen beschrieben. Zudem wird erläutert, wie sich die Änderungen auf Azure Stack Hub auswirken können. Zu diesen Einstellungen gehören die Aktualisierung von Kennwörtern, Syslog-Server, SNMP-Überwachung, Authentifizierung und die Zugriffssteuerungsliste. 

Während der Bereitstellung der Azure Stack Hub-Lösung erstellt der Originalgerätehersteller (Original Equipment Manufacturer, OEM) die Switchkonfiguration sowohl für TOR-Instanzen als auch für BMC. Der OEM verwendet das Azure Stack Hub-Automatisierungstool, um zu überprüfen, ob die erforderlichen Konfigurationen auf diesen Geräten ordnungsgemäß festgelegt sind. Die Konfigurationen basieren auf den Informationen in Ihrem [Bereitstellungsarbeitsblatt](azure-stack-deployment-worksheet.md) für Azure Stack Hub. Nachdem der OEM die Konfiguration erstellt hat, ändern Sie diese **nicht** ohne die Zustimmung des OEM oder des Microsoft Azure Stack Hub-Technikteams. Eine Änderung an der Netzwerkgerätekonfiguration kann sich erheblich auf den Betrieb oder die Behebung von Netzwerkproblemen in ihrer Azure Stack Hub-Instanz auswirken.

Es gibt jedoch einige Werte, die in der Konfiguration der Netzwerkswitches hinzugefügt, entfernt oder geändert werden können.

>[!Warning]  
> Ändern Sie die Konfiguration **nicht** ohne Zustimmung des OEM oder des Microsoft Azure Stack Hub-Technikteams. Eine Änderung an der Netzwerkgerätekonfiguration kann sich erheblich auf den Betrieb oder die Behebung von Netzwerkproblemen in ihrer Azure Stack Hub-Instanz auswirken.
>
> Weitere Informationen zu diesen Funktionen in Ihrem Netzwerkgerät und dazu, wie diese Änderungen vorgenommen werden, erhalten Sie bei Ihrem OEM-Hardwareanbieter oder beim Microsoft-Support. Der OEM hat die Konfigurationsdatei mithilfe des Automatisierungstools auf der Grundlage Ihres Bereitstellungsarbeitsblatts für Azure Stack Hub erstellt. 

## <a name="password-update"></a>Aktualisieren von Kennwörtern

Der Operator kann das Kennwort jedes Benutzers auf den Netzwerkswitches jederzeit aktualisieren. Es ist nicht erforderlich, Informationen im Azure Stack Hub-System zu ändern oder die Schritte zum [Rotieren von Geheimnissen in Azure Stack Hub](azure-stack-rotate-secrets.md) auszuführen.

## <a name="syslog-server"></a>Syslog-Server

Operatoren können die Switchprotokolle an einen Syslog-Server in Ihrem Datencenter umleiten. Stellen Sie mit dieser Konfiguration sicher, dass die Protokolle für einen bestimmten Zeitpunkt für die Problembehandlung verwendet werden können. In der Standardeinstellung werden die Protokolle auf den Switches gespeichert. Deren Kapazität zum Speichern von Protokollen ist jedoch beschränkt. Im Abschnitt [Aktualisierungen von Zugriffssteuerungslisten](#access-control-list-updates) finden Sie eine Übersicht dazu, wie die Berechtigungen für den Verwaltungszugriff auf Switches konfiguriert werden.

## <a name="snmp-monitoring"></a>SNMP-Überwachung

Der Operator kann das Simple Network Management Protocol (SNMP) v2 oder v3 konfigurieren, um die Netzwerkgeräte zu überwachen und Traps an eine Netzwerküberwachungsanwendung im Datencenter zu senden. Verwenden Sie aus Sicherheitsgründen SNMPv3, da es höhere Sicherheit als v2 bietet. Informationen zu den MIBs und der erforderlichen Konfiguration erhalten Sie bei Ihrem OEM-Hardwareanbieter. Im Abschnitt [Aktualisierungen von Zugriffssteuerungslisten](#access-control-list-updates) finden Sie eine Übersicht dazu, wie die Berechtigungen für den Verwaltungszugriff auf Switches konfiguriert werden.

## <a name="authentication"></a>Authentifizierung

Der Operator kann entweder RADIUS oder TACACS konfigurieren, um die Authentifizierung auf den Netzwerkgeräten zu verwalten. Informationen zu den unterstützten Methoden und der erforderlichen Konfiguration erhalten Sie bei Ihrem OEM-Hardwareanbieter.  Im Abschnitt [Aktualisierungen von Zugriffssteuerungslisten](#access-control-list-updates) finden Sie eine Übersicht dazu, wie die Berechtigungen für den Verwaltungszugriff auf Switches konfiguriert werden.

## <a name="access-control-list-updates"></a>Aktualisierungen von Zugriffssteuerungslisten

> [!NOTE]
> Ab 1910 befindet sich auf dem Arbeitsblatt für die Bereitstellung ein neues Feld für **zugelassene Netzwerke**, das die manuellen Schritte ersetzt, die für das Zulassen des Zugriffs auf Verwaltungsschnittstellen für Netzwerkgeräte und den Hardwarelebenszyklus-Host (HLH) von einem Netzwerkbereich vertrauenswürdiger Datencenter erforderlich sind. Weitere Informationen zu dieser neuen Funktion finden Sie unter [Planen der Netzwerkintegration für Azure Stack Hub](azure-stack-network.md#permitted-networks).

Der Operator kann einige Zugriffssteuerungslisten (ACLs) so ändern, dass der Zugriff auf Verwaltungsschnittstellen für Netzwerkgeräte und den Hardwarelebenszyklus-Host (HLH) von einem Netzwerkbereich vertrauenswürdiger Datencenter gestattet wird. Mithilfe der Zugriffssteuerungsliste kann der Operator seinen Jumpbox-VMs für die Verwaltung innerhalb eines bestimmten Netzwerkbereichs den Zugriff auf die Schnittstelle für die Switch-Verwaltung, das HLH-Betriebssystem und den HLH-BMC gewähren.

## <a name="next-steps"></a>Nächste Schritte

[Integration des Azure Stack Hub-Datencenters – DNS](azure-stack-integrate-dns.md)
