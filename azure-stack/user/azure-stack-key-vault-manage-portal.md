---
title: Verwalten von Key Vault in Azure Stack über das Portal | Microsoft Docs
description: Hier erfahren Sie, wie Sie Key Vault in Azure Stack mithilfe des Portals verwalten.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: D4300668-461F-45F6-BF3B-33B502C39D17
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 01/04/2019
ms.date: 02/18/2019
ms.author: v-jay
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: bc73e80acddaac8676b4cb47c0b9ced2467cdc44
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "64311292"
---
# <a name="manage-key-vault-in-azure-stack-by-using-the-portal"></a>Verwalten von Key Vault in Azure Stack über das Portal

Sie können Key Vault in Azure Stack über das Azure Stack-Portal verwalten. In diesem Artikel wird beschrieben, wie Sie einen Schlüsseltresor in Azure Stack erstellen und verwalten.

## <a name="prerequisites"></a>Voraussetzungen

Sie müssen ein Angebot abonnieren, das den Azure Key Vault-Dienst umfasst.

## <a name="create-a-key-vault"></a>Erstellen eines Schlüsseltresors

1. Melden Sie sich am [Benutzerportal](https://portal.local.azurestack.external) an.

2. Wählen Sie im Dashboard **+ Ressource erstellen**, **Sicherheit + Identität** und anschließend **Schlüsseltresor** aus.

    ![Key Vault-Bildschirm](media/azure-stack-key-vault-manage-portal/image1.png)

3. Weisen Sie Ihrem Tresor im Bereich **Schlüsseltresor erstellen** unter **Name** einen Namen zu. Tresornamen dürfen nur alphanumerische Zeichen und Bindestriche (-) enthalten. Sie sollten nicht mit einer Zahl beginnen.

4. Wählen Sie aus der Liste der verfügbaren Abonnements ein **Abonnement** aus. Die Dropdownliste enthält alle Abonnements, für die der Key Vault-Dienst zur Verfügung steht.

5. Wählen Sie eine vorhandene **Ressourcengruppe** aus, oder erstellen Sie eine neue.

6. Wählen Sie den **Tarif** aus. Im Azure Stack Development Kit (ASDK) unterstützen Schlüsseltresore nur **Standard**-SKUs.

7. Wählen Sie eine der vorhandenen **Zugriffsrichtlinien** aus, oder erstellen Sie eine neue Richtlinie. Mithilfe einer Zugriffsrichtlinie können Sie einen Benutzer, eine Anwendung oder eine Sicherheitsgruppe dazu berechtigen, Vorgänge mit diesem Tresor auszuführen.

8. Wählen Sie optional eine **erweiterte Zugriffsrichtlinie** aus, um den Zugriff auf Features zu ermöglichen. Beispiele: virtuelle Computer (virtual machines, VMs) für die Bereitstellung, Resource Manager für die Vorlagenbereitstellung sowie Zugriff auf Azure Disk Encryption für die Volumeverschlüsselung.

9. Nachdem Sie die Einstellungen konfiguriert haben, wählen Sie **OK** aus, und wählen Sie dann **Erstellen** aus. Daraufhin wird die Schlüsseltresorbereitstellung gestartet.

## <a name="manage-keys-and-secrets"></a>Verwalten von Schlüsseln und Geheimnissen

Gehen Sie nach dem Erstellen eines Tresors wie folgt vor, um Schlüssel und Geheimnisse innerhalb des Tresors zu erstellen und zu verwalten.

### <a name="create-a-key"></a>Erstellen eines Schlüssels

1. Melden Sie sich am [Benutzerportal](https://portal.local.azurestack.external) an.

2. Wählen Sie im Dashboard **Alle Ressourcen** aus, wählen Sie den zuvor erstellten Schlüsseltresor aus, und klicken Sie dann auf die Kachel **Schlüssel**.

3. Wählen Sie im Bereich **Schlüssel** die Option **Hinzufügen** aus.

4. Wählen Sie im Bereich **Schlüssel erstellen** in der Liste mit den **Optionen** die gewünschte Schlüsselerstellungsmethode aus. Sie können einen neuen Schlüssel **generieren**, einen vorhandenen Schlüssel **hochladen** oder **Sicherung wiederherstellen** verwenden, um eine Sicherung eines Schlüssels auszuwählen.

5. Geben Sie einen **Namen** für Ihren Schlüssel ein. Schlüsselnamen dürfen nur alphanumerische Zeichen und Bindestriche (-) enthalten.

6. Konfigurieren Sie optional Werte für **Aktivierungsdatum festlegen** und **Ablaufdatum festlegen** für Ihren Schlüssel.

7. Wählen Sie **Erstellen** aus, um die Bereitstellung zu starten.

Nach erfolgreicher Erstellung des Schlüssels können Sie ihn unter **Schlüssel** auswählen und seine Eigenschaften anzeigen oder ändern. Der Abschnitt „Eigenschaften“ enthält den **Schlüsselbezeichner**. Hierbei handelt es sich um einen URI (Uniform Resource Identifier), über den externe Anwendungen auf diesen Schlüssel zugreifen. Konfigurieren Sie Einstellungen unter **Zulässige Vorgänge**, um Vorgänge für diesen Schlüssel einzuschränken.

![URI (Schlüssel)](media/azure-stack-key-vault-manage-portal/image4.png)

### <a name="create-a-secret"></a>Erstellen eines Geheimnisses

1. Melden Sie sich am [Benutzerportal](https://portal.local.azurestack.external) an.

2. Wählen Sie im Dashboard **Alle Ressourcen** aus, wählen Sie den zuvor erstellten Schlüsseltresor aus, und klicken Sie dann auf die Kachel **Geheimnisse**.

3. Wählen Sie unter **Geheimnisse** die Option **Hinzufügen** aus.

4. Wählen Sie unter **Geheimnis erstellen** in der Liste **Uploadoptionen** die gewünschte Option für die Geheimniserstellung aus. Sie können ein Geheimnis **manuell** erstellen, indem Sie einen Wert für das Geheimnis eingeben, oder ein **Zertifikat** von Ihrem lokalen Computer hochladen.

5. Geben Sie einen **Namen** für das Geheimnis ein. Geheimnisnamen dürfen nur alphanumerische Zeichen und Bindestriche (-) enthalten.

6. Geben Sie optional den **Inhaltstyp** an, und konfigurieren Sie Werte für **Aktivierungsdatum festlegen** und **Ablaufdatum festlegen** für das Geheimnis.

7. Wählen Sie **Erstellen** aus, um die Bereitstellung zu starten.

Nach erfolgreicher Erstellung des Geheimnisses können Sie es unter **Geheimnisse** auswählen und seine Eigenschaften anzeigen oder ändern. Die **Geheimnis-ID** ist ein URI, mit dem externe Anwendungen auf dieses Geheimnis zugreifen können.

![URI (Geheimnis)](media/azure-stack-key-vault-manage-portal/image5.png)

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen eines virtuellen Computers durch Abrufen des in Key Vault gespeicherten Kennworts](azure-stack-key-vault-deploy-vm-with-secret.md)
* [Create a virtual machine and include certificate retrieved from a key vault](azure-stack-key-vault-push-secret-into-vm.md) (Erstellen eines virtuellen Computers und Einbeziehen eines Zertifikats aus einem Schlüsseltresor)
