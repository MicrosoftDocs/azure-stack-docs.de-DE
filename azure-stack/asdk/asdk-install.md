---
title: Installieren des Azure Stack Development Kits (ASDK) | Microsoft-Dokumentation
description: In diesem Artikel wird die Installation des Azure Stack Development Kits (ASDK) beschrieben.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/06/2019
ms.author: mabrigg
ms.reviewer: misainat
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: f0aef7d1699065664f2013076ed2b12b24a06d4d
ms.sourcegitcommit: ccd86bd0862c45de1f6a4993f783ea2e186c187a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65172443"
---
# <a name="install-the-azure-stack-development-kit-asdk"></a>Installieren des Azure Stack Development Kits (ASDK)
Nachdem Sie den [ASDK-Hostcomputer vorbereitet](asdk-prepare-host.md) haben, kann das ASDK mit den folgenden Schritten in diesem Artikel im Image „CloudBuilder.vhdx“ bereitgestellt werden.

## <a name="install-the-asdk"></a>Installieren des ASDK
Die Schritte in diesem Artikel zeigen, wie das ASDK mit einer grafischen Benutzeroberfläche (GUI) bereitgestellt werden kann, die durch Herunterladen und Ausführen des PowerShell-Skripts **asdk-installer.ps1** bereitgestellt werden kann.

> [!NOTE]
> Die Benutzeroberfläche des Installers für das Azure Stack Development Kit ist ein Open Source-Skript, das auf WCF und PowerShell basiert.


1. Nachdem der Hostcomputer im Image „CloudBuilder.vhdx“ erfolgreich gestartet wurde, können Sie sich mit den Administratoranmeldeinformationen anmelden, die Sie bei der [Vorbereitung des Development Kit-Hostcomputers](asdk-prepare-host.md) für die ASDK-Installation angegeben haben. Diese sollten mit den Anmeldeinformationen für den lokalen Administrator des Development Kit-Hosts identisch sein.
2. Öffnen Sie eine PowerShell-Konsole mit erhöhten Rechten, und führen Sie das PowerShell-Skript „**&lt;Laufwerkbuchstabe>\AzureStack_Installer\asdk-installer.ps1**“ aus. Beachten Sie, dass sich das Skript jetzt eventuell auf einem anderen Laufwerk als „C:\“ im Image „CloudBuilder.vhdx“ befindet. Klicken Sie auf **Installieren**.

    ![](media/asdk-install/1.PNG) 

3. Wählen Sie im Dropdownfeld **Typ** des Identitätsanbieters**Azure China-Cloud**, **Azure US Government-Cloud**, **AD FS** oder **Azure-Cloud** aus. Geben Sie unter **Lokales Administratorkennwort** im Feld **Kennwort** das lokale Administratorkennwort ein (das mit dem derzeit konfigurierten lokalen Administratorkennwort übereinstimmen muss), und klicken Sie dann auf **Weiter**.

    ![](media/asdk-install/2.PNG) 
  
   Wenn Sie einen Azure-Abonnementidentitätsanbieter auswählen, benötigen Sie eine Internetverbindung, den vollständigen Namen eines Azure AD-Verzeichnismandanten im Format „*Domänenname*.onmicrosoft.com“ oder einen von Azure AD überprüften benutzerdefinierten Domänennamen und globale Administratoranmeldeinformationen für das angegebene Verzeichnis.<br><br>Nach der Bereitstellung ist die globale Azure Active Directory-Administratorberechtigung nicht erforderlich. Einige Vorgänge erfordern jedoch möglicherweise die Anmeldeinformationen für den globalen Administrator. Beispielsweise ein Ressourcenanbieter-Installationsskript oder ein neues Feature, dem eine Berechtigung erteilt werden muss. Sie können entweder die globalen Administratorrechte des Kontos vorübergehend wiederherstellen oder ein separates globales Administratorkonto verwenden, das Besitzer des *Standardanbieterabonnements* ist.<br><br>Wenn Sie AD FS als Identitätsanbieter verwenden, wird der standardmäßige Stampverzeichnisdienst verwendet. Das Standardkonto für die Anmeldung lautet azurestackadmin@azurestack.local, und das dazugehörige Kennwort haben Sie im Rahmen des Setupvorgangs angegeben.

   > [!NOTE]
   > Um auch dann optimale Ergebnisse zu erzielen, wenn Sie eine nicht verbundene Azure Stack-Umgebung mit AD FS als Identitätsanbieter verwenden möchten, wird empfohlen, das ASDK bei bestehender Internetverbindung zu installieren. Auf diese Weise kann die Evaluierungsversion von Windows Server 2016, die Teil der Development Kit-Installation ist, während der Bereitstellung aktiviert werden.

4. Wählen Sie einen für das Development Kit zu verwendenden Netzwerkadapter aus, und klicken Sie dann auf **Weiter**.

    ![](media/asdk-install/3.PNG)

5. Geben Sie auf der Seite **Netzwerkkonfiguration** eine gültige **Zeitserver-IP**-Adresse an. In diesem Pflichtfeld wird der Zeitserver festgelegt, der vom Development Kit verwendet wird. Dieser Parameter muss als gültige Zeitserver-IP-Adresse angegeben werden. Servernamen werden nicht unterstützt.

      > [!TIP]
      > Besuchen Sie zum Ermitteln der IP-Adresse eines Zeitservers [ntppool.org](https://www.ntppool.org/), oder pingen Sie „time.windows.com“. 

    **Optional** können Sie eine **DNS-Weiterleitungs**-IP-Adresse angeben. Ein DNS-Server wird als Teil der Azure Stack-Bereitstellung erstellt. Damit Computer innerhalb der Lösung Namen außerhalb des Stamps auflösen können, stellen Sie Ihren vorhandenen Infrastruktur-DNS-Server bereit. Der DNS-Server innerhalb des Stamps leitet Auflösungsanforderungen, die unbekannte Namen betreffen, an diesen Server weiter.

    ![](media/asdk-install/4.PNG)

6. Auf der Seite **Überprüfen von Netzwerkadaptereigenschaften** sehen Sie eine Statusanzeige. Klicken Sie nach Abschluss der Überprüfung auf **Weiter**.

    ![](media/asdk-install/5.PNG)

7. Klicken Sie auf der Seite **Zusammenfassung** auf **Bereitstellen**, um die ASDK-Installation auf dem Development Kit-Hostcomputer zu starten.

    ![](media/asdk-install/6.PNG)

    > [!TIP]
    > Hier können Sie auch die PowerShell-Setupbefehle kopieren, die zum Installieren des Development Kit verwendet werden. Dies ist hilfreich, falls Sie [das ASDK erneut mithilfe von PowerShell auf dem Hostcomputer bereitstellen müssen](asdk-deploy-powershell.md).

8. Wenn Sie eine Azure AD-Bereitstellung durchführen, werden Sie einige Minuten nach dem Start des Setupvorgangs zum Eingeben der Anmeldeinformationen Ihres globalen Administratorkontos für Azure AD aufgefordert.

9. Der Bereitstellungsprozess nimmt einige Stunden in Anspruch, und während dieses Zeitraums wird der Hostcomputer automatisch einmal neu gestartet. Falls Sie den Status der Bereitstellung überwachen möchten, können Sie sich als „azurestack\AzureStackAdmin“ anmelden, nachdem der Development Kit-Host neu gestartet wurde. Wenn die Bereitstellung erfolgreich verlaufen ist, zeigt die PowerShell-Konsole folgende Meldung an: **ABGESCHLOSSEN: Aktion „Bereitstellung“**. 
    > [!IMPORTANT]
    > Wenn Sie sich als lokaler Administrator anmelden, nachdem der Computer der Domäne „azurestack“ beigetreten ist, wird der Bereitstellungsstatus nicht angezeigt. Führen Sie die Bereitstellung nicht erneut aus, sondern melden Sie sich stattdessen als „azurestack\AzureStackAdmin“ an, um zu überprüfen, ob sie ausgeführt wird.

    ![](media/asdk-install/7.PNG)

Herzlichen Glückwunsch, Sie haben das ASDK erfolgreich installiert!

Wenn die Bereitstellung aus irgendeinem Grund nicht erfolgreich ist, können Sie [erneut eine Bereitstellung](asdk-redeploy.md) durchführen oder die folgenden PowerShell-Befehle im selben PowerShell-Fenster mit erhöhten Rechten verwenden, um den Bereitstellungsvorgang ab dem letzten erfolgreichen Schritt neu zu starten:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

## <a name="next-steps"></a>Nächste Schritte
[Konfiguration nach der Bereitstellung](asdk-post-deploy.md)
