---
title: Adatfolyam-betöltési átviteli sebesség korlátozásai – Azure Time Series Insights Gen2 | Microsoft Docs
description: További információ a bejövő átviteli sebesség korlátairól Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/21/2021
ms.custom: seodec18
ms.openlocfilehash: 3c938e7fe180e6a9cf08031a2a4565486f3f77a9
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504634"
---
# <a name="streaming-ingestion-throughput-limits"></a>Adatfolyam-betöltési átviteli sebesség korlátai

Azure Time Series Insights Gen2 adatfolyam-továbbítási korlátozásokat az alábbiakban olvashat.

> [!TIP]
> Az összes korlát átfogó listájáért olvassa el [a Azure Time Series Insights Gen2-környezet megtervezése](./how-to-plan-your-environment.md#review-azure-time-series-insights-gen2-limits) című részét.

## <a name="per-environment-limitations"></a>/Környezet korlátozásai

Általánosságban elmondható, hogy a bejövő forgalom díjszabása a szervezet eszközeinek száma, az esemény-emisszió gyakorisága, valamint az egyes események mérete:

* **Az eszközök száma** × **esemény kibocsátásának gyakorisága** × **az egyes események mérete**.

Alapértelmezés szerint a Azure Time Series Insights Gen2 **legfeljebb 1 megabájt/másodperc (Mbps)** sebességgel képes befogadni a bejövő adatot Azure Time Series Insights Gen2-környezetben. A [hub-partíciók esetében](./concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits)további korlátozások vannak érvényben.

> [!TIP]
>
> * A legfeljebb 2 MBps sebességű feltöltési környezet támogatása kéréssel biztosítható.
> * Vegye fel velünk a kapcsolatot, ha magasabb átviteli sebességre van szüksége egy támogatási jegynek a Azure Portalon keresztüli elküldésével.

* **1. példa:**

    A contoso szállítása 100 000 olyan eszközzel rendelkezik, amely percenként három alkalommal bocsát ki eseményt. Az események mérete 200 bájt. A IoT Hub négy partíciót használnak a Azure Time Series Insights Gen2-esemény forrásaként.

  * A Azure Time Series Insights Gen2-környezet betöltési sebessége a következő: **100 000-es eszközök * 200 bájt/esemény * (3/60 esemény/másodperc) = 1 Mbps**.
    * A kiegyensúlyozott partíciót feltételezve, hogy a partíciók terhelési aránya 0,25 MB/s.
    * A contoso szállításának betöltési aránya a skálázási korlátokon belül lenne.

* **2. példa:**

    A contoso Fleet Analytics 10 000 olyan eszközt tartalmaz, amely másodpercenként egy eseményt bocsát ki. Egy Event hub-t használnak, amelynek a partícióinak száma 2, Azure Time Series Insights Gen2 eseményforrás. Az események mérete 200 bájt.

  * A környezet betöltési sebessége a következő: **10 000 eszköz * 200 bájt/esemény * 1 esemény/MP = 2 Mbps**.
    * A kiegyensúlyozott partíciót feltételezve, hogy a partíciók aránya 1 MB/s.
    * A contoso Fleet Analytics betöltési aránya a környezet és a partíciók korlátain alapul. Kérést küldhetnek Azure Time Series Insights Gen2 a Azure Portalon keresztül, hogy növeljék a környezet betöltési arányát, és hozzon létre egy olyan Event hub-t, amely a határértékeken belül több partícióval rendelkezik.

## <a name="hub-partitions-and-per-partition-limits"></a>Hub-partíciók és partíciós korlátok

A Azure Time Series Insights Gen2-környezet tervezésekor fontos figyelembe venni azon eseményforrás (ok) konfigurációját, amelyekhez csatlakozni fog Azure Time Series Insights Gen2. Mind az Azure-IoT Hub, mind a Event Hubs partíciókat használ az események feldolgozásához szükséges horizontális méretezés lehetővé tételéhez.

A *partíciók* a központban tárolt események rendezett sorrendje. A partíciók száma a központ létrehozási fázisában van beállítva, és nem módosítható.

Event Hubs particionálással kapcsolatos ajánlott eljárások esetében ellenőrizze, [hogy hány partícióra van szükségem?](../event-hubs/event-hubs-faq.md#how-many-partitions-do-i-need)

> [!NOTE]
> Azure Time Series Insights Gen2 használt legtöbb IoT hub-nak csak négy partícióra van szüksége.

Akár új hubot hoz létre a Azure Time Series Insights Gen2-környezethez, vagy egy meglévőt használ, ki kell számítania a partíciók betöltési arányát annak megállapítására, hogy az a határértékeken belül van-e.

Azure Time Series Insights Gen2 jelenleg a **0,5 Mbps-ra vonatkozó általános korláttal** rendelkezik.

### <a name="iot-hub-specific-considerations"></a>IoT Hub-specifikus megfontolások

Ha egy eszköz a IoT Hubban jön létre, akkor véglegesen hozzá van rendelve egy partícióhoz. Ennek során a IoT Hub képes biztosítani az események rendezését (mivel a hozzárendelés soha nem változik).

A rögzített partíciós hozzárendelés olyan Azure Time Series Insights Gen2-példányokat is érint, amelyek IoT Hub alsóbb rétegből érkező adatok betöltését használják. Ha több eszközről származó üzeneteket továbbítanak a központba ugyanazzal az átjáró-eszköz azonosítójával, akkor előfordulhat, hogy ugyanazon a partíción érkeznek, amely a partíciós méretezési korlátokat is meghaladja.

**Hatás**:

* Ha egy partíció tartósan betöltési sebességet tapasztal a korláton keresztül, lehetséges, hogy Azure Time Series Insights Gen2 nem fogja szinkronizálni az összes eszközt a IoT Hub adatmegőrzési időszak túllépése előtt. Ennek eredményeképpen a továbbított adatmennyiségek elvesznek, ha a betöltési korlátokat folyamatosan túllépik.

Ennek a körülménynek a mérséklése érdekében a következő ajánlott eljárásokat javasoljuk:

* A megoldás üzembe helyezése előtt számítsa ki a környezet és a partíciók betöltési díjait.
* Győződjön meg arról, hogy a IoT Hub-eszközök terheléselosztása a lehető legtávolabbi mértékben történik.

> [!IMPORTANT]
> Az IoT Hub esemény forrásaként használó környezetek esetében a használatban lévő hub-eszközök számával Számítsuk ki a betöltési arányt, hogy a sebesség a partícióra vonatkozó korlátozás szerint a 0,5 MBps-ra essen.
>
> * Még ha több esemény is érkezik egyszerre, a korlátot nem fogja meghaladni.

  ![IoT Hub partíciós diagram](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

Az alábbi forrásokból tájékozódhat a hub átviteli sebességének és partícióinak optimalizálásáról:

* [IoT Hub skála](../iot-hub/iot-hub-scaling.md)
* [Event hub-skála](../event-hubs/event-hubs-scalability.md#throughput-units)
* [Event hub-partíciók](../event-hubs/event-hubs-features.md#partitions)

## <a name="next-steps"></a>Következő lépések

* További információ az [adattárolásról](./concepts-storage.md)
