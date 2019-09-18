---
title: Azure Stack-PKI-Zertifikatanforderungen für in Azure Stack integrierte Systeme | Microsoft-Dokumentation
description: Beschreibt die Azure Stack-PKI-Zertifikatanforderungen für in Azure Stack integrierte Systeme.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2019
ms.author: justinha
ms.reviewer: ppacent
ms.lastreviewed: 09/10/2019
ms.openlocfilehash: 53d8e3daecba269bcdd21fc726e312758f1f6c6f
ms.sourcegitcommit: 38f21e0bcf7b593242ad615c9d8ef8a1ac19c734
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70902700"
---
# <a name="azure-stack-public-key-infrastructure-certificate-requirements"></a>Azure Stack-PKI-Zertifikatanforderungen

Azure Stack verfügt über ein öffentliches Infrastrukturnetz mit extern zugänglichen öffentlichen IP-Adressen, die einer kleinen Gruppe von Azure Stack-Diensten und möglicherweise Mandanten-VMs zugewiesen sind. PKI-Zertifikate (Public Key-Infrastruktur) mit den entsprechenden DNS-Namen für diese Endpunkte der öffentlichen Infrastruktur von Azure Stack werden während der Bereitstellung von Azure Stack benötigt. Dieser Artikel enthält Informationen zu Folgendem:

- Den zum Bereitstellen von Azure Stack erforderlichen Zertifikaten
- Der Prozess des Beziehens der Zertifikate, die diesen Vorgaben entsprechen
- Vorbereiten, Überprüfen und Verwenden dieser Zertifikate während der Bereitstellung

> [!NOTE]
> Azure Stack verwendet standardmäßig auch Zertifikate, die von einer internen, in Active Directory integrierten Zertifizierungsstelle (Certificate Authority, CA) für die Authentifizierung zwischen den Knoten ausgestellt werden. Um das Zertifikat zu überprüfen, vertrauen alle Azure Stack-Infrastrukturcomputer dem Stammzertifikat der internen Zertifizierungsstelle, indem sie dieses Zertifikat dem lokalen Zertifikatspeicher hinzufügen. In Azure Stack werden keine Zertifikate angeheftet oder auf eine Whitelist gesetzt. Der SAN der einzelnen Serverzertifikate wird anhand des FQDN des Ziels überprüft. Außerdem werden die gesamte Vertrauenskette sowie das Ablaufdatum des Zertifikats (standardmäßige TLS-Server-Authentifizierung ohne Anheften von Zertifikaten) überprüft.

## <a name="certificate-requirements"></a>Zertifikatanforderungen
Die folgende Liste beschreibt die Zertifikatsanforderungen, die für die Bereitstellung von Azure Stack erfüllt sein müssen: 
- Zertifikate müssen von einer internen oder öffentlichen Zertifizierungsstelle ausgestellt werden. Wenn eine öffentliche Zertifizierungsstelle verwendet wird, muss diese im Rahmen des Microsoft Trusted Root Authority Program in das Basisbetriebssystem-Image aufgenommen werden. Die vollständige Liste finden Sie hier: https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca 
- Ihre Azure Stack-Infrastruktur muss über Netzwerkzugriff auf den im Zertifikat veröffentlichten Speicherort der Zertifikatsperrliste (Certificate Revocation List, CRL) der Zertifizierungsstelle verfügen. Bei dieser CRL muss es sich um einen HTTP-Endpunkt handeln.
- Beim Rotieren von Zertifikaten in Builds vor 1903 müssen diese entweder von der gleichen internen Zertifizierungsstelle stammen, die auch zum Signieren der bei der Bereitstellung angegebenen Zertifikate verwendet wurde, oder von einer der oben angegebenen öffentlichen Zertifizierungsstellen. Ab Build 1903 können Zertifikate von einer beliebige Unternehmens- oder öffentlichen Zertifizierungsstelle ausgestellt werden.
- Die Verwendung selbstsignierter Zertifikate wird nicht unterstützt.
- Für Bereitstellung und Rotation können Sie entweder ein einziges Zertifikat verwenden, das alle Namespaces in den Feldern „Antragstellername“ und „Alternativer Antragstellername“ des Zertifikats abdeckt. ODER Sie können einzelne Zertifikate für jeden der folgenden Namespaces verwenden, die die Azure Stack-Dienste benötigen, deren Nutzung Sie planen. Beide Ansätze erfordern bei Bedarf die Verwendung von Platzhaltern für Endpunkte, z.B. **KeyVault** und **KeyVaultInternal**. 
- Die PFX-Verschlüsselung des Zertifikats sollte mit 3DES erfolgen. 
- Der Zertifikatsignaturalgorithmus sollte nicht SHA1 sein. 
- Das Zertifikatsformat muss PFX sein, da sowohl der öffentliche als auch der private Schlüssel für die Installation von Azure Stack benötigt werden. Für den privaten Schlüssel muss das Schlüsselattribut des lokalen Computers festgelegt sein.
- Die PFX-Verschlüsselung muss 3DES sein. (Dies ist beim Exportieren von einem Windows 10-Client oder einem Windows Server 2016-Zertifikatspeicher der Standard.)
- Die PFX-Zertifikatdateien müssen im Feld „Schlüsselverwendung“ einen Wert in „Digitale Signatur“ und „Schlüsselchiffrierung“ enthalten.
- Die PFX-Zertifikatdateien müssen die Werte „Serverauthentifizierung (1.3.6.1.5.5.7.3.1)“ und „Clientauthentifizierung (1.3.6.1.5.5.7.3.2)“ im Feld „Erweiterte Schlüsselverwendung“ aufweisen.
- Das Feld „Ausgestellt für:“ des Zertifikats darf nicht mit dem Feld „Ausgestellt von:“ identisch sein.
- Die Kennwörter aller PFX-Zertifikatdateien müssen zum Zeitpunkt der Bereitstellung identisch sein.
- Für die PFX-Zertifikatdatei muss ein komplexes Kennwort verwendet werden. Erstellen Sie ein Kennwort, das die folgenden Anforderungen an die Komplexität von Kennwörtern erfüllt. Mindestlänge von 8 Zeichen. Das Kennwort enthält mindestens drei der folgenden Elemente: Großbuchstaben, Kleinbuchstaben, Zahlen von 0–9, Sonderzeichen, alphabetische Zeichen, die weder Groß- noch Kleinbuchstaben sind. Notieren Sie sich dieses Kennwort. Sie verwenden es noch als Bereitstellungsparameter.
- Stellen Sie sicher, dass die Antragstellernamen und alternativen Antragstellernamen in der Erweiterung für alternative Antragstellernamen (x509v3_config) übereinstimmen. Im Feld für den alternativen Antragstellernamen können Sie zusätzliche Hostnamen (Websites, IP-Adressen, allgemeine Namen) angeben, die durch ein einzelnes SSL-Zertifikat geschützt werden sollen.

> [!NOTE]  
> Selbstsignierte Zertifikate werden nicht unterstützt.

> [!NOTE]  
> Das Vorhandensein von Zwischenzertifizierungsstellen in der Vertrauenskette eines Zertifikats *wird unterstützt*. 

## <a name="mandatory-certificates"></a>Erforderliche Zertifikate
Die Tabelle in diesem Abschnitt beschreibt die PKI-Zertifikate für öffentliche Azure Stack-Endpunkte, die sowohl für Azure AD- als auch für AD FS-gestützte Azure Stack-Installationen erforderlich sind. Zertifikatsanforderungen werden nach Bereichen gruppiert, ebenso wie die verwendeten Namespaces und die Zertifikate, die für jeden Namespace benötigt werden. Die Tabelle beschreibt auch den Ordner, in den Ihr Lösungsanbieter die verschiedenen Zertifikate für jeden öffentlichen Endpunkt kopiert. 

Zertifikate mit den entsprechenden DNS-Namen für diese Endpunkte der öffentlichen Infrastruktur von Azure Stack sind erforderlich. Der DNS-Name von Endpunkten wird im folgenden Format angegeben: *&lt;Präfix>.&lt;Region>.&lt;FQDN>* . 

Für Ihre Bereitstellung müssen die Werte [region] und [externalfqdn] mit der Region und den externen Domänennamen übereinstimmen, die Sie für Ihr Azure Stack-System ausgewählt haben. Beispiel: Wenn der Name der Region *Redmond* und der externe Domänenname *contoso.com* lautet, haben die DNS-Namen das Format *&lt;Präfix>.redmond.contoso.com*. Die *&lt;Präfix>* -Werte werden von Microsoft vordefiniert, um den durch das Zertifikat geschützten Endpunkt zu beschreiben. Darüber hinaus hängen die *&lt;Präfix>* -Werte der externen Infrastrukturendpunkte vom Azure Stack-Dienst ab, der den spezifischen Endpunkt verwendet. 

Für Produktionsumgebungen wird empfohlen, für jeden Endpunkt eigene Zertifikate zu generieren und in das entsprechende Verzeichnis zu kopieren. In Entwicklungsumgebungen können Zertifikate als einzelnes, in alle Verzeichnisse kopiertes Platzhalterzertifikat bereitgestellt werden, das alle Namespaces in den Feldern für Antragsteller und alternativen Antragstellernamen (Subject Alternative Name, SAN) abdeckt. Ein einzelnes Zertifikat für alle Endpunkte und Dienste ist eine unsichere Methode und sollte daher nur in der Entwicklung verwendet werden. Wie bereits erwähnt, müssen für beide Optionen Platzhalterzertifikate für Endpunkte wie **acs** und Key Vault verwendet werden, wo diese erforderlich sind. 

> [!Note]  
> Während der Bereitstellung müssen Sie Zertifikate in den Bereitstellungsordner kopieren, der zu dem Identitätsanbieter gehört, für den Sie die Bereitstellung ausführen (Azure AD oder AD FS). Wenn Sie ein einzelnes Zertifikat für alle Endpunkte verwenden, müssen Sie diese Zertifikatdatei, wie in den folgenden Tabellen aufgeführt, in jeden Bereitstellungsordner kopieren. Die Ordnerstruktur ist bereits im virtuellen Computer für die Bereitstellung unter folgendem Pfad festgelegt: C:\CloudDeployment\Setup\Certificates. 


| Bereitstellungsordner | Erforderlicher Zertifikatantragsteller und alternative Antragstellernamen | Bereich (pro Region) | Namespace der Unterdomäne |
|-------------------------------|------------------------------------------------------------------|----------------------------------|-----------------------------|
| Öffentliches Portal | portal.&lt;Region>.&lt;FQDN> | Portale | &lt;Region>.&lt;FQDN> |
| Verwaltungsportal | adminportal.&lt;Region>.&lt;FQDN> | Portale | &lt;Region>.&lt;FQDN> |
| Azure Resource Manager (Öffentlich) | management.&lt;Region>.&lt;FQDN> | Azure Resource Manager | &lt;Region>.&lt;FQDN> |
| Azure Resource Manager (Verwaltung) | adminmanagement.&lt;Region>.&lt;FQDN> | Azure Resource Manager | &lt;Region>.&lt;FQDN> |
| ACSBlob | *.blob.&lt;Region>.&lt;FQDN><br>(SSL-Platzhalterzertifikat) | Blob Storage | blob.&lt;Region>.&lt;FQDN> |
| ACSTable | *.table.&lt;Region>.&lt;FQDN><br>(SSL-Platzhalterzertifikat) | Table Storage | table.&lt;Region>.&lt;FQDN> |
| ACSQueue | *.queue.&lt;Region>.&lt;FQDN><br>(SSL-Platzhalterzertifikat) | Queue Storage | queue.&lt;Region>.&lt;FQDN> |
| KeyVault | *.vault.&lt;Region>.&lt;FQDN><br>(SSL-Platzhalterzertifikat) | Key Vault | vault.&lt;Region>.&lt;FQDN> |
| KeyVaultInternal | *.adminvault.&lt;Region>.&lt;FQDN><br>(SSL-Platzhalterzertifikat) |  Interner Schlüsseltresor |  adminvault.&lt;Region>.&lt;FQDN> |
| Administratorerweiterungshost | *.adminhosting.\<Region>.\<FQDN> (SSL-Platzhalterzertifikate) | Administratorerweiterungshost | adminhosting.\<Region>.\<FQDN> |
| Öffentlicher Erweiterungshost | *.hosting.\<Region>.\<FQDN> (SSL-Platzhalterzertifikate) | Öffentlicher Erweiterungshost | hosting.\<region>.\<fqdn> |

Wenn Sie Azure Stack im Azure AD-Bereitstellungsmodus bereitstellen, müssen Sie nur die in der vorigen Tabelle aufgeführten Zertifikate anfordern. Wenn Sie jedoch Azure Stack mit dem AD FS-Bereitstellungsmodus bereitstellen, müssen Sie auch die in der folgenden Tabelle beschriebenen Zertifikate anfordern:

|Bereitstellungsordner|Erforderlicher Zertifikatantragsteller und alternative Antragstellernamen|Bereich (pro Region)|Namespace der Unterdomäne|
|-----|-----|-----|-----|
|ADFS|adfs. *&lt;region>.&lt;fqdn>*<br>(SSL-Zertifikat)|ADFS|*&lt;region>.&lt;fqdn>*|
|Graph|graph. *&lt;region>.&lt;fqdn>*<br>(SSL-Zertifikat)|Graph|*&lt;region>.&lt;fqdn>*|
|

> [!IMPORTANT]
> Alle Zertifikate, die in diesem Abschnitt aufgeführt sind, müssen das gleiche Kennwort haben. 

## <a name="optional-paas-certificates"></a>Optionale PaaS-Zertifikate
Wenn Sie die zusätzlichen Azure Stack-PaaS-Dienste (SQL, MySQL und App Service) bereitstellen möchten, nachdem Azure Stack bereitgestellt und konfiguriert wurde, müssen Sie zusätzliche Zertifikate anfordern, um die Endpunkte der PaaS-Dienste abzudecken. 

> [!IMPORTANT]
> Die Zertifikate, die Sie für App Service-, SQL- und MySQL-Ressourcenanbieter verwenden, müssen die gleiche Stammzertifizierungsstelle haben wie die Zertifikate, die für die globalen Azure Stack-Endpunkte verwendet werden. 

In der folgenden Tabelle werden die Endpunkte und Zertifikate beschrieben, die für die SQL- und MySQL-Adapter sowie für App Service erforderlich sind. Sie müssen diese Zertifikate nicht in den Azure Stack-Bereitstellungsordner kopieren. Stattdessen stellen Sie diese Zertifikate bei der Installation der zusätzlichen Ressourcenanbieter bereit. 

|Bereich (pro Region)|Zertifikat|Erforderlicher Zertifikatantragsteller und alternative Antragstellernamen|Namespace der Unterdomäne|
|-----|-----|-----|-----|
|SQL, MySQL|SQL und MySQL|&#42;.dbadapter. *&lt;region>.&lt;fqdn>*<br>(SSL-Platzhalterzertifikat)|dbadapter. *&lt;region>.&lt;fqdn>*|
|App Service|Webdatenverkehr: SSL-Standardzertifikat|&#42;.appservice. *&lt;region>.&lt;fqdn>*<br>&#42;.scm.appservice. *&lt;region>.&lt;fqdn>*<br>&#42;.sso.appservice. *&lt;Region>.&lt;FQDN>*<br>(SSL-Platzhalterzertifikat für mehrere Domänen<sup>1</sup>)|appservice. *&lt;region>.&lt;fqdn>*<br>scm.appservice. *&lt;region>.&lt;fqdn>*|
|App Service|API|api.appservice. *&lt;region>.&lt;fqdn>*<br>(SSL-Zertifikat<sup>2</sup>)|appservice. *&lt;region>.&lt;fqdn>*<br>scm.appservice. *&lt;region>.&lt;fqdn>*|
|App Service|FTP|ftp.appservice. *&lt;region>.&lt;fqdn>*<br>(SSL-Zertifikat<sup>2</sup>)|appservice. *&lt;region>.&lt;fqdn>*<br>scm.appservice. *&lt;region>.&lt;fqdn>*|
|App Service|SSO|sso.appservice. *&lt;region>.&lt;fqdn>*<br>(SSL-Zertifikat<sup>2</sup>)|appservice. *&lt;region>.&lt;fqdn>*<br>scm.appservice. *&lt;region>.&lt;fqdn>*|

<sup>1</sup> Erfordert ein Zertifikat mit Platzhaltern für mehrere alternative Antragstellernamen. Mehrere Platzhalter für alternative Antragstellernamen auf einem einzigen Zertifikat werden ggf. nicht von allen öffentlichen Zertifizierungsstellen unterstützt. 

<sup>2</sup> Ein Platzhalterzertifikat des Typs &#42;.appservice. *&lt;region>.&lt;fqdn>* kann nicht anstelle dieser drei Zertifikate (api.appservice. *&lt;region>.&lt;fqdn>* , ftp.appservice. *&lt;region>.&lt;fqdn>* und sso.appservice. *&lt;region>.&lt;fqdn>* ) verwendet werden. App Service erfordert explizit die Verwendung separater Zertifikate für diese Endpunkte. 

## <a name="learn-more"></a>Weitere Informationen
Erfahren Sie mehr zum [Generieren von PKI-Zertifikaten für die Azure Stack-Bereitstellung](azure-stack-get-pki-certs.md). 

## <a name="next-steps"></a>Nächste Schritte
[Identitätsintegration](azure-stack-integrate-identity.md)