---
title: Verwenden von iDNs in Azure Stack Hub
description: Hier erfahren Sie, wie Sie iDNS-Features und -Funktionen in Azure Stack Hub verwenden.
author: Justinha
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: Justinha
ms.reviewer: scottnap
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 3cf7b6cadbefb32359e6104bd7b5a3c7851e73f7
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883121"
---
# <a name="use-idns-in-azure-stack-hub"></a>Verwenden von iDNs in Azure Stack Hub 

iDNS ist ein Netzwerkfeature von Azure Stack Hub, mit dem Sie externe DNS-Namen (beispielsweise „https:\//www.bing.com“) auflösen können. Zudem ermöglicht es Ihnen, interne virtuelle Netzwerknamen zu registrieren. Auf diese Weise können Sie virtuelle Computer im selben virtuellen Netzwerk nach dem Namen statt der IP-Adresse auflösen. Bei diesem Ansatz müssen keine benutzerdefinierten DNS-Servereinträge angegeben werden. Weitere Informationen zu DNS finden Sie unter [Azure DNS – Übersicht](https://docs.microsoft.com/azure/dns/dns-overview).

## <a name="what-does-idns-do"></a>Wozu dient iDNS?

Mit iDNS in Azure Stack Hub erhalten Sie die folgenden Funktionen, ohne benutzerdefinierte DNS-Servereinträge angeben zu müssen:

- Gemeinsam genutzte DNS-Namensauflösungsdienste für Mandantenworkloads.
- Autorisierender DNS-Dienst für Namensauflösung und DNS-Registrierung innerhalb des virtuellen Netzwerks des Mandanten.
- Rekursiver DNS-Dienst für die Auflösung von Internetnamen aus den Mandanten-VMs. Mandanten müssen nicht mehr benutzerdefinierte DNS-Einträge zum Auflösen von Internetnamen (z.B. www\.bing.com) angeben.

Sie können weiterhin Ihr eigenes DNS und benutzerdefinierte DNS-Server verwenden. Mit iDNS können Sie Internet-DNS-Namen auflösen und eine Verbindung mit anderen virtuellen Computern im gleichen virtuellen Netzwerk herstellen, ohne benutzerdefinierte DNS-Einträge erstellen zu müssen.

## <a name="what-doesnt-idns-do"></a>Was ist mit iDNS nicht möglich?

iDNS ermöglicht Ihnen nicht, einen DNS-Eintrag für einen Namen zu erstellen, der von außerhalb des virtuellen Netzwerks aufgelöst werden kann.

In Azure haben Sie die Option, eine DNS-Namensbezeichnung anzugeben, die einer öffentlichen IP-Adresse zugeordnet ist. Sie können die Bezeichnung (das Präfix) auswählen, aber Azure wählt das Suffix, das auf der Region basiert, in der Sie die öffentliche IP-Adresse erstellen.

![Beispiel für eine DNS-Namensbezeichnung](media/azure-stack-understanding-dns-in-tp2/image3.png)

In der Abbildung oben erstellt Azure einen A-Eintrag im DNS für die DNS-Namensbezeichnung, angegeben unter der Zone **westus.cloudapp.azure.com**. Das Präfix und Suffix werden kombiniert, um einen [vollqualifizierten Domänennamen](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (Fully Qualified Domain Name, FQDN) zu bilden, der von überall im öffentlichen Internet aufgelöst werden kann.

Azure Stack Hub unterstützt iDNS nur für die Registrierung interner Namen, sodass Folgendes nicht möglich ist:

- Erstellen eines DNS-Eintrags unter einer vorhandenen gehosteten DNS-Zone (z.B. „local.azurestack.external“).
- Erstellen einer DNS-Zone (z.B. „contoso.com“).
- Erstellen eines Eintrags unter Ihrer eigenen benutzerdefinierten DNS-Zone.
- Unterstützen des Erwerbs von Domänennamen.

## <a name="demo-of-how-idns-works"></a>Demo zur Funktionsweise von iDNS

Alle Hostnamen für virtuelle Computer in virtuellen Netzwerken werden als DNS-Ressourceneinträge in derselben Zone gespeichert, jedoch unter einem eigenen eindeutigen Depot, das als GUID definiert ist, die mit der VNET-ID in der SDN-Infrastruktur korreliert, für die die VM bereitgestellt wurde. Vollqualifizierte Domänennamen (Fully Qualified Domain Names, FQDNs) für Mandanten-VMs bestehen aus dem Computernamen und der DNS-Suffixzeichenfolge für das virtuelle Netzwerk im GUID-Format.

<!--- what does compartment mean? Add a screenshot? can we clarify what we mean by host name and computer name. the description doesn't match the example in the table.--->
 
Das folgende einfache Lab veranschaulicht, wie dies funktioniert. Wir haben 3 VMs in einem VNET und eine andere VM in einem separaten VNET erstellt:

<!--- Is DNS Label the right term? If so, we should define it. The column lists FQDNs, afaik. Where does the domain suffix come from? --->
 
|VM    |vNet    |Private IP-Adresse   |Öffentliche IP-Adresse    | DNS-Bezeichnung                                |
|------|--------|-------------|-------------|------------------------------------------|
|VM-A1 |VNetA   | 10.0.0.5    |172.31.12.68 |VM-A1-Label.lnv1.cloudapp.azscss.external |
|VM-A2 |VNetA   | 10.0.0.6    |172.31.12.76 |VM-A2-Label.lnv1.cloudapp.azscss.external |
|VM-A3 |VNetA   | 10.0.0.7    |172.31.12.49 |VM-A3-Label.lnv1.cloudapp.azscss.external |
|VM-B1 |VNetB   | 10.0.0.4    |172.31.12.57 |VM-B1-Label.lnv1.cloudapp.azscss.external |
 
 
|VNet  |GUID                                 |DNS-Suffixzeichenfolge                                                  |
|------|-------------------------------------|-------------------------------------------------------------------|
|VNetA |e71e1db5-0a38-460d-8539-705457a4cf75 |e71e1db5-0a38-460d-8539-705457a4cf75.internal.lnv1.azurestack.local|
|VNetB |e8a6e386-bc7a-43e1-a640-61591b5c76dd |e8a6e386-bc7a-43e1-a640-61591b5c76dd.internal.lnv1.azurestack.local|
 
 
Sie können einige Namensauflösungstests durchführen, um besser zu verstehen, wie iDNS funktioniert:

<!--- why Linux?--->

Von VM-A1 (Linux-VM): Suchen von VM-A2. Sie können sehen, dass das DNS-Suffix für VNetA hinzugefügt und der Name in die private IP-Adresse aufgelöst wird:
 
```console
carlos@VM-A1:~$ nslookup VM-A2
Server:         127.0.0.53
Address:        127.0.0.53#53
 
Non-authoritative answer:
Name:   VM-A2.e71e1db5-0a38-460d-8539-705457a4cf75.internal.lnv1.azurestack.local
Address: 10.0.0.6
```
 
Beim Suchen von VM-A2-Label ohne Angabe des FQDN tritt wie erwartet ein Fehler auf:

```console 
carlos@VM-A1:~$ nslookup VM-A2-Label
Server:         127.0.0.53
Address:        127.0.0.53#53
 
** server can't find VM-A2-Label: SERVFAIL
```

Wenn Sie den FQDN für die DNS-Bezeichnung angeben, wird der Name in die öffentliche IP-Adresse aufgelöst:

```console
carlos@VM-A1:~$ nslookup VM-A2-Label.lnv1.cloudapp.azscss.external
Server:         127.0.0.53
Address:        127.0.0.53#53
 
Non-authoritative answer:
Name:   VM-A2-Label.lnv1.cloudapp.azscss.external
Address: 172.31.12.76
```
 
Beim Versuch, VM-B1 (aus einem anderen VNET) aufzulösen, tritt ein Fehler auf, da dieser Eintrag in dieser Zone nicht vorhanden ist.

```console
carlos@caalcobi-vm4:~$ nslookup VM-B1
Server:         127.0.0.53
Address:        127.0.0.53#53
 
** server can't find VM-B1: SERVFAIL
```

Die Verwendung des FQDN für VM-B1 ist nicht hilfreich, da dieser Eintrag aus einer anderen Zone stammt.

```console 
carlos@VM-A1:~$ nslookup VM-B1.e8a6e386-bc7a-43e1-a640-61591b5c76dd.internal.lnv1.azurestack.local
Server:         127.0.0.53
Address:        127.0.0.53#53
 
** server can't find VM-B1.e8a6e386-bc7a-43e1-a640-61591b5c76dd.internal.lnv1.azurestack.local: SERVFAIL
```
 
Wenn Sie den FQDN für die DNS-Bezeichnung verwenden, wird sie erfolgreich aufgelöst:

``` 
carlos@VM-A1:~$ nslookup VM-B1-Label.lnv1.cloudapp.azscss.external
Server:         127.0.0.53
Address:        127.0.0.53#53
 
Non-authoritative answer:
Name:   VM-B1-Label.lnv1.cloudapp.azscss.external
Address: 172.31.12.57
```
 
Von VM-A3 (Windows-VM). Beachten Sie den Unterschied zwischen autorisierenden und nicht autorisierenden Antworten.

Interne Einträge:

```console
C:\Users\carlos>nslookup
Default Server:  UnKnown
Address:  168.63.129.16
 
> VM-A2
Server:  UnKnown
Address:  168.63.129.16
 
Name:    VM-A2.e71e1db5-0a38-460d-8539-705457ª4cf75.internal.lnv1.azurestack.local
Address:  10.0.0.6
```

Externe Einträge:

```console
> VM-A2-Label.lnv1.cloudapp.azscss.external
Server:  UnKnown
Address:  168.63.129.16
 
Non-authoritative answer:
Name:    VM-A2-Label.lnv1.cloudapp.azscss.external
Address:  172.31.12.76
``` 
 
Kurz gesagt, können Sie dem oben stehenden Folgendes entnehmen:
 
*   Jedes VNET verfügt über eine eigene Zone, die A-Einträge für alle privaten IP-Adressen enthält, bestehend aus dem VM-Namen und dem DNS-Suffix des VNET (was seine GUID ist).
    *   \<vmname>.\<vnetGUID\>.internal.\<region>.\<stackinternalFQDN>
    *   Dies erfolgt automatisch.
*   Wenn Sie öffentliche IP-Adressen verwenden, können Sie auch DNS-Bezeichnungen dafür erstellen. Diese werden wie jede andere externe Adresse aufgelöst.
 
 
- iDNS-Server sind die autorisierenden Server für ihre internen DNS-Zonen und fungieren auch als Konfliktlöser für öffentliche Namen, wenn Mandanten-VMs versuchen, eine Verbindung mit externen Ressourcen herzustellen. Wenn eine Abfrage für eine externe Ressource vorliegt, leiten iDNS-Server die Anforderung zur Auflösung an autorisierende DNS-Server weiter.
 
Die Labergebnisse zeigen, dass Sie bestimmen können, welche IP-Adresse verwendet wird. Wenn Sie den VM-Namen verwenden, erhalten Sie die private IP-Adresse. Wenn Sie die DNS-Bezeichnung verwenden, erhalten Sie die öffentliche IP-Adresse.

## <a name="next-steps"></a>Nächste Schritte

[Verwenden von DNS in Azure Stack Hub](azure-stack-dns.md)
