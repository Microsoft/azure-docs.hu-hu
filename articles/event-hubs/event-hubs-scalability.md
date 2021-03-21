---
title: Skálázhatóság – Azure Event Hubs | Microsoft Docs
description: Ez a cikk az Azure-Event Hubs partíciók és átviteli egységek használatával történő skálázását ismerteti.
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: f258ee2a3b4162dabf7a8e615db82b9b889d628b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103601278"
---
# <a name="scaling-with-event-hubs"></a>Méretezés Event Hubs

Két tényező befolyásolja a skálázást Event Hubs.
*   Átviteli egységek
*   Partíciók

## <a name="throughput-units"></a>Átviteli egységek

A Event Hubs átviteli kapacitását az *átviteli egységek* vezérlik. Az átviteli egységek előre megvásárolt kapacitásegységek. Egyetlen átviteli sebesség A következőket teszi lehetővé:

* Bejövő forgalom: másodpercenként legfeljebb 1 MB vagy 1000 esemény másodpercenként (amelyik előbb bekövetkezik).
* Kimenő forgalom: másodpercenként legfeljebb 2 MB vagy 4096 esemény másodpercenként.

A megvásárolt átviteli egységek kapacitásán túli bemenő forgalmat a rendszer korlátozza, és [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) választ ad vissza. A kimenő forgalom nem eredményez korlátozási kivételeket, azonban a megvásárolt átviteli egységek kapacitására van korlátozva. Ha közzétételi sebességhez kapcsolódó kivételeket kap, vagy nagyobb kimenő forgalomra számított, ellenőrizze, hány átviteli egységet vásárolt a névtérhez. Az átviteli egységek kezelése a névterek **Méretezés** panelén történik az [Azure Portalon](https://portal.azure.com). Az átviteli egységeket programozott módon is kezelheti a [Event Hubs API](./event-hubs-samples.md)-k használatával.

Az átviteli egységek előzetes megvásárlása és óránkénti számlázása. Miután megvásárolta, az átviteli egységek után legalább egy órányi díjat ki kell fizetni. Egy Event Hubs névtérhez legfeljebb 20 átviteli egység vásárolható meg, és az adott névtérben található összes Event hub esetében meg van osztva.

A Event Hubs **automatikus** feltöltési funkciója automatikusan méretezi az átviteli egységek számának növelésével, hogy megfeleljen a használati igényeknek. Az átviteli egységek növelése megakadályozza a szabályozást, amelyben:

- A bejövő adatforgalom aránya meghaladja a beállított átviteli egységeket.
- A kimenő adatforgalomra vonatkozó kérelmek aránya meghaladja a set átviteli egységeket.

A Event Hubs szolgáltatás növeli az átviteli sebességet, ha a terhelés a minimális küszöbértéknél nagyobb mértékben növekszik, anélkül, hogy a ServerBusy hibákkal kapcsolatos kérések sikertelenek lesznek. 

További információ az automatikus kiosztási szolgáltatásról: az [átviteli egységek automatikus skálázása](event-hubs-auto-inflate.md).

## <a name="partitions"></a>Partíciók
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]




## <a name="next-steps"></a>Következő lépések
Az alábbi webhelyeken további információt talál az Event Hubsról:

- [Átviteli egységek automatikus skálázása](event-hubs-auto-inflate.md)
- [Event Hubs szolgáltatás áttekintése](./event-hubs-about.md)
