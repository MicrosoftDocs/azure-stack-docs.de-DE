---
title: Verwenden von API-Versionsprofilen mit Python in Azure Stack | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie API-Versionsprofile mit Python in Azure Stack verwenden.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
<!-- dev: viananth -->
ms.openlocfilehash: 35ce331c29e89af3a81396a9658cf8a0f29018d3
ms.sourcegitcommit: 58c28c0c4086b4d769e9d8c5a8249a76c0f09e57
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/12/2019
ms.locfileid: "68959431"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>Verwenden von API-Versionsprofilen mit Python in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

## <a name="python-and-api-version-profiles"></a>Python und API-Versionsprofile

Das Python SDK unterstützt API-Versionsprofile für unterschiedliche Cloudplattformen, z.B. Azure Stack und globales Azure. Verwenden Sie API-Profile beim Erstellen von Lösungen für eine Hybrid Cloud. Das Python SDK unterstützt die folgenden API-Profile:

- **Neueste**  
    Dieses Profil ist auf die aktuellen API-Versionen für alle Dienstanbieter auf der Azure Platform ausgerichtet.
- **2019-03-01-hybrid**  
    Dieses Profil ist auf die aktuellen API-Versionen für alle Ressourcenanbieter auf der Azure Stack-Plattform für die Stempelversionen 1904 oder höher ausgerichtet.
- **2018-03-01-hybrid**  
    Dieses Profil ist auf die kompatibelsten API-Versionen für alle Ressourcenanbieter auf der Azure Stack-Plattform ausgerichtet.
- **2017-03-09-profile**  
    Dieses Profil ist auf die API-Versionen mit der höchsten Kompatibilität der von Azure Stack unterstützten Ressourcenanbieter ausgerichtet.

   Weitere Informationen zu API-Profilen und Azure Stack finden Sie unter [Verwalten von API-Versionsprofilen in Azure Stack](azure-stack-version-profiles.md).

## <a name="install-the-azure-python-sdk"></a>Installieren des Azure Python SDK

1. Installieren Sie Git über die [offizielle Website](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
2. Eine Anleitung zum Installieren des Python SDK finden Sie unter [Azure für Python-Entwickler](/python/azure/python-sdk-azure-install?view=azure-python).
3. Erstellen Sie ein Abonnement, wenn keins verfügbar ist, und speichern Sie die Abonnement-ID zur späteren Verwendung. Anweisungen zum Erstellen eines Abonnements finden Sie unter [Erstellen von Abonnements für Angebote in Azure Stack](../operator/azure-stack-subscribe-plan-provision-vm.md).
4. Erstellen Sie einen Dienstprinzipal, und speichern Sie die ID und das Geheimnis. Eine Anleitung zum Erstellen eines Dienstprinzipals für Azure Stack finden Sie unter [Bereitstellen des Anwendungszugriffs auf Azure Stack](../operator/azure-stack-create-service-principals.md).
5. Stellen Sie sicher, dass Ihr Dienstprinzipal über die Rolle „Mitwirkender“ bzw. „Besitzer“ für Ihr Abonnement verfügt. Eine Anleitung zum Zuweisen einer Rolle zum Dienstprinzipal finden Sie unter [Bereitstellen des Anwendungszugriffs auf Azure Stack](../operator/azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Voraussetzungen

Um das Python Azure SDK mit Azure Stack zu verwenden, müssen Sie die folgenden Werte angeben und dann Werte mit Umgebungsvariablen festlegen. Befolgen Sie die Anweisungen in der Tabelle für Ihr Betriebssystem, um die Umgebungsvariablen festzulegen.

| Wert | Umgebungsvariablen | BESCHREIBUNG |
|---------------------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------|
| Mandanten-ID | `AZURE_TENANT_ID` | Der Wert Ihrer [Mandanten-ID](../operator/azure-stack-identity-overview.md) für Azure Stack. |
| Client-ID | `AZURE_CLIENT_ID` | Die Anwendungs-ID des Dienstprinzipals, die beim Erstellen des Dienstprinzipals im vorherigen Abschnitt dieses Artikels gespeichert wurde. |
| Abonnement-ID | `AZURE_SUBSCRIPTION_ID` | Mit der [Abonnement-ID](../operator/azure-stack-plan-offer-quota-overview.md#subscriptions) greifen Sie in Azure Stack auf Angebote zu. |
| Geheimer Clientschlüssel | `AZURE_CLIENT_SECRET` | Das App-Geheimnis des Dienstprinzipals, das bei der Erstellung des Dienstprinzipals gespeichert wurde. |
| Resource Manager-Endpunkt | `ARM_ENDPOINT` | Siehe [Azure Stack-Resource Manager-Endpunkt](azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint). |
| Resource Location | `AZURE_RESOURCE_LOCATION` | Der Ressourcenspeicherort Ihrer Azure Stack-Umgebung.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Einstufen des Zertifizierungsstellen-Stammzertifikats für Azure Stack als vertrauenswürdig

Bei Verwendung des ASDK müssen Sie das Zertifizierungsstellen-Stammzertifikat auf Ihrem Remotecomputer als vertrauenswürdig einstufen. Sie müssen das Zertifizierungsstellen-Stammzertifikat nicht für die integrierten Systeme als vertrauenswürdig einstufen.

#### <a name="windows"></a>Windows

1. Suchen Sie den Zertifikatspeicherort auf Ihrem Computer. Der Speicherort kann je nachdem, wo Sie Python installiert haben, variieren. Öffnen Sie eine Eingabeaufforderung oder eine PowerShell-Eingabeaufforderung mit erhöhten Rechten, und geben Sie den folgenden Befehl ein:

    ```PowerShell  
      python -c "import certifi; print(certifi.where())"
    ```

    Notieren Sie sich den Zertifikatspeicherort. Beispiel: *~/lib/python3.5/site-packages/certifi/cacert.pem*. Der konkrete Pfad hängt vom jeweiligen Betriebssystem und der installierten Python-Version ab.

2. Stufen Sie das Zertifizierungsstellen-Stammzertifikat für Azure Stack als vertrauenswürdig ein, indem Sie es an das vorhandene Python-Zertifikat anfügen.

    ```powershell
    $pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
    $root.Import($pemFile)

    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry    = [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Python35\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

    Write-Host "Python Cert store was updated to allow the Azure Stack CA root certificate"

    ```

> [!NOTE]  
> Wenn Sie wie weiter unten erwähnt virtualenv für die Entwicklung mit dem Python SDK verwenden, muss das obige Zertifikat dem Zertifikatspeicher Ihrer virtuellen Umgebung hinzugefügt werden. Der Pfad kann beispielsweise wie folgt aussehen: „..\mytestenv\Lib\site-packages\certifi\cacert.pem“.



## <a name="python-samples-for-azure-stack"></a>Python-Beispiele für Azure Stack

Folgende Codebeispiele sind u. a. für Azure Stack mit dem Python SDK verfügbar:

- [Manage Resources and Resource Groups](https://azure.microsoft.com/resources/samples/hybrid-resourcemanager-python-manage-resources/) (Verwalten von Ressourcen und Ressourcengruppen)
- [Manage Storage Account](https://azure.microsoft.com/resources/samples/hybrid-storage-python-manage-storage-account/) (Verwalten eines Speicherkontos)
- [Manage Virtual Machines](https://azure.microsoft.com/resources/samples/hybrid-compute-python-manage-vm/) (Verwalten virtueller Computer); (In diesem Beispiel wird „2019-03-01-hybrid“ für die von Azure Stack unterstützten aktuellen API-Versionen verwendet)

## <a name="python-manage-virtual-machine-sample"></a>Python-Beispiel für die Verwaltung von VMs

Verwenden Sie das folgende Codebeispiel, um allgemeine Verwaltungsaufgaben für virtuelle Computer (VMs) in Ihrer Azure Stack-Instanz auszuführen. Das Codebeispiel veranschaulicht folgende Aufgaben:

- Erstellen virtueller Computer:
  - Erstellen eines virtuellen Linux-Computers
  - Erstellen eines virtuellen Windows-Computers
- Aktualisieren eines virtuellen Computers:
  - Erweitern eines Laufwerks
  - Kennzeichnen eines virtuellen Computers
  - Anfügen von Datenträgern für Daten
  - Trennen von Datenträgern
- Betreiben eines virtuellen Computers:
  - Starten eines virtuellen Computers
  - Anhalten eines virtuellen Computers
  - Neustarten eines virtuellen Computers
- Auflisten der virtuellen Computer
- Löschen eines virtuellen Computers

Den Code zum Ausführen dieser Vorgänge finden Sie in der **run_example()** -Funktion im Python-Skript **example.py** im GitHub-Repository [Hybrid-Compute-Python-Manage-VM](https://github.com/Azure-Samples/Hybrid-Compute-Python-Manage-VM).

Jeder Vorgang ist deutlich mit einem Kommentar und einer Druckfunktion gekennzeichnet. Die Reihenfolge der Beispiele entspricht nicht unbedingt der in der obigen Liste.

## <a name="run-the-python-sample"></a>Ausführen des Python-Beispiels

1. [Installieren Sie Python](https://www.python.org/downloads/), falls dies noch nicht erfolgt ist. Dieses Beispiel (und das SDK) ist mit Python 2.7, 3.4, 3.5 und 3.6 kompatibel.

2. Allgemein wird für die Python-Entwicklung die Verwendung einer virtuellen Umgebung empfohlen. Weitere Informationen finden Sie in der [Python-Dokumentation](https://docs.python.org/3/tutorial/venv.html).

3. Installieren und initialisieren Sie die virtuelle Umgebung mit dem „venv“-Modul auf Python 3 (Sie müssen [virtualenv](https://pypi.python.org/pypi/virtualenv) für Python 2.7 installieren):

    ```bash
    python -m venv mytestenv # Might be "python3" or "py -3.6" depending on your Python installation
    cd mytestenv
    source bin/activate      # Linux shell (Bash, ZSH, etc.) only
    ./scripts/activate       # PowerShell only
    ./scripts/activate.bat   # Windows CMD only
    ```

4. Klonen Sie das Repository:

    ```bash
    git clone https://github.com/Azure-Samples/Hybrid-Compute-Python-Manage-VM.git
    ```

5. Installieren Sie die Abhängigkeiten mit „pip“:

    ```bash
    cd Hybrid-Compute-Python-Manage-VM
    pip install -r requirements.txt
    ```

6. Erstellen Sie einen [Dienstprinzipal](../operator/azure-stack-create-service-principals.md) zur Zusammenarbeit mit Azure Stack. Stellen Sie sicher, dass Ihr Dienstprinzipal über die Rolle [„Mitwirkender“ bzw. „Besitzer“](../operator/azure-stack-create-service-principals.md#assign-a-role) für Ihr Abonnement verfügt.

7. Legen Sie die folgenden Variablen fest, und exportieren Sie diese Umgebungsvariablen in Ihre aktuelle Shell:

    ```bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    export AZURE_RESOURCE_LOCATION={your AzureStack Resource location}
    ```

8. Zum Ausführen dieses Beispiels müssen Ubuntu 16.04 LTS- und Windows Server 2012 R2 Datacenter-Images im Azure Stack-Marketplace vorhanden sein. Diese Images können entweder [aus Azure heruntergeladen](../operator/azure-stack-download-azure-marketplace-item.md) oder dem [Platform Image Repository](../operator/azure-stack-add-vm-image.md) hinzugefügt werden.

9. Führen Sie das Beispiel aus:

    ```python
    python example.py
    ```


## <a name="next-steps"></a>Nächste Schritte

- [Azure Python Developer Center](https://azure.microsoft.com/develop/python/)
- [Dokumentation zu virtuellen Azure-Computern](https://azure.microsoft.com/services/virtual-machines/)
- [Lernpfad für Virtual Machines](/learn/paths/deploy-a-website-with-azure-virtual-machines/)

Wenn Sie kein Microsoft Azure-Abonnement haben, können Sie [hier](https://go.microsoft.com/fwlink/?LinkId=330212) ein KOSTENLOSES Testkonto erstellen.
