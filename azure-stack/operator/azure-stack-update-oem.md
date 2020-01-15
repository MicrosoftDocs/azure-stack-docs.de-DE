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
ms.date: 10/15/2019
ms.author: mabrigg
ms.lastreviewed: 08/15/2019
ms.reviewer: ppacent
ms.openlocfilehash: 7c2ddd8c692efa423083b95eb7fb107dd763bab6
ms.sourcegitcommit: 1cb84d8417367b553194614d53a477a94a8e9be9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75755161"
---
# <a name="apply-azure-stack-hub-original-equipment-manufacturer-oem-updates"></a>Anwenden von OEM-Updates (Originalgerätehersteller) auf Azure Stack Hub

*Anwendungsbereich: Integrierte Azure Stack Hub-Systeme*

Sie können OEM-Updates (Originalgerätehersteller) auf Ihre Azure Stack Hub-Hardwarekomponenten anwenden, um von Treiber- und Firmwareverbesserungen sowie Sicherheitspatches zu profitieren; die Beeinträchtigung der Benutzer ist dabei minimal. In diesem Artikel werden OEM-Updates erläutert, Sie erhalten OEM-Kontaktinformationen, und es wird beschrieben, wie ein OEM-Update angewendet wird.

## <a name="overview-of-oem-updates"></a>Übersicht über OEM-Updates

Neben Microsoft Azure Stack-Updates veröffentlichen viele OEMs auch regelmäßig Updates für Ihre Azure Stack-Hardware, z. B. Treiber- und Firmwareupdates. Diese werden als **OEM-Paketupdates** bezeichnet. Sehen Sie die [Azure Stack-Dokumentation Ihres OEMs](#oem-contact-information) ein, um festzustellen, ob er OEM-Paketupdates veröffentlicht.

Diese OEM-Paketupdates werden in das Speicherkonto **updateadminaccount** hochgeladen und über das Azure Stack Hub-Administratorportal angewendet. Weitere Informationen finden Sie unter [Applying OEM Updates](#apply-oem-updates) (Anwenden von OEM-Updates).

Informieren Sie sich bei Ihrem Originalgerätehersteller (Original Equipment Manufacturer, OEM) nach dem jeweiligen Benachrichtigungsprozess, um sicherzustellen, dass die Benachrichtigungen zu OEM-Paketupdates Ihre Organisation erreichen.

Einige Hardwareanbieter fordern möglicherweise eine *Hardwareanbieter-VM*, auf welcher der interne Firmware-Updateprozess abgewickelt wird. Weitere Informationen finden Sie unter [Konfigurieren der Hardwareanbieter-VM](#configure-hardware-vendor-vm).

## <a name="oem-contact-information"></a>OEM-Kontaktinformationen 

In diesem Abschnitt finden sich OEM-Kontaktinformationen sowie Links zu OEM Azure Stack-Referenzmaterialien.

| Hardwarepartner | Region | URL |
|------------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cisco | All | [Betriebshandbuch für Cisco Integrated System for Microsoft Azure Stack](https://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/azure-stack/b_Azure_Stack_Operations_Guide_4-0/b_Azure_Stack_Operations_Guide_4-0_chapter_01000.html)<br><br>[Software für UCS-verwaltete Server der UCS C-Serie für die Rackmontage](https://software.cisco.com/download/home/283862063/type/286320368/release/2.0(0)) |
| Dell EMC | All | [Cloud für Microsoft Azure Stack 14G (Konto und Anmeldung erforderlich)](https://support.emc.com/downloads/44615_Cloud-for-Microsoft-Azure-Stack-14G)<br><br>[Cloud für Microsoft Azure Stack 13G (Konto und Anmeldung erforderlich)](https://support.emc.com/downloads/42238_Cloud-for-Microsoft-Azure-Stack-13G) |
| Fujitsu | JAPAN | [Fujitsu-Supportdesk für verwaltete Dienste (Konto und Anmeldung erforderlich)](https://eservice.fujitsu.com/supportdesk-web/) |
|  | EMEA und USA | [Fujitsu-Support für IT-Produkte und -Systeme](https://support.ts.fujitsu.com/IndexContact.asp?lng=COM&ln=no&LC=del) |
| HPE | All | [HPE ProLiant für Microsoft Azure Stack](http://www.hpe.com/info/MASupdates) |
| Lenovo | All | [ThinkAgile SXM – bewährte Vorgehensweisen](https://datacentersupport.lenovo.com/us/en/solutions/ht505122)
| Wortmann |  | [OEM-/Firmwarepaket](https://drive.terracloud.de/dl/fiTdTb66mwDAJWgUXUW8KNsd/OEM)<br>[Terra Azure Stack-Dokumentation (einschließlich FRU)](https://drive.terracloud.de/dl/fiWGZwCySZSQyNdykXCFiVCR/TerraAzSDokumentation)

## <a name="apply-oem-updates"></a>Anwenden von OEM-Updates

Führen Sie zum Anwenden von OEM-Paketen folgende Schritte aus:

1. Sie müssen sich zu folgenden Zwecken an Ihren OEM wenden:
      - Bestimmen der aktuellen Version Ihres OEM-Pakets.  
      - Ermitteln der besten Methode zum Herunterladen des OEM-Pakets.  
2. Vor dem Anwenden eines OEM-Paketupdates sollten Sie immer den neuesten Azure Stack-Hotfix anwenden, der für die aktuelle Azure Stack Hub-Version Ihres Systems verfügbar ist. Weitere Informationen zu Hotfixes finden Sie unter [Azure Stack Hub-Hotfixes](https://docs.microsoft.com/azure-stack/operator/azure-stack-servicing-policy).
3. Bereiten Sie Ihr OEM-Paket mit den unter [Herunterladen der Updatepakete für integrierte Systeme](azure-stack-servicing-policy.md) beschriebenen Schritten vor.
4. Wenden Sie die Updates mit den unter [Anwenden von Updates in Azure Stack Hub](azure-stack-apply-updates.md) beschriebenen Schritten an.

## <a name="configure-hardware-vendor-vm"></a>Konfigurieren der Hardwareanbieter-VM

Einige Hardwareanbieter fordern möglicherweise eine VM, die den OEM-Updatevorgang unterstützt. Ihr Hardwareanbieter ist für das Erstellen dieser VMs verantwortlich. Zudem muss er dokumentieren, ob Sie beim Ausführen des Cmdlets **Set-OEMExternalVM** für **-VMType**`ProxyVM` oder `HardwareManager` angeben müssen und welche Anmeldeinformationen für **-Credential** verwendet werden müssen. Nachdem die VMs erstellt wurden, konfigurieren Sie sie mit der **Set-OEMExternalVM** vom privilegierten Endpunkt.

Weitere Informationen zum privilegierten Endpunkt in Azure Stack finden Sie unter [Verwenden des privilegierten Endpunkts in Azure Stack](azure-stack-privileged-endpoint.md).

1.  Greifen Sie auf den privilegierten Endpunkt zu.

    ```powershell  
    $cred = Get-Credential
    $session = New-PSSession -ComputerName <IP Address of ERCS>
    -ConfigurationName PrivilegedEndpoint -Credential $cred
    ```

2. Konfigurieren Sie die Hardwareanbieter-VM mithilfe des Cmdlets **Set-OEMExternalVM**. Das Cmdlet überprüft die IP-Adresse und die Anmeldeinformationen für den **-VMType** `ProxyVM`. Für den **-VMType** `HardwareManager` wird die Eingabe vom Cmdlet nicht überprüft. Der Wert des Parameters **-Credential**, der für **Set-OEMExternalVM** bereitgestellt wird, muss in der Dokumentation des Hardwareherstellers eindeutig angegeben werden.  Dabei handelt es sich NICHT um die CloudAdmin-Anmeldeinformationen, die mit dem privilegierten Endpunkt verwendet werden, oder um andere vorhandene Azure Stack-Anmeldeinformationen.

    ```powershell  
    $VmCred = Get-Credential
    Invoke-Command -Session $session
        { 
    Set-OEMExternalVM -VMType <Either "ProxyVM" or "HardwareManager">
        -IPAddress <IP Address of hardware vendor VM> -Credential $using:VmCred
        }
    ```

## <a name="next-steps"></a>Nächste Schritte

[Azure Stack Hub-Updates](azure-stack-updates.md)
