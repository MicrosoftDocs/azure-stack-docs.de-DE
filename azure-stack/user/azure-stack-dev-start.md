---
title: Einrichten einer Entwicklungsumgebung in Azure Stack Hub
description: Hier finden Sie Informationen zu den ersten Schritten bei der Entwicklung von Anwendungen für Azure Stack Hub.
author: mattbriggs
ms.topic: overview
ms.date: 04/20/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: d86797e7239b32e779567942b2c53c1ae58d94c8
ms.sourcegitcommit: 32834e69ef7a804c873fd1de4377d4fa3cc60fb6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81660999"
---
# <a name="set-up-a-development-environment-in-azure-stack-hub"></a>Einrichten einer Entwicklungsumgebung in Azure Stack Hub 

Sie können Anwendungen für Azure Stack Hub auf einer Windows 10-, Linux- oder macOS-Arbeitsstation entwickeln. In diesem Artikel wird Folgendes erläutert: 

- Die verschiedenen Kontexte, in denen Ihre App in Azure Stack Hub ausgeführt wird 
- Die Schritte, die Sie zum Einrichten einer Windows 10-, Linux- oder macOS-Arbeitsstation ausführen müssen 
- Die Schritte zum Erstellen von Ressourcen in Azure Stack Hub und Bereitstellen in einer App 

## <a name="azure-stack-hub-context-and-your-code"></a>Azure Stack Hub-Kontext und Ihr Code 

Sie können Skripts und Apps schreiben, um viele Aufgaben in Azure Stack Hub auszuführen. Allerdings ist es hilfreich, den Umfang auf die folgenden drei Modi zu beschränken: 

1. Im ersten Modus können Sie Apps erstellen, die Ressourcen mithilfe von Azure Resource Manager-Vorlagen in Azure Stack Hub bereitstellen. Sie können z. B. ein Skript schreiben, das eine Azure Resource Manager-Vorlage erstellt. Diese Vorlage wiederum erstellt ein virtuelles Netzwerk und die virtuellen Computer, die Ihre Apps hosten sollen. 

2. Im zweiten Modus arbeiten Sie über die REST-API und einen REST-Client direkt mit Endpunkten. Diese haben Sie in Ihrem Code erstellt. In diesem Modus können Sie ein Skript schreiben, das ein virtuelles Netzwerk und die virtuellen Computer erstellt. Dazu werden Anforderungen an die APIs gesendet. 

3. Im dritten Modus können Sie Ihren Code verwenden, um eine App zu erstellen, die in Azure Stack Hub gehostet wird. Wenn Sie die Infrastruktur in Azure Stack Hub zum Hosten Ihrer App erstellt haben, stellen Sie Ihre App in der Infrastruktur bereit. In der Regel bereiten Sie zuerst die Umgebung vor und stellen dann die App dort bereit. 

###  <a name="infrastructure-as-a-service-and-platform-as-a-service"></a>Infrastructure-as-a-Service und Platform-as-a-Service 

Als Cloudplattform unterstützt Azure Stack Hub beide Konzepte: 

- Infrastructure-as-a-Service (IaaS) 
- Platform-as-a-Service (PaaS) 

Sowohl IaaS als auch PaaS stellen Informationen zu Einrichten Ihres Entwicklungscomputers bereit. 

IaaS ist die Virtualisierung der Bestandteile eines Rechenzentrums: Netzwerkkomponenten, Netzwerk und Server. Wenn Sie eine App auf einem virtuellen Computer bereitstellen, der den Webserver hostet, arbeiten Sie mit einem IaaS-Modell. Bei diesem Modell verwaltet Azure Stack Hub die virtuellen Elemente, und Ihre App befindet sich auf einem virtuellen Server. Azure Stack Hub-Ressourcenanbieter unterstützen Netzwerkkomponenten und virtuelle Server. 

PaaS abstrahiert die Infrastrukturebene, sodass Sie Ihre App auf einem Endpunkt bereitstellen, der die App dann ausführt. In diesem PaaS-Modell können Sie Container verwenden, die Ihre App hosten, und dann die containerbasierte App in einem Dienst bereitstellen, der die Container ausgeführt. Oder Sie stellen Ihre App per Push direkt in einem Dienst bereit, der die App ausführt. Sie können Azure Stack Hub zum Ausführen von Azure App Service und Kubernetes verwenden. 

### <a name="azure-stack-hub-resource-manager"></a>Azure Stack Hub: Resource Manager 

Die drei zuvor erwähnten Modi sowie PaaS bzw. IaaS werden durch die Azure Stack Hub-Version von Azure Resource Manager ermöglicht. In diesem Verwaltungsframework können Sie Azure Stack Hub-Ressourcen bereitstellen, verwalten und überwachen. Sie können mit den Ressourcen als Gruppe in einem einzigen Vorgang arbeiten. Weitere Informationen zum Arbeiten mit Azure Stack Hub Resource Manager finden Sie unter [Verwalten von API-Versionsprofilen in Azure Stack](azure-stack-version-profiles.md). 

### <a name="azure-stack-hub-sdks"></a>Azure Stack Hub SDKs 

Azure Stack Hub verwendet eine Azure Stack Hub-Version von Azure Resource Manager. Um Sie bei der Arbeit mit dem Code Ihrer Wahl in Azure Stack Hub Resource Manager zu unterstützen, haben wir eine Reihe von SDKs bereitgestellt: 

- [.NET/C#](azure-stack-version-profiles-net.md)
- [Java](azure-stack-version-profiles-java.md)
- [Go](azure-stack-version-profiles-go.md)
- [Ruby](azure-stack-version-profiles-ruby.md)
- [Python](azure-stack-version-profiles-python.md)
- [Node.js](azure-stack-version-profile-nodejs.md)

## <a name="before-you-start"></a>Vorbereitung 

Zum Einrichten Ihrer Umgebung benötigen Sie: 

- Zugriff auf das Azure Stack Hub-Benutzerportal 
- Den Namen Ihres Mandanten. 
- Sie müssen ermitteln, ob Sie Ihre Identitäten mit Azure Active Directory (Azure AD) oder Active Directory-Verbunddiensten (AD FS) verwalten. 

Wenn Sie Fragen zu Azure Stack Hub haben, wenden Sie sich an Ihren Cloudbetreiber. 

## <a name="windows-10"></a>Windows 10 

Wenn Sie einen Windows 10-Computer verwenden, können Sie mit PowerShell 5.0 und Visual Studio arbeiten. Und wenn Sie mit einem Azure Stack Development Kit (ASDK) arbeiten, können Sie eine VPN-Verbindung mit Ihrer Umgebung herstellen. 

### <a name="set-up-your-tools"></a>Einrichten der Tools 

1. Richten Sie PowerShell ein. Weitere Informationen finden Sie unter [Installieren von Azure Stack Hub PowerShell](../operator/azure-stack-powershell-install.md). 

2. Laden Sie Azure Stack Hub-Tools herunter. Anweisungen dazu finden Sie unter [Herunterladen von Azure Stack Hub-Tools von GitHub](../operator/azure-stack-powershell-download.md). 

3. Wenn Sie ein ASDK verwenden, installieren und konfigurieren Sie eine [VPN-Verbindung mit Azure Stack Hub](azure-stack-connect-azure-stack.md#connect-to-azure-stack-hub-with-vpn). 

4. Installieren und konfigurieren Sie die Azure-Befehlszeilenschnittstelle. Anweisungen hierzu finden Sie unter [Verwenden von API-Versionsprofilen mit der Azure CLI in Azure Stack Hub](azure-stack-version-profiles-azurecli2.md). 

5. Installieren und konfigurieren Sie den Azure Storage-Explorer. Beim Storage-Explorer handelt es sich um eine eigenständige App, über die Sie mit Azure Stack Hub-Speicherdaten arbeiten können. Weitere Informationen finden Sie unter [Herstellen einer Verbindung von Storage-Explorer mit einem Azure Stack Hub-Abonnement oder -Speicherkonto](azure-stack-storage-connect-se.md). 

### <a name="install-your-integrated-development-environment"></a>Installieren Ihrer integrierten Entwicklungsumgebung 

1. Installieren Sie Ihre integrierte Entwicklungsumgebung (IDE), je nach Codebasis und Präferenzen. 

     - Visual Studio Code (Python, GO, NodeJS). Laden Sie Visual Studio Code für Ihren Computer von [code.visualstudio.com](https://code.visualstudio.com/Download) herunter. 
     - Visual Studio (.NET/C#). Laden Sie die Visual Studio Community-Edition von [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/) herunter. 
     - Eclipse (Java). Laden Sie Eclipse von [eclipse.org](https://www.eclipse.org/downloads/) herunter. 

2. Installieren Sie das SDK für Ihren Code: 

     - [.NET/C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="linux"></a>Linux 

Wenn Sie einen Linux-Computer verwenden, können Sie mit der Azure-Befehlszeilenschnittstelle, Visual Studio Code oder in Ihrer bevorzugten integrierten Entwicklungsumgebung arbeiten. 

> [!Note]   
> Wenn Sie einen Linux-Computer mit dem ASDK verwenden, muss sich Ihr Remotecomputer im gleichen Netzwerk befinden wie das ASDK. Sie können keine Verbindung über ein VPN herstellen. 

### <a name="set-up-your-tools"></a>Einrichten der Tools 

1. Installieren und konfigurieren Sie die Azure-Befehlszeilenschnittstelle. Anweisungen hierzu finden Sie unter [Verwenden von API-Versionsprofilen mit der Azure CLI in Azure Stack Hub](azure-stack-version-profiles-azurecli2.md). 

2. Installieren und konfigurieren Sie den Azure Storage-Explorer. Beim Storage-Explorer handelt es sich um eine eigenständige App, über die Sie mit Azure Stack Hub-Speicherdaten arbeiten können. Weitere Informationen finden Sie unter [Herstellen einer Verbindung von Storage-Explorer mit einem Azure Stack Hub-Abonnement oder -Speicherkonto](azure-stack-storage-connect-se.md). 

### <a name="install-your-integrated-development-environment"></a>Installieren Ihrer integrierten Entwicklungsumgebung 

1. Installieren Sie Ihre integrierte Entwicklungsumgebung (IDE), je nach Codebasis und Präferenzen. 

     - Visual Studio Code (Python, GO, NodeJS). Laden Sie Visual Studio Code für Ihren Computer von [code.visualstudio.com](https://code.visualstudio.com/Download) herunter. 
     - Visual Studio (.NET/C#). Laden Sie die Visual Studio Community-Edition von [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/) herunter. 
     - Eclipse (Java). Laden Sie Eclipse von [eclipse.org](https://www.eclipse.org/downloads/) herunter. 

2. Installieren Sie das SDK für Ihren Code: 

     - [.NET/C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="macos"></a>macOS 

Auf einem macOS-Computer können Sie mit der Azure CLI und Visual Studio Code oder in Ihrer bevorzugten integrierten Entwicklungsumgebung arbeiten. 

> [!Note]   
> Wenn Sie einen macOS-Computer mit dem ASDK verwenden, muss sich Ihr Remotecomputer im gleichen Netzwerk befinden wie das ASDK. Sie können keine Verbindung über ein VPN herstellen. 

### <a name="set-up-your-tools"></a>Einrichten der Tools 

1. Installieren und konfigurieren Sie die Azure-Befehlszeilenschnittstelle. Anweisungen hierzu finden Sie unter [Verwenden von API-Versionsprofilen mit der Azure CLI in Azure Stack Hub](azure-stack-version-profiles-azurecli2.md). 

2. Installieren und konfigurieren Sie den Azure Storage-Explorer. Beim Storage-Explorer handelt es sich um eine eigenständige App, über die Sie mit Azure Stack Hub-Speicherdaten arbeiten können. Weitere Informationen finden Sie unter [Herstellen einer Verbindung von Storage-Explorer mit einem Azure Stack Hub-Abonnement oder -Speicherkonto](azure-stack-storage-connect-se.md). 

### <a name="install-your-integrated-development-environment"></a>Installieren Ihrer integrierten Entwicklungsumgebung 

1. Installieren Sie Ihre integrierte Entwicklungsumgebung (IDE), je nach Codebasis und Präferenzen. 

     - Visual Studio Code (Python, GO, NodeJS). Laden Sie Visual Studio Code für Ihren Computer von [code.visualstudio.com](https://code.visualstudio.com/Download) herunter. 
     - Visual Studio (.NET/C#). Laden Sie die Visual Studio Community-Edition von [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/) herunter. 
     - Eclipse (Java). Laden Sie Eclipse von [eclipse.org](https://www.eclipse.org/downloads/) herunter. 

2. Installieren Sie das SDK für Ihren Code: 

     - [.NET/C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md)
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="next-steps"></a>Nächste Schritte 

Weitere Informationen zum Bereitstellen einer App auf Ressourcen in Azure Stack Hub finden Sie unter [Häufige Bereitstellungen für Azure Stack Hub](azure-stack-dev-start-deploy-app.md).
