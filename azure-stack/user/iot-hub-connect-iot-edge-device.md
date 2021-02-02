---
title: Herstellen einer Verbindung zwischen einem IoT Edge-Gerät und IoT Hub in Azure Stack Hub
description: In diesem Artikel erfahren Sie, wie Sie eine Verbindung zwischen einem IoT Edge-Gerät und IoT Hub in Azure Stack Hub herstellen.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/20/2020
ms.reviewer: dmolokanov
ms.lastreviewed: 11/20/2020
ms.openlocfilehash: 768952db6d93930f710dd80ea10e23ae6d8297a5
ms.sourcegitcommit: 0765de47f4a73e09192d34739e40c750b6e7abaf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920830"
---
# <a name="connect-an-iot-edge-device-to-iot-hub-on-azure-stack-hub"></a>Herstellen einer Verbindung zwischen einem IoT Edge-Gerät und IoT Hub in Azure Stack Hub

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

In diesem Artikel erfahren Sie, wie Sie eine Verbindung zwischen einem virtuellen IoT Edge-Gerät und einer IoT Hub-Instanz herstellen, die in Azure Stack Hub ausgeführt wird. Sie können dieselben allgemeinen Prozesse verwenden, um ein physisches Gerät mit Ihrer IoT Hub-Instanz zu verbinden.

## <a name="prerequisites"></a>Voraussetzungen

Achten Sie darauf, dass die folgenden Voraussetzungen erfüllt sind, bevor Sie fortfahren:

- Sie benötigen ein Konto, das Zugriffsberechtigungen für das [Azure Stack Hub-Benutzerportal](../user/azure-stack-use-portal.md) hat (mindestens die Berechtigungen der Rolle [Mitwirkender](../user/azure-stack-manage-permissions.md)).

- Arbeiten Sie mit Ihrem Administrator zusammen, um [IoT Hub für den Azure Stack Hub-Ressourcenanbieter zu installieren](../operator/iot-hub-rp-overview.md). In den Installationsschritten wird auch die Erstellung eines Angebots beschrieben, das den IoT Hub-Dienst enthält. 

- Sobald ein Angebot verfügbar ist, kann der Administrator Ihr Azure Stack Hub-Abonnement so erstellen oder aktualisieren, dass IoT Hub eingeschlossen wird. Alternativ können Sie [das neue Angebot abonnieren und Ihr eigenes Abonnement erstellen](azure-stack-subscribe-services.md).

- Es ist hilfreich, ein grundlegendes Verständnis für die Verschlüsselung mit öffentlichem Schlüssel (Public Key Encryption, PKI) zu erlangen. Insbesondere sollten Sie wissen, wie eine Zertifizierungsstelle und X509-Zertifikate verwendet werden, um eine Vertrauenskette zu erstellen. Die so geschaffene Vertrauensstellung ermöglicht Netzwerkknoten eine sichere Authentifizierung und Kommunikation, z. B. zwischen Ihrem IoT Hub-Dienst und Ihrem IoT Edge-Gerät. 

## <a name="overview"></a>Übersicht

Hier finden Sie eine Zusammenfassung der Schritte, die Sie durchführen müssen, um eine Verbindung zwischen einem IoT Edge-Gerät und Ihrer IoT Hub-Instanz in Azure Stack Hub herzustellen:

1. Erstellen Sie IoT Hub- und Linux-VM-Ressourcen in Ihrer Azure Stack Hub-Instanz. Die Linux-VM dient als virtuelles IoT Edge-Gerät. 
2. Konfigurieren Sie die Zertifikate, die für das IoT Edge-Gerät erforderlich sind. Die Anzahl erforderlicher Schritte hängt vom Typ der Zertifizierungsstelle ab, von der Ihr Stammzertifizierungsstellenzertifikat für IoT Hub veröffentlicht wurde.
3. Konfigurieren Sie die Software und Dienste, die für das IoT Edge-Gerät erforderlich sind.
4. Testen Sie das IoT Edge-Gerät, um sicherzustellen, dass es ordnungsgemäß funktioniert und mit Ihrer IoT Hub-Instanz kommuniziert.

Bevor Sie das Skript in den einzelnen Abschnitten ausführen, sollten Sie sicherstellen, dass Sie die Platzhalter im Skript so aktualisieren, dass sie der Konfiguration Ihrer Umgebung entsprechen. Notieren Sie sich die Werte, die Sie verwenden. Sie benötigen sie in späteren Abschnitten:

| Platzhalter | Beispiel | BESCHREIBUNG |
|-------------|---------|-------------|
| `<DEVICE-CA-CERT-NAME>` | `edged1cert`| Hierbei handelt es sich um den Namen, den Sie dem Zertifikat der Zertifizierungsstelle für Ihr IoT Edge-Gerät geben. |
| `<IOT-HUB-HOSTNAME>`| `test-hub-1.mgmtiothub.region.mydomain.com`| Hierbei handelt es sich um Ihren IoT Hub-Hostnamen. |
| `<IOT-HUB-ROOT-CA>`| `root.cer`| Hierbei handelt es sich um den Dateinamen, den Sie der exportierten Stammzertifizierungsstelle für IoT Hub geben (bei Verwendung einer privaten Zertifizierungsstelle). |
| `<DATA-DIR>`| `/home/edgeadmin/edged1`| Hierbei handelt es sich um den vollständigen Pfad zum Datenverzeichnis, das Sie auf der Linux-VM erstellen. |
| `<USER-ACCOUNT>`| `edgeadmin`| Hierbei handelt es sich um das Konto, das Sie verwenden, um sich bei der Linux-VM anzumelden. |

## <a name="create-azure-stack-hub-resources"></a>Erstellen von Azure Stack Hub-Ressourcen

Zuerst erstellen Sie die erforderlichen Azure Stack Hub-Ressourcen einschließlich einer IoT Hub-Instanz und einer Linux-VM. Sie verwenden das [Azure Stack Hub-Benutzerportal](../user/azure-stack-use-portal.md), um diese Ressourcen zu erstellen.

### <a name="create-an-iot-hub-resource"></a>Erstellen einer IoT Hub-Ressource

1. Melden Sie sich beim [Azure Stack Hub-Benutzerportal](../user/azure-stack-use-portal.md) über einen Computer an, der Zugriff auf Ihre Azure Stack Hub-Instanz hat.
2. Wenn Sie noch keine Instanz erstellt haben, befolgen Sie die Anleitung im Abschnitt [Erstellen eines IoT-Hubs](/azure/iot-hub/iot-hub-create-through-portal#create-an-iot-hub) des Artikels **Erstellen einer IoT Hub-Instanz über das Portal**, um eine IoT Hub-Ressource zu erstellen. 

   > [!IMPORTANT]
   > Achten Sie darauf, das [Azure Stack Hub-Benutzerportal](../user/azure-stack-use-portal.md) zu verwenden, um die Schritte in diesem Artikel auszuführen, NICHT das Azure-Portal. Beachten Sie außerdem, dass sich einige der Screenshots und Anweisungen möglicherweise leicht unterscheiden, da in Azure Stack Hub nicht alle Azure-Features zur Verfügung stehen. 

### <a name="deploy-and-configure-a-linux-vm"></a>Bereitstellen und Konfigurieren einer Linux-VM 

In diesem Abschnitt stellen Sie eine neue Linux-VM bereit, die als virtuelles IoT Edge-Gerät fungiert:

1. Erstellen Sie mithilfe des Schnellstarts [Erstellen eines virtuellen Linux-Servers mit dem Azure Stack Hub-Portal](azure-stack-quick-linux-portal.md) eine Linux-VM in Ihrer Azure Stack Hub-Instanz. Aktivieren Sie Port **SSH (22)** für die Eigenschaft **Öffentliche Eingangsports hinzufügen**, wenn die Seite **Einstellungen** angezeigt wird. 

   > [!NOTE]
   > Sie müssen nur die ersten fünf Abschnitte abschließen (bis zu **Herstellen einer Verbindung zur VM**), da Sie den NGINX-Webserver nicht benötigen. 

2. Nachdem Sie die VM mithilfe von PuTTY erstellt und eine Verbindung dazu hergestellt haben, erstellen Sie im Basisverzeichnis Ihres Benutzerkontos ein Datenunterverzeichnis. Im Folgenden sehen Sie ein Beispiel:

   ```bash
   mkdir edged1
   ```

3. Richten Sie das Zertifikatgenerierungstool mithilfe einer der folgenden Methoden ein. Jede der Methoden lädt Dateien aus dem GitHub-Repository für IoT Edge herunter, die später für das Erstellen von Gerätezertifikaten erforderlich sind:

   **Verwenden Sie das cURL-Skript:**
   ```bash
   # Navigate to the edge device data directory
   cd <DATA-DIR>
   
   # Download certGen.sh script file, and openssl_root_ca.cnf config file
   curl -Lo certGen.sh https://raw.githubusercontent.com/Azure/iotedge/master/tools/CACertificates/certGen.sh
   curl -Lo openssl_root_ca.cnf https://raw.githubusercontent.com/Azure/iotedge/master/tools/CACertificates/openssl_root_ca.cnf
   ```
   **Verwenden Sie Git zum Klonen des Repository:**
   
   ```bash
   # Navigate to the home directory
   cd /home/<USER-ACCOUNT>
   
   # Clone the iotedge repo, which contains the certGen.sh script file and openssl_root_ca.cnf config file
   git clone https://github.com/Azure/iotedge.git
   
   # Navigate to the edge device data directory
   cd <DATA-DIR>
   
   # Copy certGen.sh and openssl_root_ca.cnf into your working directory
   cp /home/<USER-ACCOUNT>/iotedge/tools/CACertificates/*.cnf .
   cp /home/<USER-ACCOUNT>/iotedge/tools/CACertificates/certGen.sh .
   ```

## <a name="configure-iot-edge-device-certificates"></a>Konfigurieren von IoT Edge-Gerätezertifikaten

In diesem Abschnitt schließen Sie die VM-Zertifikatkonfiguration ab, die für das virtuelle IoT Edge-Gerät erforderlich ist. 

Ihr IoT Hub-Dienst und das IoT Edge-Gerät werden durch X509-Zertifikate geschützt. Beide müssen ein Zertifikat der Stammzertifizierungsstelle verwenden, das von derselben Zertifizierungsstelle ausgestellt wurde. Wählen Sie unten die auf Sie zutreffende Registerkarte aus, um die Zertifikatkonfiguration abzuschließen, je nachdem, welcher Typ von Stammzertifizierungsstelle von Ihrer IoT Hub-Instanz genutzt wird.

# <a name="public-ca"></a>[Öffentliche Zertifizierungsstelle](#tab/public-ca)

[!INCLUDE [common-cert-config](../includes/iot-hub-connect-an-iot-edge-device-cert-common.md)]

# <a name="private-ca"></a>[Private Zertifizierungsstelle](#tab/private-ca)

> [!IMPORTANT]
> Wenn Sie eine private Zertifizierungsstelle verwenden, sind zusätzliche Schritte erforderlich, um das Zertifikat der Stammzertifizierungsstelle für IoT Hub auf das IoT Edge-Gerät zu übertragen. Schließen Sie diese Schritte **nur** ab, wenn Sie eine eigene private Zertifizierungsstelle für die Zertifikatgenerierung verwenden. Wenn Ihr Zertifikat der Stammzertifizierungsstelle für IoT Hub von einer öffentlichen Zertifizierungsstelle ausgestellt wurde, wählen Sie die erste Registerkarte *Öffentliche Zertifizierungsstelle* aus. 

### <a name="export-the-root-ca-certificate-from-your-iot-hub"></a>Exportieren Sie das Zertifikat der Stammzertifizierungsstelle aus Ihrer IoT Hub-Instanz.

Exportieren Sie mithilfe eines Computers mit Zugriff auf Ihre Azure Stack Hub-Instanz das Zertifikat der Stammzertifizierungsstelle für IoT Hub im PEM-Format. Im folgenden Beispiel sehen Sie, wie Sie das Zertifikat mithilfe eines [Microsoft Edge](https://www.microsoft.com/edge)- oder [Google Chrome](https://www.google.com/chrome/index.html)-Browsers exportieren können: 

   1. Verwenden Sie auf der Seite **Übersicht** Ihrer IoT Hub-Instanz die Schaltfläche **Kopieren** rechts neben der Eigenschaft **Hostname**, um den IoT Hub-Hostnamen in die Zwischenablage zu kopieren:  

      [![IoT Hub-Hostname](media\iot-hub-connect-an-iot-edge-device\copy-iot-hub-host-name.png)](media\iot-hub-connect-an-iot-edge-device\copy-iot-hub-host-name.png#lightbox)
   1. Öffnen Sie eine neue Registerkarte in einem Microsoft Edge- oder Google Chrome-Browser, geben Sie `https://` ein, fügen Sie den IoT Hub-Hostnamen ein, den Sie im vorherigen Schritt kopiert haben, und drücken Sie die **EINGABETASTE**. 

   1. Nachdem eine Fehlermeldung zurückgegeben wurde, klicken Sie auf das Symbol eines Schlosses links neben der Adressleiste, und klicken Sie dann auf **Zertifikat**.
      [![Sichere Verbindung mit Zertifikat](media\iot-hub-connect-an-iot-edge-device\iot-hub-root-ca-connection.png)](media\iot-hub-connect-an-iot-edge-device\iot-hub-root-ca-connection.png#lightbox)

   1. Wenn das SSL-/TLS-Zertifikat angezeigt wird, klicken Sie auf die Registerkarte **Zertifizierungspfad**. Wählen Sie dann das oberste Zertifikat unter dem Pfad aus, und klicken Sie auf die Schaltfläche **Zertifikat anzeigen**.  
      [![Sichere Verbindung mit SSL-Zertifikat](media\iot-hub-connect-an-iot-edge-device\iot-hub-root-ca-certificate-ssl.png)](media\iot-hub-connect-an-iot-edge-device\iot-hub-root-ca-certificate-ssl.png#lightbox) 

   1. Wenn das Zertifikat der Stammzertifizierungsstelle angezeigt wird, klicken Sie auf die Registerkarte **Details** und dann auf die Schaltfläche **In Datei kopieren…** .
      [![Sichere Verbindung mit Zertifikat der Stammzertifizierungsstelle](media\iot-hub-connect-an-iot-edge-device\iot-hub-root-ca-certificate-ssl-root-ca.png)](media\iot-hub-connect-an-iot-edge-device\iot-hub-root-ca-certificate-ssl-root-ca.png#lightbox) 

   1. Wenn der **Zertifikatexport-Assistent** angezeigt wird, exportieren Sie das Zertifikat in eine Datei im **Base64-codierten X.509-Format**, z. B. **root.cer**. Sie verwenden diese Datei im nächsten Abschnitt. Exportieren Sie sie also an einen Speicherort, auf den von der Linux-VM zugegriffen werden kann bzw. an dem Kopien eingefügt werden können.

   1. Nachdem der Export erfolgreich abgeschlossen wurde, können Sie die Browserregisterkarte schließen.

### <a name="install-the-iot-hub-root-ca-certificate-on-the-iot-edge-device"></a>Installieren des Zertifikats der Stammzertifizierungsstelle für IoT Hub auf dem IoT Edge-Gerät

Installieren Sie nun das im vorherigen Abschnitt exportierte Zertifikat der Stammzertifizierungsstelle für IoT Hub in den Vertrauensspeicher des IoT Edge-Geräts. 

1. Übertragen Sie nun die im vorherigen Abschnitt exportierte Datei der Stammzertifizierungsstelle für IoT Hub auf das IoT Edge-Gerät. Da eine Linux-VM als IoT Edge-Gerät verwendet wird, müssen Sie die Datei auf die Linux-VM kopieren. Je nach Umgebung können Sie entweder PuTTY mit dem PSCP-Befehl oder das WinSCP-Programm zum Kopieren der Datei auf die Linux-VM verwenden.
2. Führen Sie das folgende Skript in Ihrer PuTTY-Sitzung auf der Linux-VM aus, wo Sie die Datei der Stammzertifizierungsstelle für IoT Hub gespeichert haben. Das Skript überprüft, ob die TLS-Verbindung erfolgreich hergestellt wurde, und es installiert die Stammzertifizierungsstelle im Vertrauensspeicher des IoT Edge-Geräts:

   ```bash
   # Verify connection failed first
   # Expected response: Verify return code: 2 (unable to get issuer certificate)
   openssl s_client -connect <IOT-HUB-HOSTNAME>:443
   
   # Verify connection succeeded when root CA provided
   # Expected response: Verify return code: 0 (ok)
   openssl s_client -connect <IOT-HUB-HOSTNAME>:443 -CAfile <IOT-HUB-ROOT-CA>
   
   # Install root CA in the trusted store on Edge device
   sudo cp <IOT-HUB-ROOT-CA> /usr/local/share/ca-certificates/
   sudo update-ca-certificates
   
   # Verify connection succeeded even when no root CA provided
   # Expected response: Verify return code: 0 (ok)
   openssl s_client -connect <IOT-HUB-HOSTNAME>:443
   ```

[!INCLUDE [common-cert-config](../includes/iot-hub-connect-an-iot-edge-device-cert-common.md)]

### <a name="append-the-iot-hub-root-ca-to-the-device-root-ca"></a>Anfügen der Stammzertifizierungsstelle für IoT Hub an die Stammzertifizierungsstelle des Geräts

Fügen Sie nun die exportierte und auf das IoT Edge-Gerät kopierte Stammzertifizierungsstelle für IoT Hub in die Stammzertifizierungsstelle des Geräts ein, die Sie zuvor erstellt haben:

```bash
# Navigate to home directory
cd /home/<USER-ACCOUNT>

# Append IoT Hub root CA to the device root CA
cat <IOT-HUB-ROOT-CA> >> certs/azure-iot-test-only.root.ca.cert.pem
```
-----

## <a name="configure-iot-edge-device-software-and-services"></a>Konfigurieren der Software und Dienste für das IoT Edge-Gerät

In diesem Abschnitt schließen Sie die IoT Hub- und VM-Konfiguration ab, die für das virtuelle IoT Edge-Gerät erforderlich ist.

### <a name="install-iot-edge-and-container-runtimes-on-the-vm"></a>Installieren der IoT Edge-Runtime und von Containerruntimes auf der VM

1. Kehren Sie zur PuTTY-Sitzung auf der VM zurück, und führen Sie das folgende Skript aus, um den Schlüssel- und Softwarerepositoryfeed von Microsoft zu registrieren:

   ```bash
   # Navigate to the home directory
   cd /home/<USER-ACCOUNT>

   # Install the repository configuration. 
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   
   # Copy the generated list.
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   
   # Install Microsoft GPG public key.
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

2. Installieren Sie die Containerruntime:

   ```bash
   sudo apt-get update
   sudo apt-get install moby-engine moby-cli
   ```

3. Installieren Sie den Sicherheitsdaemon für Azure IoT Edge:

   ```bash
   sudo apt-get update
   sudo apt-get install iotedge
   ```

> [!NOTE]
> Sie können die Meldung **IMPORTANT: Please update the configuration file** (WICHTIG: Aktualisieren Sie die Konfigurationsdatei.) vorerst ignorieren, da die Konfigurationsdatei in einem späteren Abschnitt aktualisiert wird.

### <a name="create-an-iot-edge-device-in-iot-hub"></a>Erstellen eines IoT Edge-Geräts in IoT Hub

1. Kehren Sie zum [Azure Stack Hub-Benutzerportal](../user/azure-stack-use-portal.md) zurück, und navigieren Sie zum **IoT Hub**-Dienst.

2. Wählen Sie Ihre IoT Hub-Ressource aus, wählen Sie dann die **IoT Edge**-Seite aus, und klicken Sie dann auf **IoT Edge-Gerät hinzufügen**.

   [![IoT Hub-Ressource](media\iot-hub-connect-an-iot-edge-device\add-an-iot-edge-device.png)](media\iot-hub-connect-an-iot-edge-device\add-an-iot-edge-device.png#lightbox)

3. Geben Sie auf der Seite **Gerät erstellen** die **Geräte-ID** ein, z. B. edged1.
4. Wenn Sie fertig sind, wählen Sie **Speichern** aus.

   [![IoT Edge: Gerät erstellen](media\iot-hub-connect-an-iot-edge-device\create-iot-edge-device.png)](media\iot-hub-connect-an-iot-edge-device\create-iot-edge-device.png#lightbox)

5. Wenn im Portal die **IoT Edge**-Seite angezeigt wird, wählen Sie Ihr neues Gerät aus der Geräteliste aus.

   [![IoT Edge: Geräte anzeigen](media\iot-hub-connect-an-iot-edge-device\view-iot-edge-devices.png)](media\iot-hub-connect-an-iot-edge-device\view-iot-edge-devices.png#lightbox)

6. Verwenden Sie auf der Seite mit Gerätedetails die Schaltfläche **Kopieren** rechts neben **Primäre Verbindungszeichenfolge**, um die Zeichenfolge in die Zwischenablage zu kopieren. Sie benötigen die Verbindungszeichenfolge im nächsten Abschnitt.

   [![IoT Edge: Gerätedetails](media\iot-hub-connect-an-iot-edge-device\iot-edge-device-details.png)](media\iot-hub-connect-an-iot-edge-device\iot-edge-device-details.png#lightbox)

### <a name="configure-the-virtual-iot-edge-device-on-the-vm"></a>Konfigurieren des virtuellen IoT Edge-Geräts auf der VM

1. Kehren Sie zur PuTTY-Sitzung auf der VM zurück. Öffnen Sie mithilfe von Bash die Konfigurationsdatei in Nano auf dem virtuellen IoT Edge-Gerät:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

2. Suchen Sie nach der `provisioning`-Eigenschaft unterhalb des Kommentars **# Manual provisioning configuration using a connection string** (# Manuelle Bereitstellungskonfiguration mithilfe einer Verbindungszeichenfolge) im Abschnitt **Provisioning mode and settings** (Bereitstellungsmodus und Einstellungen). Aktualisieren Sie die Verbindungszeichenfolge für das IoT Edge-Gerät, indem Sie den `<ADD DEVICE CONNECTION STRING HERE>`-Platzhalter durch die Verbindungszeichenfolge ersetzen, die Sie im vorherigen Abschnitt in die Zwischenablage kopiert haben:

   > [!NOTE]
   > Wenn Sie Inhalte aus der Zwischenablage in Nano einfügen möchten, halten Sie die **UMSCHALTTASTE** gedrückt, und drücken Sie die rechte Maustaste. Alternativ können Sie die **UMSCHALTTASTE** und die Taste **EINFÜGEN** gleichzeitig drücken. Wenn der Einfügevorgang Ihren Cursor am rechten Ende der Verbindungszeichenfolge platziert, drücken Sie die Taste **Pos1**, um an den Anfang der Verbindungszeichenfolge zu springen.

   ```yaml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
   ```

3. Suchen Sie nach der `certificates`-Eigenschaft im Abschnitt **Certificate settings** (Zertifikateinstellungen), und heben Sie die Auskommentierung auf. Heben Sie die Auskommentierung auf, und ersetzen Sie die Datei-URIs durch die Pfade zu den drei Zertifikaten, die Sie zuvor erstellt haben. Im Folgenden sehen Sie ein Beispiel:

   ```yaml
   certificates:
     device_ca_cert: "<DATA-DIR>/certs/iot-edge-device-ca-<DEVICE-CA-CERT-NAME>-full-chain.cert.pem"
     device_ca_pk: "<DATA-DIR>/private/iot-edge-device-ca-<DEVICE-CA-CERT-NAME>.key.pem"
     trusted_ca_certs: "<DATA-DIR>/certs/azure-iot-test-only.root.ca.cert.pem"
   ```

4. Wenn Sie diese Schritte abgeschlossen haben, sollten die Eigenschaften `provisioning` und `certificates` ähnlich wie die folgende Antwort aussehen:

   [![Nano-Editor: Bereitstellen der Eigenschaft](media\iot-hub-connect-an-iot-edge-device\nano-edit-config-yml-connection-string.png)](media\iot-hub-connect-an-iot-edge-device\nano-edit-config-yml-connection-string.png#lightbox)

   [![Nano-Editor: Eigenschaft „certificates“](media\iot-hub-connect-an-iot-edge-device\nano-edit-config-yml-certificates.png)](media\iot-hub-connect-an-iot-edge-device\nano-edit-config-yml-certificates.png#lightbox)


5. Speichern Sie die Datei, und schließen Sie sie. Drücken Sie dafür **STRG** + **X** und dann **Y** und anschließend die **EINGABETASTE**.

6. Kehren Sie zu Bash zurück, und starten Sie den Daemon neu:
   
   ```bash
   sudo systemctl restart iotedge
   ```

## <a name="verify-a-successful-installation"></a>Bestätigen einer erfolgreichen Installation

1. Überprüfen Sie den Status des IoT Edge-Daemons:

   ```bash
   systemctl status iotedge
   ```

2. Der IoT Edge-Dienst sollte ausgeführt werden und als „active“ (aktiv) angezeigt werden. Die Antwort sollte der folgenden ähneln. Ist dies der Fall, können Sie mit Schritt 4 fortfahren.

   [![Erfolgreich ausgeführter IoT Edge-Dienst](media\iot-hub-connect-an-iot-edge-device\iot-edge-service-running.png)](media\iot-hub-connect-an-iot-edge-device\iot-edge-service-running.png#lightbox)

3. Wenn für den IoT Edge-Dienst ein Fehler auftritt, gilt Folgendes:
   - Sie sehen eine Antwort ähnlich dem folgenden Beispiel, in der „failed“ (Fehler) angezeigt wird: [![Fehler für den IoT Edge-Dienst](media\iot-hub-connect-an-iot-edge-device\iot-edge-service-failed.png)](media\iot-hub-connect-an-iot-edge-device\iot-edge-service-failed.png#lightbox)   
   - Sie haben die folgenden Möglichkeiten für eine Problembehandlung:
     - Sehen Sie sich die Daemonprotokolle an:

         ```bash
         journalctl -u iotedge --no-pager --no-full
         ```
     - Führen Sie das Tool zur Problembehandlung aus, um nach den häufigsten Konfigurations-und Netzwerkfehlern zu suchen. In diesem Beispiel wurde eine nicht wohlgeformte `/etc/iotedge/config.yaml`-Datei entdeckt:

         ```bash
         sudo iotedge check
         ```

         [![Überprüfung des IoT Edge-Diensts](media\iot-hub-connect-an-iot-edge-device\iot-edge-service-check.png)](media\iot-hub-connect-an-iot-edge-device\iot-edge-service-check.png#lightbox)

         > [!NOTE]
         > Das `$edgeHub`-Systemmodul ist optional, und es wird erst auf dem Gerät bereitgestellt, wenn Sie Ihr erstes IoT Edge-Modul bereitstellen. Deshalb kann `iotedge check` während Konnektivitätsprüfungen für einen Host einen Fehler zurückgeben, der darauf hinweist, dass `$edgeHub` keine Bindung zu Ports herstellen kann. Dieser Fehler kann ignoriert werden, wenn `$edgeHub` in Ihrer Bereitstellung nicht erforderlich ist.           

   - Wenn Sie der Ansicht sind, dass die YML-Datei nicht wie im obigen Beispiel wohlgeformt ist, führen Sie die folgenden Schritte durch:
      - Wiederholen Sie die Schritte unter [Konfigurieren des virtuellen IoT Edge-Geräts auf der VM](#configure-the-virtual-iot-edge-device-on-the-vm), um die Fehler in der YML-Datei zu beheben.
      - Wiederholen Sie die Schritte unter [Bestätigen einer erfolgreichen Installation](#verify-a-successful-installation).

4. Sobald Sie bestätigen konnten, dass der IoT Edge-Dienst erfolgreich gestartet wurden, listen Sie die ausgeführten Module auf. Der Dienst `edgeAgent` sollte mit einem `running`-Status angezeigt werden:

   ```bash
   sudo iotedge list
   ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcen nicht mehr verwenden, kehren Sie zum [Azure Stack Hub-Benutzerportal](../user/azure-stack-use-portal.md) zurück, und löschen Sie die VM und die IoT Hub-Ressourcen, die Sie zuvor erstellt haben.

## <a name="next-steps"></a>Nächste Schritte

Bei den folgenden Artikeln handelt es sich um ergänzende Ressourcen:

- [Installieren der Azure IoT Edge-Runtime auf Debian-basierten Linux-Systemen](/azure/iot-edge/how-to-install-iot-edge-linux)
- [Konfigurieren eines IoT Edge-Geräts als transparentes Gateway](/azure/iot-edge/how-to-create-transparent-gateway)
- [Erstellen von Demozertifikaten zum Testen der Features von IoT Edge-Geräten](/azure/iot-edge/how-to-create-test-certificates)