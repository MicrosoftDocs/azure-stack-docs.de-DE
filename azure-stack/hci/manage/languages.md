---
title: Ändern von Sprachen in Azure Stack HCI
description: Dieses Thema enthält Anleitungen zum Ändern von Sprachen im Azure Stack HCI-Betriebssystem, in Windows 10, dem Windows Admin Center und in Microsoft Edge.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 07/21/2020
ms.openlocfilehash: 09245e4498e87896b95df993e23693ae1ed23eaf
ms.sourcegitcommit: a15a0f955bac922cebb7bf90a72384fd84ddfe56
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2020
ms.locfileid: "86947332"
---
# <a name="change-languages-in-azure-stack-hci"></a>Ändern von Sprachen in Azure Stack HCI

>Gilt für: Azure Stack HCI, Version 20H2; Windows Server 2019

Sie können Ihre bevorzugte Sprache auf den von Ihnen verwalteten Servern an einem Verwaltungs-PC, im Windows Admin Center, in Microsoft Edge und im Azure Stack HCI-Betriebssystem ändern. Das Windows Admin Center verbleibt in der von Ihnen gewählten Sprache, unabhängig von Änderungen an den Spracheinstellungen in diesen anderen Ressourcen.

Dieser Artikel erläutert das Wechseln der Sprache in diesen Produkten:
- Windows 10
- Windows Admin Center
- Microsoft Edge
- Server Core im Azure Stack HCI-Betriebssystem

## <a name="change-the-language-in-the-management-pc"></a>Ändern der Sprache des Verwaltungs-PCs
Wenn Sie auf Ihrem Verwaltungs-PC Windows 10 verwenden, können Sie ein Sprachpaket für die Verwendung in Windows 10, den von Ihnen verwendeten Apps und den besuchten Websites installieren. Sie können darüber hinaus Ihre Tastatursprache ändern und die Eingabesprache in einer bevorzugten Sprachreihenfolge für Websites und Apps festlegen.

Weder das Ändern der Sprache, die Sie in Windows 10 verwenden, noch das der Tastatursprache wirkt sich auf die Anzeigesprache in Windows Admin Center aus.

   >[!IMPORTANT]
   > Nach dem Ändern Ihrer Sprache in Windows 10 empfehlen wir Ihnen, Ihre bevorzugte Tastatursprache als Standard festzulegen, bevor Sie sich abmelden, um potenzielle Eingabeprobleme bei der Anmeldung über Tastatur zu vermeiden.

Weitere Informationen finden Sie unter [Verwalten der Spracheinstellungen für Eingabe und Anzeige in Windows 10](https://support.microsoft.com/help/4496404/windows-10-manage-the-input-and-display-language).

## <a name="change-the-language-in-windows-admin-center"></a>Ändern der Sprache in Windows Admin Center
Sie können die Sprache und das Regionsformat in Windows Admin Center nach Bedarf gemäß Ihrem Standort ändern. Das Ändern dieser Optionen in Windows Admin Center hat keine Auswirkung auf die Spracheinstellung des Verwaltungs-PCs, auf dem Windows 10 ausgeführt wird.

So ändern Sie die Sprache in Windows Admin Center:
1. Wählen Sie im Bereich **Alle Verbindungen** das Zahnradsymbol **Einstellungen** und dann unter **Einstellungen** die Option **Sprache/Region** aus.
1. Klappen Sie die Dropdownliste **Sprache** auf, um Ihre bevorzugte Sprache auszuwählen, und klappen Sie dann bei Bedarf die Dropdownliste **Regionales Format** auf, um eine andere Region auszuwählen.
1. Wählen Sie **Speichern und neu laden** aus.

    :::image type="content" source="media/languages/language-region.png" alt-text="Seite „Sprache/Region“ in Windows Admin Center":::

Weitere Informationen finden Sie unter [Windows Admin Center – Einstellungen](/windows-server/manage/windows-admin-center/configure/settings).

## <a name="change-the-language-in-microsoft-edge"></a>Ändern der Sprache in Microsoft Edge
Sie können Microsoft Edge unterstützte Sprachen hinzufügen und die Reihenfolge Ihrer bevorzugten Sprachen im Browser ändern. Außerdem können Sie dem Browser eine Übersetzungserweiterung für Fremdsprachen hinzufügen, um Übersetzungen anzuzeigen.

Weitere Informationen finden Sie unter [Microsoft Edge-Sprachunterstützung](/deployedge/microsoft-edge-supported-languages).

## <a name="change-the-language-in-server-core"></a>Ändern der Sprache in Server Core
Wenn Sie die Sprache in Server Core des Betriebssystems Azure Stack HCI ändern müssen, empfehlen wir, dies nach dem Clustering Ihrer Server vorzunehmen. Sie können Server Core unterstützte Sprachpakete hinzufügen und anschließend Sprache und Tastaturlayout wie gewünscht ändern. Außerdem können Sie einen Windows PowerShell-Befehl verwenden, um die aktuelle Sprache und die Tastatureingabemethode außer Kraft zu setzen.

Jedes Sprachpaket wird im Verzeichnis *%SystemRoot%\System32\\%Language-ID%* installiert. Beispielsweise ist *C:\Windows\System32\es-ES* der Speicherort des spanischen Sprachpakets. Jedes Sprachpaket umfasst ca. 50 MB. Beim Installieren aller 38 Sprachpakete beträgt die Größe des dabei erstellten Images etwa 2 GB.

Weitere Informationen finden Sie unter [Verfügbare Sprachen für Windows](/windows-hardware/manufacture/desktop/available-language-packs-for-windows).

So rufen Sie Sprachpakete manuell ab und fügen Sie den Betriebssystem hinzu:

1. Fügen Sie Server Core ein Sprachpaket mithilfe des Tools **DISM / Add-WindowsPackage** hinzu. Der PowerShell-Befehl `Add-WindowsPackage` ist das Äquivalent der ausführbaren DISM-Datei.

    Weitere Informationen finden Sie unter [Hinzufügen von Sprachen zu Windows-Images](/windows-hardware/manufacture/desktop/add-language-packs-to-windows).

1. Wir empfehlen, Sprachfeatures bei Bedarf (Features on Demand, FODs) hinzuzufügen, um zusätzliche Funktionalität für die hinzugefügte Sprache zu aktivieren, wie im folgenden Beispiel:

    ```DOS
    dism /online /add-capability /capabilityname:Language.Basic~~~de-de~0.0.1.0
    ```

    Weitere Informationen finden Sie unter [Features bei Bedarf (FOD) für Sprachen und Regionen](/windows-hardware/manufacture/desktop/features-on-demand-language-fod).

1. Sie können das PowerShell-Cmdlet **Set-WinUILanguageOverride** verwenden, um die Sprache der Windows-Benutzeroberfläche im Betriebssystem des aktuellen Benutzerkontos zu ändern. Im folgenden Beispiel gibt `de-DE` Deutsch als aktuelle Spracheinstellung im Betriebssystem an:

    ```PowerShell
    Set-WinUILanguageOverride de-DE
    ```

    Weitere Informationen finden Sie unter [Set-WinUILanguageOverride](/powershell/module/international/set-winuilanguageoverride?view=win10-ps).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie auch unter:

- [Hinzufügen oder Entfernen von Servern für einen Azure Stack HCI-Cluster](./add-cluster.md)
