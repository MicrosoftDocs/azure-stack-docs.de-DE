---
title: Bekannte Probleme bei Azure Stack HCI
description: In diesem Thema werden bekannte Probleme bei Azure Stack HCI erläutert.
author: myoungerman
ms.author: v-myoung
ms.topic: troubleshooting
ms.date: 10/27/2020
ms.openlocfilehash: 05884ce83f6f35f8d75c632f67dabd38fa3c2814
ms.sourcegitcommit: 75603007badd566f65d01ac2eacfe48ea4392e58
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92688321"
---
# <a name="known-issues-for-azure-stack-hci"></a>Bekannte Probleme bei Azure Stack HCI

>Gilt für: Azure Stack HCI, Version 20H2

Dieses Thema behandelt bekannte Probleme bei Azure Stack HCI ausführlich

- Das Vorschauupdate (KB4580388) vom 20.10.2020 kann zu einem Fehler bei einer clusterfähigen Aktualisierung (CAU) führen, wenn einer der virtuellen Computer während der clusterfähigen Aktualisierung eine Livemigration ausführen soll. Um dies zu vermeiden, ändern Sie das Standardverhalten zeitweilig, damit beim CAU die Schnellmigration anstelle einer Livemigration verwendet wird. Weitere Informationen finden Sie in den [Versionshinweisen](release-notes.md#october-20-2020-preview-update-kb4580388).
- Die Verbindung zwischen dem lokalen Cluster und dem Azure Stack HCI-Clouddienst unterstützt [Azure Private Link](https://azure.microsoft.com/services/private-link) zurzeit noch nicht.
- Derzeit ist der Azure Stack HCI-Clouddienst nur in ausgewählten Regionen verfügbar.
- Wenn Sie sich interaktiv beim Betriebssystem anmelden, ist der Begrüßungsbildschirm mit dem Text „Willkommen bei Azure Stack HCI“ noch nicht in Tschechisch, Ungarisch, Niederländisch, Polnisch, Schwedisch und Türkisch lokalisiert verfügbar (Gebietsschemacodes cs-cz, hu-hu, nl-nl, pl-pl, pt-pt, sv-se, tr-tr). Darüber hinaus akzeptierten Eingabeaufforderungen wie „[Y]es/[N]o“ in allen Sprachen außer Englisch nur die Werte „Y“ und „N“, selbst wenn die Aufforderung selbst irreführenderweise lokalisiert ist, wie [O]ui/[N]on auf Französisch oder [J]a/[N]ein auf Deutsch.
- Wenn Sie eine Evaluierungsversion von Azure Stack HCI mit geschachtelter Virtualisierung ausführen, begegnet Ihnen möglicherweise ein Fehler wie „Hyper-V kann nicht installiert werden, da die Virtualisierungsunterstützung nicht aktiviert ist“. Dies hat die Ursache, dass Azure Stack HCI von virtualisierungsgestützer Sicherheit abhängt. Es gibt zwei mögliche Problemumgehungen: (1) Verwenden Sie stattdessen virtuelle Computer mit Hyper-V der 1. Generation, oder (2) injizieren Sie die Hyper-V-Funktion offline in die VHDX-Datei der VM. Führen Sie auf dem Host den folgenden PowerShell-Befehl auf allen VMs im heruntergefahrenen Zustand aus, die als Azure Stack HCI-Knoten fungieren: **Install-WindowsFeature -Vhd \<path> -Name Hyper-V, RSAT-Hyper-V-Tools, Hyper-V-Powershell** .
