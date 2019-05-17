---
title: Integration des Azure Stack-Datencenters – DNS
description: Erfahren Sie, wie Sie das Azure Stack-DNS in Ihr Datencenter-DNS integrieren.
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 05/09/2019
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 05/09/2019
keywords: ''
ms.openlocfilehash: bf1aed6c8140f0c0753f49195082dfd71737868a
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618667"
---
# <a name="azure-stack-datacenter-integration---dns"></a>Integration des Azure Stack-Datencenters – DNS

Für den Zugriff auf Azure Stack-Endpunkte ((**portal**, **adminportal**, **management**, **adminmanagement** usw.) von außerhalb von Azure Stack müssen Sie die Azure Stack-DNS-Dienste in die DNS-Server integrieren, die die DNS-Zonen hosten, die Sie in Azure Stack verwenden möchten.

## <a name="azure-stack-dns-namespace"></a>DNS-Namespace von Azure Stack

Sie müssen einige wichtige Informationen über das DNS zur Verfügung stellen, wenn Sie Azure Stack bereitstellen.


|Feld  |BESCHREIBUNG  |Beispiel|
|---------|---------|---------|
|Region|Der geografische Standort Ihrer Azure Stack-Bereitstellung.|`east`|
|Externer Domänenname|Der Name der Zone, die Sie für Ihre Azure Stack-Bereitstellung verwenden möchten.|`cloud.fabrikam.com`|
|Interner Domänenname|Der Name der internen Zone, die für die Infrastrukturdienste in Azure Stack verwendet wird.  Dieser ist im Verzeichnisdienst integriert und privat (nicht von außerhalb der Azure Stack-Bereitstellung erreichbar).|`azurestack.local`|
|DNS-Weiterleitung|DNS-Server, die zum Weiterleiten von DNS-Abfragen verwendet werden, DNS-Zonen und -Einträge, die außerhalb von Azure Stack gehostet werden, entweder im Unternehmensintranet oder im öffentlichen Internet.|`10.57.175.34`<br>`8.8.8.8`|
|Namenspräfix (optional)|Das Namenspräfix, das die Computernamen der Rolleninstanz Ihrer Azure Stack-Infrastruktur aufweisen sollen.  Wenn dieses nicht angegeben wird, lautet der Standardwert `azs`.|`azs`|

Der vollqualifizierte Domänenname (fully qualified domain name, FQDN) Ihrer Azure Stack-Bereitstellung und -Endpunkte ist die Kombination aus den Parametern „Region“ und „externer Domänenname“. Wenn die Werte aus den Beispielen der vorherigen Tabelle verwendet werden, lautet der vollqualifizierte Domänenname für diese Azure Stack-Bereitstellung folgendermaßen:

`east.cloud.fabrikam.com`

Beispiele für einige Endpunkte dieser Bereitstellung würden wie folgende URLs aussehen:

`https://portal.east.cloud.fabrikam.com`

`https://adminportal.east.cloud.fabrikam.com`

Folgende Bedingungen sind erforderlich, um diesen DNS-Beispielnamespace für eine Azure Stack-Bereitstellung zu verwenden:

- Die Zone `fabrikam.com` ist entweder mit einer Domänenregistrierungsstelle, einem internen DNS-Unternehmensserver oder beidem registriert. Dies ist abhängig von Ihren Anforderungen für die Namensauflösung.
- Die untergeordnete Domäne `cloud.fabrikam.com` ist in der Zone `fabrikam.com` vorhanden.
- Die DNS-Server, die die Zonen `fabrikam.com` und `cloud.fabrikam.com` hosten, können von der Azure Stack-Bereitstellung aus erreicht werden.

Damit Sie die DNS-Namen für Azure Stack-Endpunkte und -Instanzen von außerhalb von Azure Stack auflösen können, müssen Sie die DNS-Server integrieren, die die externen DNS-Zonen für Azure Stack hosten sowie die DNS-Server, die die übergeordnete Zone hosten, die Sie verwenden möchten.

### <a name="dns-name-labels"></a>DNS-Namensbezeichnungen

Azure Stack unterstützt das Hinzufügen einer DNS-Namensbezeichnung zu einer öffentlichen IP-Adresse, um eine Namensauflösung für öffentliche IP-Adressen zu ermöglichen. Benutzer können dies als bequeme Möglichkeit nutzen, um in Azure Stack gehostete Anwendungen und Dienste anhand des Namens zu erreichen. Die DNS-Namensbezeichnung verwendet einen etwas anderen Namespace als die Infrastrukturendpunkte. Gemäß dem vorherigen Beispiel-Namespace sieht der Namespace für DNS-Namensbezeichnungen wie folgt aus:

`*.east.cloudapp.cloud.fabrikam.com`

Wenn ein Mandant daher den Wert **Myapp** im Feld für die DNS-Namensbezeichnung einer öffentlichen IP-Adresse angibt, wird auf dem externen DNS-Server von Azure Stack ein A-Eintrag für **myapp** in der Zone **east.cloudapp.cloud.fabrikam.com** erstellt. Daraus ergibt sich der folgende voll qualifizierte Domänenname:

`myapp.east.cloudapp.cloud.fabrikam.com`

Wenn Sie diese Funktionalität nutzen und diesen Namespace verwenden möchten, müssen Sie die DNS-Server, die die externe DNS-Zone für Azure Stack hosten, in die DNS-Server integrieren, die die übergeordnete Zone hosten, die Sie ebenfalls verwenden möchten. Dies ist ein anderer Namespace als der für die Azure Stack-Dienstendpunkte verwendete Namespace. Daher müssen Sie eine zusätzliche Delegierung oder Regel für bedingte Weiterleitung erstellen.

Weitere Informationen zur Funktionsweise der DNS-Namensbezeichnung finden Sie unter [Verwenden von DNS in Azure Stack](../user/azure-stack-dns.md).

## <a name="resolution-and-delegation"></a>Auflösung und Delegierung

Es gibt zwei Arten von DNS-Server:

- Ein autoritativer DNS-Server hostet DNS-Zonen. Er antwortet nur auf DNS-Abfragen nach Einträgen für diese Zonen.
- Ein rekursiver DNS-Server hostet keine DNS-Zonen. Er reagiert auf alle DNS-Abfragen durch Aufrufen der autoritativen DNS-Server, um die benötigten Daten zu erfassen.

Azure Stack enthält autoritative und rekursive DNS-Server. Die rekursiven Server werden verwendet, um alle Namen außer denen der internen privaten Zone und der externen öffentlichen DNS-Zone für diese Azure Stack-Bereitstellung aufzulösen. 

![DNS-Architektur von Azure Stack](media/azure-stack-integrate-dns/Integrate-DNS-01.png)

## <a name="resolving-external-dns-names-from-azure-stack"></a>Auflösen von externen DNS-Namen in Azure Stack

Zum Auflösen von DNS-Namen für Endpunkte außerhalb von Azure Stack (z.B. www.bing.com) müssen Sie DNS-Server bereitstellen, die Azure Stack für die Weiterleitung von DNS-Anforderungen verwenden kann, für die Azure Stack nicht autoritativ ist. Für die Bereitstellung sind die DNS-Server, an die Azure Stack Anforderungen weiterleitet, im Arbeitsblatt der Bereitstellung erforderlich (im Feld „DNS-Weiterleitung“). Geben Sie für die Fehlertoleranz mindestens zwei Server in diesem Feld an. Ohne diese Werte schlägt die Azure Stack-Bereitstellung fehl.

### <a name="configure-conditional-dns-forwarding"></a>Konfigurieren der bedingten DNS-Weiterleitung

> [!IMPORTANT]
> Diese Informationen gelten nur für eine AD FS-Bereitstellung.

Um die Namensauflösung mit Ihrer vorhandenen DNS-Infrastruktur zu ermöglichen, konfigurieren Sie eine bedingte Weiterleitung.

Zum Hinzufügen einer bedingten Weiterleitung müssen Sie den privilegierten Endpunkt verwenden.

Verwenden Sie für diesen Vorgang einen Computer in Ihrem Datencenternetzwerk, der mit dem privilegierten Endpunkt in Azure Stack kommunizieren kann.

1. Öffnen Sie eine Windows PowerShell-Sitzung mit erhöhten Rechten (Als Administrator ausführen), und stellen Sie eine Verbindung zur IP-Adresse des privilegierten Endpunkts her. Verwenden Sie die Anmeldeinformationen für die CloudAdmin-Authentifizierung.

   ```
   $cred=Get-Credential 
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $cred
   ```

2. Nachdem Sie eine Verbindung mit dem privilegierten Endpunkt hergestellt haben, führen Sie folgenden PowerShell-Befehl aus. Ersetzen Sie die bereitgestellten Beispielwerte durch Ihren Domänennamen und die IP-Adressen des DNS-Servers, den Sie verwenden möchten.

   ```
   Register-CustomDnsServer -CustomDomainName "contoso.com" -CustomDnsIPAddresses "192.168.1.1","192.168.1.2"
   ```

## <a name="resolving-azure-stack-dns-names-from-outside-azure-stack"></a>Auflösen von Azure Stack-DNS-Namen von außerhalb von Azure Stack
Die autoritativen Server enthalten die Informationen zu externen DNS-Zonen und zu allen vom Benutzer erstellten Zonen. Integrieren Sie diese Server, um die Zonendelegierung oder die bedingte Weiterleitung für das Auflösen von Azure Stack-DNS-Namen von außerhalb von Azure Stack zu aktivieren.

## <a name="get-dns-server-external-endpoint-information"></a>Erhalten der externen Endpunktinformationen des DNS-Servers

Sie benötigen folgende Informationen, um Ihre Azure Stack-Bereitstellung in Ihre DNS-Infrastruktur zu integrieren:

- Vollqualifizierte Domänennamen des DNS-Servers
- IP-Adressen des DNS-Servers

Die vollqualifizierten Domänennamen für die Azure Stack-DNS-Server weisen folgendes Format auf:

`<NAMINGPREFIX>-ns01.<REGION>.<EXTERNALDOMAINNAME>`

`<NAMINGPREFIX>-ns02.<REGION>.<EXTERNALDOMAINNAME>`

Wenn Sie die Beispielwerte verwenden, sind die vollqualifizierten Domänennamen für die DNS-Server Folgende:

`azs-ns01.east.cloud.fabrikam.com`

`azs-ns02.east.cloud.fabrikam.com`


Diese Informationen werden auch am Ende aller Azure Stack-Bereitstellungen in einer Datei namens `AzureStackStampInformation.json` erstellt. Diese Datei befindet sich im Ordner `C:\CloudDeployment\logs` des virtuellen Computers der Bereitstellung. Wenn Sie nicht sicher sind, welche Werte für Ihre Azure Stack-Bereitstellung verwendet werden, können Sie diese Werte hier abrufen.

Wenn der virtuelle Computer für die Bereitstellung nicht mehr verfügbar ist oder nicht darauf zugegriffen werden kann, können Sie die Werte erhalten, indem Sie eine Verbindung mit dem privilegierten Endpunkt herstellen und das PowerShell-Cmdlet `Get-AzureStackStampInformation` ausführen. Weitere Informationen finden Sie unter [Privilegierter Endpunkt](azure-stack-privileged-endpoint.md).

## <a name="setting-up-conditional-forwarding-to-azure-stack"></a>Einrichten der bedingten Weiterleitung an Azure Stack

Der einfachste und sicherste Weg, um Azure Stack in Ihre DNS-Infrastruktur zu integrieren, ist die bedingte Weiterleitung der Zone von dem Server aus, der die übergeordnete Zone hostet. Dieser Ansatz wird empfohlen, wenn Sie die direkte Kontrolle über die DNS-Server besitzen, die die übergeordnete Zone für Ihren externen DNS-Namespace von Azure Stack hosten.

Wenn Sie nicht mit der bedingten Weiterleitung mit DNS vertraut sind, lesen Sie folgenden TechNet-Artikel: [Zuweisen einer bedingten Weiterleitung für einen Domänennamen](https://technet.microsoft.com/library/cc794735), oder die Dokumentation zu Ihrer DNS-Lösung.

In Szenarios, in denen Sie angegeben haben, dass Ihre externe Azure Stack-DNS-Zone wie die untergeordnete Domäne des Namens Ihrer Unternehmensdomäne aussehen soll, kann die bedingte Weiterleitung nicht verwendet werden. Die DNS-Delegierung muss konfiguriert werden.

Beispiel:

- Name der DNS-Unternehmensdomäne: `contoso.com`
- Externer Azure Stack-DNS-Domänenname: `azurestack.contoso.com`

## <a name="delegating-the-external-dns-zone-to-azure-stack"></a>Delegieren der externen DNS-Zone an Azure Stack

Damit DNS-Namen von außerhalb einer Azure Stack-Bereitstellung auflösbar sind, müssen Sie eine DNS-Delegierung einrichten.

Jede Registrierungsstelle hat seine eigenen DNS-Verwaltungstools, um die Namenservereinträge für eine Domäne zu ändern. Bearbeiten Sie auf der DNS-Verwaltungsseite der Registrierungsstelle die NS-Einträge, und ersetzen Sie die NS-Einträge für die Zone durch die in Azure Stack erstellten Einträge.

Die meisten DNS-Registrierungsstellen erfordern, dass Sie mindestens zwei DNS-Server angeben, um die Delegierung abzuschließen.

## <a name="next-steps"></a>Nächste Schritte

[Firewallintegration](azure-stack-firewall.md)
