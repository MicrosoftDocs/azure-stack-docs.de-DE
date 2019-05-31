---
title: Erstellen eines virtuellen Linux-Servers mit Azure Stack | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie einen virtuellen Linux-Server mit Azure Stack erstellen.
services: azure-stack
cloud: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 05/16/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.custom: mvc
ms.lastreviewed: 12/03/2018
ms.openlocfilehash: 379c473080fdbddec811d7982a571cd00e6e1e62
ms.sourcegitcommit: be5382f715a9c1c18c660b630d8fcd823f13aae3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/24/2019
ms.locfileid: "66197421"
---
# <a name="quickstart-create-a-linux-server-vm-with-the-azure-stack-portal"></a>Schnellstart: Erstellen eines virtuellen Linux-Servers mit dem Azure Stack-Portal

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Sie können einen virtuellen Computer (VM) mit Ubuntu Server 16.04 LTS über das Azure Stack-Portal erstellen. Befolgen Sie die Schritte in diesem Artikel zum Erstellen und Verwenden eines virtuellen Computers. In diesem Artikel führen Sie auch die folgenden Schritte aus:

* Herstellen der Verbindung mit dem virtuellen Computer über einen Remoteclient
* Installieren eines NGINX-Webservers
* Bereinigen Ihrer Ressourcen

> [!NOTE]  
> Die Screenshots in diesem Artikel wurden aktualisiert, um die Änderungen anzuzeigen, die in Version 1808 von Azure Stack eingeführt wurden. 1808 bietet Unterstützung für die Verwendung von *verwalteten Datenträgern*, zusätzlich zu nicht verwalteten Datenträgern. Wenn Sie eine frühere Version nutzen, sehen einige Screenshots für Aufgaben, z. B. die Datenträgerauswahl, anders als in Ihrer Benutzeroberfläche aus.  


## <a name="prerequisites"></a>Voraussetzungen

* **Ein Linux-Image im Azure Stack-Marketplace**

   Im Azure Stack-Marketplace ist nicht standardmäßig ein Linux-Image enthalten. Bitten Sie Ihren Azure Stack-Betreiber, das Image **Ubuntu Server 16.04 LTS** auf Ihrem Marketplace bereitzustellen. Hierzu kann der Operator die Schritte des Artikels [Herunterladen von Marketplace-Elementen von Azure in Azure Stack](../operator/azure-stack-download-azure-marketplace-item.md) ausführen.

* **Zugriff auf einen SSH-Client**

   Wenn Sie das Azure Stack Development Kit (ASDK) verwenden, haben Sie unter Umständen keinen Zugriff auf einen SSH-Client. Für den Fall, dass Sie einen Client benötigen, stehen mehrere Pakete zur Verfügung, die einen SSH-Client enthalten. So enthält beispielsweise PuTTY einen SSH-Client und einen SSH-Schlüsselgenerator (puttygen.exe). Weitere Informationen zu den verfügbaren Paketen finden Sie im Artikel [Verwenden eines öffentlichen SSH-Schlüssels](azure-stack-dev-start-howto-ssh-public-key.md).

   In dieser Schnellstartanleitung wird PuTTY verwendet, um die SSH-Schlüssel zu generieren und eine Verbindung mit dem virtuellen Linux-Server herzustellen. Navigieren Sie zu [https://www.putty.org/](https://www.putty.org), um PuTTY herunterzuladen und zu installieren.

## <a name="create-an-ssh-key-pair"></a>Erstellen eines SSH-Schlüsselpaars

Für die Schritte in diesem Artikel benötigen Sie ein SSH-Schlüsselpaar. Falls Sie bereits über ein SSH-Schlüsselpaar verfügen, können Sie diesen Schritt überspringen.

1. Navigieren Sie zum PuTTY-Installationsordner (der Standardspeicherort ist `C:\Program Files\PuTTY`), und führen Sie `puttygen.exe` aus.
2. Legen Sie im Fenster „PuTTY Key Generator“ die Option **Type of key to generate** auf **RSA** und **Number of bits in a generated key** auf **2048** fest. Klicken Sie abschließend auf **Generate** (Generieren).

   ![PuTTY Key Generator: Konfiguration](media/azure-stack-quick-linux-portal/Putty01.PNG)

3. Bewegen Sie den Mauszeiger nach Belieben im Fenster von PuTTY Key Generator, um einen Schlüssel zu generieren.
4. Klicken Sie nach Abschluss der Schlüsselgenerierung auf **Save public key** (Öffentlichen Schlüssel speichern) und anschließend auf **Save private key** (Privaten Schlüssel speichern), um Ihre Schlüssel in Dateien zu speichern.

   ![PuTTY Key Generator: Ergebnisse](media/azure-stack-quick-linux-portal/Putty02.PNG)

## <a name="sign-in-to-the-azure-stack-portal"></a>Anmelden beim Azure Stack-Portal

Die Adresse des Azure Stack-Portals hängt davon ab, mit welchem Azure Stack-Produkt Sie eine Verbindung herstellen:

* Navigieren Sie für das Azure Stack Development Kit (ASDK) zu folgender Adresse: https://portal.local.azurestack.external.
* Rufen Sie bei einem integrierten Azure Stack-System die vom Azure Stack-Operator bereitgestellte URL auf.

## <a name="create-the-vm"></a>Erstellen des virtuellen Computers

1. Klicken Sie links oben im Azure Stack-Portal auf **Ressource erstellen**.

2. Wählen Sie **Compute** und dann **Ubuntu Server 16.04 LTS**.
   
   ![Auswählen des Linux-Servers](media/azure-stack-quick-linux-portal/select.png)
1. Klicken Sie auf **Create**.

4. Geben Sie die VM-Informationen ein. Wählen Sie unter **Authentifizierungstyp** die Option **Öffentlicher SSH-Schlüssel**. Fügen Sie den öffentlichen SSH-Schlüssel ein, den Sie zuvor gespeichert haben, und klicken Sie anschließend auf **OK**.

   > [!NOTE]
   > Entfernen Sie alle voran- und nachgestellten Leerzeichen des Schlüssels.

   ![Bereich „Grundlagen“: VM konfigurieren](media/azure-stack-quick-linux-portal/linux-01.PNG)

5. Wählen Sie für die VM die Option **D1** aus.

   ![Bereich „Größe“: VM-Größe auswählen](media/azure-stack-quick-linux-portal/linux-02.PNG)

6. Nehmen Sie auf der Seite **Einstellungen** sämtliche gewünschten Änderungen an den Standardwerten vor.
   
   - Ab Azure Stack Version 1808 können Sie den **Speicher** konfigurieren und die Option *Verwaltete Datenträger* auswählen. In Versionen vor 1808 können nur nicht verwaltete Datenträger verwendet werden.
     ![Konfigurieren von Speicher für nicht verwaltete Datenträger](media/azure-stack-quick-linux-portal/linux-03.PNG)
    
     Wenn Ihre Konfigurationen abgeschlossen sind, klicken Sie auf **OK**, um den Vorgang fortzusetzen.

7. Klicken Sie auf der Seite **Zusammenfassung** auf **OK**, um die Bereitstellung der VM zu starten.  
   ![Bereitstellen](media/azure-stack-quick-linux-portal/deploy.png)

## <a name="connect-to-the-vm"></a>Herstellen der Verbindung zur VM

1. Klicken Sie auf der VM-Seite auf **Verbinden**. Sie können die SSH-Verbindungszeichenfolge ermitteln, die für die Verbindungsherstellung mit der VM benötigt wird. 

2. Öffnen Sie PuTTY.

3. Auf dem Bildschirm für die PuTTY-Konfiguration können Sie im Fenster mit den **Kategorien** nach oben oder unten scrollen. Scrollen Sie nach unten zu **SSH**, erweitern Sie **SSH**, und klicken Sie anschließend auf **Auth** (Authentifizieren). Klicken Sie auf **Browse** (Durchsuchen), und wählen Sie die Datei mit dem privaten Schlüssel aus, die Sie zuvor gespeichert haben.
   ![Virtuellen Computer verbinden](media/azure-stack-quick-linux-portal/putty03.PNG)

4. Scrollen Sie im Bereich mit den **Kategorien**nach oben, und klicken Sie auf **Session** (Sitzung).
5. Fügen Sie im Feld **Host Name (or IP address)** (Hostname (oder IP-Adresse)) die Verbindungszeichenfolge aus dem Azure Stack-Portal ein. In diesem Beispiel ist dies `asadmin@192.168.102.34`.

   ![PuTTY-Konfiguration: Verbindungszeichenfolge](media/azure-stack-quick-linux-portal/Putty04.PNG)

6. Klicken Sie auf **Open** (Öffnen), um eine Sitzung für die VM zu öffnen.

   ![Linux-Sitzung](media/azure-stack-quick-linux-portal/Putty05.PNG)

## <a name="install-the-nginx-web-server"></a>Installieren des NGINX-Webservers

Verwenden Sie die folgenden Bash-Befehle, um Paketquellen zu aktualisieren und das neueste NGINX-Paket auf der VM zu installieren.

```bash
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Beenden Sie die SSH-Sitzung nach Abschluss der NGINX-Installation, und öffnen Sie im Azure Stack-Portal die Übersichtsseite der VM.

## <a name="open-port-80-for-web-traffic"></a>Öffnen von Port 80 für Webdatenverkehr

Mit einer Netzwerksicherheitsgruppe (NSG) wird eingehender und ausgehender Datenverkehr geschützt. Bei der Erstellung einer VM im Azure Stack-Portal wird für SSH-Verbindungen an Port 22 eine Eingangsregel für Datenverkehr erstellt. Da diese VM einen Webserver hostet, muss eine NSG-Regel erstellt werden, die Webdatenverkehr an Port 80 zulässt.

1. Klicken Sie auf der Seite **Übersicht** der VM auf den Namen der **Ressourcengruppe**.
2. Wählen Sie die **Netzwerksicherheitsgruppe** für die VM aus. Die NSG können Sie anhand der Spalte **Typ** identifizieren.
3. Klicken Sie im Menü auf der linken Seite unter **Einstellungen** auf **Eingangssicherheitsregeln**.
4. Klicken Sie auf **Hinzufügen**.
5. Geben Sie unter **Name** den Text **http** ein. Stellen Sie sicher, dass **Portbereich** auf 80 und **Aktion** auf **Zulassen** festgelegt ist.
6. Klicken Sie auf **OK**.

## <a name="view-the-nginx-welcome-page"></a>Anzeigen der NGINX-Willkommensseite

Nachdem NGINX installiert und Port 80 auf der VM geöffnet wurde, können Sie über die öffentliche IP-Adresse der VM auf den Webserver zugreifen. (Die öffentliche IP-Adresse wird auf der Übersichtsseite der VM angezeigt.)

Öffnen Sie einen Browser, und navigieren Sie zu `http://<public IP address>`.

![Willkommensseite des NGINX-Webservers](media/azure-stack-quick-linux-portal/linux-05.PNG)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Bereinigen Sie die Ressourcen, die Sie nicht mehr benötigen. Wählen Sie zum Löschen der VM und ihrer Ressourcen auf der VM-Seite die Ressourcengruppe aus, und klicken Sie dann auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen einfachen virtuellen Linux-Server mit einem Webserver bereitgestellt. Weitere Informationen zu Azure Stack-VMs finden Sie unter [Überlegungen zur Verwendung von virtuellen Computern in Azure Stack](azure-stack-vm-considerations.md).
