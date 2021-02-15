---
title: Az adatmegőrzés konfigurálása – prémium szintű Azure cache a Redis-hez
description: Ismerje meg, hogyan konfigurálhatja és kezelheti az adatmegőrzést a prémium szintű Azure cache Redis-példányok esetében
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 58148e3a20ba41ae9707543be290f2d632cb1185
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100375289"
---
# <a name="configure-data-persistence-for-a-premium-azure-cache-for-redis-instance"></a>Az adatmegőrzés konfigurálása prémium szintű Azure cache esetén a Redis-példányhoz

A Redis megőrzése lehetővé teszi a Redis-ben tárolt [adatmegőrzést](https://redis.io/topics/persistence) . Pillanatképeket és biztonsági mentést is készíthet, amelyeket hardverhiba esetén betölthet. Ez óriási előnyt jelent az alapszintű és a standard szint esetében, ahol az összes adat a memóriában tárolódik, és előfordulhat, hogy a gyorsítótár-csomópontok leállításakor hiba történt. 

A Redis-hez készült Azure cache a következő modellek használatával nyújt Redis-megőrzést:

* **RDB-megőrzés** – ha a RDB (Redis-adatbázis) megőrzése konfigurálva van, az Azure cache a Redis számára megőrzi az Azure cache-t a Redis bináris formátumból a lemezre egy konfigurálható biztonsági mentési gyakoriság alapján. Ha végzetes esemény következik be, amely letiltja az elsődleges és a replika gyorsítótárat is, a rendszer a legutóbbi pillanatkép használatával újraépíti a gyorsítótárat. További információ az RDB megőrzésének [előnyeiről](https://redis.io/topics/persistence#rdb-advantages) és [hátrányairól](https://redis.io/topics/persistence#rdb-disadvantages) .
* **AOF-megőrzés** – ha a AOF (csak Hozzáfűzés) adatmegőrzés konfigurálva van, az Azure cache for Redis minden írási műveletet egy olyan naplóba ment, amely másodpercenként legalább egyszer ment egy Azure Storage-fiókba. Ha olyan katasztrofális esemény következik be, amely letiltja az elsődleges és a replika gyorsítótárat is, a gyorsítótárat a tárolt írási műveletek használatával rekonstruáljuk. További információ az AOF megőrzésének [előnyeiről](https://redis.io/topics/persistence#aof-advantages) és [hátrányairól](https://redis.io/topics/persistence#aof-disadvantages) .

Az adatmegőrzés a saját és felügyelt Azure Storage-fiókba írja a Redis-adatot. A gyorsítótár létrehozásakor és a meglévő prémium gyorsítótárak **erőforrás menüjében** konfigurálhatja az **új Azure cache for Redis** panelt.

> [!NOTE]
> 
> Az Azure Storage automatikusan titkosítja az adataikat, amikor azok megmaradnak. A titkosításhoz saját kulcsokat is használhat. További információ: [ügyfél által felügyelt kulcsok Azure Key Vault használatával](../storage/common/storage-service-encryption.md).
> 
> 

## <a name="set-up-data-persistence"></a>Az adatmegőrzés beállítása

1. Prémium gyorsítótár létrehozásához jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és válassza az **erőforrás létrehozása** lehetőséget. A gyorsítótárak létrehozására az Azure Portalon kívül a Resource Manager-sablonok, a PowerShell vagy az Azure parancssori felület is használható. A Redis készült Azure cache létrehozásával kapcsolatos további információkért lásd: [gyorsítótár létrehozása](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Erőforrás létrehozása.":::
   
2. Az **új** lapon válassza az **adatbázisok** lehetőséget, majd válassza az Azure cache lehetőséget a **Redis számára**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Válassza ki az Azure cache-t a Redis.":::

3. Az **új Redis cache** lapon adja meg az új prémium szintű gyorsítótár beállításait.
   
   | Beállítás      | Ajánlott érték  | Leírás |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS-név** | Adjon meg egy globálisan egyedi nevet. | A gyorsítótár nevének 1 és 63 karakter közötti sztringnek kell lennie, amely csak számokat, betűket vagy kötőjeleket tartalmazhat. A névnek számmal vagy betűvel kell kezdődnie és végződnie, és nem tartalmazhat egymást követő kötőjeleket. A gyorsítótár-példány *állomásneve* a *\<DNS name> . Redis.cache.Windows.net* lesz. | 
   | **Előfizetés** | Legördülő menüből válassza ki az előfizetését. | Az előfizetés, amely alatt létre kell hoznia ezt az új Azure cache-t a Redis-példányhoz. | 
   | **Erőforráscsoport** | Legördülő listából válassza ki az erőforráscsoportot, vagy válassza az **új létrehozása** elemet, és adjon meg egy új erőforráscsoport-nevet. | Azon erőforráscsoport neve, amelyben létre szeretné hozni a gyorsítótárat és az egyéb erőforrásokat. Az összes alkalmazás-erőforrás egy erőforráscsoporthoz való elhelyezésével könnyedén kezelheti és törölheti azokat. | 
   | **Hely** | Legördülő menüből válassza ki a kívánt helyet. | Válasszon ki egy [régiót](https://azure.microsoft.com/regions/) a többi olyan szolgáltatás közelében, amely a gyorsítótárat fogja használni. |
   | **Gyorsítótár típusa** | Legördülő listából válassza ki a prémium szintű gyorsítótárat a prémium szintű funkciók konfigurálásához. Részletekért lásd: [Az Azure cache Redis díjszabása](https://azure.microsoft.com/pricing/details/cache/). |  A tarifacsomag határozza meg a gyorsítótár méretét, teljesítményét és elérhető funkcióit. További információ: [Azure cache for Redis – áttekintés](cache-overview.md). |

4. Válassza a **hálózatkezelés** fület, vagy kattintson a lap alján található **hálózatkezelés** gombra.

5. A **hálózatkezelés** lapon válassza ki a kapcsolati módszert. A Premium cache-példányok esetében nyilvánosan, nyilvános IP-címeken vagy szolgáltatási végpontokon keresztül, vagy magántulajdonban, privát végpont használatával kapcsolódhat.

6. Válassza a **Next (speciális** ) lapot, vagy kattintson a lap alján található **Tovább: speciális** gombra.

7. A Premium cache-példány **speciális** lapján konfigurálja a nem TLS port, a fürtözés és az adatmegőrzés beállításait. Az adatmegőrzéshez kiválaszthatja a **RDB** vagy a **AOF** megőrzése lehetőséget. 

8. A RDB megőrzésének engedélyezéséhez kattintson a **RDB** elemre, és konfigurálja a beállításokat. 
   
   | Beállítás      | Ajánlott érték  | Leírás |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Biztonsági mentés gyakorisága** | Legördülő menüből válassza ki a biztonsági mentés időközét, a választható lehetőségek közé **15 perc**, **30 perc**, **60 perc**, **6 óra**, **12 óra** és **24 óra**. | Ez az intervallum az előző biztonsági mentési művelet sikeres befejezését és az új biztonsági mentés eltelte után kezdődik. | 
   | **Tárfiók** | Legördülő menüből válassza ki a Storage-fiókját. | A Storage-fióknak ugyanabban a régióban és előfizetésben kell kiválasztania, mint a gyorsítótár, és a **Premium Storage** fiók használata ajánlott, mert a Premium Storage nagyobb átviteli sebességgel rendelkezik.  | 
   | **Storage-kulcs** | Legördülő menüből válassza ki a használni kívánt **elsődleges kulcsot** vagy **másodlagos kulcsot** . | Ha az adatmegőrzési fiók tárolási kulcsa újragenerált, újra kell konfigurálnia a kívánt kulcsot a **tárolási kulcs** legördülő menüjéből. | 

    Az első biztonsági mentés a biztonsági mentés gyakorisági intervallumának eltelte után indul el.
    
   > [!NOTE]
   > Ha a RDB-fájlok biztonsági mentése a tárolóba történik, a rendszer az oldal Blobok formájában tárolja őket.

9. A AOF megőrzésének engedélyezéséhez kattintson a **AOF** elemre, és konfigurálja a beállításokat. 
   
   | Beállítás      | Ajánlott érték  | Leírás |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Első Storage-fiók** | Legördülő menüből válassza ki a Storage-fiókját. | Ennek a Storage-fióknak ugyanabban a régióban és előfizetésben kell lennie, mint a gyorsítótár, és a **Premium Storage** fiók használata ajánlott, mert a Premium Storage nagyobb átviteli sebességgel rendelkezik. | 
   | **Első tároló kulcsa** | Legördülő menüből válassza ki a használni kívánt **elsődleges kulcsot** vagy **másodlagos kulcsot** . | Ha az adatmegőrzési fiók tárolási kulcsa újragenerált, újra kell konfigurálnia a kívánt kulcsot a **tárolási kulcs** legördülő menüjéből. | 
   | **Második Storage-fiók** | Választható Legördülő menüből válassza ki a másodlagos Storage-fiókját. | Opcionálisan beállíthat egy további Storage-fiókot is. Ha egy második Storage-fiók van konfigurálva, a replika gyorsítótárba való írás a második Storage-fiókba kerül. | 
   | **Második Storage-kulcs** | Választható Legördülő menüből válassza ki a használni kívánt **elsődleges kulcsot** vagy **másodlagos kulcsot** . | Ha az adatmegőrzési fiók tárolási kulcsa újragenerált, újra kell konfigurálnia a kívánt kulcsot a **tárolási kulcs** legördülő menüjéből. | 

    Ha a AOF megőrzése engedélyezve van, a gyorsítótárba való írási műveletek a kijelölt Storage-fiókba lesznek mentve (vagy ha konfigurált egy második Storage-fiókot). Abban az esetben, ha az elsődleges és a replika gyorsítótárat is igénybe vevő végzetes hiba esetén a rendszer a tárolt AOF-naplót használja a gyorsítótár újraépítéséhez.

10. Válassza a **Next: Tags (tovább** ) lapot, vagy kattintson a **Next: Tags (tovább** ) gombra a lap alján.

11. Szükség esetén a **címkék** lapon adja meg a nevet és az értéket, ha az erőforrást kategorizálni szeretné. 

12. Válassza az **Áttekintés + létrehozás** lehetőséget. A felülvizsgálat + létrehozás lapon az Azure ellenőrzi a konfigurációt.

13. Ha megjelenik az átadott zöld érvényesítés üzenet, válassza a **Létrehozás** lehetőséget.

Eltarthat egy ideig a gyorsítótár létrehozásához. Nyomon követheti a folyamat előrehaladását az Azure cache Redis **– Áttekintés** oldalon. Ha az **állapot** **futásra** mutat, a gyorsítótár készen áll a használatra. 

## <a name="persistence-faq"></a>Adatmegőrzés – gyakori kérdések
Az alábbi lista az Azure cache Redis-megőrzési szolgáltatásával kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.

* [Engedélyezhető az adatmegőrzés egy korábban létrehozott gyorsítótárban?](#can-i-enable-persistence-on-a-previously-created-cache)
* [Egyszerre engedélyezhető a AOF és a RDB megőrzése?](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
* [Milyen adatmegőrzési modellt választok?](#which-persistence-model-should-i-choose)
* [Mi történik, ha egy másik méretre skálázást végezek, és a rendszer visszaállítja a biztonsági mentést, amely a skálázási művelet előtt történt?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)
* [Használhatom ugyanazt a Storage-fiókot két különböző gyorsítótárban való adatmegőrzéshez?](#can-i-use-the-same-storage-account-for-persistence-across-two-different-caches)
* [Az adatmegőrzés során használt tárterület után számítunk fel díjat](#will-i-be-charged-for-the-storage-being-used-in-data-persistence)

### <a name="rdb-persistence"></a>RDB megőrzése
* [Módosíthatom a RDB biztonsági mentésének gyakoriságát a gyorsítótár létrehozása után?](#can-i-change-the-rdb-backup-frequency-after-i-create-the-cache)
* [Miért van a RDB biztonsági mentés gyakorisága 60 percnél több, mint 60 perc a biztonsági másolatok között?](#why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
* [Mi történik a régi RDB biztonsági mentésével, amikor új biztonsági mentés készül?](#what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made)

### <a name="aof-persistence"></a>AOF megőrzése
* [Mikor érdemes egy második Storage-fiókot használni?](#when-should-i-use-a-second-storage-account)
* [A AOF-megőrzés a gyorsítótár egészére, késésére vagy teljesítményére is hatással van?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)
* [Hogyan lehet eltávolítani a második Storage-fiókot?](#how-can-i-remove-the-second-storage-account)
* [Mi az az újraírás, és hogyan befolyásolja a gyorsítótárat?](#what-is-a-rewrite-and-how-does-it-affect-my-cache)
* [Mit várhatok a gyorsítótár és a AOF engedélyezése esetén?](#what-should-i-expect-when-scaling-a-cache-with-aof-enabled)
* [Hogyan történik a AOF-adataim tárolása?](#how-is-my-aof-data-organized-in-storage)


### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>Engedélyezhető az adatmegőrzés egy korábban létrehozott gyorsítótárban?
Igen, a Redis megőrzése a gyorsítótár létrehozásakor és a meglévő prémium szintű gyorsítótárban is konfigurálható.

### <a name="can-i-enable-aof-and-rdb-persistence-at-the-same-time"></a>Egyszerre engedélyezhető a AOF és a RDB megőrzése?

Nem, engedélyezheti csak a RDB vagy a AOF, de egyszerre nem.

### <a name="which-persistence-model-should-i-choose"></a>Milyen adatmegőrzési modellt választok?

A AOF megőrzése minden írást egy naplóba ment, amely hatással van az átviteli sebességre, a RDB-megőrzéssel összehasonlítva, amely a biztonsági mentéseket a beállított biztonsági mentési intervallum alapján menti, és minimális hatással van a teljesítményre. Válassza a AOF megőrzése lehetőséget, ha az elsődleges célja az adatvesztés csökkentése, és az átviteli sebesség csökkentése a gyorsítótárban. Válassza a RDB megőrzése lehetőséget, ha az optimális teljesítményt szeretné fenntartani a gyorsítótárban, de továbbra is szeretne egy mechanizmust használni az adatok helyreállításához.

* További információ az RDB megőrzésének [előnyeiről](https://redis.io/topics/persistence#rdb-advantages) és [hátrányairól](https://redis.io/topics/persistence#rdb-disadvantages) .
* További információ az AOF megőrzésének [előnyeiről](https://redis.io/topics/persistence#aof-advantages) és [hátrányairól](https://redis.io/topics/persistence#aof-disadvantages) .

A AOF megőrzése esetén a teljesítményre vonatkozó további információkért lásd: a [AOF megőrzése a gyorsítótár egészére, késésére vagy teljesítményére vonatkozik?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>Mi történik, ha egy másik méretre skálázást végezek, és a rendszer visszaállítja a biztonsági mentést, amely a skálázási művelet előtt történt?

Mind a RDB, mind a AOF megőrzése esetén:

* Ha nagyobb méretre méretezett, nincs hatása.
* Ha kisebb méretre méretezett, és rendelkezik olyan egyéni [adatbázis](cache-configure.md#databases) -beállítással, amely nagyobb, mint az új mérethez tartozó [adatbázis-korlát](cache-configure.md#databases) , az ezekben az adatbázisokban tárolt adatmennyiség nem lesz visszaállítva. További információ: az [Egyéni adatbázisok beállítása a skálázás során?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
* Ha kisebb méretre méretezett, és nincs elég hely a kisebb méretekben az utolsó biztonsági mentésből származó összes adatok tárolásához, a kulcsok a visszaállítási folyamat során törlődnek, jellemzően a [allkeys-LRU](https://redis.io/topics/lru-cache) kizárási házirend használatával.

### <a name="can-i-use-the-same-storage-account-for-persistence-across-two-different-caches"></a>Használhatom ugyanazt a Storage-fiókot két különböző gyorsítótárban való adatmegőrzéshez?
Igen, ugyanazt a Storage-fiókot használhatja két különböző gyorsítótárban való megőrzésre

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>Módosíthatom a RDB biztonsági mentésének gyakoriságát a gyorsítótár létrehozása után?
Igen, az **adatmegőrzés** panelen módosíthatja a RDB megőrzésének biztonsági mentésének gyakoriságát. Útmutatásért lásd: a Redis megőrzésének konfigurálása.

### <a name="why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>Miért van a RDB biztonsági mentés gyakorisága 60 percnél több, mint 60 perc a biztonsági másolatok között?
A RDB megőrzése biztonsági mentési gyakorisága nem indul el, amíg az előző biztonsági mentési folyamat sikeresen befejeződött. Ha a biztonsági mentés gyakorisága 60 perc, és a biztonsági mentési folyamat 15 percet vesz igénybe, a következő biztonsági mentés nem indul el, amíg az előző biztonsági mentés kezdési időpontja nem fog megjelenni a 75 percben.

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>Mi történik a régi RDB biztonsági mentésével, amikor új biztonsági mentés készül?
Az összes RDB-megőrzési biztonsági mentést, kivéve a legutóbbit, automatikusan törlődnek. Előfordulhat, hogy ez a törlés nem azonnal történik, de a régebbi biztonsági mentések határozatlan ideig nem maradnak meg.


### <a name="when-should-i-use-a-second-storage-account"></a>Mikor érdemes egy második Storage-fiókot használni?

Ha úgy gondolja, hogy a gyorsítótárban a vártnál több művelet van, használjon egy második Storage-fiókot a AOF megőrzéséhez.  A másodlagos Storage-fiók beállítása segít biztosítani, hogy a gyorsítótár ne érje el a tárolási sávszélesség korlátozásait.

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>A AOF-megőrzés a gyorsítótár egészére, késésére vagy teljesítményére is hatással van?

A AOF-megőrzés az átviteli sebességet körülbelül 15% – 20%-kal befolyásolja, ha a gyorsítótár a maximális terhelés alatt van (a processzor és a kiszolgáló terhelése egyaránt 90% alatt). A gyorsítótár ezen korlátok között nem lehet késéssel kapcsolatos probléma. A gyorsítótár azonban hamarabb eléri ezeket a korlátokat, ha a AOF engedélyezve van.

### <a name="how-can-i-remove-the-second-storage-account"></a>Hogyan lehet eltávolítani a második Storage-fiókot?

A AOF megőrzése másodlagos Storage-fiókot úgy távolíthatja el, hogy a második Storage-fiókot az első Storage-fiókkal megegyezőre állítja. Meglévő gyorsítótárak esetében az **adatmegőrzési** panel a gyorsítótár **erőforrás menüjéből** érhető el. A AOF megőrzésének letiltásához kattintson a **Letiltva** gombra.

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>Mi az az újraírás, és hogyan befolyásolja a gyorsítótárat?

Ha a AOF-fájl mérete elég nagy, az újraírás automatikusan várólistára kerül a gyorsítótárban. Az újraírás átméretezi a AOF-fájlt az aktuális adathalmaz létrehozásához szükséges minimális műveletek közül. Az újraírások során várhatóan a teljesítményre vonatkozó korlátok hamarabb, különösen a nagyméretű adatkészletek kezelésekor is elérhetők. Az újraírások ritkábban történnek, mert a AOF-fájl nagyobb lesz, de jelentős időt vesz igénybe.

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>Mit várhatok a gyorsítótár és a AOF engedélyezése esetén?

Ha a skálázáskor a AOF-fájl mérete jelentősen nagyobb, akkor a méretezési művelet a vártnál hosszabb időt vesz igénybe, mert a skálázás befejeződése után újra be lesz töltve a fájl.

A skálázással kapcsolatos további információkért lásd: [Mi történik, ha egy másik méretre skálázást végezek, és a rendszer visszaállítja a biztonsági mentést, amely a skálázási művelet előtt történt?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="how-is-my-aof-data-organized-in-storage"></a>Hogyan történik a AOF-adataim tárolása?

A AOF-fájlokban tárolt adatmennyiségek csomóponton több blobra vannak osztva, így növelhetik az adattárolóba való mentés teljesítményét. Az alábbi táblázat azt mutatja, hogy az egyes díjszabási szintek hány oldal blobot használnak:

| Prémium szintű csomag | Blobok |
|--------------|-------|
| P1           | 4/szilánk    |
| P2           | 8/szilánk    |
| P3           | 16/szilánk   |
| P4           | 20/szegmens   |

Ha a fürtözés engedélyezve van, a gyorsítótárban lévő összes szegmens saját blob-készlettel rendelkezik, ahogy azt az előző táblázatban is említettük. Egy három szegmensből álló P2-gyorsítótár például elosztja a AOF-fájlt a 24 oldal Blobok között (8 blob/szilánk, 3 szegmenssel).

Az újraírást követően két AOF-fájl létezik a tárolóban. Az újraírások a háttérben történnek, és a rendszer hozzáfűzi a fájlok első készletéhez, míg a gyorsítótárba az újraírás során elküldett műveleteket a második készlethez adja meg. Hiba esetén a rendszer átmenetileg tárolja a biztonsági mentést, de az újraírás befejeződése után azonnal törölve lesz.

### <a name="will-i-be-charged-for-the-storage-being-used-in-data-persistence"></a>Kell-e fizetnem az adatmegőrzéshez használt tárterületért?

Igen, a használatban lévő Storage-fiók díjszabási modellje alapján kell fizetnie.


## <a name="next-steps"></a>Következő lépések
További információ az Azure cache Redis szolgáltatásairól.

* [Azure cache a Redis prémium szintű szolgáltatási szintjeihez](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
