---
title: Verwalten von Azure Stack Hub-Speicherkonten
description: Informationen zum Suchen, Verwalten, Wiederherstellen und Freigeben von Azure Stack Hub-Speicherkonten
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: xiaofmao
ms.lastreviewed: 03/19/2019
ms.openlocfilehash: 2d836c601f292b25a472b38d1afef464a3b22960
ms.sourcegitcommit: 6306e0c2506106ad01ff50010f36466f3325d0a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/09/2020
ms.locfileid: "84630928"
---
# <a name="manage-azure-stack-hub-storage-accounts"></a>Verwalten von Azure Stack Hub-Speicherkonten

Erfahren Sie, wie Sie Azure Stack Hub-Speicherkonten verwalten. Sie können Speicherkonten suchen und wiederherstellen sowie Speicherkapazität abhängig von Ihren geschäftlichen Anforderungen freigeben.

## <a name="find-a-storage-account"></a>Suchen eines Speicherkontos

Sie können die Liste der Speicherkonten in der Region in Azure Stack Hub abrufen, indem Sie wie folgt vorgehen:

1. Melden Sie sich beim Administratorportal `https://adminportal.local.azurestack.external` an.

2. Wählen Sie **Alle Dienste** > **Speicher** > **Speicherkonten** aus.

   ![Azure Stack Hub-Speicherkonten](media/azure-stack-manage-storage-accounts/image4.png)

Standardmäßig werden die ersten 10 Konten angezeigt. Sie können weitere abrufen, indem Sie unten auf der Seite auf den Link **Weitere laden** klicken.

oder

Wenn Sie an einem bestimmten Speicherkonto interessiert sind, können Sie nur **die entsprechenden Konten abrufen**.

**So filtern Sie Konten:**

1. Klicken Sie oben im Bereich auf **Filter**.
2. Im Filterbereich können Sie Werte für **Kontoname**, **Abonnement-ID** oder **Status** angeben, um die angezeigte Speicherkontoliste einzugrenzen. Nutzen Sie die Optionen je nach Bedarf.
3. Der Filter wird automatisch auf die Liste angewendet, während Sie tippen.

    ![Filtern von Azure Stack Hub-Speicherkonten](media/azure-stack-manage-storage-accounts/image5.png)

4. Klicken Sie zum Zurücksetzen des Filters auf **Filter**, deaktivieren Sie die getroffene Auswahl, und aktualisieren Sie die Liste.

Mit dem Suchfeld (oben im Bereich mit der Speicherkontenliste) können Sie den ausgewählten Text in der Liste mit den Konten hervorheben. Sie können diese Funktion nutzen, wenn der vollständige Name oder die ID nicht ohne Weiteres verfügbar ist.

Hier können Sie Freitext verwenden, um das gewünschte Konto zu finden.

![Suchen nach Azure Stack Hub-Speicherkonten](media/azure-stack-manage-storage-accounts/image6.png)

## <a name="look-at-account-details"></a>Überprüfen der Kontodetails
Wenn Sie die gewünschten Konten gefunden haben, können Sie auf ein Konto klicken, um bestimmte Details anzuzeigen. Dann wird ein neuer Bereich mit den Kontodetails geöffnet. Zu diesen Details zählen die Art des Kontos, der Zeitpunkt der Erstellung, der Standort usw.

![Speicherkontodetails](media/azure-stack-manage-storage-accounts/image7.png)

## <a name="recover-a-deleted-account"></a>Wiederherstellen eines gelöschten Kontos
Es gibt Situationen, in denen Sie ein gelöschtes Konto wiederherstellen müssen.

In Azure Stack Hub gibt es hierfür eine einfache Möglichkeit:

1. Navigieren Sie zur Liste der Speicherkonten. Weitere Informationen finden Sie weiter oben in diesem Artikel unter [Suchen eines Speicherkontos](azure-stack-manage-storage-accounts.md).
2. Suchen Sie das gewünschte Konto in der Liste. Möglicherweise müssen Sie filtern.
3. Überprüfen Sie den *Status* des Kontos. Dieser müsste **Gelöscht** lauten.
4. Klicken Sie auf das Konto, um den Bereich mit den Kontodetails zu öffnen.
5. Klicken Sie oben in diesem Bereich auf die Schaltfläche **Wiederherstellen**.
6. Klicken Sie auf **Ja**, um zu bestätigen.

   ![Bestätigungsfenster für die Wiederherstellung eines Speicherkontos](media/azure-stack-manage-storage-accounts/image8.png)

7. Die Wiederherstellung wird jetzt durchgeführt. Warten Sie auf die Bestätigung, dass der Vorgang erfolgreich war. Sie können auch das Glockensymbol oben im Portal wählen, um eine Fortschrittsanzeige einzublenden.

   ![Speicherkontowiederherstellung erfolgreich](media/azure-stack-manage-storage-accounts/image9.png)

   Sobald das wiederhergestellte Konto erfolgreich synchronisiert wurde, kann es wieder verwendet werden.

### <a name="some-gotchas"></a>Häufige Fehler
* Der Status des gelöschten Kontos gibt an, dass es **aus der Aufbewahrung entfernt** wurde.
  
  Die Entfernung aus der Aufbewahrung bedeutet, dass der Aufbewahrungszeitraum für das gelöschte Konto überschritten ist und es unter Umständen nicht wiederhergestellt werden kann.

* Das gelöschte Konto wird nicht in der Liste der Konten angezeigt.
  
  Ihr Konto wird in der Kontoliste ggf. nicht angezeigt, wenn für das gelöschte Konto bereits der Garbage Collection-Vorgang durchgeführt wurde. In diesem Fall kann es nicht wiederhergestellt werden. Weitere Informationen finden Sie in diesem Artikel unter [Freigeben von Kapazität](#reclaim).

## <a name="set-the-retention-period"></a>Festlegen des Aufbewahrungszeitraums
Die Einstellung für den Aufbewahrungszeitraum ermöglicht einem Cloudbetreiber das Festlegen eines Zeitraums in Tagen (zwischen 0 und 9999 Tage), in dem gelöschte Konten ggf. wiederhergestellt werden können. Der Standardaufbewahrungszeitraum ist auf 0 Tage festgelegt. Mit dem Wert „0“ wird festgelegt, dass alle gelöschten Konten sofort aus der Aufbewahrung entfernt und für die regelmäßige automatische Speicherbereinigung markiert werden.

**Ändern des Aufbewahrungszeitraums:**

1. Melden Sie sich beim Administratorportal `https://adminportal.local.azurestack.external` an.
2. Wählen Sie **Alle Dienste** > **Regionsverwaltung** unter **Verwaltung** aus.
3. Wählen Sie **Ressourcenanbieter** > **Speicher** > **Einstellungen** aus. Der Pfad lautet: „Startseite“ > „*Region* – Ressourcenanbieter“ > „Speicher“.
4. Klicken Sie auf **Konfiguration**, und bearbeiten Sie dann den Wert für den Aufbewahrungszeitraum.

   Legen Sie die Anzahl von Tagen fest, und speichern Sie sie.

   Dieser Wert ist sofort wirksam und gilt für Ihre gesamte Region.

   ![Aufbewahrungszeitraum im Administratorportal bearbeiten](media/azure-stack-manage-storage-accounts/image10.png)

## <a name="reclaim-capacity"></a><a name="reclaim"></a>Freigeben von Kapazität
Einer der Nebeneffekte eines Aufbewahrungszeitraums ist, dass ein gelöschtes Konto weiterhin Kapazität verbraucht, bis sein Aufbewahrungszeitraum abläuft. Als Cloudbetreiber benötigen Sie ggf. eine Möglichkeit, den Speicherplatz von gelöschten Konten freizugeben, auch wenn der Aufbewahrungszeitraum noch nicht abgelaufen ist.

Sie können Kapazität entweder mithilfe des Azure-Portals oder mit PowerShell freigeben.

**Freigeben von Kapazität mithilfe des Portals:**
1. Navigieren Sie zum Bereich „Speicherkonten“. Siehe „Suchen eines Speicherkontos“.
2. Klicken Sie oben im Bereich auf **Speicherplatz freigeben**.
3. Lesen Sie die Meldung, und klicken Sie auf **OK**.

    ![Speicherplatz in Speicherkonten freigeben](media/azure-stack-manage-storage-accounts/image11.png)

4. Warten Sie darauf, dass die erfolgreiche Durchführung bestätigt wird. Klicken Sie dafür auf das Glockensymbol im Portal.

    ![Es wurde erfolgreich Speicherplatz freigegeben.](media/azure-stack-manage-storage-accounts/image12.png)

5. Aktualisieren Sie die Seite „Speicherkonten“. Die gelöschten Konten werden nicht mehr in der Liste angezeigt, da sie endgültig gelöscht wurden.

Sie können auch PowerShell verwenden, um den Aufbewahrungszeitraum explizit außer Kraft zu setzen und sofort Kapazität freizugeben.

**Freigeben von Kapazität mithilfe von PowerShell:**

1. Vergewissern Sie sich, dass Azure PowerShell installiert und konfiguriert ist. Ist dies nicht der Fall, gehen Sie folgendermaßen vor: 
   * Anweisungen zur Installation und zur Zuordnung der aktuellen Azure PowerShell-Version an Ihr Azure-Abonnement finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
   Weitere Informationen zu Azure Resource Manager-Cmdlets finden Sie unter [Verwenden von Azure PowerShell mit dem Azure Resource Manager](https://go.microsoft.com/fwlink/?LinkId=394767).
2. Führen Sie die folgenden Cmdlets aus:

> [!NOTE]  
> Wenn Sie diese Cmdlets ausführen, wird das Konto samt Inhalt endgültig gelöscht. Es kann nicht wiederhergestellt werden. Verwenden Sie diese Option mit Umsicht.

```powershell  
    $farm_name = (Get-AzsStorageFarm)[0].name
    Start-AzsReclaimStorageCapacity -FarmName $farm_name
```

Weitere Informationen finden Sie in der [Azure Stack Hub-PowerShell-Dokumentation](https://docs.microsoft.com/powershell/azure/azure-stack/overview).
 

## <a name="next-steps"></a>Nächste Schritte

 - Informationen zum Verwalten von Berechtigungen finden Sie unter [Festlegen von Zugriffsberechtigungen mithilfe der rollenbasierten Zugriffssteuerung](azure-stack-manage-permissions.md).
 - Informationen zum Verwalten der Speicherkapazität für Azure Stack Hub finden Sie unter [Verwalten der Speicherkapazität für Azure Stack Hub](azure-stack-manage-storage-shares.md).
