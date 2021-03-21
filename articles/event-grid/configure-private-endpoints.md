---
title: Privát végpontok konfigurálása Azure Event Grid témakörökhöz vagy tartományokhoz
description: Ez a cikk ismerteti, hogyan konfigurálhat privát végpontokat Azure Event Grid témakörökhöz vagy tartományhoz.
ms.topic: how-to
ms.date: 11/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: f8a9ac46596b1c2611ce9df387ac995e8149e7b9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102425345"
---
# <a name="configure-private-endpoints-for-azure-event-grid-topics-or-domains"></a>Privát végpontok konfigurálása Azure Event Grid témakörökhöz vagy tartományokhoz
A [privát végpontok](../private-link/private-endpoint-overview.md) lehetővé teszik, hogy közvetlenül a virtuális hálózatról küldje el az eseményeket egy [privát kapcsolaton](../private-link/private-link-overview.md) keresztül, anélkül, hogy a nyilvános interneten kellene haladnia. A privát végpont egy IP-címet használ a témakörhöz vagy tartományhoz tartozó VNet. További elméleti információkat a [hálózati biztonság](network-security.md)című témakörben talál.

Ez a cikk bemutatja, hogyan konfigurálhat saját végpontokat témakörökhöz vagy tartományokhoz.

## <a name="use-azure-portal"></a>Az Azure Portal használata 
Ebből a szakaszból megtudhatja, hogyan hozhat létre saját végpontot egy témakörhöz vagy tartományhoz a Azure Portal használatával.

> [!NOTE]
> Az ebben a szakaszban bemutatott lépések többnyire témakörök. A hasonló lépéseket követve saját végpontokat hozhat létre a **tartományokhoz**. 

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon a témakörhöz vagy tartományhoz.
2. Váltson a témakör oldal **hálózatkezelés** lapjára. Válassza a **+ privát végpont** lehetőséget az eszköztáron.

    ![Privát végpont hozzáadása](./media/configure-private-endpoints/add-button.png)
2. Az **alapvető beállítások** lapon kövesse az alábbi lépéseket: 
    1. Válassza ki azt az **Azure-előfizetést** , amelyben létre szeretné hozni a privát végpontot. 
    2. Válasszon ki egy **Azure-erőforráscsoportot** a privát végpont számára. 
    3. Adja meg a végpont **nevét** . 
    4. Válassza ki a végpont **régióját** . A privát végpontnak ugyanabban a régióban kell lennie, mint a virtuális hálózatnak, de a privát kapcsolati erőforrástól (ebben a példában egy Event Grid-témakör) eltérő régióban lehet. 
    5. Ezután válassza a **Tovább: erőforrás >** gombot az oldal alján. 

      ![Privát végpont – alapismeretek lap](./media/configure-private-endpoints/basics-page.png)
3. Az **erőforrás** oldalon hajtsa végre az alábbi lépéseket: 
    1. A kapcsolódási módszer esetén, ha a **címtárban a kapcsolódás Azure-erőforráshoz** lehetőséget választja, kövesse az alábbi lépéseket. Ez a példa bemutatja, hogyan csatlakozhat egy Azure-erőforráshoz a címtárban. 
        1. Válassza ki azt az **Azure-előfizetést** , amelyben a **témakör/tartomány** létezik. 
        1. Az **erőforrástípus** mezőben válassza a **Microsoft. EventGrid/témák** vagy a **Microsoft. EventGrid/tartományok** elemet az **erőforrás típushoz**.
        2. Az **erőforrás** területen válasszon ki egy témakört vagy tartományt a legördülő listából. 
        3. Győződjön meg arról, hogy a **célként megadott alerőforrás** a (z) **témakörre** vagy **tartományra** van beállítva (a kiválasztott erőforrástípus alapján).    
        4. Válassza a **Tovább: konfigurációs >** gombot az oldal alján. 

            ![Képernyőkép, amely a "privát végpont-erőforrás létrehozása" lapot mutatja.](./media/configure-private-endpoints/resource-page.png)
    2. Ha a **Kapcsolódás erőforráshoz erőforrás-azonosító vagy alias használatával** lehetőséget választja, kövesse az alábbi lépéseket:
        1. Adja meg az erőforrás AZONOSÍTÓját. Példa: `/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>`.  
        2. Az **erőforrás** mezőbe írja be a következőt: **témakör** vagy **tartomány**. 
        3. választható Adja meg a kérelem üzenetét. 
        4. Válassza a **Tovább: konfigurációs >** gombot az oldal alján. 

            ![Magánhálózati végpont – Erőforrás lap](./media/configure-private-endpoints/connect-azure-resource-id.png)
4. A **konfiguráció** lapon válassza ki azt az alhálózatot egy virtuális hálózaton, ahová a magánhálózati végpontot telepíteni szeretné. 
    1. Válasszon ki egy **virtuális hálózatot**. A legördülő listában csak a jelenleg kijelölt előfizetésben és helyen lévő virtuális hálózatok jelennek meg. 
    2. Válasszon egy **alhálózatot** a kiválasztott virtuális hálózatban. 
    3. Válassza a **Tovább: címkék >** gombot az oldal alján. 

    ![Magánhálózati végpont – konfigurációs lap](./media/configure-private-endpoints/configuration-page.png)
5. A **címkék** lapon hozzon létre minden olyan címkét (nevet és értéket), amelyet hozzá szeretne rendelni a privát végpont-erőforráshoz. Ezután kattintson az oldal alján található **felülvizsgálat + létrehozás** gombra. 
6. A **felülvizsgálat + létrehozás** lapon tekintse át az összes beállítást, majd kattintson a **Létrehozás** elemre a privát végpont létrehozásához. 

    ![Privát végpont – áttekintés & lap létrehozása](./media/configure-private-endpoints/review-create-page.png)
    

### <a name="manage-private-link-connection"></a>Privát kapcsolat kapcsolatának kezelése

Privát végpont létrehozásakor jóvá kell hagyni a kapcsolódást. Ha az erőforrás, amelyhez privát végpontot hoz létre, akkor jóváhagyhatja a megfelelő engedélyekkel rendelkező kapcsolatkérelem-kérést. Ha egy másik címtárban található Azure-erőforráshoz csatlakozik, meg kell várnia, hogy az erőforrás tulajdonosa jóváhagyja a kapcsolódási kérelmet.

Négy kiépítési állapot létezik:

| Szolgáltatásművelet | A szolgáltatás fogyasztói magánhálózati végpontjának állapota | Leírás |
|--|--|--|
| Nincs | Függőben | A kapcsolat manuálisan lett létrehozva, és jóváhagyásra vár a Private link erőforrás-tulajdonostól. |
| Jóváhagyás | Approved | A kapcsolódás automatikusan vagy manuálisan lett jóváhagyva, és készen áll a használatra. |
| Elutasítás | Elutasítva | A magánhálózati kapcsolat erőforrásának tulajdonosa elutasította a kapcsolatot. |
| Eltávolítás | Leválasztott | A kapcsolatot a privát kapcsolat erőforrás-tulajdonosa eltávolította, a magánhálózati végpont informatív lesz, és törölni kell a tisztításhoz. |
 
###  <a name="how-to-manage-a-private-endpoint-connection"></a>Privát végponti kapcsolatok kezelése
A következő részben bemutatjuk, hogyan lehet jóváhagyni vagy visszautasítani egy privát végponti kapcsolat létrejöttét. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A keresősáv mezőbe írja be **Event Grid témaköröket** vagy **Event Grid tartományokat**.
1. Válassza ki a kezelni kívánt **témakört** vagy **tartományt** .
1. Válassza a **Hálózatkezelés** lapot.
1. Ha van függőben lévő kapcsolat, a rendszer a kiépítési állapot **függőben lévő** kapcsolatát fogja látni. 

### <a name="to-approve-a-private-endpoint"></a>Privát végpont jóváhagyása
Jóvá is hagyhatja a függő állapotú privát végpontokat. A jóváhagyáshoz kövesse az alábbi lépéseket: 

> [!NOTE]
> Az ebben a szakaszban bemutatott lépések többnyire témakörök. A **tartományokhoz** tartozó magánhálózati végpontok jóváhagyásához hasonló lépések használhatók. 

1. Válassza ki a jóváhagyni kívánt **privát végpontot** , és válassza a **jóváhagyás** lehetőséget az eszköztáron.

    ![Magánhálózati végpont – függő állapot](./media/configure-private-endpoints/pending.png)
1. A **kapcsolatok jóváhagyása** párbeszédpanelen írjon be egy megjegyzést (nem kötelező), majd válassza az **Igen** lehetőséget. 

    ![Magánhálózati végpont – jóváhagyás](./media/configure-private-endpoints/approve.png)
1. Győződjön meg róla, hogy a végpont állapota **jóváhagyottként** jelenik meg. 

    ![Magánhálózati végpont – jóváhagyott állapot](./media/configure-private-endpoints/approved-status.png)

### <a name="to-reject-a-private-endpoint"></a>Privát végpont elutasítása
Elutasítja a függő állapotú vagy jóváhagyott állapotú privát végpontokat. Az elutasításhoz kövesse az alábbi lépéseket: 

> [!NOTE]
> Az ebben a szakaszban bemutatott lépések témakörökre vonatkoznak. Hasonló lépéseket használhat a privát végpontok elutasításához a **tartományokban**. 

1. Válassza ki azt a **privát végpontot** , amelyet el szeretne utasítani, majd válassza az **elutasítás** lehetőséget az eszköztáron.

    ![Képernyőkép, amely a "hálózatkezelés – privát végponti kapcsolatok" elemet jeleníti meg az "elutasítás" beállítással.](./media/configure-private-endpoints/reject-button.png)
1. A **kapcsolatok elutasítása** párbeszédpanelen írjon be egy megjegyzést (nem kötelező), majd válassza az **Igen** lehetőséget. 

    ![Privát végpont – elutasítás](./media/configure-private-endpoints/reject.png)
1. Ellenőrizze, hogy a végpont állapota **visszautasítva** állapotú-e. 

    ![Magánhálózati végpont – elutasított állapot](./media/configure-private-endpoints/rejected-status.png)

    > [!NOTE]
    > Egy privát végpontot nem hagyhat jóvá a Azure Portal a visszautasítása után. 


## <a name="use-azure-cli"></a>Az Azure parancssori felület használatával
Privát végpont létrehozásához használja az az [Network Private-Endpoint Create](/cli/azure/network/private-endpoint?#az-network-private-endpoint-create) metódust az alábbi példában látható módon:

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

A példában használt paraméterek leírását az az [Network Private-Endpoint Create](/cli/azure/network/private-endpoint?#az-network-private-endpoint-create)című dokumentációban találja. Ebben a példában a következő szempontokat kell figyelembe venni: 

- A esetében `private-connection-resource-id` a **témakör** vagy **tartomány** erőforrás-azonosítóját kell megadnia. Az előző példa a következő típust használja: témakör.
- a esetében válassza a vagy a következőt: `group-ids` `topic` `domain` . Az előző példában a `topic` használatban van. 

Privát végpont törléséhez használja az az [Network Private-Endpoint delete](/cli/azure/network/private-endpoint?#az-network-private-endpoint-delete) metódust az alábbi példában látható módon:

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> Az ebben a szakaszban bemutatott lépések témakörökre vonatkoznak. A hasonló lépéseket követve saját végpontokat hozhat létre a **tartományokhoz**. 



### <a name="prerequisites"></a>Előfeltételek
Frissítse a CLI Azure Event Grid bővítményét a következő parancs futtatásával: 

```azurecli-interactive
az extension update -n eventgrid
```

Ha a bővítmény nincs telepítve, futtassa a következő parancsot a telepítéséhez: 

```azurecli-interactive
az extension add -n eventgrid
```

### <a name="create-a-private-endpoint"></a>Privát végpont létrehozása
Privát végpont létrehozásához használja az az [Network Private-Endpoint Create](/cli/azure/network/private-endpoint?#az-network-private-endpoint-create) metódust az alábbi példában látható módon:

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

A példában használt paraméterek leírását az az [Network Private-Endpoint Create](/cli/azure/network/private-endpoint?#az-network-private-endpoint-create)című dokumentációban találja. Ebben a példában a következő szempontokat kell figyelembe venni: 

- A esetében `private-connection-resource-id` a **témakör** vagy **tartomány** erőforrás-azonosítóját kell megadnia. Az előző példa a következő típust használja: témakör.
- a esetében válassza a vagy a következőt: `group-ids` `topic` `domain` . Az előző példában a `topic` használatban van. 

Privát végpont törléséhez használja az az [Network Private-Endpoint delete](/cli/azure/network/private-endpoint?#az-network-private-endpoint-delete) metódust az alábbi példában látható módon:

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> Az ebben a szakaszban bemutatott lépések témakörökre vonatkoznak. A hasonló lépéseket követve saját végpontokat hozhat létre a **tartományokhoz**. 

#### <a name="sample-script"></a>Példaszkript
Íme egy példa, amely a következő Azure-erőforrásokat hozza létre:

- Erőforráscsoport
- Virtuális hálózat
- Alhálózat a virtuális hálózaton
- Azure Event Grid témakör
- Privát végpont a témakörhöz

> [!NOTE]
> Az ebben a szakaszban bemutatott lépések témakörökre vonatkoznak. A hasonló lépéseket követve saját végpontokat hozhat létre a tartományokhoz.

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
Az alábbi CLI-kódrészletből megtudhatja, hogyan hagyhatja jóvá a privát végpontok közötti kapcsolatokat. 

```azurecli-interactive
az eventgrid topic private-endpoint-connection approve \
    --resource-group $resourceGroupName \
    --topic-name $topicName \
    --name  $endpointName \
    --description "connection approved"
```


### <a name="reject-a-private-endpoint"></a>Privát végpont elutasítása
A következő minta CLI-kódrészletből megtudhatja, hogyan utasítsa el a privát végponti kapcsolatokat. 

```azurecli-interactive
az eventgrid topic private-endpoint-connection reject \
    --resource-group $resourceGroupName \
    --topic-name $topicName \
    --name $endpointName \
    --description "Connection rejected"
```

### <a name="disable-public-network-access"></a>Nyilvános hálózati hozzáférés letiltása
Alapértelmezés szerint a nyilvános hálózati hozzáférés engedélyezve van egy Event Grid témakörhöz vagy tartományhoz. Ha csak privát végpontokon keresztül szeretné engedélyezni a hozzáférést, tiltsa le a nyilvános hálózati hozzáférést a következő parancs futtatásával:  

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access disabled
```


## <a name="use-powershell"></a>A PowerShell használata
Ebből a szakaszból megtudhatja, hogyan hozhat létre saját végpontot egy témakörhöz vagy tartományhoz a PowerShell használatával. 

### <a name="prerequisite"></a>Előfeltétel
Kövesse az utasításokat a következő témakör útmutatását követve [: a portál használatával hozzon létre egy Azure ad-alkalmazást és egy egyszerű szolgáltatásnevet, amely hozzáférhet az erőforrásokhoz](../active-directory/develop/howto-create-service-principal-portal.md) Azure Active Directory alkalmazás létrehozásához, és jegyezze fel a **címtár (bérlő) azonosító**, az **alkalmazás (ügyfél) azonosítója** és az **alkalmazás (ügyfél) titkos kulcsának** értékét. 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Jogkivonatok és fejlécek előkészítése REST API-hívásokhoz 
A következő előfeltétel-utasítások futtatásával szerezzen be egy hitelesítési tokent REST API hívásokkal és engedélyezéssel, valamint más fejléc-információkkal való használatra. 

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

### <a name="create-a-subnet-with-endpoint-network-policies-disabled"></a>Alhálózat létrehozása végponti hálózati házirendekkel letiltva

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
> Az ebben a szakaszban bemutatott lépések témakörökre vonatkoznak. A hasonló lépéseket követve saját végpontokat hozhat létre a **tartományokhoz**. 


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

Ha ellenőrzi, hogy létrejött-e a végpont, a következőhöz hasonló eredményt kell látnia:

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

### <a name="approve-a-private-endpoint-connection"></a>Privát végponti kapcsolatok jóváhagyása
Az alábbi PowerShell-kódrészletből megtudhatja, hogyan hagyhat jóvá egy privát végpontot. 

> [!NOTE]
> Az ebben a szakaszban bemutatott lépések témakörökre vonatkoznak. A **tartományokhoz** tartozó magánhálózati végpontok jóváhagyásához hasonló lépések használhatók. 

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

### <a name="reject-a-private-endpoint-connection"></a>Privát végponti kapcsolatok elutasítása
Az alábbi példa bemutatja, hogyan utasíthatja el a privát végpontot a PowerShell használatával. A privát végpont GUID azonosítóját az előző GET parancs eredménye alapján szerezheti be. 

> [!NOTE]
> Az ebben a szakaszban bemutatott lépések témakörökre vonatkoznak. Hasonló lépéseket használhat a privát végpontok elutasításához a **tartományokban**. 


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

Az API-n keresztüli elutasítása után is jóváhagyhatja a kapcsolatokat. Ha Azure Portal használ, nem hagyhat jóvá egy elutasított végpontot. 

## <a name="next-steps"></a>Következő lépések
* Az IP-tűzfalbeállítások konfigurálásával kapcsolatos további tudnivalókért lásd: [IP-tűzfal konfigurálása Azure Event Grid témakörökhöz vagy tartományokhoz](configure-firewall.md).
* A hálózati kapcsolattal kapcsolatos problémák elhárításáról lásd: [hálózati kapcsolattal kapcsolatos problémák elhárítása](troubleshoot-network-connectivity.md)
