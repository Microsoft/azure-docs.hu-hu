---
title: Azure cache kezelése a Redis az Azure klasszikus CLI használatával
description: Megtudhatja, hogyan telepítheti a klasszikus Azure CLI-t bármely platformra, hogyan használhatja azt az Azure-fiókhoz való kapcsolódáshoz, és hogyan hozhat létre és kezelhet Azure-gyorsítótárat a Redis a klasszikus CLI-ből.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 01/23/2017
ms.author: yegu
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7643f882d5ac330046c169e0a3f2fa4920331d4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92537694"
---
# <a name="how-to-create-and-manage-azure-cache-for-redis-using-the-azure-classic-cli"></a>Azure cache létrehozása és kezelése a Redis a klasszikus Azure CLI használatával
> [!div class="op_single_selector"]
> * [PowerShell](cache-how-to-manage-redis-cache-powershell.md)
> * [Klasszikus Azure CLI](cache-manage-cli.md)
>

Az Azure-beli klasszikus CLI nagyszerű lehetőséget biztosít az Azure-infrastruktúra bármely platformról való kezelésére. Ez a cikk bemutatja, hogyan hozhatja létre és kezelheti az Azure cache-t a Redis-példányok számára a klasszikus Azure CLI használatával.

[!INCLUDE [outdated-cli-content](../../includes/contains-classic-cli-content.md)]
> [!NOTE]
> A legújabb Azure CLI-parancsfájlokat az Azure [CLI Azure cache Redis-mintákhoz](cli-samples.md)című témakörben tekintheti meg.

## <a name="prerequisites"></a>Előfeltételek
Az Azure cache Redis-példányok Azure-beli klasszikus CLI használatával történő létrehozásához és kezeléséhez az alábbi lépéseket kell végrehajtania.

* Rendelkeznie kell egy Azure-fiókkal. Ha még nem rendelkezik ilyennel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) .
* [Telepítse a klasszikus Azure CLI](/cli/azure/install-classic-cli)-t.
* Az Azure CLI-telepítést személyes Azure-fiókkal, munkahelyi vagy iskolai Azure-fiókkal, valamint a parancs használatával jelentkezzen be a klasszikus CLI-vel `azure login` .
* A következő parancsok bármelyikének futtatása előtt váltson át a klasszikus CLI-t Resource Manager módba a parancs futtatásával `azure config mode arm` . További információ: az Azure-beli [klasszikus CLI használata Azure-erőforrások és-erőforráscsoportok kezeléséhez](../azure-resource-manager/management/manage-resources-cli.md).

## <a name="azure-cache-for-redis-properties"></a>Azure cache a Redis tulajdonságaihoz
Az Redis-példányok Azure gyorsítótárának létrehozásakor és frissítésekor a következő tulajdonságok használatosak.

| Tulajdonság | Kapcsoló (switch) | Leírás |
| --- | --- | --- |
| name |-n,--név |A Redis tartozó Azure cache neve. |
| erőforráscsoport |-g,--Resource-Group |Az erőforráscsoport neve. |
| location |-l,--hely |A gyorsítótár létrehozására szolgáló hely. |
| size |-z,--size |Az Azure cache mérete a Redis számára. Érvényes értékek: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4] |
| SKU |-x,--SKU |Redis-termékváltozat. Az alábbiak egyike lehet: [Basic, standard, Premium] |
| EnableNonSslPort |-e,--Enable-nem SSL-port |A Redis tartozó Azure cache EnableNonSslPort tulajdonsága. Adja hozzá ezt a jelzőt, ha engedélyezni szeretné a nem TLS/SSL portot a gyorsítótárhoz |
| Redis-konfiguráció |-c,--Redis-Configuration |Redis-konfiguráció. Itt adhatja meg a konfigurációs kulcsok és értékek JSON formátumú karakterláncát. Formátum: "{" ":" "," ":" "}" |
| Redis-konfiguráció |-f,--Redis-Configuration-file |Redis-konfiguráció. Itt adhatja meg a konfigurációs kulcsokat és értékeket tartalmazó fájl elérési útját. A következő fájl formátuma: {"": "", "": ""} |
| Szegmensek száma |-r,--szilánkok száma |Azoknak a szegmenseknek a száma, amelyeket egy prémium szintű fürt-gyorsítótárban kell létrehozni a fürtözéssel. |
| Virtual Network |-v,--Virtual-Network |A gyorsítótár egy VNET való üzemeltetése során a a virtuális hálózat pontos ARM-erőforrás-AZONOSÍTÓját adja meg a Redis tartozó Azure cache üzembe helyezéséhez. Példa formátum:/subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| kulcs típusa |-t,--Key-Type |A megújítani kívánt kulcs típusa. Érvényes értékek: [elsődleges, másodlagos] |
| StaticIP |-p,--Static-IP \<static-ip\> |A gyorsítótár egy VNET való üzemeltetése esetén a gyorsítótárban egy egyedi IP-címet ad meg az alhálózatban. Ha nincs megadva, az egyiket az alhálózatból választjuk ki. |
| Alhálózat |t,--alhálózat \<subnet\> |A gyorsítótár VNET való üzemeltetése esetén annak az alhálózatnak a nevét adja meg, amelyben a gyorsítótárat telepíteni szeretné. |
| VirtualNetwork |-v,--Virtual-Network \<virtual-network\> |A gyorsítótár egy VNET való üzemeltetése során a a virtuális hálózat pontos ARM-erőforrás-AZONOSÍTÓját adja meg a Redis tartozó Azure cache üzembe helyezéséhez. Példa formátum:/subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Előfizetés |-s,--előfizetés |Az előfizetés azonosítója. |

## <a name="see-all-azure-cache-for-redis-commands"></a>A Redis-parancsok összes Azure-gyorsítótárának megtekintése
Az Redis parancsok és a hozzájuk tartozó paraméterek összes Azure-gyorsítótárának megtekintéséhez használja az `azure rediscache -h` parancsot.

```azurecli
C:\>azure rediscache -h
help:    Commands to manage your Azure Cache for Redis(s)
help:
help:    Create an Azure Cache for Redis
help:      rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
help:
help:    Delete an existing Azure Cache for Redis
help:      rediscache delete [--name <name> --resource-group <resource-group> ]
help:
help:    List all Azure Cache for Redis within your Subscription or Resource Group
help:      rediscache list [options]
help:
help:    Show properties of an existing Azure Cache for Redis
help:      rediscache show [--name <name> --resource-group <resource-group>]
help:
help:    Change settings of an existing Azure Cache for Redis
help:      rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
help:
help:    Renew the authentication key for an existing Azure Cache for Redis
help:      rediscache renew-key [--name <name> --resource-group <resource-group> ]
help:
help:    Lists Primary and Secondary key of an existing Azure Cache for Redis
help:      rediscache list-keys [--name <name> --resource-group <resource-group>]
help:
help:    Options:
help:      -h, --help  output usage information
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="create-an-azure-cache-for-redis"></a>Az Azure Cache for Redis létrehozása
A következő paranccsal hozhat létre Azure cache-t a Redis számára:

```azurecli
    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
```

A paranccsal kapcsolatos további információkért futtassa a `azure rediscache create -h` parancsot.

```azurecli
C:\>azure rediscache create -h
help:    Create an Azure Cache for Redis
help:
help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
help:
help:    Options:
help:      -h, --help                                               output usage information
help:      -v, --verbose                                            use verbose output
help:      -vv                                                      more verbose with debug output
help:      --json                                                   use json output
help:      -n, --name <name>                                        Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>                    Name of the Resource Group
help:      -l, --location <location>                                Location to create cache.
help:      -z, --size <size>                                        Size of the Azure Cache for Redis. Valid values: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]
help:      -x, --sku <sku>                                          Redis SKU. Should be one of : [Basic, Standard, Premium]
help:      -e, --enable-non-ssl-port                                EnableNonSslPort property of the Azure Cache for Redis. Add this flag if you want to enable the non-TLS/SSL Port for your cache
help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here. Format:"{"<key1>":"<value1>","<key2>":"<value2>"}"
help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here. Format for the file entry: {"<key1>":"<value1>","<key2>":"<value2>"}
help:      -r, --shard-count <shard-count>                          Number of Shards to create on a Premium Cluster Cache
help:      -v, --virtual-network <virtual-network>                  The exact ARM resource ID of the virtual network to deploy the Azure Cache for Redis in. Example format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1
help:      -t, --subnet <subnet>                                    Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network
help:      -p, --static-ip <static-ip>                              Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network
help:      -s, --subscription <id>                                  the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="delete-an-existing-azure-cache-for-redis"></a>Meglévő Azure cache törlése a Redis-hez
Ha törölni szeretne egy Azure-gyorsítótárat a Redis, használja a következő parancsot:

```azurecli
    azure rediscache delete [--name <name> --resource-group <resource-group> ]
```

A paranccsal kapcsolatos további információkért futtassa a `azure rediscache delete -h` parancsot.

```azurecli
C:\>azure rediscache delete -h
help:    Delete an existing Azure Cache for Redis
help:
help:    Usage: rediscache delete [--name <name> --resource-group <resource-group> ]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group under which the cache exists
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="list-all-azure-cache-for-redis-within-your-subscription-or-resource-group"></a>Az előfizetésen vagy az Redis belül található összes Azure cache listázása
Az előfizetésben vagy az Redis lévő összes Azure cache listázásához használja az alábbi parancsot:

```azurecli
    azure rediscache list [options]
```

A paranccsal kapcsolatos további információkért futtassa a `azure rediscache list -h` parancsot.

```azurecli
C:\>azure rediscache list -h
help:    List all Azure Cache for Redis within your Subscription or Resource Group
help:
help:    Usage: rediscache list [options]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -g, --resource-group <resource-group>  Name of the Resource Group
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="show-properties-of-an-existing-azure-cache-for-redis"></a>Meglévő Azure cache tulajdonságainak megjelenítése a Redis
A Redis meglévő Azure cache tulajdonságainak megjelenítéséhez használja a következő parancsot:

```azurecli
    azure rediscache show [--name <name> --resource-group <resource-group>]
```

A paranccsal kapcsolatos további információkért futtassa a `azure rediscache show -h` parancsot.

```azurecli
C:\>azure rediscache show -h
help:    Show properties of an existing Azure Cache for Redis
help:
help:    Usage: rediscache show [--name <name> --resource-group <resource-group>]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

<a name="scale"></a>

## <a name="change-settings-of-an-existing-azure-cache-for-redis"></a>Meglévő Azure cache beállításainak módosítása a Redis
A Redis meglévő Azure cache beállításainak módosításához használja a következő parancsot:

```azurecli
    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
```

A paranccsal kapcsolatos további információkért futtassa a `azure rediscache set -h` parancsot.

```azurecli
C:\>azure rediscache set -h
help:    Change settings of an existing Azure Cache for Redis
help:
help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
help:
help:    Options:
help:      -h, --help                                               output usage information
help:      -v, --verbose                                            use verbose output
help:      -vv                                                      more verbose with debug output
help:      --json                                                   use json output
help:      -n, --name <name>                                        Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>                    Name of the Resource Group
help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here.
help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here.
help:      -s, --subscription <subscription>                        the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="renew-the-authentication-key-for-an-existing-azure-cache-for-redis"></a>Meglévő Azure cache hitelesítési kulcsának megújítása a Redis
A Redis meglévő Azure cache hitelesítési kulcsának megújításához használja a következő parancsot:

```azurecli
    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]
```

`Primary`A vagy `Secondary` a megadása `key-type` .

A paranccsal kapcsolatos további információkért futtassa a `azure rediscache renew-key -h` parancsot.

```azurecli
C:\>azure rediscache renew-key -h
help:    Renew the authentication key for an existing Azure Cache for Redis
help:
help:    Usage: rediscache renew-key [--name <name> --resource-group <resource-group> ]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group under which cache exists
help:      -t, --key-type <key-type>              type of key to renew. Valid values are: 'Primary', 'Secondary'.
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="list-primary-and-secondary-keys-of-an-existing-azure-cache-for-redis"></a>Meglévő Azure cache elsődleges és másodlagos kulcsainak listázása a Redis
A Redis meglévő Azure cache elsődleges és másodlagos kulcsainak listázásához használja a következő parancsot:

```azurecli
    azure rediscache list-keys [--name <name> --resource-group <resource-group>]
```

A paranccsal kapcsolatos további információkért futtassa a `azure rediscache list-keys -h` parancsot.

```azurecli
C:\>azure rediscache list-keys -h
help:    Lists Primary and Secondary key of an existing Azure Cache for Redis
help:
help:    Usage: rediscache list-keys [--name <name> --resource-group <resource-group>]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group under which Cache exists
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```