---
title: Azure cache Redis-figyeléshez és hibaelhárításhoz – GYIK
description: A Redis Azure cache figyelését és hibakeresését segítő gyakori kérdésekre adott válaszok ismertetése
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 8e96c73578a9341f67d90cd4482ed75179c6886d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92537524"
---
# <a name="azure-cache-for-redis-monitoring-and-troubleshooting-faqs"></a>Azure cache Redis-figyeléshez és hibaelhárításhoz – GYIK
Ez a cikk a Redis Azure cache figyelésével és hibakeresésével kapcsolatos gyakori kérdésekre ad választ.

## <a name="common-questions-and-answers"></a>Gyakori kérdések és válaszok
Ez a szakasz a következő gyakori kérdéseket ismerteti:

* [Hogyan figyeli a gyorsítótár állapotát és teljesítményét?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [Miért látok időtúllépéseket?](#why-am-i-seeing-timeouts)
* [Miért bontottam le az ügyfelem a gyorsítótárat?](#why-was-my-client-disconnected-from-the-cache)

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Hogyan figyeli a gyorsítótár állapotát és teljesítményét?
Az Redis-példányok Microsoft Azure gyorsítótára figyelhető a [Azure Portalban](https://portal.azure.com). Megtekintheti a metrikákat, rögzítheti a metrikák diagramokat a Kezdőpulton, testreszabhatja a figyelési diagramok dátumát és időtartományát, metrikákat adhat hozzá és távolíthat el a diagramokból, valamint riasztásokat állíthat be bizonyos feltételek teljesülése esetén. További információ: [Az Azure cache figyelése a Redis](cache-how-to-monitor.md).

Az Azure cache for Redis **erőforrás menü** számos eszközt is tartalmaz a gyorsítótárak figyeléséhez és hibaelhárításához.

* A **problémák diagnosztizálása és megoldása** a gyakori problémákról és stratégiákról nyújt információkat.
* A **Resource Health** figyeli az erőforrást, és jelzi, hogy a várt módon fut-e. Az Azure Resource Health szolgáltatással kapcsolatos további információkért lásd: az [Azure Resource Health áttekintése](../service-health/resource-health-overview.md).
* Az **új támogatási kérelem** lehetőséget biztosít a gyorsítótárhoz tartozó támogatási kérelem megnyitására.

Ezek az eszközök lehetővé teszik az Azure cache Redis-példányok állapotának figyelését, valamint a gyorsítótárazási alkalmazások kezelését. További információ: az [Azure cache konfigurálása a Redis-hoz](cache-configure.md)– a "támogatási & hibaelhárítási beállítások" című szakasz.

### <a name="why-am-i-seeing-timeouts"></a>Miért látok időtúllépéseket?
Időtúllépések történnek abban az ügyfélben, amelyet a Redis való kommunikációhoz használ. Ha egy parancsot a Redis-kiszolgálónak továbbítanak, a parancs várólistára kerül, és a Redis-kiszolgáló végül felveszi a parancsot, és végrehajtja azt. Az ügyfél azonban időtúllépést okozhat a folyamat során, és ha kivétel történik a hívási oldalon. Az időtúllépési problémák elhárításával kapcsolatos további információkért lásd: [ügyféloldali hibaelhárítás](cache-troubleshoot-client.md) és [StackExchange. Redis időtúllépési kivételek](cache-troubleshoot-timeouts.md#stackexchangeredis-timeout-exceptions).

### <a name="why-was-my-client-disconnected-from-the-cache"></a>Miért bontottam le az ügyfelem a gyorsítótárat?
A gyorsítótár leválasztásának néhány gyakori oka a következő:

* Ügyféloldali okok
  * Az ügyfélalkalmazás újra lett telepítve.
  * Az ügyfélalkalmazás skálázási műveletet hajtott végre.
    * Cloud Services vagy Web Apps esetén ennek oka az automatikus skálázás lehet.
  * A hálózati réteg módosult az ügyfél oldalán.
  * Átmeneti hibák fordultak elő az ügyfélen vagy a hálózati csomópontok között az ügyfél és a kiszolgáló között.
  * Elérte a sávszélességre vonatkozó küszöbértéket.
  * A CPU-kötésű műveletek végrehajtása túl sokáig tartott.
* Kiszolgálóoldali okok
  * A standard szintű gyorsítótár-ajánlat esetében az Azure cache for Redis szolgáltatás egy feladatátvételt kezdeményezett az elsődleges csomópontról a replika csomópontra.
  * Az Azure a gyorsítótár üzembe helyezési példányának javítását használta
    * Ez lehet a Redis-kiszolgáló frissítései vagy az általános virtuális gép karbantartása.


## <a name="next-steps"></a>Következő lépések

Az Azure cache Redis-példányok figyelésével és hibaelhárításával kapcsolatos további információkért lásd: az [Azure cache figyelése a Redis](cache-how-to-monitor.md) és a különböző hibaelhárítási útmutatókhoz.

További információ [Az Azure cache Redis-ről – gyakori kérdések](cache-faq.md).