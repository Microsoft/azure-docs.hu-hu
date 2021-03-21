---
title: Mi az az Azure Cache for Redis?
description: Ismerje meg az Azure cache-t, amely lehetővé teszi a gyorsítótárazást, a tartalom gyorsítótárazását, a felhasználói munkamenetek gyorsítótárazását, a feladatok és a Message Queuing Redis, valamint az elosztott tranzakciókat.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 02/08/2021
ms.openlocfilehash: 5a0389b1074737728bd0ffa5d6db90d077a9f45f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101652169"
---
# <a name="about-azure-cache-for-redis"></a>Az Azure Cache for Redis névjegye
A Redis-hez készült Azure cache memóriában tárolt adattárakat biztosít a [Redis](https://redis.io/) szoftver alapján. A Redis javítja a háttérbeli adattárakat használó alkalmazások teljesítményét és méretezhetőségét. Nagy mennyiségű alkalmazás-kérést képes feldolgozni a gyakran használt adatoknak a kiszolgáló memóriájában való megtartásával, amely gyorsan beírható és olvasható. A Redis kritikus, kis késésű és nagy átviteli sebességű adattárolási megoldást nyújt a modern alkalmazásokhoz.

A Redis készült Azure cache a Redis nyílt forráskódú (OSS Redis) és a Redis Labs (Redis Enterprise) kereskedelmi termékét is felügyelt szolgáltatásként kínálja. Biztonságos és dedikált Redis-kiszolgálói példányokat és teljes Redis API-kompatibilitást biztosít. A szolgáltatást a Microsoft üzemelteti, amely az Azure-ban üzemel, és az Azure-on belüli és kívüli alkalmazások számára is elérhető.

A Redis készült Azure cache elosztott vagy tartalom-gyorsítótárként, egy munkamenet-tárolóban, egy üzenetkezelőben és más szolgáltatásokban használható. Önálló vagy más Azure-adatbázis-szolgáltatásokkal (például Azure SQL vagy Cosmos DB) is üzembe helyezhető.

## <a name="key-scenarios"></a>Főbb forgatókönyvek
Az Azure cache for Redis az alkalmazások teljesítményének növelésével javítja az alkalmazás-architektúrák általános mintáit. A leggyakoribbak többek között a következők:

| Mintázat      | Description                                        |
| ------------ | -------------------------------------------------- |
| [Adatgyorsítótár](cache-web-app-cache-aside-leaderboard.md) | Az adatbázisok gyakran túl nagyok a gyorsítótárba való közvetlen betöltéshez. Gyakori a [gyorsítótár-](/azure/architecture/patterns/cache-aside) feltöltési minta használata, hogy csak szükség esetén töltse be az adatgyorsítótárat a gyorsítótárba. Ha a rendszer módosítja az adatmódosítást, a rendszer frissíti a gyorsítótárat is, amelyet aztán más ügyfelek számára terjesztenek. Emellett a rendszer beállíthat egy lejárati időpontot, vagy egy kizárási házirend használatával aktiválhatja az adatfrissítéseket a gyorsítótárba.|
| [Tartalom-gyorsítótár](cache-aspnet-output-cache-provider.md) | Számos weblap olyan sablonokból jön létre, amelyek statikus tartalmat, például fejléceket, lábléceket és szalagcímeket használnak. Ezek a statikus elemek gyakran nem változnak. A memórián belüli gyorsítótár használata gyors hozzáférést biztosít a háttérben lévő adattárolókkal összehasonlítva a statikus tartalmakhoz. Ez a minta csökkenti a feldolgozási időt és a kiszolgáló terhelését, így a webkiszolgálók rugalmasabbak lesznek. Lehetővé teszi a terhelések kezeléséhez szükséges kiszolgálók számának csökkentését. A Redis készült Azure cache biztosítja a Redis kimeneti gyorsítótár-szolgáltatóját, hogy támogassa ezt a mintát a ASP.NET.|
| [Munkamenet-tároló](cache-aspnet-session-state-provider.md) | Ezt a mintát gyakran használják a bevásárlókocsi-és más felhasználói előzményi adatként, amelyeket a webalkalmazások felhasználói cookie-kkal kívánnak rendelni. Ha túl sok információt tárol cookie-kban, csökkenhet a teljesítmény, mivel a cookie mérete nő, és a rendszer minden kérés esetén átadja és ellenőrzi a cookie-t. Egy tipikus megoldás a cookie-t használja kulcsként az adatbázisban tárolt adatlekérdezéshez. A memóriában tárolt gyorsítótár, például a Redis-hez készült Azure cache használatával az adatok egy felhasználóhoz való hozzárendelése sokkal gyorsabb, mint a teljes körű, kapcsolatban álló adatbázisokkal való interakció. |
| Feladatok és üzenetek üzenetsor-kezelése | Az alkalmazások gyakran vesznek fel feladatokat egy várólistába, ha a kérelemhez társított műveletek végrehajtása időt vesz igénybe. A már futó műveletek várólistára kerülnek, és gyakran egy másik kiszolgáló végzi a feldolgozást.  Ezt a késleltetési módot a tevékenységek üzenetsorba való helyezésének nevezik. A Redis készült Azure cache elosztott várólistát biztosít a minta alkalmazásban való engedélyezéséhez.|
| Elosztott tranzakciók | Az alkalmazásokhoz időnként szükség van egy adott háttérbeli adattárolóra vonatkozó parancsok sorozatára, hogy egyetlen atomi műveletként fussanak. Minden parancsnak sikeresnek kell lennie, vagy mindegyiket vissza kell állítani a kezdeti állapotba. A Redis-hez készült Azure cache egyetlen [tranzakcióként](https://redis.io/topics/transactions)támogatja a parancsok kötegének végrehajtását. |

## <a name="redis-versions"></a>Redis-verziók

A Redis-hez készült Azure cache az OSS Redis 4. x-es verzióját és a 6,0-as előnézetét támogatja. Eldöntöttük, hogy kihagyjuk a Redis 5,0-et, hogy a legújabb verziót hozza létre. Korábban az Azure cache for Redis csak egyetlen Redis-verziót tart fenn. Egy újabb főverzió-frissítést és legalább egy régebbi stabil verziót fog biztosítani. Kiválaszthatja, hogy az alkalmazás [melyik verziója](cache-how-to-version.md) legyen a legmegfelelőbb.


## <a name="service-tiers"></a>Szolgáltatásszintek
A Redis készült Azure cache a következő szinteknél érhető el:

| Szint | Description |
|---|---|
| Alapszintű | Egyetlen virtuális gépen futó OSS Redis cache. Ez a szint nem rendelkezik szolgáltatói szerződéssel (SLA), és ideális fejlesztési, tesztelési és nem kritikus fontosságú számítási feladatokhoz. |
| Standard | Egy, a replikált konfigurációban két virtuális gépen futó OSS Redis cache. |
| Prémium | Nagy teljesítményű OSS Redis cache-gyorsítótárak. Ez a platform magasabb átviteli sebességet, kisebb késést, jobb rendelkezésre állást és további funkciókat kínál. A prémium szintű gyorsítótárak üzembe helyezése nagyobb teljesítményű virtuális gépeken történik, mint az alapszintű vagy standard gyorsítótárak esetében. |
| Enterprise | A Redis Labs Redis Enterprise szoftverrel működő nagy teljesítményű gyorsítótárak. Ez a platform támogatja a Redis-modulokat, például a RediSearch, a RedisBloom és a RedisTimeSeries. Emellett a prémium szintnél is magasabb rendelkezésre állást biztosít. |
| Nagyvállalati Flash | Költséghatékony nagy gyorsítótárak a Redis Labs Redis Enterprise szoftverrel. Ez a csomag kiterjeszti a Redis az adattárolót a nem felejtő memóriára, ami olcsóbb a DRAM esetében, egy virtuális gépen. Ez csökkenti a GB-os memória teljes költségeit. |

### <a name="feature-comparison"></a>Szolgáltatások összehasonlítása
Az [Azure cache for Redis díjszabása](https://azure.microsoft.com/pricing/details/cache/) az egyes szintek részletes összehasonlítását tartalmazza. Az alábbi táblázat a szintek által támogatott néhány funkciót írja le:

| Funkció leírása | Alapszintű | Standard | Prémium | Enterprise | Nagyvállalati Flash |
| ------------------- | :-----: | :------: | :---: | :---: | :---: |
| [Szolgáltatói szerződés (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |-|✔|✔|✔|✔|
| Adattitkosítás |✔|✔|✔|✔|✔|
| [Hálózatelkülönítés](cache-how-to-premium-vnet.md) |✔|✔|✔|✔|✔|
| [Méretezés](cache-how-to-scale.md) |✔|✔|✔|✔|✔|
| [OSS-fürt](cache-how-to-premium-clustering.md) |-|-|✔|✔|✔|
| [Adatmegőrzés](cache-how-to-premium-persistence.md) |-|-|✔|Előnézet|Előnézet|
| [Zónaredundancia](cache-how-to-zone-redundancy.md) |-|-|Előnézet|✔|✔|
| [Georeplikáció](cache-how-to-geo-replication.md) |-|-|✔|Előnézet|Előnézet|
| [Modulok](https://redis.io/modules) |-|-|-|✔|✔|
| [Import/Export](cache-how-to-import-export-data.md) |-|-|✔|✔|✔|
| [Ütemezett frissítések](cache-administration.md#schedule-updates) |✔|✔|✔|-|-|

### <a name="choosing-the-right-tier"></a>A megfelelő szint kiválasztása
A következő esetekben érdemes figyelembe vennie az Azure cache Redis-szinten való kiválasztásakor:

* **Memória**: az alapszintű és a Standard csomag 250 MB – 53 GB; a prémium szint 6 GB-1,2 TB; a nagyvállalati réteg 12 GB-14 TB.  120 GB-nál nagyobb prémium szintű gyorsítótár létrehozásához használhatja az Redis OSS-fürtözést. További információt az [Azure cache Redis díjszabását](https://azure.microsoft.com/pricing/details/cache/)ismertető témakörben talál. További információ: [fürtözés konfigurálása prémium szintű Azure cache-hez a Redis](cache-how-to-premium-clustering.md)-hez.
* **Teljesítmény**: a prémium és a nagyvállalati szinten található gyorsítótárak olyan hardverekre vannak telepítve, amelyek gyorsabb processzorokkal rendelkeznek, és jobb teljesítményt biztosítanak az alapszintű vagy a standard csomaghoz képest. A prémium szintű gyorsítótárak nagyobb átviteli sebességgel és kisebb késéssel rendelkeznek. További információ: [Azure cache for Redis Performance](cache-planning-faq.md#azure-cache-for-redis-performance).
* **Dedikált mag a Redis-kiszolgálóhoz**: az összes gyorsítótár, kivéve a C0 dedikált virtuálisgép-magok futtatását. A Redis alapján a rendszer csak egy szálat használ a parancsok feldolgozásához. Az Azure cache for Redis további magokat használ az I/O-feldolgozáshoz. Ha egyre több mag van, akkor az átviteli teljesítmény még akkor is javul, ha nem lehet lineáris skálázást létrehozni. Emellett a nagyobb méretű virtuálisgép-méretek általában nagyobb sávszélesség-korláttal rendelkeznek, mint a kisebbek. Ez segít elkerülni a hálózat telítettségét, ami időtúllépést okoz az alkalmazásban.
* **Hálózati teljesítmény**: Ha olyan számítási feladattal rendelkezik, amely nagy adatátvitelt igényel, a prémium vagy a nagyvállalati szint nagyobb sávszélességet biztosít az alapszintű vagy a standardhoz képest Az egyes szinteknél a nagyobb méretű gyorsítótárak is nagyobb sávszélességet igényelnek a gyorsítótárat működtető mögöttes virtuális gép miatt. További információ: [Azure cache for Redis Performance](cache-planning-faq.md#azure-cache-for-redis-performance).
* **Ügyfélkapcsolatok maximális száma**: a prémium szint azon ügyfelek maximális számát kínálja, akik csatlakozhatnak a Redis, és nagyobb számú kapcsolattal rendelkeznek a nagyobb méretű gyorsítótárak esetében. A fürtözés nem fokozza a fürtözött gyorsítótár számára elérhető kapcsolatok számát. További információt az [Azure cache Redis díjszabását](https://azure.microsoft.com/pricing/details/cache/)ismertető témakörben talál.
* **Magas rendelkezésre állás**: a Redis készült Azure cache több [magas rendelkezésre állási](cache-high-availability.md) lehetőséget biztosít. Garantálja, hogy a standard, prémium vagy vállalati gyorsítótár az [SLA](https://azure.microsoft.com/support/legal/sla/cache/v1_0/)-nak megfelelően elérhető. Az SLA csak a cache-végpontokhoz való kapcsolódást fedi le. Az SLA nem fedi le az adatvesztés elleni védelmet. Javasoljuk, hogy a prémium és a nagyvállalati rétegek Redis adatmegőrzési funkciójának használatával növelje a rugalmasságot az adatvesztéssel szemben.
* **Adatmegőrzés**: a prémium és a nagyvállalati rétegek lehetővé teszik, hogy a gyorsítótárban tárolt és felügyelt lemezeket is megmaradjanak. Az alapul szolgáló infrastrukturális problémák potenciális adatvesztést okozhatnak. Javasoljuk, hogy a Redis adatmegőrzési funkciójának használatával növelje a rugalmasságot az adatvesztéssel szemben. A Redis-hez készült Azure cache a RDB és a AOF (előzetes verzió) lehetőséget is kínálja. Az adatmegőrzés Azure Portal és CLI használatával engedélyezhető. A prémium szintű csomag esetében lásd: az [adatmegőrzés konfigurálása prémium szintű Azure cache-hez a Redis](cache-how-to-premium-persistence.md)-hez.
* **Hálózati elkülönítés**: az Azure Private link és a Virtual Network (VNET) üzemelő példányok fokozott biztonságot és adatforgalom-elkülönítést biztosítanak az Azure cache Redis számára. A VNET lehetővé teszi a hozzáférés további korlátozását a hálózati hozzáférés-vezérlési házirendeken keresztül. További információkért lásd: [Azure cache for Redis with Azure Private link](cache-private-link.md) , és [hogyan konfigurálható Virtual Network támogatás a prémium szintű Azure cache-hez a Redis](cache-how-to-premium-vnet.md).
* **Bővíthetőség**: a vállalati rétegek támogatják a [RediSearch](https://docs.redislabs.com/latest/modules/redisearch/), a [RedisBloom](https://docs.redislabs.com/latest/modules/redisbloom/) és a [RedisTimeSeries](https://docs.redislabs.com/latest/modules/redistimeseries/). Ezek a modulok új adattípusokat és funkciókat vesznek fel a Redis.

A gyorsítótárat az alapszintű csomagról a prémium szintre méretezheti a létrehozása után. Az alacsonyabb szintű méretezés jelenleg nem támogatott. A részletes skálázási utasításokért lásd: az [Azure cache skálázása Redis](cache-how-to-scale.md) és [a skálázási műveletek automatizálása](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

### <a name="special-considerations-for-enterprise-tiers"></a>A nagyvállalati rétegek speciális szempontjai

A vállalati rétegek a Redis Enterprise, a Redis Labs Redis kereskedelmi változatán alapulnak. Az ügyfelek egy Azure Marketplace-ajánlaton keresztül kapják meg és fizetik ki a szoftver licencét. A Redis készült Azure cache megkönnyíti a licencek beszerzését, így nem kell külön elvégeznie. Az Azure Marketplace-en való vásárláshoz a következő előfeltételek szükségesek:
* Az Azure-előfizetés érvényes fizetési eszközzel rendelkezik. Az Azure-kreditek vagy az ingyenes MSDN-előfizetések nem támogatottak.
* A szervezete engedélyezi az [Azure Marketplace megvásárlását](../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases).
* Ha privát Piactért használ, tartalmaznia kell a Redis Labs Enterprise ajánlatot.

> [!IMPORTANT]
> Az Redis Enterprise cache-hez készült Azure cache olyan szabványos hálózati terheléselosztást igényel, amelyet a gyorsítótár példányaitól külön kell fizetni. További részletekért tekintse meg a [Load Balancer díjszabását](https://azure.microsoft.com/pricing/details/load-balancer/) . Ha egy vállalati gyorsítótár több Availability Zones használatára van konfigurálva, az adatátvitel a [standard szintű hálózati sávszélesség díjszabása](https://azure.microsoft.com/pricing/details/bandwidth/) alapján történik, 2021. július 1-től kezdődően.
>
> Az adatmegőrzés emellett Managed Disks. Ezeknek az erőforrásoknak a használata a vállalati adatmegőrzés nyilvános előzetes verziójában ingyenes lesz. Ez változhat, ha a funkció általánosan elérhetővé válik.
>
>

## <a name="next-steps"></a>Következő lépések
* [Nyílt forráskódú Redis cache létrehozása](quickstart-create-redis.md)
* [Redis vállalati gyorsítótár létrehozása](quickstart-create-redis-enterprise.md)
* [Az Azure cache használata a Redis egy ASP.NET-webalkalmazásban](cache-web-app-howto.md)
* [Az Azure cache használata a Redis a .NET Core-ban](cache-dotnet-core-quickstart.md)
* [Az Azure cache használata a Redis a .NET-keretrendszerben](cache-dotnet-how-to-use-azure-redis-cache.md)
* [Az Azure cache használata a Redis Node.js](cache-nodejs-get-started.md)
* [Az Azure cache használata a Redis javában](cache-java-get-started.md)
* [Az Azure cache használata a Redis a Pythonban](cache-python-get-started.md)