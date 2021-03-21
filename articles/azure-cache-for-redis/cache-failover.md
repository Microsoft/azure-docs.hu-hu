---
title: Feladatátvétel és javítás – Azure Cache for Redis
description: Ismerje meg a feladatátvételt, a javítást, valamint az Azure cache Redis-hez készült frissítési folyamatát.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: cc7c70fa2e7131f09f621e992d537e0b120061ef
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102210733"
---
# <a name="failover-and-patching-for-azure-cache-for-redis"></a>Feladatátvétel és javítás a Redis készült Azure cache-hez

Rugalmas és sikeres ügyfélalkalmazások kiépítéséhez elengedhetetlen a feladatátvétel megismerése az Azure cache for Redis szolgáltatás kontextusában. A feladatátvétel a tervezett felügyeleti műveletek része lehet, vagy a nem tervezett hardver-vagy hálózati hibák okozzák. A gyorsítótár-feladatátvétel gyakori használata akkor következik be, amikor a felügyeleti szolgáltatás az Azure cache-t az Redis bináris fájljaihoz is felhasználja. Ez a cikk ismerteti a feladatátvételt, a javítások során felmerülő tudnivalókat és a rugalmas ügyfélalkalmazás felépítését.

## <a name="what-is-a-failover"></a>Mi az a feladatátvétel?

Kezdjük azzal, hogy áttekintjük az Azure cache Redis-hez készült feladatátvételét.

### <a name="a-quick-summary-of-cache-architecture"></a>A gyorsítótár-architektúra gyors összefoglalása

A gyorsítótár több, különálló, magánhálózati IP-címmel rendelkező virtuális gépről épül fel. Minden virtuális gép (más néven csomópont) egy megosztott terheléselosztó egyetlen virtuális IP-címmel van csatlakoztatva. Mindegyik csomópont futtatja a Redis-kiszolgáló folyamatát, és az állomásnév és a Redis-portok segítségével érhető el. Az egyes csomópontok elsődleges vagy replika csomópontnak tekintendők. Amikor egy ügyfélalkalmazás egy gyorsítótárhoz csatlakozik, a forgalma áthalad ezen a terheléselosztóon, és automatikusan átirányítja az elsődleges csomóponthoz.

Alapszintű gyorsítótárban az egyetlen csomópont mindig elsődleges. Standard vagy prémium szintű gyorsítótárban két csomópont létezik: az egyiket az elsődlegesként, a másik pedig a replikát választja. Mivel a standard és a prémium gyorsítótárak több csomóponttal rendelkeznek, előfordulhat, hogy egy csomópont nem érhető el, míg a másik folytatja a kérelmek feldolgozását. A fürtözött gyorsítótárak számos szegmensből állnak, amelyek mindegyike különböző elsődleges és replika csomópontokkal rendelkezik. Előfordulhat, hogy az egyik szegmens nem áll le, amíg a többiek elérhetők maradnak.

> [!NOTE]
> Az alapszintű gyorsítótár nem rendelkezik több csomóponttal, és nem biztosít szolgáltatói szerződést (SLA) a rendelkezésre állásához. Az alapszintű gyorsítótárak használata csak fejlesztési és tesztelési célokra ajánlott. A rendelkezésre állás növeléséhez használjon standard vagy prémium szintű gyorsítótárat a több csomópontos telepítéshez.

### <a name="explanation-of-a-failover"></a>A feladatátvétel magyarázata

Feladatátvétel akkor történik, amikor egy replika csomópontja maga is elsődleges csomópontnak válik, és a régi elsődleges csomópont lezárja a meglévő kapcsolatokat. Az elsődleges csomópont biztonsági mentése után a megfigyeli a szerepkörök változását, és lefokozza magát replikaként. Ezután csatlakozik az új elsődlegeshez, és szinkronizálja az adatokat. Előfordulhat, hogy A feladatátvétel tervezett vagy nem tervezett.

Egy *tervezett feladatátvétel* a rendszerfrissítések során történik, például a Redis javításához vagy az operációs rendszer frissítéseihez, valamint a felügyeleti műveletekhez, például a skálázáshoz és az újraindításhoz. Mivel a csomópontok előzetes értesítést kapnak a frissítésről, a szerepkörök együttműködési felcserélése és a változás terheléselosztó gyors frissítése is megtörtént. A tervezett feladatátvétel általában kevesebb, mint 1 másodperc alatt fejeződik be.

A nem *tervezett feladatátvétel* a hardverhiba, a hálózati hiba vagy az elsődleges csomópont váratlan kimaradásai miatt fordulhat elő. A replika csomópont az elsődlegesre is serkenti, de a folyamat tovább tart. A replika csomópontnak először azt kell megállapítania, hogy az elsődleges csomópontja nem érhető el, mielőtt kezdeményezni tudja a feladatátvételi folyamatot. A replika csomópontnak azt is ellenőriznie kell, hogy ez a nem tervezett hiba nem átmeneti vagy helyi, a szükségtelen feladatátvétel elkerülése érdekében. Ez az észlelési késleltetés azt jelenti, hogy egy nem tervezett feladatátvétel általában 10 – 15 másodpercen belül fejeződik be.

## <a name="how-does-patching-occur"></a>Hogyan történik a javítás?

A Redis szolgáltatáshoz készült Azure cache rendszeresen frissíti a gyorsítótárat a legújabb platform-funkciókkal és javításokkal. A gyorsítótár javításához a szolgáltatás a következő lépéseket követi:

1. A kezelési szolgáltatás kiválasztja az egyik csomópontot a javításhoz.
1. Ha a kijelölt csomópont egy elsődleges csomópont, a megfelelő replika csomópont is támogatja magát. Ez az előléptetés tervezett feladatátvételnek minősül.
1. A kiválasztott csomópont újraindul az új módosítások elvégzéséhez, és biztonsági másolatot készít a replika csomópontként.
1. A replika csomópont csatlakozik az elsődleges csomóponthoz, és szinkronizálja az adatokat.
1. Az adatszinkronizálás befejezésekor a javítási folyamat megismétli a többi csomópontot.

Mivel a javítások tervezett feladatátvételt végeznek, a replika csomópont gyorsan elősegíti az elsődleges és a karbantartási kérelmek és az új kapcsolatok megkezdését. Az alapszintű gyorsítótárak nem rendelkeznek replika-csomóponttal, és nem érhetők el, amíg a frissítés be nem fejeződik. A fürtözött gyorsítótár minden szegmense külön van kijavítottan, és nem zárul a kapcsolat egy másik szegmenshez.

> [!IMPORTANT]
> Az adatvesztés elkerülése érdekében a csomópontok egyenként is kijavítottak. Az alapszintű gyorsítótárak adatvesztéssel fognak rendelkezni. A fürtözött gyorsítótárak egyszerre csak egy szegmenst javítottak.

Egyszerre több gyorsítótár is található egy adott erőforráscsoport és régió esetében.  A különböző erőforráscsoportok vagy különböző régiókba tartozó gyorsítótárak egyidejűleg is kijavíthatók.

Mivel a folyamat megismétlése előtt a teljes adatszinkronizálás történik, az adatvesztés nem valószínű, ha standard vagy prémium szintű gyorsítótárat használ. További védelmet biztosíthat az adatvesztés ellen az [adatexportálás](cache-how-to-import-export-data.md#export) és az [adatmegőrzés](cache-how-to-premium-persistence.md)engedélyezése mellett.

## <a name="additional-cache-load"></a>További gyorsítótár-betöltés

Ha feladatátvétel történik, a standard és a prémium gyorsítótárnak az egyik csomópontról a másikra kell replikálnia az adatait. Ez a replikálás némi terhelést okoz a kiszolgáló memóriájában és a CPU-ban is. Ha a gyorsítótár-példány már be van töltve, az ügyfélalkalmazások nagyobb késéssel találkozhatnak. Szélsőséges esetekben előfordulhat, hogy az ügyfélalkalmazások időtúllépési kivételeket kapnak. A további terhelés hatásának enyhítése érdekében [konfigurálja](cache-configure.md#memory-policies) a gyorsítótár `maxmemory-reserved` beállításait.

## <a name="how-does-a-failover-affect-my-client-application"></a>Hogyan befolyásolja a feladatátvétel az ügyfélalkalmazás alkalmazását?

Az ügyfélalkalmazás által látott hibák száma attól függ, hogy a feladatátvétel során hány művelet van függőben a kapcsolatban. Az összes olyan kapcsolat, amely a kapcsolat lezárt csomópontján keresztül van átirányítva, hibaüzeneteket fog látni. Számos ügyfél-függvénytár különböző típusú hibákat tud eldobni a kapcsolatok megszakításakor, beleértve az időtúllépési kivételeket, a csatlakozási kivételeket vagy a szoftvercsatorna kivételeit. A kivételek száma és típusa attól függ, hogy hol található a kód elérési útja, amikor a gyorsítótár lezárja a kapcsolatait. Például egy olyan művelet, amely kérelmet küld, de nem kapott választ, ha a feladatátvétel során időtúllépési kivételt tapasztalhat. A lezárt kapcsolatok objektumon lévő új kérések a kapcsolatok kivételeit kapják, amíg az újracsatlakoztatás sikeresen megtörténik.

A legtöbb ügyfél-függvénytár megpróbál újracsatlakozni a gyorsítótárhoz, ha erre van konfigurálva. Az előre nem látható hibák azonban esetenként nem állíthatók helyre a függvénytár-objektumokat. Ha a hibák hosszabb ideig tartanak, mint az előre beállított időtartam, a rendszer újból létrehozza a kapcsolatok objektumát. A Microsoft.NET és más objektumorientált nyelveken a kapcsolatok újraindítása nélkül, [lusta \<T\> minta](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#reconnecting-with-lazyt-pattern)használatával lehet újból létrehozni a kapcsolatokat.

### <a name="how-do-i-make-my-application-resilient"></a>Hogyan rugalmasan tehetem az alkalmazást?

Mivel nem tudja teljesen elkerülni a feladatátvételt, megírhatja az ügyfélalkalmazások rugalmasságát a kapcsolódási szünetek és a sikertelen kérelmek esetében. Bár a legtöbb ügyfél-függvénytár automatikusan újracsatlakozik a gyorsítótár-végponthoz, néhányan megpróbálják újrapróbálkozni a sikertelen kérelmeket. Az alkalmazás forgatókönyvének függvényében érdemes lehet az újrapróbálkozási logikát használni a leállítási.

Az ügyfélalkalmazás rugalmasságának teszteléséhez használjon [újraindítást](cache-administration.md#reboot) manuális triggerként a kapcsolódási szünetekhez. Javasoljuk továbbá, hogy a frissítéseket a gyorsítótárban [ütemezze](cache-administration.md#schedule-updates) . Adja meg a kezelési szolgáltatást, hogy Redis-futtatókörnyezeti javításokat alkalmazzon a megadott heti Windows rendszerben. Ezek a Windows-alkalmazások jellemzően olyan időszakok, amikor az ügyfélalkalmazások forgalma alacsony, így elkerülhetők a lehetséges incidensek.

### <a name="can-i-be-notified-in-advance-of-a-planned-maintenance"></a>Előre értesítést kaphatok a tervezett karbantartásról?

A Redis-hez készült Azure cache mostantól a tervezett frissítések előtt 30 másodpercen belül közzéteszi az [AzureRedisEvents](https://github.com/Azure/AzureCacheForRedis/blob/main/AzureRedisEvents.md) nevű közzétételi és előfizetési csatornán beérkező értesítéseket. Ezek a futásidejű értesítések, és főleg olyan alkalmazások számára készültek, amelyek áramkör-megszakítók használatával megkerülhetik a gyorsítótár-és a puffer parancsait, például a tervezett frissítések során. Ez nem egy olyan mechanizmus, amely nem tud értesíteni a napokat vagy órákat előre.

### <a name="client-network-configuration-changes"></a>Ügyfél hálózata – konfigurációs változások

Bizonyos ügyféloldali hálózat – a konfigurációs változások "nincs elérhető elérhetőség" hibaüzenetet válthatnak ki. Ilyen változások például a következők lehetnek:

- Egy ügyfélalkalmazás virtuális IP-címének cseréje átmeneti és üzemi tárolóhelyek között.
- Az alkalmazáshoz tartozó példányok méretének vagy számának skálázása.

Ezek a változások olyan kapcsolódási problémát okozhatnak, amely egy percnél rövidebb ideig tart. Az ügyfélalkalmazás valószínűleg elveszti a kapcsolatát más külső hálózati erőforrásokkal, a Redis szolgáltatáshoz készült Azure cache mellett.

## <a name="next-steps"></a>Következő lépések

- A gyorsítótár [frissítéseinek ütemezett időpontja](cache-administration.md#schedule-updates) .
- Az alkalmazások rugalmasságának tesztelése [Újraindítás](cache-administration.md#reboot)használatával.
- Memória-foglalások és-házirendek [konfigurálása](cache-configure.md#memory-policies) .
