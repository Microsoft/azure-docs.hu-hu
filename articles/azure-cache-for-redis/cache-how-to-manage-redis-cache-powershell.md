---
title: Az Azure cache kezelése a Redis Azure PowerShell
description: Ismerje meg, hogyan végezheti el az Azure cache felügyeleti feladatait a Redis Azure PowerShell használatával.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/13/2017
ms.author: yegu
ms.openlocfilehash: 88e433dbfa87d8ea483789b1fd838c62a6a481c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92536759"
---
# <a name="manage-azure-cache-for-redis-with-azure-powershell"></a>Az Azure cache kezelése a Redis Azure PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](cache-how-to-manage-redis-cache-powershell.md)
> * [Azure CLI](cache-manage-cli.md)
> 
> 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ebből a témakörből megtudhatja, hogyan hajthat végre olyan gyakori műveleteket, mint például az Azure cache létrehozása, frissítése és skálázása Redis-példányokhoz, hozzáférési kulcsok újragenerálása, valamint a gyorsítótárral kapcsolatos információk megtekintése. Az Azure cache Redis PowerShell-parancsmagok teljes listájáért lásd: [Azure cache for Redis parancsmagok](/powershell/module/az.rediscache).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

A klasszikus üzemi modellel kapcsolatos további információkért lásd: [Azure Resource Manager és klasszikus üzembe helyezési modell: az üzembe helyezési modellek és az erőforrások állapotának ismertetése](../azure-resource-manager/management/deployment-models.md).

## <a name="prerequisites"></a>Előfeltételek
Ha már telepítette a Azure PowerShell-t, akkor Azure PowerShell 1.0.0 vagy újabb verzió szükséges. Az ezzel a paranccsal telepített Azure PowerShell verzióját a Azure PowerShell parancssorban tekintheti meg.

```azurepowershell
    Get-Module Az | format-table version
```

Először be kell jelentkeznie az Azure-ba ezzel a paranccsal.

```azurepowershell
    Connect-AzAccount
```

Adja meg az Azure-fiókja és a jelszavának e-mail-címét a Microsoft Azure bejelentkezési párbeszédpanelen.

Ha több Azure-előfizetéssel rendelkezik, be kell állítania az Azure-előfizetését. A jelenlegi előfizetések listájának megtekintéséhez futtassa ezt a parancsot.

```azurepowershell
    Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Az előfizetés megadásához futtassa a következő parancsot. A következő példában az előfizetés neve: `ContosoSubscription` .

```azurepowershell
    Select-AzSubscription -SubscriptionName ContosoSubscription
```

A Windows PowerShell és a Azure Resource Manager használata előtt a következőkre lesz szüksége:

* Windows PowerShell, 3,0-es vagy 4,0-es verzió. A Windows PowerShell verziójának megkereséséhez írja be a következőt: `$PSVersionTable` és ellenőrizze, hogy az értéke `PSVersion` 3,0 vagy 4,0. A kompatibilis verziók telepítéséhez lásd: [Windows Management Framework 3,0](https://www.microsoft.com/download/details.aspx?id=34595).

Az oktatóanyagban látható parancsmagok részletes súgójának megjelenítéséhez használja a Get-Help parancsmagot.

```azurepowershell
    Get-Help <cmdlet-name> -Detailed
```

Ha például segítséget szeretne kérni a `New-AzRedisCache` parancsmaghoz, írja be a következőt:

```azurepowershell
    Get-Help New-AzRedisCache -Detailed
```

### <a name="how-to-connect-to-other-clouds"></a>Kapcsolódás más felhőkhöz
Alapértelmezés szerint az Azure-környezet a `AzureCloud` globális Azure Cloud-példányt jelképezi. Egy másik példányhoz való kapcsolódáshoz használja a `Connect-AzAccount` parancsot a `-Environment` vagy a- `EnvironmentName` Command Line kapcsolóval a kívánt környezet vagy környezet nevével.

Az elérhető környezetek listájának megtekintéséhez futtassa a `Get-AzEnvironment` parancsmagot.

### <a name="to-connect-to-the-azure-government-cloud"></a>Kapcsolódás a Azure Government felhőhöz
A Azure Government felhőhöz való kapcsolódáshoz használja a következő parancsok egyikét.

```azurepowershell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
```

vagy

```azurepowershell
    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureUSGovernment)
```

Ha gyorsítótárat szeretne létrehozni a Azure Government-felhőben, használja az alábbi helyszínek egyikét.

* USGov Virginia
* USGov Iowa

További információ a Azure Government felhőről: [Microsoft Azure Government](https://azure.microsoft.com/features/gov/) és [Microsoft Azure Government fejlesztői útmutató](../azure-government/documentation-government-developer-guide.md).

### <a name="to-connect-to-the-azure-china-cloud"></a>Kapcsolódás az Azure-beli kínai felhőhöz
Az Azure China-felhőhöz való kapcsolódáshoz használja a következő parancsok egyikét.

```azurepowershell
    Connect-AzAccount -EnvironmentName AzureChinaCloud
```

vagy

```azurepowershell
    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureChinaCloud)
```

Ha gyorsítótárat szeretne létrehozni az Azure China-felhőben, használja az alábbi helyszínek egyikét.

* Kelet-Kína
* Észak-Kína

További információ az Azure China Cloud-ról: [AzureChinaCloud for Azure által üzemeltetett 21Vianet Kínában](https://www.windowsazure.cn/).

### <a name="to-connect-to-microsoft-azure-germany"></a>Kapcsolódás Microsoft Azure Germanyhoz
A Microsoft Azure Germanyhoz való kapcsolódáshoz használja a következő parancsok egyikét.

```azurepowershell
    Connect-AzAccount -EnvironmentName AzureGermanCloud
```

vagy

```azurepowershell
    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureGermanCloud)
```

Ha Microsoft Azure Germany gyorsítótárat szeretne létrehozni, használja a következő helyszínek egyikét.

* Közép-Németország
* Északkelet-Németország

További információ a Microsoft Azure Germanyről: [Microsoft Azure Germany](https://azure.microsoft.com/overview/clouds/germany/).

### <a name="properties-used-for-azure-cache-for-redis-powershell"></a>Az Azure cache Redis PowerShell-hez használt tulajdonságai
A következő táblázat a gyakran használt paraméterek tulajdonságait és leírásait tartalmazza, amikor az Azure cache-t az Azure PowerShell használatával hozza létre és kezeli az Redis-példányokhoz.

| Paraméter | Leírás | Alapértelmezett |
| --- | --- | --- |
| Name |A gyorsítótár neve | |
| Hely |A gyorsítótár helye | |
| ResourceGroupName |Az erőforráscsoport neve, amelyben létre szeretné hozni a gyorsítótárat | |
| Méret |A gyorsítótár mérete Érvényes értékek: P1, P2, P3, P4, c0, C1, C2, C3, C4, C5, C6, 250MB, 1GB, 2,5 GB, 6GB, 13GB, 26GB, 53GB |1 GB |
| ShardCount |Azon szegmensek száma, amelyeket létre kell hozni a prémium szintű gyorsítótár létrehozásakor, ha a fürtözés engedélyezve van. Az érvényes értékek: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 | |
| Termékváltozat |A gyorsítótár SKU-jának meghatározása. Az érvényes értékek a következők: alapszintű, standard, prémium |Standard |
| RedisConfiguration |Meghatározza a Redis konfigurációs beállításait. Az egyes beállításokkal kapcsolatos részletekért tekintse meg a következő [RedisConfiguration-tulajdonságok](#redisconfiguration-properties) táblázatot. | |
| EnableNonSslPort |Azt jelzi, hogy engedélyezve van-e a nem SSL-port. |Hamis |
| MaxMemoryPolicy |Ez a paraméter elavult – használja helyette a RedisConfiguration. | |
| StaticIP |A gyorsítótár egy VNET való üzemeltetése esetén a gyorsítótárban egy egyedi IP-címet ad meg az alhálózatban. Ha nincs megadva, az egyiket az alhálózatból választjuk ki. | |
| Alhálózat |A gyorsítótár VNET való üzemeltetése esetén annak az alhálózatnak a nevét adja meg, amelyben a gyorsítótárat telepíteni szeretné. | |
| VirtualNetwork |Ha a gyorsítótárat a VNET tárolja, meghatározza annak a VNET az erőforrás-AZONOSÍTÓját, amelyben a gyorsítótárat telepíteni szeretné. | |
| KeyType |Megadja, hogy melyik hozzáférési kulcsot kell újragenerálni a hozzáférési kulcsok megújításakor. Az érvényes értékek a következők: elsődleges, másodlagos | |

### <a name="redisconfiguration-properties"></a>RedisConfiguration tulajdonságai
| Tulajdonság | Leírás | Árképzési szintek |
| --- | --- | --- |
| RDB – biztonsági mentés engedélyezve |Annak megadása, hogy engedélyezve van-e a [Redis adatmegőrzés](cache-how-to-premium-persistence.md) |Csak prémium |
| RDB-Storage – kapcsolatok – sztring |A [Redis adatmegőrzéshez](cache-how-to-premium-persistence.md) használt Storage-fiókhoz tartozó kapcsolódási karakterlánc |Csak prémium |
| RDB – biztonsági mentés gyakorisága |A [Redis adatmegőrzési](cache-how-to-premium-persistence.md) szolgáltatásának biztonsági mentési gyakorisága |Csak prémium |
| maxmemory – fenntartott |A nem gyorsítótárazott folyamatok számára [fenntartott memória](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) konfigurálása |Standard és prémium |
| maxmemory – szabályzat |Beállítja a gyorsítótár [kizárási házirendjét](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) . |Az összes díjszabási csomag |
| értesítés – alapterületek – események |Lemezterület- [értesítések](cache-configure.md#keyspace-notifications-advanced-settings) konfigurálása |Standard és prémium |
| kivonat – Max-ZipList – bejegyzések |A [memória optimalizálásának](https://redis.io/topics/memory-optimization) beállítása kis aggregált adattípusokhoz |Standard és prémium |
| kivonat-Max-ZipList-Value |A [memória optimalizálásának](https://redis.io/topics/memory-optimization) beállítása kis aggregált adattípusokhoz |Standard és prémium |
| set-Max-intset-bejegyzések |A [memória optimalizálásának](https://redis.io/topics/memory-optimization) beállítása kis aggregált adattípusokhoz |Standard és prémium |
| zset-Max-ZipList-bejegyzések |A [memória optimalizálásának](https://redis.io/topics/memory-optimization) beállítása kis aggregált adattípusokhoz |Standard és prémium |
| zset-Max-ZipList-Value |A [memória optimalizálásának](https://redis.io/topics/memory-optimization) beállítása kis aggregált adattípusokhoz |Standard és prémium |
| adatbázisokban |Az adatbázisok számának beállítása. Ezt a tulajdonságot csak a gyorsítótár létrehozásakor lehet konfigurálni. |Standard és prémium |

## <a name="to-create-an-azure-cache-for-redis"></a>Azure cache létrehozása a Redis-hez
A [New-AzRedisCache](/powershell/module/az.rediscache/new-azrediscache) parancsmaggal hozhatók létre új Azure cache a Redis-példányokhoz.

> [!IMPORTANT]
> Amikor először hoz létre egy Azure-gyorsítótárat a Redis egy előfizetésben a Azure Portal használatával, a portál regisztrálja az `Microsoft.Cache` adott előfizetés névterét. Ha egy előfizetésben a PowerShell használatával próbálja meg létrehozni az első Azure cache-Redis, először regisztrálnia kell a névteret a következő parancs használatával. egyéb parancsmagok, például `New-AzRedisCache` és `Get-AzRedisCache` sikertelenek.
> 
> `Register-AzResourceProvider -ProviderNamespace "Microsoft.Cache"`
> 
> 

Az elérhető paraméterek és a hozzájuk tartozó leírások megjelenítéséhez `New-AzRedisCache` futtassa a következő parancsot.

```azurepowershell
    PS C:\> Get-Help New-AzRedisCache -detailed

    NAME
        New-AzRedisCache

    SYNOPSIS
        Creates a new Azure Cache for Redis.


    SYNTAX
        New-AzRedisCache -Name <String> -ResourceGroupName <String> -Location <String> [-RedisVersion <String>]
        [-Size <String>] [-Sku <String>] [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort
        <Boolean>] [-ShardCount <Integer>] [-VirtualNetwork <String>] [-Subnet <String>] [-StaticIP <String>]
        [<CommonParameters>]


    DESCRIPTION
        The New-AzRedisCache cmdlet creates a new Azure Cache for Redis.


    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis to create.

        -ResourceGroupName <String>
            Name of resource group in which to create the Azure Cache for Redis.

        -Location <String>
            Location in which to create the Azure Cache for Redis.

        -RedisVersion <String>
            RedisVersion is deprecated and will be removed in future release.

        -Size <String>
            Size of the Azure Cache for Redis. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of Azure Cache for Redis. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value, databases.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Cache for Redis. If no value is provided, the default value is false and the
            non-SSL port will be disabled. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        -VirtualNetwork <String>
            The exact ARM resource ID of the virtual network to deploy the Azure Cache for Redis in. Example format: /subscriptions/{
            subid}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicNetwork/VirtualNetworks/{vnetName}

        -Subnet <String>
            Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network.

        -StaticIP <String>
            Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

Az alapértelmezett paraméterekkel rendelkező gyorsítótár létrehozásához futtassa a következő parancsot.

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"
```

`ResourceGroupName``Name`a, a és a `Location` kötelező paraméterek, de a többi nem kötelező, és alapértelmezett értékekkel rendelkezik. Az előző parancs futtatása létrehoz egy standard SKU Azure cache-t a Redis-példányhoz a megadott névvel, hellyel és erőforráscsoporthoz, amely 1 GB méretű, és a nem SSL-port le van tiltva.

Prémium gyorsítótár létrehozásához meg kell adni a P1 (6 GB-60 GB), P2 (13 GB-130 GB), P3 (26 GB-260 GB) vagy P4 (53 GB-530 GB) méretet. A fürtözés engedélyezéséhez a paraméterrel kell megadnia a szegmensek darabszámát `ShardCount` . Az alábbi példa egy P1 Premium gyorsítótárat hoz létre 3 szegmenssel. A P1 prémium gyorsítótár mérete 6 GB, és mivel három szegmenst adtunk meg, a teljes méret 18 GB (3 x 6 GB).

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3
```

A paraméter értékének megadásához az értékeket a következőhöz `RedisConfiguration` `{}` hasonló kulcs/érték párokba kell foglalni: `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}` . Az alábbi példa egy standard 1 GB-os gyorsítótárat hoz létre, amely a `allkeys-random` maxmemory szabályzattal és a által konfigurált, a-val beállított webtárhely `KEA` További információ: a [lemezterület-értesítések (speciális beállítások)](cache-configure.md#keyspace-notifications-advanced-settings) és a [memória-házirendek](cache-configure.md#memory-policies).

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}
```

<a name="databases"></a>

## <a name="to-configure-the-databases-setting-during-cache-creation"></a>Az adatbázisok beállítása a gyorsítótár létrehozásakor
A `databases` beállítás csak a gyorsítótár létrehozásakor konfigurálható. A következő példa egy prémium P3 (26 GB) gyorsítótárat hoz létre a 48-adatbázisokkal a [New-AzRedisCache](/powershell/module/az.rediscache/New-azRedisCache) parancsmag használatával.

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P3 -RedisConfiguration @{"databases" = "48"}
```

További információ a `databases` tulajdonságról: [alapértelmezett Azure cache a Redis Server Configuration](cache-configure.md#default-redis-server-configuration). A gyorsítótár a [New-AzRedisCache](/powershell/module/az.rediscache/new-azrediscache) parancsmaggal történő létrehozásával kapcsolatos további információkért tekintse meg a következőt: Azure cache létrehozása Redis szakasz.

## <a name="to-update-an-azure-cache-for-redis"></a>Azure cache frissítése a Redis-hez
A Redis példányok Azure gyorsítótára a [set-AzRedisCache](/powershell/module/az.rediscache/Set-azRedisCache) parancsmag használatával frissül.

Az elérhető paraméterek és a hozzájuk tartozó leírások megjelenítéséhez `Set-AzRedisCache` futtassa a következő parancsot.

```azurepowershell
    PS C:\> Get-Help Set-AzRedisCache -detailed

    NAME
        Set-AzRedisCache

    SYNOPSIS
        Set Azure Cache for Redis updatable parameters.

    SYNTAX
        Set-AzRedisCache -Name <String> -ResourceGroupName <String> [-Size <String>] [-Sku <String>]
        [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort <Boolean>] [-ShardCount
        <Integer>] [<CommonParameters>]

    DESCRIPTION
        The Set-AzRedisCache cmdlet sets Azure Cache for Redis parameters.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis to update.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        -Size <String>
            Size of the Azure Cache for Redis. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of Azure Cache for Redis. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Cache for Redis. The default value is null and no change will be made to the
            currently configured value. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

A parancsmag használható a tulajdonságok, például,, `Set-AzRedisCache` `Size` `Sku` `EnableNonSslPort` és értékek frissítésére `RedisConfiguration` . 

A következő parancs frissíti az Azure cache maxmemory-házirendjét a myCache nevű Redis.

```azurepowershell
    Set-AzRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}
```

<a name="scale"></a>

## <a name="to-scale-an-azure-cache-for-redis"></a>Azure cache méretezése Redis
`Set-AzRedisCache` a Redis-példány Azure-gyorsítótárának méretezésére használható, ha a `Size` , `Sku` vagy a `ShardCount` Tulajdonságok módosulnak. 

> [!NOTE]
> A gyorsítótárnak a PowerShell használatával történő skálázása ugyanazokra a korlátozásokra és irányelvekre vonatkozik, mint a Azure Portal gyorsítótárának skálázása. A következő korlátozásokkal méretezheti át egy másik díjszabási szintet.
> 
> * Magasabb díjszabási szintet nem lehet alacsonyabb díjszabási szinten méretezni.
> * **Prémium** szintű gyorsítótárból nem méretezhető **standard** vagy **alapszintű** gyorsítótár.
> * **Standard** szintű gyorsítótárból nem méretezhető **alapszintű** gyorsítótár.
> * **Alapszintű** gyorsítótárból **szabványos** gyorsítótárra méretezheti, de a méret nem módosítható egyszerre. Ha más méretre van szüksége, egy későbbi skálázási műveletet is végrehajthat a kívánt méretre.
> * **Alapszintű** gyorsítótárból nem lehet közvetlenül **prémium** szintű gyorsítótárra méretezni. Az **alapszintű** és a **standard** közötti méretezést egy skálázási műveletben, majd a **standard** és a **prémium** között egy későbbi skálázási műveletben kell megadni.
> * A **C0 (250 MB)** mérete nem méretezhető nagyobb méretre.
> 
> További információ: az [Azure cache skálázása a Redis](cache-how-to-scale.md).
> 
> 

Az alábbi példa bemutatja, hogyan méretezheti át a gyorsítótárat `myCache` egy 2,5 GB-os gyorsítótárba. Vegye figyelembe, hogy ez a parancs egy alapszintű vagy egy standard szintű gyorsítótár esetében is működik.

```azurepowershell
    Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB
```

A parancs kiadását követően a rendszer a gyorsítótár állapotát adja vissza (a híváshoz hasonlóan `Get-AzRedisCache` ). Vegye figyelembe, hogy a `ProvisioningState` `Scaling` .

```azurepowershell
    PS C:\> Set-AzRedisCache -Name myCache -ResourceGroupName myGroup -Size 2.5GB


    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/mygroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Scaling
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 150], [notify-keyspace-events, KEA],
                         [maxmemory-delta, 150]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : mygroup
    PrimaryKey         : ....
    SecondaryKey       : ....
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :
```

A skálázási művelet befejezésekor a `ProvisioningState` módosítások a következőre változnak: `Succeeded` . Ha egy későbbi skálázási műveletet kell végrehajtania, például az alapszintről a standard értékre való váltásra, majd a méret módosítására, meg kell várnia, amíg az előző művelet befejeződik, vagy a következőhöz hasonló hibaüzenetet kap.

```azurepowershell
    Set-AzRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.
```

## <a name="to-get-information-about-an-azure-cache-for-redis"></a>A Redis Azure-gyorsítótárával kapcsolatos információk beolvasása
A gyorsítótárra vonatkozó információkat a [Get-AzRedisCache](/powershell/module/az.rediscache/get-azrediscache) parancsmag használatával kérheti le.

Az elérhető paraméterek és a hozzájuk tartozó leírások megjelenítéséhez `Get-AzRedisCache` futtassa a következő parancsot.

```azurepowershell
    PS C:\> Get-Help Get-AzRedisCache -detailed

    NAME
        Get-AzRedisCache

    SYNOPSIS
        Gets details about a single cache or all caches in the specified resource group or all caches in the current
        subscription.

    SYNTAX
        Get-AzRedisCache [-Name <String>] [-ResourceGroupName <String>] [<CommonParameters>]

    DESCRIPTION
        The Get-AzRedisCache cmdlet gets the details about a cache or caches depending on input parameters. If both
        ResourceGroupName and Name parameters are provided then Get-AzRedisCache will return details about the
        specific cache name provided.

        If only ResourceGroupName is provided than it will return details about all caches in the specified resource group.

        If no parameters are given than it will return details about all caches the current subscription.

    PARAMETERS
        -Name <String>
            The name of the cache. When this parameter is provided along with ResourceGroupName, Get-AzRedisCache
            returns the details for the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache or caches. If ResourceGroupName is provided with Name
            then Get-AzRedisCache returns the details of the cache specified by Name. If only the ResourceGroup
            parameter is provided, then details for all caches in the resource group are returned.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

Ha a jelenlegi előfizetésben lévő összes gyorsítótárra vonatkozó információt szeretne visszaadni, paraméterek nélkül futtassa a parancsot `Get-AzRedisCache` .

```azurepowershell
    Get-AzRedisCache
```

Egy adott erőforráscsoport összes gyorsítótárával kapcsolatos információk visszaadásához futtassa a (z `Get-AzRedisCache` `ResourceGroupName` ) paramétert.

```azurepowershell
    Get-AzRedisCache -ResourceGroupName myGroup
```

Egy adott gyorsítótárra vonatkozó információk visszaadásához futtassa a parancsot a `Get-AzRedisCache` `Name` gyorsítótár nevét tartalmazó paraméterrel, valamint a `ResourceGroupName` paramétert a gyorsítótárat tartalmazó erőforráscsoporthoz.

```azurepowershell
    PS C:\> Get-AzRedisCache -Name myCache -ResourceGroupName myGroup

    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/myGroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Succeeded
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 62], [notify-keyspace-events, KEA],
                         [maxclients, 1000]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : myGroup
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :
```

## <a name="to-retrieve-the-access-keys-for-an-azure-cache-for-redis"></a>Azure cache elérési kulcsainak beolvasása a Redis
A gyorsítótárhoz tartozó hozzáférési kulcsok lekéréséhez használhatja a [Get-AzRedisCacheKey](/powershell/module/az.rediscache/Get-azRedisCacheKey) parancsmagot.

Az elérhető paraméterek és a hozzájuk tartozó leírások megjelenítéséhez `Get-AzRedisCacheKey` futtassa a következő parancsot.

```azurepowershell
    PS C:\> Get-Help Get-AzRedisCacheKey -detailed

    NAME
        Get-AzRedisCacheKey

    SYNOPSIS
        Gets the accesskeys for the specified Azure Cache for Redis.


    SYNTAX
        Get-AzRedisCacheKey -Name <String> -ResourceGroupName <String> [<CommonParameters>]

    DESCRIPTION
        The Get-AzRedisCacheKey cmdlet gets the access keys for the specified cache.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

A gyorsítótár kulcsainak lekéréséhez hívja meg a `Get-AzRedisCacheKey` parancsmagot, és adja meg a gyorsítótár nevét a gyorsítótárban található erőforráscsoport nevével.

```azurepowershell
    PS C:\> Get-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup

    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=
```

## <a name="to-regenerate-access-keys-for-your-azure-cache-for-redis"></a>Az Azure cache elérési kulcsainak újragenerálása a Redis
A gyorsítótár elérési kulcsainak újragenerálása érdekében használhatja a [New-AzRedisCacheKey](/powershell/module/az.rediscache/New-azRedisCacheKey) parancsmagot.

Az elérhető paraméterek és a hozzájuk tartozó leírások megjelenítéséhez `New-AzRedisCacheKey` futtassa a következő parancsot.

```azurepowershell
    PS C:\> Get-Help New-AzRedisCacheKey -detailed

    NAME
        New-AzRedisCacheKey

    SYNOPSIS
        Regenerates the access key of an Azure Cache for Redis.

    SYNTAX
        New-AzRedisCacheKey -Name <String> -ResourceGroupName <String> -KeyType <String> [-Force] [<CommonParameters>]

    DESCRIPTION
        The New-AzRedisCacheKey cmdlet regenerate the access key of an Azure Cache for Redis.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        -KeyType <String>
            Specifies whether to regenerate the primary or secondary access key. Possible values are Primary or Secondary.

        -Force
            When the Force parameter is provided, the specified access key is regenerated without any confirmation prompts.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

A gyorsítótár elsődleges vagy másodlagos kulcsának újragenerálása érdekében hívja meg a `New-AzRedisCacheKey` parancsmagot, és adja meg a nevet, az erőforráscsoportot, majd adja meg `Primary` `Secondary` a vagy a `KeyType` paramétert. A következő példában a gyorsítótár másodlagos elérési kulcsa újra létrejön.

```azurepowershell
    PS C:\> New-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary

    Confirm
    Are you sure you want to regenerate Secondary key for Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=
```

## <a name="to-delete-an-azure-cache-for-redis"></a>Azure cache törlése a Redis-hez
Ha törölni szeretne egy Azure-gyorsítótárat a Redis, használja a [Remove-AzRedisCache](/powershell/module/az.rediscache/remove-azrediscache) parancsmagot.

Az elérhető paraméterek és a hozzájuk tartozó leírások megjelenítéséhez `Remove-AzRedisCache` futtassa a következő parancsot.

```azurepowershell
    PS C:\> Get-Help Remove-AzRedisCache -detailed

    NAME
        Remove-AzRedisCache

    SYNOPSIS
        Remove Azure Cache for Redis if exists.

    SYNTAX
        Remove-AzRedisCache -Name <String> -ResourceGroupName <String> [-Force] [-PassThru] [<CommonParameters>

    DESCRIPTION
        The Remove-AzRedisCache cmdlet removes an Azure Cache for Redis if it exists.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis to remove.

        -ResourceGroupName <String>
            Name of the resource group of the cache to remove.

        -Force
            When the Force parameter is provided, the cache is removed without any confirmation prompts.

        -PassThru
            By default Remove-AzRedisCache removes the cache and does not return any value. If the PassThru par
            is provided then Remove-AzRedisCache returns a boolean value indicating the success of the operatio

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

A következő példában a nevű gyorsítótár `myCache` el lesz távolítva.

```azurepowershell
    PS C:\> Remove-AzRedisCache -Name myCache -ResourceGroupName myGroup

    Confirm
    Are you sure you want to remove Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
```


## <a name="to-import-an-azure-cache-for-redis"></a>Azure cache importálása a Redis-hez
A parancsmag használatával importálhat egy Azure-gyorsítótárba az Redis-példányokat `Import-AzRedisCache` .

> [!IMPORTANT]
> Az import/export csak a [prémium szintű](cache-overview.md#service-tiers) csomagok gyorsítótárai esetében érhető el. Az importálással/exportálással kapcsolatos további információkért lásd: [adatok importálása és exportálása az Azure cache-ben a Redis-hez](cache-how-to-import-export-data.md).
> 
> 

Az elérhető paraméterek és a hozzájuk tartozó leírások megjelenítéséhez `Import-AzRedisCache` futtassa a következő parancsot.

```azurepowershell
    PS C:\> Get-Help Import-AzRedisCache -detailed

    NAME
        Import-AzRedisCache

    SYNOPSIS
        Import data from blobs to Azure Cache for Redis.


    SYNTAX
        Import-AzRedisCache -Name <String> -ResourceGroupName <String> -Files <String[]> [-Format <String>] [-Force]
        [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Import-AzRedisCache cmdlet imports data from the specified blobs into Azure Cache for Redis.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -Files <String[]>
            SAS urls of blobs whose content should be imported into the cache.

        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.

        -Force
            When the Force parameter is provided, import will be performed without any confirmation prompts.

        -PassThru
            By default Import-AzRedisCache imports data in cache and does not return any value. If the PassThru
            parameter is provided then Import-AzRedisCache returns a boolean value indicating the success of the
            operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```


A következő parancs a SAS URI által megadott blob adatait importálja az Azure cache-be a Redis-hez.

```azurepowershell
    PS C:\>Import-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Files @("https://mystorageaccount.blob.core.windows.net/mycontainername/blobname?sv=2015-04-05&sr=b&sig=caIwutG2uDa0NZ8mjdNJdgOY8%2F8mhwRuGNdICU%2B0pI4%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwd") -Force
```

## <a name="to-export-an-azure-cache-for-redis"></a>Azure cache exportálása Redis
Az adatok az Redis-példány Azure cache-ből exportálhatók a `Export-AzRedisCache` parancsmag használatával.

> [!IMPORTANT]
> Az import/export csak a [prémium szintű](cache-overview.md#service-tiers) csomagok gyorsítótárai esetében érhető el. Az importálással/exportálással kapcsolatos további információkért lásd: [adatok importálása és exportálása az Azure cache-ben a Redis-hez](cache-how-to-import-export-data.md).
> 
> 

Az elérhető paraméterek és a hozzájuk tartozó leírások megjelenítéséhez `Export-AzRedisCache` futtassa a következő parancsot.

```azurepowershell
    PS C:\> Get-Help Export-AzRedisCache -detailed

    NAME
        Export-AzRedisCache

    SYNOPSIS
        Exports data from Azure Cache for Redis to a specified container.


    SYNTAX
        Export-AzRedisCache -Name <String> -ResourceGroupName <String> -Prefix <String> -Container <String> [-Format
        <String>] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Export-AzRedisCache cmdlet exports data from Azure Cache for Redis to a specified container.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -Prefix <String>
            Prefix to use for blob names.

        -Container <String>
            SAS url of container where data should be exported.

        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.

        -PassThru
            By default Export-AzRedisCache does not return any value. If the PassThru parameter is provided
            then Export-AzRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```


A következő parancs a Redis példány Azure cache-ből származó adatait exportálja a SAS URI által megadott tárolóba.

```azurepowershell
    PS C:\>Export-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Prefix "blobprefix"
    -Container "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2015-04-05&sr=c&sig=HezZtBZ3DURmEGDduauE7
    pvETY4kqlPI8JCNa8ATmaw%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwdl"
```

## <a name="to-reboot-an-azure-cache-for-redis"></a>Azure cache újraindítása a Redis
A parancsmag használatával újraindíthatja az Azure cache-t a Redis-példányhoz `Reset-AzRedisCache` .

> [!IMPORTANT]
> Az újraindítás csak a [prémium szintű](cache-overview.md#service-tiers) gyorsítótárak esetében érhető el. A gyorsítótár újraindításával kapcsolatos további információkért lásd: [gyorsítótár-felügyelet – újraindítás](cache-administration.md#reboot).
> 
> 

Az elérhető paraméterek és a hozzájuk tartozó leírások megjelenítéséhez `Reset-AzRedisCache` futtassa a következő parancsot.

```azurepowershell
    PS C:\> Get-Help Reset-AzRedisCache -detailed

    NAME
        Reset-AzRedisCache

    SYNOPSIS
        Reboot specified node(s) of an Azure Cache for Redis instance.


    SYNTAX
        Reset-AzRedisCache -Name <String> -ResourceGroupName <String> -RebootType <String> [-ShardId <Integer>]
        [-Force] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Reset-AzRedisCache cmdlet reboots the specified node(s) of an Azure Cache for Redis instance.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -RebootType <String>
            Which node to reboot. Possible values are "PrimaryNode", "SecondaryNode", "AllNodes".

        -ShardId <Integer>
            Which shard to reboot when rebooting a premium cache with clustering enabled.

        -Force
            When the Force parameter is provided, reset will be performed without any confirmation prompts.

        -PassThru
            By default Reset-AzRedisCache does not return any value. If the PassThru parameter is provided
            then Reset-AzRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```


A következő parancs újraindítja a megadott gyorsítótár mindkét csomópontját.

```azurepowershell
    PS C:\>Reset-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -RebootType "AllNodes"
    -Force
```


## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni a Windows PowerShell és az Azure használatával kapcsolatban, tekintse meg a következő forrásokat:

* [Az Azure cache for Redis parancsmag dokumentációja az MSDN-ben](/powershell/module/az.rediscache)
* [Azure Resource Manager parancsmagok](/powershell/module/): megtudhatja, hogyan használhatja a parancsmagokat a Azure Resource Manager modulban.
* [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/templates/deploy-portal.md): megtudhatja, hogyan hozhat létre és kezelhet erőforráscsoportokat a Azure Portalban.
* [Azure blog](https://azure.microsoft.com/blog/): Ismerkedjen meg az Azure új szolgáltatásaival.
* [Windows PowerShell blog](https://devblogs.microsoft.com/powershell/): Ismerkedjen meg a Windows PowerShell új szolgáltatásaival.
* ["Hey, Scripting Guy!" Blog](https://devblogs.microsoft.com/scripting/tag/hey-scripting-guy/): valós tippeket és trükköket szerezhet be a Windows PowerShell Közösségből.