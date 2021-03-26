---
title: Mi a metrikai tanácsadó szolgáltatás?
titleSuffix: Azure Cognitive Services
description: Mi az a Metrics Advisor?
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: overview
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: 901d86b5569be61f89178dac460b8750bce9ea73
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605535"
---
# <a name="what-is-metrics-advisor-preview"></a>Mi a mérőszámok tanácsadója (előzetes verzió)? 

A metrikai tanácsadó az Azure Cognitive Services része, amely az AI használatával végzi az adatelemzést és a rendellenességek észlelését az idősorozat-adatokban. A szolgáltatás automatizálja a modellek alkalmazásának folyamatát, és API-kat és webes munkaterületet biztosít az adatfeldolgozáshoz, a rendellenességek észleléséhez és a diagnosztikahez – anélkül, hogy ismernie kellene a gépi tanulást. A fejlesztők AIOps, predicative karbantartást és üzleti figyelő alkalmazásokat hozhatnak létre a szolgáltatás felett. Metrikai tanácsadó használata a következőhöz:

* Több dimenziós adatok elemzése több adatforrásból
* Rendellenességek azonosítása és korrelációja
* Az adatain használt anomália-észlelési modell konfigurálása és finomhangolása
* Anomáliák diagnosztizálása és Súgó a kiváltó okok elemzéséhez

:::image type="content" source="media/metrics-advisor-overview.png" alt-text="A metrikai tanácsadó áttekintése":::

## <a name="connect-to-a-variety-of-data-sources"></a>Kapcsolódás különféle adatforrásokhoz

A metrikai tanácsadó képes a több adattárolóból származó [többdimenziós metrikai](how-tos/onboard-your-data.md) adatokhoz való kapcsolódásra, többek között a következőkre: SQL Server, Azure Blob Storage, MongoDB stb.

## <a name="easy-to-use-and-customizable-anomaly-detection"></a>Könnyen használható és testreszabható anomáliák észlelése

* A metrikai tanácsadó automatikusan kiválasztja a legjobb modellt az adatokhoz, anélkül, hogy ismernie kellene a gépi tanulást.
* A [többdimenziós mérőszámokban](glossary.md#multi-dimensional-metric)lévő minden idősorozat automatikus figyelése.
* A [Paraméterek finomhangolása](how-tos/configure-metrics.md) és az [interaktív visszajelzések](how-tos/anomaly-feedback.md) segítségével testre szabhatja az adatain alkalmazott modellt, valamint a jövőbeli anomáliák észlelésének eredményét.

## <a name="real-time-alerts-through-multiple-channels"></a>Valós idejű riasztások több csatornán keresztül

Ha rendellenességek észlelhetők, a metrikai tanácsadó több csatornán keresztül tud [valós idejű riasztásokat küldeni](how-tos/alerts.md) , például: e-mail-hookok, webhookok és az Azure DevOps hookok. A rugalmas riasztási szabályok lehetővé teszik a riasztások küldésének és céljának testreszabását.

## <a name="smart-diagnostic-insights-by-analyzing-anomalies"></a>Intelligens diagnosztikai adatok rendellenességek elemzésével

Elemezheti a többdimenziós metrikákban észlelt rendellenességeket, és [intelligens diagnosztikai](how-tos/diagnose-incident.md) elemzéseket készíthet, beleértve a legvalószínűbb kiváltó okot, a diagnosztikai fákat, a metrikus fúrást és egyebeket. A [metrikai gráf](how-tos/metrics-graph.md)konfigurálásával lehetővé teheti a kereszthivatkozások elemzését az incidensek megjelenítésének megkönnyítésére.


## <a name="typical-workflow"></a>Jellemző munkafolyamat

A munkafolyamat egyszerű: az adatfeldolgozás után finomíthatja a rendellenességek észlelését, és létrehozhat konfigurációkat, hogy illeszkedjenek a forgatókönyvhöz.

1. [Hozzon létre egy Azure-erőforrást](https://go.microsoft.com/fwlink/?linkid=2142156) a metrikák tanácsadójának. 
2. Hozza létre első figyelőjét a webes portál használatával.
    1. Az adatok előkészítése
    2. Az anomáliák finomhangolása
    3. Feliratkozás riasztásokra
    4. Diagnosztikai ismeretek megtekintése
3. A példány testreszabásához használja a REST API.

## <a name="next-steps"></a>Következő lépések

* Ismerkedjen meg a gyors útmutatóval: [Figyelje meg az első mérőszámot a weben](quickstarts/web-portal.md).
* Ismerkedés a gyors üzembe helyezéssel: [a REST API-k segítségével testre szabhatja a megoldást](./quickstarts/rest-api-and-client-library.md).
