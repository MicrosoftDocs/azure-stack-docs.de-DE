---
title: Konfigurieren einer Hybrid Cloud-Identität mit Azure- und Azure Stack-Apps | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie eine Hybrid Cloud-Identität mit Azure- und Azure Stack-Apps konfigurieren.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/26/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 06/26/2019
ms.openlocfilehash: 074d971c1f951797b5dc2d53a62eef56d0b7249f
ms.sourcegitcommit: eccbd0098ef652919f357ef6dba62b68abde1090
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67492323"
---
# <a name="tutorial-configure-hybrid-cloud-identity-for-azure-and-azure-stack-applications"></a>Tutorial: Konfigurieren einer Hybrid Cloud-Identität für Azure- und Azure Stack-Anwendungen

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Hier erfahren Sie, wie Sie eine Hybrid Cloud-Identität für Ihre Azure- und Azure Stack-Apps konfigurieren.

Der Zugriff auf Ihre Apps in globalen Azure- und Azure Stack-Instanzen kann auf zwei Arten gewährt werden.

 * Wenn Azure Stack über eine ständige Internetverbindung verfügt, können Sie Azure Active Directory (Azure AD) verwenden.
 * Wenn Azure Stack über keine Internetverbindung verfügt, können Sie Azure Directory-Verbunddienste (AD FS) verwenden.

Dienstprinzipale werden verwendet, um den Zugriff auf Ihre Azure Stack-Apps zur Bereitstellung oder Konfiguration über Azure Resource Manager in Azure Stack zu gewähren.

In diesem Tutorial erstellen Sie eine Beispielumgebung, die Folgendes ermöglicht:

> [!div class="checklist"]
> - Einrichten einer Hybrididentität in globalen Azure- und Azure Stack-Instanzen
> - Abrufen eines Tokens für den Zugriff auf die Azure Stack-API

Sie benötigen Azure Stack-Bedienerberechtigungen für die Schritte in diesem Tutorial.

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack ist eine Erweiterung von Azure. Mit Azure Stack holen Sie sich die Agilität und Innovation von Cloud Computing in Ihre lokale Umgebung, indem Sie die einzige Hybrid Cloud aktivieren, mit der Sie Hybrid-Apps überall entwickeln und bereitstellen können.  
> 
> Im Whitepaper [Design Considerations for Hybrid Applications](https://aka.ms/hybrid-cloud-applications-pillars) (Entwurfsüberlegungen für Hybridanwendungen) werden die wichtigen Aspekte in Bezug auf die Softwarequalität (Platzierung, Skalierbarkeit, Verfügbarkeit, Resilienz, Verwaltbarkeit und Sicherheit) beschrieben, die für das Entwerfen, Bereitstellen und Betreiben von Hybridanwendungen erforderlich sind. Die Überlegungen zum Entwurf dienen als Hilfe beim Optimieren des Designs von Hybridanwendungen, um für Produktionsumgebungen das Auftreten von Problemen zu minimieren.


## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>Erstellen eines Dienstprinzipals für Azure AD über das Portal

Wenn Sie Azure Stack mit Azure AD als Identitätsspeicher bereitgestellt haben, können Sie Dienstprinzipale genauso wie für Azure erstellen. Im Artikel [Verwenden einer App-Identität für den Ressourcenzugriff](../operator/azure-stack-create-service-principals.md#manage-an-azure-ad-service-principal) erfahren Sie, wie die Schritte über das Portal ausgeführt werden. Vergewissern Sie sich vorher, dass Sie über die [erforderlichen Azure AD-Berechtigungen](/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) verfügen.

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>Erstellen eines Dienstprinzipals für AD FS mithilfe von PowerShell

Wenn Sie Azure Stack mit AD FS bereitgestellt haben, können Sie PowerShell verwenden, um einen Dienstprinzipal zu erstellen, eine Rolle für den Zugriff zuzuweisen und die Anmeldung über PowerShell mit dieser Identität durchzuführen. Im Artikel [Verwenden einer App-Identität für den Ressourcenzugriff](../operator/azure-stack-create-service-principals.md#manage-an-ad-fs-service-principal) erfahren Sie, wie Sie die erforderlichen Schritte mithilfe von PowerShell ausführen.

## <a name="using-the-azure-stack-api"></a>Verwenden der Azure Stack-API

Im Tutorial zur [Azure Stack-API](azure-stack-rest-api-use.md) erfahren Sie Schritt für Schritt, wie Sie ein Token für den Zugriff auf die Azure Stack-API abrufen.

## <a name="connect-to-azure-stack-using-powershell"></a>Herstellen einer Verbindung mit Azure Stack mithilfe von PowerShell

In der Schnellstartanleitung zum [Einrichten von PowerShell in Azure Stack](../operator/azure-stack-powershell-install.md) sind die Schritte erläutert, die zum Installieren von Azure PowerShell und zum Herstellen einer Verbindung mit der Azure Stack-Installation ausgeführt werden müssen.

### <a name="prerequisites"></a>Voraussetzungen

Sie benötigen eine mit Azure Active Directory verbundene Azure Stack-Installation mit einem Abonnement, auf das Sie zugreifen können. Sollten Sie über keine Azure Stack-Installation verfügen, sehen Sie sich die Anleitung zum Einrichten eines [Azure Stack Development Kits](../asdk/asdk-install.md) an.

#### <a name="connect-to-azure-stack-using-code"></a>Herstellen einer Verbindung mit Azure Stack mithilfe von Code

Wenn Sie mithilfe von Code eine Verbindung mit Azure Stack herstellen möchten, verwenden Sie die Endpunkte-API von Azure Resource Manager, um die Authentifizierung und die Graph-Endpunkte für Ihre Azure Stack-Installation abzurufen, und führen Sie dann die Authentifizierung mithilfe von REST-Anforderungen durch. Eine Beispielclientanwendung finden Sie auf [GitHub](https://github.com/shriramnat/HybridARMApplication).

>[!Note]
>Wenn Sie das Azure SDK für Azure Stack verwenden möchten, muss es Azure-API-Profile für die gewählte Sprache unterstützen. Weitere Informationen zu Azure-API-Profilen finden Sie im Artikel [Verwalten von API-Versionsprofilen in Azure Stack](azure-stack-version-profiles.md).

## <a name="next-steps"></a>Nächste Schritte

 - Weitere Informationen zur Behandlung der Identität in Azure Stack finden Sie unter [Identitätsarchitektur für Azure Stack](../operator/azure-stack-identity-architecture.md).
 - Weitere Informationen zu Azure-Cloudmustern finden Sie unter [Cloudentwurfsmuster](https://docs.microsoft.com/azure/architecture/patterns).
