---
title: OPC UA-adatmegjelenítés Azure Time Series Insightsban
description: Ebből az oktatóanyagból megtudhatja, hogyan jelenítheti meg az adatTime Series Insights.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 5bd218c0d94922b6137a964e3993f516216ca4b7
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787717"
---
# <a name="tutorial-visualize-data-with-time-series-insights-tsi"></a>Oktatóanyag: az adatmegjelenítés Time Series Insightssal (ÁME)

Az OPC közzétevő modul az OPC UA-kiszolgálókhoz csatlakozik, és az ezekről a kiszolgálókról származó adatok közzétételét IoT Hub. Az ipari IoT-platform telemetria processzora dolgozza fel ezeket az eseményeket, és a környezetfüggő mintákat továbbítja az ÁME-nek és más fogyasztóknak.  

Ez az útmutató bemutatja, hogyan jelenítheti meg és elemezheti az OPC UA-telemetria ezzel a Time Series Insights környezettel.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Minden oktatóanyag tartalmaz egy listát, amely összegzi a Befejezés lépéseit.
> * A pontok mindegyike egy fő H2-re van igazítva
> * A következő zöld jelölőnégyzetek használata egy oktatóanyagban

## <a name="prerequisite"></a>Előfeltétel

* A IIoT platform üzembe helyezése Time Series Insights környezet automatikus létrehozásához
* A rendszer közzéteszi az adatIoT Hub

## <a name="time-series-insights-explorer"></a>Time Series Insights Explorer

A Time Series Insights Explorer egy webalkalmazás, amelyet a telemetria megjelenítésére használhat. Az alkalmazás URL-címének lekéréséhez nyissa meg a `.env` központi telepítés eredményeképpen mentett fájlt.  Nyisson meg egy böngészőt a változóban található URL-címhez `PCS_TSI_URL` .  

A Time Series Insights Explorer használata előtt hozzáférést kell biztosítania az ÁME-adathoz az adat megjelenítésére jogosult felhasználók számára. Vegye figyelembe, hogy egy új központi telepítésben nincs beállítva adathozzáférési szabályzat, ezért senki nem láthatja az adatelérési házirendeket. Az adathozzáférési házirendeket a IIoT platformon üzembe helyezett erőforráscsoporthoz telepített Time Series Insights környezetben kell beállítani a Azure Portal a következő módon:

   ![Time Series Insights Explorer 1](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-data-access-1.png)

Válassza ki az adatelérési házirendeket:

   ![Time Series Insights Explorer 2](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-data-access-2.png)

A szükséges felhasználók kiosztása:

   ![Time Series Insights Explorer 3](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-data-access-3.png)


Az ÁME Explorerben vegye figyelembe a nem hozzárendelt idősorozat-példányokat. Az ÁME-példányok egy adott adatponthoz tartozó Time/Value sorozatnak felelnek meg egy OPC-kiszolgáló közzétett csomópontja alapján. Az ÁME-példányt, illetve az OPC UA adatpontot egyedileg azonosítja a EndpointId, a SubscriptionId és a NodeId. Az ÁME-példányok modelljeit a rendszer automatikusan észleli és megjeleníti az Explorerben a IIoT platform telemetria processzorának Event hub-telemetria származó adatok alapján.

   ![Time Series Insights Explorer 4](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-0.png)

A telemetria a diagramon úgy is megjeleníthetők, hogy a jobb gombbal az ÁME-példányra kattint, és kiválasztja az értéket. A diagramon használni kívánt időkeret a jobb felső sarokban állítható be. Több példány értékének megjelenítéséhez ugyanazt az időkorlátot kell kijelölni.

További információ: gyors útmutató [: a Azure Time Series Insights előzetes verziójának megismerése](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)

## <a name="define-and-apply-a-new-model"></a>Új modell definiálása és alkalmazása

Mivel a telemetria-példányok már csak nyers formátumban jelennek meg, a megfelelő 

Az ÁME-modellekkel kapcsolatos részletes információkért tekintse meg [az idősorozat-modellt Azure Time Series Insights előzetes](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-tsm) verziójában

1. 1. lépés – az Explorer Model (modell) lapján Definiáljon egy új hierarchiát a telemetria tárolt adatmennyiséghez. A hierarchia a logikai fastruktúra, amely lehetővé teszi a felhasználó számára, hogy beillessze a szükséges meta-információkat az ÁME-példányokon keresztüli intuitív navigáláshoz. a felhasználók létrehozhatnak/törölhetnek vagy módosíthatnak olyan hierarchia-sablonokat, amelyek később a különböző ÁME-példányokhoz hozhatók létre.

   ![1. lépés](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-1.png)

2. 2. lépés – új típus megadása az értékekhez. A példánkban csak a numerikus adattípusokat kezeljük

   ![2. lépés](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-2.png)

3. 3. lépés – Válassza ki azt az új ÁME-példányt, amely a korábban definiált hierarchiában kategorizálva van

   ![3. lépés](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-3.png)

4. 4. lépés – a példányok tulajdonságainak kitöltése – név, leírás, adatérték, valamint a hierarchia mezői a logikai struktúra egyeztetéséhez 

   ![4. lépés](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-4.png)

5. 5. lépés – ismételje meg az 5. lépést az összes Kategorizálatlan ÁME-példány esetében

   ![5. lépés](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-5.png)

6. 6. lépés – vissza az ÁME Explorer főoldalára, lépjen a kategorizált példányok hierarchiába, és válassza ki az elemezni kívánt adatpontok értékeit.

   ![Step6](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-6.png)

## <a name="connect-time-series-insights-to-power-bi"></a>Time Series Insights összekötése a Power BI

A Time Series Insights-környezet Power BIhoz is csatlakoztatható.  További információkért lásd: az [ÁME összekötése Power bi](https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi) és az [ÁME-ből származó adatok megjelenítése Power BIban](https://docs.microsoft.com/azure/time-series-insights/concepts-power-bi).


## <a name="next-steps"></a>Következő lépések
Most, hogy megismerte, hogyan jelenítheti meg az adatkezelési ÁME-t, tekintse meg az ipari IoT GitHub-tárházát:

> [!div class="nextstepaction"]
> [IIoT platform GitHub-adattár](https://github.com/Azure/iot-edge-opc-publisher)