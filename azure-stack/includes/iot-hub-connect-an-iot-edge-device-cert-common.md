---
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: include
ms.date: 11/20/2020
ms.reviewer: bryanla
ms.lastreviewed: 11/20/2020
ms.openlocfilehash: 6d4bb587ad28fb26adf60689989d0e1858519cfe
ms.sourcegitcommit: 0765de47f4a73e09192d34739e40c750b6e7abaf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920828"
---
### <a name="create-a-device-root-ca-certificate"></a>Erstellen eines Zertifikats der Gerätestammzertifizierungsstelle

Erstellen Sie das Zertifikat der Gerätestammzertifizierungsstelle und die für Ihr Gerät erforderlichen Schlüsseldateien: 

1. Kehren Sie in der PuTTY-Sitzung zur Bash-Eingabeaufforderung zurück.
2. Navigieren Sie zum Datenverzeichnis, in dem Sie im vorherigen Abschnitt die Skripts zur Zertifikaterstellung abgelegt haben.
3. Führen Sie den folgenden Befehl aus:

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

4. Das Zertifikat der Gerätestammzertifizierungsstelle wird in der folgenden Datei gespeichert: `<DATA-DIR>/certs/azure-iot-test-only.root.ca.cert.pem`.

### <a name="create-the-iot-edge-device-ca-certificate"></a>Erstellen des Zertifikats der Stammzertifizierungsstelle für IoT Edge-Geräte

Für IoT Edge-Geräte in der Produktion wird ein Zertifikat der Stammzertifizierungsstelle benötigt, auf das in der Datei „config.yaml“ verwiesen wird. Das Zertifikat der Stammzertifizierungsstelle ist für das Erstellen von Zertifikaten für Module zuständig, die auf dem Gerät ausgeführt werden. Es ist außerdem für Gatewayszenarios erforderlich, weil das IoT Edge-Gerät damit seine Identität gegenüber nachgeschalteten Geräten nachweist.

So erstellen Sie die Zertifikatdateien der Zertifizierungsstelle eines IoT Edge-Geräts:

1. Kehren Sie in der PuTTY-Sitzung zur Bash-Eingabeaufforderung zurück.
2. Führen Sie das folgende Skript aus, das mehrere Zertifikat- und Schlüsseldateien der Gerätezertifizierungsstelle erstellt: 

   ```bash
   # Navigate to data directory
   cd <DATA-DIR>
   
   # Generate IoT Edge device CA certificate 
   ./certGen.sh create_edge_device_ca_certificate <DEVICE-CA-CERT-NAME>
   ```

3.  Auf die folgenden Zertifikat- und Schlüsselpaardateien wird später in der Datei „config.yaml“ verwiesen:

    `<DATA-DIR>/certs/iot-edge-device-ca-<DEVICE-CA-CERT-NAME>-full-chain.cert.pem`  
    `<DATA-DIR>/private/iot-edge-device-ca-<DEVICE-CA-CERT-NAME>.key.pem`


