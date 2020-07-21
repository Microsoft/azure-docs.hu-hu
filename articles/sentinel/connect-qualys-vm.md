---
title: A Qualys sebezhetőségi kezelési adatvédelmek összekapcsolhatók az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztathatók a Qualys sebezhetőségi kezelési információi az Azure Sentinel szolgáltatáshoz.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 44002a8f4ab3b644e3530ee2d2fc06a7af271fbe
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531460"
---
# <a name="connect-your-qualys-vm-to-azure-sentinel-with-azure-function"></a>A Qualys virtuális gép összekötése az Azure Sentinel szolgáltatással az Azure Function használatával

A Qualys sebezhetőség-kezelő (VM) összekötő segítségével könnyedén csatlakoztathatja az összes [Qualys-alapú virtuális gép](https://www.qualys.com/apps/vulnerability-management/) biztonsági megoldásának naplóit az Azure Sentinel használatával, megtekintheti az irányítópultokat, egyéni riasztásokat hozhat létre, és javíthatja a vizsgálatot. A Qualys virtuális gép és az Azure Sentinel közötti integráció a Azure Functions használatával kéri le a naplózási adataikat a REST API használatával.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="configure-and-connect-qualys-vm"></a>Qualys virtuális gép konfigurálása és összekapcsolása

A Azure Functions az eseményeket és naplókat közvetlenül a Qualys virtuális gépről integrálhatja és lehívhatja, és továbbíthatja őket az Azure Sentinel szolgáltatásba.

1. Az Azure Sentinel-portálon kattintson az **adatösszekötők** elemre, és válassza a **Qualys sebezhetőségi kezelési** összekötő elemet.

1. Válassza az **összekötő megnyitása lapot**.

1. Kövesse az **Qualys biztonsági rések kezelése** oldalon található utasításokat.

## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat a **QualysHostDetection_CL** táblázat alatt log Analytics jelenik meg.

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan csatlakoztatható a Qualys virtuális gép az Azure Sentinelhez az Azure Function Apps használatával. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.
