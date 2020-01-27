---
title: DNS-Integration in ein Azure Stack Hub-Rechenzentrum | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie das DNS von Azure Stack Hub in das DNS Ihres Rechenzentrums integrieren.
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 08/21/2019
keywords: ''
ms.openlocfilehash: 3ef8bb7595711c5df991956d6cbde8d4e379ec47
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76535279"
---
# <a name="azure-stack-hub-datacenter-dns-integration"></a>DNS-Integration in ein Azure Stack Hub-Rechenzentrum

Um von außerhalb eines Azure Stack Hub-Rechenzentrums auf Azure Stack Hub-Endpunkte wie **portal**, **adminportal**, **management** und **adminmanagement** zugreifen zu können, müssen Sie die Azure Stack Hub-DNS-Dienste in die DNS-Server integrieren, die die DNS-Zonen hosten, die Sie in Azure Stack Hub verwenden möchten.

## <a name="azure-stack-hub-dns-namespace"></a>DNS-Namespace von Azure Stack Hub

Sie müssen einige wichtige Informationen zum DNS angeben, wenn Sie Azure Stack Hub bereitstellen.


|Feld  |Beschreibung  |Beispiel|
|---------|---------|---------|
|Region|Der geografische Standort Ihrer Azure Stack Hub-Bereitstellung.|`east`|
|Externer Domänenname|Der Name der Zone, die Sie für Ihre Azure Stack Hub-Bereitstellung verwenden möchten.|`cloud.fabrikam.com`|
|Interner Domänenname|Der Name der internen Zone, die für die Infrastrukturdienste in Azure Stack Hub verwendet wird. Sie ist in den Verzeichnisdienst integriert und privat (nicht von außerhalb der Azure Stack Hub-Bereitstellung erreichbar).|`azurestack.local`|
|DNS-Weiterleitungen|DNS-Server, die zum Weiterleiten von DNS-Abfragen verwendet werden, DNS-Zonen und -Einträge, die außerhalb von Azure Stack Hub gehostet werden – entweder im Unternehmensintranet oder im öffentlichen Internet. Sie können den Wert für die DNS-Weiterleitung nach der Bereitstellung mit dem [**Set-AzSDnsForwarder**-Cmdlet](#editing-dns-forwarder-ips) bearbeiten. 
|Namenspräfix (optional)|Das Namenspräfix, das die Computernamen der Rolleninstanz Ihrer Azure Stack Hub-Infrastruktur aufweisen sollen.  Wenn dieses nicht angegeben wird, lautet der Standardwert `azs`.|`azs`|

Der vollqualifizierte Domänenname (Fully Qualified Domain Name, FQDN) Ihrer Azure Stack Hub-Bereitstellung und -Endpunkte ist die Kombination aus den Parametern „Region“ und „externer Domänenname“. Wenn die Werte aus den Beispielen der vorherigen Tabelle verwendet werden, lautet der vollqualifizierte Domänenname für diese Azure Stack Hub-Bereitstellung folgendermaßen:

`east.cloud.fabrikam.com`

Beispiele für einige Endpunkte dieser Bereitstellung würden wie folgende URLs aussehen:

`https://portal.east.cloud.fabrikam.com`

`https://adminportal.east.cloud.fabrikam.com`

Für die Verwendung dieses DNS-Beispielnamespace für eine Azure Stack Hub-Bereitstellung gelten die folgenden Bedingungen:

- Die Zone `fabrikam.com` ist entweder mit einer Domänenregistrierungsstelle, einem internen DNS-Unternehmensserver oder beidem registriert. Dies ist abhängig von Ihren Anforderungen für die Namensauflösung.
- Die untergeordnete Domäne `cloud.fabrikam.com` ist in der Zone `fabrikam.com` vorhanden.
- Die DNS-Server, die die Zonen `fabrikam.com` und `cloud.fabrikam.com` hosten, sind von der Azure Stack Hub-Bereitstellung aus erreichbar.

Damit Sie die DNS-Namen für Azure Stack Hub-Endpunkte und -Instanzen von außerhalb von Azure Stack Hub auflösen können, müssen Sie die DNS-Server zum Hosten der externen DNS-Zonen für Azure Stack Host mit den DNS-Servern integrieren, die die zu verwendende übergeordnete Zone hosten.

### <a name="dns-name-labels"></a>DNS-Namensbezeichnungen

Azure Stack Hub unterstützt das Hinzufügen einer DNS-Namensbezeichnung zu einer öffentlichen IP-Adresse, um eine Namensauflösung für öffentliche IP-Adressen zu ermöglichen. Benutzer können DNS-Bezeichnungen als bequeme Möglichkeit nutzen, um in Azure Stack Hub gehostete Apps und Dienste anhand des Namens zu erreichen. Die DNS-Namensbezeichnung verwendet einen etwas anderen Namespace als die Infrastrukturendpunkte. Gemäß dem vorherigen Beispiel-Namespace sieht der Namespace für DNS-Namensbezeichnungen wie folgt aus:

`*.east.cloudapp.cloud.fabrikam.com`

Wenn ein Mandant daher den Wert **Myapp** im Feld für die DNS-Namensbezeichnung einer öffentlichen IP-Adresse angibt, wird auf dem externen DNS-Server von Azure Stack Hub ein A-Eintrag für **myapp** in der Zone **east.cloudapp.cloud.fabrikam.com** erstellt. Daraus ergibt sich der folgende voll qualifizierte Domänenname:

`myapp.east.cloudapp.cloud.fabrikam.com`

Wenn Sie diese Funktionalität nutzen und diesen Namespace verwenden möchten, müssen Sie die DNS-Server zum Hosten der externe DNS-Zone für Azure Stack Hub mit den DNS-Servern integrieren, die die ebenfalls zu verwendende übergeordnete Zone hosten. Dies ist ein anderer Namespace als der für die Azure Stack Hub-Dienstendpunkte verwendete Namespace. Daher müssen Sie eine zusätzliche Delegierung oder Regel für die bedingte Weiterleitung erstellen.

Weitere Informationen zur Funktionsweise der DNS-Namensbezeichnung finden Sie unter [Verwenden von DNS in Azure Stack Hub](../user/azure-stack-dns.md).

## <a name="resolution-and-delegation"></a>Auflösung und Delegierung

Es gibt zwei Arten von DNS-Server:

- Ein autoritativer DNS-Server hostet DNS-Zonen. Er antwortet nur auf DNS-Abfragen nach Einträgen für diese Zonen.
- Ein rekursiver DNS-Server hostet keine DNS-Zonen. Er reagiert auf alle DNS-Abfragen durch Aufrufen der autoritativen DNS-Server, um die benötigten Daten zu erfassen.

Azure Stack Hub enthält autoritative und rekursive DNS-Server. Die rekursiven Server werden verwendet, um alle Namen außer denen der internen privaten Zone und der externen öffentlichen DNS-Zone für diese Azure Stack Hub-Bereitstellung aufzulösen.

![DNS-Architektur von Azure Stack Hub](media/azure-stack-integrate-dns/Integrate-DNS-01.png)

## <a name="resolving-external-dns-names-from-azure-stack-hub"></a>Auflösen von externen DNS-Namen in Azure Stack Hub

Zum Auflösen von DNS-Namen für Endpunkte außerhalb von Azure Stack Hub (z. B. www\.bing.com) müssen Sie DNS-Server bereitstellen, die Azure Stack Hub für die Weiterleitung von DNS-Anforderungen verwenden kann, für die Azure Stack Hub nicht autoritativ ist. Für die Bereitstellung müssen im Arbeitsblatt der Bereitstellung die DNS-Server angegeben werden, an die Azure Stack Hub Anforderungen weiterleitet (im Feld „DNS-Weiterleitung“). Geben Sie für die Fehlertoleranz mindestens zwei Server in diesem Feld an. Ohne diese Werte kommt es bei der Azure Stack Hub-Bereitstellung zu Fehlern. Sie können die Werte für die DNS-Weiterleitung nach der Bereitstellung mit dem [**Set-AzSDnsForwarder**-Cmdlet](#editing-dns-forwarder-ips) bearbeiten. 



### <a name="configure-conditional-dns-forwarding"></a>Konfigurieren der bedingten DNS-Weiterleitung

> [!IMPORTANT]
> Diese Informationen gelten nur für eine AD FS-Bereitstellung.

Um die Namensauflösung mit Ihrer vorhandenen DNS-Infrastruktur zu ermöglichen, konfigurieren Sie eine bedingte Weiterleitung.

Zum Hinzufügen einer bedingten Weiterleitung müssen Sie den privilegierten Endpunkt verwenden.

Verwenden Sie für diesen Vorgang einen Computer in Ihrem Rechenzentrumsnetzwerk, der mit dem privilegierten Endpunkt in Azure Stack Hub kommunizieren kann.

1. Öffnen Sie eine Windows PowerShell-Sitzung mit erhöhten Rechten (Als Administrator ausführen), und stellen Sie eine Verbindung zur IP-Adresse des privilegierten Endpunkts her. Verwenden Sie die Anmeldeinformationen für die CloudAdmin-Authentifizierung.

   ```
   $cred=Get-Credential 
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $cred
   ```

2. Nachdem Sie eine Verbindung mit dem privilegierten Endpunkt hergestellt haben, führen Sie folgenden PowerShell-Befehl aus. Ersetzen Sie die bereitgestellten Beispielwerte durch Ihren Domänennamen und die IP-Adressen des DNS-Servers, den Sie verwenden möchten.

   ```
   Register-CustomDnsServer -CustomDomainName "contoso.com" -CustomDnsIPAddresses "192.168.1.1","192.168.1.2"
   ```

## <a name="resolving-azure-stack-hub-dns-names-from-outside-azure-stack-hub"></a>Auflösen von Azure Stack Hub-DNS-Namen von außerhalb von Azure Stack Hub
Die autoritativen Server enthalten die Informationen zu externen DNS-Zonen und zu allen vom Benutzer erstellten Zonen. Integrieren Sie diese Server, um die Zonendelegierung oder die bedingte Weiterleitung für das Auflösen von Azure Stack Hub-DNS-Namen von außerhalb von Azure Stack Hub zu aktivieren.

## <a name="get-dns-server-external-endpoint-information"></a>Erhalten der externen Endpunktinformationen des DNS-Servers

Sie benötigen folgende Informationen, um Ihre Azure Stack Hub-Bereitstellung in Ihre DNS-Infrastruktur zu integrieren:

- Vollqualifizierte Domänennamen des DNS-Servers
- IP-Adressen des DNS-Servers

Die vollqualifizierten Domänennamen für die Azure Stack Hub-DNS-Server weisen folgendes Format auf:

`<NAMINGPREFIX>-ns01.<REGION>.<EXTERNALDOMAINNAME>`

`<NAMINGPREFIX>-ns02.<REGION>.<EXTERNALDOMAINNAME>`

Wenn Sie die Beispielwerte verwenden, sind die vollqualifizierten Domänennamen für die DNS-Server Folgende:

`azs-ns01.east.cloud.fabrikam.com`

`azs-ns02.east.cloud.fabrikam.com`


Diese Informationen werden nach Abschluss aller Azure Stack Hub-Bereitstellungen in einer Datei namens `AzureStackStampInformation.json` erfasst. Diese Datei befindet sich im Ordner `C:\CloudDeployment\logs` des virtuellen Computers der Bereitstellung. Wenn Sie nicht sicher sind, welche Werte für Ihre Azure Stack Hub-Bereitstellung verwendet werden, können Sie die Werte hier nachsehen.

Wenn der virtuelle Computer für die Bereitstellung nicht mehr verfügbar ist oder nicht darauf zugegriffen werden kann, können Sie die Werte erhalten, indem Sie eine Verbindung mit dem privilegierten Endpunkt herstellen und das PowerShell-Cmdlet `Get-AzureStackStampInformation` ausführen. Weitere Informationen finden Sie unter [Privilegierter Endpunkt](azure-stack-privileged-endpoint.md).

## <a name="setting-up-conditional-forwarding-to-azure-stack-hub"></a>Einrichten der bedingten Weiterleitung an Azure Stack Hub

Der einfachste und sicherste Weg, um Azure Stack Hub in Ihre DNS-Infrastruktur zu integrieren, ist die bedingte Weiterleitung der Zone von dem Server aus, der die übergeordnete Zone hostet. Dieser Ansatz wird empfohlen, wenn Sie die direkte Kontrolle über die DNS-Server besitzen, die die übergeordnete Zone für Ihren externen DNS-Namespace von Azure Stack Hub hosten.

Wenn Sie nicht mit der bedingten Weiterleitung mit DNS vertraut sind, lesen Sie folgenden TechNet-Artikel: [Zuweisen einer bedingten Weiterleitung für einen Domänennamen](https://technet.microsoft.com/library/cc794735), oder die Dokumentation zu Ihrer DNS-Lösung.

In Szenarien, in denen Sie angegeben haben, dass Ihre externe Azure Stack Hub-DNS-Zone wie die untergeordnete Domäne des Namens Ihrer Unternehmensdomäne aussehen soll, kann die bedingte Weiterleitung nicht verwendet werden. Die DNS-Delegierung muss konfiguriert werden.

Beispiel:

- Name der DNS-Unternehmensdomäne: `contoso.com`
- Externer Azure Stack Hub-DNS-Domänenname: `azurestack.contoso.com`

## <a name="editing-dns-forwarder-ips"></a>Bearbeiten von IP-Adressen der DNS-Weiterleitung

IP-Adressen der DNS-Weiterleitung werden während der Bereitstellung von Azure Stack Hub festgelegt. Wenn die IP-Adressen der Weiterleitung jedoch aus irgendeinem Grund aktualisiert werden müssen, können Sie die Werte bearbeiten, indem Sie eine Verbindung mit dem privilegierten Endpunkt herstellen und die PowerShell-Cmdlets `Get-AzSDnsForwarder` und `Set-AzSDnsForwarder [[-IPAddress] <IPAddress[]>]` ausführen. Weitere Informationen finden Sie unter [Privilegierter Endpunkt](azure-stack-privileged-endpoint.md).

## <a name="delegating-the-external-dns-zone-to-azure-stack-hub"></a>Delegieren der externen DNS-Zone an Azure Stack Hub

Damit DNS-Namen von außerhalb einer Azure Stack Hub-Bereitstellung auflösbar sind, müssen Sie eine DNS-Delegierung einrichten.

Jede Registrierungsstelle hat seine eigenen DNS-Verwaltungstools, um die Namenservereinträge für eine Domäne zu ändern. Bearbeiten Sie auf der DNS-Verwaltungsseite der Registrierungsstelle die NS-Einträge, und ersetzen Sie die NS-Einträge für die Zone durch die in Azure Stack Hub erstellten Einträge.

Die meisten DNS-Registrierungsstellen erfordern, dass Sie mindestens zwei DNS-Server angeben, um die Delegierung abzuschließen.

## <a name="next-steps"></a>Nächste Schritte

[Firewallintegration](azure-stack-firewall.md)
