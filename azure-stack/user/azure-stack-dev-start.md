---
title: Einrichten einer Entwicklungsumgebung in Azure Stack | Microsoft-Dokumentation
description: Erste Schritte bei der Entwicklung von Anwendungen für Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/25/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 9b3d58e67d7c6ca7016b3ecb51c09171aae7c06a
ms.sourcegitcommit: 0d8ccf2a32b08ab9bcbe13d54c7c3dce2379757f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/25/2019
ms.locfileid: "64490032"
---
# <a name="set-up-a-development-environment-on-azure-stack"></a>Einrichten einer Entwicklungsumgebung in Azure Stack 

Sie können Anwendungen für Azure Stack auf einer Windows 10-, Linux- oder macOS-Arbeitsstation entwickeln. In diesem Artikel wird Folgendes erläutert: 

- Die verschiedenen Kontexte, in denen Ihre App in Azure Stack ausgeführt wird. 
- Die Schritte, die Sie ausführen müssen, um eine Windows 10-, Linux- oder macOS-Arbeitsstation einzurichten. 
- Schritte zum Erstellen von Ressourcen in Azure Stack und Bereitstellen einer App. 

## <a name="azure-stack-context-and-your-code"></a>Azure Stack-Kontext und Ihr Code 

Sie können Skripts und Apps schreiben, die jede Aufgabe in Azure Stack ausführen können. Es kann jedoch hilfreich sein, die Aufgaben, die Sie ausführen möchten, in drei verschiedene Modi einzuteilen. 

1. Im ersten Modus können Sie Apps erstellen, die Ressourcen mithilfe von Azure Resource Manager-Vorlagen in Azure Stack bereitstellen. Sie können z.B. ein Skript schreiben, das eine Azure Resource Manager-Vorlage erstellt. Diese Vorlage wiederum erstellt ein virtuelles Netzwerk und die VMs, die Ihre Apps hosten sollen. 

2. Im zweiten Modus arbeiten Sie über die REST-API und einen REST-Client, den Sie in Ihrem Code erstellt haben, direkt mit Endpunkten. In diesem Modus können Sie ein Skript schreiben, das ein virtuelles Netzwerk und die VMs erstellt, indem Anforderungen an die APIs gesendet werden. 

3. Im dritten Modus können Sie Ihren Code verwenden, um eine App zu erstellen, die in Azure Stack gehostet wird. Sobald Sie über eine Infrastruktur in Azure Stack verfügen, in der Sie Ihre App hosten können, stellen Sie Ihre App in der Infrastruktur bereit. In der Regel bereiten Sie zuerst die Umgebung vor und stellen dann die App in dieser Umgebung bereit. 

###  <a name="infrastructure-as-a-service-and-platform-as-a-service"></a>Infrastructure-as-a-Service und Platform-as-a-Service 

Als Cloud unterstützt Azure Stack beide Konzepte: 

- Infrastructure-as-a-Service (IaaS) 
- Platform-as-a-Service (PaaS) 

Sowohl IaaS als auch PaaS stellen Informationen dazu bereit, wie Sie Ihren Entwicklungscomputer einrichten sollten. 

IaaS ist die Virtualisierung der Bestandteile eines Rechenzentrums: Netzwerkkomponenten, Netzwerk und Server. Wenn Sie eine App in einer VM bereitstellen, die den Webserver hostet, arbeiten Sie in einem IaaS-Paradigma. In diesem Paradigma verwaltet Azure Stack die virtuellen Elemente, und Ihre App befindet sich auf einem virtuellen Server. Azure Stack-Ressourcenanbieter unterstützen Netzwerkkomponenten und virtuelle Server. 

PaaS abstrahiert die Infrastrukturebene, sodass Sie Ihre App auf einem Endpunkt bereitstellen, der die App dann ausführt. Im PaaS-Paradigma können Sie einen Container zum Hosten Ihrer App verwenden und die im Container befindliche App dann in einem Dienst bereitstellen, der den Container ausführt. Alternativ dazu können Sie Ihre App direkt in einem Dienst bereitstellen, der Ihre App ausführt. Azure Stack kann für die Ausführung in Azure App Service und Kubernetes verwendet werden. 

### <a name="azure-stack-resource-manager"></a>Azure Stack Resource Manager 

Diese drei Modi sowie PaaS bzw. IaaS werden durch die Azure Stack-Version von Azure Resource Manager ermöglicht. Im Verwaltungsframework können Sie Azure Stack-Ressourcen bereitstellen, verwalten und überwachen. Im Manager können Sie diese Elemente als Gruppe in einem einzigen Vorgang bearbeiten. Weitere Informationen zum Arbeiten mit dem Azure Stack Resource Manager finden Sie unter [Verwalten von API-Versionsprofilen in Azure Stack](azure-stack-version-profiles.md). 

### <a name="azure-stack-development-kits"></a>Azure Stack Development Kits 

Azure Stack verwendet eine Azure Stack-Version von Azure Resource Manager.  Um Sie bei der Arbeit mit dem Code Ihrer Wahl im Azure Stack Resource Manager zu unterstützen, haben wir eine Reihe von Software Development Kits bereitgestellt. Das umfasst: 

- [.NET/C#](azure-stack-version-profiles-net.md)
- [Java](azure-stack-version-profiles-java.md)
- [Go](azure-stack-version-profiles-go.md)
- [Ruby](azure-stack-version-profiles-ruby.md)
- [Python](azure-stack-version-profiles-python.md)

## <a name="before-you-start"></a>Vorbereitung 

Sie benötigen Folgendes: 

- Zugriff auf das Azure Stack-Benutzerportal. 
- Den Namen Ihres Mandanten. 
- Sie müssen ermitteln, ob Sie Azure Active Directory (Azure AD) oder Active Directory-Verbunddienste (AD FS) als Identitäts-Manager verwenden. 

Wenn Sie Fragen zu Ihrer Azure Stack-Instanz haben, wenden Sie sich an Ihren Cloudbetreiber. 

## <a name="windows-10"></a>Windows 10 

Ein Windows 10-Computer ermöglicht die Arbeit mit PowerShell 5.0 und Visual Studio. Wenn Sie mit einem ASDK arbeiten, stellen Sie über VPN eine Verbindung mit Ihrer Umgebung her. 

### <a name="set-up-your-tools"></a>Einrichten der Tools 

1. Richten Sie PowerShell ein. Führen Sie dazu die Schritte unter [Installieren von PowerShell für Azure Stack](../operator/azure-stack-powershell-install.md) aus. 

2. Laden Sie Azure Stack-Tools herunter. Führen Sie dazu die Schritte unter [Herunterladen von Azure Stack-Tools von GitHub](../operator/azure-stack-powershell-download.md) aus. 

3. Wenn Sie ein ASDK verwenden, installieren und konfigurieren Sie eine [VPN-Verbindung mit Azure Stack](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn). 

4. Installieren und konfigurieren Sie die Azure CLI. Führen Sie dazu die Schritte unter [Verwenden von API-Versionsprofilen mit der Azure CLI in Azure Stack](azure-stack-version-profiles-azurecli2.md) aus. 

5. Installieren und konfigurieren Sie den Azure Storage-Explorer. Beim Azure Storage-Explorer handelt es sich um eine eigenständige App, über die Sie mit Azure Stack-Speicherdaten arbeiten können.  Führen Sie dazu die Schritte unter [Herstellen einer Verbindung von Storage-Explorer mit einem Azure Stack-Abonnement oder -Speicherkonto](azure-stack-storage-connect-se.md) aus. 

### <a name="install-your-integrated-development-environment"></a>Installieren Ihrer integrierten Entwicklungsumgebung 

1. Installieren Sie Ihre integrierte Entwicklungsumgebung (IDE), je nach Codebasis und Präferenzen. 

     - Visual Studio Code (Python, GO, NodeJS). Laden Sie Visual Studio Code für Ihren Computer von [code.visualstudio.com](https://code.visualstudio.com/Download) herunter. 
     - Visual Studio (.NET/C#). Laden Sie die Visual Studio Community-Edition von [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/) herunter. 
     - Eclipse (Java). Laden Sie Eclipse von [eclipse.org](https://www.eclipse.org/downloads/) herunter. 

2. Installieren Sie das Software Development Kit (SDK) für Ihren Code. 

     - [.NET/C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="linux"></a>Linux 

Auf einem Linux-Computer können Sie mit der Azure CLI und Visual Studio Code oder in Ihrer bevorzugten integrierten Entwicklungsumgebung arbeiten. 

> [!Note]   
> Wenn Sie einen Linux-Computer mit dem ASDK verwenden, muss sich Ihr Remotecomputer im gleichen Netzwerk befinden wie das ASDK. Sie können keine Verbindung über ein VPN herstellen. 

### <a name="set-up-your-tools"></a>Einrichten der Tools 

1. Installieren und konfigurieren Sie die Azure CLI. Führen Sie dazu die Schritte unter [Verwenden von API-Versionsprofilen mit der Azure CLI in Azure Stack](azure-stack-version-profiles-azurecli2.md) aus. 

2. Installieren und konfigurieren Sie den Azure Storage-Explorer. Beim Azure Storage-Explorer handelt es sich um eine eigenständige App, über die Sie mit Azure Stack-Speicherdaten arbeiten können.  Führen Sie dazu die Schritte unter [Herstellen einer Verbindung von Storage-Explorer mit einem Azure Stack-Abonnement oder -Speicherkonto](azure-stack-storage-connect-se.md) aus. 

### <a name="install-your-integrated-development-environment"></a>Installieren Ihrer integrierten Entwicklungsumgebung 

1. Installieren Sie Ihre integrierte Entwicklungsumgebung (IDE), je nach Codebasis und Präferenzen. 

     - Visual Studio Code (Python, GO, NodeJS). Laden Sie Visual Studio Code für Ihren Computer von [code.visualstudio.com](https://code.visualstudio.com/Download) herunter. 
     - Visual Studio (.NET/C#). Laden Sie die Visual Studio Community-Edition von [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/) herunter. 
     - Eclipse (Java). Laden Sie Eclipse von [eclipse.org](https://www.eclipse.org/downloads/) herunter. 

2. Installieren Sie das Software Development Kit (SDK) für Ihren Code. 

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

1. Installieren und konfigurieren Sie die Azure CLI. Führen Sie dazu die Schritte unter [Verwenden von API-Versionsprofilen mit der Azure CLI in Azure Stack](azure-stack-version-profiles-azurecli2.md) aus. 

2. Installieren und konfigurieren Sie den Azure Storage-Explorer. Beim Azure Storage-Explorer handelt es sich um eine eigenständige App, über die Sie mit Azure Stack-Speicherdaten arbeiten können. Führen Sie dazu die Schritte unter [Herstellen einer Verbindung von Storage-Explorer mit einem Azure Stack-Abonnement oder -Speicherkonto](azure-stack-storage-connect-se.md) aus. 

### <a name="install-your-integrated-development-environment"></a>Installieren Ihrer integrierten Entwicklungsumgebung 

1. Installieren Sie Ihre integrierte Entwicklungsumgebung (IDE), je nach Codebasis und Präferenzen. 

     - Visual Studio Code (Python, GO, NodeJS). Laden Sie Visual Studio Code für Ihren Computer von [code.visualstudio.com](https://code.visualstudio.com/Download) herunter. 
     - Visual Studio (.NET/C#). Laden Sie die Visual Studio Community-Edition von [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/) herunter. 
     - Eclipse (Java). Laden Sie Eclipse von [eclipse.org](https://www.eclipse.org/downloads/) herunter. 

2. Installieren Sie das Software Development Kit (SDK) für Ihren Code. 

     - [.NET/C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="next-steps"></a>Nächste Schritte 

Stellen Sie eine App für Ressourcen in Azure Stack bereit. Die entsprechenden Schritte finden Sie unter [Häufige Bereitstellungen für Azure Stack](azure-stack-dev-start-deploy-app.md).