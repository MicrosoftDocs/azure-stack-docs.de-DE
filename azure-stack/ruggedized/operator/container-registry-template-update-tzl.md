---
title: Aktualisieren der Containerregistrierung in Azure Stack Hub | Microsoft-Dokumentation
titleSuffix: Azure Stack
description: Hier erfahren Sie mehr über das Aktualisieren der Containerregistrierung in Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/8/2020
ms.author: mabrigg
ms.reviewer: chasat
ms.lastreviewed: 12/17/2019
ms.openlocfilehash: 1239e12235debaa8ab6a3037c34ea27e11da0ce2
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96941166"
---
# <a name="update-the-container-registry-in-azure-stack-hub"></a>Aktualisieren der Containerregistrierung in Azure Stack Hub

Azure Stack Hub-Benutzer können ihre Containerregistrierungsbereitstellung auf eine aktuelle SKU des AKS-Basisimage mithilfe der untenstehenden Anleitung aktualisieren. Die VM und der Dienst der Containerregistrierungsvorlage sind zustandslos, da der gesamte Zustand und die Containerimages in Blobspeicher gespeichert werden. Eine Aktualisierung ist genauso einfach wie das Bereitstellen der Containerregistrierungsvorlage mit einer aktuellen Version der VHD des AKS-Basisimage mit anschließender DNS-Umleitung zur neuen VM. Die Aktion der Aktualisierung des DNS-Werts für die alte und die neue VM der Containerregistrierungsvorlage führt zu einem kleinen Zeitfenster, während dem die Registrierungskonnektivität zeitweilig unterbrochen ist, während die Werte weitergegeben werden.

## <a name="prerequisites"></a>Voraussetzungen

### <a name="operator"></a>Operator

1.  Veröffentlichen Sie das aktuelle AKS-Basisimage im Azure Stack-Marketplace. Das AKS-Basisimage wird monatlich aktualisiert.

> ![Containerregistrierungsvorlage](./media/container-registry-template-updating-tzl/image1.png)

### <a name="user"></a>Benutzer

1.  Überprüfen Sie die SKU des AKS-Basisimage, die für die Bereitstellung der Containerregistrierungsvorlage verwendet wurde, indem Sie sich die Bereitstellungseinträge in der Ressourcengruppe ansehen und auf **Eingaben** klicken.

    ![Containerregistrierungsvorlage](./media/container-registry-template-updating-tzl/image2.png)

2.  Überprüfen Sie, ob es neuere SKUs des AKS-Basisimage gibt. Verwenden Sie dazu die **Get-VMImageSku**-Funktion. Dafür ist `Import-Module .\pre-reqs.ps1` aus den Skripten der Containerregistrierungsvorlage erforderlich.

    ```powershell  
    PS C:\azurestack-galler-master\registry\Scripts> Get-VMImageSku -Location Shanghai
    
    Skus                  
    ----                  
    aks-ubuntu-1604-201909
    aks-ubuntu-1604-201910 
    ```

## <a name="parameters-required"></a>Erforderliche Parameter

| Parameter | Details |
| --- | --- |
| Username | Geben Sie den Benutzername für die Anmeldung bei der VM an. |
| Öffentlicher SSH-Schlüssel | Geben Sie den öffentlichen SSH-Schlüssel an, der für die Authentifizierung mit der VM mithilfe des SSH-Protokolls genutzt wird. |
| Size | Wählen Sie die Größe der bereitzustellenden VM aus. |
| Öffentliche IP-Adresse | Geben Sie den Namen und den Typ der IP-Adresse (dynamisch oder statisch) für diese VM an. |
| Domänennamenbezeichnung | Geben Sie das DNS-Präfix für Ihre Registrierung an. Der gesamte FQDN sollte mit dem CN-Wert des für die Registrierung erstellten PFX-Zertifikats übereinstimmen. |
| Replikate | Geben Sie zu Beginn die Anzahl der Containerreplikate an. |
| Image-SKU | Geben Sie die Image-SKU an, die für die Bereitstellung verwendet werden soll. Die für das AKS-Basisimage verfügbaren SKUs werden vom **Get-VMImageSku**-Cmdlet in PowerShell aufgeführt. |
| Client-ID des Dienstprinzipals | Geben Sie die App-ID für den Dienstprinzipal (SPN) wie in der vorherigen Bereitstellung verwendet an. |
| Service Principal Password/Confirm Password (Kennwort für Dienstprinzipal/Kennwort bestätigen) | Geben Sie das SPN-App-ID-Geheimnis wie in der vorherigen Bereitstellung verwendet an. |
| Existing extended storage account resource ID (Vorhandene Ressourcen-ID des erweiterten Speicherkontos) | Geben Sie die Ressourcen-ID des Speicherkontos wie in der vorherigen Bereitstellung verwendet an. |
| Existing backend blob container (Vorhandener Back-End-Blobcontainer) | Geben Sie den Blobcontainernamen wie in der vorherigen Bereitstellung verwendet an. |
| PFX Certificate Key Vault Resource ID (Ressourcen-ID des PFX-Zertifikats in Key Vault) | Geben Sie die Ressourcen-ID von Microsoft Azure Key Vault wie in der vorherigen Bereitstellung verwendet an. |
| PFX Certificate Key Vault Secret URL (URL für das Key Vault-Geheimnis des PFX-Zertifikats) | Geben Sie die Zertifikat-URL wie in der vorherigen Bereitstellung verwendet an. |
| PFX Certificate Thumbprint (PFX-Zertifikatfingerabdruck) | Geben Sie den Zertifikatfingerabdruck wie in der vorherigen Bereitstellung verwendet an. |

## <a name="installation"></a>Installation

1.  Installieren Sie eine neue Instanz der Containerregistrierungsvorlage in einer neuen Ressourcengruppe.

    ![Containerregistrierungsvorlage](./media/container-registry-template-updating-tzl/image3.png)

2.  Geben Sie die aktuelle SKU-Ausgabe aus dem `Get-VMImage`-Skript an, und verwenden Sie einen eindeutigen **dnsname**-Parameter der ursprünglichen Installation für die VM-Konfiguration, und verwenden Sie denselben Dienstprinzipal und dasselbe Geheimnis wie bei der ursprünglichen Installation.

    ![Containerregistrierungsvorlage](./media/container-registry-template-updating-tzl/image4.png)

3.  Verwenden Sie dieselben Speicher- und Key Vault-Parameter wie bei der ursprünglichen Installation für die Speicher- und Key Vault-Konfiguration.

    ![Containerregistrierungsvorlage](./media/container-registry-template-updating-tzl/image5.png)

1.  Sobald die neue Containerregistrierungsvorlage bereitgestellt wurde, navigieren Sie zur anfänglichen Ressourcengruppe und wählen die Ressource der öffentlichen IP-Adresse aus.

    ![Containerregistrierungsvorlage](./media/container-registry-template-updating-tzl/image6.png)

1.  Navigieren Sie in der Ressource der öffentlichen IP-Adresse zu „Konfiguration“, und ändern Sie die DNS-Namensbezeichnung, damit diese für die neu bereitgestellte Ressource verwendet werden kann. Beachten Sie, dass Fehler für Aufrufe der Containerregistrierung auftreten, sobald Sie die DNS-Namensbezeichnung bearbeitet und auf **Speichern** geklickt haben.

    ![Containerregistrierungsvorlage](./media/container-registry-template-updating-tzl/image7.png)
    
    ![Containerregistrierungsvorlage](./media/container-registry-template-updating-tzl/image8.png)

2.  Navigieren Sie zur neuen Ressourcengruppe, die zum Bereitstellen der neuen Instanz der Containerregistrierungsvorlage verwendet wurde, wählen Sie die Ressource der öffentlichen IP-Adresse und die Konfiguration aus, und aktualisieren Sie die Bezeichnung des DNS-Namens in den korrekten Namen, der bei der ursprünglichen Bereitstellung genutzt wurde, in diesem Beispiel also `myreg`. Klicken Sie dann auf **Speichern**.

    ![Containerregistrierungsvorlage](./media/container-registry-template-updating-tzl/image9.png)
    
    ![Containerregistrierungsvorlage](./media/container-registry-template-updating-tzl/image10.png)

3.  Während der nächsten dreißig Minuten ist der Zugriff auf die Containerregistrierung zeitweilig unterbrochen, da der DNS-Eintrag weitergegeben wird. Überprüfen Sie die Verbindung, indem Sie sich bei der Docker-Registrierung anmelden und ein Image pullen/pushen.

## <a name="next-steps"></a>Nächste Schritte

[Der Azure Stack-Marketplace – Übersicht](../../operator/azure-stack-marketplace.md)
