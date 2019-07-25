---
title: Verwalten von Azure Stack-Speicherkonten | Microsoft-Dokumentation
description: Informationen zum Suchen, Verwalten, Wiederherstellen und Freigeben von Azure Stack-Speicherkonten
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 03/19/2019
ms.openlocfilehash: 4f9e9c4f79a06e0f1f74db8152047beb3af07b75
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68417531"
---
# <a name="manage-azure-stack-storage-accounts"></a>Verwalten von Azure Stack-Speicherkonten

Hier erfahren Sie, wie Sie Azure Stack-Speicherkonten verwalten. Sie können Speicherkonten suchen und wiederherstellen sowie Speicherkapazität abhängig von Ihren geschäftlichen Anforderungen freigeben.

## <a name="find-a-storage-account"></a>Suchen eines Speicherkontos

Die Liste der Speicherkonten in der Region kann in Azure Stack folgendermaßen angezeigt werden:

1. Melden Sie sich am [Administratorportal](https://adminportal.local.azurestack.external) an.

2. Wählen Sie **Alle Dienste** > **Speicher** > **Speicherkonten** aus.

   ![Azure Stack-Speicherkonten](media/azure-stack-manage-storage-accounts/image4.png)

Standardmäßig werden die ersten 10 Konten angezeigt. Sie können weitere abrufen, indem Sie unten auf der Seite auf den Link **Weitere laden** klicken.

OR

Wenn Sie an einem bestimmten Speicherkonto interessiert sind, können Sie nur **die entsprechenden Konten abrufen**.


**So filtern Sie Konten:**

1. Klicken Sie oben im Bereich auf **Filter**.
2. Im Filterbereich können Sie Werte für **Kontoname**, **Abonnement-ID** oder **Status** angeben, um die angezeigte Speicherkontoliste einzugrenzen. Nutzen Sie die Optionen je nach Bedarf.
3. Der Filter wird automatisch auf die Liste angewendet, während Sie tippen.  .
   
    ![Filtern von Azure Stack-Speicherkonten](media/azure-stack-manage-storage-accounts/image5.png)

4. Klicken Sie zum Zurücksetzen des Filters auf **Filter**, deaktivieren Sie die getroffene Auswahl, und aktualisieren Sie die Liste.

Mit dem Suchfeld (oben im Bereich mit der Speicherkontenliste) können Sie den ausgewählten Text in der Liste mit den Konten hervorheben. Sie können diese Funktion nutzen, wenn der vollständige Name oder die ID nicht ohne Weiteres verfügbar ist.

Hier können Sie Freitext verwenden, um das gewünschte Konto zu finden.

![Suchen nach Azure Stack-Speicherkonten](media/azure-stack-manage-storage-accounts/image6.png)

## <a name="look-at-account-details"></a>Überprüfen der Kontodetails
Wenn Sie die gewünschten Konten gefunden haben, können Sie auf ein Konto klicken, um bestimmte Details anzuzeigen. Ein neuer Bereich mit Kontodetails wird geöffnet, darunter der Typ des Kontos, die Erstellungszeit, der Standort usw.

![](media/azure-stack-manage-storage-accounts/image7.png)

## <a name="recover-a-deleted-account"></a>Wiederherstellen eines gelöschten Kontos
Es gibt Situationen, in denen Sie ein gelöschtes Konto wiederherstellen müssen.

In Azure Stack gibt es hierfür eine einfache Möglichkeit:

1. Navigieren Sie zur Liste der Speicherkonten. Weitere Informationen finden Sie in diesem Artikel unter „Suchen eines Speicherkontos“.
2. Suchen Sie das gewünschte Konto in der Liste. Möglicherweise müssen Sie filtern.
3. Überprüfen Sie den *Status* des Kontos. Dieser müsste **Gelöscht** lauten.
4. Klicken Sie auf das Konto, um den Bereich mit den Kontodetails zu öffnen.
5. Klicken Sie oben in diesem Bereich auf die Schaltfläche **Wiederherstellen**.
6. Klicken Sie auf **Ja**, um zu bestätigen.
   
   ![](media/azure-stack-manage-storage-accounts/image8.png)
7. Die Wiederherstellung wird jetzt *ausgeführt. . .Warten* Sie auf den Hinweis, dass sie erfolgreich war.
   Sie können auch das Glockensymbol oben im Portal wählen, um eine Fortschrittsanzeige einzublenden.
   
   ![](media/azure-stack-manage-storage-accounts/image9.png)
   
   Sobald das wiederhergestellte Konto erfolgreich synchronisiert wurde, kann es wieder verwendet werden.

### <a name="some-gotchas"></a>Häufige Fehler
* Der Status des gelöschten Kontos gibt an, dass es **aus der Aufbewahrung entfernt** wurde.
  
  Die Entfernung aus der Aufbewahrung bedeutet, dass der Aufbewahrungszeitraum für das gelöschte Konto überschritten ist und es unter Umständen nicht wiederhergestellt werden kann.
* Das gelöschte Konto wird nicht in der Liste der Benutzerkonten angezeigt.
  
  Ihr Konto wird in der Kontoliste ggf. nicht angezeigt, wenn für das gelöschte Konto bereits der Garbage Collection-Vorgang durchgeführt wurde. In diesem Fall kann es nicht wiederhergestellt werden. Siehe [Freigeben von Kapazität](#reclaim) in diesem Artikel.

## <a name="set-the-retention-period"></a>Festlegen des Aufbewahrungszeitraums
Die Einstellung für den Aufbewahrungszeitraum ermöglicht einem Cloudbetreiber das Festlegen eines Zeitraums in Tagen (zwischen 0 und 9999 Tage), in dem gelöschte Konten ggf. wiederhergestellt werden können. Der Standardaufbewahrungszeitraum ist auf 0 Tage festgelegt. Mit dem Wert „0“ wird festgelegt, dass alle gelöschten Konten sofort aus der Aufbewahrung entfernt und für die regelmäßige automatische Speicherbereinigung markiert werden.

**Ändern des Aufbewahrungszeitraums:**

1. Melden Sie sich am [Administratorportal](https://adminportal.local.azurestack.external) an.
2. Wählen Sie **Alle Dienste** > **Regionsverwaltung** unter **Verwaltung** aus.
3. Wählen Sie **Ressourcenanbieter** > **Speicher** > **Einstellungen** aus. Der Pfad lautet: „Startseite“ > „*Region* – Ressourcenanbieter“ > „Speicher“.
4. Klicken Sie auf **Konfiguration**, und bearbeiten Sie dann den Wert für den Aufbewahrungszeitraum.

   Legen Sie die Anzahl von Tagen fest, und speichern Sie sie.
   
   Dieser Wert ist sofort wirksam und gilt für Ihre gesamte Region.

   ![](media/azure-stack-manage-storage-accounts/image10.png)

## <a name="reclaim"></a>Freigeben von Kapazität
Einer der Nebeneffekte eines Aufbewahrungszeitraums ist, dass ein gelöschtes Konto weiterhin Kapazität verbraucht, bis sein Aufbewahrungszeitraum abläuft. Als Cloudbetreiber benötigen Sie möglicherweise eine Möglichkeit zum Freigeben des Speicherplatzes von gelöschten Konten, auch wenn der Aufbewahrungszeitraum noch nicht abgelaufen ist.

Sie können Kapazität entweder mithilfe des Azure-Portals oder mit PowerShell freigeben.

**Freigeben von Kapazität mithilfe des Portals:**
1. Navigieren Sie zum Bereich „Speicherkonten“. Siehe „Suchen eines Speicherkontos“.
2. Klicken Sie oben im Bereich auf **Speicherplatz freigeben**.
3. Lesen Sie die Meldung, und klicken Sie auf **OK**.

    ![](media/azure-stack-manage-storage-accounts/image11.png)
4. Warten Sie auf eine Erfolgsmeldung – siehe Glockensymbol im Portal.

    ![](media/azure-stack-manage-storage-accounts/image12.png)
5. Aktualisieren Sie die Seite „Speicherkonten“. Die gelöschten Konten werden nicht mehr in der Liste angezeigt, da sie endgültig gelöscht wurden.

Sie können auch PowerShell verwenden, um den Aufbewahrungszeitraum explizit außer Kraft zu setzen und sofort Kapazität freizugeben.

**Freigeben von Kapazität mithilfe von PowerShell:**   

1. Vergewissern Sie sich, dass Azure PowerShell installiert und konfiguriert ist. Ist dies nicht der Fall, gehen Sie folgendermaßen vor: 
   * Anweisungen zur Installation und zur Zuordnung der aktuellen Azure PowerShell-Version an Ihr Azure-Abonnement finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
   Weitere Informationen zu Azure Resource Manager-Cmdlets finden Sie unter [Verwenden von Azure PowerShell mit Azure Resource Manager](https://go.microsoft.com/fwlink/?LinkId=394767).
2. Führen Sie die folgenden Cmdlets aus:

> [!NOTE]  
> Wenn Sie diese Cmdlets ausführen, wird das Konto samt Inhalt endgültig gelöscht. Es kann nicht wiederhergestellt werden. Verwenden Sie diese Option mit Umsicht.

```powershell  
    $farm_name = (Get-AzsStorageFarm)[0].name
    Start-AzsReclaimStorageCapacity -FarmName $farm_name
```

Weitere Informationen finden Sie in der [Azure Stack-PowerShell-Dokumentation](https://docs.microsoft.com/powershell/azure/azure-stack/overview).
 

## <a name="next-steps"></a>Nächste Schritte

 - Informationen zum Verwalten von Berechtigungen finden Sie unter [Verwalten der rollenbasierten Zugriffssteuerung](azure-stack-manage-permissions.md).
 - Informationen zum Verwalten der Speicherkapazität für Azure Stack finden Sie unter [Verwalten der Speicherkapazität für Azure Stack](azure-stack-manage-storage-shares.md).
