---
title: Ajánlott eljárások idősorozat-AZONOSÍTÓk kiválasztásához – Azure Time Series Insights | Microsoft Docs
description: Ismerje meg az idősorozat-azonosító Azure Time Series Insights Gen2 történő kiválasztásakor ajánlott eljárásokat.
author: shipramishra
ms.author: shmishr
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: seodec18
ms.openlocfilehash: 8aefa6efa5f10dae8a28e7126b91b7b5f6ac77a6
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950739"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>Az idősor-azonosító kiválasztásának ajánlott eljárásai

Ez a cikk összefoglalja a Azure Time Series Insights Gen2-környezet idősorozat-AZONOSÍTÓjának fontosságát, valamint az ajánlott eljárásokat az egyik kiválasztásához.

## <a name="choose-a-time-series-id"></a>Time Series-azonosító kiválasztása

A megfelelő idősorozat-azonosító kiválasztása kritikus. Az idősorozat-azonosító kiválasztása olyan, mint egy adatbázis partíciós kulcsának kiválasztása. Azure Time Series Insights Gen2-környezet létrehozásakor szükséges.

Tekintse meg a környezet üzembe helyezési oktatóanyagát az idősorozat-azonosító részletes ismertetéséhez. Megtekintheti két különböző JSON telemetria hasznos adatot, valamint a megfelelő idősorozat-azonosítót.</br>

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWzk3P]


> [!IMPORTANT]
> Az idősorozat-azonosítók a következők:
>
> * *Kis-* és nagybetűket megkülönböztető tulajdonság: a levél és a karakter betokozása a keresésekben, összehasonlításokban, frissítésekben és a particionáláskor használatos.
> * Nem *módosítható tulajdonság:* a létrehozás után nem módosítható.

> [!TIP]
> Ha az eseményforrás egy IoT hub, az idősorozat-azonosító valószínűleg ***iothub-kapcsolat-eszköz-azonosító*** lesz.

Az alábbi legfontosabb ajánlott eljárások a következők:

* Válasszon egy olyan partíciót, amely sok különböző értékkel rendelkezik (például több száz vagy ezer). Sok esetben ez lehet az eszköz azonosítója, az érzékelő azonosítója vagy a címke azonosítója a JSON-ban.
* Az idősorozat-AZONOSÍTÓnak egyedinek kell lennie az [Idősorozat-modell](./concepts-model-overview.md)levél csomópontjának szintjén.
* Az idősorozat-azonosító tulajdonságának Name karakterláncának karakteres korlátja 128. Az idősorozat-azonosító tulajdonságának értékeként a karakteres korlát 1 024.
* Ha hiányzik az idősorozat-azonosító egyedi tulajdonságának értéke, akkor a rendszer null értékként kezeli, és az egyediségi megkötésre vonatkozó szabályt követi.
* Ha az idősorozat-azonosító egy összetett JSON-objektumba van beágyazva, akkor ügyeljen arra, hogy a tulajdonság nevének megadásakor kövesse a bejövő [adatösszeolvasztási szabályokat](./concepts-json-flattening-escaping-rules.md) . Tekintse meg a [B](concepts-json-flattening-escaping-rules.md#example-b)példát.
* Az idősorozat-azonosító legfeljebb *három* fő tulajdonságot választhat. A kombinációjuk egy összetett kulcs lesz, amely az idősorozat AZONOSÍTÓját jelöli.  
  > [!NOTE]
  > A három fő tulajdonságnak karakterláncnak kell lennie.
  > Egyszerre csak egy tulajdonság helyett kell lekérdezni ezt az összetett kulcsot.

## <a name="select-more-than-one-key-property"></a>Egynél több Key tulajdonság kijelölése

Az alábbi forgatókönyvek több Key tulajdonság kiválasztását írják le idősorozat-AZONOSÍTÓként.  

### <a name="example-1-time-series-id-with-a-unique-key"></a>1. példa: idősorozat-azonosító egyedi kulccsal

* Régi típusú flottája van. Mindegyik egyedi kulccsal rendelkezik.
* Az egyik flottát a **deviceId** tulajdonság egyedileg azonosítja. Egy másik flotta esetében az egyedi tulajdonság a **objectId**. Egyik flotta sem tartalmazza a másik flotta egyedi tulajdonságát. Ebben a példában két kulcsot, **deviceId** és **objectId** választ ki egyedi kulcsként.
* A rendszer null értékeket fogad el, és a tulajdonság jelenlétének hiánya az esemény-adattartalomban null értéknek számít. Ez azt is lehetővé teszi, hogy az adatküldés két eseményforrás számára legyen kezelhető, ahol az egyes eseményforrás-forrásokban lévő adatok egyedi idősorozat-AZONOSÍTÓval rendelkeznek.

### <a name="example-2-time-series-id-with-a-composite-key"></a>2. példa: idősorozat-azonosító összetett kulccsal

* Több tulajdonságot is meg kell adni az azonos adatflottaon belül.
* Az intelligens épületek gyártója és az érzékelők üzembe helyezése minden helyiségben. Minden helyiségben általában ugyanazok az értékek szerepelnek a **sensorId**. Ilyenek például a következők: **sensor1**, **sensor2** és **sensor3**.
* Az épület átfedésben van az alapszintű és a szobai számokkal a **flrRm** tulajdonságban található helyek között. Ezek a számok olyan értékekkel rendelkeznek, mint az **1a**, **2b** és **3A**.
* Van egy tulajdonsága, **helye**, amely olyan értékeket tartalmaz, mint a **Redmond**, a **Barcelona** és a **Tokió**. Az egyediség létrehozásához a következő három tulajdonságot kell kijelölnie idősorozat-azonosító kulcsaiként: **sensorId**, **flrRm** és **Location**.

Példa nyers eseményre:

```JSON
{
  "sensorId": "sensor1",
  "flrRm": "1a",
  "location": "Redmond",
  "temperature": 78
}
```

A Azure Portal az alábbi módon adhatja meg az összetett kulcsot:

[![Állítsa be a környezet idősorozat-AZONOSÍTÓját.](media/v2-how-to-tsid/configure-environment-key.png)](media/v2-how-to-tsid/configure-environment-key.png#lightbox)

  > [!NOTE]
  > A Azure Portalban ne adja meg a tulajdonságok vesszővel tagolt nevét egy texbox, ellenkező esetben a rendszer egyetlen, vesszőt tartalmazó tulajdonságnévként kezeli azt.
  > Adja meg a tulajdonságok nevét a saját texbox.

## <a name="next-steps"></a>Következő lépések

* Olvassa el a [JSON-összeolvasztási és-Escape-szabályokat](./concepts-json-flattening-escaping-rules.md) , hogy megtudja, hogyan lesznek tárolva az események.

* Tervezze meg [Azure Time Series Insights Gen2-környezetét](./how-to-plan-your-environment.md).