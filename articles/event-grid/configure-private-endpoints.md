---
title: Privát végpontok konfigurálása Azure Event Grid témakörök vagy tartományok számára
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja a privát végpontokat Azure Event Grid témakörök vagy tartományok számára.
ms.topic: how-to
ms.date: 11/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 85546e99a8c431dc75b1af3d5044e06a18cf226d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770506"
---
# <a name="configure-private-endpoints-for-azure-event-grid-topics-or-domains"></a>Privát végpontok konfigurálása Azure Event Grid témakörök vagy tartományok számára
Privát végpontok [használatával](../private-link/private-endpoint-overview.md) biztonságosan, a nyilvános interneten való áthaladás nélkül engedélyezheti az események bejövő forgalomát [a](../private-link/private-link-overview.md) virtuális hálózatból a témakörökbe és tartományokba egy privát kapcsolaton keresztül. A privát végpont a virtuális hálózat címtartományának IP-címét használja a témakör vagy tartomány számára. További elméleti információkért lásd: [Hálózati biztonság.](network-security.md)

Ez a cikk azt ismerteti, hogyan konfigurálhatja a privát végpontokat témakörök vagy tartományok számára.

## <a name="use-azure-portal"></a>Az Azure Portal használata 
Ez a szakasz bemutatja, hogyan használhatja a Azure Portal egy témakör vagy tartomány privát végpontjának létrehozásához.

> [!NOTE]
> Az ebben a szakaszban bemutatott lépések többnyire témakörökre mutatnak. Hasonló lépésekkel hozhat létre privát végpontokat a **tartományokhoz.** 

1. Jelentkezzen be a [Azure Portal,](https://portal.azure.com) és keresse meg a témakört vagy a tartományt.
2. Váltson **a témaköroldal** Hálózat lapjára. Az **eszköztáron válassza a +** Privát végpont lehetőséget.

    ![Privát végpont hozzáadása](./media/configure-private-endpoints/add-button.png)
2. Az Alapvető **funkciók lapon** kövesse az alábbi lépéseket: 
    1. Válassza ki azt **az Azure-előfizetést,** amelyben létre szeretné hozni a privát végpontot. 
    2. Válasszon ki **egy Azure-erőforráscsoportot** a privát végponthoz. 
    3. Adja **meg** a végpont nevét. 
    4. Válassza ki **a végpont** régióját. A privát végpontnak és a virtuális hálózatnak ugyanabban a régióban kell lennie, de más régióban is lehet, mint a privát kapcsolati erőforrás (ebben a példában egy Event Grid-témakör). 
    5. Ezután válassza **a Next: Resource >(Tovább:** Erőforrás-erőforráscsoport) gombot a lap alján. 

      ![Privát végpont – alapszintű lap](./media/configure-private-endpoints/basics-page.png)
3. Az Erőforrás **lapon** kövesse az alábbi lépéseket: 
    1. A csatlakozási módszernél kövesse az alábbi lépéseket, ha a címtárban a **Csatlakozás Azure-erőforráshoz** lehetőséget választja. Ez a példa bemutatja, hogyan csatlakozhat egy Azure-erőforráshoz a címtárban. 
        1. Válassza ki **azt az Azure-előfizetést,** amelyben **a témakör/tartomány** létezik. 
        1. Az **Erőforrástípus mezőben** válassza a **Microsoft.EventGrid/topics** vagy **a Microsoft.EventGrid/domains** **erőforrástípust.**
        2. Az **Erőforrás** mezőben válasszon ki egy témakört/tartományt a legördülő listából. 
        3. Győződjön meg arról, hogy a  **Cél alerőforrás** beállítása témakör vagy tartomány **(a** kiválasztott erőforrástípus alapján).    
        4. Válassza **a Tovább: >** gombra a lap alján. 

            ![A "Privát végpont létrehozása – Erőforrás" lapot bemutató képernyőkép.](./media/configure-private-endpoints/resource-page.png)
    2. Ha a Csatlakozás **erőforrás-azonosítóval** vagy aliassal lehetőséget választja, kövesse az alábbi lépéseket:
        1. Adja meg az erőforrás azonosítóját. Példa: `/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>`.  
        2. A **Resource (Erőforrás)** területen adja meg **a témakört** vagy **tartományt.** 
        3. (nem kötelező) Adjon hozzá egy kérésüzenetet. 
        4. Válassza **a Tovább: >** gombra a lap alján. 

            ![Privát végpont – erőforráslap](./media/configure-private-endpoints/connect-azure-resource-id.png)
4. A Konfiguráció **lapon** válassza ki azt a virtuális hálózat alhálózatát, ahová a privát végpontot telepíteni szeretné. 
    1. Válasszon ki **egy virtuális hálózatot.** A legördülő listában csak az aktuálisan kiválasztott előfizetésben és helyen lévő virtuális hálózatok listában szerepelnek. 
    2. Válasszon ki **egy alhálózatot** a kiválasztott virtuális hálózaton. 
    3. Válassza **a Tovább: Címkék >** gombot a lap alján. 

    ![Privát végpont – konfigurációs lap](./media/configure-private-endpoints/configuration-page.png)
5. A Címkék **lapon** hozza létre a privát végpont erőforrásával társítani kívánt címkéket (neveket és értékeket). Ezután válassza **az Áttekintés + létrehozás** gombot az oldal alján. 
6. Az Áttekintés **+ létrehozás lapon** tekintse át az összes beállítást, majd válassza a **Létrehozás** lehetőséget a privát végpont létrehozásához. 

    ![Privát végpont – & lap áttekintése](./media/configure-private-endpoints/review-create-page.png)
    

### <a name="manage-private-link-connection"></a>Privát kapcsolati kapcsolat kezelése

Privát végpont létrehozásakor a kapcsolatot jóvá kell hagyni. Ha az erőforrás, amelyhez privát végpontot hoz létre, a címtárban található, jóváhagyhatja a kapcsolatkérést, ha rendelkezik a megfelelő engedélyekkel. Ha egy másik címtárban található Azure-erőforráshoz csatlakozik, meg kell várnia, amíg az erőforrás tulajdonosa jóváhagyja a kapcsolódási kérelmet.

Négy kiépítési állam van:

| Szolgáltatásművelet | Szolgáltatás fogyasztói privát végpontjának állapota | Leírás |
|--|--|--|
| Nincs | Függőben | A kapcsolat manuálisan jön létre, és a privát kapcsolati erőforrás tulajdonosa jóváhagyásra vár. |
| Jóváhagyás | Approved | A kapcsolat automatikusan vagy manuálisan lett jóváhagyva, és készen áll a használatra. |
| Elutasítás | Elutasítva | A kapcsolatot a privát kapcsolati erőforrás tulajdonosa elutasította. |
| Eltávolítás | Leválasztott | A kapcsolatot a privát kapcsolati erőforrás tulajdonosa eltávolította, a privát végpont informatívvá válik, és törölni kell a törléshez. |
 
###  <a name="how-to-manage-a-private-endpoint-connection"></a>Privát végpontkapcsolat kezelése
A következő szakaszok azt mutatják be, hogyan lehet jóváhagyni vagy elutasítani egy privát végponti kapcsolatot. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A keresősávba írja be a **következőt: Event Grid témakörök vagy** Event Grid **tartomány.**
1. Válassza ki **a** **kezelni** kívánt témakört vagy tartományt.
1. Válassza a **Hálózatkezelés** lapot.
1. Ha vannak függőben lévő kapcsolatok, akkor a Függőben  állapottal listázott kapcsolat látható. 

### <a name="to-approve-a-private-endpoint"></a>Privát végpont jóváhagyása
Jóváhagyhat egy függőben lévő privát végpontot. A jóváhagyáshoz kövesse az alábbi lépéseket: 

> [!NOTE]
> Az ebben a szakaszban bemutatott lépések többnyire témakörökre mutatnak. A tartományok privát végpontjainak jóváhagyásához hasonló lépéseket **is használhat.** 

1. Válassza ki **a jóváhagyni** kívánt privát végpontot, majd válassza az eszköztár **Jóváhagyás** gombját.

    ![Privát végpont – függőben lévő állapot](./media/configure-private-endpoints/pending.png)
1. A Kapcsolat **jóváhagyása párbeszédpanelen** írjon be egy megjegyzést (nem kötelező), majd válassza az **Igen lehetőséget.** 

    ![Privát végpont – jóváhagyás](./media/configure-private-endpoints/approve.png)
1. Győződjön meg arról, hogy a végpont Állapota **Jóváhagyott.** 

    ![Privát végpont – jóváhagyott állapot](./media/configure-private-endpoints/approved-status.png)

### <a name="to-reject-a-private-endpoint"></a>Privát végpont elutasítása
Elutasíthat egy függőben lévő vagy jóváhagyott állapotban lévő privát végpontot. Az elutasításhoz kövesse az alábbi lépéseket: 

> [!NOTE]
> Az ebben a szakaszban bemutatott lépések témakörökre mutatnak. Hasonló lépésekkel utasíthatja el a tartományok privát **végpontját.** 

1. Válassza ki **az elutasítani** kívánt privát végpontot, majd válassza az **Elutasítás** lehetőséget az eszköztáron.

    ![Képernyőkép a "Hálózat – Privát végpontkapcsolatok" elemről az "Elutasítás" beállítással.](./media/configure-private-endpoints/reject-button.png)
1. A Kapcsolat **elutasítása párbeszédpanelen** írjon be egy megjegyzést (nem kötelező), majd válassza az **Igen lehetőséget.** 

    ![Privát végpont – elutasítás](./media/configure-private-endpoints/reject.png)
1. Győződjön meg arról, hogy a végpont Elutasítva **állapotú.** 

    ![Privát végpont – elutasított állapot](./media/configure-private-endpoints/rejected-status.png)

    > [!NOTE]
    > Az elutasítás után nem hagyhat jóvá privát végpontot a Azure Portal-ban. 


## <a name="use-azure-cli"></a>Az Azure parancssori felület használatával
Privát végpont létrehozásához használja az [az network private-endpoint create](/cli/azure/network/private-endpoint?#az_network_private_endpoint_create) metódust az alábbi példában látható módon:

```azurecli-interactive
az network private-endpoint create \
    --resource-group <RESOURECE GROUP NAME> \
    --name <PRIVATE ENDPOINT NAME> \
    --vnet-name <VIRTUAL NETWORK NAME> \
    --subnet <SUBNET NAME> \
    --private-connection-resource-id "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME> \
    --connection-name <PRIVATE LINK SERVICE CONNECTION NAME> \
    --location <LOCATION> \
    --group-ids topic
```

A példában használt paraméterek leírását az [az network private-endpoint create dokumentációjában találhatja meg.](/cli/azure/network/private-endpoint?#az_network_private_endpoint_create) Ebben a példában érdemes megjegyezni néhány dolgot: 

- A `private-connection-resource-id` esetén adja meg a témakör vagy tartomány **erőforrás-azonosítóját.**  Az előző példa a type: topic típust használja.
- a `group-ids` , adja meg a vagy a `topic` `domain` értéket. Az előző példában `topic` a van használva. 

Privát végpont törléséhez használja az [az network private-endpoint delete](/cli/azure/network/private-endpoint?#az_network_private_endpoint_delete) metódust az alábbi példában látható módon:

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> Az ebben a szakaszban bemutatott lépések témakörökre mutatnak. Hasonló lépésekkel hozhat létre privát végpontokat a **tartományokhoz.** 



### <a name="prerequisites"></a>Előfeltételek
Frissítse a Azure Event Grid CLI-hez a következő parancs futtatásával: 

```azurecli-interactive
az extension update -n eventgrid
```

Ha a bővítmény nincs telepítve, futtassa a következő parancsot a telepítéséhez: 

```azurecli-interactive
az extension add -n eventgrid
```

### <a name="create-a-private-endpoint"></a>Privát végpont létrehozása
Privát végpont létrehozásához használja az [az network private-endpoint create](/cli/azure/network/private-endpoint?#az_network_private_endpoint_create) metódust az alábbi példában látható módon:

```azurecli-interactive
az network private-endpoint create \
    --resource-group <RESOURECE GROUP NAME> \
    --name <PRIVATE ENDPOINT NAME> \
    --vnet-name <VIRTUAL NETWORK NAME> \
    --subnet <SUBNET NAME> \
    --private-connection-resource-id "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME> \
    --connection-name <PRIVATE LINK SERVICE CONNECTION NAME> \
    --location <LOCATION> \
    --group-ids topic
```

A példában használt paraméterek leírását az [az network private-endpoint create dokumentációjában találhatja meg.](/cli/azure/network/private-endpoint?#az_network_private_endpoint_create) Ebben a példában érdemes megjegyezni néhány dolgot: 

- A `private-connection-resource-id` esetén adja meg a témakör vagy tartomány **erőforrás-azonosítóját.**  Az előző példa a type: topic típust használja.
- a `group-ids` következő számára: , adja meg a `topic` vagy a `domain` értéket. Az előző példában `topic` a van használva. 

Privát végpont törléséhez használja az [az network private-endpoint delete](/cli/azure/network/private-endpoint?#az_network_private_endpoint_delete) metódust az alábbi példában látható módon:

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> Az ebben a szakaszban bemutatott lépések témakörökre mutatnak. Hasonló lépésekkel hozhat létre privát végpontokat a **tartományokhoz.** 

#### <a name="sample-script"></a>Példaszkript
Az alábbi példaszk szkript a következő Azure-erőforrásokat hozza létre:

- Erőforráscsoport
- Virtuális hálózat
- A virtuális hálózat alhálózata
- Azure Event Grid témakör
- A témakör privát végpontja

> [!NOTE]
> Az ebben a szakaszban bemutatott lépések témakörökre mutatnak. Hasonló lépésekkel hozhat létre privát végpontokat a tartományokhoz.

```azurecli-interactive
subscriptionID="<AZURE SUBSCRIPTION ID>"
resourceGroupName="<RESOURCE GROUP NAME>"
location="<LOCATION>"
vNetName="<VIRTUAL NETWORK NAME>"
subNetName="<SUBNET NAME>"
topicName = "<TOPIC NAME>"
connectionName="<ENDPOINT CONNECTION NAME>"
endpointName=<ENDPOINT NAME>

# resource ID of the topic. replace <SUBSCRIPTION ID>, <RESOURCE GROUP NAME>, and <TOPIC NAME>
topicResourceID="/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME>"

# select subscription
az account set --subscription $subscriptionID

# create resource group
az group create --name $resourceGroupName --location $location

# create vnet 
az network vnet create \
    --resource-group $resourceGroupName \
    --name $vNetName \
    --address-prefix 10.0.0.0/16

# create subnet
az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --address-prefixes 10.0.0.0/24

# disable private endpoint network policies for the subnet
az network vnet subnet update \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --disable-private-endpoint-network-policies true

# create event grid topic. update <LOCATION>
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location

# verify that the topic was created.
az eventgrid topic show \
    --resource-group $resourceGroupName \
    --name $topicName

# create private endpoint for the topic you created
az network private-endpoint create 
    --resource-group $resourceGroupName \
    --name $endpointName \
    --vnet-name $vNetName \
    --subnet $subNetName \
    --private-connection-resource-id $topicResourceID \
    --connection-name $connectionName \
    --location $location \
    --group-ids topic

# get topic 
az eventgrid topic show \
    --resource-group $resourceGroupName \
    --name $topicName

```

### <a name="approve-a-private-endpoint"></a>Privát végpont jóváhagyása
Az alábbi CLI-kódrészletminta bemutatja, hogyan lehet jóváhagyni egy privát végponti kapcsolatot. 

```azurecli-interactive
az eventgrid topic private-endpoint-connection approve \
    --resource-group $resourceGroupName \
    --topic-name $topicName \
    --name  $endpointName \
    --description "connection approved"
```


### <a name="reject-a-private-endpoint"></a>Privát végpont elutasítása
Az alábbi CLI-kódrészletminta bemutatja, hogyan utasíthatja el a privát végponti kapcsolatot. 

```azurecli-interactive
az eventgrid topic private-endpoint-connection reject \
    --resource-group $resourceGroupName \
    --topic-name $topicName \
    --name $endpointName \
    --description "Connection rejected"
```

### <a name="disable-public-network-access"></a>Nyilvános hálózati hozzáférés letiltása
Alapértelmezés szerint a nyilvános hálózati hozzáférés engedélyezve van egy Event Grid témakörben vagy tartományban. Ha csak privát végpontokkal szeretné engedélyezni a hozzáférést, tiltsa le a nyilvános hálózati hozzáférést a következő parancs futtatásával:  

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access disabled
```


## <a name="use-powershell"></a>A PowerShell használata
Ez a szakasz bemutatja, hogyan hozhat létre privát végpontot egy témakör vagy tartomány számára a PowerShell használatával. 

### <a name="prerequisite"></a>Előfeltétel
Kövesse az Útmutató: A portál használata olyan [Azure AD-alkalmazás](../active-directory/develop/howto-create-service-principal-portal.md) és -szolgáltatásnév létrehozásához, amely erőforrásokhoz fér hozzá egy Azure Active Directory-alkalmazás létrehozásához, és jegyezze fel a címtár (bérlő) azonosítójának, az alkalmazás **(ügyfél)** azonosítójának és az alkalmazás (ügyfél) titkos kódjának **értékeit.**  

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Jogkivonat és fejlécek előkészítése REST API hívásokhoz 
Futtassa az alábbi előfeltételként szükséges parancsokat egy hitelesítési jogkivonat le REST API hívásokkal és engedélyezéssel és egyéb fejléc-információkkal való használathoz. 

```azurepowershell-interactive
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get authentication token
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token  `
    -Body $body  `
    -ContentType 'application/x-www-form-urlencoded' 

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
```

### <a name="create-a-subnet-with-endpoint-network-policies-disabled"></a>Alhálózat létrehozása letiltott végponthálózati szabályzatokkal

```azurepowershell-interactive

# create resource group
New-AzResourceGroup -ResourceGroupName <RESOURCE GROUP NAME>  -Location <LOCATION>

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName <RESOURCE GROUP NAME> `
                    -Location <LOCATION> `
                    -Name <VIRTUAL NETWORK NAME> `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name example-privatelinksubnet `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork
```

### <a name="create-an-event-grid-topic-with-a-private-endpoint"></a>Event Grid-témakör létrehozása privát végponttal

> [!NOTE]
> Az ebben a szakaszban bemutatott lépések témakörökre mutatnak. Hasonló lépésekkel hozhat létre privát végpontokat a **tartományokhoz.** 


```azurepowershell-interactive
$body = @{"location"="<LOCATION>"; "properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json

# create topic
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $body

# verify that the topic was created
$topic=Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01"   `
    -Headers $Headers  

# create private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name "<PRIVATE LINK SERVICE CONNECTION NAME>" `
                                -PrivateLinkServiceId $topic.Id `
                                -GroupId "topic"

# get virtual network info 
$virtualNetwork = Get-AzVirtualNetwork  `
                    -ResourceGroupName  <RESOURCE GROUP NAME> `
                    -Name <VIRTUAL NETWORK NAME>

# get subnet info
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                             | Where-Object  {$_.Name -eq <SUBNET NAME>}  

# now, you are ready to create a private endpoint 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName <RESOURCE GROUP NAME>  `
                                        -Name <PRIVATE ENDPOINT NAME>   `
                                        -Location <LOCATION> `
                                        -Subnet  $subnet   `
                                        -PrivateLinkServiceConnection $privateEndpointConnection

# verify that the endpoint was created
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections?api-version=2020-06-01"   `
    -Headers $Headers   `
    | ConvertTo-Json -Depth 5

```

Amikor ellenőrzi, hogy a végpont létrejött-e, az eredménynek az alábbihoz hasonlónak kell lennie:

```json

{
  "value": [
    {
      "properties": {
        "privateEndpoint": {
          "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Network/privateEndpoints/<PRIVATE ENDPOINT NAME>"
        },
        "groupIds": [
          "topic"
        ],
        "privateLinkServiceConnectionState": {
          "status": "Approved",
          "description": "Auto-approved",
          "actionsRequired": "None"
        },
        "provisioningState": "Succeeded"
      },
      "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>",
      "name": "myConnection",
      "type": "Microsoft.EventGrid/topics/privateEndpointConnections"
    }
  ]
}
```

### <a name="approve-a-private-endpoint-connection"></a>Privát végpontkapcsolat jóváhagyása
Az alábbi PowerShell-kódrészletminta bemutatja, hogyan lehet jóváhagyni egy privát végpontot. 

> [!NOTE]
> Az ebben a szakaszban bemutatott lépések témakörökre mutatnak. A tartományok privát végpontjainak jóváhagyásához hasonló lépéseket **is használhat.** 

```azurepowershell-interactive
$approvedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="approved";"description"="connection approved";"actionsRequired"="none"}}} | ConvertTo-Json

# approve endpoint connection
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AzuRE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $approvedBody

# confirm that the endpoint connection was approved
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers

```

### <a name="reject-a-private-endpoint-connection"></a>Privát végpont kapcsolatának elutasítása
Az alábbi példa bemutatja, hogyan utasíthat el egy privát végpontot a PowerShell használatával. A privát végpont GUID azonosítóját az előző GET parancs eredményében kaphatja meg. 

> [!NOTE]
> Az ebben a szakaszban bemutatott lépések témakörökre mutatnak. Hasonló lépésekkel utasíthatja el a tartományok privát **végpontját.** 


```azurepowershell-interactive
$rejectedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="rejected";"description"="connection rejected";"actionsRequired"="none"}}} | ConvertTo-Json

# reject private endpoint
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $rejectedBody

# confirm that endpoint was rejected
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01" ` 
    -Headers $Headers
```

A kapcsolatot az API-n keresztüli elutasítás után is jóváhagyhatja. Ha ilyen Azure Portal használ, nem hagyhat jóvá elutasított végpontokat. 

## <a name="next-steps"></a>Következő lépések
* További információ az IP-tűzfal beállításainak konfigurálásról: IP-tűzfal konfigurálása Azure Event Grid [témakörökben vagy tartományokban.](configure-firewall.md)
* A hálózati kapcsolati hibák elhárításához lásd: [Hálózati kapcsolati problémák elhárítása](troubleshoot-network-connectivity.md)
