---
title: Migrálás az Azure Cache for Redisbe
description: Megtudhatja, hogyan telepítheti át meglévő gyorsítótárát az Azure cache-be a Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: yegu
ms.openlocfilehash: d63cafb32dc1db0a901ed3e6004446b450db10c7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102451523"
---
# <a name="migrate-to-azure-cache-for-redis"></a>Migrálás az Azure Cache for Redisbe
Ez a cikk számos módszert ismertet a helyszíni vagy egy másik felhőalapú szolgáltatásban futó meglévő Redis-gyorsítótár áttelepítése érdekében az Azure cache for Redis számára.

## <a name="migration-scenarios"></a>Migrálási forgatókönyvek
A nyílt forráskódú Redis számos számítási környezetben futhatnak. Gyakori példák a következők:

- Privát adatközpontokban **futó helyszíni Redis** cache-gyorsítótárak.
- **Felhőalapú virtuális gépek** – Azure-beli virtuális gépeken futó Redis-gyorsítótárak, AWS-EC2 stb.
- **Szolgáltatásokkal** felügyelt Redis-szolgáltatások (például AWS ElastiCache).
- **Különböző régiók** – egy másik Azure-régióban található Redis cache-gyorsítótárak.

Ha rendelkezik ilyen gyorsítótárral, lehetséges, hogy az Azure cache-re helyezheti át a Redis minimális megszakítással vagy állásidővel.

## <a name="migration-options"></a>Migrálási lehetőségek

Többféleképpen válthat az egyik gyorsítótárból a másikba. Attól függően, hogy a gyorsítótár hol és hogyan kommunikál az alkalmazással, az egyik módszer hasznosabb lesz a többinél. A gyakran használt áttelepítési stratégiák némelyike alább látható.

   | Beállítás       | Előnyök | Hátrányok |
   | ------------ | ---------- | ------------- |
   | Új gyorsítótár létrehozása | Legegyszerűbben implementálható. | Újra fel kell töltenie az adatokat az új gyorsítótárba, ami esetleg nem működik sok alkalmazással. |
   | Adatexportálás és-importálás RDB-fájlon keresztül | Általában minden Redis cache-gyorsítótárral kompatibilis. | Egyes adatvesztések elvesznek, ha a RDB fájl létrehozása után a meglévő gyorsítótárba írják őket. | 
   | Kettős írású adatlemez két gyorsítótárba | Nincs adatvesztés vagy állásidő. A meglévő gyorsítótár megszakítás nélküli műveletei. Az új gyorsítótár egyszerűbb tesztelése. | Hosszabb ideig két gyorsítótárra van szükség. | 
   | Az adatáttelepítés programozott módon | Az adatáthelyezés módjának teljes körű vezérlése. | Egyéni kódot igényel. | 

### <a name="create-a-new-azure-cache-for-redis"></a>Új Azure cache létrehozása a Redis-hez

Ez a megközelítés technikailag nem Migrálás. Ha az adatvesztés nem jelent problémát, a Redis-hez készült Azure cache-re való áttérés legegyszerűbb módja a gyorsítótár-példány létrehozása, és az alkalmazás összekapcsolása. Ha például a Redis-t használja az adatbázisrekordok megtekinthető gyorsítótárában, a gyorsítótárat egyszerűen újraépítheti a semmiből.

A beállítás megvalósításának általános lépései a következők:

1. Hozzon létre egy új Azure cache-t a Redis-példányhoz.

2. Frissítse az alkalmazást az új példány használatára.

3. Törölje a régi Redis-példányt.

### <a name="export-data-to-an-rdb-file-and-import-it-into-azure-cache-for-redis"></a>Az adatexportálás RDB-fájlba, és az Azure gyorsítótárba való importálása az Redis-be

A nyílt forráskódú Redis egy standard mechanizmust határoz meg a gyorsítótár memóriában tárolt adatkészletének pillanatképének elkészítéséhez és fájlba mentéséhez. Ezt a fájlt (RDB néven) egy másik Redis-gyorsítótár is olvashatja. [Az Redis prémium szintű Azure cache](cache-overview.md#service-tiers) támogatja az adatimportálást a gyorsítótár-PÉLDÁNYba RDB-fájlok használatával. Egy RDB-fájl használatával átviheti az adatok egy meglévő gyorsítótárból az Azure cache-be a Redis.

> [!IMPORTANT]
> A RDB fájlformátuma változhat a Redis-verziók között, és előfordulhat, hogy nem tartja vissza a visszamenőleges kompatibilitást. A Redis exportált gyorsítótár verziószáma nem lehet kisebb, mint az Azure cache által a Redis számára biztosított verzió.
>

A beállítás megvalósításának általános lépései a következők:

1. Hozzon létre egy új Azure cache-t a Redis-példányhoz a prémium szinten, amely a meglévő gyorsítótár méretével megegyező méretű (vagy nagyobb).

2. Mentse a meglévő Redis cache pillanatképét. A Redis úgy is [beállíthatja, hogy rendszeres időközönként mentse a pillanatképeket](https://redis.io/topics/persistence) , vagy a [Mentés](https://redis.io/commands/save) vagy a [BGSAVE](https://redis.io/commands/bgsave) parancs használatával manuálisan futtassa a folyamatot. A RDB-fájl neve alapértelmezés szerint "dump. RDB", és az *Redis. conf* konfigurációs fájlban megadott elérési úton lesz elhelyezve.

    > [!NOTE]
    > Ha a Redis az Azure cache-ben végez áttelepítést, tekintse [meg a RDB-fájlok exportálásának](cache-how-to-import-export-data.md) lépéseit, vagy használja helyette a [PowerShell-exportálási parancsmagot](/powershell/module/azurerm.rediscache/export-azurermrediscache) .
    >

3. Másolja a RDB-fájlt egy Azure Storage-fiókba abban a régióban, ahol az új gyorsítótár található. Ehhez a feladathoz használhatja a AzCopy.

4. Importálja a RDB-fájlt az új gyorsítótárba ezen [importálási utasítások](cache-how-to-import-export-data.md) vagy a [PowerShell importálási parancsmagjának](/powershell/module/azurerm.rediscache/import-azurermrediscache)használatával.

5. Frissítse az alkalmazást az új gyorsítótár-példány használatára.

### <a name="write-to-two-redis-caches-simultaneously-during-migration-period"></a>Az áttelepítési időszak során egyszerre két Redis gyorsítótárba ír

Ahelyett, hogy közvetlenül a gyorsítótárak között helyezi át az adatátvitelt, az alkalmazás használatával is írhat az adatait egy meglévő gyorsítótárba, és újat is beállíthat. Az alkalmazás először is beolvassa az adatait a meglévő gyorsítótárból. Ha az új gyorsítótár rendelkezik a szükséges adattal, átváltja az alkalmazást a gyorsítótárba, és kivonja a régit. Tegyük fel például, hogy a Redis-t munkamenet-tárolóként használja, és az alkalmazás munkamenetei hét napig érvényesek. Ha egy hétre ír a két gyorsítótárba, biztos lehet benne, hogy az új gyorsítótár tartalmazza az összes nem lejárt munkamenet-információt. Az adatvesztéssel kapcsolatos aggályok nélkül nyugodtan támaszkodhat erre a pontra.

A beállítás megvalósításának általános lépései a következők:

1. Hozzon létre egy új Azure cache-t a Redis-példányhoz a prémium szinten, amely a meglévő gyorsítótár méretével megegyező méretű (vagy nagyobb).

2. Módosítsa az alkalmazás kódját az új és az eredeti példányokra való íráshoz.

3. Folytassa az adatok olvasását az eredeti példányból, amíg az új példány megfelelően fel nem töltődik az adatokkal.

4. Az alkalmazás kódjának frissítése csak az új példány olvasására és írására.

5. Törölje az eredeti példányt.

### <a name="migrate-programmatically"></a>Programozott Migrálás

Létrehozhat egy egyéni áttelepítési folyamatot egy meglévő gyorsítótárból származó adatok programozott olvasásával, és az Azure cache-be való írásával Redis. Ez a [nyílt forráskódú eszköz](https://github.com/deepakverma/redis-copy) a Redis-példányok egyik Azure cache-ről egy másikra történő másolására használható. Ez az eszköz akkor hasznos, ha a különböző Azure cache-régiókban lévő gyorsítótár-példányok között helyez át adatátvitelt. [Lefordított verzió](https://github.com/deepakverma/redis-copy/releases/download/alpha/Release.zip) is elérhető. A forráskódot a saját áttelepítési eszköz írásához hasznos útmutatóként is megtalálhatja.

> [!NOTE]
> Ezt az eszközt a Microsoft hivatalosan nem támogatja. 
>

A beállítás megvalósításának általános lépései a következők:

1. Hozzon létre egy virtuális gépet abban a régióban, ahol a meglévő gyorsítótár található. Ha az adatkészlet nagyméretű, a másolási idő csökkentése érdekében válasszon egy viszonylag nagy teljesítményű virtuális gépet.

2. Hozzon létre egy új Azure cache-t a Redis-példányhoz.

3. Ürítse ki az adatait az új gyorsítótárból, hogy az üres legyen. Erre a lépésre azért van szükség, mert a másolási eszköz maga nem írja felül a meglévő kulcsot a cél gyorsítótárban.

    > [!IMPORTANT]
    > Ügyeljen arra, hogy ne Ürítse ki a forrás-gyorsítótárból.
    >

4. Használjon olyan alkalmazást, mint a fenti nyílt forráskódú eszköz, amellyel automatizálható az adatok másolása a forrás-gyorsítótárból a célhelyre. Ne feledje, hogy a másolási folyamat eltarthat egy darabig, hogy az adatkészlet méretétől függően befejeződjön.

## <a name="next-steps"></a>Következő lépések
További információ az Azure cache Redis szolgáltatásairól.

* [Azure cache a Redis szolgáltatási szintjeihez](cache-overview.md#service-tiers)
* [Adatimportálás](cache-how-to-import-export-data.md#import)