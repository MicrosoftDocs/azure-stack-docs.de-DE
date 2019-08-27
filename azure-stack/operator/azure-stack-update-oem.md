---
title: Anwenden eines OEM-Updates (Originalgerätehersteller) auf Azure Stack | Microsoft-Dokumentation
description: In diesem Artikel wird das Anwenden eines OEM-Updates (Originalgerätehersteller) auf Azure Stack erläutert.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2019
ms.author: mabrigg
ms.lastreviewed: 08/15/2019
ms.reviewer: ppacent
ms.openlocfilehash: 1342eb503abb81308740c0103b1d54887a46cf85
ms.sourcegitcommit: f62d58ae724020a24fa5905b6663abb5f1d62178
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69520925"
---
# <a name="apply-azure-stack-original-equipment-manufacturer-oem-updates"></a>Anwenden von OEM-Updates (Originalgerätehersteller) auf Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

Sie können OEM-Updates (Originalgerätehersteller) auf Ihre Azure Stack-Hardwarekomponenten anwenden, um von Treiber- und Firmwareverbesserungen sowie Sicherheitspatches zu profitieren; die Beeinträchtigung Ihrer Benutzer ist dabei minimal. In diesem Artikel werden OEM-Updates erläutert, Sie erhalten OEM-Kontaktinformationen, und es wird beschrieben, wie ein OEM-Update angewendet wird.

## <a name="overview-of-oem-updates"></a>Übersicht über OEM-Updates

Neben Microsoft Azure Stack-Updates veröffentlichen viele OEMs auch regelmäßig Updates für Ihre Azure Stack-Hardware, z. B. Treiber- und Firmwareupdates. Diese werden als **OEM-Paketupdates** bezeichnet. Sehen Sie die [Azure Stack-Dokumentation Ihres OEMs](#oem-contact-information) ein, um festzustellen, ob er OEM-Paketupdates veröffentlicht.

Diese OEM-Paketupdates werden in das Speicherkonto **updateadminaccount** hochgeladen und über das Azure Stack-Administratorportal angewendet. Weitere Informationen finden Sie unter [Applying OEM Updates](#apply-oem-updates) (Anwenden von OEM-Updates).

Informieren Sie sich bei Ihrem Originalgerätehersteller (Original Equipment Manufacturer, OEM) nach dem jeweiligen Benachrichtigungsprozess, um sicherzustellen, dass die Benachrichtigungen zu OEM-Paketupdates Ihre Organisation erreichen.

Einige Hardwareanbieter fordern möglicherweise eine *Hardwareanbieter-VM*, auf welcher der interne Firmware-Updateprozess abgewickelt wird. Weitere Informationen finden Sie unter [Konfigurieren der Hardwareanbieter-VM](#configure-hardware-vendor-vm).

## <a name="oem-contact-information"></a>OEM-Kontaktinformationen 

In diesem Abschnitt finden sich OEM-Kontaktinformationen sowie Links zu OEM Azure Stack-Referenzmaterialien.

| Hardwarepartner | Region | URL |
|------------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cisco | Alle | [Betriebshandbuch für Cisco Integrated System for Microsoft Azure Stack](https://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/azure-stack/b_Azure_Stack_Operations_Guide_4-0/b_Azure_Stack_Operations_Guide_4-0_chapter_00.html#concept_wks_t1q_wbb)<br><br>[Versionshinweise für Cisco Integrated System for Microsoft Azure Stack](https://www.cisco.com/c/en/us/support/servers-unified-computing/ucs-c-series-rack-mount-ucs-managed-server-software/products-release-notes-list.html) |
| Dell EMC | Alle | [Cloud für Microsoft Azure Stack 14G (Konto und Anmeldung erforderlich)](https://support.emc.com/downloads/44615_Cloud-for-Microsoft-Azure-Stack-14G)<br><br>[Cloud für Microsoft Azure Stack 13G (Konto und Anmeldung erforderlich)](https://support.emc.com/downloads/42238_Cloud-for-Microsoft-Azure-Stack-13G) |
| Fujitsu | JAPAN | [Fujitsu-Supportdesk für verwaltete Dienste (Konto und Anmeldung erforderlich)](https://eservice.fujitsu.com/supportdesk-web/) |
|  | EMEA | [Fujitsu-Support für IT-Produkte und -Systeme](https://support.ts.fujitsu.com/IndexContact.asp?lng=COM&ln=no&LC=del) |
|  | EU | [Fujitsu MySupport (Konto und Anmeldung erforderlich)](https://support.ts.fujitsu.com/IndexMySupport.asp) |
| HPE | Alle | [HPE ProLiant für Microsoft Azure Stack](http://www.hpe.com/info/MASupdates) |
| Lenovo | Alle | [ThinkAgile SXM – bewährte Vorgehensweisen](https://datacentersupport.lenovo.com/us/en/solutions/ht505122)
| Wortmann |  | [OEM-/Firmwarepaket](https://drive.terracloud.de/dl/fiTdTb66mwDAJWgUXUW8KNsd/OEM)<br>[Terra Azure Stack-Dokumentation (einschließlich FRU)](https://drive.terracloud.de/dl/fiWGZwCySZSQyNdykXCFiVCR/TerraAzSDokumentation)

## <a name="apply-oem-updates"></a>Anwenden von OEM-Updates

Führen Sie zum Anwenden von OEM-Paketen folgende Schritte aus:

1. Sie müssen sich zu folgenden Zwecken an Ihren OEM wenden:
      - Bestimmen der aktuellen Version Ihres OEM-Pakets.  
      - Ermitteln der besten Methode zum Herunterladen des OEM-Pakets.  
2. Bereiten Sie Ihr OEM-Paket mit den unter [Herunterladen der Updatepakete für integrierte Systeme](azure-stack-servicing-policy.md#download-update-packages-for-integrated-systems) beschriebenen Schritten vor.
3. Wenden Sie die Updates mit den unter [Anwenden von Updates in Azure Stack](azure-stack-apply-updates.md) beschriebenen Schritten an.

## <a name="configure-hardware-vendor-vm"></a>Konfigurieren der Hardwareanbieter-VM

Einige Hardwareanbieter fordern möglicherweise eine VM, die den OEM-Updatevorgang unterstützt. Ihr Hardwareanbieter ist für das Erstellen dieser VMs verantwortlich. Zudem muss er dokumentieren, ob Sie `ProxyVM` oder `HardwareManager` für **-VMType** angeben müssen, wenn Sie das Cmdlet **Set-OEMExternalVM** ausführen. Nachdem die VMs erstellt wurden, konfigurieren Sie sie mit der **Set-OEMExternalVM** vom privilegierten Endpunkt.

Weitere Informationen zum privilegierten Endpunkt in Azure Stack finden Sie unter [Verwenden des privilegierten Endpunkts in Azure Stack](azure-stack-privileged-endpoint.md).

1.  Greifen Sie auf den privilegierten Endpunkt zu.

    ```powershell  
    $cred = Get-Credential
    $session = New-PSSession -ComputerName <IP Address of ERCS>
    -ConfigurationName PrivilegedEndpoint -Credential $cred
    ```

2. Konfigurieren Sie die Hardwareanbieter-VM mithilfe des Cmdlets **Set-OEMExternalVM**. Das Cmdlet überprüft die IP-Adresse und die Anmeldeinformationen für **-VMType** `ProxyVM`. Für **-VMType** `HardwareManager` wird die Eingabe vom Cmdlet nicht überprüft.

    ```powershell  
    
    Invoke-Command -Session $session
        { 
    Set-OEMExternalVM -VMType <Either "ProxyVM" or "HardwareManager">
        -IPAddress <IP Address of hardware vendor VM>
        }
    ```

## <a name="next-steps"></a>Nächste Schritte

[Azure Stack-Updates](azure-stack-updates.md)