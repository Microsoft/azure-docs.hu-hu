---
title: Egy Azure Cache for Redis méretezése
description: Megtudhatja, hogyan skálázhatóak Azure Cache for Redis-példányok a Azure Portal, valamint olyan eszközökkel, mint Azure PowerShell és az Azure CLI
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 43db8d4c094ec1b08a24c29fdaccf97f63ef29b9
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833975"
---
# <a name="scale-an-azure-cache-for-redis-instance"></a>Egy Azure Cache for Redis méretezése
Azure Cache for Redis különböző gyorsítótár-ajánlatokkal rendelkezik, amelyek rugalmasságot biztosítanak a gyorsítótár méretének és funkcióinak megválasztásában. Alapszintű, standard vagy prémium szintű gyorsítótárak esetében a létrehozásuk után módosíthatja annak méretét és rétegét, hogy lépést tartson az alkalmazás igényeivel. Ez a cikk bemutatja, hogyan skáláz a gyorsítótárat a Azure Portal, valamint olyan eszközökkel, mint a Azure PowerShell és az Azure CLI.

## <a name="when-to-scale"></a>Mikor érdemes méretezni
A gyorsítótár [állapotának](cache-how-to-monitor.md) és teljesítményének figyelése és Azure Cache for Redis a gyorsítótár figyelése, valamint annak meghatározása, hogy mikor kell méretezni a gyorsítótárat. 

A következő metrikák figyelése segít meghatározni, hogy szükség van-e skálázhatóra.

* Redis Server Load
* Memóriahasználat
* Hálózati sávszélesség
* CPU-használat

Ha úgy dönt, hogy a gyorsítótár már nem elég az alkalmazás követelményeinek, skálázhat egy nagyobb vagy kisebb gyorsítótár-tarifacsomagra, amely megfelelő az alkalmazás számára. További információ a használt gyorsítótár-tarifacsomag meghatározásáról: [A megfelelő tarifacsomag kiválasztása.](cache-overview.md#choosing-the-right-tier)

## <a name="scale-a-cache"></a>Gyorsítótár méretezése
A gyorsítótár méretezéshez keresse [meg](cache-configure.md#configure-azure-cache-for-redis-settings) a gyorsítótárat  a [Azure Portal,](https://portal.azure.com) majd kattintson az Erőforrás menü Méretezés **parancsára.**

![Méretezés](./media/cache-how-to-scale/redis-cache-scale-menu.png)

Válassza ki a kívánt tarifacsomagot a **Tarifacsomag kiválasztása** panelen, majd kattintson a **Kijelölés elemre.**

![Tarifacsomag][redis-cache-pricing-tier-blade]


A következő korlátozásokkal skálázhat egy másik tarifacsomagra:

* Magasabb tarifacsomagról nem skálázhat alacsonyabb tarifacsomagra.
  * Prémium szintű gyorsítótárról **nem** skálázhat le Standard vagy **Alapszintű** **gyorsítótárra.**
  * Standard gyorsítótárról alapszintű  gyorsítótárra nem **skálázhat** le.
* Az alapszintű **gyorsítótárról** standard  gyorsítótárra skálázhat, de a méretet nem módosíthatja egyszerre. Ha más méretre van szüksége, ezt követően a kívánt méretre skálázhatja a műveletet.
* Alapszintű gyorsítótárról nem  skálázhat közvetlenül prémium **szintű gyorsítótárra.** Először skálázhat **alapszintűről** **Standardra** egy skálázási műveletben, majd egy későbbi skálázási műveletben **Standardról** **Prémiumra.**
* Nagyobb méretről nem skálázhat le **250 MB-os C0-méretre.** Ugyanakkor az ugyanazon a tarifacsomagon belül bármilyen más méretre is leskálabhat. Például a C5 Standardről a C1 Standard-ra skálázhat le.
 
Amíg a gyorsítótár az új tarifacsomagra skáláz, **a** méretezés állapota megjelenik a Azure Cache for Redis **panelen.**

![Méretezés][redis-cache-scaling]

Ha a skálázás befejeződött, az állapot Skálázásról **Fut** **állapotra változik.**

## <a name="how-to-automate-a-scaling-operation"></a>Méretezési művelet automatizálása
A Azure Portal gyorsítótárpéldányainak skálázása mellett a skálázást a PowerShell-parancsmagok, az Azure CLI és a Microsoft Azure Management Libraries (MAML) használatával is használhatja. 

* [Méretezés a PowerShell használatával](#scale-using-powershell)
* [Skálázás az Azure CLI használatával](#scale-using-azure-cli)
* [Méretezés MAML használatával](#scale-using-maml)

### <a name="scale-using-powershell"></a>Méretezés a PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A PowerShell-Azure Cache for Redis a [Set-AzRedisCache](/powershell/module/az.rediscache/set-azrediscache) parancsmaggal skálázhatja a , vagy tulajdonságok `Size` `Sku` `ShardCount` módosításakor. Az alábbi példa bemutatja, hogyan skálázhat egy nevű `myCache` gyorsítótárat egy 2,5 GB-os gyorsítótárra. 

```powershell
   Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB
```

A PowerShell-méretezéssel kapcsolatos további információkért lásd: Méretezési Azure Cache for Redis [PowerShell használatával.](cache-how-to-manage-redis-cache-powershell.md#scale)

### <a name="scale-using-azure-cli"></a>Skálázás az Azure CLI használatával
A Azure Cache for Redis-példányok Azure CLI használatával való méretezéséhez hívja meg a parancsot, és adja át a kívánt méretezési művelettől függően az új méretet, termékváltozatot vagy fürtméretet is magában foglaló konfigurációs `azure rediscache set` módosításokat.

Az Azure CLI-val való méretezéssel kapcsolatos további információkért lásd a meglévő erőforrások [beállításainak Azure Cache for Redis.](cache-manage-cli.md#scale)

### <a name="scale-using-maml"></a>Méretezés MAML használatával
Ha a Azure Cache for Redis-példányokat a [Microsoft Azure Management Libraries (MAML)](https://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/)használatával méretezné, hívja meg a metódust, és adja át az új `IRedisOperations.CreateOrUpdate` méretet a `RedisProperties.SKU.Capacity` számára.

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

További információkért tekintse meg a Manage Azure Cache for Redis using MAML sample (A [maml-Azure Cache for Redis kezelése)](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) mintát.

## <a name="scaling-faq"></a>Méretezés – gyakori kérdések
Az alábbi lista a skálázható alkalmazásokkal kapcsolatos gyakori Azure Cache for Redis tartalmazza.

* [Skálázhatok prémium szintű gyorsítótárakra, illetve a gyorsítótárakról vagy gyorsítótárakra?](#can-i-scale-to-from-or-within-a-premium-cache)
* [A skálázás után módosítanom kell a gyorsítótár nevét vagy a hozzáférési kulcsaimat?](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
* [Hogyan működik a skálázás?](#how-does-scaling-work)
* [Elveszítek adatokat a gyorsítótárból a skálázás során?](#will-i-lose-data-from-my-cache-during-scaling)
* [A méretezés hatással van az egyéni adatbázisok beállításra?](#is-my-custom-databases-setting-affected-during-scaling)
* [A gyorsítótár elérhető lesz a skálázás során?](#will-my-cache-be-available-during-scaling)
* Ha konfigurálva van a georeplikáció, miért nem tudom skálázni a gyorsítótárat vagy módosítani a fürtben lévő szegmenseket?
* [Nem támogatott műveletek](#operations-that-are-not-supported)
* [Mennyi ideig tart a skálázás?](#how-long-does-scaling-take)
* [Honnan tudom, hogy mikor fejeződött be a skálázás?](#how-can-i-tell-when-scaling-is-complete)

### <a name="can-i-scale-to-from-or-within-a-premium-cache"></a>Skálázhatok prémium szintű gyorsítótárakra, illetve a gyorsítótárakról vagy gyorsítótárakra?
* Prémium szintű gyorsítótárról  nem skálázhat  le alapszintű vagy **standard** tarifacsomagra.
* Az egyik Prémium szintű **gyorsítótár** tarifacsomagja skálázhat egy másikra.
* Alapszintű gyorsítótárról nem  skálázhat közvetlenül prémium **szintű gyorsítótárra.** Először skálázhat **alapszintűről** **Standardra** egy skálázási műveletben, majd egy későbbi skálázási műveletben **Standardról** **Prémiumra.**
* Ha engedélyezte a fürtözést a **prémium** szintű gyorsítótár létrehozásakor, módosíthatja [a fürt méretét.](cache-how-to-premium-clustering.md#cluster-size) Ha a gyorsítótárat a fürtözés engedélyezése nélkül hozta létre, később is konfigurálhatja a fürtözést.
  
  További információ: [Fürtözés konfigurálása](cache-how-to-premium-clustering.md)prémium szintű Azure Cache for Redis.

### <a name="after-scaling-do-i-have-to-change-my-cache-name-or-access-keys"></a>A skálázás után módosítanom kell a gyorsítótár nevét vagy a hozzáférési kulcsaimat?
Nem, a gyorsítótár neve és kulcsai nem változnak a skálázási műveletek során.

### <a name="how-does-scaling-work"></a>Hogyan működik a skálázás?
* Ha egy **alapszintű** gyorsítótárat más méretre skáláz, a rendszer leállít és új gyorsítótárat hoz létre az új mérettel. Ez idő alatt a gyorsítótár nem érhető el, és a gyorsítótárban lévő összes adat elveszik.
* Ha egy **alapszintű** gyorsítótárat standard szintű gyorsítótárra skáláz, **a** rendszer kiépít egy replika-gyorsítótárat, és átmásolja az adatokat az elsődleges gyorsítótárból a replikagyorsítótárba. A gyorsítótár a skálázási folyamat során is elérhető marad.
* Ha egy **standard** szintű gyorsítótárat más méretre vagy prémium szintű gyorsítótárra méretez, az egyik replika leáll, és újra kiépíti az új méretre, és átviti az adatokat, majd a másik replika feladatátvételt hajt végre az újra kiépítése előtt, hasonlóan ahhoz a folyamathoz, amely az egyik gyorsítótár-csomópont meghibásodása esetén történik. 

### <a name="will-i-lose-data-from-my-cache-during-scaling"></a>Elveszítek adatokat a gyorsítótárból a skálázás során?
* Ha egy **alapszintű** gyorsítótárat új méretre skáláz, az összes adat elveszik, és a gyorsítótár nem érhető el a skálázási művelet során.
* Ha egy **alapszintű** gyorsítótárat **standard** gyorsítótárra méretez, a rendszer általában megőrzi a gyorsítótárban lévő adatokat.
* Ha egy **standard** szintű gyorsítótárat nagyobb méretre vagy szintre, vagy a **Prémium** szintű gyorsítótárat nagyobb méretre skáláz, a rendszer általában minden adatot megőriz. Ha egy **Standard** vagy **Prémium** szintű gyorsítótárat kisebb méretre skáláz le, az adatok elveszhetnek attól függően, hogy mennyi adat található a gyorsítótárban, és milyen az új méret a skálázáskor. Ha az adatok elvesznek a leméretezéskor, a kulcsok az [allkeys-lru](https://redis.io/topics/lru-cache) kiépítési szabályzat használatával vannak kivezetve. 

### <a name="is-my-custom-databases-setting-affected-during-scaling"></a>A méretezés hatással van az egyéni adatbázisok beállításra?
Ha a gyorsítótár létrehozása során egyéni értéket konfigurált a beállításhoz, ne feledje, hogy egyes tarifacsomagok `databases` [adatbáziskorlátai eltérőek.](cache-configure.md#databases) Ebben a forgatókönyvben a skálázás során az alábbi szempontokat kell figyelembe venni:

* Ha az aktuális szintnél alacsonyabb korlátú tarifacsomagra `databases` skáláz:
  * Ha az alapértelmezett számot használja, amely minden tarifacsomag `databases` esetében 16, akkor nem veszhet el adat.
  * Ha olyan egyéni számot használ, amely azon szint korlátain belül esik, amelyre skálázást használ, a rendszer megőrzi ezt a beállítást, és nem vesz el `databases` `databases` adat.
  * Ha olyan egyéni számot használ, amely meghaladja az új szint korlátait, a beállítás az új szint korlátaira csökken, és az eltávolított adatbázisokban minden `databases` `databases` adat elveszik.
* Ha az aktuális szintnél azonos vagy magasabb korlátú tarifacsomagra skáláz, a rendszer megőrzi a beállítást, és nem vesz el `databases` `databases` adat.

Bár a standard és a prémium szintű gyorsítótárak rendelkezésre állása 99,9%-os SLA-t biztosít, adatvesztésre nincs SLA.

### <a name="will-my-cache-be-available-during-scaling"></a>A gyorsítótár elérhető lesz a skálázás során?
* **A standard** **és prémium** szintű gyorsítótárak a skálázási művelet során is elérhetők maradnak. A kapcsolati három pont azonban a Standard és a Prémium gyorsítótárak skálázása, valamint az Alapszintű és Standard gyorsítótárak között is előfordulhat. Ezek a kapcsolati három pont várhatóan kis méretűek lesznek, és a Redis-ügyfeleknek azonnal képesnek kell lennie újra létrehozni a kapcsolatot.
* **Az** alapszintű gyorsítótárak különböző méretű skálázási műveletek során offline állapotban vannak. Az alapszintű gyorsítótárak az **Alapszintűről** a **Standardra** való skálázáskor is elérhetők maradnak, de kis kapcsolati három ponttal is előfordulhatnak. Ha kapcsolati három pont történik, a Redis-ügyfeleknek azonnal képesnek kell lennie újra létrehozni a kapcsolatot.


### <a name="scaling-limitations-with-geo-replication"></a>Skálázás korlátozásai georeplikációval

Miután hozzáadott egy georeplikációs hivatkozást két gyorsítótár között, nem fog tudni skálázási műveletet kezdeményezni, vagy módosítani a fürtben lévő szegmensek számát. Ezeknek a parancsoknak a kiadásához le kell mondania a gyorsítótárat. További információ: [Georeplikáció konfigurálása.](cache-how-to-geo-replication.md)


### <a name="operations-that-are-not-supported"></a>Nem támogatott műveletek
* Magasabb tarifacsomagról nem skálázhat alacsonyabb tarifacsomagra.
  * Prémium szintű gyorsítótárról **nem** skálázhat le Standard vagy **Alapszintű** **gyorsítótárra.**
  * Standard gyorsítótárról alapszintű  gyorsítótárra nem **skálázhat** le.
* Az alapszintű **gyorsítótárról** standard  gyorsítótárra skálázhat, de a méretet nem módosíthatja egyszerre. Ha más méretre van szüksége, ezt követően a kívánt méretre skálázhatja a műveletet.
* Alapszintű gyorsítótárról nem  skálázhat közvetlenül prémium **szintű gyorsítótárra.** Először **skálázhat alapszintűről** **Standardra** egy skálázási műveletben, majd skálázhat **a Standardról** a **Prémiumra** egy későbbi műveletben.
* Nagyobb méretről nem skálázhat le A **C0 (250 MB) méretre.**

Ha egy skálázási művelet meghiúsul, a szolgáltatás megpróbálja visszaállni a műveletet, és a gyorsítótár visszaáll az eredeti méretre.


### <a name="how-long-does-scaling-take"></a>Mennyi ideig tart a skálázás?
A skálázási idő attól függ, hogy mennyi adat található a gyorsítótárban, a nagyobb adatmennyiség pedig hosszabb időt vesz igénybe. A skálázás körülbelül 20 percet vesz igénybe. Fürtözött gyorsítótárak esetén a skálázás szegmensenként körülbelül 20 percet vesz igénybe.

### <a name="how-can-i-tell-when-scaling-is-complete"></a>Honnan tudom, hogy mikor fejeződött be a skálázás?
A Azure Portal láthatja a skálázás folyamatban lévő műveletét. Ha a skálázás befejeződött, a gyorsítótár állapota Fut **állapotúra változik.**

<!-- IMAGES -->

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png
