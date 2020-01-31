---
title: Vorbereiten des ASDK-Hostcomputers
description: Erfahren Sie, wie Sie den Azure Stack Development Kit-Hostcomputer (ASDK) für die ASDK-Installation vorbereiten.
author: justinha
ms.topic: article
ms.date: 08/28/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 08/28/2019
ms.openlocfilehash: 81e8e8f470988d84441d238ef677950df2d2334b
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76873616"
---
# <a name="prepare-the-asdk-host-computer"></a>Vorbereiten des ASDK-Hostcomputers
Bevor Sie das Azure Stack Development Kit (ASDK) auf dem Hostcomputer installieren können, muss der ASDK-Host für die Installation vorbereitet werden. Nach der Vorbereitung des Hosts wird der Computer von der Festplatte des virtuellen Computers („CloudBuilder.vhdx“) gestartet, um mit der ASDK-Bereitstellung zu beginnen.

## <a name="prepare-the-development-kit-host-computer"></a>Vorbereiten des Development Kit-Hostcomputers
Bevor Sie das ASDK auf dem Hostcomputer installieren können, muss die Umgebung des ASDK-Hostcomputers vorbereitet werden. Führen Sie die folgenden Schritte aus, um die Umgebung vorzubereiten:

1. Melden Sie sich als lokaler Administrator auf Ihrem ASDK-Hostcomputer an.
2. Stellen Sie sicher, dass die Datei „CloudBuilder.vhdx“ in den Stammordner des Laufwerks „C:\“ verschoben wurde (`C:\CloudBuilder.vhdx`).
3. Führen Sie zum Herunterladen der ASDK-Installationsdatei („asdk-installer.ps1“) aus dem [GitHub-Repository mit den Azure Stack-Tools](https://github.com/Azure/AzureStack-Tools) in den Ordner **C:\AzureStack_Installer** auf Ihrem ASDK-Hostcomputer das folgende Skript aus:

   > [!IMPORTANT]
   > Stellen Sie sicher, dass Sie die Datei „asdk-installer.ps1“ jedes Mal herunterladen, wenn Sie das ASDK installieren. An diesem Skript werden häufig Änderungen vorgenommen, und es sollte immer die neueste Version für jede ASDK-Bereitstellung verwendet werden. Ältere Versionen des Skripts funktionieren möglicherweise nicht mit dem neuesten Release.

   ```powershell
   # Variables
   $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
   $LocalPath = 'C:\AzureStack_Installer'
   # Create folder
   New-Item $LocalPath -Type directory
   # Enforce usage of TLSv1.2 to download from GitHub
   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
   # Download file
   Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
   ```

4. Führen Sie in einer PowerShell-Konsole mit erhöhten Rechten das Skript **C:\AzureStack_Installer\asdk-installer.ps1** aus, und klicken Sie anschließend auf **Umgebung vorbereiten**.

    ![Vorbereiten der Umgebung im ASDK](media/asdk-prepare-host/1.PNG) 

5. Navigieren Sie auf der Seite **„CloudBuilder.vhdx“ auswählen** des Installationsprogramms zu der in den [vorherigen Schritten](asdk-download.md) heruntergeladenen und extrahierten **CloudBuilder.vhdx**-Datei, und wählen Sie sie aus. Auf dieser Seite können Sie auch das Kontrollkästchen **Treiber hinzufügen** aktivieren, wenn Sie dem ASDK-Hostcomputer zusätzliche Treiber hinzufügen müssen. Klicken Sie auf **Weiter**.  

    ![Auswählen von „cloudbuilder.vhdx“ und Hinzufügen von Treibern zum ASDK](media/asdk-prepare-host/2.PNG)

6. Geben Sie auf der Seite **Optionale Einstellungen** die Informationen des lokalen Administratorkontos für den ASDK-Hostcomputer an, und klicken Sie dann auf **Weiter**.

    Wenn Sie die Anmeldeinformationen des lokalen Administrators in diesem Schritt nicht angeben, benötigen Sie direkten Zugriff oder KVM-Zugriff auf den Host, nachdem der Computer während der ASDK-Einrichtung neu gestartet wurde.

   ![Optionale Einstellungen im ASDK – Angeben der Informationen zum lokalen Administratorkonto](media/asdk-prepare-host/3.PNG)

    Sie können auch Werte für die folgenden optionalen Einstellungen angeben:
    - **Computername**: Diese Option legt den Namen für den ASDK-Host fest. Der Name muss die FQDN-Anforderungen erfüllen und darf höchstens 15 Zeichen umfassen. Der Standardwert ist ein von Windows generierter zufälliger Computername.

        - Wählen Sie einen Netzwerkadapter aus. Stellen Sie sicher, dass Sie eine Verbindung mit dem Adapter herstellen können, bevor Sie auf **Weiter** klicken.

            ![Screenshot: Einstellungen für Netzwerkadapter](media/asdk-prepare-host/step-four-network-adapter.png)

        - Stellen Sie sicher, dass die angezeigten Werte für **IP-Adresse**, **Gateway** und **DNS** korrekt sind, geben Sie eine gültige IP-Adresse für den **Zeitserver** an, und klicken Sie auf **Weiter**.

            >[!TIP]
            >Besuchen Sie zum Ermitteln der IP-Adresse eines Zeitservers [ntppool.org](https://www.ntppool.org/), oder pingen Sie „time.windows.com“. 

            ![Screenshot: IP-Konfigurationseinstellungen](media/asdk-prepare-host/step-five-host-ip-config.png)

7. Klicken Sie auf **Weiter**, um den Vorbereitungsprozess zu starten.
8. Wenn die Vorbereitung **Abgeschlossen** anzeigt, klicken Sie auf **Weiter**.

    ![Vorbereiten der Umgebung im ASDK](media/asdk-prepare-host/4.PNG)

9. Klicken Sie auf **Jetzt neu starten**, um den ASDK-Hostcomputer in „CloudBuilder.vhdx“ zu starten und den [Bereitstellungsprozess fortzusetzen](asdk-install.md).

    ![Neustarten des ASDK](media/asdk-prepare-host/5.PNG)


## <a name="next-steps"></a>Nächste Schritte
[Installieren des ASDK](asdk-install.md)
