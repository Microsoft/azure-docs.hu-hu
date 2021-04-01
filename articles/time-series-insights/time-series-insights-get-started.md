---
title: Környezet létrehozása – Azure Time Series Insights | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre új Azure Time Series Insights-környezetet a Azure Portal használatával.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 61af7922318514a7b86a349d1970c59d4d168d85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95023326"
---
# <a name="create-a-new-azure-time-series-insights-gen1-environment-in-the-azure-portal"></a>Új Azure Time Series Insights Gen1-környezet létrehozása a Azure Portal

> [!CAUTION]
> Ez egy Gen1-cikk.

Ez a cikk azt ismerteti, hogyan hozható létre új Azure Time Series Insights-környezet a Azure Portal használatával.

Azure Time Series Insights lehetővé teszi az Azure IoT-Event Hubs hubokba való adatforgalom megjelenítésének és lekérdezésének megkezdését percek alatt, így másodpercek alatt lekérdezheti a nagy mennyiségű idősoros adatsorozatot.  A szolgáltatás az IoT-méretezéshez lett tervezve, és képes terabájtos adatmennyiség kezelésére.

## <a name="steps-to-create-the-environment"></a>A környezet létrehozásának lépései

A következő lépések végrehajtásával hozhat létre környezetet:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza az **+ erőforrás létrehozása** gombot.

1. Válassza ki a **eszközök internetes hálózata** kategóriát, és válassza a **Time Series Insights** lehetőséget.

   [![A Azure Time Series Insights-környezet létrehozása](media/time-series-insights-get-started/tsi-create-new-environment.png)](media/time-series-insights-get-started/tsi-create-new-environment.png#lightbox)

1. A **Time Series Insights** lapon válassza a **Létrehozás** lehetőséget.

1. Adja meg a szükséges paramétereket. Az alábbi táblázat az egyes paramétereket ismerteti:

   [![A Azure Time Series Insights erőforráscsoport létrehozása](media/time-series-insights-get-started/tsi-configure-and-create.png)](media/time-series-insights-get-started/tsi-configure-and-create.png#lightbox)

   Beállítás|Ajánlott érték|Leírás
   ---|---|---
   Környezet neve | Egyedi név | Ez a név jelenti a környezetet a [Time Series Explorerben](https://insights.timeseries.azure.com)
   Előfizetés | Az Ön előfizetése | Ha több előfizetéssel rendelkezik, válassza ki azt az előfizetést, amelyik az adott eseményforrás tartalmazza. A Azure Time Series Insights képes automatikusan felderíteni az Azure-IoT Hub és az Event hub-erőforrásokat ugyanabban az előfizetésben.
   Erőforráscsoport | Új létrehozása vagy meglévő használata | Az erőforráscsoport olyan Azure-erőforrások gyűjteménye, amelyeket együtt használnak. Kiválaszthat egy meglévő erőforráscsoportot, például azt, amely az Event hub-t vagy IoT Hub tartalmazza. Egy újat is létrehozhat, ha az erőforrás nem kapcsolódik a többi erőforráshoz.
   Hely | Az eseményforrás legközelebbi forrása | Lehetőleg válassza ki ugyanazt az adatközpont-helyet, amely tartalmazza az eseményforrás adatait, így elkerülhető, hogy a régióból kifelé irányuló adatáthelyezés során ne legyenek hozzáadva a régiók közötti és a zónák közötti sávszélesség-költségek.
   Tarifacsomag | S1 | Válassza ki a szükséges teljesítményt. A legalacsonyabb költségek és a kezdő kapacitás esetében válassza az S1 elemet.
   Kapacitás | 1 | A kapacitás a szorzó a bejövő forgalom arányára, a tárolókapacitásra és a kiválasztott SKU-ra vonatkozó díjakra vonatkozik.  A környezet kapacitása a létrehozást követően módosítható. A legalacsonyabb költségekhez válasszon 1 kapacitást.
  
1. A létesítési folyamat megkezdéséhez válassza a **Létrehozás** lehetőséget. Ez eltarthat pár percig.

1. Az üzembe helyezési folyamat figyeléséhez válassza az **értesítések** szimbólumot (harang ikon).

   [![Az értesítések megtekintése](media/time-series-insights-get-started/tsi-deploy-notifications.png)](media/time-series-insights-get-started/tsi-deploy-notifications.png#lightbox)

1. Az erőforrás- **áttekintésben** ellenőrizze az üzembe helyezés konfigurációs beállításait.

   [![A Azure Time Series Insights PIN-kód létrehozása az irányítópulton](media/time-series-insights-get-started/tsi-verify-deployment.png)](media/time-series-insights-get-started/tsi-verify-deployment.png#lightbox)

1. **(Nem kötelező)** Válassza a jobb felső sarokban található **rögzítés ikont** , hogy a későbbiekben könnyen hozzáférhessen a Azure Time Series Insights-környezethez.

## <a name="next-steps"></a>Következő lépések

* [Adja meg az adatelérési szabályzatokat](./concepts-access-policies.md) a környezet biztonságossá tételéhez.

* [Adja hozzá az Event hub-esemény forrását](./how-to-ingest-data-event-hub.md) a Azure Time Series Insights-környezethez.

* [Események küldése](time-series-insights-send-events.md) az esemény forrásának.

* A környezet megtekintése [Azure Time Series Insights Explorerben](https://insights.timeseries.azure.com).