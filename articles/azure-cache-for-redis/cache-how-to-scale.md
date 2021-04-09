---
title: Azure cache méretezése Redis-példányhoz
description: Megtudhatja, hogyan méretezheti az Azure cache-t a Redis-példányok számára a Azure Portal és az eszközök, például a Azure PowerShell és az Azure CLI használatával.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: d91b62afacad31d78feb7d4743cd4050fcc1bd4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104581643"
---
# <a name="scale-an-azure-cache-for-redis-instance"></a>Azure cache méretezése Redis-példányhoz
A Redis készült Azure cache különböző gyorsítótár-ajánlatokat tartalmaz, amelyek rugalmasságot biztosítanak a gyorsítótár méretének és funkcióinak kiválasztásában. Alapszintű, standard vagy prémium szintű gyorsítótár esetén megváltoztathatja annak méretét és szintjét, miután létrejött az alkalmazás igényeinek megfelelően. Ez a cikk bemutatja, hogyan méretezheti a gyorsítótárat a Azure Portal és az eszközök, például a Azure PowerShell és az Azure CLI használatával.

## <a name="when-to-scale"></a>Mikor érdemes méretezni
Az Azure cache [figyelési](cache-how-to-monitor.md) funkcióit használhatja a Redis számára a gyorsítótár állapotának és teljesítményének figyeléséhez, valamint a gyorsítótár skálázási időpontjának meghatározásához. 

A következő mérőszámok figyelésével megállapíthatja, hogy szükség van-e a skálázásra.

* Redis-kiszolgáló terhelése
* Memóriahasználat
* Hálózati sávszélesség
* CPU-használat

Ha azt állapítja meg, hogy a gyorsítótár már nem felel meg az alkalmazás követelményeinek, egy nagyobb vagy kisebb gyorsítótár-díjszabási csomagra méretezheti, amely megfelelő az alkalmazásához. További információ a használandó gyorsítótár-díjszabási csomag meghatározásáról: [a megfelelő csomag kiválasztása](cache-overview.md#choosing-the-right-tier).

## <a name="scale-a-cache"></a>Gyorsítótár skálázása
A gyorsítótár méretezéséhez keresse meg a [gyorsítótárat](cache-configure.md#configure-azure-cache-for-redis-settings) a [Azure Portal](https://portal.azure.com) , majd kattintson a **Méretezés** elemre az **erőforrás menüben**.

![Méretezés](./media/cache-how-to-scale/redis-cache-scale-menu.png)

Válassza ki a kívánt díjszabási szintet a **Select díjszabási csomag kiválasztása** panelen, és kattintson a **kiválasztás** elemre.

![Tarifacsomag][redis-cache-pricing-tier-blade]


A következő korlátozásokkal méretezheti át egy másik díjszabási szintet:

* Magasabb díjszabási szintet nem lehet alacsonyabb díjszabási szinten méretezni.
  * **Prémium** szintű gyorsítótárból nem méretezhető **standard** vagy **alapszintű** gyorsítótár.
  * **Standard** szintű gyorsítótárból nem méretezhető **alapszintű** gyorsítótár.
* **Alapszintű** gyorsítótárból **szabványos** gyorsítótárra méretezheti, de a méret nem módosítható egyszerre. Ha más méretre van szüksége, egy későbbi skálázási műveletet is végrehajthat a kívánt méretre.
* **Alapszintű** gyorsítótárból nem lehet közvetlenül **prémium** szintű gyorsítótárra méretezni. Első lépésként az **alapszintű** és a **standard** közötti méretezés egy skálázási műveletben, majd a **standard** és a **prémium** között egy későbbi skálázási műveletben.
* A **C0 (250 MB)** mérete nem méretezhető nagyobb méretre. Azonban az azonos díjszabási szinten lévő más méretekre is méretezhető. Például a C5 szabványtól a C1 szabványig méretezheti a méretezést.
 
Míg a gyorsítótár az új díjszabási szinten méretezhető, a Redis panelhez tartozó **Azure cache** -ben egy **skálázási** állapot jelenik meg.

![Méretezés][redis-cache-scaling]

A skálázás befejezése után az állapot a **méretezéstől** a **futtatásig** változik.

## <a name="how-to-automate-a-scaling-operation"></a>Skálázási művelet automatizálása
A Azure Portal gyorsítótár-példányainak skálázása mellett a PowerShell-parancsmagok, az Azure CLI és a Microsoft Azure Management Library (MAML) használatával is méretezhető. 

* [Méretezés a PowerShell használatával](#scale-using-powershell)
* [Méretezés az Azure CLI használatával](#scale-using-azure-cli)
* [Méretezés a MAML használatával](#scale-using-maml)

### <a name="scale-using-powershell"></a>Méretezés a PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A Redis-példányok Azure-gyorsítótárát a PowerShell-lel méretezheti a [set-AzRedisCache](/powershell/module/az.rediscache/set-azrediscache) parancsmag használatával `Size` , ha a, vagy a `Sku` `ShardCount` tulajdonság módosul. Az alábbi példa bemutatja, hogyan méretezheti át a gyorsítótárat `myCache` egy 2,5 GB-os gyorsítótárba. 

```powershell
   Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB
```

A PowerShell-sel való skálázással kapcsolatos további információkért lásd: [Azure cache skálázása Redis a PowerShell használatával](cache-how-to-manage-redis-cache-powershell.md#scale).

### <a name="scale-using-azure-cli"></a>Méretezés az Azure CLI használatával
Az Azure-gyorsítótár Redis-példányok Azure CLI-vel történő méretezéséhez hívja meg a `azure rediscache set` parancsot, és adja meg a kívánt konfigurációs módosításokat, amelyek a kívánt skálázási művelettől függően új méretet, SKU-t vagy szektorcsoport-méretet tartalmaznak.

Az Azure CLI-vel való skálázással kapcsolatos további információkért lásd: [meglévő Azure cache beállításainak módosítása a Redis](cache-manage-cli.md#scale).

### <a name="scale-using-maml"></a>Méretezés a MAML használatával
Ha az Azure cache-t a [Microsoft Azure Management librarys (MAML)](https://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/)használatával szeretné Redis-példányokra méretezni, hívja meg a `IRedisOperations.CreateOrUpdate` metódust, és adja át az új méretet a következőnek: `RedisProperties.SKU.Capacity` .

```csharp
    static void Main(string[] args)
    {
        // For instructions on getting the access token, see
        // https://azure.microsoft.com/documentation/articles/cache-configure/#access-keys
        string token = GetAuthorizationHeader();

        TokenCloudCredentials creds = new TokenCloudCredentials(subscriptionId,token);

        RedisManagementClient client = new RedisManagementClient(creds);
        var redisProperties = new RedisProperties();

        // To scale, set a new size for the redisSKUCapacity parameter.
        redisProperties.Sku = new Sku(redisSKUName,redisSKUFamily,redisSKUCapacity);
        redisProperties.RedisVersion = redisVersion;
        var redisParams = new RedisCreateOrUpdateParameters(redisProperties, redisCacheRegion);
        client.Redis.CreateOrUpdate(resourceGroupName,cacheName, redisParams);
    }
```

További információ: az [Azure cache kezelése a REDIS MAML](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) -minta használatával.

## <a name="scaling-faq"></a>Méretezés – gyakori kérdések
Az alábbi lista az Azure cache szolgáltatással történő Redis skálázással kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.

* [Méretezhető a-re vagy a prémium szintű gyorsítótárba?](#can-i-scale-to-from-or-within-a-premium-cache)
* [A skálázás után módosítani kell a gyorsítótár nevét vagy a hozzáférési kulcsokat?](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
* [Hogyan működik a skálázás?](#how-does-scaling-work)
* [Elvesztik az adatok a gyorsítótárból a skálázás során?](#will-i-lose-data-from-my-cache-during-scaling)
* [Az egyéni adatbázisokra vonatkozó beállítások a skálázás során is érintettek?](#is-my-custom-databases-setting-affected-during-scaling)
* [Elérhető lesz a gyorsítótár a skálázás során?](#will-my-cache-be-available-during-scaling)
* Ha a Geo-replikáció konfigurálva van, miért nem tudom méretezni a gyorsítótárat vagy módosítani a szegmensek szegmenseit?
* [Nem támogatott műveletek](#operations-that-are-not-supported)
* [Mennyi ideig tart a skálázás?](#how-long-does-scaling-take)
* [Honnan tudhatom meg, hogy mikor fejeződik be a skálázás?](#how-can-i-tell-when-scaling-is-complete)

### <a name="can-i-scale-to-from-or-within-a-premium-cache"></a>Méretezhető a-re vagy a prémium szintű gyorsítótárba?
* Egy **prémium** szintű gyorsítótárból nem méretezhető **alapszintű vagy** **standard** szintű díjszabás.
* A **prémium** szintű gyorsítótár díjszabási szintjei egy másikra méretezhetők.
* **Alapszintű** gyorsítótárból nem lehet közvetlenül **prémium** szintű gyorsítótárra méretezni. Első lépésként az **alapszintű** és a **standard** közötti méretezés egy skálázási műveletben, majd a **standard** és a **prémium** között egy későbbi skálázási műveletben.
* Ha engedélyezte a fürtözést a **prémium** szintű gyorsítótár létrehozásakor, módosíthatja [a fürt méretét](cache-how-to-premium-clustering.md#cluster-size). Ha a gyorsítótár a fürtözés nélkül lett létrehozva, később is konfigurálhatja a fürtözést.
  
  További információ: [fürtözés konfigurálása prémium szintű Azure cache-hez a Redis](cache-how-to-premium-clustering.md)-hez.

### <a name="after-scaling-do-i-have-to-change-my-cache-name-or-access-keys"></a>A skálázás után módosítani kell a gyorsítótár nevét vagy a hozzáférési kulcsokat?
Nem, a gyorsítótár neve és kulcsa változatlan marad a skálázási művelet során.

### <a name="how-does-scaling-work"></a>Hogyan működik a skálázás?
* Ha egy **alapszintű** gyorsítótár méretezése egy másik méretre történik, a rendszer leállítja, és az új mérettel kiépít egy új gyorsítótárat. Ebben az időszakban a gyorsítótár nem érhető el, és a gyorsítótárban lévő összes érték elvész.
* Ha egy **alapszintű** gyorsítótár méretezése **szabványos** gyorsítótárba történik, a rendszer kiépít egy replika-gyorsítótárat, és az adatok az elsődleges gyorsítótárból a replika-gyorsítótárba lesznek másolva. A gyorsítótár a skálázási folyamat során továbbra is elérhető marad.
* Ha egy **standard** gyorsítótár méretezése eltérő méretre vagy **prémium** szintű gyorsítótárra történik, az egyik replikát a rendszer leállítja és újra kiépíti az új méretre és az átvitt adatokra, majd a másik replika feladatátvételt hajt végre az Újraépítés előtt, hasonlóan ahhoz a folyamathoz, amely a gyorsítótár-csomópontok valamelyikének meghibásodása során következik be.

### <a name="will-i-lose-data-from-my-cache-during-scaling"></a>Elvesztik az adatok a gyorsítótárból a skálázás során?
* Ha egy **alapszintű** gyorsítótár új méretre van méretezve, az összes adat elvész, és a gyorsítótár nem érhető el a skálázási művelet során.
* Ha egy **alapszintű** gyorsítótár méretezése **szabványos** gyorsítótárba történik, a gyorsítótárban lévő adatok általában megmaradnak.
* Ha egy **standard** gyorsítótár nagyobb méretre vagy szintre van méretezve, vagy a **prémium** szintű gyorsítótár nagyobb méretűre van méretezve, az összes adatmennyiséget általában megőrzi a rendszer. Ha egy standard vagy **prémium** **szintű** gyorsítótárat kisebb méretre méretezi, az adatvesztés attól függ, hogy mennyi adat van a gyorsítótárban az új mérethez kapcsolódóan. Ha a skálázás során az adatvesztés történik, a kulcsok kimaradnak a [allkeys-LRU](https://redis.io/topics/lru-cache) kizárási házirend használatával. 

### <a name="is-my-custom-databases-setting-affected-during-scaling"></a>Az egyéni adatbázisokra vonatkozó beállítások a skálázás során is érintettek?
Ha egyéni értéket konfigurált a `databases` beállításhoz a gyorsítótár létrehozása során, vegye figyelembe, hogy egyes díjszabási szintek eltérő adatbázis- [korlátokkal](cache-configure.md#databases)rendelkeznek. Ebben az esetben a következő szempontokat érdemes figyelembe venni:

* Az aktuális szintjénél alacsonyabb korláttal rendelkező díjszabási csomagra való skálázás esetén `databases` :
  * Ha az alapértelmezett értéket használja `databases` , amely az összes díjszabási csomag esetében 16, az adatvesztés nem történik meg.
  * Ha olyan egyéni számot használ, amely a `databases` skálázási szintre korlátozza a korlátot, a rendszer megőrzi ezt a beállítást, `databases` és nem vesz fel adatvesztést.
  * Ha olyan egyéni számot használ, `databases` amely meghaladja az új csomag korlátait, a `databases` beállítás az új csomag korlátaira csökken, és az eltávolított adatbázisokban lévő összes érték elvész.
* Ha az aktuális csomaggal megegyező vagy magasabb korláttal rendelkező árképzési szintre `databases` van skálázás, a `databases` rendszer megőrzi a beállítást, és nem vesz fel adatvesztést.

Míg a standard és a prémium szintű gyorsítótár 99,9%-os SLA-val rendelkezik a rendelkezésre álláshoz, az adatvesztéshez nem biztosítunk SLA-t.

### <a name="will-my-cache-be-available-during-scaling"></a>Elérhető lesz a gyorsítótár a skálázás során?
* A **standard** és a **prémium** gyorsítótárak továbbra is elérhetők a skálázási művelet során. A rendszer azonban a standard és a prémium gyorsítótárak skálázása, valamint az alapszintű és a standard szintű gyorsítótárak közötti skálázás során is előfordulhatnak. Ezek a kapcsolati visszaírások várhatóan kis méretűek lesznek, és a Redis-ügyfeleknek azonnal újra létre kell hozniuk a kapcsolatot.
* Az **alapszintű** gyorsítótárak offline állapotban vannak a skálázási műveletek során. Az alapszintű gyorsítótárak továbbra is elérhetők, ha az **alapszintű** és a **standard** közötti méretre van kiterjedően, de előfordulhat, hogy kis Ha hiba lép fel a kapcsolaton, a Redis-ügyfeleknek azonnal újra létre kell hozniuk a kapcsolatot.


### <a name="scaling-limitations-with-geo-replication"></a>Korlátozások méretezése a Geo-replikációval

Miután hozzáadta a földrajzi replikálási kapcsolatot két gyorsítótár között, a továbbiakban nem kezdeményezhet méretezési műveletet, vagy nem módosíthatja a fürtben lévő szegmensek számát. A parancsok kiválasztásához le kell állítania a gyorsítótár összekapcsolását. További információ: [geo-replikáció konfigurálása](cache-how-to-geo-replication.md).


### <a name="operations-that-are-not-supported"></a>Nem támogatott műveletek
* Magasabb díjszabási szintet nem lehet alacsonyabb díjszabási szinten méretezni.
  * **Prémium** szintű gyorsítótárból nem méretezhető **standard** vagy **alapszintű** gyorsítótár.
  * **Standard** szintű gyorsítótárból nem méretezhető **alapszintű** gyorsítótár.
* **Alapszintű** gyorsítótárból **szabványos** gyorsítótárra méretezheti, de a méret nem módosítható egyszerre. Ha más méretre van szüksége, egy későbbi skálázási műveletet is végrehajthat a kívánt méretre.
* **Alapszintű** gyorsítótárból nem lehet közvetlenül **prémium** szintű gyorsítótárra méretezni. Az első skálázás az **alapszintű** a **standardra** egy méretezési művelet során, majd a **standard** és a **prémium** közötti skálázás egy későbbi művelet során.
* A **C0 (250 MB)** mérete nem méretezhető nagyobb méretre.

Ha a skálázási művelet meghiúsul, a szolgáltatás megkísérli visszaállítani a műveletet, és a gyorsítótár visszaállítja az eredeti méretet.


### <a name="how-long-does-scaling-take"></a>Mennyi ideig tart a skálázás?
A skálázási idő attól függ, hogy mennyi az adatok a gyorsítótárban, és nagyobb mennyiségű adattal hosszabb időt vesz igénybe. A skálázás körülbelül 20 percet vesz igénybe. A fürtözött gyorsítótárak esetében a skálázás körülbelül 20 percet vesz igénybe.

### <a name="how-can-i-tell-when-scaling-is-complete"></a>Honnan tudhatom meg, hogy mikor fejeződik be a skálázás?
A Azure Portalban láthatja a skálázási műveletet. A skálázás befejeződése után a gyorsítótár állapota **fut** értékre változik.

<!-- IMAGES -->

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png
