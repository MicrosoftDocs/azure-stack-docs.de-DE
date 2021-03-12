---
title: Konfigurieren von Firewalls für Azure Stack HCI
description: In diesem Thema erfahren Sie, wie Sie Firewalls für das Azure Stack HCI-Betriebssystem konfigurieren können.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 02/12/2021
ms.openlocfilehash: 28fd04d9fb84f612dca6b241b8935b8f9cbfe049
ms.sourcegitcommit: 7ee28fad5b8ba628b1a7dc3d82cabfc36aa62f0d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2021
ms.locfileid: "102250319"
---
# <a name="configure-firewalls-for-azure-stack-hci"></a>Konfigurieren von Firewalls für Azure Stack HCI

>Gilt für: Azure Stack HCI, Version 20H2

In diesem Thema erfahren Sie, wie Sie Firewalls für das Azure Stack HCI-Betriebssystem konfigurieren können. Außerdem erhalten Sie Informationen zu Konnektivitätsanforderungen, und es wird erläutert, wie Diensttags IP-Adressen in Azure gruppieren, auf die das Betriebssystem zugreifen muss. Im Artikel finden Sie außerdem die erforderlichen Schritte, mit denen Microsoft Defender Firewall aktualisiert werden kann.

## <a name="connectivity-requirements"></a>Konnektivitätsanforderungen
Azure Stack HCI muss regelmäßig eine Verbindung mit Azure herstellen. Der Zugriff ist auf Folgendes beschränkt:
- Bekannte Azure-IP-Adressen
- Ausgehender Datenverkehr
- Port 443 (HTTPS)

Weitere Informationen finden Sie im Abschnitt „Azure Stack HCI-Konnektivität“ des Artikels [Häufig gestellte Fragen zur Azure Stack HCI](../faq.yml).

In diesem Thema wird erläutert, wie Sie optional eine streng abgeriegelte Firewallkonfiguration verwenden, damit der gesamte Datenverkehr zu allen Zielen mit Ausnahmen derjenigen, die in der Liste „Zulassen“ aufgeführt sind, blockiert wird.

   >[!IMPORTANT]
   > Sollte die ausgehende Konnektivität durch Ihre externe Unternehmensfirewall oder Ihren Proxyserver eingeschränkt sein, stellen Sie sicher, dass die in der Tabelle unten aufgeführten URLs nicht blockiert werden. Weitere Informationen finden Sie im Abschnitt „Netzwerkkonfiguration“ des Artikels [Übersicht über den Agent für Azure Arc-fähige Server](/azure/azure-arc/servers/agent-overview#networking-configuration).


Wie unten gezeigt kann Azure Stack HCI mit mehr als einer Firewall auf Azure zugreifen.

:::image type="content" source="./media/configure-firewalls/firewalls-diagram.png" alt-text="Abbildung des Zugriffs auf Diensttagendpunkte durch Azure Stack HCI über Port 443 (HTTPS) der Firewalls" lightbox="./media/configure-firewalls/firewalls-diagram.png":::

## <a name="working-with-service-tags"></a>Verwenden von Diensttags
Ein *Diensttag* steht für eine Gruppe von IP-Adressen eines bestimmten Azure-Diensts. Microsoft verwaltet die im Diensttag eingeschlossenen IP-Adressen und aktualisiert das Diensttag automatisch, wenn sich IP-Adressen ändern, damit möglichst wenige Updates erforderlich sind. Weitere Informationen finden Sie unter [Diensttags in virtuellen Netzwerken](/azure/virtual-network/service-tags-overview).

## <a name="required-endpoint-daily-access-after-azure-registration"></a>Täglich erforderlicher Endpunktzugriff (nach der Azure-Registrierung)
Azure verwaltet bekannte IP-Adressen für Azure-Dienste mithilfe von Diensttags. Azure veröffentlicht wöchentlich eine JSON-Datei aller IP-Adresse für die einzelnen Dienste. Die IP-Adressen ändern sich zwar nicht oft, jedoch mehrmals im Jahr. In der folgenden Tabelle sehen Sie die Diensttagendpunkte, auf die das Betriebssystem zugreifen muss.

| BESCHREIBUNG                   | Diensttag für IP-Adressbereich  | URL                                                                                 |
| :-----------------------------| :-----------------------  | :---------------------------------------------------------------------------------- |
| Azure Active Directory        | AzureActiveDirectory      | `https://login.microsoftonline.com`<br> `https://graph.microsoft.com`               |
| Azure Resource Manager        | AzureResourceManager      | `https://management.azure.com`                        |
| Azure Stack HCI-Clouddienst | AzureFrontDoor.Frontend   | `https://azurestackhci.azurefd.net` |
| Azure Arc                     | AzureArcInfrastructure<br> AzureTrafficManager | Die URL hängt von der Funktionalität ab, die Sie verwenden möchten:<br> Hybrididentitätsdienst: `*.his.arc.azure.com`<br> Gastkonfiguration: `*.guestconfiguration.azure.com`<br> **Hinweis:** Da weitere Funktionen ermöglicht werden sollen, ist mit weiteren URLs zu rechnen. |

## <a name="update-microsoft-defender-firewall"></a>Aktualisieren von Microsoft Defender Firewall
In diesem Abschnitt erfahren Sie, wie Sie Microsoft Defender Firewall konfigurieren, sodass zugelassen wird, dass IP-Adressen, die einem Diensttag zugeordnet sind, eine Verbindung zum Betriebssystem herstellen können:

1. Laden Sie die JSON-Datei von der folgenden Ressource auf den Zielcomputer herunter, auf dem das Betriebssystem ausgeführt wird: [Azure-IP-Adressbereiche und -Diensttags – öffentliche Cloud](https://www.microsoft.com/download/details.aspx?id=56519).

1. Verwenden Sie den folgenden PowerShell-Befehl zum Öffnen der JSON-Datei:

    ```powershell
    $json = Get-Content -Path .\ServiceTags_Public_20201012.json | ConvertFrom-Json
    ```

1. Rufen Sie die Liste der IP-Adressbereiche für ein bestimmtes Diensttag ab, z. B. für das Diensttag „AzureResourceManager“:

    ```powershell
    $IpList = ($json.values | where Name -Eq "AzureResourceManager").properties.addressPrefixes
    ```

1. Importieren Sie die Liste der IP-Adressen in Ihre externe Unternehmensfirewall, wenn Sie eine dazugehörige Liste „Zulassen“ verwenden.

1. Erstellen Sie für jeden Server im Cluster eine Firewallregel, um ausgehenden Datenverkehr für Port 443 (HTTPS) für die Liste der IP-Adressbereiche zuzulassen:

    ```powershell
    New-NetFirewallRule -DisplayName "Allow Azure Resource Manager" -RemoteAddress $IpList -Direction Outbound -LocalPort 443 -Protocol TCP -Action Allow -Profile Any -Enabled True
    ```

## <a name="additional-endpoint-for-one-time-azure-registration"></a>Weitere Endpunkte für einmalige Azure-Registrierungen
Während des Azure-Registrierungsprozesses versucht das Cmdlet, wenn Sie entweder `Register-AzStackHCI` oder Windows Admin Center ausführen, den PowerShell-Katalog zu kontaktieren, um zu überprüfen, ob Sie über die aktuelle Version der erforderlichen PowerShell-Module verfügen, z. B. Az und AzureAD.

Obwohl der PowerShell-Katalog in Azure gehostet wird, verfügt er derzeit über kein entsprechendes Diensttag. Wenn Sie das `Register-AzStackHCI`-Cmdlet nicht auf einem Serverknoten ausführen können, da Sie keinen Zugriff auf das Internet haben, wird empfohlen, die Module auf Ihren Verwaltungscomputer herunterzuladen, und sie dann manuell auf den Serverknoten zu übertragen, auf dem Sie das Cmdlet ausführen möchten.

## <a name="set-up-a-proxy-server"></a>Einrichten eines Proxyservers
Führen Sie den folgenden PowerShell-Befehl als Administrator aus, um einen Proxyserver für Azure Stack HCI einzurichten:

```powershell
Set-WinInetProxy -ProxySettingsPerUser 0 -ProxyServer webproxy1.com:9090
```

Verwenden Sie das Flag `ProxySettingsPerUser 0`, damit die Proxykonfiguration serverweit statt benutzerbezogen (Standardeinstellung) angewendet wird. 

Laden Sie das Skript „WinInetProxy.psm1“ herunter: [PowerShell-Katalog | WinInetProxy.psm1 0.1.0](https://www.powershellgallery.com/packages/WinInetProxy/0.1.0/Content/WinInetProxy.psm1)

## <a name="network-port-requirements"></a>Anforderungen an Netzwerkports
Stellen Sie sicher, dass zwischen allen Serverknoten innerhalb eines Standorts und zwischen Standorten (bei Stretchingclustern) die richtigen Netzwerkports geöffnet sind. Sie benötigen geeignete Firewall- und Routerregeln zum Zulassen von ICMP- und SMB-Datenverkehr (Port 445 plus Port 5445 für SMB Direct) sowie von bidirektionalem WS-MAN-Datenverkehr (Port 5985) zwischen allen Servern im Cluster.

Wenn Sie zum Erstellen des Clusters den Assistenten für die Clustererstellung in Windows Admin Center verwenden, öffnet dieser automatisch auf jedem Server im Cluster die entsprechenden Firewallports für Failoverclustering, Hyper-V und Speicherreplikate. Wenn Sie auf den einzelnen Servern unterschiedliche Firewalls verwenden, öffnen Sie die folgenden Ports:

### <a name="failover-clustering-ports"></a>Ports für Failoverclustering
- ICMPv4 und ICMPv6
- TCP-Port 445
- Dynamische RPC-Ports
- TCP-Port 135
- TCP-Port 137
- TCP-Port 3343
- UDP-Port 3343

### <a name="hyper-v-ports"></a>Hyper-V-Ports
- TCP-Port 135
- TCP-Port 80 (HTTP-Konnektivität)
- TCP-Port 443 (HTTPS-Konnektivität)
- TCP-Port 6600
- TCP-Port 2179
- Dynamische RPC-Ports
- RPC-Endpunktzuordnung
- TCP-Port 445

### <a name="storage-replica-ports-stretched-cluster"></a>Ports für Speicherreplikate (Stretchingcluster)
- TCP-Port 445
- TCP 5445 (bei Verwendung von RDMA über iWARP)
- TCP-Port 5985
- ICMPv4 und ICMPv6 (bei Verwendung des `Test-SRTopology`-PowerShell-Cmdlets)

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie auch unter:
- Abschnitt zur Konnektivität im Artikel [Häufig gestellte Fragen zur Azure Stack HCI](../faq.yml)