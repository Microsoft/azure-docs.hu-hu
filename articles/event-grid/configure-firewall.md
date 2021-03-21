---
title: IP-tűzfal konfigurálása Azure Event Grid témakörökhöz vagy tartományokhoz
description: Ez a cikk a tűzfalbeállítások konfigurálását ismerteti Event Grid témakörökhöz vagy tartományokhoz.
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 178b9d84ea8b2e0f764f7584526db8dbcf5284f3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031837"
---
# <a name="configure-ip-firewall-for-azure-event-grid-topics-or-domains"></a>IP-tűzfal konfigurálása Azure Event Grid témakörökhöz vagy tartományokhoz 
Alapértelmezés szerint a témakör és a tartomány elérhető az internetről, feltéve, hogy a kérés érvényes hitelesítéssel és engedélyezéssel rendelkezik. Az IP-tűzfallal a [CIDR (osztály nélküli Inter-Domain útválasztás)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) jelöléssel tovább korlátozhatja az IPv4-címek vagy az IPv4-címtartományok körét. A más IP-címről származó közzétevőket a rendszer elutasítja, és 403 (tiltott) választ fog kapni. A Event Grid által támogatott hálózati biztonsági funkciókkal kapcsolatos további információkért lásd: [Event Grid hálózati biztonsága](network-security.md).

Ez a cikk azt ismerteti, hogyan konfigurálható az IP-tűzfalbeállítások Azure Event Grid témakörökhöz vagy tartományokhoz.

## <a name="use-azure-portal"></a>Az Azure Portal használata
Ez a szakasz bemutatja, hogyan használható a Azure Portal a bejövő IP-tűzfalszabályok létrehozásához. Az ebben a szakaszban bemutatott lépések témakörökre vonatkoznak. Hasonló lépéseket használhat a **tartományok** bejövő IP-szabályainak létrehozásához. 

1. A [Azure Portal](https://portal.azure.com)navigáljon az Event Grid-témakörhöz vagy a tartományhoz, és váltson a **hálózatkezelés** lapra.
2. Válassza a **nyilvános hálózatok** lehetőséget az erőforrás eléréséhez az összes hálózat, beleértve az internetet. 

    Az IP-alapú tűzfalszabályok használatával korlátozhatja a forgalmat. Egyetlen IPv4-címet vagy IP-címtartományt kell megadnia az osztály nélküli Inter-domain Routing (CIDR) jelöléssel. 

    ![Képernyőfelvétel: a "nyilvános hálózati hozzáférés" lap van kijelölve a "nyilvános hálózatok" lapon.](./media/configure-firewall/public-networks-page.png)
3. Válassza a **privát végpontok** lehetőséget, hogy csak a privát végponti kapcsolatok férhessenek hozzá ehhez az erőforráshoz. A kapcsolatok kezeléséhez használja ezen a lapon a **Private Endpoint Connections** fület. 

    ![Nyilvános hálózatok lap](./media/configure-firewall/private-endpoints-page.png)
4. Válassza az eszköztár **Save** (Mentés) elemét. 



## <a name="use-azure-cli"></a>Az Azure parancssori felület használatával
Ebből a szakaszból megtudhatja, hogyan hozhat létre a bejövő IP-szabályokkal rendelkező témaköröket az Azure CLI-parancsok használatával. Az ebben a szakaszban bemutatott lépések témakörökre vonatkoznak. Hasonló lépéseket használhat a **tartományok** bejövő IP-szabályainak létrehozásához. 


### <a name="enable-or-disable-public-network-access"></a>Nyilvános hálózati hozzáférés engedélyezése vagy letiltása
Alapértelmezés szerint a nyilvános hálózati hozzáférés engedélyezve van a témakörökhöz és a tartományokhoz. Azt is engedélyezheti explicit módon, vagy letilthatja. A bejövő IP-tűzfalszabályok beállításával korlátozhatja a forgalmat. 

#### <a name="enable-public-network-access-while-creating-a-topic"></a>Nyilvános hálózati hozzáférés engedélyezése témakör létrehozásakor

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access enabled
```


#### <a name="disable-public-network-access-while-creating-a-topic"></a>Nyilvános hálózati hozzáférés letiltása témakör létrehozása közben

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access disabled
```

> [!NOTE]
> Ha egy témakör vagy tartomány nyilvános hálózati hozzáférése le van tiltva, a nyilvános interneten keresztüli forgalom nem engedélyezett. Ezekhez az erőforrásokhoz csak a magánhálózati végponti kapcsolatok férhetnek hozzá. 


#### <a name="enable-public-network-access-for-an-existing-topic"></a>Nyilvános hálózati hozzáférés engedélyezése meglévő témakörhöz

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled 
```

#### <a name="disable-public-network-access-for-an-existing-topic"></a>Nyilvános hálózati hozzáférés letiltása meglévő témakörhöz 

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access disabled
```

### <a name="create-a-topic-with-single-inbound-ip-rule"></a>Témakör létrehozása egyetlen bejövő IP-szabállyal
Az alábbi CLI-parancs egy Event Grid-témakört hoz létre a bejövő IP-szabályokkal. 

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR or CIDR MASK> allow 
```

### <a name="create-a-topic-with-multiple-inbound-ip-rules"></a>Témakör létrehozása több bejövő IP-szabállyal

Az alábbi CLI-parancs létrehoz egy Event Grid-témakört két bejövő IP-szabályként egy lépésben: 

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR 1 or CIDR MASK 1> allow \
    --inbound-ip-rules <IP ADDR 2 or CIDR MASK 2> allow
```

### <a name="update-an-existing-topic-to-add-inbound-ip-rules"></a>Meglévő témakör frissítése a bejövő IP-szabályok hozzáadásához
Ez a példa először létrehoz egy Event Grid-témakört, majd egy külön parancsban hozzáadja a témakörhöz tartozó bejövő IP-szabályokat. Emellett frissíti a második parancsban beállított bejövő IP-szabályokat is. 

```azurecli-interactive

# create the event grid topic first
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location

# add inbound IP rules to an existing topic
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR or CIDR MASK> allow

# later, update topic with additional ip rules
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR 1 or CIDR MASK 1> allow \
    --inbound-ip-rules <IP ADDR 2 or CIDR MASK 2> allow
```

### <a name="remove-an-inbound-ip-rule"></a>Bejövő IP-szabály eltávolítása
A következő parancs eltávolítja az előző lépésben létrehozott második szabályt úgy, hogy csak az első szabályt adta meg a beállítás frissítésekor. 

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR 1 or CIDR MASK 1> allow
```


## <a name="use-powershell"></a>A PowerShell használata
Ebből a szakaszból megtudhatja, hogyan hozhat létre Azure PowerShell-parancsokat a bejövő IP-tűzfalszabályok használatával Azure Event Grid témakörök létrehozásához. Az ebben a szakaszban bemutatott lépések témakörökre vonatkoznak. Hasonló lépéseket használhat a **tartományok** bejövő IP-szabályainak létrehozásához. 

### <a name="prerequisites"></a>Előfeltételek
Kövesse az utasításokat a következő témakör útmutatását követve [: a portál használatával hozzon létre egy Azure ad-alkalmazást és egy egyszerű szolgáltatásnevet, amely hozzáférhet az erőforrásokhoz](../active-directory/develop/howto-create-service-principal-portal.md) Azure Active Directory-alkalmazás létrehozásához, és jegyezze fel az alábbi értékeket:

- Címtár (bérlő) azonosítója
- Alkalmazás (ügyfél) azonosítója
- Alkalmazás (ügyfél) titka

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Jogkivonatok és fejlécek előkészítése REST API-hívásokhoz 
A következő előfeltétel-utasítások futtatásával szerezzen be egy hitelesítési tokent REST API hívásokkal, valamint az engedélyezési és egyéb fejléc-információkkal való használatra. 

```azurepowershell-interactive
# replace <CLIENT ID> and <CLIENT SECRET>
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get the authentication token. Replace <TENANT ID>
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token `
    -Body $body `
    -ContentType 'application/x-www-form-urlencoded'

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
$Headers.Add("Content-Type","application/json")
```

### <a name="enable-public-network-access-for-an-existing-topic"></a>Nyilvános hálózati hozzáférés engedélyezése meglévő témakörhöz
Alapértelmezés szerint a nyilvános hálózati hozzáférés engedélyezve van a témakörökhöz és a tartományokhoz. A bejövő IP-tűzfalszabályok beállításával korlátozhatja a forgalmat. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="enabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>Nyilvános hálózati hozzáférés letiltása meglévő témakörhöz
Ha egy témakör vagy tartomány nyilvános hálózati hozzáférése le van tiltva, a nyilvános interneten keresztüli forgalom nem engedélyezett. Ezekhez az erőforrásokhoz csak a magánhálózati végponti kapcsolatok férhetnek hozzá. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="create-an-event-grid-topic-with-inbound-rules-in-one-step"></a>Event Grid-témakör létrehozása bejövő szabályokkal egy lépésben

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that inbound IP rules are included. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDR or CIDR MASK>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# create the event grid topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5
```


### <a name="create-event-grid-topic-first-and-then-add-inbound-ip-rules"></a>Először hozzon létre egy Event Grid-témakört, majd adja hozzá a bejövő IP-szabályokat

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that no inbound IP rules are specified. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled";}} | ConvertTo-Json -Depth 5

# create the Event Grid topic
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body`

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

# prepare the body for REST PUT method. Notice that it includes inbound IP rules now. This feature is available in both basic and premium tiers.
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDR or CIDR MASK>";"action"="allow"}, @{"ipmask"="<IP ADDR or CIDR MASK>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# update the topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body

# verify that the topic was updated
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AzURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

```

## <a name="next-steps"></a>Következő lépések

* További információ az események kézbesítésének figyeléséről: [Event Grid üzenet kézbesítésének figyelése](monitor-event-delivery.md).
* További információ a hitelesítési kulcsról: [Event Grid biztonság és hitelesítés](security-authentication.md).
* Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid előfizetés sémája](subscription-creation-schema.md).
* A hálózati kapcsolattal kapcsolatos problémák elhárításáról lásd: [hálózati kapcsolattal kapcsolatos problémák elhárítása](troubleshoot-network-connectivity.md)
