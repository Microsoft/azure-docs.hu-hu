---
title: A rendellenesség-Kiderítő API használata az idősorozat-adatain
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan észlelheti a rendellenességeket az adataiban kötegként vagy adatfolyamként.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: mbullwin
ms.openlocfilehash: 74f891ba7f5b400b5782565e670539167f4e2464
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97703432"
---
# <a name="how-to-use-the-anomaly-detector-api-on-your-time-series-data"></a>Útmutató: a rendellenesség-Kiderítő API használata az idősorozat-adatain  

Az [anomália-érzékelő API](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) két módszert biztosít a anomáliák észlelésére. Észlelheti a rendellenességeket a Times-sorozat során, vagy az adatmennyiséget a legújabb adatpontok anomália állapotának észlelésével. Az észlelési modell a anomáliák eredményét adja vissza az egyes adatpontok várt értékével együtt, valamint a felső és az alsó anomália észlelési határait. ezekkel az értékekkel megjelenítheti a normál értékek tartományát és az adatokban mutatkozó rendellenességeket.

## <a name="anomaly-detection-modes"></a>Anomáliák észlelési módjai 

Az anomália detektor API az észlelési módokat biztosítja: a Batch és a streaming.

> [!NOTE]
> A következő kérelem URL-címeit össze kell kapcsolni az előfizetéséhez tartozó megfelelő végponttal. Például: `https://<your-custom-subdomain>.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/entire/detect`


### <a name="batch-detection"></a>Kötegelt észlelés

Egy adott időtartományon belüli adatpontok egy kötegében észlelt rendellenességek észleléséhez használja az alábbi kérelem URI-JÁT az idősoros adataival: 

`/timeseries/entire/detect`. 

Az idősorozat-adatok egyszerre történő elküldésével az API létrehoz egy modellt a teljes sorozat használatával, és elemzi az összes adatpontot.  

### <a name="streaming-detection"></a>Folyamatos átvitel észlelése

A folyamatos adatátviteli hibák észleléséhez használja a következő kérelem URI-JÁT a legújabb adatponttal: 

`/timeseries/last/detect'`. 

Az új adatpontok létrehozásakor az adatok valós időben figyelhetők. A rendszer létrehoz egy modellt az elküldött adatpontokkal, és az API megállapítja, hogy az idősorozat utolsó pontja anomália-e.

## <a name="adjusting-lower-and-upper-anomaly-detection-boundaries"></a>Alsó és felső anomáliák észlelési határainak módosítása

Alapértelmezés szerint a rendszer a, és a használatával számítja ki a anomáliák észlelésének alsó és felső határát `expectedValue` `upperMargin` `lowerMargin` . Ha eltérő határokra van szüksége, javasoljuk, hogy alkalmazzon a vagy a rendszerre `marginScale` `upperMargin` `lowerMargin` . A határok kiszámítása a következőképpen történik:

|Határ  |Számítás  |
|---------|---------|
|`upperBoundary` | `expectedValue + (100 - marginScale) * upperMargin`        |
|`lowerBoundary` | `expectedValue - (100 - marginScale) * lowerMargin`        |

Az alábbi példákban egy anomália-detektor API-nak a különböző érzékenységi eredményei láthatók.

### <a name="example-with-sensitivity-at-99"></a>Példa érzékenységgel a 99

![Alapértelmezett érzékenység](../media/sensitivity_99.png)

### <a name="example-with-sensitivity-at-95"></a>Példa érzékenységgel a 95

![99 érzékenység](../media/sensitivity_95.png)

### <a name="example-with-sensitivity-at-85"></a>Példa érzékenységgel a 85

![85 érzékenység](../media/sensitivity_85.png)

## <a name="next-steps"></a>Következő lépések

* [Mi az az Anomaly Detector API?](../overview.md)
* [Gyors útmutató: anomáliák észlelése az idősoros adataiban az anomália detektor használatával](../quickstarts/client-libraries.md)
