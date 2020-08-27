---
title: Problembehandlung für Validation-as-a-Service
titleSuffix: Azure Stack Hub
description: Problembehandlung für Validation-as-a-Service in Azure Stack Hub
author: mattbriggs
ms.topic: article
ms.date: 08/24/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 06e9958d2311d456e977d72fe37eb88d18008092
ms.sourcegitcommit: 4922a14fdbc8a3b67df065336e8a21a42f224867
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2020
ms.locfileid: "88764799"
---
# <a name="troubleshoot-validation-as-a-service"></a>Problembehandlung für Validation-as-a-Service

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Die folgenden allgemeinen Probleme treten unabhängig von Softwareversionen und deren Lösungen auf.

## <a name="local-agent"></a>Lokaler Agent

### <a name="the-portal-shows-local-agent-in-debug-mode"></a>Das Portal zeigt den lokalen Agent im Debugmodus.

Dieses Problem tritt wahrscheinlich auf, weil der Agent aufgrund einer instabilen Netzwerkverbindung keine Heartbeats an den Dienst senden kann. Ein Takt wird alle fünf Minuten gesendet. Wenn der Dienst innerhalb von 15 Minuten keinen Heartbeat erhält, betrachtet der Dienst den Agent als inaktiv, und es werden keine weiteren Tests für ihn geplant. Überprüfen Sie die Fehlermeldung in der *Agenthost.log*-Datei im Verzeichnis, in dem der Agent gestartet wurde.

> [!Note]
> Zwar werden alle Tests, die bereits auf dem Agent ausgeführt werden, auch weiterhin ausgeführt, allerdings treten beim Aktualisieren von Teststatus oder Hochladen von Protokollen Fehler auf, wenn der Heartbeat vor Ende des Tests nicht wiederhergestellt wird. Der Test wird immer als **wird ausgeführt** angezeigt und muss abgebrochen werden.

### <a name="agent-process-on-machine-was-shut-down-while-executing-test-what-to-expect"></a>Der Agent-Prozess auf dem Computer wurde während der Ausführung des Tests beendet. Was können Sie erwarten?

Wenn der Agent-Prozess nicht ordnungsgemäß heruntergefahren wird, wird der auf ihm ausgeführte Test weiterhin mit dem Status **Wird ausgeführt** angezeigt. Ein Beispiel für nicht ordnungsgemäßes Herunterfahren ist der Neustart eines Computers und das Abbrechen des Prozesses. (Das Drücken von STRG+C im Agent-Fenster gilt als ordnungsgemäßes Herunterfahren.) Wenn der Agent neu gestartet wird, aktualisiert der Agent den Status des Tests nach **abgebrochen**. Wenn der Agent nicht neu gestartet wird, wird der Test als **wird ausgeführt** angezeigt, und Sie müssen den Test manuell abbrechen.

> [!Note]
> Tests in einem Workflow werden für eine sequenzielle Ausführung geplant. **Ausstehende** Tests werden erst ausgeführt, wenn Tests im Zustand **Wird ausgeführt** im gleichen Workflow abgeschlossen werden.

## <a name="vm-images"></a>VM-Images

### <a name="failure-occurs-when-uploading-vm-image-in-the-vaasprereq-script"></a>Fehler beim Hochladen eines VM-Images im Skript `VaaSPreReq`
Lesen Sie den Abschnitt **Behandeln langsamer Netzwerkverbindung** weiter unten. Dort finden Sie manuelle Schritte zum Hochladen der VM-Images in den Azure Stack-Stempel.

### <a name="handle-slow-network-connectivity"></a>Behandeln langsamer Netzwerkverbindung

#### <a name="1-verify-that-the-environment-is-healthy"></a>1. Überprüfen der Umgebung auf Fehlerfreiheit

1. Überprüfen Sie in der DVM/Jumpbox, ob Sie sich mit den Administratoranmeldeinformationen beim Verwaltungsportal anmelden können.

2. Vergewissern Sie sich, dass keine Benachrichtigungen oder Warnungen vorhanden sind.

3. Wenn die Umgebung fehlerfrei ist, laden Sie die erforderlichen VM-Images manuell für VaaS-Testläufe hoch, indem Sie die Schritte im Abschnitt unten ausführen:

<!-- This is from the appendix to the Deploy local agent topic. -->

#### <a name="2-download-pir-image-to-local-share-in-case-of-slow-network-traffic"></a>2. Herunterladen eines PIR-Images in die lokale Freigabe bei einem langsamen Netzwerkdatenverkehr

1. Laden Sie AzCopy hier herunter: [vaasexternaldependencies(AzCopy)](https://vaasexternaldependencies.blob.core.windows.net/prereqcomponents/AzCopy.zip).

2. Extrahieren Sie „AzCopy.zip“, und wechseln Sie zum Verzeichnis mit `AzCopy.exe`.

3. Öffnen Sie Windows PowerShell über eine Eingabeaufforderung mit erhöhten Rechten. Führen Sie die folgenden Befehle aus:

```powershell  
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterFullBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterCoreBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'WindowsServer2012R2DatacenterBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1404LTS.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1604-20170619.1.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'OpenLogic-CentOS-69-20180105.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Debian8_latest.vhd' /NC:12 /V:azcopylog.log /Y
```

> [!Note]  
> LocalFileShare ist der Freigabepfad oder der lokale Pfad.

#### <a name="3-verifying-pir-image-file-hash-value"></a>3. Überprüfen des Hashwerts von PIR-Imagedateien

Sie können das **Get-HashFile**-Cmdlet verwenden, um den Hashwert für die heruntergeladenen PIR-Imagedateien abzurufen und die Integrität der Images zu überprüfen.

| Dateiname | SHA256 |
|---------------------------------------|------------------------------------------------------------------|
| Server2016DatacenterFullBYOL.vhd | 6ED58DCA666D530811A1EA563BA509BF9C29182B902D18FCA03C7E0868F733E9 |
| WindowsServer2012R2DatacenterBYOL.vhd | 9792CBF742870B1730B9B16EA814C683A8415EFD7601DDB6D5A76D0964767028 |
| Server2016DatacenterCoreBYOL.vhd | 5E80E1A6721A48A10655E6154C1B90E320DF5558487D6A0D7BFC7DCD32C4D9A5 |
| Ubuntu1404LTS.vhd | B24CDD12352AAEBC612A4558AB9E80F031A2190E46DCB459AF736072742E20E0 |
| Ubuntu1604-20170619.1.vhd | C481B88B60A01CBD5119A3F56632A2203EE5795678D3F3B9B764FFCA885E26CB |
| OpenLogic-CentOS-69-20180105.vhd | C8B874FE042E33B488110D9311AF1A5C7DC3B08E6796610BF18FDD6728C7913C |
| Debian8_latest.vhd | 06F8C11531E195D0C90FC01DFF5DC396BB1DD73A54F8252291ED366CACD996C1 |

#### <a name="4-upload-vm-images-to-a-storage-account"></a>4. Hochladen der VM-Images in ein Speicherkonto

1. Verwenden Sie ein vorhandenes Speicherkonto, oder erstellen Sie ein neues in Azure.

2. Erstellen Sie einen Container, in den die Images hochgeladen werden.

3. Laden Sie die VM-Images aus dem [*LocalFileShare*] oben (in den Sie die VM-Images hochgeladen haben) in den soeben erstellten Container hoch.
    > [!IMPORTANT]
    > Ändern Sie „Öffentliche Zugriffsebene“ des Containers in „Blob (anonymer Lesezugriff nur für Blobs)“.

#### <a name="5-upload-vm-images-to-azure-stack-environment"></a>5. Hochladen der VM-Images in die Azure Stack Umgebung

1. Melden Sie sich als Dienstadministrator beim Verwaltungsportal an. Die Administratorportal-URL finden Sie im ECE-Store und in Ihrer Stempelinformationsdatei. Anweisungen hierzu finden Sie unter [Umgebungsparameter](azure-stack-vaas-parameters.md#environment-parameters).

2. Wählen Sie **Weitere Dienste** > **Ressourcenanbieter** > **Compute** > **VM-Images** aus.

3. Wählen Sie die Schaltfläche **+ Hinzufügen** am oberen Rand des Blatts **VM-Images** aus.

4. Ändern oder überprüfen Sie die Werte der folgenden Felder für das erste VM-Image:

    > [!IMPORTANT]
    > Nicht alle Standardwerte sind für das vorhandene Marketplace-Element richtig.

    | Feld  | Wert  |
    |---------|---------|
    | Herausgeber | MicrosoftWindowsServer |
    | Angebot | Windows Server |
    | Betriebssystemtyp | Windows |
    | SKU | 2012-R2-Datacenter |
    | Version | 1.0.0 |
    | Betriebssystem-Datenträgerblob-URI | https://<*Speicherkonto*>/<*Containername*>/WindowsServer2012R2DatacenterBYOL.vhd |


5. Wählen Sie die Schaltfläche **Erstellen**.

6. Wiederholen Sie diesen Vorgang für die verbleibenden VM-Images.

Alle erforderlichen VM-Images weisen die folgenden Eigenschaften auf:

| Herausgeber  | Angebot  | Betriebssystemtyp | SKU | Version | Betriebssystem-Datenträgerblob-URI |
|---------|---------|---------|---------|---------|---------|
| MicrosoftWindowsServer| Windows Server | Windows | 2012-R2-Datacenter | 1.0.0 | https://[*Speicherkonto*]/[*Containername*]/WindowsServer2012R2DatacenterBYOL.vhd |
| MicrosoftWindowsServer | Windows Server | Windows | 2016-Datacenter | 1.0.0 | https://[*Speicherkonto*]/[*Containername*]/Server2016DatacenterFullBYOL.vhd |
| MicrosoftWindowsServer | Windows Server | Windows | 2016-Datacenter-Server-Core | 1.0.0 | https://[*Speicherkonto*]/[*Containername*]/Server2016DatacenterCoreBYOL.vhd |
| Canonical | UbuntuServer | Linux | 14.04.3-LTS | 1.0.0 | https://[*Speicherkonto*]/[*Containername*]/Ubuntu1404LTS.vhd |
| Canonical | UbuntuServer | Linux | 16.04-LTS | 16.04.20170811 | https://[*Speicherkonto*]/[*Containername*]/Ubuntu1604-20170619.1.vhd |
| OpenLogic | CentOS | Linux | 6.9 | 1.0.0 | https://[*Speicherkonto*]/[*Containername*]/OpenLogic-CentOS-69-20180105.vhd |
| Credativ | Debian | Linux | 8 | 1.0.0 | https://[*Speicherkonto*]/[*Containername*]/Debian8_latest.vhd |

## <a name="next-steps"></a>Nächste Schritte

- Unter [Versionshinweise für Validation-as-a-Service](azure-stack-vaas-release-notes.md) finden Sie die Änderungen in den letzten Versionen.