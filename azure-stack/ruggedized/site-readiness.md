---
title: Standortvorbereitung von Azure Stack Hub Ruggedized für Azure Stack Hub | Microsoft-Dokumentation
description: Informieren Sie sich über die Spezifikationen der Standortvorbereitung für Azure Stack Hub Ruggedized.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2020
ms.author: wamota
ms.reviewer: wamota
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: 02e549d4cae9dc0c543d4a21c48e972231257ced
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96941151"
---
# <a name="azure-stack-hub-ruggedized-site-readiness"></a>Standortvorbereitung von Azure Stack Hub Ruggedized

In diesem Thema werden die Umgebungsanforderungen und die Anforderungen an die Stromzuführungen der Stromverteilereinheit für Azure Stack Hub Ruggedized beschrieben. 

>[!NOTE]
>Diese Werte gelten nur für die Standortplanung, und es handelt sich um konservative Schätzwerte. Die tatsächlichen Anforderungen können variieren.

## <a name="environmental-requirements"></a>Umgebungsanforderungen

Die folgende Tabelle enthält die Umgebungsanforderungen für eine Azure Stack Hub Ruggedized-Lösung mit der folgenden Konfiguration:

- Skalierungseinheit von 14 TB
- Eingangsspannung von 200 V AC
- Maximale Umgebungstemperatur 35 °C

*Tabelle 1: Hohe/niedrige Konfiguration der Umgebungsanforderungen*

| Object                         | Anforderungen für Azure Stack Hub Ruggedized               |
|--------------------------------|--------------------------------|
|Betriebstemperatur           | Azure Stack Hub Ruggedized-Betriebstemperatur (mit Heizungsanforderungen): –32 °C (–25,6 °F) bis 43 °C (109 °F)    |
|Luftfeuchtigkeit           | Speicher: Relative Luftfeuchtigkeit von 5 % bis 95 % mit maximalem Taupunkt von 33 °C (91 °F). Die Atmosphäre darf zu keiner Zeit kondensierend sein. <br> In Betrieb: Relative Luftfeuchtigkeit von 5 % bis 85 % mit maximalem Taupunkt von 29 °C (84,2 °F).
|Physische Konnektivität           | Für Azure Stack Hub Ruggedized sind folgende physische Anschlüsse möglich: <br>4x 10G SR SFP+ <br>4x 1000BASE-SX <br>4x 1000Base-T
|Eingangsleistung                     | Max. 4,981 kW, durchschn. 4,391 kW<br> Eingangsstecker C13/C14<br> Eingabe: 100–240 V, 50/60 Hz

## <a name="pdu-power-drop-requirements"></a>Anforderungen an die Stromzuführungen der Stromverteilereinheit

In der folgenden Tabelle sind die für Azure Stack Hub Ruggedizied erforderlichen Stromzuführungen aufgeführt.

*Tabelle 2. Erforderliche Anzahl von Stromzuführungen*

| Konfiguration  | Einphasig  | Dreiphasig, Dreieckschaltung |Dreiphasig, Sternschaltung |
|----------------|---------------|-------------------|----------------|
|Hoch/niedrig        | 2             | 2                 | 2              |

Mit dem integrierten System von Azure Stack können Sie zur optimalen Integration in Ihr Rechenzentrum unterschiedliche Anschlusstypen für die Stromverteilereinheit verwenden. In der folgenden Tabelle werden die Anschlusstypen aufgeführt:

*Tabelle 3. Stromverteilereinheit und Anschlussoptionen*

| Standort     | Einphasig                                | Dreiphasig, Dreieckschaltung                                   | Dreiphasig, Sternschaltung                                        |
|--------------|---------------------------------------------|-----------------------------------------------------|-----------------------------------------------------------|
|Nordamerika |– L630P<br>– L7-30P<br>– Russellstoll 3750DP |– Hubbell Pro CS8365L<br>– Russellstoll 9P54U2T/1100 |– Hubbell C530P6S<br>– ABL Sursum S52S0A<br>– Freie Kabel |


