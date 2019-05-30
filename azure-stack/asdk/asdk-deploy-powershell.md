---
title: Bereitstellen von Azure Stack – PowerShell | Microsoft-Dokumentation
description: In diesem Artikel installieren Sie das ASDK mithilfe von PowerShell über die Befehlszeile.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: ''
ms.date: 05/06/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 4a32631441760db715443b8979e2769b55258fcf
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66267167"
---
# <a name="deploy-the-asdk-from-the-command-line"></a>Bereitstellen des ASDK über die Befehlszeile
Das ASDK ist eine Test- und Entwicklungsumgebung, die sie zum Evaluieren und Demonstrieren von Azure Stack-Funktionen und -Diensten bereitstellen können. Zur Einrichtung der Umgebung müssen Sie die Umgebungshardware vorbereiten und einige Skripts ausführen. (Dies nimmt mehrere Stunden in Anspruch.) Anschließend können Sie sich beim Administrator- und Benutzerportal anmelden und Azure Stack verwenden.

## <a name="prerequisites"></a>Voraussetzungen 
Bereiten Sie den Development Kit-Hostcomputer vor. Planen Sie die Hardware, die Software und das Netzwerk. Der Computer, der das Development Kit hostet (der Development Kit-Host) muss die Hardware-, Software- und Netzwerkanforderungen erfüllen. Sie müssen auch zwischen Azure Active Directory (Azure AD) und Active Directory-Verbunddienste (AD FS) wählen. Achten Sie darauf, diese Voraussetzungen vor dem Beginn der Bereitstellung zu erfüllen, damit der Installationsvorgang reibungslos ausgeführt wird. 

Vergewissern Sie sich vor der ASDK-Bereitstellung, dass Hardware, Betriebssystem, Konto und Netzwerkkonfigurationen Ihres geplanten Development Kit-Hostcomputers die Mindestanforderungen für die ASDK-Installation erfüllen.

Die Überlegungen zur Bereitstellungsplanung für das ASDK finden Sie **[hier](asdk-deploy-considerations.md)** .

> [!TIP]
> Mit dem [Tool zur Überprüfung von Bereitstellungsanforderungen für Azure Stack](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) können Sie sich nach der Installation des Betriebssystems auf dem Development Kit-Hostcomputer vergewissern, dass die Hardware die Anforderungen erfüllt.

## <a name="download-and-extract-the-deployment-package"></a>Herunterladen und Extrahieren des Bereitstellungspakets
Nachdem Sie sich vergewissert haben, dass Ihr Development Kit-Hostcomputer die Mindestanforderungen für die ASDK-Installation erfüllt, können Sie im nächsten Schritt das ASDK-Bereitstellungspaket herunterladen und extrahieren. Das Bereitstellungspaket enthält die Datei „Cloudbuilder.vhdx“. Hierbei handelt es sich um eine virtuelle Festplatte mit startbarem Betriebssystem und den Azure Stack-Installationsdateien.

Sie können das Bereitstellungspaket auf den Development Kit-Host oder auf einen anderen Computer herunterladen. Die extrahierten Bereitstellungsdateien belegen bis zu 60 GB freien Speicherplatz auf dem Datenträger, durch die Verwendung eines anderen Computers lassen sich also die Hardwareanforderungen an den Development Kit-Host reduzieren.

**[Download and extract the Azure Stack Development Kit (ASDK)](asdk-download.md)** (Herunterladen und Extrahieren des Azure Stack Development Kits)

## <a name="prepare-the-development-kit-host-computer"></a>Vorbereiten des Development Kit-Hostcomputers
Bevor Sie das ASDK auf dem Hostcomputer installieren können, muss zunächst die Umgebung vorbereitet und das System für den Start von einer VHD konfiguriert werden. Nach diesem Schritt startet der Development Kit-Host mit „Cloudbuilder.vhdx“ (eine virtuelle Festplatte mit startbarem Betriebssystem und den Azure Stack-Installationsdateien).

Verwenden Sie PowerShell, um den ASDK-Hostcomputer für den Start mit „CloudBuilder.vhdx“ zu konfigurieren. Mit diesen Befehlen wird Ihr ASDK-Hostcomputer für den Start mit der heruntergeladenen und extrahierten virtuellen Azure Stack-Festplatte (CloudBuilder.vhdx) konfiguriert. Starten Sie den ASDK-Hostcomputer neu, nachdem Sie diese Schritte ausgeführt haben.

So konfigurieren Sie den ASDK-Hostcomputer für den Start mit „CloudBuilder.vhdx“:

  1. Starten Sie eine Eingabeaufforderung als Administrator.
  2. Führen Sie `bcdedit /copy {current} /d "Azure Stack"` aus.
  3. Kopieren Sie (mit STRG+C) den zurückgegebenen CLSID-Wert, einschließlich der erforderlichen geschweiften Klammern ({}). Dieser Wert hat die Bezeichnung {CLSID} und muss (mit STRG+V oder Rechtsklick) in den verbleibenden Schritten eingefügt werden.
  4. Führen Sie `bcdedit /set {CLSID} device vhd=[C:]\CloudBuilder.vhdx` aus. 
  5. Führen Sie `bcdedit /set {CLSID} osdevice vhd=[C:]\CloudBuilder.vhdx` aus. 
  6. Führen Sie `bcdedit /set {CLSID} detecthal on` aus. 
  7. Führen Sie `bcdedit /default {CLSID}` aus.
  8. Führen Sie `bcdedit` aus, um die Starteinstellungen zu überprüfen. 
  9. Stellen Sie sicher, dass die Datei „CloudBuilder.vhdx“ in den Stammordner des Laufwerks C:\ verschoben wurde (C:\CloudBuilder.vhdx), und starten Sie den Development Kit-Hostcomputer neu. Der ASDK-Hostcomputer sollte nun von der VM-Festplatte „CloudBuilder.vhdx“ gestartet werden, um mit der ASDK-Bereitstellung zu beginnen. 

> [!IMPORTANT]
> Vergewissern Sie sich vor dem Neustart, dass Sie über direkten physischen Zugriff oder über KVM-Zugriff auf den Development Kit-Hostcomputer verfügen. Beim ersten Start des virtuellen Computers werden Sie aufgefordert, das Windows Server-Setup abzuschließen. Geben Sie die gleichen Administratoranmeldeinformationen an, mit denen Sie sich auch bei dem Development Kit-Hostcomputer angemeldet haben. 

### <a name="prepare-the-development-kit-host-using-powershell"></a>Vorbereiten des Development Kit-Hosts mit PowerShell 
Wenn der Development Kit-Hostcomputer erfolgreich mit dem Image „CloudBuilder.vhdx“ gestartet wurde, melden Sie sich mit den gleichen lokalen Administratoranmeldeinformationen an, mit denen Sie sich auch beim Development Kit-Hostcomputer angemeldet haben (und die Sie zum Abschließen des Windows Server-Setups angegeben haben, als der Computer von der VHD gestartet wurde). 

> [!NOTE]
> Optional können Sie [Azure Stack-Telemetrieeinstellungen](asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) auch *vor* der ASDK-Installation konfigurieren.

Öffnen Sie eine PowerShell-Konsole mit erhöhten Rechten, und führen Sie die Befehle in diesem Abschnitt aus, um das ASDK auf dem Development Kit-Host bereitzustellen.

> [!IMPORTANT] 
> Für die ASDK-Installation wird genau eine Netzwerkschnittstellenkarte (NIC) für das Netzwerk unterstützt. Wenn Sie mehrere Netzwerkadapter nutzen, sollten Sie vor dem Ausführen des Bereitstellungsskripts sicherstellen, dass nur einer aktiviert ist (und alle anderen deaktiviert).

Sie können Azure Stack mit Azure AD oder Windows Server AD FS als Identitätsanbieter bereitstellen. Azure Stack, Ressourcenanbieter und andere Anwendungen funktionieren in beiden Fällen.

> [!TIP]
> Wenn Sie keine Setupparameter angeben (siehe „InstallAzureStackPOC.ps1“ mit optionalen Parametern und Beispielen weiter unten), werden Sie zur Eingabe der erforderlichen Parameter aufgefordert.

### <a name="deploy-azure-stack-using-azure-ad"></a>Bereitstellen von Azure Stack mit Azure AD 
Zum Bereitstellen von Azure Stack **mit Azure AD als Identitätsanbieter** müssen Sie entweder direkt oder über einen transparenten Proxy über eine Internetverbindung verfügen. 

Führen Sie die folgenden PowerShell-Befehle aus, um das Development Kit mit Azure AD bereitzustellen:

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator     
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password
  ```

Einige Minuten nach Beginn der ASDK-Installation werden Sie zum Eingeben der Azure AD-Anmeldeinformationen aufgefordert. Geben Sie die Anmeldeinformationen des globalen Administrators für Ihren Azure AD-Mandanten an. 

Nach der Bereitstellung ist die globale Azure Active Directory-Administratorberechtigung nicht erforderlich. Einige Vorgänge erfordern jedoch möglicherweise die Anmeldeinformationen für den globalen Administrator. Beispielsweise ein Ressourcenanbieter-Installationsskript oder ein neues Feature, dem eine Berechtigung erteilt werden muss. Sie können entweder die globalen Administratorrechte des Kontos vorübergehend wiederherstellen oder ein separates globales Administratorkonto verwenden, das Besitzer des *Standardanbieterabonnements* ist.

### <a name="deploy-azure-stack-using-ad-fs"></a>Bereitstellen von Azure Stack mit AD FS 
Führen Sie die folgenden PowerShell-Befehle aus (indem Sie einfach den Parameter „-UseADFS“ hinzufügen), um das Development Kit **mit AD FS als Identitätsanbieter** bereitzustellen: 

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator 
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -UseADFS
  ```

Bei AD FS-Bereitstellungen wird der standardmäßige Stampverzeichnisdienst als Identitätsanbieter verwendet. Das Standardkonto für die Anmeldung ist azurestackadmin@azurestack.local, und das Kennwort ist auf den Wert festgelegt, den sie für die PowerShell-Setupbefehle angegeben haben.

Der Bereitstellungsprozess kann einige Stunden dauern, und während dieses Zeitraums wird das System automatisch einmal neu gestartet. Wenn die Bereitstellung erfolgreich verlaufen ist, zeigt die PowerShell-Konsole folgende Meldung an: **ABGESCHLOSSEN: Aktion „Bereitstellung“** . Wenn die Bereitstellung nicht erfolgreich ist, können Sie versuchen, das Skript erneut auszuführen, indem Sie den Parameter „-rerun“ verwenden. Alternativ dazu können Sie [ASDK auch ganz neu bereitstellen](asdk-redeploy.md).

> [!IMPORTANT]
> Wenn Sie den Bereitstellungsstatus nach dem Neustart des ASDK-Hosts überwachen möchten, müssen Sie sich als „AzureStack\AzureStackAdmin“ anmelden. Der Bereitstellungsstatus wird nicht angezeigt, wenn Sie sich nach dem Neustart des Hostcomputers (und dem Beitritt zur Domäne „azurestack.local“) als lokaler Administrator anmelden. Führen Sie die Bereitstellung nicht erneut durch, sondern melden Sie sich als „AzureStack\AzureStackAdmin“ mit dem gleichen Kennwort wie der lokale Administrator an, um zu überprüfen, ob das Setup ausgeführt wird.


#### <a name="azure-ad-deployment-script-examples"></a>Beispiele für Azure AD-Bereitstellungsskripts
Sie können Skripts für die gesamte Azure AD-Bereitstellung erstellen. Hier finden Sie eine Auswahl kommentierter Beispiele mit einigen optionalen Parametern.

Wenn Ihre Azure AD-Identität nur **einem** Azure AD-Verzeichnis zugeordnet ist:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 52.168.138.145 #Example time server IP address.
```

Wenn Ihre Azure AD-Identität **mehr als einem** Azure AD-Verzeichnis zugeordnet ist:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" #Example: user@AADDirName.onmicrosoft.com 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -InfraAzureDirectoryTenantName "<Azure AD directory in the form of domainname.onmicrosoft.com or an Azure AD verified custom domain name>" -TimeServer 52.168.138.145 #Example time server IP address.
```

Wenn DHCP für Ihre Umgebung nicht aktiviert ist, müssen Sie die folgenden zusätzlichen Parameter in eine der obigen Optionen einfügen, wie im Verwendungsbeispiel zu sehen: 

```powershell
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 10.222.112.26
```

### <a name="asdk-installazurestackpocps1-optional-parameters"></a>ASDK „InstallAzureStackPOC.ps1“ – Optionale Parameter

|Parameter|Erforderlich/Optional|BESCHREIBUNG|
|-----|-----|-----|
|AdminPassword|Erforderlich|Legt das lokale Administratorkonto und alle anderen Benutzerkonten auf allen virtuellen Computern fest, die im Rahmen der Development Kit-Bereitstellung erstellt werden. Dieses Kennwort muss mit dem Kennwort des lokalen Administrators auf dem Host übereinstimmen.|
|InfraAzureDirectoryTenantName|Erforderlich|Legt das Mandantenverzeichnis fest. Verwenden Sie diesen Parameter, um ein bestimmtes Verzeichnis anzugeben, für das das AAD-Konto über Berechtigungen zum Verwalten mehrerer Verzeichnisse verfügt. Vollständiger Name eines AAD-Verzeichnismandanten im Format „.onmicrosoft.com“ oder einen von Azure AD überprüften benutzerdefinierten Domänennamen.|
|TimeServer|Erforderlich|Verwenden Sie diesen Parameter, um einen bestimmten Zeitserver anzugeben. Dieser Parameter muss als gültige Zeitserver-IP-Adresse angegeben werden. Servernamen werden nicht unterstützt.|
|InfraAzureDirectoryTenantAdminCredential|Optional|Legt den Benutzernamen und das Kennwort für Azure Active Directory fest. Bei diesen Azure-Anmeldeinformationen muss es sich um eine Organisations-ID handeln.|
|InfraAzureEnvironment|Optional|Wählen Sie die Azure-Umgebung aus, für die Sie diese Azure Stack-Bereitstellung registrieren möchten. Die verfügbaren Optionen sind „Globales Azure“, „Azure – China“ und „Azure – US Government“.|
|DNSForwarder|Optional|Ein DNS-Server wird als Teil der Azure Stack-Bereitstellung erstellt. Damit Computer innerhalb der Lösung Namen außerhalb des Stamps auflösen können, stellen Sie Ihren vorhandenen Infrastruktur-DNS-Server bereit. Der DNS-Server innerhalb des Stamps leitet Auflösungsanforderungen, die unbekannte Namen betreffen, an diesen Server weiter.|
|Rerun|Optional|Verwenden Sie dieses Flag, um die Bereitstellung erneut durchzuführen. Alle vorherigen Eingaben werden verwendet. Das erneute Eingeben von zuvor angegebenen Daten wird nicht unterstützt, da mehrere eindeutige Werte generiert und für die Bereitstellung verwendet werden.|


## <a name="perform-post-deployment-configurations"></a>Durchführen der Konfigurationsschritte nach der Bereitstellung
Nach der ASDK-Installation empfiehlt es sich, einige Überprüfungen und Konfigurationsänderungen durchzuführen. Sie können mit dem Cmdlet „test-AzureStack“ überprüfen, ob die Installation erfolgreich war, und Azure Stack PowerShell sowie GitHub-Tools installieren. 

Darüber hinaus sollten Sie die Kennwortablaufrichtlinie zurücksetzen, um sicherzustellen, dass das Kennwort für den Development Kit-Host nicht vor dem Ende des Evaluierungszeitraums abläuft.

> [!NOTE]
> Optional können Sie [Azure Stack-Telemetrieeinstellungen](asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) auch *nach* der ASDK-Installation konfigurieren.

**[Post ASDK installation configuration tasks](asdk-post-deploy.md)** (Aufgaben nach der ASDK-Bereitstellung)

## <a name="register-with-azure"></a>Registrieren bei Azure
Sie müssen Azure Stack bei Azure registrieren, um [Azure Marketplace-Elemente in Azure Stack herunterladen](../operator/azure-stack-create-and-publish-marketplace-item.md) zu können.

**[Register Azure Stack with Azure](asdk-register.md)** (Registrieren von Azure Stack bei Azure)

## <a name="next-steps"></a>Nächste Schritte
Glückwunsch! Nach Abschluss dieser Schritte verfügen Sie über eine Development Kit-Umgebung mit [Administratorportal](https://adminportal.local.azurestack.external) und [Benutzerportal](https://portal.local.azurestack.external). 

[Konfigurationsaufgaben nach der Installation des ASDK](asdk-post-deploy.md)

