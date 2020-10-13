---
title: Bereitstellen einer Vorlage über das Portal in Azure Stack Hub
description: Erfahren Sie, wie Sie mit dem Azure Stack Hub-Portal eine Vorlage bereitstellen.
author: mattbriggs
ms.topic: how-to
ms.date: 10/05/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/05/2020
ms.openlocfilehash: b428c99f7c8b847e575e40af2915a4da60f2312f
ms.sourcegitcommit: 868887e4b13b1572f15004a9db2c334e60d8add2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777999"
---
# <a name="deploy-a-template-using-the-portal-in-azure-stack-hub"></a>Bereitstellen einer Vorlage über das Portal in Azure Stack Hub

Sie können das Azure Stack Hub-Benutzerportal verwenden, um ARM-Vorlagen (Azure Resource Manager) für Azure Stack Hub bereitzustellen.

## <a name="to-deploy-a-template"></a>So stellen Sie eine Vorlage bereit

1. Melden Sie sich beim Azure Stack Hub-Benutzerportal an, und wählen Sie **+ Ressource erstellen** > **Benutzerdefiniert** > **Vorlagenbereitstellung** aus.

   ![Erstellen einer Ressource im Azure Stack Hub-Portal](media/azure-stack-deploy-template-portal/template-deploy1.png)

2. Sie können entweder **Mit Eingabe beginnen, um zu filtern** auswählen, um eine GitHub-Schnellstartvorlage auszuwählen, oder **Eigene Vorlage im Editor erstellen** auswählen.

   ![Bereitstellen der Vorlage im Azure Stack Hub-Portal](media/azure-stack-deploy-template-portal/template-deploy2.png)

    [**Azure Stack-Schnellstartvorlagen**](https://github.com/Azure/AzureStack-QuickStart-Templates) werden nicht von Microsoft, sondern von einem Mitglied der Azure Stack Hub-Community erstellt. Die Vorlagen werden nicht von Microsoft, sondern von dessen Besitzer unter einem Lizenzvertrag für Sie lizenziert. Microsoft ist für diese Vorlagen nicht verantwortlich und führt dafür keine Tests in Bezug auf die Sicherheit, Kompatibilität oder Leistung durch. Vorlagen aus der Community werden von keinem Microsoft-Supportprogramm oder -Dienst unterstützt und *in der vorliegenden Form* ohne jegliche Gewährleistung zur Verfügung gestellt.

3. Wenn Sie **Eigene Vorlage im Editor erstellen** ausgewählt haben, fügen Sie Ihren JSON-Vorlagencode in das Codefenster ein.

   ![Bearbeiten der Vorlage im Azure Stack Hub-Portal](media/azure-stack-deploy-template-portal/template-deploy3.png)

    - Wählen Sie **Schnellstartvorlage** aus, um eine Communityvorlage im Editor zu laden.

    - Wählen Sie **Datei laden** aus, um eine Azure Resource Manager-Vorlage, die sich auf Ihrem lokalen Computer befindet, in den Editor zu laden.

    - Wählen Sie **Herunterladen** aus, um die Azure Resource Manager-Vorlage auf Ihrem lokalen Computer zu speichern.

    Wenn Sie die gewünschten Änderungen an der Vorlage vorgenommen haben, wählen Sie **Speichern** aus.

4. Wählen Sie **Abonnement** aus. Wählen Sie das Abonnement aus, das Sie verwenden möchten. Wählen Sie **Ressourcengruppe** aus. Sie können eine vorhandene Ressourcengruppe auswählen oder eine neue Ressourcengruppe erstellen. Wählen Sie anschließend **OK** aus. Wählen Sie dann **Überprüfen und erstellen** aus.

   ![Bearbeiten von Parametern im Azure Stack Hub-Portal](media/azure-stack-deploy-template-portal/template-deploy4.png)

5. Klicken Sie auf **Erstellen**.

   ![Auswählen des Abonnements im Azure Stack Hub-Portal](media/azure-stack-deploy-template-portal/template-deploy5.png)

6. Eine neue Kachel auf dem Dashboard verfolgt den Fortschritt Ihrer Vorlagenbereitstellung.

   ![Auswählen der Ressourcengruppe im Azure Stack Hub-Portal](media/azure-stack-deploy-template-portal/template-deploy6.png)

   Mit Azure Resource Manager-Vorlagen können Sie alle Ressourcen für Ihre Anwendung in einem einzigen, koordinierten Vorgang bereitstellen. Sie können Vorlagen auch erneut bereitstellen, um Änderungen an den Ressourcen in einer Ressourcengruppe vorzunehmen. Weitere Informationen zur Verwendung von Vorlagen mit Azure Stack Hub finden Sie unter [Verwenden von Azure Resource Manager-Vorlagen in Azure Stack Hub](azure-stack-arm-templates.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Bereitstellen von Vorlagen finden Sie im folgenden Artikel:

- [Bereitstellen von Vorlagen mit PowerShell](azure-stack-deploy-template-powershell.md)
