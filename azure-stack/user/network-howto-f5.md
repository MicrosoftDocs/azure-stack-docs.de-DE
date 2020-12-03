---
title: Bereitstellen von F5 in zwei Azure Stack Hub-Instanzen
description: Hier erfahren Sie, wie Sie F5 in zwei Azure Stack Hub-Instanzen bereitstellen.
author: mattbriggs
ms.topic: how-to
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: b00d2a482faf2921e8823c705d8c8e861a763a78
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525624"
---
# <a name="how-to-deploy-f5-across-two-azure-stack-hub-instances"></a>Bereitstellen von F5 in zwei Azure Stack Hub-Instanzen

Dieser Artikel führt Sie durch die Einrichtung eines externen Lastenausgleichs in zwei Azure Stack Hub-Umgebungen. Mit dieser Konfiguration können Sie unterschiedliche Workloads verwalten. In diesem Artikel stellen Sie F5 als globale Lastenausgleichslösung in zwei unabhängigen Azure Stack Hub-Instanzen bereit. Außerdem stellen Sie eine Web-App mit Lastenausgleich, die auf einem NGINX-Server ausgeführt wird, in den beiden Instanzen bereit. Sie werden hinter einem Failover-Hochverfügbarkeitspaar von virtuellen F5-Geräten ausgeführt.

Die Azure Resource Manager-Vorlagen finden Sie im GitHub-Repository [f5-azurestack-gslb](https://github.com/Mikej81/f5-azurestack-gslb).

## <a name="overview-of-load-balancing-with-f5"></a>Übersicht über den Lastenausgleich mit F5

Die F5-Hardware (der Lastenausgleich) kann sich außerhalb von Azure Stack Hub und innerhalb des Rechenzentrums befinden, das Azure Stack Hub hostet. Azure Stack Hub verfügt nicht über eine native Funktion für den Lastenausgleich von Workloads in zwei separaten Azure Stack Hub-Bereitstellungen. Die BIG-IP Virtual Edition (VE) von F5 wird auf beiden Plattformen ausgeführt. Diese Konfiguration unterstützt die Parität zwischen Azure- und Azure Stack Hub-Architekturen durch Replikation der unterstützenden Anwendungsdienste. Sie können eine App in einer Umgebung entwickeln und in eine andere verschieben. Sie können auch die gesamte produktionsreife Azure Stack Hub-Umgebung spiegeln, einschließlich der BIG-IP-Konfigurationen, Richtlinien und Anwendungsdienste. Bei diesem Ansatz ist es nicht notwendig, die Anwendung zeitaufwändig umzugestalten und zu testen, sodass Sie sich ganz dem Schreiben von Code widmen können.

Das Schützen von Anwendungen und der zugehörigen Daten ist häufig ein Problem für Entwickler, die Apps in die Public Cloud verschieben. Das muss nicht so sein. Sie können eine App in Ihrer Azure Stack Hub-Umgebung erstellen, während ein Sicherheitsarchitekt die erforderlichen Einstellungen für die Web Application Firewall (WAF) von F5 konfiguriert. Der gesamte Stapel kann dann mit dem sicheren Wissen, dass die Anwendung durch die gleiche branchenführende WAF geschützt wird, in Azure Stack Hub repliziert werden. Bei identischen Richtlinien und Regelsätzen gibt es keine Sicherheitslücken oder -risiken, die andernfalls bei der Verwendung unterschiedlicher WAFs entstehen könnten.

Azure Stack Hub verfügt über einen eigenen Marketplace, der vom Azure Marketplace getrennt ist. Dort werden nur bestimmte Elemente hinzugefügt. In diesem Fall möchten Sie eine neue Ressourcengruppe in jeder Azure Stack Hub-Instanz erstellen und die bereits verfügbaren virtuellen F5-Geräte bereitstellen. Wie Sie feststellen werden, ist eine **öffentliche IP-Adresse** erforderlich, um Netzwerkverbindungen zwischen den beiden Azure Stack Hub-Instanzen zuzulassen. Im Grunde sind beide Instanzen „Inseln“, denen die **öffentliche IP-Adresse** die standortübergreifende Kommunikation ermöglicht.

## <a name="prerequisites-for-big-ip-ve"></a>Voraussetzungen für BIG-IP VE

-  Laden Sie **F5 BIG-IP VE – ALL (BYOL, 2 Boot Locations)** in jeden Azure Stack Hub-Marketplace herunter. Wenden Sie sich an Ihren Cloudbetreiber, falls sie nicht in Ihrem Portal verfügbar sind.

-  Die Azure Resource Manager-Vorlage finden Sie im folgenden GitHub-Repository: https://github.com/Mikej81/f5-azurestack-gslb.

## <a name="deploy-f5-big-ip-ve-on-each-instance"></a>Bereitstellen von F5 BIG-IP VE in jeder Instanz

Nehmen Sie die Bereitstellung in Azure Stack Hub-Instanz A und B vor.

1. Melden Sie sich beim Azure Stack Hub-Benutzerportal an.

2. Klicken Sie auf **+ Ressource erstellen**.

3. Geben Sie `F5` ein, um den Marketplace zu durchsuchen.

4. Wählen Sie **F5 BIG-IP VE – ALL (BYOL, 2 Boot Locations)** aus.

    ![Das Dialogfeld „Dashboard > Neu > Marketplace > Alles > F5 BIG-IP VE – ALL (BYOL, 2 Boot Locations)“ zeigt „f5“ im Suchfeld an. Das einzige Suchergebnis lautet „F5 BIG-IP VE – ALL (BYOL, 2 Boot Locations)“.](./media/network-howto-f5/image1.png)

5. Wählen Sie unten auf der nächsten Seite **Erstellen** aus.

    ![Das Dialogfeld „F5 BIG-IP VE – ALL (BYOL, 2 Boot Locations)“ enthält Informationen über BIG-IP VE und die Module, die Sie gemäß Ihrer Lizenz bereitstellen können. Es gibt eine Schaltfläche „Erstellen“.](./media/network-howto-f5/image2.png)

6. Erstellen Sie eine neue Ressourcengruppe namens **F5-GSLB**.

7. Verwenden Sie die folgenden Werte als Beispiel, um die Bereitstellung abzuschließen:

    ![Die Seite „Eingaben“ des Dialogfelds „Microsoft.Template“ zeigt 15 Textfelder, z. B. VIRTUALMACHINENNAME und ADMINUSERNAME, die Werte für eine Beispielbereitstellung enthalten.](./media/network-howto-f5/image3.png)

8. Überprüfen Sie, ob die Bereitstellung erfolgreich abgeschlossen wurde.

    ![Die Übersichtsseite des Dialogfelds „Microsoft.Template“ meldet „Ihre Bereitstellung wurde abgeschlossen“ und enthält Details zur Bereitstellung.](./media/network-howto-f5/image4.png)

    > [!NOTE]  
    > Jede BIG-IP-Bereitstellung dauert etwa 20 Minuten.

## <a name="configure-big-ip-appliances"></a>Konfigurieren der BIG-IP-Appliances

Führen Sie die folgenden Schritte für Azure Stack Hub A und B aus.

1. Melden Sie sich auf Azure Stack Hub-Instanz A beim Azure Stack Hub-Benutzerportal an, um die Ressourcen zu überprüfen, die bei der BIG-IP-Vorlagenbereitstellung erstellt wurden.

    ![Auf der Übersichtsseite des Dialogfelds „F5-GSLB“ werden die bereitgestellten Ressourcen und die zugehörigen Informationen aufgelistet.](./media/network-howto-f5/image18.png)

2. Folgen Sie den Anweisungen in der F5-Dokumentation zu den [BIG-IP-Konfigurationselementen](https://clouddocs.f5.com/training/community/dns/html/class1/class1.html). 

3. Konfigurieren Sie die „Wide IP List“ von BIG-IP, um in den beiden in Azure Stack Hub-Instanz A und B bereitgestellten Appliances zu lauschen. Anweisungen finden Sie in der Dokumentation zur [Big-IP-GTM-Konfiguration](https://techdocs.f5.com/kb/en-us/products/big-ip_gtm/manuals/product/gtm-concepts-11-5-0/4.html).


4. Überprüfen Sie das Failover der BIG-IP-Appliances. Konfigurieren Sie Ihre DNS-Server auf einem Testsystem wie folgt:
    - Azure Stack Hub-Instanz A = öffentliche IP-Adresse `f5stack1-ext`
    - Azure Stack Hub-Instanz B = öffentliche IP-Adresse `f5stack1-ext`

5. Navigieren Sie zu `www.contoso.com`. Der Browser lädt die NGINX-Standardseite.

## <a name="create-a-dns-sync-group"></a>Erstellen einer DNS-Synchronisierungsgruppe

1. Aktivieren Sie das Stammkonto, um eine Vertrauensstellung einzurichten. Folgen Sie den Anweisungen in der Dokumentation zum [Ändern der Kontokennwörter für die Systemwartung (11.x – 15.x)](https://support.f5.com/csp/article/K13121). Nachdem Sie die Vertrauensstellung (Zertifikataustausch) eingerichtet haben, deaktivieren Sie das Stammkonto.

1. Melden Sie sich bei BIG-IP an, und erstellen Sie eine DNS-Synchronisierungsgruppe. Anweisungen finden Sie in der Dokumentation zum [Erstellen einer BIG-IP-DNS-Synchronisierungsgruppe](https://f5-dns-automation-demo-12-1-x.readthedocs.io/en/latest/lab2/sync-group.html).

    > [!NOTE]  
    > Die lokale IP-Adresse der BIG-IP-Appliance finden Sie in der Ressourcengruppe **F5-GSLB**. Die Netzwerkschnittstelle ist „f5stack1-ext“, und Sie möchten eine Verbindung mit der öffentlichen oder privaten IP-Adresse (je nach Zugriff) herstellen.

    ![„DNS >> GSLB: Rechenzentren: Im Dialogfeld für die Liste der Rechenzentren werden Rechenzentren und deren Status aufgelistet. Es gibt Schaltflächen zum Aktivieren, Deaktivieren und Löschen, die auf ausgewählte Rechenzentren angewendet werden können.](./media/network-howto-f5/image5.png)
          
    ![„DNS >> GSLB: Server: Im Dialogfeld „Serverliste“ werden Server und ihr Status aufgelistet. Es gibt Schaltflächen zum Aktivieren, Deaktivieren, Löschen und Wiederherstellen der Verbindung, die auf ausgewählte Server angewendet werden können.](./media/network-howto-f5/image6.png)

1. Wählen Sie die neue Ressourcengruppe **F5-GSLB** und dann die VM **f5stack1** aus, und wählen Sie unter **Settings** (Einstellungen) die Option **Networking** (Netzwerk) aus.

## <a name="post-install-configurations"></a>Konfigurationen nach der Installation

Nach der Installation müssen Sie Ihre Azure Stack Hub-Netzwerksicherheitsgruppen (NSGs) konfigurieren und die Quell-IP-Adressen sperren.

1. Deaktivieren Sie Port 22, nachdem die Vertrauensstellung eingerichtet wurde.

2. Wenn Ihr System online ist, blockieren Sie die Quell-NSGs. Die Verwaltungs-NSG sollte an die Verwaltungsquelle gebunden werden und die externe NSG (4353/TCP) zur Synchronisierung an die andere Instanz. 443 sollte ebenfalls gesperrt werden, bis Anwendungen mit virtuellen Servern bereitgestellt werden.

3. Die Regel „GTM_DNS“ ist so konfiguriert, dass eingehender Datenverkehr über Port 53 (DNS-Datenverkehr) zugelassen wird, und der BIG-IP-Konfliktlöser wird einmal gestartet. Listener werden erstellt.

    ![Die Seite „fStack1-ext“ des Dialogfelds „Netzwerkschnittstelle“ zeigt Informationen zur Schnittstelle „fstack1-ext“ und zu deren Netzwerksicherheitsgruppe, „fstack1-ext-nsg“. Es gibt Registerkarten zur Auswahl der Anzeige der Regeln für eingehende Ports oder der Regeln für ausgehende Ports.](./media/network-howto-f5/image7.png)

4. Stellen Sie eine einfache Webanwendungsworkload in Ihrer Azure Stack Hub-Umgebung bereit, um einen Lastenausgleich hinter der BIG-IP-Instanz vorzunehmen. Ein Beispiel für die Verwendung des NGNIX-Servers finden Sie im Artikel zur [Bereitstellung von NGINX und NGINX Plus unter Docker](https://docs.nginx.com/nginx/admin-guide/installing-nginx/installing-nginx-docker/).

    > [!NOTE]  
    > Stellen Sie eine Instanz von NGNIX auf Azure Stack Hub A und Azure Stack Hub B bereit.

5. Nachdem NGNIX in einem Docker-Container auf einer Ubuntu-VM in jeder Azure Stack Hub-Instanz bereitgestellt wurde, überprüfen Sie, ob Sie die Standardwebseite auf den Servern erreichen können.

    ![Die Seite „Willkommen bei nginx!“ zeigt an, dass der nginx-Webserver erfolgreich installiert wurde und dass eine weitere Konfiguration erforderlich ist. Es gibt zwei Links, die zu unterstützenden Informationen führen.](./media/network-howto-f5/image8.png)

6. Melden Sie sich bei der Verwaltungsschnittstelle der BIG-IP-Appliance an. Verwenden Sie in diesem Beispiel die öffentliche IP-Adresse **f5-stack1-ext**.

    ![Der Anmeldebildschirm für das BIG-IP-Konfigurationsprogramm erfordert Benutzername und Kennwort.](./media/network-howto-f5/image9.png)

7. Veröffentlichen Sie den Zugriff auf NGINX über BIG-IP.
    
    -  In dieser Aufgabe konfigurieren Sie BIG-IP mit einem virtuellen Server und Pool, um eingehenden Internetzugriff auf die WordPress-Anwendung zuzulassen. Zuerst müssen Sie die private IP-Adresse für die NGINX-Instanz ermitteln.

8. Melden Sie sich beim Azure Stack Hub-Benutzerportal an. 

9. Wählen Sie Ihre NGINX-Netzwerkschnittstelle aus.

    ![Die Übersichtsseite des Dialogfensters „Dashboard > Ressourcengruppen > NGINX > ubuntu2673“ zeigt Informationen zur Netzwerkschnittstelle „ubuntu2673“ an.](./media/network-howto-f5/image10.png)

10. Wechseln Sie in der BIG-IP-Konsole zu **Local traffic > Pools > Pool List** (Lokaler Datenverkehr > Pools > Poolliste), und wählen Sie **+** aus. Konfigurieren Sie den Pool mit den Werten in der Tabelle. Übernehmen Sie bei allen anderen Feldern die Standardwerte.

    ![Der linke Bereich bietet die Möglichkeit, zur Erstellung eines neuen Pools zu navigieren. Der rechte Bereich trägt den Titel „Local Traffic >> Pools“ (Lokaler Datenverkehr >> Pools): Pool List >> New Pool“ (Poolliste >> Neuer Pool), und bietet die Möglichkeit, die Informationen zum neuen Pool anzugeben. Es gibt eine Schaltfläche „Finished“ (Abgeschlossen).](./media/network-howto-f5/image11.png)
    
    | Schlüssel | Wert |
    | --- | --- |
    | Name | NGINX_Pool |
    | Health Monitors (Integritätsüberwachungen) | HTTPS |
    | Node Name (Knotenname) | NGINX |
    | Adresse | \<your NGINX private IP address> |
    | Service Port (Dienstport) | 443 |

11. Wählen Sie **Finished** (Fertig) aus. Wenn Sie die Einstellungen richtig konfiguriert haben, wird der Poolstatus grün angezeigt.

    ![Der rechte Bereich trägt den Titel „Local Traffic >> Pools“ (Lokaler Datenverkehr >> Pools): Pool List“ (Poolliste), und der neu erstellte Pool ist der einzige Eintrag in der Liste.](./media/network-howto-f5/image12.png)

    Jetzt müssen Sie den virtuellen Server konfigurieren. Dazu müssen Sie zunächst die private IP-Adresse Ihrer F5 BIG-IP-Instanz ermitteln.

12. Wechseln Sie in der BIG-IP-Konsole zu **Network > Self IPs** (Netzwerk > Eigene IPs), und notieren Sie die IP-Adresse.

    ![Der linke Bereich bietet die Möglichkeit zum Anzeigen von „Self IPs“ (Eigene IPs) zu navigieren. Der rechte Bereich ist mit „Network >> Self IPs“ (Netzwerk >> Eigene IPs) betitelt. Zwei Eigene-IPs werden aufgelistet und die erste, „self_2nic“, ist hervorgehoben.](./media/network-howto-f5/image13.png)

13. Erstellen Sie einen virtuellen Server, indem Sie zu **Local Traffic** > **Virtual Servers** > **Virtual Server List** (Lokaler Datenverkehr > Virtuelle Server > Liste virtueller Server) navigieren und **+** auswählen. Konfigurieren Sie den Pool mit den Werten in der Tabelle. Übernehmen Sie bei allen anderen Feldern die Standardwerte.

    | Schlüssel | Wert |
    | --- | --- |
    |Name | NGINX |
    |Destination Address (Zieladresse) | \<Self IP address of the BIG-IP> |
    |Service Port (Dienstport) | 443 |
    |SSL Profile (Client) (SSL-Profil (Client)) | clientssl |
    |Source Address Translation (Quelladressenübersetzung) | Auto Map (Automatische Zuordnung) |
        
    ![Der linke Bereich wird verwendet, um im rechten Bereich zu „Local Traffic >> Virtual Servers: Virtual Server List >> NGINX“ (Lokaler Datenverkehr >> Virtuelle Server: Liste der virtuellen Server >> NGINX) zu navigieren, wo die erforderlichen Informationen eingegeben werden.](./media/network-howto-f5/image14.png)

    ![Diese Seite bietet die Möglichkeit, zusätzliche Informationen einzugeben. Es gibt die Schaltflächen zum Aktualisieren und Löschen.](./media/network-howto-f5/image15.png)

14. Damit haben Sie die BIG-IP-Konfiguration für die NGINX-Anwendung abgeschlossen. Um sicherzustellen, dass sie einwandfrei funktioniert, navigieren Sie zur Website, und überprüfen Sie die F5-Statistik.

15. Öffnen Sie in einem Browser `https://<F5-public-VIP-IP>`, und vergewissern Sie sich, dass Ihre NGINX-Standardseite angezeigt wird.

    ![Die Seite „Willkommen bei nginx!“ zeigt an, dass der nginx-Webserver erfolgreich installiert ist und dass eine weitere Konfiguration erforderlich ist. Es gibt zwei Links, die zu unterstützenden Informationen führen.](./media/network-howto-f5/image16.png)

16. Zeigen Sie dann die Statistik Ihres virtuellen Servers an, um den Datenverkehrsfluss zu überprüfen, indem Sie zu **Statistics > Module Statistics > Local Traffic** (Statistik > Modulstatistiken > Lokaler Datenverkehr) navigieren.

17. Wählen Sie unter **Statistics Type** (Statistiktyp) die Option **Virtual Servers** (Virtuelle Server) aus.

    ![Der linke Bereich hat den rechten Bereich zu „Statistics >> Module Statistics: Local Traffic >> Virtual Servers“ (Statistik >> Modulstatistik: Lokaler Datenverkehr >> Virtuelle Server) navigiert, und die Liste zeigt den virtuellen NGINX-Server und andere an. NGINX ist hervorgehoben.](./media/network-howto-f5/image17.png)


## <a name="for-more-information"></a>Weitere Informationen finden Sie unter

Referenzartikel zur Verwendung von F5 finden Sie hier:

- [Verfügbarkeitsdienste für Rechenzentren mit Big-IP DNS](https://clouddocs.f5.com/training/community/dns/html/class3/class3.html)
- [Bereitstellen des BIG-IP-Systems mit HTTP-Anwendungen](https://www.f5.com/content/dam/f5/corp/global/pdf/deployment-guides/iapp-http-dg.pdf)
- [Erstellen einer WIP (Wide IP) für GSLB](https://clouddocs.f5.com/training/community/big-iq-cloud-edition/html/class10/module2/lab1.html)

## <a name="next-steps"></a>Nächste Schritte

[Azure Stack Hub-Netzwerke: Unterschiede und Überlegungen](azure-stack-network-differences.md) 
