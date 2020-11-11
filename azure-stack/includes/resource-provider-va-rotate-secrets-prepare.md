---
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: include
ms.date: 10/10/2020
ms.reviewer: bryanla
ms.lastreviewed: 10/20/2020
ms.openlocfilehash: 900d7ac882a37e229bec6dc916653cf55992cccb
ms.sourcegitcommit: 81e2d627c9dc4cc365deb4a0e0674b5ab3a7efbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/21/2020
ms.locfileid: "93050296"
---
Als Nächstes erstellen oder erneuern Sie Ihr TLS-Zertifikat zum Schutz der Ressourcenanbieterendpunkte, die einen Mehrwert schaffen:

1. Führen Sie die Schritte unter [Generieren von Zertifikatsignieranforderungen für die Zertifikaterneuerung](../operator/azure-stack-get-pki-certs.md#generate-certificate-signing-requests-for-certificate-renewal) für Ihren Ressourcenanbieter aus. Hier verwenden Sie das Tool zur Bereitschaftsüberprüfung von Azure Stack Hub, um die Zertifikatsignieranforderung zu erstellen. Stellen Sie sicher, dass Sie das richtige Cmdlet für Ihren Ressourcenanbieter im Schritt „Generieren von Zertifikatanforderungen für andere Azure Stack Hub-Dienste“ ausführen. Beispielsweise wird `New-AzsHubEventHubsCertificateSigningRequest` für Event Hubs verwendet. Wenn Sie fertig sind, übermitteln Sie die generierte REQ-Datei an Ihre Zertifizierungsstelle für das neue Zertifikat.

2. Sobald Sie Ihre Zertifikatdatei von der Zertifizierungsstelle erhalten haben, führen Sie die Schritte unter [Vorbereiten von Zertifikaten für die Bereitstellung oder Rotation](../operator/azure-stack-prepare-pki-certs.md). Verwenden Sie das Tool zur Bereitschaftsüberprüfung nochmal, um die von der Zertifizierungsstelle zurückgegebene Datei zu verarbeiten.

3. Führen Sie schließlich die Schritte unter [Überprüfen von Azure Stack Hub-PKI-Zertifikaten](../operator/azure-stack-validate-pki-certs.md) aus. Sie verwenden das Tool zur Bereitschaftsüberprüfung ein weiteres Mal, um Validierungstests für Ihr neues Zertifikat durchzuführen.


