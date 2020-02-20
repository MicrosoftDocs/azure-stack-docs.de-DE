---
title: Anwenden eines OEM-Updates (Originalgerätehersteller) auf Azure Stack Hub
description: In diesem Artikel wird das Anwenden eines OEM-Updates (Originalgerätehersteller) auf Azure Stack Hub erläutert.
author: ihenkel
ms.topic: article
ms.date: 10/15/2019
ms.author: inhenkel
ms.lastreviewed: 08/15/2019
ms.reviewer: ppacent
ms.openlocfilehash: 4ff26f9da9d15bd7a19cf1d54a0810feaeee4d8d
ms.sourcegitcommit: b2173b4597057e67de1c9066d8ed550b9056a97b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/20/2020
ms.locfileid: "77491202"
---
# <a name="apply-azure-stack-hub-original-equipment-manufacturer-oem-updates"></a>Anwenden von OEM-Updates (Originalgerätehersteller) auf Azure Stack Hub

Sie können OEM-Updates (Originalgerätehersteller) auf Ihre Azure Stack Hub-Hardwarekomponenten anwenden, um von Treiber- und Firmwareverbesserungen sowie Sicherheitspatches zu profitieren; die Beeinträchtigung der Benutzer ist dabei minimal. In diesem Artikel werden OEM-Updates erläutert, Sie erhalten OEM-Kontaktinformationen, und es wird beschrieben, wie ein OEM-Update angewendet wird.

## <a name="overview-of-oem-updates"></a>Übersicht über OEM-Updates

Neben Microsoft Azure Stack Hub-Updates veröffentlichen viele OEMs auch regelmäßig Updates für Ihre Azure Stack Hub-Hardware, z. B. Treiber- und Firmwareupdates. Diese werden als **OEM-Paketupdates** bezeichnet. Sehen Sie die [Azure Stack Hub-Dokumentation Ihres OEMs](#oem-contact-information) ein, um festzustellen, ob er OEM-Paketupdates veröffentlicht.

Diese OEM-Paketupdates werden in das Speicherkonto **updateadminaccount** hochgeladen und über das Azure Stack Hub-Administratorportal angewendet. Weitere Informationen finden Sie unter [Applying OEM Updates](#apply-oem-updates) (Anwenden von OEM-Updates).

Informieren Sie sich bei Ihrem Originalgerätehersteller (Original Equipment Manufacturer, OEM) nach dem jeweiligen Benachrichtigungsprozess, um sicherzustellen, dass die Benachrichtigungen zu OEM-Paketupdates Ihre Organisation erreichen.

Einige Hardwareanbieter fordern möglicherweise eine *Hardwareanbieter-VM*, auf welcher der interne Firmware-Updateprozess abgewickelt wird. Weitere Informationen finden Sie unter [Konfigurieren der Hardwareanbieter-VM](#configure-hardware-vendor-vm).

## <a name="oem-contact-information"></a>OEM-Kontaktinformationen 

In diesem Abschnitt finden sich OEM-Kontaktinformationen sowie Links zu OEM Azure Stack Hub-Referenzmaterialien.

| Hardwarepartner | Region | URL |
|------------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cisco | All | [Betriebshandbuch für Cisco Integrated System for Microsoft Azure Stack Hub](https://aka.ms/aa708e2)<br><br>[Software für UCS-verwaltete Server der UCS C-Serie für die Rackmontage](https://aka.ms/aa700rq) |
| Dell EMC | All | [Cloud für Microsoft Azure Stack Hub 14G (Konto und Anmeldung erforderlich)](https://support.emc.com/downloads/44615_Cloud-for-Microsoft-Azure-Stack-14G)<br><br>[Cloud für Microsoft Azure Stack Hub 13G (Konto und Anmeldung erforderlich)](https://support.emc.com/downloads/42238_Cloud-for-Microsoft-Azure-Stack-13G) |
| Fujitsu | JAPAN | [Fujitsu-Supportdesk für verwaltete Dienste (Konto und Anmeldung erforderlich)](https://eservice.fujitsu.com/supportdesk-web/) |
|  | EMEA und USA | [Fujitsu-Support für IT-Produkte und -Systeme](https://support.ts.fujitsu.com/IndexContact.asp?lng=COM&ln=no&LC=del) |
| HPE | All | [HPE ProLiant für Microsoft Azure Stack Hub](http://www.hpe.com/info/MASupdates) |
| Lenovo | All | [ThinkAgile SXM – bewährte Vorgehensweisen](https://datacentersupport.lenovo.com/us/en/solutions/ht505122)
| Wortmann |  | [OEM-/Firmwarepaket](https://aka.ms/AA6z600)<br>[Terra Azure Stack Hub-Dokumentation (einschließlich FRU)](https://aka.ms/aa6zktc)

## <a name="apply-oem-updates"></a>Anwenden von OEM-Updates

Führen Sie zum Anwenden von OEM-Paketen folgende Schritte aus:

> [!IMPORTANT]
> Vergewissern Sie sich vor dem Anwenden von Updates in Azure Stack Hub, dass Sie **alle** Schritte in der [Prüfliste für das Update](release-notes-checklist.md) ausgeführt und ein entsprechendes Wartungsfenster für den von Ihnen angesetzten Updatetyp geplant haben.

1. Sie müssen sich zu folgenden Zwecken an Ihren OEM wenden:
      - Bestimmen der aktuellen Version Ihres OEM-Pakets.  
      - Ermitteln der besten Methode zum Herunterladen des OEM-Pakets.  
2. Vor dem Anwenden eines OEM-Paketupdates sollten Sie immer den neuesten Azure Stack Hub-Hotfix anwenden, der für die aktuelle Azure Stack Hub-Version Ihres Systems verfügbar ist. Weitere Informationen zu Hotfixes finden Sie unter [Azure Stack Hub-Hotfixes](azure-stack-servicing-policy.md).
3. Bereiten Sie Ihr OEM-Paket mit den unter [Herunterladen der Updatepakete für integrierte Systeme](azure-stack-servicing-policy.md) beschriebenen Schritten vor.
4. Wenden Sie die Updates mit den unter [Anwenden von Updates in Azure Stack Hub](azure-stack-apply-updates.md) beschriebenen Schritten an.

## <a name="configure-hardware-vendor-vm"></a>Konfigurieren der Hardwareanbieter-VM

Einige Hardwareanbieter fordern möglicherweise eine VM, die den OEM-Updatevorgang unterstützt. Ihr Hardwareanbieter ist für das Erstellen dieser VMs verantwortlich. Zudem muss er dokumentieren, ob Sie beim Ausführen des Cmdlets **Set-OEMExternalVM** für **-VMType**`ProxyVM` oder `HardwareManager` angeben müssen und welche Anmeldeinformationen für **-Credential** verwendet werden müssen. Nachdem die VMs erstellt wurden, konfigurieren Sie sie mit der **Set-OEMExternalVM** vom privilegierten Endpunkt.

Weitere Informationen zum privilegierten Endpunkt in Azure Stack Hub finden Sie unter [Verwenden des privilegierten Endpunkts in Azure Stack Hub](azure-stack-privileged-endpoint.md).

1.  Greifen Sie auf den privilegierten Endpunkt zu.

    ```powershell  
    $cred = Get-Credential
    $session = New-PSSession -ComputerName <IP Address of ERCS>
    -ConfigurationName PrivilegedEndpoint -Credential $cred
    ```

2. Konfigurieren Sie die Hardwareanbieter-VM mithilfe des Cmdlets **Set-OEMExternalVM**. Das Cmdlet überprüft die IP-Adresse und die Anmeldeinformationen für den **-VMType** `ProxyVM`. Für den **-VMType** `HardwareManager` wird die Eingabe vom Cmdlet nicht überprüft. Der Wert des Parameters **-Credential**, der für **Set-OEMExternalVM** bereitgestellt wird, muss in der Dokumentation des Hardwareherstellers eindeutig angegeben werden.  Dabei handelt es sich NICHT um die CloudAdmin-Anmeldeinformationen, die mit dem privilegierten Endpunkt verwendet werden, oder um andere vorhandene Azure Stack Hub-Anmeldeinformationen.

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
