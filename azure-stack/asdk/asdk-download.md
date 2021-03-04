---
title: Herunterladen und Extrahieren des ASDK
description: Dieser Artikel beschreibt, wie Sie das Azure Stack Development Kit (ASDK) herunterladen und extrahieren.
author: PatAltimore
ms.topic: article
ms.date: 05/06/2019
ms.author: patricka
ms.reviewer: misainat
ms.lastreviewed: 08/10/2019
ms.openlocfilehash: de9085ee201fd472a0ffd71cb97f519f66fe3621
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "101839673"
---
# <a name="download-and-extract-the-asdk"></a>Herunterladen und Extrahieren des ASDK
Nachdem Sie sich vergewissert haben, dass Ihr Development Kit-Hostcomputer die Mindestanforderungen für die Installation des ASDK (Azure Stack Development Kit) erfüllt, besteht der nächste Schritt darin, das ASDK-Bereitstellungspaket herunterzuladen und zu extrahieren, um die Datei „CloudBuilder.vhdx“ zu erhalten.

## <a name="download-the-asdk"></a>Herunterladen des ASDK
1. Bevor Sie den Download starten, stellen Sie sicher, dass Ihr Computer die folgenden Voraussetzungen erfüllt:

   - Der Computer muss zusätzlich zum Betriebssystem-Datenträger mindestens über 60 GB freien Speicherplatz auf vier separaten und identischen logischen Festplatten verfügen.
   - [.NET Framework 4.6 (oder eine höhere Version)](https://dotnet.microsoft.com/download/dotnet-framework-runtime/net46) muss installiert sein.

2. [Navigieren Sie zur Seite mit den ersten Schritten](https://azure.microsoft.com/overview/azure-stack/try/?v=try), auf der Sie das ASDK herunterladen und Ihre Details angeben können. Klicken Sie anschließend auf **Übermitteln**.
3. Laden Sie die [Deployment Checker for ASDK](https://github.com/Azure/AzureStack-Tools/blob/master/Deployment/asdk-prechecker.ps1) (Bereitstellungsüberprüfung für ASDK) herunter, und führen Sie dieses Skript zur Überprüfung der Voraussetzungen aus. Mit diesem eigenständigen Skript werden die Voraussetzungsüberprüfungen des Setups für das ASDK durchlaufen. Hiermit können Sie sicherstellen, dass die Hardware- und Softwareanforderungen erfüllt sind, bevor Sie das größere Paket für das ASDK herunterladen.
4. Klicken Sie unter **Software herunterladen** auf **Azure Stack Development Kit**.

   > [!NOTE]
   > Der ASDK-Download (AzureStackDevelopmentKit.exe) umfasst ca. 10 GB.

## <a name="extract-the-asdk"></a>Extrahieren des ASDK
1. Klicken Sie nach Abschluss des Downloads auf **Ausführen**, um die selbstextrahierende ASDK-Datei (AzureStackDevelopmentKit.exe) zu starten.
2. Prüfen und akzeptieren Sie den angezeigten Lizenzvertrag auf der Seite **License Agreement** (Lizenzvertrag) des Self-Extractor-Assistenten, und klicken Sie anschließend auf **Weiter**.
3. Überprüfen Sie die Informationen in der Datenschutzerklärung, die auf der Seite **Important Notice** (Wichtiger Hinweis) des Self-Extractor-Assistenten angezeigt werden, und klicken Sie anschließend auf **Weiter**.
4. Wählen Sie den Speicherort für die Azure Stack-Setupdateien, die extrahiert werden sollen, auf der Seite **Select Destination Location** (Zielspeicherort auswählen) des Self-Extractor-Assistenten aus, und klicken Sie anschließend auf **Weiter**. Der Standardspeicherort lautet „*Aktueller Ordner*\Azure Stack Development Kit“. 
5. Sehen Sie sich die zusammengefassten Informationen zum Speicherort auf der Seite **Ready to Extract** (Bereit für Extraktion) des Self-Extractor-Assistenten an, und klicken Sie anschließend auf **Extract** (Extrahieren), um die Dateien „CloudBuilder.vhdx“ (ca. 28 GB) und „ThirdPartyLicenses.rtf“ zu extrahieren. Dieser Vorgang nimmt einige Zeit in Anspruch.
6. Kopieren bzw. verschieben Sie die Datei „CloudBuilder.vhdx“ in den Stammordner des Laufwerks C:\ (`C:\CloudBuilder.vhdx`) auf dem ASDK-Hostcomputer.

> [!NOTE]
> Nachdem Sie die Dateien extrahiert haben, können Sie die EXE- und BIN-Dateien löschen, um Platz auf der Festplatte zu schaffen. Sie können diese Dateien auch sichern, um sie nicht erneut herunterladen zu müssen, wenn Sie das ASDK erneut bereitstellen müssen.


## <a name="next-steps"></a>Nächste Schritte
[Vorbereiten des ASDK-Hostcomputers](asdk-prepare-host.md)