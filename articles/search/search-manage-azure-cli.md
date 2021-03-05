---
title: Azure CLI-parancsfájlok az az Search modul használatával
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search szolgáltatás létrehozása és konfigurálása az Azure CLI-vel. A szolgáltatás fel-vagy leskálázása, a rendszergazdák és a lekérdezési API-kulcsok kezelése, valamint a rendszerinformációk lekérdezése.
manager: luisca
author: DerekLegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 02/17/2021
ms.openlocfilehash: ee6b0e1b745e86c72843af88c0f6d17f91512e15
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176756"
---
# <a name="manage-your-azure-cognitive-search-service-with-the-azure-cli"></a>Azure Cognitive Search szolgáltatás kezelése az Azure CLI-vel
> [!div class="op_single_selector"]
> * [Portál](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [Azure CLI](search-manage-azure-cli.md)
> * [REST API](/rest/api/searchmanagement/)
> * [.NET SDK](/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)

Azure CLI-parancsokat és-parancsfájlokat Windows, macOS, Linux vagy [Azure Cloud Shell](../cloud-shell/overview.md) rendszeren is futtathat az azure-Cognitive Search létrehozásához és konfigurálásához. Az az [**Search**](/cli/azure/search) modul kiterjeszti az [Azure CLI](/cli/) -t teljes paritáson a [keresési felügyeleti REST API](/rest/api/searchmanagement) -kra, és képes a következő feladatok végrehajtására:

> [!div class="checklist"]
> * [Előfizetés keresési szolgáltatásainak listázása](#list-search-services)
> * [Szolgáltatás adatainak visszaküldése](#get-search-service-information)
> * [Szolgáltatás létrehozása vagy törlése](#create-or-delete-a-service)
> * [Szolgáltatás létrehozása privát végponttal](#create-a-service-with-a-private-endpoint)
> * [Felügyeleti API-kulcsok újragenerálása](#regenerate-admin-keys)
> * [Lekérdezési API-kulcsok létrehozása vagy törlése](#create-or-delete-query-keys)
> * [Vertikális fel-vagy leskálázás replikákkal és partíciókkal](#scale-replicas-and-partitions)
> * [Megosztott magánhálózati kapcsolati erőforrás létrehozása](#create-a-shared-private-link-resource)

Alkalmanként a fenti listán *nem* szereplő feladatokkal kapcsolatos kérdéseket is fel kell kérni. Jelenleg nem használható az az **Search** modul vagy a felügyeleti REST API a kiszolgálónév, régió vagy rétegek módosításához. A dedikált erőforrások a szolgáltatás létrehozásakor vannak lefoglalva. Ennek megfelelően a mögöttes hardver (hely vagy csomópont típusa) módosítása új szolgáltatást igényel. Hasonlóképpen nincsenek olyan eszközök vagy API-k, amelyek a tartalmak, például indexek továbbítására szolgálnak az egyik szolgáltatásból a másikba.

A szolgáltatáson belül a tartalom létrehozása és kezelése [Search Service REST API](/rest/api/searchservice/) vagy [.net SDK](/dotnet/api/overview/azure/search.documents-readme)-n keresztül történik. Noha nincsenek a tartalomhoz dedikált PowerShell-parancsok, olyan parancsfájlokat írhat, amelyek REST vagy .NET API-kat hívhatnak indexek létrehozásához és betöltéséhez.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>Előfizetéshez tartozó szolgáltatások listázása

Az alábbi parancsok az [**erőforrásból**](/cli/azure/resource)származnak, és az előfizetésében már üzembe helyezendő meglévő erőforrásokkal és szolgáltatásokkal kapcsolatos információkat adnak vissza. Ha nem tudja, hogy hány keresési szolgáltatás van már létrehozva, ezek a parancsok visszaküldik ezt az információt, és megmenti a portálon.

Az első parancs visszaadja az összes keresési szolgáltatást.

```azurecli-interactive
az resource list --resource-type Microsoft.Search/searchServices --output table
```

A szolgáltatások listájából egy adott erőforrásra vonatkozó adatokat ad vissza.

```azurecli-interactive
az resource list --name <service-name>
```

## <a name="list-all-az-search-commands"></a>Az összes keresési parancs listázása

Az [**az az keresés**](/cli/azure/search) a CLI-n belül elérhető alcsoportokra és parancsokra vonatkozó információkat a következő témakörben tekintheti meg:. Azt is megteheti, hogy áttekinti a [dokumentációt](/cli/azure/search).

A-ben elérhető alcsoportok megtekintéséhez `az search` futtassa a következő parancsot.

```azurecli-interactive
az search --help
```

A válasznak a következő kimenethez hasonlóan kell kinéznie.

```
Group
    az search : Manage Azure Search services, admin keys and query keys.
        WARNING: This command group is in preview and under development. Reference and support
        levels: https://aka.ms/CLI_refstatus
Subgroups:
    admin-key                    : Manage Azure Search admin keys.
    private-endpoint-connection  : Manage Azure Search private endpoint connections.
    private-link-resource        : Manage Azure Search private link resources.
    query-key                    : Manage Azure Search query keys.
    service                      : Manage Azure Search services.
    shared-private-link-resource : Manage Azure Search shared private link resources.

For more specific examples, use: az find "az search"
```

Minden alcsoporton belül több parancs is elérhető. Az `service` alcsoporthoz elérhető parancsokat a következő sor futtatásával tekintheti meg.

```azurecli-interactive
az search service --help
```

Megtekintheti az adott parancshoz elérhető argumentumokat is.

```azurecli-interactive
az search service create --help
```

## <a name="get-search-service-information"></a>Keresési szolgáltatás adatainak beolvasása

Ha ismeri a keresési szolgáltatást tartalmazó erőforráscsoportot, futtassa az [**az Search Service show**](/cli/azure/search/service#az_search_service_show) parancsot a szolgáltatás definíciójának visszaküldéséhez, beleértve a nevet, a régiót, a szintet, valamint a replika és a partíciók számát.

```azurecli-interactive
az search service show --name <service-name> --resource-group <resource-group-name>
```

## <a name="create-or-delete-a-service"></a>Szolgáltatás létrehozása vagy törlése

[Új keresési szolgáltatás létrehozásához](search-create-service-portal.md)használja az az [**Search Service Create**](/cli/azure/search/service#az_search_service_show) parancsot.

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1
``` 

Az eredményeknek a következő kimenethez hasonlóan kell kinéznie:

```
{
  "hostingMode": "default",
  "id": "/subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp",
  "identity": null,
  "location": "West US",
  "name": "my-demo-searchapp",
  "networkRuleSet": {
    "bypass": "None",
    "ipRules": []
  },
  "partitionCount": 1,
  "privateEndpointConnections": [],
  "provisioningState": "succeeded",
  "publicNetworkAccess": "Enabled",
  "replicaCount": 1,
  "resourceGroup": "demo-westus",
  "sharedPrivateLinkResources": [],
  "sku": {
    "name": "standard"
  },
  "status": "running",
  "statusDetails": "",
  "tags": null,
  "type": "Microsoft.Search/searchServices"
}
```

### <a name="create-a-service-with-ip-rules"></a>Szolgáltatás létrehozása IP-szabályokkal

A biztonsági követelményektől függően érdemes lehet olyan keresési szolgáltatást létrehozni, amely konfigurálva van egy [IP-tűzfallal](service-configure-firewall.md). Ehhez továbbítsa a nyilvános IP-címeket (v4) vagy a CIDR az `ip-rules` alábbihoz hasonló argumentumként. A szabályokat vesszővel ( `,` ) vagy pontosvesszővel () kell elválasztani `;` .

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --ip-rules "55.5.63.73;52.228.215.197;101.37.221.205"
```

### <a name="create-a-service-with-a-system-assigned-managed-identity"></a>Szolgáltatás létrehozása rendszerhez rendelt felügyelt identitással

Bizonyos esetekben, például ha a [felügyelt identitás használatával csatlakozik egy adatforráshoz](search-howto-managed-identities-storage.md), be kell kapcsolni a [rendszerhez rendelt felügyelt identitást](../active-directory/managed-identities-azure-resources/overview.md). Ezt a parancs hozzáadásával teheti `--identity-type SystemAssigned` meg.

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --identity-type SystemAssigned
```

## <a name="create-a-service-with-a-private-endpoint"></a>Szolgáltatás létrehozása privát végponttal

Az Azure Cognitive Search [magánhálózati végpontok](../private-link/private-endpoint-overview.md) lehetővé teszik, hogy egy virtuális hálózaton lévő ügyfél biztonságosan hozzáférhessen egy keresési indexben lévő adathoz egy [privát hivatkozáson](../private-link/private-link-overview.md)keresztül. A privát végpont egy IP-címet használ a keresési szolgáltatáshoz tartozó [virtuális hálózati címtartomány](../virtual-network/private-ip-addresses.md) alapján. Az ügyfél és a keresési szolgáltatás közötti hálózati forgalom áthalad a virtuális hálózaton és a Microsoft gerinc hálózatán található privát kapcsolaton, ami kiküszöböli a nyilvános internetről való kitettséget. További részletekért tekintse meg az Azure-beli [privát végpontok létrehozásához szükséges](service-create-private-endpoint.md) dokumentációt Cognitive Search

Az alábbi példa bemutatja, hogyan hozhat létre egy keresési szolgáltatást privát végponttal.

Először telepítsen egy keresési szolgáltatást, amely a következőre van `PublicNetworkAccess` beállítva: `Disabled` .

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --public-access Disabled
```

Ezután hozzon létre egy virtuális hálózatot és a privát végpontot.

```azurecli-interactive
# Create the virtual network
az network vnet create \
    --resource-group <resource-group-name> \
    --location "West US" \
    --name <virtual-network-name> \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name <subnet-name> \
    --subnet-prefixes 10.1.0.0/24

# Update the subnet to disable private endpoint network policies
az network vnet subnet update \
    --name <subnet-name> \
    --resource-group <resource-group-name> \
    --vnet-name <virtual-network-name> \
    --disable-private-endpoint-network-policies true

# Get the id of the search service
id=$(az search service show \
    --resource-group <resource-group-name> \
    --name <service-name> \
    --query [id] \
    --output tsv)

# Create the private endpoint
az network private-endpoint create \
    --name <private-endpoint-name> \
    --resource-group <resource-group-name> \
    --vnet-name <virtual-network-name> \
    --subnet <subnet-name> \
    --private-connection-resource-id $id \
    --group-id searchService \
    --connection-name <private-link-connection-name>  
```

Végül hozzon létre egy privát DNS-zónát. 

```azurecli-interactive
## Create private DNS zone
az network private-dns zone create \
    --resource-group <resource-group-name> \
    --name "privatelink.search.windows.net"

## Create DNS network link
az network private-dns link vnet create \
    --resource-group <resource-group-name> \
    --zone-name "privatelink.search.windows.net" \
    --name "myLink" \
    --virtual-network <virtual-network-name> \
    --registration-enabled false

## Create DNS zone group
az network private-endpoint dns-zone-group create \
   --resource-group <resource-group-name>\
   --endpoint-name <private-endpoint-name> \
   --name "myZoneGroup" \
   --private-dns-zone "privatelink.search.windows.net" \
   --zone-name "searchServiceZone"
```

A privát végpontok PowerShellben való létrehozásával kapcsolatos további információkért tekintse meg ezt a [privát hivatkozás](https://docs.microsoft.com/azure/private-link/create-private-endpoint-cli) rövid útmutatóját.

### <a name="manage-private-endpoint-connections"></a>Magánhálózati végpontok kapcsolatainak kezelése

Privát végponti kapcsolatok létrehozásán kívül `show` `update` a, a és a kapcsolatok is megadhatók `delete` .

Privát végponti kapcsolat lekéréséhez és az állapotának megtekintéséhez használja [**az az Search Private-Endpoint-kapcsolat show**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_show)lehetőséget.

```azurecli-interactive
az search private-endpoint-connection show \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

A kapcsolódási funkció frissítéséhez használja az [**az Search Private-Endpoint-Update**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_update). Az alábbi példa egy privát végponti kapcsolódást állít be az elutasított értékre:

```azurecli-interactive
az search private-endpoint-connection show \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
    --status Rejected \
    --description "Rejected" \
    --actions-required "Please fix XYZ"
```

A privát végponti kapcsolatok törléséhez használja az [**az Search Private-Endpoint-kapcsolatok delete kifejezést**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_delete).

```azurecli-interactive
az search private-endpoint-connection delete \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

## <a name="regenerate-admin-keys"></a>Rendszergazdai kulcsok újragenerálása

A felügyeleti API- [kulcsok](search-security-api-keys.md)átadásához használja [**az az Search admin-Key renew**](/cli/azure/search/admin-key#az_search_admin_key_renew)lehetőséget. A hitelesített hozzáféréshez a szolgáltatás két rendszergazdai kulcsot hoz létre. Minden kérelemhez kulcsokra van szükség. Mindkét rendszergazdai kulcs funkcionálisan egyenértékű, és teljes írási hozzáférést biztosít egy keresési szolgáltatáshoz bármilyen információ lekéréséhez, illetve bármely objektum létrehozásához és törléséhez. Két kulcs létezik, hogy a másikat cserélje le. 

Egyszerre csak egyszer lehet újradefiniálni, a `primary` vagy a `secondary` kulcsként megadva. A nem folytonos szolgáltatás esetében ne felejtse el frissíteni az összes állapotkódot, hogy másodlagos kulcsot használjon, miközben az elsődleges kulcsra mutat. Ne változtassa meg a kulcsokat, amíg a műveletek bekerülnek a repülésbe.

Ahogy várható, ha az ügyfél kódjának frissítése nélkül újragenerálja a kulcsokat, a régi kulcsot használó kérelmek sikertelenek lesznek. Az összes új kulcs újragenerálása nem zárja ki véglegesen a szolgáltatást, és a portálon keresztül továbbra is hozzáférhet a szolgáltatáshoz. Az elsődleges és a másodlagos kulcsok újragenerálása után frissítheti az ügyfélszoftvert az új kulcsok használatára, és ennek megfelelően folytathatja a műveletet.

Az API-kulcsok értékeit a szolgáltatás hozza létre. Nem adhat meg egyéni kulcsot az Azure Cognitive Search használatához. Hasonlóképpen, a rendszergazda API-kulcsok nem rendelkeznek felhasználó által definiált névvel. A kulcsra mutató hivatkozások rögzített karakterláncok, `primary` vagy vagy `secondary` . 

```azurecli-interactive
az search admin-key renew \
    --resource-group <resource-group-name> \
    --service-name <search-service-name> \
    --key-kind primary
```

Az eredményeknek az alábbi kimenethez hasonlóan kell kinéznie. Mindkét kulcsot a rendszer akkor is visszaadja, ha egyszerre csak egyszer módosítja.

```   
{
  "primaryKey": <alphanumeric-guid>,
  "secondaryKey": <alphanumeric-guid>  
}
```

## <a name="create-or-delete-query-keys"></a>Lekérdezési kulcsok létrehozása vagy törlése

Ha a lekérdezési [API-kulcsokat](search-security-api-keys.md) csak olvasási hozzáféréssel szeretné létrehozni az ügyfélalkalmazások Azure Cognitive Search indexéhez, használja az [**az Search Query-Key Create**](/cli/azure/search/query-key#az_search_query_key_create)elemet. A lekérdezési kulcsok egy adott indexre való hitelesítésre szolgálnak a keresési eredmények beolvasása céljából. A lekérdezési kulcsok nem biztosítanak csak olvasási hozzáférést a szolgáltatás más elemeihez, például az indexhez, az adatforráshoz vagy az indexelő.

Nem adhat meg kulcsot az Azure Cognitive Search használatához. Az API-kulcsokat a szolgáltatás hozza létre.

```azurecli-interactive
az search query-key create \
    --name myQueryKey \
    --resource-group <resource-group-name> \
    --service-name <search-service-name>
```

## <a name="scale-replicas-and-partitions"></a>Replikák és partíciók skálázása

A [replikák és partíciók növeléséhez vagy csökkentéséhez](search-capacity-planning.md) használja [**az az Search Service Update kifejezést**](/cli/azure/search/service#az_search_service_update). Egyre több replika vagy partíció járul hozzá a számlához, amely rögzített és változó díjakat is tartalmaz. Ha átmenetileg további feldolgozási teljesítményre van szüksége, növelheti a replikákat és a partíciókat a munkaterhelés kezeléséhez. Az áttekintő portál figyelés területén található csempék a lekérdezési késéssel, a másodpercenkénti lekérdezésekkel és a szabályozással kapcsolatban, és jelzi, hogy az aktuális kapacitás megfelelő-e.

Eltarthat egy darabig a reforrások hozzáadásához vagy eltávolításához is. A kapacitás módosítása a háttérben történik, ami lehetővé teszi a meglévő munkaterhelések folytatását. A beérkező kérésekhez további kapacitást kell használni, amint az készen áll, és nincs szükség további konfigurálásra. 

A kapacitás eltávolítása zavaró lehet. Az eldobott kérések elkerülése érdekében ajánlott az összes indexelő és indexelő feladat leállítása a kapacitás csökkentése előtt. Ha ez nem lehetséges, érdemes lehet fokozatosan csökkenteni a kapacitást, egy replikát és partíciót egyszerre, amíg el nem éri az új célként megadott szintet.

A parancs elküldése után nem lehet lemondani félúton. A számok módosítása előtt várnia kell, amíg a parancs be nem fejeződik.

```azurecli-interactive
az search service update \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --partition-count 6 \
    --replica-count 6
```

A replika és a partíciók számának frissítése mellett a, a és a is frissíthető `ip-rules` `public-access` `identity-type` .

## <a name="create-a-shared-private-link-resource"></a>Megosztott magánhálózati kapcsolati erőforrás létrehozása

Az Azure Cognitive Search API-kkal létrehozott biztonságos erőforrások privát végpontait *megosztott magánhálózati kapcsolati erőforrásoknak* nevezzük. Ennek az az oka, hogy az [Azure Private link szolgáltatással](https://azure.microsoft.com/services/private-link/)integrált erőforráshoz (például egy Storage-fiókhoz) való hozzáférés "megosztás".

Ha indexelő használatával indexeli az adatait az Azure Cognitive Searchban, és az adatforrás egy magánhálózaton található, létrehozhat egy kimenő [magánhálózati végponti kapcsolattal](../private-link/private-endpoint-overview.md) az adatelérést.

Azon Azure-erőforrások teljes listája, amelyekhez kimenő privát végpontokat hozhat létre az Azure Cognitive Search a kapcsolódó **Csoportazonosító** értékeivel együtt [itt](search-indexer-howto-access-private.md#shared-private-link-resources-management-apis) található.

A megosztott magánhálózati kapcsolati erőforrás létrehozásához használja az [**az Search Shared-Private-link-Resource Create**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_list)lehetőséget. Ne feledje, hogy a parancs futtatása előtt néhány konfigurációra szükség lehet az adatforráshoz.

```azurecli-interactive
az search shared-private-link-resource create \
    --name <spl-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> \
    --group-id blob \
    --resource-id "/subscriptions/<alphanumeric-subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/myBlobStorage"  \
    --request-message "Please approve" 
```


A megosztott magánhálózati kapcsolat erőforrásainak lekéréséhez és az állapotuk megtekintéséhez használja [**az az Search Shared-Private-link-Resource List**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_list)lehetőséget.

```azurecli-interactive
az search shared-private-link-resource list \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

Ahhoz, hogy használni lehessen, jóvá kell hagynia a következő paranccsal való kapcsolódást. A privát végpont-kapcsolatok AZONOSÍTÓját le kell kérni a gyermek erőforrásból. Ebben az esetben az az Storage-ból kapja meg a kapcsolatok AZONOSÍTÓját.

```azurecli-interactive
id = (az storage account show -n myBlobStorage --query "privateEndpointConnections[0].id")

az network private-endpoint-connection approve --id $id
```

A megosztott magánhálózati kapcsolati erőforrás törléséhez használja az [**az Search Shared-Private-link-Resource delete kifejezést**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_delete).

```azurecli-interactive
az search shared-private-link-resource delete \
    --name <spl-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

A megosztott magánhálózati kapcsolati erőforrások beállításával kapcsolatos részletes információkért tekintse meg az [Indexelő kapcsolatainak privát végponton keresztül](search-indexer-howto-access-private.md)történő létrehozását ismertető dokumentációt.

## <a name="next-steps"></a>Következő lépések

Hozzon létre [egy indexet, és](search-what-is-an-index.md) [Kérdezzen le egy indexet](search-query-overview.md) a portál, a REST API-k vagy a .net SDK használatával.

* [Hozzon létre egy Azure Cognitive Search indexet a Azure Portal](search-get-started-portal.md)
* [Indexelő beállítása az adatok más szolgáltatásokból való betöltéséhez](search-indexer-overview.md)
* [Azure Cognitive Search-index lekérdezése a keresési ablak használatával a Azure Portal](search-explorer.md)
* [Az Azure Cognitive Search használata a .NET-ben](search-howto-dotnet-sdk.md)
