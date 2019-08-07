---
title: Konfigurieren der automatischen Azure Stack-Protokollsammlung | Microsoft-Dokumentation
description: Konfigurieren der automatischen Protokollsammlung in Azure Stack – Hilfe und Support.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 07/25/2019
ms.openlocfilehash: efab23f12086fee2e4f5c14a70f95717ac9669b9
ms.sourcegitcommit: b752f4e6733d9ebe56dbd171a14528dcb9a693fd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68522062"
---
# <a name="configure-automatic-azure-stack-diagnostic-log-collection"></a>Konfigurieren der automatischen Azure Stack-Diagnoseprotokollsammlung

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

Es wird empfohlen, die Funktion zur automatischen Sammlung von Diagnoseprotokollen zu konfigurieren, um die Protokollsammlung und den Kundensupport zu optimieren. Wenn die Integritätsbedingungen des Systems untersucht werden müssen, können die Protokolle automatisch zur Analyse durch die Microsoft Customer Support Services (CSS) hochgeladen werden. 

## <a name="create-an-azure-blob-container-sas-url"></a>Erstellen einer SAS-URL für Azure-Blobcontainer 

Bevor Sie die automatische Protokollsammlung konfigurieren können, müssen Sie eine SAS (Shared Access Signature) für einen Blobcontainer abrufen. Mit einer SAS können Sie Zugriff auf Ressourcen unter Ihrem Speicherkonto erteilen, ohne dafür Kontoschlüssel weitergeben zu müssen. Sie können Azure Stack-Protokolldateien in einem Blobcontainer in Azure speichern und dann die SAS-URL übergeben, über die CSS die Protokolle sammeln kann. 

### <a name="prerequisites"></a>Voraussetzungen

Sie können einen neuen oder vorhandenen Blobcontainer in Azure verwenden. Um einen Blobcontainer in Azure zu erstellen, müssen Sie mindestens über die [Rolle „Mitwirkender an Speicher-Blobs“](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) oder die [spezifische Berechtigung](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations) verfügen. Globale Administratoren verfügen ebenfalls über die erforderliche Berechtigung. 

Bewährte Methoden zum Wählen von Parametern für das Speicherkonto für die automatische Protokollsammlung finden Sie unter [Best practices for automatic Azure Stack log collection](azure-stack-best-practices-automatic-diagnostic-log-collection.md) (Bewährte Methoden für die automatische Azure Stack-Protokollsammlung). Weitere Informationen zu Typen von Speicherkonten finden Sie unter [Azure Storage-Konto – Übersicht](https://docs.microsoft.com/azure/storage/common/storage-account-overview)

### <a name="create-a-blob-storage-account"></a>Erstellen eines Blob Storage-Kontos
 
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Klicken Sie auf **Speicherkonten** > **Hinzufügen**. 
1. Erstellen Sie einen Blobcontainer mit diesen Einstellungen:
   - **Abonnement**: Wählen Sie Ihr Azure-Abonnement aus.
   - **Ressourcengruppe**: Geben Sie eine Ressourcengruppe an.
   - **Speicherkontoname**: Geben Sie einen eindeutigen Speicherkontonamen an.
   - **Standort**: Wählen Sie in Übereinstimmung mit Ihrer Unternehmensrichtlinie ein Rechenzentrum aus.
   - **Leistung**: Wählen Sie „Standard“ aus.
   - **Kontotyp** Wählen Sie „StorageV2 (universell v2)“ aus. 
   - **Replikation**: Wählen Sie „Lokal redundanter Speicher (LRS)“ aus.
   - **Zugriffsebene**: Wählen Sie „Kalt“ aus.

   ![Screenshot mit der Darstellung der Blobcontainer-Eigenschaften](media/azure-stack-automatic-log-collection/azure-stack-log-collection-create-storage-account.png)

1. Klicken Sie auf **Überprüfen + erstellen** und dann auf **Erstellen**.  

### <a name="create-a-blob-container"></a>Erstellen eines Blobcontainers 

1. Klicken Sie nach der erfolgreichen Bereitstellung auf **Zu Ressource wechseln**. Sie können das Speicherkonto auch für den bequemen Zugriff am Dashboard anheften. 
1. Klicken Sie auf **Storage-Explorer (Vorschau)** , klicken Sie mit der rechten Maustaste auf **Blobcontainer**, und klicken Sie auf **Blobcontainer erstellen**. 
1. Geben Sie einen Namen für den neuen Container ein, und klicken Sie auf **OK**.

## <a name="create-a-sas-url"></a>Erstellen einer SAS-URL

1. Klicken Sie mit der rechten Maustaste auf den Container, und klicken Sie auf **Shared Access Signature abrufen**. 
   
   ![Screenshot, der das Abrufen der Shared Access Signature eines Blobcontainers darstellt](media/azure-stack-automatic-log-collection/get-sas.png)

1. Wählen Sie diese Eigenschaften aus:
   - Startzeit: Optional können Sie die Startzeit zurück verlegen. 
   - Ablaufzeit: Zwei Jahre
   - Zeitzone: UTC
   - Berechtigungen: Lesen, schreiben und auflisten

   ![Screenshot mit Darstellung der Shared Access Signature-Eigenschaften](media/azure-stack-automatic-log-collection/sas-properties.png) 

1. Klicken Sie auf **Create**.  

Kopieren Sie die URL, und geben Sie sie beim [Konfigurieren der automatischen Protokollsammlung](azure-stack-configure-automatic-diagnostic-log-collection.md) ein. Weitere Informationen zu SAS-URLs finden Sie unter [Verwenden von Shared Access Signatures (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1). 


## <a name="steps-to-configure-automatic-log-collection"></a>Schritte zum Konfigurieren der automatischen Protokollsammlung

Führen Sie diese Schritte aus, um der Benutzeroberfläche der Protokollsammlung die SAS-URL hinzuzufügen: 

1. Melden Sie sich beim Azure Stack-Administratorportal an.
1. Öffnen Sie **Hilfe und Support – Übersicht**.
1. Klicken Sie auf **Einstellungen der automatischen Sammlung**.

   ![Screenshot, der darstellt, wo die Protokollsammlung in „Hilfe und Support“ aktiviert wird](media/azure-stack-automatic-log-collection/azure-stack-automatic-log-collection.png)

1. Legen Sie die automatische Protokollsammlung auf **Aktiviert** fest.
1. Geben Sie die SAS-URL (Shared Access Signature) des Speicherkonto-Blobcontainers ein.

   ![Screenshot mit Darstellung der Blob-SAS-URL](media/azure-stack-automatic-log-collection/azure-stack-enable-automatic-log-collection.png)

>[!NOTE]
>Die automatische Protokollsammlung kann jederzeit deaktiviert und wieder aktiviert werden. Die SAS-URL-Konfiguration ändert sich dadurch nicht. Wenn die automatische Protokollsammlung wieder aktiviert wird, durchläuft die zuvor eingegebene SAS-URL die gleichen Überprüfungen, und eine abgelaufene SAS-URL wird abgelehnt. 


## <a name="view-log-collection"></a>Anzeigen der Protokollsammlung

Der Verlauf der von Azure Stack gesammelten Protokolle wird auf der Seite **Protokollsammlung** in Hilfe und Support mit den folgenden Daten und Uhrzeiten angezeigt:

- **Sammlungszeit**: Zeitpunkt, zu dem der Sammlungsvorgang begonnen hat 
- **Startdatum**: Beginn des Zeitraums, für den Sie die Sammlung ausführen möchten.
- **Enddatum**: Ende des Zeitraums

![Screenshot mit Darstellung von Protokollsammlungen](media/azure-stack-automatic-log-collection/azure-stack-log-collection.png)

Wenn die Sammlung von Diagnoseprotokollen misslingt, überprüfen Sie, ob die SAS-URL gültig ist. Wenn der Fehler weiterhin auftritt oder Sie mehrere Fehler sehen, wenden Sie sich zur Unterstützung an Microsoft CSS. 

Operatoren können darüber hinaus das Speicherkonto für automatisch gesammelte Protokolle überprüfen. Dieser Screenshot zeigt z.B. Protokollsammlungen mithilfe der Storage-Explorer-Vorschau aus dem Azure-Portal:

![Screenshot mit Darstellung von Protokollsammlungen](media/azure-stack-automatic-log-collection/check-storage-account.png)


## <a name="see-also"></a>Weitere Informationen

[Azure Stack-Protokoll und Behandlung von Kundendaten](https://docs.microsoft.com/azure-stack/operator/azure-stack-data-collection)

[Verwenden von Shared Access Signatures (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)

[Bewährte Methoden für die automatische Azure Stack-Protokollsammlung](azure-stack-best-practices-automatic-diagnostic-log-collection.md)





