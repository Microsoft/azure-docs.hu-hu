---
title: A BETTER Mobile Threat Defense (MTD) összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan használhatja a BETTER Mobile Threat Defense (MTD) adatösszekötőt a MTD-naplók Azure Sentinelbe való lekéréséhez. Megtekintheti a munkafüzetek MTD, riasztásokat hozhat létre, és javíthatja a vizsgálatot.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2021
ms.author: yelevin
ms.openlocfilehash: 4828e31b9d15f101740c158ee62c90c95673c9a7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98541559"
---
# <a name="connect-your-better-mobile-threat-defense-mtd-to-azure-sentinel"></a>A BETTER Mobile Threat Defense (MTD) és az Azure Sentinel összekötése

> [!IMPORTANT]
> A BETTER Mobile Threat Defense (MTD)-összekötő jelenleg **előzetes** verzióban érhető el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.

A BETTER Mobile Threat Defense-(MTD-) összekötővel könnyedén csatlakoztathatja az összes jobb MTD biztonsági megoldást az Azure Sentinelhez, megtekintheti az irányítópultokat, egyéni riasztásokat hozhat létre, és javíthatja a vizsgálatot. A BETTER Mobile Threat Defense és az Azure Sentinel közötti integráció a REST API használatát teszi lehetővé.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="configure-and-connect-better-mobile-threat-defense"></a>A BETTER Mobile Threat Defense konfigurálása és összekapcsolása

A jobb MTD képes közvetlenül az Azure Sentinelbe integrálni és exportálni a naplókat.

1. Az Azure Sentinel navigációs menüjében válassza az **adatösszekötők** lehetőséget.

1. Az **adatösszekötők katalógusában** válassza a **jobb Mobile Threat Defense (MTD) (előzetes verzió)** lehetőséget, majd **nyissa meg az összekötő lapot**.

1. Kövesse az összekötő oldalon található lépéseket, és [ezen a lapon a jobb MTD dokumentációjában](https://mtd-docs.bmobi.net/integrations/azure-sentinel/setup-integration#mtd-integration-configuration) véglegesítse az integrációt a jobb MTD-konzolon.

    Amikor a rendszer kéri a **munkaterület-azonosító** és az **elsődleges kulcs** értékének megadását, másolja őket az Azure Sentinel Connector oldaláról, és ILLESSZE be őket a jobb MTD-konfigurációba.

    :::image type="content" source="media/connectors/workspace-id-primary-key.png" alt-text="{Munkaterület-azonosító és elsődleges kulcs}":::

## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat a **naplók** területen, a **CustomLogs** szakaszban jelenik meg, az alábbi táblázatok közül egyet vagy többet:
- `BetterMTDDeviceLog_CL`
- `BetterMTDIncidentLog_CL`
- `BetterMTDAppLog_CL`
- `BetterMTDNetflowLog_CL`

Az elemzési szabályokban, a vadászati lekérdezésekben vagy az Azure Sentinelben bárhol máshol található jobb MTD-naplók lekérdezéséhez adja meg a fenti táblanév egyikét a lekérdezési ablak tetején.

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics. 

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtanulta, hogyan csatlakoztatható a BETTER Mobile Threat Defense (MTD) az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan [érheti el az adatait és a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.
