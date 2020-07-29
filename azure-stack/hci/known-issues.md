---
title: Bekannte Probleme bei Azure Stack HCI
description: In diesem Thema werden bekannte Probleme bei Azure Stack HCI erläutert.
author: JohnCobb1
ms.author: v-johcob
ms.topic: troubleshooting
ms.date: 07/21/2020
ms.openlocfilehash: e740dacb88374e0203ad7363b291a806c5ea3139
ms.sourcegitcommit: 0e52f460295255b799bac92b40122a22bf994e27
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/21/2020
ms.locfileid: "86868060"
---
# <a name="known-issues-for-azure-stack-hci"></a>Bekannte Probleme bei Azure Stack HCI

>Gilt für: Azure Stack HCI, Version 20H2

Dieses Thema behandelt bekannte Probleme bei Azure Stack HCI ausführlich

- Die Verbindung zwischen dem lokalen Cluster und dem Azure Stack HCI-Clouddienst unterstützt [Azure Private Link](https://azure.microsoft.com/services/private-link) zurzeit noch nicht.
- Derzeit ist der Azure Stack HCI-Clouddienst nur in ausgewählten Regionen verfügbar.
- Azure Stack HCI wurde am 21. Juli 2020 angekündigt. Es kann mehrere Tage dauern, bis seine Benutzeroberflächenerweiterung für das Azure-Portal weltweit sichtbar wird. Während dieser Zeit wird Ihr Cluster möglicherweise im Azure-Portal als Standard-Ressourcenseite angezeigt. Vielen Dank für Ihre Geduld.
- Wenn Sie sich interaktiv beim Betriebssystem anmelden, ist der Begrüßungsbildschirm mit dem Text „Willkommen bei Azure Stack HCI“ noch nicht in Tschechisch, Ungarisch, Niederländisch, Polnisch, Schwedisch und Türkisch lokalisiert verfügbar (Gebietsschemacodes cs-cz, hu-hu, nl-nl, pl-pl, pt-pt, sv-se, tr-tr). Darüber hinaus akzeptierten Eingabeaufforderungen wie „[Y]es/[N]o“ in allen Sprachen außer Englisch nur die Werte „Y“ und „N“, selbst wenn die Aufforderung selbst irreführenderweise lokalisiert ist, wie [O]ui/[N]on auf Französisch oder [J]a/[N]ein auf Deutsch.
- Wenn Sie eine Evaluierungsversion von Azure Stack HCI mit geschachtelter Virtualisierung ausführen, begegnet Ihnen möglicherweise ein Fehler wie „Hyper-V kann nicht installiert werden, da die Virtualisierungsunterstützung nicht aktiviert ist“. Dies hat die Ursache, dass Azure Stack HCI von virtualisierungsgestützer Sicherheit abhängt. Es gibt zwei mögliche Problemumgehungen: (1) Verwenden Sie stattdessen virtuelle Computer mit Hyper-V der 1. Generation oder (2) injizieren Sie die Hyper-V-Funktion offline in die VHDX-Datei der VM. Führen Sie auf dem Host den folgenden PowerShell-Befehl auf allen VMs im heruntergefahrenen Zustand aus, die als Azure Stack HCI-Knoten fungieren: **Install-WindowsFeature -Vhd <path> -Name Hyper-V, RSAT-Hyper-V-Tools, Hyper-V-Powershell**.
