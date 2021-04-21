---
title: Az Azure Cache for Redis kezelése Azure PowerShell
description: Megtudhatja, hogyan hajthat végre felügyeleti feladatokat a Azure Cache for Redis a Azure PowerShell.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/13/2017
ms.author: yegu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ac1456e2dc640e1076857da78cf4145b61ea69d4
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832787"
---
# <a name="manage-azure-cache-for-redis-with-azure-powershell"></a>Az Azure Cache for Redis kezelése Azure PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](cache-how-to-manage-redis-cache-powershell.md)
> * [Azure CLI](cache-manage-cli.md)
> 
> 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ez a témakör bemutatja, hogyan hajthat végre olyan gyakori feladatokat, mint például a Azure Cache for Redis-példányok létrehozása, frissítése és méretezése, hozzáférési kulcsok újragenerálása, valamint a gyorsítótárak információinak megtekintése. A PowerShell-parancsmagok teljes Azure Cache for Redis lásd: Azure Cache for Redis [parancsmagok.](/powershell/module/az.rediscache)

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

További információ a klasszikus üzembe helyezési modellről: Azure Resource Manager és a klasszikus üzembe helyezés: Az üzembe helyezési modellek és az [erőforrások állapota.](../azure-resource-manager/management/deployment-models.md)

## <a name="prerequisites"></a>Előfeltételek
Ha már telepítette a Azure PowerShell, a Azure PowerShell 1.0.0-s vagy újabb verziójával kell lennie. A parancssorban Azure PowerShell paranccsal ellenőrizheti a telepített Azure PowerShell verzióját.

```azurepowershell
    Get-Module Az | format-table version
```

Először ezzel a paranccsal kell bejelentkeznie az Azure-ba.

```azurepowershell
    Connect-AzAccount
```

Adja meg az Azure-fiókja e-mail-címét és jelszavát Microsoft Azure bejelentkezési párbeszédpanelen.

Ezután, ha több Azure-előfizetéssel rendelkezik, be kell állítania az Azure-előfizetését. Az aktuális előfizetések listájának listájáért futtassa ezt a parancsot.

```azurepowershell
    Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Az előfizetés megadásához futtassa a következő parancsot. A következő példában az előfizetés neve `ContosoSubscription` .

```azurepowershell
    Select-AzSubscription -SubscriptionName ContosoSubscription
```

Mielőtt használhatja a Windows PowerShell a Azure Resource Manager, a következőkre lesz szüksége:

* Windows PowerShell 3.0-s vagy 4.0-s verzió. Az alkalmazás verziójának Windows PowerShell írja be a következőt: , és ellenőrizze, hogy a `$PSVersionTable` `PSVersion` értéke 3.0 vagy 4.0. A kompatibilis verzió telepítéséhez lásd: [Windows Management Framework 3.0.](https://www.microsoft.com/download/details.aspx?id=34595)

Az oktatóanyagban látható parancsmagokkal a következő parancsmag használatával Get-Help részletes segítséget.

```azurepowershell
    Get-Help <cmdlet-name> -Detailed
```

Ha például segítségre van szüksége a `New-AzRedisCache` parancsmaghoz, írja be a következőt:

```azurepowershell
    Get-Help New-AzRedisCache -Detailed
```

### <a name="how-to-connect-to-other-clouds"></a>Csatlakozás más felhőkhöz
Alapértelmezés szerint az Azure-környezet `AzureCloud` a , amely a globális Azure-felhőpéldányt jelöli. Egy másik példányhoz való csatlakozáshoz használja a parancsot a vagy a - parancssori kapcsolóval a kívánt környezet vagy `Connect-AzAccount` `-Environment` környezet `EnvironmentName` nevével.

Az elérhető környezetek listájának a listához futtassa a `Get-AzEnvironment` parancsmagot.

### <a name="to-connect-to-the-azure-government-cloud"></a>Csatlakozás az Azure Government Cloudhoz
Az Azure Government Cloudhoz való csatlakozáshoz használja az alábbi parancsok egyikét.

```azurepowershell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
```

vagy

```azurepowershell
    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureUSGovernment)
```

Ha gyorsítótárat hoz létre a Azure Government Cloudban, használja az alábbi helyek egyikét.

* USGov Virginia
* USGov Iowa

A felhőalapú felhővel kapcsolatos további Azure Government [lásd](https://azure.microsoft.com/features/gov/) a Microsoft Azure Government és [Microsoft Azure Government útmutatót.](../azure-government/documentation-government-developer-guide.md)

### <a name="to-connect-to-the-azure-china-cloud"></a>Csatlakozás az Azure China Cloudhoz
Az Azure China Cloudhoz való csatlakozáshoz használja az alábbi parancsok egyikét.

```azurepowershell
    Connect-AzAccount -EnvironmentName AzureChinaCloud
```

vagy

```azurepowershell
    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureChinaCloud)
```

Gyorsítótár az Azure China Cloudban való létrehozásához használja az alábbi helyek egyikét.

* Kelet-Kína
* Észak-Kína

További információ az Azure China Cloudról: A 21Vianet által Kínában üzemeltetett [AzureChinaCloud for Azure.](https://www.windowsazure.cn/)

### <a name="to-connect-to-microsoft-azure-germany"></a>Csatlakozás Microsoft Azure Germany
A Microsoft Azure Germany az alábbi parancsok egyikével csatlakozhat.

```azurepowershell
    Connect-AzAccount -EnvironmentName AzureGermanCloud
```

vagy

```azurepowershell
    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureGermanCloud)
```

Gyorsítótár létrehozásához a Microsoft Azure Germany használja az alábbi helyek egyikét.

* Közép-Németország
* Északkelet-Németország

További információ a Microsoft Azure Germany: [Microsoft Azure Germany.](https://azure.microsoft.com/overview/clouds/germany/)

### <a name="properties-used-for-azure-cache-for-redis-powershell"></a>A PowerShellhez Azure Cache for Redis tulajdonságok
Az alábbi táblázat a gyakran használt paraméterek tulajdonságait és leírását tartalmazza a Azure Cache for Redis példányok létrehozásakor és Azure PowerShell.

| Paraméter | Leírás | Alapértelmezett |
| --- | --- | --- |
| Name |A gyorsítótár neve | |
| Hely |A gyorsítótár helye | |
| ResourceGroupName |A gyorsítótár létrehozásához használt erőforráscsoport neve | |
| Méret |A gyorsítótár mérete. Érvényes értékek: P1, P2, P3, P4, C0, C1, C2, C3, C4, C5, C6, 250MB, 1GB, 2,5GB, 6GB, 13 GB, 26 GB, 53 GB |1 GB |
| ShardCount (Szegmens száma) |Azon szegmensek száma, amelyek akkor hozhatók létre, amikor prémium szintű gyorsítótárat hoz létre engedélyezett fürtözéssel. Érvényes értékek: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 | |
| Termékváltozat |Megadja a gyorsítótár SKU-ját. Érvényes értékek: Alapszintű, Standard, Prémium |Standard |
| RedisConfiguration |Megadja a Redis konfigurációs beállításait. Az egyes beállításokkal kapcsolatos részletekért tekintse meg a [következő RedisConfiguration tulajdonságok táblázatát.](#redisconfiguration-properties) | |
| EnableNonSslPort |Azt jelzi, hogy a nem SSL-port engedélyezve van-e. |Hamis |
| MaxMemoryPolicy |Ez a paraméter elavult – használja helyette a RedisConfiguration paramétert. | |
| StaticIP |A gyorsítótár virtuális hálózaton való üzemeltetésekor a egyedi IP-címet ad meg a gyorsítótár alhálózatában. Ha nincs megtéve, az alhálózatból kiválaszt egyet. | |
| Alhálózat |A gyorsítótár virtuális hálózatban való üzemeltetésekor a határozza meg annak az alhálózatnak a nevét, amelyben a gyorsítótárat üzembe helyezni kell. | |
| VirtualNetwork |A gyorsítótár virtuális hálózatban való üzemeltetésekor megadja annak a virtuális hálózatnak az erőforrás-azonosítóját, amelyen a gyorsítótárat üzembe helyezni kell. | |
| KeyType (Kulcstípus) |Megadja, hogy melyik hozzáférési kulcsot kell újragenerálni a hozzáférési kulcsok megújításakor. Érvényes értékek: Elsődleges, Másodlagos | |

### <a name="redisconfiguration-properties"></a>RedisConfiguration tulajdonságok
| Tulajdonság | Leírás | Árképzési szintek |
| --- | --- | --- |
| rdb-backup-enabled |A [Redis-adatmegőrzés engedélyezése](cache-how-to-premium-persistence.md) |Csak Prémium |
| rdb-storage-connection-string |A Redis-adatmegőrzéshez szükséges kapcsolati [sztring a tárfiókhoz](cache-how-to-premium-persistence.md) |Csak Prémium |
| rdb-backup-frequency |A [Redis-adatmegőrzés biztonsági mentésének gyakorisága](cache-how-to-premium-persistence.md) |Csak Prémium |
| maxmemory-reserved |A nem [gyorsítótárazási folyamatok számára](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) fenntartott memória konfigurálása |Standard és Prémium |
| maxmemory-policy |A gyorsítótár [kiürítő szabályzatának](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) konfigurálása |Minden tarifacsomag |
| notify-keyspace-events |[Kulcstér-értesítések konfigurálása](cache-configure.md#keyspace-notifications-advanced-settings) |Standard és Prémium |
| hash-max-ziplist-entries |[Memóriaoptimalizálás konfigurálása](https://redis.io/topics/memory-optimization) kis összesített adattípusok esetén |Standard és Prémium |
| hash-max-ziplist-value |[Memóriaoptimalizálás konfigurálása](https://redis.io/topics/memory-optimization) kis összesített adattípusok esetén |Standard és Prémium |
| set-max-intset-entries |[Memóriaoptimalizálás konfigurálása](https://redis.io/topics/memory-optimization) kis összesített adattípusok esetén |Standard és Prémium |
| zset-max-ziplist-entries |[Memóriaoptimalizálás konfigurálása](https://redis.io/topics/memory-optimization) kis összesített adattípusok esetén |Standard és Prémium |
| zset-max-ziplist-value |[Memóriaoptimalizálás konfigurálása](https://redis.io/topics/memory-optimization) kis összesített adattípusok esetén |Standard és Prémium |
| adatbázisokban |Konfigurálja az adatbázisok számát. Ez a tulajdonság csak a gyorsítótár létrehozásakor konfigurálható. |Standard és Prémium |

## <a name="to-create-an-azure-cache-for-redis"></a>Új Azure Cache for Redis
Új Azure Cache for Redis a [New-AzRedisCache parancsmag](/powershell/module/az.rediscache/new-azrediscache) használatával jön létre.

> [!IMPORTANT]
> Amikor első alkalommal hoz létre Azure Cache for Redis előfizetésben a Azure Portal használatával, a portál regisztrálja az előfizetés `Microsoft.Cache` névterét. Ha a PowerShell használatával próbálja létrehozni az első Azure Cache for Redis egy előfizetésben, először regisztrálnia kell a névteret a következő paranccsal; ellenkező esetben a parancsmagok, például a és a `New-AzRedisCache` `Get-AzRedisCache` sikertelenek.
> 
> `Register-AzResourceProvider -ProviderNamespace "Microsoft.Cache"`
> 
> 

Az elérhető paraméterek listájának és azok leírásának a () számára való megtekintése érdekében `New-AzRedisCache` futtassa a következő parancsot.

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

A gyorsítótár alapértelmezett paraméterekkel való létrehozásához futtassa a következő parancsot.

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"
```

`ResourceGroupName`A , és paraméterek kötelezőek, de a többi nem kötelező, `Name` `Location` és alapértelmezett értékekkel rendelkezik. Az előző parancs futtatása létrehoz egy Standard termékváltozatot Azure Cache for Redis megadott névvel, hellyel és erőforráscsoporttal, amely 1 GB méretű, és a nem SSL-port le van tiltva.

Prémium szintű gyorsítótár létrehozásához P1 (6 GB – 60 GB), P2 (13 GB – 130 GB), P3 (26 GB – 260 GB) vagy P4 (53 GB – 530 GB) méretet adjon meg. A fürtözés engedélyezéséhez adja meg a szegmensek számát a `ShardCount` paraméterrel. Az alábbi példa egy P1 prémium szintű gyorsítótárat hoz létre 3 szegmenssel. A P1 prémium szintű gyorsítótár mérete 6 GB, és mivel három szegmenst adtunk meg, a teljes méret 18 GB (3 x 6 GB).

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3
```

A paraméter értékeinek megadásához az értékeket `RedisConfiguration` `{}` kulcs/érték párokként kell megadni, például `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}` : . Az alábbi példa egy szabványos 1 GB-os gyorsítótárat hoz létre a következővel konfigurált maxmemory házirendekkel és `allkeys-random` kulcstér-értesítésekkel: `KEA` . További információ: [Kulcstér-értesítések (speciális beállítások) és](cache-configure.md#keyspace-notifications-advanced-settings) [Memória-szabályzatok.](cache-configure.md#memory-policies)

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}
```

<a name="databases"></a>

## <a name="to-configure-the-databases-setting-during-cache-creation"></a>Az adatbázisok beállításának konfigurálása a gyorsítótár létrehozása során
A `databases` beállítás csak a gyorsítótár létrehozása során konfigurálható. Az alábbi példa egy prémium P3 (26 GB) gyorsítótárat hoz létre 48 adatbázissal a [New-AzRedisCache](/powershell/module/az.rediscache/New-azRedisCache) parancsmaggal.

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P3 -RedisConfiguration @{"databases" = "48"}
```

További információ a `databases` tulajdonságról: Default Azure Cache for Redis server configuration (Alapértelmezett [Azure Cache for Redis-kiszolgáló konfigurációja).](cache-configure.md#default-redis-server-configuration) A gyorsítótár [New-AzRedisCache](/powershell/module/az.rediscache/new-azrediscache) parancsmag használatával való létrehozásával kapcsolatos további információkért lásd az előző, Új gyorsítótár létrehozása Azure Cache for Redis szakaszt.

## <a name="to-update-an-azure-cache-for-redis"></a>Frissítés Azure Cache for Redis
Azure Cache for Redis-példányok a [Set-AzRedisCache parancsmag](/powershell/module/az.rediscache/Set-azRedisCache) használatával frissülnek.

Az elérhető paraméterek listájának és azok leírásának a () számára való megtekintése érdekében `Set-AzRedisCache` futtassa a következő parancsot.

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

A parancsmaggal frissítheti az olyan tulajdonságokat, mint a `Set-AzRedisCache` , a , és az `Size` `Sku` `EnableNonSslPort` `RedisConfiguration` értékek. 

A következő parancs frissíti a myCache nevű Azure Cache for Redis maxmemory-policyt.

```azurepowershell
    Set-AzRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}
```

<a name="scale"></a>

## <a name="to-scale-an-azure-cache-for-redis"></a>Méretezési Azure Cache for Redis
`Set-AzRedisCache` A egy példány skálázható Azure Cache for Redis , `Size` `Sku` vagy tulajdonságok `ShardCount` módosításakor. 

> [!NOTE]
> A gyorsítótár PowerShell használatával való skálázása ugyanazokra a korlátokra és irányelvekre vonatkozik, mint a gyorsítótár méretezése a Azure Portal. A következő korlátozásokkal skálázhat egy másik tarifacsomagra.
> 
> * Magasabb tarifacsomagról nem skálázhat alacsonyabb tarifacsomagra.
> * Prémium szintű gyorsítótárról **nem** skálázhat le Standard vagy **Alapszintű** **gyorsítótárra.**
> * Standard gyorsítótárról nem  skálázhat le alapszintű **gyorsítótárra.**
> * Az alapszintű **gyorsítótárról** standard  gyorsítótárra skálázhat, de a méretet nem módosíthatja egyszerre. Ha más méretre van szüksége, ezt követően a kívánt méretre skálázhatja a műveletet.
> * Alapszintű gyorsítótárról nem  skálázhat közvetlenül prémium **szintű gyorsítótárra.** Egy skálázási **műveletben az Alapszintűről** a **Standardra,** majd egy későbbi méretezési műveletben **a Standardról** a **Prémiumra** kell skálázni.
> * Nagyobb méretről nem skálázhat le A **C0 (250 MB) méretre.**
> 
> További információ: [How to Scale Azure Cache for Redis.](cache-how-to-scale.md)
> 
> 

Az alábbi példa bemutatja, hogyan skálázhat egy nevű `myCache` gyorsítótárat egy 2,5 GB-os gyorsítótárra. Vegye figyelembe, hogy ez a parancs alapszintű és standard gyorsítótárakhoz is működik.

```azurepowershell
    Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB
```

A parancs kibocsátását követően a rendszer visszaadja a gyorsítótár állapotát (a híváshoz `Get-AzRedisCache` hasonlóan). Figyelje meg, hogy `ProvisioningState` a `Scaling` a következő: .

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

A skálázás befejeztét a `ProvisioningState` következőre módosítja: `Succeeded` . Ha egy későbbi skálázást kell végrehajtania, például alapszintűről Standardra, majd méretet kell megváltoztatnia, meg kell várnia az előző művelet befejezését, vagy az alábbihoz hasonló hibaüzenetet kell kapnia.

```azurepowershell
    Set-AzRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.
```

## <a name="to-get-information-about-an-azure-cache-for-redis"></a>Információk lekérte egy Azure Cache for Redis
A gyorsítótárakra vonatkozó információkat a [Get-AzRedisCache parancsmag](/powershell/module/az.rediscache/get-azrediscache) használatával lehet lekérni.

Az elérhető paraméterek listájának és azok leírásának a listájához `Get-AzRedisCache` futtassa a következő parancsot.

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

Az aktuális előfizetés összes gyorsítótárával kapcsolatos információk visszaadása érdekében futtassa a következőt `Get-AzRedisCache` paraméterek nélkül: .

```azurepowershell
    Get-AzRedisCache
```

Egy adott erőforráscsoport összes gyorsítótárával kapcsolatos információk visszaadása érdekében futtassa a következőt `Get-AzRedisCache` a `ResourceGroupName` paraméterrel: .

```azurepowershell
    Get-AzRedisCache -ResourceGroupName myGroup
```

Egy adott gyorsítótárra vonatkozó információk visszaadása érdekében futtassa a(z) adatokat a(z) paraméterrel, amely tartalmazza a gyorsítótár nevét, valamint a paramétert az adott gyorsítótárat `Get-AzRedisCache` `Name` tartalmazó `ResourceGroupName` erőforráscsoporttal.

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

## <a name="to-retrieve-the-access-keys-for-an-azure-cache-for-redis"></a>A hozzáférési kulcsok lekérése egy Azure Cache for Redis
A gyorsítótár hozzáférési kulcsait a [Get-AzRedisCacheKey](/powershell/module/az.rediscache/Get-azRedisCacheKey) parancsmag használatával lehet lekérni.

Az elérhető paraméterek listájának és azok leírásának a listájához `Get-AzRedisCacheKey` futtassa a következő parancsot.

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

A gyorsítótár kulcsának lekéréshez hívja meg a parancsmagot, és adja meg a gyorsítótár nevét annak az erőforráscsoportnak a nevét, amely `Get-AzRedisCacheKey` a gyorsítótárat tartalmazza.

```azurepowershell
    PS C:\> Get-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup

    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=
```

## <a name="to-regenerate-access-keys-for-your-azure-cache-for-redis"></a>Hozzáférési kulcsok újragenerálásának Azure Cache for Redis
A gyorsítótár hozzáférési kulcsait a [New-AzRedisCacheKey](/powershell/module/az.rediscache/New-azRedisCacheKey) parancsmag használatával tudja újragenerálni.

Az elérhető paraméterek listájának és azok leírásának a listájához `New-AzRedisCacheKey` futtassa a következő parancsot.

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

A gyorsítótár elsődleges vagy másodlagos kulcsának újragenerál való generáláshoz hívja meg a parancsmagot, adja meg a nevet és az erőforráscsoportot, és adja meg a vagy a `New-AzRedisCacheKey` `Primary` `Secondary` `KeyType` paramétert. A következő példában a gyorsítótár másodlagos hozzáférési kulcsa lesz újragenerálva.

```azurepowershell
    PS C:\> New-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary

    Confirm
    Are you sure you want to regenerate Secondary key for Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=
```

## <a name="to-delete-an-azure-cache-for-redis"></a>Egy fiók Azure Cache for Redis
Egy új Azure Cache for Redis a [Remove-AzRedisCache](/powershell/module/az.rediscache/remove-azrediscache) parancsmag használatával.

Az elérhető paraméterek listájának és azok leírásának a listájához `Remove-AzRedisCache` futtassa a következő parancsot.

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


## <a name="to-import-an-azure-cache-for-redis"></a>Adatfájl importálása Azure Cache for Redis
Az adatokat importálhatja egy Azure Cache for Redis-példányba a `Import-AzRedisCache` parancsmag használatával.

> [!IMPORTANT]
> Az Import/Export csak a prémium szintű [gyorsítótárak esetében](cache-overview.md#service-tiers) érhető el. További információ az Import/Exportról: Adatok importálása és [exportálása a Azure Cache for Redis.](cache-how-to-import-export-data.md)
> 
> 

Az elérhető paraméterek listájának és azok leírásának a listájához `Import-AzRedisCache` futtassa a következő parancsot.

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


A következő parancs adatokat importál a SAS URI által megadott blobból a Azure Cache for Redis.

```azurepowershell
    PS C:\>Import-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Files @("https://mystorageaccount.blob.core.windows.net/mycontainername/blobname?sv=2015-04-05&sr=b&sig=caIwutG2uDa0NZ8mjdNJdgOY8%2F8mhwRuGNdICU%2B0pI4%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwd") -Force
```

## <a name="to-export-an-azure-cache-for-redis"></a>Alkalmazás exportálása Azure Cache for Redis
A parancsmag használatával exportálhat adatokat `Export-AzRedisCache` Azure Cache for Redis-példányból.

> [!IMPORTANT]
> Az Import/Export csak a prémium szintű [gyorsítótárak esetében](cache-overview.md#service-tiers) érhető el. További információ az Import/Exportról: Adatok importálása és [exportálása a Azure Cache for Redis.](cache-how-to-import-export-data.md)
> 
> 

Az elérhető paraméterek listájának és azok leírásának a listájához `Export-AzRedisCache` futtassa a következő parancsot.

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


A következő parancs adatokat exportál egy Azure Cache for Redis-példányból a SAS URI által megadott tárolóba.

```azurepowershell
    PS C:\>Export-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Prefix "blobprefix"
    -Container "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2015-04-05&sr=c&sig=HezZtBZ3DURmEGDduauE7
    pvETY4kqlPI8JCNa8ATmaw%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwdl"
```

## <a name="to-reboot-an-azure-cache-for-redis"></a>A virtuális gép Azure Cache for Redis
A parancsmag használatával újraindíthatja `Reset-AzRedisCache` Azure Cache for Redis-példányt.

> [!IMPORTANT]
> Az újraindítás csak a prémium szintű [gyorsítótárak esetében](cache-overview.md#service-tiers) érhető el. A gyorsítótár újraindításával kapcsolatos további információkért lásd: [Gyorsítótár-felügyelet – újraindítás.](cache-administration.md#reboot)
> 
> 

Az elérhető paraméterek listájának és azok leírásának a listájához `Reset-AzRedisCache` futtassa a következő parancsot.

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


Az alábbi parancs újraindítja a megadott gyorsítótár mindkét csomópontját.

```azurepowershell
    PS C:\>Reset-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -RebootType "AllNodes"
    -Force
```


## <a name="next-steps"></a>Következő lépések
Az Azure-beli Windows PowerShell további információért tekintse meg a következő forrásokat:

* [Azure Cache for Redis msdn parancsmag dokumentációja](/powershell/module/az.rediscache)
* [Azure Resource Manager parancsmagok:](/powershell/module/)Ismerje meg, hogyan használhatja a parancsmagokat a Azure Resource Manager modulban.
* [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez:](../azure-resource-manager/templates/deploy-portal.md)Megtudhatja, hogyan hozhat létre és kezelhet erőforráscsoportokat a Azure Portal.
* [Azure blog:](https://azure.microsoft.com/blog/)További tudnivalók az Azure új funkcióiról.
* [Windows PowerShell blog:](https://devblogs.microsoft.com/powershell/)A Windows PowerShell.
* ["Hey, Scripting Guy!" Blog:](https://devblogs.microsoft.com/scripting/tag/hey-scripting-guy/)Valós tippeket és trükköket kaphat a Windows PowerShell közösségtől.
