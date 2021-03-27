---
title: Felügyelt identitás hozzáadása egy szerepkörhöz Azure Event Grid célhelyen
description: Ez a cikk azt ismerteti, hogyan adhat hozzá felügyelt identitást Azure-szerepkörökhöz olyan célhelyeken, mint például a Azure Service Bus és az Azure Event Hubs.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 1bcef878c982122d80980dd7194fae2de6fc8762
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630384"
---
# <a name="add-an-identity-to-azure-roles-on-azure-event-grid-destinations"></a>Identitás hozzáadása az Azure-szerepkörökhöz Azure Event Grid célhelyeken
Ez a szakasz azt ismerteti, hogyan adható hozzá a rendszertémakör, az egyéni témakör vagy a tartomány identitása egy Azure-szerepkörhöz. 

## <a name="prerequisites"></a>Előfeltételek
Rendeljen hozzá egy rendszerhez rendelt felügyelt identitást a következő cikkek utasításai alapján:

- [Egyéni témakörök vagy tartományok](enable-identity-custom-topics-domains.md)
- [Rendszerrel kapcsolatos témakörök](enable-identity-system-topics.md)

## <a name="supported-destinations-and-azure-roles"></a>Támogatott célhelyek és Azure-szerepkörök
Az egyéni Event Grid-témakör vagy-tartomány identitásának engedélyezése után az Azure automatikusan létrehoz egy identitást Azure Active Directory. Adja hozzá ezt az identitást a megfelelő Azure-szerepkörökhöz, hogy az egyéni témakör vagy tartomány továbbítsa az eseményeket a támogatott célhelyekre. Adja hozzá például az identitást az **azure Event Hubs Adatfeladói** szerepkörhöz egy Azure Event Hubs-névtérhez, hogy az Event Grid egyéni témaköre az eseményeket az adott névtérben lévő Event hubokba továbbítsa. 

Az Azure Event Grid jelenleg a rendszer által hozzárendelt felügyelt identitással konfigurált egyéni témákat vagy tartományokat támogatja az események továbbításához a következő célhelyekre. Ez a táblázat az identitáshoz tartozó szerepköröket is megadja, hogy az egyéni témakör továbbítsa az eseményeket.

| Cél | Azure-szerepkör | 
| ----------- | --------- | 
| Várólisták és témakörök Service Bus | [Adatfeladó Azure Service Bus](../service-bus-messaging/authenticate-application.md#azure-built-in-roles-for-azure-service-bus) |
| Azure Event Hubs | [Azure Event Hubs adatfeladó](../event-hubs/authorize-access-azure-active-directory.md#azure-built-in-roles-for-azure-event-hubs) | 
| Azure Blob Storage | [Storage-blobadatok közreműködője](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) |
| Azure Queue Storage |[Tárolási várólista adatüzenetének küldője](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) | 


## <a name="use-the-azure-portal"></a>Az Azure Portal használata
A Azure Portal használatával az egyéni témakört vagy a tartományi identitást hozzárendelheti egy megfelelő szerepkörhöz, hogy az egyéni témakör vagy tartomány továbbítsa az eseményeket a célhelyre. 

Az alábbi példa egy **msitesttopic** nevű Event Grid egyéni témakörhöz tartozó felügyelt identitást hoz létre egy olyan Service Bus névtérhez, amely üzenetsor-vagy témakör-erőforrást tartalmaz **Azure Service Bus adatfeladói** szerepkörhöz. Ha a szerepkört a névtér szintjén adja hozzá, az Event Grid egyéni témaköre a névtérben lévő összes entitáshoz továbbíthatja az eseményeket. 

1. Nyissa meg a **Service Bus névteret** a [Azure Portalban](https://portal.azure.com). 
1. A bal oldali ablaktáblán válassza a **Access Control** lehetőséget. 
1. A **szerepkör-hozzárendelés hozzáadása** szakaszban válassza a **Hozzáadás** lehetőséget. 
1. A **szerepkör-hozzárendelés hozzáadása** oldalon hajtsa végre a következő lépéseket:
    1. Válassza ki a szerepkört. Ebben az esetben **Azure Service Bus az adatfeladó**. 
    1. Válassza ki az Event Grid egyéni témakörének vagy tartományának **identitását** . 
    1. A konfiguráció mentéséhez kattintson a **Mentés** gombra.

A lépések hasonlóak az identitásnak a táblázatban említett más szerepkörökhöz való hozzáadásához. 

## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata
Az ebben a szakaszban szereplő példa bemutatja, hogyan adhat identitást Azure-szerepkörhöz az Azure CLI használatával. A minta parancsok az Event Grid egyéni témaköreire vonatkoznak. Az Event Grid-tartományok parancsai hasonlóak. 

### <a name="get-the-principal-id-for-the-custom-topics-system-identity"></a>Az egyéni témakör rendszeridentitásának elsődleges AZONOSÍTÓjának beolvasása 
Először kérje le az egyéni témakör rendszerfelügyelt identitásának elsődleges AZONOSÍTÓját, és rendelje hozzá az identitást a megfelelő szerepkörökhöz.

```azurecli-interactive
topic_pid=$(az ad sp list --display-name "$<TOPIC NAME>" --query [].objectId -o tsv)
```

### <a name="create-a-role-assignment-for-event-hubs-at-various-scopes"></a>Szerepkör-hozzárendelés létrehozása az Event hubokhoz különböző hatókörökben 
Az alábbi CLI-példa azt szemlélteti, hogyan adhat hozzá egyéni témakör identitását az **Azure Event Hubs Adatfeladói** szerepkörhöz a névtér vagy az Event hub szintjén. Ha a szerepkör-hozzárendelést a névtér szintjén hozza létre, az egyéni témakör az adott névtérben lévő összes esemény-hubhoz továbbítja az eseményeket. Ha az Event hub szintjén hoz létre szerepkör-hozzárendelést, akkor az egyéni témakör csak az adott Event hub-ra továbbíthatja az eseményeket. 


```azurecli-interactive
role="Azure Event Hubs Data Sender" 
namespaceresourceid=$(az eventhubs namespace show -n $<EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 
eventhubresourceid=$(az eventhubs eventhub show -n <EVENT HUB NAME> --namespace-name <EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one event hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$eventhubresourceid" 
```

### <a name="create-a-role-assignment-for-a-service-bus-topic-at-various-scopes"></a>Szerepkör-hozzárendelés létrehozása Service Bus témakörhöz különböző hatókörökben 
Az alábbi CLI-példa azt szemlélteti, hogyan adhat hozzá egy Event Grid egyéni témakör identitását a **Azure Service Bus Adatfeladói** szerepkörhöz a névtér szintjén vagy a Service Bus témakör szintjén. Ha a szerepkör-hozzárendelést a névtér szintjén hozza létre, az Event Grid-témakör a névtérben lévő összes entitásra (Service Bus várólistákra vagy témakörökre) továbbíthatja az eseményeket. Ha a Service Bus üzenetsor vagy a témakör szintjén hoz létre szerepkör-hozzárendelést, akkor az Event Grid egyéni témaköre csak az adott Service Bus-várólistára vagy-témakörre továbbíthatja az eseményeket. 

```azurecli-interactive
role="Azure Service Bus Data Sender" 
namespaceresourceid=$(az servicebus namespace show -n $RG\SB -g "$RG" --query "{I:id}" -o tsv 
sbustopicresourceid=$(az servicebus topic show -n topic1 --namespace-name $RG\SB -g "$RG" --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$sbustopicresourceid" 
```

## <a name="next-steps"></a>Következő lépések
Most, hogy hozzárendelt egy rendszerhez rendelt identitást a rendszertémakörhöz, az egyéni témakörhöz vagy a tartományhoz, és hozzáadta az identitást a megfelelő szerepkörökhöz a célhelyeken, tekintse meg a [Devlier-események identitás használatával](managed-service-identity.md) történő kézbesítése az identitás segítségével című témakört.


