---
title: Konfigurieren von Firewalls für Azure Stack HCI
description: In diesem Thema erfahren Sie, wie Sie Firewalls für das Azure Stack HCI-Betriebssystem konfigurieren können.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 01/06/2020
ms.openlocfilehash: a67881f2dd4be5e4dce5fb967c88484c27025624
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98255230"
---
# <a name="configure-firewalls-for-azure-stack-hci"></a>Konfigurieren von Firewalls für Azure Stack HCI

>Gilt für: Azure Stack HCI, Version 20H2

In diesem Thema erfahren Sie, wie Sie Firewalls für das Azure Stack HCI-Betriebssystem konfigurieren können. Außerdem erhalten Sie Informationen zu Konnektivitätsanforderungen, und es wird erläutert, wie Diensttags IP-Adressen in Azure gruppieren, auf die das Betriebssystem zugreifen muss. Im Artikel finden Sie außerdem die erforderlichen Schritte, mit denen Microsoft Defender Firewall aktualisiert werden kann.

## <a name="connectivity-requirements"></a>Konnektivitätsanforderungen
Azure Stack HCI muss regelmäßig eine Verbindung mit Azure herstellen. Der Zugriff ist auf Folgendes beschränkt:
- Bekannte Azure-IP-Adressen
- Ausgehender Datenverkehr
- Port 443 (HTTPS)

Weitere Informationen finden Sie im Abschnitt „Azure Stack HCI-Konnektivität“ des Artikels [Häufig gestellte Fragen zur Azure Stack HCI](../faq.md).

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
Während des Azure-Registrierungsprozesses versucht das Cmdlet, wenn Sie entweder `Register-AzStackHCI` oder Windows Admin Center ausführen, den PowerShell-Katalog zu kontaktieren, um zu überprüfen, ob Sie über die aktuelle Version der erforderlichen PowerShell-Module verfügen, z. B. Az und AzureAD. Obwohl der PowerShell-Katalog in Azure gehostet wird, verfügt er derzeit über kein entsprechendes Diensttag. Wenn Sie das `Register-AzStackHCI`-Cmdlet nicht auf einem Serverknoten ausführen können, da Sie keinen Zugriff auf das Internet haben, wird empfohlen, die Module auf Ihren Verwaltungscomputer herunterzuladen, und sie dann manuell auf den Serverknoten zu übertragen, auf dem Sie das Cmdlet ausführen möchten.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie auch unter:
- Abschnitt zur Konnektivität im Artikel [Häufig gestellte Fragen zur Azure Stack HCI](../faq.md)