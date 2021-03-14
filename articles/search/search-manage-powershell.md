---
title: PowerShell-parancsfájlok az az. Search modul használatával
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search szolgáltatás létrehozása és konfigurálása a PowerShell-lel. A szolgáltatás fel-vagy leskálázása, a rendszergazdák és a lekérdezési API-kulcsok kezelése, valamint a rendszerinformációk lekérdezése.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 02/09/2021
ms.openlocfilehash: efb5d498c627a6731d2a90623c81eefabd0042a0
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462779"
---
# <a name="manage-your-azure-cognitive-search-service-with-powershell"></a>Azure Cognitive Search szolgáltatás kezelése a PowerShell-lel
> [!div class="op_single_selector"]
> * [Portál](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [Azure CLI](search-manage-azure-cli.md)
> * [REST API](/rest/api/searchmanagement/)
> * [.NET SDK](/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)

Az Azure Cognitive Search létrehozásához és konfigurálásához Windows, Linux vagy [Azure Cloud Shell](../cloud-shell/overview.md) PowerShell-parancsmagokat és-parancsfájlokat is futtathat. Az az **. Search** modul a [keresési felügyeleti REST API](/rest/api/searchmanagement) -k teljes paritásával bővíti a [Azure PowerShellt](/powershell/) , és képes a következő feladatok végrehajtására:

> [!div class="checklist"]
> * [Előfizetés keresési szolgáltatásainak listázása](#list-search-services)
> * [Szolgáltatás adatainak visszaküldése](#get-search-service-information)
> * [Szolgáltatás létrehozása vagy törlése](#create-or-delete-a-service)
> * [Szolgáltatás létrehozása privát végponttal](#create-a-service-with-a-private-endpoint)
> * [Felügyeleti API-kulcsok újragenerálása](#regenerate-admin-keys)
> * [Lekérdezési API-kulcsok létrehozása vagy törlése](#create-or-delete-query-keys)
> * [Vertikális fel-vagy leskálázás replikákkal és partíciókkal](#scale-replicas-and-partitions)
> * [Megosztott magánhálózati kapcsolati erőforrás létrehozása](#create-a-shared-private-link-resource)

Alkalmanként a fenti listán *nem* szereplő feladatokkal kapcsolatos kérdéseket is fel kell kérni. Jelenleg nem használható az az **. Search** modul vagy a felügyeleti REST API a kiszolgálónév, régió vagy rétegek módosításához. A dedikált erőforrások a szolgáltatás létrehozásakor vannak lefoglalva. Ennek megfelelően a mögöttes hardver (hely vagy csomópont típusa) módosítása új szolgáltatást igényel. Hasonlóképpen nincsenek olyan eszközök vagy API-k, amelyek a tartalmak, például indexek továbbítására szolgálnak az egyik szolgáltatásból a másikba.

A szolgáltatáson belül a tartalom létrehozása és kezelése [Search Service REST API](/rest/api/searchservice/) vagy [.net SDK](/dotnet/api/overview/azure/search.documents-readme)-n keresztül történik. Noha nincsenek a tartalomhoz dedikált PowerShell-parancsok, olyan PowerShell-parancsfájlt írhat, amely REST vagy .NET API-kat hív meg indexek létrehozásához és betöltéséhez.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>Verziók és betöltési modulok keresése

A cikkben szereplő példák interaktívak, és emelt szintű engedélyeket igényelnek. Azure PowerShell **(az az modul)** telepíteni kell. További információ: [Install Azure PowerShell](/powershell/azure/).

### <a name="powershell-version-check-51-or-later"></a>PowerShell-verzió-ellenőrzési (5,1 vagy újabb)

A helyi PowerShellnek 5,1 vagy újabb verziójúnak kell lennie bármely támogatott operációs rendszeren.

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>Betöltés Azure PowerShell

Ha nem tudja biztosan, **hogy az az telepítve** van-e, futtassa a következő parancsot ellenőrzési lépésként. 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

Néhány rendszer nem tölti ki automatikusan a modulokat. Ha hibaüzenet jelenik meg az előző parancsban, próbálja meg betölteni a modult, és ha ez nem sikerül, lépjen vissza a telepítési utasításokba, és ellenőrizze, hogy kihagyott-e egy lépést.

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>Kapcsolódás az Azure-hoz egy böngésző bejelentkezési jogkivonattal

A portál bejelentkezési hitelesítő adatait használhatja a PowerShell-előfizetéshez való kapcsolódáshoz. Azt is megteheti, hogy [nem interaktív módon tud hitelesítést végezni egy egyszerű szolgáltatással](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

```azurepowershell-interactive
Connect-AzAccount
```

Ha több Azure-előfizetéssel rendelkezik, állítsa be az Azure-előfizetését. A jelenlegi előfizetések listájának megtekintéséhez futtassa ezt a parancsot.

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Az előfizetés megadásához futtassa a következő parancsot. A következő példában az előfizetés neve: `ContosoSubscription` .

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>Előfizetéshez tartozó szolgáltatások listázása

A következő parancsok az [**az. Resources**](/powershell/module/az.resources), az előfizetésben már üzembe helyezendő meglévő erőforrásokkal és szolgáltatásokkal kapcsolatos információkat adnak vissza. Ha nem tudja, hogy hány keresési szolgáltatás van már létrehozva, ezek a parancsok visszaküldik ezt az információt, és megmenti a portálon.

Az első parancs visszaadja az összes keresési szolgáltatást.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

A szolgáltatások listájából egy adott erőforrásra vonatkozó adatokat ad vissza.

```azurepowershell-interactive
Get-AzResource -ResourceName <service-name>
```

Az eredményeknek az alábbi kimenethez hasonlóan kell kinéznie.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : westus
ResourceId        : /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="import-azsearch"></a>Importálás az. Search

Az [**az. Search**](/powershell/module/az.search) utasításai nem érhetők el, amíg be nem tölti a modult.

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>Az összes. keresési parancs listázása

Ellenőrzési lépésként a modulban megadott parancsok listáját kell visszaadnia.

```azurepowershell-interactive
Get-Command -Module Az.Search
```

Az eredményeknek az alábbi kimenethez hasonlóan kell kinéznie.

```
CommandType     Name                                               Version    Source                                                                
-----------     ----                                               -------    ------                                                                
Cmdlet          Get-AzSearchAdminKeyPair                           0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchPrivateEndpointConnection              0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchPrivateLinkResource                    0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchQueryKey                               0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchService                                0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchSharedPrivateLinkResource              0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchAdminKey                               0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchQueryKey                               0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchService                                0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchSharedPrivateLinkResource              0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchPrivateEndpointConnection           0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchQueryKey                            0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchService                             0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchSharedPrivateLinkResource           0.8.0      Az.Search                                                             
Cmdlet          Set-AzSearchPrivateEndpointConnection              0.8.0      Az.Search                                                             
Cmdlet          Set-AzSearchService                                0.8.0      Az.Search                                                             
Cmdlet          Set-AzSearchSharedPrivateLinkResource              0.8.0      Az.Search   
```

Ha a csomag egy régebbi verziója van telepítve, frissítse a modult a legújabb funkciók beszerzéséhez.

```azurepowershell-interactive
Update-Module -Name Az.Search
```

## <a name="get-search-service-information"></a>Keresési szolgáltatás adatainak beolvasása

Az **az. Search** importálása után, és ismeri a keresési szolgáltatást tartalmazó erőforráscsoportot, a [Get-AzSearchService](/powershell/module/az.search/get-azsearchservice) futtatásával adja vissza a szolgáltatás definícióját, beleértve a nevet, a régiót, a szintet, valamint a replika és a partíciók számát.

```azurepowershell-interactive
Get-AzSearchService -ResourceGroupName <resource-group-name>
```

Az eredményeknek az alábbi kimenethez hasonlóan kell kinéznie.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : West US
Sku               : Standard
ReplicaCount      : 1
PartitionCount    : 1
HostingMode       : Default
ResourceId        : /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="create-or-delete-a-service"></a>Szolgáltatás létrehozása vagy törlése

A [**New-AzSearchService**](/powershell/module/az.search/new-azsearchservice) [új keresési szolgáltatás létrehozásához](search-create-service-portal.md)használatos.

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -Sku "Standard" -Location "West US" -PartitionCount 3 -ReplicaCount 3 -HostingMode Default
``` 
Az eredményeknek az alábbi kimenethez hasonlóan kell kinéznie.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Id                : /subscriptions/<alphanumeric-subscription-ID>/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 3
PartitionCount    : 3
HostingMode       : Default
Tags
```     

### <a name="create-a-service-with-ip-rules"></a>Szolgáltatás létrehozása IP-szabályokkal

A biztonsági követelményektől függően érdemes lehet olyan keresési szolgáltatást létrehozni, amely konfigurálva van egy [IP-tűzfallal](service-configure-firewall.md). Ehhez először meg kell határoznia az IP-szabályokat, majd át kell adni őket a `IPRuleList` paraméternek az alább látható módon.

```azurepowershell-interactive
$ipRules = @([pscustomobject]@{Value="55.5.63.73"},
        [pscustomobject]@{Value="52.228.215.197"},
        [pscustomobject]@{Value="101.37.221.205"})

 New-AzSearchService -ResourceGroupName <resource-group-name> `
                      -Name <search-service-name> `
                      -Sku Standard `
                      -Location "West US" `
                      -PartitionCount 3 -ReplicaCount 3 `
                      -HostingMode Default `
                      -IPRuleList $ipRules
```

### <a name="create-a-service-with-a-system-assigned-managed-identity"></a>Szolgáltatás létrehozása rendszerhez rendelt felügyelt identitással

Bizonyos esetekben, például ha a [felügyelt identitás használatával csatlakozik egy adatforráshoz](search-howto-managed-identities-storage.md), be kell kapcsolni a [rendszerhez rendelt felügyelt identitást](../active-directory/managed-identities-azure-resources/overview.md). Ezt a parancs hozzáadásával teheti `-IdentityType SystemAssigned` meg.

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName <resource-group-name> `
                      -Name <search-service-name> `
                      -Sku Standard `
                      -Location "West US" `
                      -PartitionCount 3 -ReplicaCount 3 `
                      -HostingMode Default `
                      -IdentityType SystemAssigned
```

## <a name="create-a-service-with-a-private-endpoint"></a>Szolgáltatás létrehozása privát végponttal

Az Azure Cognitive Search [magánhálózati végpontok](../private-link/private-endpoint-overview.md) lehetővé teszik, hogy egy virtuális hálózaton lévő ügyfél biztonságosan hozzáférhessen egy keresési indexben lévő adathoz egy [privát hivatkozáson](../private-link/private-link-overview.md)keresztül. A privát végpont egy IP-címet használ a keresési szolgáltatáshoz tartozó [virtuális hálózati címtartomány](../virtual-network/private-ip-addresses.md) alapján. Az ügyfél és a keresési szolgáltatás közötti hálózati forgalom áthalad a virtuális hálózaton és a Microsoft gerinc hálózatán található privát kapcsolaton, ami kiküszöböli a nyilvános internetről való kitettséget. További részletekért tekintse meg az Azure-beli [privát végpontok létrehozásához szükséges](service-create-private-endpoint.md) dokumentációt Cognitive Search

Az alábbi példa bemutatja, hogyan hozhat létre egy keresési szolgáltatást privát végponttal.

Először telepítsen egy keresési szolgáltatást, amely a következőre van `PublicNetworkAccess` beállítva: `Disabled` .

```azurepowershell-interactive
$searchService = New-AzSearchService `
    -ResourceGroupName <resource-group-name> `
    -Name <search-service-name> `
    -Sku Standard `
    -Location "West US" `
    -PartitionCount 1 -ReplicaCount 1 `
    -HostingMode Default `
    -PublicNetworkAccess Disabled
```

Ezután hozzon létre egy virtuális hálózatot, magánhálózati kapcsolatokat és a magánhálózati végpontot.

```azurepowershell-interactive
# Create the subnet
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
    -Name <subnet-name> `
    -AddressPrefix 10.1.0.0/24 `
    -PrivateEndpointNetworkPolicies Disabled 

# Create the virtual network
$virtualNetwork = New-AzVirtualNetwork `
    -ResourceGroupName <resource-group-name> `
    -Location "West US" `
    -Name <virtual-network-name> `
    -AddressPrefix 10.1.0.0/16 `
    -Subnet $subnetConfig

# Create the private network connection
$privateLinkConnection = New-AzPrivateLinkServiceConnection `
    -Name <private-link-name> `
    -PrivateLinkServiceId $searchService.Id `
    -GroupId searchService

# Create the private endpoint
$privateEndpoint = New-AzPrivateEndpoint `
    -Name <private-endpoint-name> `
    -ResourceGroupName <resource-group-name> `
    -Location "West US" `
    -Subnet $virtualNetwork.subnets[0] `
    -PrivateLinkServiceConnection $privateLinkConnection
```

Végül hozzon létre egy privát DNS-zónát. 

```azurepowershell-interactive
## Create private dns zone
$zone = New-AzPrivateDnsZone `
    -ResourceGroupName <resource-group-name> `
    -Name "privatelink.search.windows.net"

## Create dns network link
$link = New-AzPrivateDnsVirtualNetworkLink `
    -ResourceGroupName <resource-group-name> `
    -ZoneName "privatelink.search.windows.net" `
    -Name "myLink" `
    -VirtualNetworkId $virtualNetwork.Id

## Create DNS configuration 
$config = New-AzPrivateDnsZoneConfig `
    -Name "privatelink.search.windows.net" `
    -PrivateDnsZoneId $zone.ResourceId

## Create DNS zone group
New-AzPrivateDnsZoneGroup `
    -ResourceGroupName <resource-group-name> `
    -PrivateEndpointName <private-endpoint-name> `
    -Name 'myZoneGroup' `
    -PrivateDnsZoneConfig $config
```

A privát végpontok PowerShellben való létrehozásával kapcsolatos további információkért tekintse meg ezt a [privát hivatkozás](../private-link/create-private-endpoint-powershell.md) rövid útmutatóját.

### <a name="manage-private-endpoint-connections"></a>Magánhálózati végpontok kapcsolatainak kezelése

Privát végponti kapcsolatok létrehozásán kívül `Get` `Set` a, a és a kapcsolatok is megadhatók `Remove` .

A [Get-AzSearchPrivateEndpointConnection](/powershell/module/az.search/Get-AzSearchPrivateEndpointConnection) a magánhálózati végpontok kapcsolatának lekérésére és állapotának megjelenítésére szolgál.

```azurepowershell-interactive
Get-AzSearchPrivateEndpointConnection -ResourceGroupName <resource-group-name> -ServiceName <search-service-name>
```

A [set-AzSearchPrivateEndpointConnection](/powershell/module/az.search/Set-AzSearchPrivateEndpointConnection) a kapcsolódás frissítésére szolgál. Az alábbi példa egy privát végponti kapcsolódást állít be az elutasított értékre:

```azurepowershell-interactive
Set-AzSearchPrivateEndpointConnection -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <pe-connection-name> -Status Rejected  -Description "Rejected"
```

A [Remove-AzSearchPrivateEndpointConnection](/powershell/module/az.search/Remove-AzSearchPrivateEndpointConnection) a magánhálózati végponti kapcsolatok törlésére szolgál.

```azurepowershell-interactive
 Remove-AzSearchPrivateEndpointConnection -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <pe-connection-name>
```

## <a name="regenerate-admin-keys"></a>Rendszergazdai kulcsok újragenerálása

A [**New-AzSearchAdminKey**](/powershell/module/az.search/new-azsearchadminkey) a felügyeleti [API-kulcsok](search-security-api-keys.md)átadására szolgál. A hitelesített hozzáféréshez a szolgáltatás két rendszergazdai kulcsot hoz létre. Minden kérelemhez kulcsokra van szükség. Mindkét rendszergazdai kulcs funkcionálisan egyenértékű, és teljes írási hozzáférést biztosít egy keresési szolgáltatáshoz bármilyen információ lekéréséhez, illetve bármely objektum létrehozásához és törléséhez. Két kulcs létezik, hogy a másikat cserélje le. 

Egyszerre csak egyszer lehet újradefiniálni, a `primary` vagy a `secondary` kulcsként megadva. A nem folytonos szolgáltatás esetében ne felejtse el frissíteni az összes állapotkódot, hogy másodlagos kulcsot használjon, miközben az elsődleges kulcsra mutat. Ne változtassa meg a kulcsokat, amíg a műveletek bekerülnek a repülésbe.

Ahogy várható, ha az ügyfél kódjának frissítése nélkül újragenerálja a kulcsokat, a régi kulcsot használó kérelmek sikertelenek lesznek. Az összes új kulcs újragenerálása nem zárja ki véglegesen a szolgáltatást, és a portálon keresztül továbbra is hozzáférhet a szolgáltatáshoz. Az elsődleges és a másodlagos kulcsok újragenerálása után frissítheti az ügyfélszoftvert az új kulcsok használatára, és ennek megfelelően folytathatja a műveletet.

Az API-kulcsok értékeit a szolgáltatás hozza létre. Nem adhat meg egyéni kulcsot az Azure Cognitive Search használatához. Hasonlóképpen, a rendszergazda API-kulcsok nem rendelkeznek felhasználó által definiált névvel. A kulcsra mutató hivatkozások rögzített karakterláncok, `primary` vagy vagy `secondary` . 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

Az eredményeknek az alábbi kimenethez hasonlóan kell kinéznie. Mindkét kulcsot a rendszer akkor is visszaadja, ha egyszerre csak egyszer módosítja.

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>Lekérdezési kulcsok létrehozása vagy törlése

A [**New-AzSearchQueryKey**](/powershell/module/az.search/new-azsearchquerykey) használatával lekérdezési [API-kulcsokat](search-security-api-keys.md) hozhat létre a csak olvasási hozzáféréshez az ügyfélalkalmazások és egy Azure Cognitive Search index között. A lekérdezési kulcsok egy adott indexre való hitelesítésre szolgálnak a keresési eredmények beolvasása céljából. A lekérdezési kulcsok nem biztosítanak csak olvasási hozzáférést a szolgáltatás más elemeihez, például az indexhez, az adatforráshoz vagy az indexelő.

Nem adhat meg kulcsot az Azure Cognitive Search használatához. Az API-kulcsokat a szolgáltatás hozza létre.

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>Replikák és partíciók skálázása

A [**set-AzSearchService**](/powershell/module/az.search/set-azsearchservice) használatával [megnövelhető vagy csökkenthető a replikák és partíciók](search-capacity-planning.md) a szolgáltatásban lévő számlázható erőforrások újraigazítása érdekében. Egyre több replika vagy partíció járul hozzá a számlához, amely rögzített és változó díjakat is tartalmaz. Ha átmenetileg további feldolgozási teljesítményre van szüksége, növelheti a replikákat és a partíciókat a munkaterhelés kezeléséhez. Az áttekintő portál figyelés területén található csempék a lekérdezési késéssel, a másodpercenkénti lekérdezésekkel és a szabályozással kapcsolatban, és jelzi, hogy az aktuális kapacitás megfelelő-e.

Eltarthat egy darabig a reforrások hozzáadásához vagy eltávolításához is. A kapacitás módosítása a háttérben történik, ami lehetővé teszi a meglévő munkaterhelések folytatását. A beérkező kérésekhez további kapacitást kell használni, amint az készen áll, és nincs szükség további konfigurálásra. 

A kapacitás eltávolítása zavaró lehet. Az eldobott kérések elkerülése érdekében ajánlott az összes indexelő és indexelő feladat leállítása a kapacitás csökkentése előtt. Ha ez nem lehetséges, érdemes lehet fokozatosan csökkenteni a kapacitást, egy replikát és partíciót egyszerre, amíg el nem éri az új célként megadott szintet.

A parancs elküldése után nem lehet lemondani félúton. A számok módosítása előtt várnia kell, amíg a parancs be nem fejeződik.

```azurepowershell-interactive
Set-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -PartitionCount 6 -ReplicaCount 6
```

Az eredményeknek az alábbi kimenethez hasonlóan kell kinéznie.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 6
PartitionCount    : 6
HostingMode       : Default
Id                : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="create-a-shared-private-link-resource"></a>Megosztott magánhálózati kapcsolati erőforrás létrehozása

Az Azure Cognitive Search API-kkal létrehozott biztonságos erőforrások privát végpontait *megosztott magánhálózati kapcsolati erőforrásoknak* nevezzük. Ennek az az oka, hogy az [Azure Private link szolgáltatással](https://azure.microsoft.com/services/private-link/)integrált erőforráshoz (például egy Storage-fiókhoz) való hozzáférés "megosztás".

Ha indexelő használatával indexeli az adatait az Azure Cognitive Searchban, és az adatforrás egy magánhálózaton található, létrehozhat egy kimenő [magánhálózati végponti kapcsolattal](../private-link/private-endpoint-overview.md) az adatelérést.

Azon Azure-erőforrások teljes listája, amelyekhez kimenő privát végpontokat hozhat létre az Azure Cognitive Search a kapcsolódó **Csoportazonosító** értékeivel együtt [itt](search-indexer-howto-access-private.md#shared-private-link-resources-management-apis) található.

A [New-AzSearchSharedPrivateLinkResource](/powershell/module/az.search/New-AzSearchSharedPrivateLinkResource) a megosztott magánhálózati kapcsolati erőforrás létrehozásához használatos. Ne feledje, hogy a parancs futtatása előtt néhány konfigurációra szükség lehet az adatforráshoz.

```azurepowershell-interactive
New-AzSearchSharedPrivateLinkResource -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <spl-name> -PrivateLinkResourceId /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/myBlobStorage -GroupId <group-id> -RequestMessage "Please approve" 
```

A [Get-AzSearchSharedPrivateLinkResource](/powershell/module/az.search/Get-AzSearchSharedPrivateLinkResource) lehetővé teszi a megosztott magánhálózati kapcsolati erőforrások beolvasását és az állapotuk megtekintését.

```azurepowershell-interactive
Get-AzSearchSharedPrivateLinkResource -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <spl-name>
```

Ahhoz, hogy használni lehessen, jóvá kell hagynia a következő paranccsal való kapcsolódást.

```azurepowershell-interactive
Approve-AzPrivateEndpointConnection `
    -Name <spl-name> `
    -ServiceName <search-service-name> `
    -ResourceGroupName <resource-group-name> `
    -Description = "Approved"
```

A [Remove-AzSearchSharedPrivateLinkResource](/powershell/module/az.search/Remove-AzSearchSharedPrivateLinkResource) a megosztott magánhálózati kapcsolati erőforrás törlésére szolgál.

```azurepowershell-interactive
$job = Remove-AzSearchSharedPrivateLinkResource -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <spl-name> -Force -AsJob

$job | Get-Job
```

A megosztott magánhálózati kapcsolati erőforrások beállításával kapcsolatos részletes információkért tekintse meg az [Indexelő kapcsolatainak privát végponton keresztül](search-indexer-howto-access-private.md)történő létrehozását ismertető dokumentációt.

## <a name="next-steps"></a>Következő lépések

Hozzon létre [egy indexet, és](search-what-is-an-index.md) [Kérdezzen le egy indexet](search-query-overview.md) a portál, a REST API-k vagy a .net SDK használatával.

* [Hozzon létre egy Azure Cognitive Search indexet a Azure Portal](search-get-started-portal.md)
* [Indexelő beállítása az adatok más szolgáltatásokból való betöltéséhez](search-indexer-overview.md)
* [Azure Cognitive Search-index lekérdezése a keresési ablak használatával a Azure Portal](search-explorer.md)
* [Az Azure Cognitive Search használata a .NET-ben](search-howto-dotnet-sdk.md)
