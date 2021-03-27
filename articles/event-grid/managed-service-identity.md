---
title: Esemény-kézbesítés, felügyelt szolgáltatás identitása és privát hivatkozás
description: Ez a cikk azt ismerteti, hogyan engedélyezhető a felügyelt szolgáltatás identitása egy Azure Event Grid-témakörben. Használatával továbbíthatja az eseményeket a támogatott célhelyekre.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 76f10b4627dc9578b1e616a868eab03431b59b69
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625276"
---
# <a name="event-delivery-with-a-managed-identity"></a>Esemény kézbesítése felügyelt identitással
Ez a cikk azt ismerteti, hogyan használható a [felügyelt szolgáltatás identitása](../active-directory/managed-identities-azure-resources/overview.md) egy Azure Event grid rendszer-témakörhöz, egyéni témakörhöz vagy tartományhoz. Használatával továbbíthatja az eseményeket olyan támogatott célhelyekre, mint a Service Bus várólisták és témakörök, az Event hubok és a Storage-fiókok.



## <a name="prerequisites"></a>Előfeltételek
1. Rendeljen hozzá egy rendszerhez rendelt identitást egy rendszertémakörhöz, egy egyéni témakörhöz vagy egy tartományhoz. 
    - Egyéni témakörök és tartományok esetén tekintse meg az [Egyéni témakörök és tartományok felügyelt identitásának engedélyezése](enable-identity-custom-topics-domains.md)című témakört. 
    - Rendszertémakörök: [felügyelt identitás engedélyezése a rendszertémakörökhöz](enable-identity-system-topics.md)
1. Adja hozzá az identitást egy megfelelő szerepkörhöz (például Service Bus adatfeladóhoz) a célhelyen (például egy Service Bus üzenetsor). A részletes lépésekért lásd: [identitás hozzáadása az Azure-szerepkörökhöz célhelyeken](add-identity-roles.md)

    > [!NOTE]
    > Jelenleg nem lehet eseményeket kézbesíteni [privát végpontok](../private-link/private-endpoint-overview.md)használatával. További információt a cikk végén, a [privát végpontok](#private-endpoints) című szakaszban talál. 

## <a name="create-event-subscriptions-that-use-an-identity"></a>Identitást használó esemény-előfizetések létrehozása
Miután egy, a rendszer által felügyelt identitással rendelkező egyéni témakört vagy rendszertémakört vagy tartományt adott meg, és az identitást hozzáadta a megfelelő szerepkörhöz a célhelyen, készen áll az identitást használó előfizetések létrehozására. 

### <a name="use-the-azure-portal"></a>Az Azure Portal használata
Ha létrehoz egy esemény-előfizetést, a végpont **részletei** szakaszban megtekintheti a rendszer által hozzárendelt identitások használatának engedélyezését. 

![Identitás engedélyezése Service Bus üzenetsor esemény-előfizetésének létrehozásakor](./media/managed-service-identity/service-bus-queue-subscription-identity.png)

Azt is engedélyezheti, hogy a rendszer által hozzárendelt identitást használja a **további funkciók** lapon a kézbesítetlen levelekhez. 

![Rendszer által hozzárendelt identitás engedélyezése a kézbesítetlen levelekhez](./media/managed-service-identity/enable-deadletter-identity.png)

### <a name="use-the-azure-cli---service-bus-queue"></a>Az Azure CLI-Service Bus üzenetsor használata 
Ebből a szakaszból megtudhatja, hogyan használhatja az Azure CLI-t, hogy egy rendszer által hozzárendelt identitást engedélyezzen az események Service Bus üzenetsor számára való továbbításához. Az identitásnak a **Azure Service Bus Adatfeladói** szerepkör tagjának kell lennie. Emellett a **Storage-blob adatközreműködői** szerepkör tagjának kell lennie a kézbesítetlen levelekhez használt Storage-fiókban. 

#### <a name="define-variables"></a>Változók meghatározása
Először a CLI-parancsban használandó következő változók értékeit kell megadnia. 

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the service bus queue resource id
queueid=$(az servicebus queue show --namespace-name <SERVICE BUS NAMESPACE NAME> --name <QUEUE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
sb_esname = "<Specify a name for the event subscription>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Esemény-előfizetés létrehozása felügyelt identitás használatával kézbesítéshez 
Ez a minta-utasítás egy esemény-előfizetést hoz létre egy egyéni Event Grid-témakörhöz, amelyhez **Service Bus üzenetsor** van beállítva. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sb_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery-and-dead-lettering"></a>Esemény-előfizetés létrehozása felügyelt identitás használatával kézbesítéshez és kézbesítetlen levelekhez
Ez a minta-utasítás egy esemény-előfizetést hoz létre egy egyéni Event Grid-témakörhöz, amelyhez **Service Bus üzenetsor** van beállítva. Azt is megadja, hogy a rendszer által felügyelt identitást kell használni a kézbesítetlen levelekhez. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sb_esnameq 
```

### <a name="use-the-azure-cli---event-hubs"></a>Az Azure CLI használata – Event Hubs 
Ebből a szakaszból megtudhatja, hogyan használhatja az Azure CLI-t, hogy egy rendszer által hozzárendelt identitást engedélyezzen az események az Event hubhoz való továbbításához. Az identitásnak az **Azure Event Hubs Adatfeladói** szerepkör tagjának kell lennie. Emellett a **Storage-blob adatközreműködői** szerepkör tagjának kell lennie a kézbesítetlen levelekhez használt Storage-fiókban. 

#### <a name="define-variables"></a>Változók meghatározása
```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID CUSTOM TOPIC NAME>"

hubid=$(az eventhubs eventhub show --name <EVENT HUB NAME> --namespace-name <NAMESPACE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
eh_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Esemény-előfizetés létrehozása felügyelt identitás használatával kézbesítéshez 
Ez a minta-utasítás egy esemény-előfizetést hoz létre egy egyéni Event Grid-témakörhöz, amely **Event Hubs** értékre van beállítva. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type eventhub 
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    -n $sbq_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>Esemény-előfizetés létrehozása felügyelt identitás használatával a kézbesítéshez és a kézbesítetlen levelek 
Ez a minta-utasítás egy esemény-előfizetést hoz létre egy egyéni Event Grid-témakörhöz, amely **Event Hubs** értékre van beállítva. Azt is megadja, hogy a rendszer által felügyelt identitást kell használni a kézbesítetlen levelekhez. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    --deadletter-identity-endpoint $eh_deadletterendpoint
    --deadletter-identity systemassigned 
    -n $eh_esname 
```

### <a name="use-the-azure-cli---azure-storage-queue"></a>Az Azure CLI – Azure Storage-üzenetsor használata 
Ebből a szakaszból megtudhatja, hogyan használhatja az Azure CLI-t, hogy egy rendszer által hozzárendelt identitás használatával kézbesítse az eseményeket egy Azure Storage-üzenetsor számára. Az identitásnak a Storage-üzenetsor **adatüzenet-küldő** szerepkörének tagjának kell lennie a Storage-fiókban. Emellett a **Storage-blob adatközreműködői** szerepkör tagjának kell lennie a kézbesítetlen levelekhez használt Storage-fiókban.

#### <a name="define-variables"></a>Változók meghatározása  

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID CUSTOM TOPIC NAME>"

# get the storage account resource id
storageid=$(az storage account show --name <STORAGE ACCOUNT NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)

# build the resource id for the queue
queueid="$storageid/queueservices/default/queues/<QUEUE NAME>" 

sa_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Esemény-előfizetés létrehozása felügyelt identitás használatával kézbesítéshez 

```azurecli-interactive
az eventgrid event-subscription create 
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sa_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>Esemény-előfizetés létrehozása felügyelt identitás használatával a kézbesítéshez és a kézbesítetlen levelek 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sa_esname 
```

## <a name="private-endpoints"></a>Privát végpontok
Jelenleg nem lehet eseményeket kézbesíteni [privát végpontok](../private-link/private-endpoint-overview.md)használatával. Ez azt eredményezi, hogy nincs támogatás, ha szigorú hálózati elkülönítési követelmények vannak, amelyekben a továbbított események forgalma nem hagyhatja el a magánhálózati IP-területet. 

Ha azonban a követelmények biztonságos módon küldik el az eseményeket egy titkosított csatornán keresztül, és a küldő (ebben az esetben Event Grid) nyilvános IP-cím használatával történő küldésének ismert identitását, akkor az eseményeket az Azure Event Grid egyéni témaköre vagy egy, a rendszer által felügyelt identitással konfigurált tartomány használatával Event Hubs, Service Bus vagy Azure Storage szolgáltatásba kézbesítheti. Ezt követően használhat egy Azure Functions vagy a virtuális hálózaton üzembe helyezett webhookot az események lekéréséhez. Lásd a következő mintát: [Kapcsolódás privát végpontokhoz Azure functions használatával](/samples/azure-samples/azure-functions-private-endpoints/connect-to-private-endpoints-with-azure-functions/).

Ebben a konfigurációban a forgalom a nyilvános IP-cím/Internet Event Gridról Event Hubsra, Service Busra vagy az Azure Storage-ba kerül, de a csatorna titkosítható, és a Event Grid felügyelt identitása is használatos. Ha a virtuális hálózatra telepített Azure Functions vagy webhookot úgy konfigurálja, hogy Event Hubs, Service Bus vagy Azure Storage-t használ privát kapcsolaton keresztül, akkor a forgalom ezen szakasza nyilvánvalóan az Azure-ban marad.


## <a name="next-steps"></a>Következő lépések
A felügyelt identitások megismeréséhez tekintse meg a [Mi az Azure-erőforrások felügyelt identitásai](../active-directory/managed-identities-azure-resources/overview.md)című témakört.
