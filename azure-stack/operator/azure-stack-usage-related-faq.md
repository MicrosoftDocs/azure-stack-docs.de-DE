---
title: Häufig gestellte Fragen zur Nutzungs-API
titleSuffix: Azure Stack Hub
description: Hier finden Sie eine Liste mit häufig gestellten Fragen zur Azure Stack Hub-Nutzung. Der Artikel enthält unter anderem Informationen zu Verbrauchseinheiten, einen Vergleich mit der Nutzungs-API von Azure, eine Gegenüberstellung von Nutzungszeit und gemeldeter Zeit sowie Fehlercodes.
services: azure-stack
documentationcenter: ''
author: sethmanheim
ms.topic: article
ms.date: 12/15/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 02/26/2019
ms.openlocfilehash: a4f9744139199f999475f24a3910e1929b0a8b9b
ms.sourcegitcommit: a53ea4a28e715c80a99fa89e9d364bc4556558de
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97576971"
---
# <a name="frequently-asked-questions-about-azure-stack-hub-usage"></a>Häufig gestellte Fragen zur Nutzung von Azure Stack Hub

In diesem Artikel werden einige häufig gestellte Fragen zur Nutzung von Azure Stack Hub und zur Azure Stack Hub-Nutzungs-API beantwortet.

## <a name="what-meter-ids-can-i-see"></a>Welche Verbrauchseinheits-IDs kann ich sehen?

Der Verbrauch wird für die folgenden Ressourcenanbieter gemeldet:

### <a name="network"></a>Netzwerk
  
**ID der Verbrauchseinheit**: F271A8A388C44D93956A063E1D2FA80B  
**Name der Verbrauchseinheit**: Nutzung von statischen IP-Adressen  
**Einheit:** IP-Adressen  
**Hinweise**: Anzahl von verwendeten IP-Adressen; Wenn Sie die Nutzungs-API täglich aufrufen, gibt die Verbrauchseinheit die IP-Adresse multipliziert mit der Anzahl von Stunden zurück.  
  
**ID der Verbrauchseinheit**: 9E2739BA86744796B465F64674B822BA  
**Name der Verbrauchseinheit**: Nutzung von dynamischen IP-Adressen  
**Einheit:** IP-Adressen  
**Hinweise**: Anzahl von verwendeten IP-Adressen; Wenn Sie die Nutzungs-API täglich aufrufen, gibt die Verbrauchseinheit die IP-Adresse multipliziert mit der Anzahl von Stunden zurück.  
  
### <a name="storage"></a>Storage
  
**ID der Verbrauchseinheit**: B4438D5D-453B-4EE1-B42A-DC72E377F1E4  
**Name der Verbrauchseinheit**: TableCapacity  
**Einheit:** GB\*Stunden  
**Hinweise**: Von Tabellen genutzte Kapazität  
  
**ID der Verbrauchseinheit**: B5C15376-6C94-4FDD-B655-1A69D138ACA3  
**Name der Verbrauchseinheit**: PageBlobCapacity  
**Einheit:** GB\*Stunden  
**Hinweise**: Von Seitenblobs genutzte Kapazität  
  
**ID der Verbrauchseinheit**: B03C6AE7-B080-4BFA-84A3-22C800F315C6  
**Name der Verbrauchseinheit**: QueueCapacity  
**Einheit:** GB\*Stunden  
**Hinweise**: Von Warteschlangen genutzte Kapazität  
  
**ID der Verbrauchseinheit**: 09F8879E-87E9-4305-A572-4B7BE209F857  
**Name der Verbrauchseinheit**: BlockBlobCapacity  
**Einheit:** GB\*Stunden  
**Hinweise**: Von Blockblobs genutzte Kapazität  
  
**ID der Verbrauchseinheit**: B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90  
**Name der Verbrauchseinheit**: TableTransactions  
**Einheit:** Zahl der Anforderungen in 10.000ern  
**Hinweise**: Tabellenspeicherdienst-Anforderungen (in 10.000ern)  
  
**ID der Verbrauchseinheit**: 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D  
**Name der Verbrauchseinheit**: TableDataTransIn  
**Einheit:** Dateneingang in GB  
**Hinweise**: Eingang von Tabellenspeicherdienst-Daten in GB  
  
**ID der Verbrauchseinheit**: 1B8C1DEC-EE42-414B-AA36-6229CF199370  
**Name der Verbrauchseinheit**: TableDataTransOut  
**Einheit:** Ausgehende Daten in GB  
**Hinweise**: Ausgehende Tabellenspeicherdienst-Daten in GB
  
**ID der Verbrauchseinheit**: 43DAF82B-4618-444A-B994-40C23F7CD438  
**Name der Verbrauchseinheit**: BlobTransactions  
**Einheit:** Zahl der Anforderungen in 10.000ern  
**Hinweise**: Blob-Dienstanforderungen (in 10.000ern)  
  
**ID der Verbrauchseinheit**: 9764F92C-E44A-498E-8DC1-AAD66587A810  
**Name der Verbrauchseinheit**: BlobDataTransIn  
**Einheit:** Dateneingang in GB  
**Hinweise**: Eingang von Blob-Dienstdaten in GB  
  
**ID der Verbrauchseinheit**: 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8  
**Name der Verbrauchseinheit**: BlobDataTransOut  
**Einheit:** Ausgehende Daten in GB  
**Hinweise**: Ausgehender Datenverkehr von Blob-Dienstdaten in GB  
  
**ID der Verbrauchseinheit**: EB43DD12-1AA6-4C4B-872C-FAF15A6785EA  
**Name der Verbrauchseinheit**: QueueTransactions  
**Einheit:** Zahl der Anforderungen in 10.000ern  
**Hinweise**: Warteschlangendienstanforderungen (in 10.000ern)  
  
**ID der Verbrauchseinheit**: E518E809-E369-4A45-9274-2017B29FFF25  
**Name der Verbrauchseinheit**: QueueDataTransIn  
**Einheit:** Dateneingang in GB  
**Hinweise**: Eingang von Warteschlangendienstdaten in GB  
  
**ID der Verbrauchseinheit**: DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2  
**Name der Verbrauchseinheit**: QueueDataTransOut  
**Einheit:** Ausgehende Daten in GB  
**Hinweise**: Ausgang von Warteschlangendienstdaten in GB

### <a name="compute"></a>Compute
  
**ID der Verbrauchseinheit**: FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5  
**Name der Verbrauchseinheit**: Base VM Size Hours  
**Einheit:** Virtueller Kern Stunden  
**Hinweise**: Anzahl von virtuellen Kernen multipliziert mit der Anzahl von Stunden, während denen der virtuelle Computer ausgeführt wurde  
  
**ID der Verbrauchseinheit**: 9CD92D4C-BAFD-4492-B278-BEDC2DE8232A  
**Name der Verbrauchseinheit**: Windows VM Size Hours  
**Einheit:** Virtueller Kern Stunden  
**Hinweise**: Anzahl von virtuellen Kernen multipliziert mit der Anzahl von Stunden, während denen der virtuelle Computer ausgeführt wurde  
  
**ID der Verbrauchseinheit**: 6DAB500F-A4FD-49C4-956D-229BB9C8C793  
**Name der Verbrauchseinheit**: VM size hours  
**Einheit:** VM Stunden  
**Hinweise**: Erfasst sowohl die Basis- als auch die Windows-VM. Wird für Kerne nicht angepasst.  
  
### <a name="managed-disks"></a>Managed Disks

**ID der Verbrauchseinheit**: 380874f9-300c-48e0-95a0-d2d9a21ade8f **Name der Verbrauchseinheit:** S4 **Einheit:** Anzahl der Datenträger\*Monat **Hinweise:** Verwalteter Datenträger Standard – 32 GB

**ID der Verbrauchseinheit**: 1b77d90f-427b-4435-b4f1-d78adec53222 **Name der Verbrauchseinheit:** S6 **Einheit:** Anzahl der Datenträger\*Monat **Hinweise:** Verwalteter Datenträger Standard – 64 GB

**ID der Verbrauchseinheit:** d5f7731b-f639-404a-89d0-e46186e22c8d **Name der Verbrauchseinheit:** S10 **Einheit:** Anzahl der Datenträger\*Monat **Hinweise:** Verwalteter Datenträger Standard – 128 GB

**ID der Verbrauchseinheit**: ff85ef31-da5b-4eac-95dd-a69d6f97b18a **Name der Verbrauchseinheit:** S15 **Einheit:** Anzahl der Datenträger\*Monat **Hinweise:** Verwalteter Datenträger Standard – 256 GB

**ID der Verbrauchseinheit**: 88ea9228-457a-4091-adc9-ad5194f30b6e **Name der Verbrauchseinheit:** S20 **Einheit:** Anzahl der Datenträger\*Monat **Hinweise:** Verwalteter Datenträger Standard – 512 GB

**ID der Verbrauchseinheit**: 5b1db88a-8596-4002-8052-347947c26940 **Name der Verbrauchseinheit:** S30 **Einheit:** Anzahl der Datenträger\*Monat **Hinweise:** Verwalteter Datenträger Standard – 1.024 GB

**ID der Verbrauchseinheit**: 7660b45b-b29d-49cb-b816-59f30fbab011 **Name der Verbrauchseinheit:** P4 **Einheit:** Anzahl der Datenträger\*Monat **Hinweise:** Verwalteter Datenträger Premium – 32 GB

**ID der Verbrauchseinheit**: 817007fd-a077-477f-bc01-b876f27205fd **Name der Verbrauchseinheit:** P6 **Einheit:** Anzahl der Datenträger\*Monat **Hinweise:** Verwalteter Datenträger Premium – 64 GB

**ID der Verbrauchseinheit:** e554b6bc-96cd-4938-a5b5-0da990278519 **Name der Verbrauchseinheit:** P10 **Einheit:** Anzahl der Datenträger\*Monat **Hinweise:** Verwalteter Datenträger Premium – 128 GB  

**ID der Verbrauchseinheit:** cdc0f53a-62a9-4472-a06c-e99a23b02907 **Name der Verbrauchseinheit:** P15 **Einheit:** Anzahl der Datenträger\*Monat **Hinweise:** Verwalteter Datenträger Premium – 256 GB

**ID der Verbrauchseinheit:** : b9cb2d1a-84c2-4275-aa8b-70d2145d59aa **Name der Verbrauchseinheit:** P20 **Einheit:** Anzahl der Datenträger\*Monat **Hinweise:** Verwalteter Datenträger Premium – 512 GB

**ID der Verbrauchseinheit**: 06bde724-9f94-43c0-84c3-d0fc54538369 **Name der Verbrauchseinheit:** P30 **Einheit:** Anzahl der Datenträger\*Monat **Hinweise:** Verwalteter Datenträger Premium – 1.024 GB

**ID der Verbrauchseinheit**: 7ba084ec-ef9c-4d64-a179-7732c6cb5e28 **Name der Verbrauchseinheit:** ActualStandardDiskSize **Einheit:** GB\*Monat **Hinweise**: Die tatsächliche Größe auf dem Datenträger von „Verwalteter Datenträger Standard“

**ID der Verbrauchseinheit**: daef389a-06e5-4684-a7f7-8813d9f792d5  
**Name der Verbrauchseinheit**: ActualPremiumDiskSize **Einheit:** GB\*Monat **Hinweise**: Die tatsächliche Größe auf dem Datenträger von „Verwalteter Datenträger Premium“

**ID der Verbrauchseinheit**: 108fa95b-be0d-4cd9-96e8-5b0d59505df1  
**Name der Verbrauchseinheit**: ActualStandardSnapshotSize **Einheit**: GB\*Monat **Hinweise**: Die tatsächliche Größe auf dem Datenträger der verwalteten Standardmomentaufnahme.  

**ID der Verbrauchseinheit**: 578ae51d-4ef9-42f9-85ae-42b52d3d83ac **Name der Verbrauchseinheit:** ActualPremiumSnapshotSize **Einheit**: GB\*Monat **Hinweise**: Die tatsächliche Größe der Momentaufnahme des verwalteten Premium-Datenträgers auf dem Datenträger.

**ID der Verbrauchseinheit**: 5d76e09f-4567-452a-94cc-7d1f097761f0 **Name der Verbrauchseinheit:** S4 **Einheit:** Anzahl der Datenträger\*Stunden **Hinweise:** Verwalteter Datenträger Standard – 32 GB (veraltet)

**ID der Verbrauchseinheit**: dc9fc6a9-0782-432a-b8dc-978130457494 **Name der Verbrauchseinheit:** S6 **Einheit:** Anzahl der Datenträger\*Stunden **Hinweise:** Verwalteter Datenträger Standard – 64 GB (veraltet)

**ID der Verbrauchseinheit**: e5572fce-9f58-49d7-840c-b168c0f01fff **Name der Verbrauchseinheit:** S10 **Einheit:** Anzahl der Datenträger\*Stunden **Hinweise:** Verwalteter Datenträger Standard – 128 GB (veraltet)

**ID der Verbrauchseinheit**: 9a8caedd-1195-4cd5-80b4-a4c22f9302b8 **Name der Verbrauchseinheit:** S15 **Einheit:** Anzahl der Datenträger\*Stunden **Hinweise:** Verwalteter Datenträger Standard – 256 GB (veraltet)

**ID der Verbrauchseinheit**: 5938f8da-0ecd-4c48-8d5a-c7c6c23546be **Name der Verbrauchseinheit:** S20 **Einheit:** Anzahl der Datenträger\*Stunden **Hinweise:** Verwalteter Datenträger Standard – 512 GB (veraltet)

**ID der Verbrauchseinheit**: 7705a158-bd8b-4b2b-b4c2-0782343b81e6 **Name der Verbrauchseinheit:** S30 **Einheit:** Anzahl der Datenträger\*Stunden **Hinweise:** Verwalteter Datenträger Standard – 1.024 GB (veraltet)

**ID der Verbrauchseinheit**: 5c105f5f-cbdf-435c-b49b-3c7174856dcc **Name der Verbrauchseinheit:** P4 **Einheit:** Anzahl der Datenträger\*Stunden **Hinweise:** Verwalteter Datenträger Premium – 32 GB (veraltet)

**ID der Verbrauchseinheit**: 518b412b-1927-4f25-985f-4aea24e55c4f **Name der Verbrauchseinheit:** P6 **Einheit:** Anzahl der Datenträger\*Stunden **Hinweise:** Verwalteter Datenträger Premium – 64 GB (veraltet)

**ID der Verbrauchseinheit**: 5cfb1fed-0902-49e3-8217-9add946fd624 **Name der Verbrauchseinheit:** P10 **Einheit:** Anzahl der Datenträger\*Stunden **Hinweise:** Verwalteter Datenträger Premium – 128 GB (veraltet)  

**ID der Verbrauchseinheit**: 8de91c94-f740-4d9a-b665-bd5974fa08d4 **Name der Verbrauchseinheit:** P15  
**Einheit:** Anzahl der Datenträger\*Stunden **Hinweise:** Verwalteter Datenträger Premium – 256 GB (veraltet)

**ID der Verbrauchseinheit:** c7e7839c-293b-4761-ae4c-848eda91130b **Name der Verbrauchseinheit:** P20 **Einheit:** Anzahl der Datenträger\*Stunden **Hinweise:** Verwalteter Datenträger Premium – 512 GB (veraltet)

**ID der Verbrauchseinheit**: 9f502103-adf4-4488-b494-456c95d23a9f **Name der Verbrauchseinheit:** P30 **Einheit:** Anzahl der Datenträger\*Stunden **Hinweise:** Verwalteter Datenträger Premium – 1.024GB (veraltet)

**ID der Verbrauchseinheit**: 8a409390-1913-40ae-917b-08d0f16f3c38 **Name der Verbrauchseinheit:** ActualStandardDiskSize **Einheit:** Byte\*Stunden **Hinweise:** Die tatsächliche Größe auf dem Datenträger von „Verwalteter Datenträger Standard“ (veraltet)  

**ID der Verbrauchseinheit**: 1273b16f-8458-4c34-8ce2-a515de551ef6  
**Name der Verbrauchseinheit**: ActualPremiumDiskSize **Einheit:** Byte\*Stunden **Hinweise:** Die tatsächliche Größe auf dem Datenträger von „Verwalteter Datenträger Premium“ (veraltet)

**ID der Verbrauchseinheit**: 89009682-df7f-44fe-aeb1-63fba3ddbf4c  
**Name der Verbrauchseinheit**: ActualStandardSnapshotSize **Einheit**: Byte\*Stunden **Hinweise:** Die tatsächliche Größe auf dem Datenträger der verwalteten Standardmomentaufnahme (veraltet)

**ID der Verbrauchseinheit**: 95b0c03f-8a82-4524-8961-ccfbf575f536 **Name der Verbrauchseinheit:** ActualPremiumSnapshotSize **Einheit**: Byte\*Stunden **Hinweise:** Die tatsächliche Größe der Momentaufnahme des verwalteten Premium-Datenträgers auf dem Datenträger (veraltet)

**ID der Verbrauchseinheit**: 75d4b707-1027-4403-9986-6ec7c05579c8 **Name der Verbrauchseinheit**: ActualStandardSnapshotSize **Einheit**: GB\*Monat **Hinweise**: Die tatsächliche Größe auf dem Datenträger der verwalteten Standardmomentaufnahme (veraltet)

**ID der Verbrauchseinheit**: 5ca1cbb9-6f14-4e76-8be8-1ca91547965e **Name der Verbrauchseinheit**: ActualPremiumSnapshotSize **Einheit**: GB\*Monat **Hinweise**: Die tatsächliche Größe der Momentaufnahme des verwalteten Premium-Datenträgers auf dem Datenträger (veraltet)

### <a name="sql-rp"></a>Sql RP
  
**ID der Verbrauchseinheit**: CBCFEF9A-B91F-4597-A4D3-01FE334BED82  
**Name der Verbrauchseinheit**: DatabaseSizeHourSqlMeter  
**Einheit:** MB \* Stunden  
**Hinweise**: Gesamtkapazität der Datenbank bei Erstellung; Wenn Sie die Nutzungs-API täglich aufrufen, gibt die Verbrauchseinheit die Anzahl von MB multipliziert mit der Anzahl von Stunden zurück.  
  
### <a name="mysql-rp"></a>MySql RP
  
**ID der Verbrauchseinheit**: E6D8CFCD-7734-495E-B1CC-5AB0B9C24BD3  
**Name der Verbrauchseinheit**: DatabaseSizeHourMySqlMeter  
**Einheit:** MB \* Stunden  
**Hinweise**: Gesamtkapazität der Datenbank bei Erstellung; Wenn Sie die Nutzungs-API täglich aufrufen, gibt die Verbrauchseinheit die Anzahl von MB multipliziert mit der Anzahl von Stunden zurück.

### <a name="key-vault"></a>Key Vault
  
**ID der Verbrauchseinheit**: EBF13B9F-B3EA-46FE-BF54-396E93D48AB4  
**Name der Verbrauchseinheit**: Key Vault transactions  
**Einheit:** Zahl der Anforderungen in 10.000ern  
**Hinweise**: Zahl der REST-API-Anforderungen, die von der Key Vault-Datenebene empfangen werden  
  
**ID der Verbrauchseinheit**: 2C354225-B2FE-42E5-AD89-14F0EA302C87  
**Name der Verbrauchseinheit**: Transaktionen für erweiterte Schlüssel  
**Einheit:**  10.000 Transaktionen  
**Hinweise**: Transaktionen für RSA 3K/4K-, ECC-Schlüssel (Vorschau)  
  
### <a name="app-service"></a>App Service
  
**ID der Verbrauchseinheit**: 190C935E-9ADA-48FF-9AB8-56EA1CF9ADAA  
**Name der Verbrauchseinheit**: App Service  
**Einheit:** Virtueller Kern Stunden  
**Hinweise**: Anzahl der virtuellen Kerne, die zum Ausführen des App-Diensts verwendet werden

>[!NOTE]
>Microsoft verwendet diese Verbrauchseinheit, um die App Service-Instanz in Azure Stack Hub in Rechnung zu stellen. Cloudlösungsanbieter können die anderen App Service-Verbrauchseinheiten (unten) zum Berechnen der Nutzung für ihre Mandanten verwenden.  
  
**ID der Verbrauchseinheit**: 67CC4AFC-0691-48E1-A4B8-D744D1FEDBDE  
**Name der Verbrauchseinheit**: Functions-Anforderungen  
**Einheit:** 10 Anforderungen  
**Hinweise**: Gesamtzahl der angeforderten Ausführungen (pro 10 Ausführungen) Die Zählung erfolgt bei jeder Ausführung als Antwort auf ein Ereignis oder wird durch eine Bindung ausgelöst.  
  
**ID der Verbrauchseinheit**: D1D04836-075C-4F27-BF65-0A1130EC60ED  
**Name der Verbrauchseinheit**: Functions – Compute  
**Einheit:**  GB-s  
**Hinweise**:  Ressourcenverbrauch gemessen in Gigabytesekunden (GB-s) Der **gemessene Ressourcenverbrauch** wird durch Multiplikation der durchschnittlichen Speichergröße in GB mit der Zeit (in Millisekunden) berechnet, die für die Ausführung der Funktion aufgewendet wird. Der von einer Funktion genutzte Speicher wird auf die nächsten 128 MB gerundet bis zur maximalen Speichergröße von 1536 MB gemessen. Die Ausführungszeit wird durch Runden auf die nächste 1 ms berechnet. Die minimale Ausführungszeit und der minimale Arbeitsspeicher für die Ausführung einer einzelnen Funktion beträgt 100 ms bzw. 128 MB.  
  
**ID der Verbrauchseinheit**: 957E9F36-2C14-45A1-B6A1-1723EF71A01D  
**Name der Verbrauchseinheit**: Shared-App Service-Stunden  
**Einheit:** 1 Stunde **Hinweise:** Nutzung pro Stunde bei Shared-App Service-Plan Pläne werden auf App-Basis abgerechnet.  
  
**ID der Verbrauchseinheit**: 539CDEC7-B4F5-49F6-AAC4-1F15CFF0EDA9  
**Name der Verbrauchseinheit**: Free-App Service-Stunden  
**Einheit:** 1 Stunde **Hinweise:** Nutzung pro Stunde bei kostenlosem App Service-Plan Pläne werden auf App-Basis abgerechnet.  
  
**ID der Verbrauchseinheit**: 88039D51-A206-3A89-E9DE-C5117E2D10A6  
**Name der Verbrauchseinheit**: Standard-App Service-Stunden, klein  
**Einheit:** 1 Stunde **Hinweise:** Wird anhand der Größe und Anzahl der Instanzen berechnet.  
  
**ID der Verbrauchseinheit**: 83A2A13E-4788-78DD-5D55-2831B68ED825  
**Name der Verbrauchseinheit**: Standard-App Service-Stunden, mittel  
**Einheit:** 1 Stunde **Hinweise:** Wird anhand der Größe und Anzahl der Instanzen berechnet.  
  
**ID der Verbrauchseinheit**: 1083B9DB-E9BB-24BE-A5E9-D6FDD0DDEFE6  
**Name der Verbrauchseinheit**: Standard-App Service-Stunden, groß  
**Einheit:** 1 Stunde **Hinweise:** Wird anhand der Größe und Anzahl der Instanzen berechnet.  
  
### <a name="custom-worker-tiers"></a>Benutzerdefinierte Workertarife
  
**ID der Verbrauchseinheit**: *Benutzerdefinierte Workertarife*
**Name der Verbrauchseinheit:** Benutzerdefinierte Workertarife  
**Einheit:** Stunden **Hinweise:** ID der deterministischen Verbrauchseinheit wird basierend auf der SKU und dem Namen des benutzerdefinierten Workertarifs erstellt. Diese Verbrauchseinheit-ID ist für jeden benutzerdefinierten Workertarif eindeutig.  
  
**ID der Verbrauchseinheit**: 264ACB47-AD38-47F8-ADD3-47F01DC4F473  
**Name der Verbrauchseinheit**: SNI SSL  
**Einheit:** Pro SNI SSL-Bindung  
**Hinweise**: App Service unterstützt zwei Arten von SSL-Verbindungen: SSL-Verbindungen vom Typ „Servernamensanzeige“ (Server Name Indication, SNI) und SSL-Verbindungen vom Typ „IP-Adresse“. SNI SSL wird von modernen Browsern unterstützt, IP-basiertes SSL funktioniert bei allen Browsern.  
  
**ID der Verbrauchseinheit**: 60B42D72-DC1C-472C-9895-6C516277EDB4  
**Name der Verbrauchseinheit**: IP-SSL **Einheit:** Pro IP-basierter SSL-Bindung **Hinweise:** App Service unterstützt zwei Arten von SSL-Verbindungen: SSL-Verbindungen vom Typ „Servernamensanzeige“ (Server Name Indication, SNI) und SSL-Verbindungen vom Typ „IP-Adresse“. SNI SSL wird von modernen Browsern unterstützt, IP-basiertes SSL funktioniert bei allen Browsern.  
  
**ID der Verbrauchseinheit**: 73215A6C-FA54-4284-B9C1-7E8EC871CC5B  
**Name der Verbrauchseinheit**:  Webprozess **Einheit:**  
**Hinweise**: Wird pro aktive Website pro Stunde berechnet.
  
**ID der Verbrauchseinheit**: 5887D39B-0253-4E12-83C7-03E1A93DFFD9  
**Name der Verbrauchseinheit**: Externe Ausgangsbandbreite  
**Einheit:** GB  
**Hinweise**: Gesamtanzahl der eingehenden Anforderungsantwortbytes + Gesamtanzahl der ausgehenden Anforderungsantwortbytes + Gesamtanzahl der eingehenden FTP-Anforderungsantwortbytes + Gesamtanzahl der eingehenden Web Deploy-Anforderungsantwortbytes  
  
## <a name="how-do-the-azure-stack-hub-usage-apis-compare-to-the-azure-usage-api-currently-in-public-preview"></a>Wie unterscheiden sich die Azure Stack Hub-Nutzungs-APIs von der [Azure-Nutzungs-API](/azure/billing/billing-usage-rate-card-overview#azure-resource-usage-api-preview) (aktuell in der öffentlichen Vorschau)?

* Die Nutzungs-API für Mandanten stimmt in den meisten Punkten mit der Azure-API überein. Der einzige Unterschied besteht darin, dass das Flag *showDetails* derzeit in Azure Stack Hub nicht unterstützt wird.
* Die Nutzungs-API für Anbieter gibt es nur in Azure Stack Hub.
* Die [RateCard-API](/azure/billing/billing-usage-rate-card-overview#azure-resource-ratecard-api-preview) ist derzeit zwar in Azure verfügbar, aber noch nicht in Azure Stack Hub.

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>Was ist der Unterschied zwischen der Nutzungszeit und der gemeldeten Zeit?

Nutzungsdatenberichte haben zwei Hauptzeitwerte:

* **Gemeldete Zeit:** Der Zeitpunkt, zu dem das Nutzungsereignis im Nutzungssystem begonnen hat
* **Nutzungszeit:** Der Zeitpunkt, zu dem die Azure Stack Hub-Ressource verwendet wurde

Unter Umständen besteht für ein spezifisches Nutzungsereignis eine Diskrepanz zwischen der Nutzungszeit und der gemeldeten Zeit. Die Verzögerung kann in jeder Umgebung mehrere Stunden betragen.

Aktuell können Sie nur nach **gemeldeter Zeit** abfragen.

## <a name="what-do-these-usage-api-error-codes-mean"></a>Fehlercodes der Nutzungs-API und deren Bedeutungen

| **HTTP-Statuscode** | **Fehlercode** | **Beschreibung** |
| --- | --- | --- |
| 400/Bad Request |NoApiVersion |Der Abfrageparameter `api-version` fehlt. |
| 400/Bad Request |InvalidProperty |Eine Eigenschaft fehlt oder weist einen unzulässigen Wert auf. Die Meldung im Fehlercode im Antworttext gibt die fehlende Eigenschaft an. |
| 400/Bad Request |RequestEndTimeIsInFuture |Der Wert für `ReportedEndTime` liegt in der Zukunft. Für dieses Argument sind keine Werte zulässig, die in der Zukunft liegen. |
| 400/Bad Request |SubscriberIdIsNotDirectTenant |Der API-Aufruf eines Anbieters hat eine Abonnement-ID verwendet, die keinem Mandanten des Aufrufers zugeordnet werden kann. |
| 400/Bad Request |SubscriptionIdMissingInRequest |Die Abonnement-ID des Aufrufers fehlt. |
| 400/Bad Request |InvalidAggregationGranularity |Es wurde eine unzulässige Aggregationsgranularität angefordert. Zulässige Werte sind täglich und stündlich. |
| 503 |ServiceUnavailable |Ein wiederholbarer Fehler ist aufgetreten, da der Dienst ausgelastet ist oder der Aufruf gedrosselt wird. |

## <a name="what-is-the-policy-for-charging-for-vms"></a>Welche Gebührenrichtlinie gilt für virtuelle Computer?

Aktive und beendete virtuelle Computer generieren Nutzungsdaten. Um die Generierung von Nutzungsdaten zu beenden, ist genau wie bei Azure eine Aufhebung der Zuordnung erforderlich. Sollte das Portal nicht verfügbar sein und der Computeressourcenanbieter weiterhin ausgeführt werden, werden weiter Nutzungsdaten ausgegeben.

## <a name="how-do-i-extract-usage-data-from-the-azure-stack-hub-usage-apis"></a>Wie extrahiere ich Nutzungsdaten aus den Azure Stack Hub-Nutzungs-APIs?

Nutzungsdaten aus lokalen Nutzungs-APIs in einer Azure Stack Hub-Instanz lassen sich am einfachsten mit dem [Skript zur Nutzungszusammenfassung auf GitHub](https://github.com/Azure/AzureStack-Tools/blob/master/Usage/Usagesummary.ps1) extrahieren. Das Skript erfordert das Start- und Enddatum als Eingabeparameter.

Alternativ dazu können Sie auch REST-APIs verwenden, wie in den Artikeln [Ressourcennutzungs-API für Anbieter](azure-stack-provider-resource-api.md) und [Ressourcennutzungs-API für Mandanten](azure-stack-tenant-resource-usage-api.md) erläutert.

## <a name="how-can-i-associate-usage-extracted-from-azure-usage-apis-to-a-specific-azure-stack-hub-user-subscription"></a>Wie kann ich die aus Azure-Nutzungs-APIs extrahierte Nutzung einem bestimmten Azure Stack Hub-Benutzerabonnement zuordnen?

Die Nutzungsdaten beinhalten einen Eigenschaftenbehälter namens **additionalInfo**, der die Azure Stack Hub-Abonnement-ID enthält. Diese ID ist das Benutzerabonnement, dem der entsprechende Nutzungsdatensatz entspringt.

## <a name="next-steps"></a>Nächste Schritte

* [Kundenabrechnung und verbrauchsbasierte Kostenzuteilung in Azure Stack Hub](azure-stack-billing-and-chargeback.md)
* [Ressourcennutzungs-API für Anbieter](azure-stack-provider-resource-api.md)
* [Ressourcennutzungs-API für Mandanten](azure-stack-tenant-resource-usage-api.md)
