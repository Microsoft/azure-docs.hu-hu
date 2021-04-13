---
title: Az adatok megőrzésének megértése a környezetben – Azure Time Series Insight | Microsoft Docs
description: Ez a cikk két olyan beállítást ismertet, amelyek az adatmegőrzést vezérlik Azure Time Series Insights-környezetben.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 26363031aea37c53cce098e2b6cbc2b4d93b918f
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307078"
---
# <a name="understand-data-retention-in-azure-time-series-insights-gen1"></a>Az adatmegőrzés ismertetése Azure Time Series Insights Gen1

> [!CAUTION]
> Ez egy Gen1-cikk.

Ez a cikk két olyan elsődleges beállítást ismertet, amelyek hatással vannak az adatmegőrzésre a Azure Time Series Insights-környezetben.

## <a name="video"></a>Videó

### <a name="the-following-video-summarizes-azure-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>Az alábbi videó összefoglalja Azure Time Series Insights adatmegőrzést, és azt, hogyan tervezze meg.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

Az Azure Time Series Insights környezetek mindegyike rendelkezik egy olyan beállítással, amely az **adatok megőrzési idejét** vezérli. Az érték 1 – 400 nap. Az adatok törlődnek a környezet tárolási kapacitása vagy a megőrzési időtartam alapján, attól függően, hogy melyik következik be először.

Emellett a Azure Time Series Insights-környezet **tárolási korlátja túllépte a működési** beállítást. A bejövő és a kiürítési viselkedést vezérli, ha elérik a környezetek maximális kapacitását. A konfigurálása során két viselkedés közül választhat:

- **Régi adattörlés** (alapértelmezett)  
- **Bejövő forgalom szüneteltetése**

> [!NOTE]
> Új környezet létrehozásakor alapértelmezés szerint a megőrzés a **régi adatok törlésére** van konfigurálva. Ez a beállítás a Azure Time Series Insights-környezet **configure (Konfigurálás** ) lapjának a Azure Portal használatával történő létrehozása után igény szerint állítható be.
>
> - Az adatmegőrzési szabályzatok konfigurálásával kapcsolatos információkért olvassa el a [Azure Time Series Insights megőrzésének konfigurálása](time-series-insights-how-to-configure-retention.md)című témakört.

Az adatmegőrzési szabályzatokat az alábbiakban részletesebben ismertetjük.

## <a name="purge-old-data"></a>Régi adattörlés

- Azure Time Series Insights környezetek esetében az alapértelmezett beállítás a **régi értékek törlése** .  
- A **régi adattörlést** akkor érdemes használni, ha a felhasználók mindig rendelkeznek a *legfrissebb* adatAzure Time Series Insights-környezetben.
- A **régi adattörlési** beállítás *kiüríti* az adatmennyiséget, ha a környezet korlátait (a megőrzési időt, a méretet vagy a darabszámot) eléri. Alapértelmezés szerint a megőrzés értéke 30 nap.
- A legrégebbi betöltött adatot a rendszer először kiüríti (a "először az elsőben" megközelítést).

### <a name="example-one"></a>Példa egy

Vegye figyelembe, hogy az adatmegőrzési viselkedést biztosító környezet **továbbra is beáramlik és törli a régi adatokból**:

Az **adatok megőrzési ideje** 400 napra van állítva. A **kapacitás** értéke S1 egység, amely 30 GB teljes kapacitást tartalmaz. Tegyük fel, hogy a bejövő adat átlagosan 500 MB-ra halmozódik. Ez a környezet a bejövő adatmennyiség miatt csak 60 napos adatot tud megőrizni, mivel a maximális kapacitás 60 nap. A bejövő adat a következőképpen halmozódik: 500 MB minden nap x 60 nap = 30 GB.

A 61st napján a környezet a legfrissebb adatforrásokat jeleníti meg, de a legrégebbi, 60 napnál régebbi adat kiürítését. A kiürítés lehetővé teszi az új adatfolyam-továbbítást a alkalmazásban, így továbbra is megvizsgálhatja az új adatátvitelt. Ha a felhasználó továbbra is meg szeretné őrizni az adatmennyiséget, a további egységek hozzáadásával növelheti a környezet méretét, vagy kevesebb adat leküldését teszi lehetővé.  

### <a name="example-two"></a>Példa kettőre

Vegye figyelembe, hogy a környezet is konfigurálta az adatmegőrzési viselkedést **, és a régi adatok kiürítését folytatja**. Ebben a példában az **adatmegőrzési idő** a 180 napos alacsonyabb értékre van állítva. A **kapacitás** értéke S1 egység, amely 30 GB teljes kapacitást tartalmaz. Ahhoz, hogy a teljes 180 napig tárolja az adattárolást, a napi bejövő forgalom napi szinten nem haladhatja meg az 0,166 GB-ot (166 MB).  

Ha a környezet napi beléptetési aránya meghaladja a napi 0,166 GB-ot, az adat nem tárolható 180 nap alatt, mivel egyes adatmennyiségek törlődnek. Egy foglalt időkereten belül ugyanezt a környezetet érdemes figyelembe venni. Tegyük fel, hogy a környezet beléptetési aránya a napi átlagos 0,189 GB-ra nő. Ebben a foglalt időkeretben körülbelül 158 nap adat marad meg (30 GB/0.189 = 158,73 nap). Ez az idő kevesebb, mint a kívánt adatmegőrzési időszak.

## <a name="pause-ingress"></a>Bejövő forgalom szüneteltetése

- A **Bejövő forgalom felfüggesztése** beállítás úgy van kialakítva, hogy az adatok ne legyenek kiürítve, ha a méret és a szám a megőrzési időtartam előtt eléri a korlátot.  
- A **felfüggesztett behatolások** további időt biztosítanak a felhasználók számára a környezet kapacitásának növelésére, mielőtt az adatok megmaradnak a megőrzési idő megszegése miatt.
- Segít az adatvesztés elleni védelemben, de létrehozhat egy lehetőséget a legutóbbi adatok elvesztésére, ha a bejövő forgalom szünetel az eseményforrás megőrzési időtartamán túl.
- A környezet maximális kapacitásának elérésekor azonban a környezet szünetelteti az adatbevitelt, amíg a következő további műveletek nem történnek:

  - A környezet maximális kapacitásának növelésével további méretezési egységeket adhat hozzá a [Azure Time Series Insights-környezet skálázása](time-series-insights-how-to-scale-your-environment.md)című témakörben leírtak szerint.
  - A rendszer elérte az adatmegőrzési időszakot, és az adatok törlődnek, így a környezet a maximális kapacitás alatt marad.

### <a name="example-three"></a>Harmadik példa

Vegye fontolóra egy olyan környezet megőrzési viselkedését, amely a **Bejövő forgalom szüneteltetésére** van konfigurálva. Ebben a példában az **adatmegőrzési időszak** 60 napra van konfigurálva. A **kapacitás** értéke az S1 három (3) egysége. Tegyük fel, hogy a környezet minden nap 2 GB-os adatforgalommal rendelkezik. Ebben a környezetben a bejövő forgalom szünetel a maximális kapacitás elérésekor.

Ebben az időben a környezet ugyanazt az adatkészletet jeleníti meg, amíg a bejövő állapot folytatódik, vagy amíg be nem fejeződik a **bejövő** forgalom engedélyezése (ami a régebbi adatokat törli, hogy helyet szabadítson fel az új adatokat).

A bejövő forgalom folytatásakor:

- Az adatfolyamatok az eseményforrás által fogadott sorrendben
- Az események az időbélyegük alapján vannak indexelve, kivéve, ha túllépte az adott eseményforrás adatmegőrzési szabályzatait. További információ az eseményforrás-megőrzési konfigurációról: [Event HUBS GYIK](../event-hubs/event-hubs-faq.yml)

> [!IMPORTANT]
> Riasztásokat kell beállítania, hogy értesítést kapjon a bejövő forgalom szüneteltetésének elkerüléséhez. Az adatvesztés lehetséges, mivel az alapértelmezett megőrzési idő 1 nap az Azure-eseményeknél. Ezért a bejövő forgalom felfüggesztése után valószínűleg elveszíti a legutóbbi adatvesztést, kivéve, ha további műveletet végez. Az adatvesztés elkerülése érdekében növelje a kapacitást, vagy váltson viselkedésre a **régi adatmennyiség törléséhez** .

Az érintett Event Hubsban érdemes lehet módosítani az üzenet- **megőrzési** tulajdonságot, hogy az adatvesztés minimálisra csökkenjen, ha az Azure Time Series Insights.

[![Event hub-üzenetek megőrzése.](media/time-series-insights-concepts-retention/event-hub-retention.png)](media/time-series-insights-concepts-retention/event-hub-retention.png#lightbox)

Ha az eseményforrás () egyik tulajdonság sincs konfigurálva `timeStampPropertyName` , Azure Time Series Insights alapértelmezett érték az Event hub-ban az X tengelyként való érkezés időbélyege. Ha `timeStampPropertyName` úgy van konfigurálva, hogy valami más legyen, a környezet az `timeStampPropertyName` események elemzésekor az adatcsomagban konfigurált konfigurációt keresi.

Olvassa el, [Hogyan méretezheti Azure Time Series Insights-környezetét](time-series-insights-how-to-scale-your-environment.md) úgy, hogy a környezetét a további kapacitásra vagy a megőrzési időtartamra növelje.

## <a name="next-steps"></a>Következő lépések

- Az adatmegőrzési beállítások konfigurálásával és módosításával kapcsolatos információkért tekintse át [az adatmegőrzés konfigurálása Azure Time Series Insightsban](time-series-insights-how-to-configure-retention.md)című témakört.

- Ismerje meg a [Azure Time Series Insights késésének csökkentését](time-series-insights-environment-mitigate-latency.md).
