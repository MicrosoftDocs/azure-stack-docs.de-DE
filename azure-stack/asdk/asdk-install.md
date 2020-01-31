---
title: Installieren des ASDK
description: Erfahren Sie, wie Sie das Azure Stack Development Kit (ASDK) installieren.
author: justinha
ms.topic: article
ms.date: 05/06/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 732eef83c6a973a6afbe260e5cca8134dda46504
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76873786"
---
# <a name="install-the-asdk"></a>Installieren des ASDK
Nachdem Sie den [ASDK-Hostcomputer vorbereitet](asdk-prepare-host.md) haben, kann das Azure Stack Development Kit (ASDK) mit den folgenden Schritten in diesem Artikel im Image „CloudBuilder.vhdx“ bereitgestellt werden.

## <a name="install-the-asdk"></a>Installieren des ASDK
Die Schritte in diesem Artikel zeigen, wie das ASDK mit einer grafischen Benutzeroberfläche (GUI) bereitgestellt werden kann, die durch Herunterladen und Ausführen des PowerShell-Skripts **asdk-installer.ps1** bereitgestellt werden kann.

> [!NOTE]
> Die Benutzeroberfläche des Installationsprogramms für das ASDK ist ein Open-Source-Skript, das auf WCF und PowerShell basiert.


1. Nachdem der Hostcomputer im Image „CloudBuilder.vhdx“ gestartet wurde, können Sie sich mit den Administratoranmeldeinformationen anmelden, die Sie bei der [Vorbereitung des ASDK-Hostcomputers](asdk-prepare-host.md) für die ASDK-Installation angegeben haben. Diese sollten mit den Anmeldeinformationen für den lokalen Administrator des ASDK-Hosts identisch sein.
2. Öffnen Sie eine PowerShell-Konsole mit erhöhten Rechten, und führen Sie das PowerShell-Skript „ **&lt;Laufwerkbuchstabe>\AzureStack_Installer\asdk-installer.ps1**“ aus. Beachten Sie, dass sich das Skript jetzt eventuell auf einem anderen Laufwerk als „C:\“ im Image „CloudBuilder.vhdx“ befindet. Klicken Sie auf **Installieren**.

    ![Installieren des ASDK](media/asdk-install/1.PNG) 

3. Wählen Sie im Dropdownfeld **Typ** des Identitätsanbieters**Azure China-Cloud**, **Azure US Government-Cloud**, **AD FS** oder **Azure-Cloud** aus. Geben Sie unter **Lokales Administratorkennwort** im Feld **Kennwort** das lokale Administratorkennwort (das mit dem derzeit konfigurierten lokalen Administratorkennwort übereinstimmen muss) ein, und klicken Sie dann auf **Weiter**.

    ![Dropdownliste für den Typ des Identitätsanbieters in ASDK](media/asdk-install/2.PNG) 
  
    Wenn Sie ein Azure-Abonnement als Identitätsanbieter auswählen, benötigen Sie eine Internetverbindung, den vollständigen Namen eines Azure AD-Verzeichnismandanten im Format „*Domänenname*.onmicrosoft.com“ oder einen von Azure AD überprüften benutzerdefinierten Domänennamen. Außerdem benötigen Sie globale Administratoranmeldeinformationen für das angegebene Verzeichnis.

    Nach der Bereitstellung ist die globale Azure Active Directory-Administratorberechtigung (Azure AD) nicht mehr erforderlich. Einige Vorgänge erfordern jedoch möglicherweise die Anmeldeinformationen für den globalen Administrator. Beispielsweise ein Ressourcenanbieter-Installationsskript oder ein neues Feature, dem eine Berechtigung erteilt werden muss. Sie können entweder die globalen Administratorrechte des Kontos vorübergehend wiederherstellen oder ein separates globales Administratorkonto verwenden, das Besitzer des *Standardanbieterabonnements* ist.

    Wenn Sie AD FS als Identitätsanbieter verwenden, wird der standardmäßige Stampverzeichnisdienst verwendet. Das Standardkonto für die Anmeldung lautet azurestackadmin@azurestack.local, und das dazugehörige Kennwort haben Sie im Rahmen des Setupvorgangs angegeben.

   > [!NOTE]
   > Um auch dann optimale Ergebnisse zu erzielen, wenn Sie eine nicht verbundene Azure Stack-Umgebung mit AD FS als Identitätsanbieter verwenden möchten, wird empfohlen, das ASDK bei bestehender Internetverbindung zu installieren. Auf diese Weise kann die Evaluierungsversion von Windows Server 2016, die Teil der ASDK-Installation ist, während der Bereitstellung aktiviert werden.

4. Wählen Sie einen Netzwerkadapter für das ASDK aus, und klicken Sie dann auf **Weiter**.

    ![Auswählen des Netzwerkadapters für das ASDK](media/asdk-install/3.PNG)

5. Geben Sie auf der Seite **Netzwerkkonfiguration** eine gültige **Zeitserver-IP**-Adresse an. In diesem Pflichtfeld wird der Zeitserver festgelegt, der vom ASDK verwendet wird. Dieser Parameter muss als gültige Zeitserver-IP-Adresse angegeben werden. Servernamen werden nicht unterstützt.

      > [!TIP]
      > Besuchen Sie zum Ermitteln der IP-Adresse eines Zeitservers [ntppool.org](https://www.ntppool.org/), oder pingen Sie „time.windows.com“. 

    **Optional** können Sie eine **DNS-Weiterleitungs**-IP-Adresse angeben. Ein DNS-Server wird als Teil der Azure Stack-Bereitstellung erstellt. Damit Computer innerhalb der Lösung Namen außerhalb des Stamps auflösen können, stellen Sie Ihren vorhandenen Infrastruktur-DNS-Server bereit. Der DNS-Server innerhalb des Stamps leitet Auflösungsanforderungen, die unbekannte Namen betreffen, an diesen Server weiter.

    ![DNS-Weiterleitung und Netzwerkkonfiguration im ASDK](media/asdk-install/4.PNG)

6. Auf der Seite **Überprüfen von Netzwerkadaptereigenschaften** sehen Sie eine Statusanzeige. Klicken Sie nach Abschluss der Überprüfung auf **Weiter**.

    ![Überprüfen der Eigenschaften der Netzwerkschnittstellenkarte im ASDK](media/asdk-install/5.PNG)

7. Klicken Sie auf der Seite **Zusammenfassung** auf **Bereitstellen**, um die ASDK-Installation auf dem ASDK-Hostcomputer zu starten.

    ![Skriptzusammenfassung vor der Bereitstellung im ASDK](media/asdk-install/6.PNG)

    > [!TIP]
    > Hier können Sie auch die PowerShell-Setupbefehle kopieren, die zum Installieren des ASDK verwendet werden. Dies ist hilfreich, falls Sie [das ASDK erneut mithilfe von PowerShell auf dem Hostcomputer bereitstellen müssen](asdk-deploy-powershell.md).

8. Wenn Sie eine Azure AD-Bereitstellung durchführen, werden Sie einige Minuten nach dem Beginn des Setupvorgangs zum Eingeben der Anmeldeinformationen Ihres globalen Administratorkontos für Azure AD aufgefordert.

9. Der Bereitstellungsprozess nimmt einige Stunden in Anspruch, und während dieses Zeitraums wird der Hostcomputer automatisch einmal neu gestartet. Wenn Sie den Status der Bereitstellung überwachen möchten, melden Sie sich als „azurestack\AzureStackAdmin“ an, nachdem der ASDK-Host neu gestartet wurde. Wenn die Bereitstellung erfolgreich verlaufen ist, zeigt die PowerShell-Konsole folgende Meldung an: **ABGESCHLOSSEN: Aktion „Bereitstellung“** . 
    > [!IMPORTANT]
    > Wenn Sie sich als lokaler Administrator anmelden, nachdem der Computer der Domäne „azurestack“ beigetreten ist, wird der Bereitstellungsstatus nicht angezeigt. Führen Sie die Bereitstellung nicht erneut aus, sondern melden Sie sich stattdessen als „azurestack\AzureStackAdmin“ an, um zu überprüfen, ob sie ausgeführt wird.

    ![Erfolgreiche ASDK-Bereitstellung](media/asdk-install/7.PNG)

Herzlichen Glückwunsch, Sie haben das ASDK erfolgreich installiert!

Wenn die Bereitstellung aus irgendeinem Grund nicht erfolgreich ist, können Sie [erneut eine Bereitstellung durchführen](asdk-redeploy.md) oder mit den folgenden PowerShell-Befehlen den Bereitstellungsvorgang ab dem letzten erfolgreichen Schritt neu starten. Die Befehle können im selben PowerShell-Fenster mit erhöhten Rechten verwendet werden:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

## <a name="next-steps"></a>Nächste Schritte
[Konfiguration nach der Bereitstellung](asdk-post-deploy.md)
