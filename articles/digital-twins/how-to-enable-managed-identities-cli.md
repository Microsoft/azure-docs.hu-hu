---
title: Felügyelt identitás engedélyezése útválasztási eseményekhez (előzetes verzió) – parancssori felület
titleSuffix: Azure Digital Twins
description: Megtudhatja, hogyan engedélyezheti a rendszer által hozzárendelt identitást az Azure digitális ikrek számára, és hogyan továbbíthatja az eseményeket az Azure CLI használatával.
author: baanders
ms.author: baanders
ms.date: 02/09/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: c9ce87584373bd87a8f89ecb4ea692b44d3fab4d
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102202960"
---
# <a name="enable-a-managed-identity-for-routing-azure-digital-twins-events-preview-azure-cli"></a>Felügyelt identitás engedélyezése az Azure Digital Twins-események útválasztásához (előzetes verzió): Azure CLI

[!INCLUDE [digital-twins-managed-service-identity-selector.md](../../includes/digital-twins-managed-service-identity-selector.md)]

Ez a cikk azt ismerteti, hogyan engedélyezhető a [rendszer által hozzárendelt identitás egy Azure Digital Twins-példányhoz](concepts-security.md#managed-identity-for-accessing-other-resources-preview) (jelenleg előzetes verzióban), és hogyan használható az identitás az események olyan támogatott célhelyekre való továbbításakor, mint az [Event Hub](../event-hubs/event-hubs-about.md), a [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)   Destinations és az [Azure Storage tároló](../storage/blobs/storage-blobs-introduction.md).

Ez a cikk végigvezeti a folyamaton az [**Azure CLI**](/cli/azure/what-is-azure-cli)használatával.

A cikk a következő lépésekkel foglalkozik: 

1. Hozzon létre egy Azure Digital Twins-példányt egy rendszer által hozzárendelt identitással, vagy engedélyezze a rendszer által hozzárendelt identitást egy meglévő Azure Digital Twins-példányon. 
1. Adja hozzá a megfelelő szerepkört vagy szerepköröket az identitáshoz. Rendelje hozzá például az **Azure Event hub Adatfeladói** szerepkört az identitáshoz, ha a végpont az Event hub, vagy ha a végpont Service Bus, **Azure Service Bus adatfeladói szerepkört** .
1. Hozzon létre egy végpontot az Azure Digital Ikrekben, amely képes a rendszerhez rendelt identitások használatára a hitelesítéshez.

## <a name="enable-system-managed-identities-for-an-instance"></a>Rendszer által felügyelt identitások engedélyezése egy példányhoz 

Ha engedélyezi a rendszer által hozzárendelt identitást az Azure Digital Twins-példányon, az Azure automatikusan létrehoz egy identitást a [Azure Active Directory (Azure ad)](../active-directory/fundamentals/active-directory-whatis.md)szolgáltatásban. Ezt az identitást ezután az Azure Digital Twins-végpontok hitelesítésére használhatja az események továbbításához.

**A példány kezdeti beállításának részeként** engedélyezheti a rendszer által felügyelt identitásokat egy Azure digitális Twins-példányhoz, vagy **később is engedélyezheti azt egy olyan példányon, amely már létezik**.

Ezen létrehozási módszerek egyike ugyanazokat a konfigurációs beállításokat és a példányhoz tartozó végeredményt adja meg. Ez a szakasz a mindkettőt ismerteti.

### <a name="add-a-system-managed-identity-during-instance-creation"></a>Rendszer által felügyelt identitás hozzáadása a példányok létrehozása során

Ebből a szakaszból megtudhatja, hogyan engedélyezheti a rendszer által felügyelt identitásokat egy jelenleg létrehozott Azure digitális Twins-példányon. 

Ezt úgy teheti meg, hogy hozzáad egy `--assign-identity` paramétert a `az dt create` példány létrehozásához használt parancshoz. (A paranccsal kapcsolatos további információkért tekintse meg a [hivatkozási dokumentációt](/cli/azure/ext/azure-iot/dt#ext_azure_iot_az_dt_create) , vagy az [Azure Digital Twins-példány beállításának általános utasításait](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance).)

Rendszerfelügyelt identitással rendelkező példány létrehozásához adja hozzá a  `--assign-identity` következőhöz hasonló paramétert:

```azurecli-interactive
az dt create -n {new_instance_name} -g {resource_group} --assign-identity
```

### <a name="add-a-system-managed-identity-to-an-existing-instance"></a>Rendszer által felügyelt identitás hozzáadása meglévő példányhoz

Ebben a szakaszban egy rendszer által felügyelt identitást ad hozzá egy már létező Azure digitális Twins-példányhoz.

Ezt a `az dt create` parancsot és a paramétert is végrehajtja `--assign-identity` . A létrehozandó példány új nevének megadása helyett megadhatja egy olyan példány nevét, amely már létezik az adott példány értékének frissítéséhez `--assign-identity` .

A felügyelt identitás **engedélyezésére** szolgáló parancs ugyanaz, mint a rendszer által felügyelt identitással rendelkező példány létrehozásához szükséges parancs. Az összes változás a példánynév paraméter értéke:

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --assign-identity
```

Ha egy olyan példányon szeretné **letiltani** a felügyelt identitást, ahol a szolgáltatás jelenleg engedélyezve van, az alábbi hasonló paranccsal állíthatja be a következőt: `--assign-identity` `false` .

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --assign-identity false
```

## <a name="assign-azure-roles-to-the-identity"></a>Azure-szerepkörök kiosztása az identitáshoz 

Miután létrehozta a rendszer által hozzárendelt identitást az Azure Digital Twins-példányhoz, hozzá kell rendelnie a megfelelő szerepköröket a különböző típusú [végpontok](concepts-route-events.md) hitelesítéséhez az események támogatott célhelyekre való továbbításához. Ez a szakasz a szerepkör-beállításokat és azok hozzárendelését ismerteti a rendszer által hozzárendelt identitáshoz.

>[!NOTE]
> Ez egy fontos lépés – anélkül, hogy az identitás nem fog tudni hozzáférni a végpontokhoz, és az események nem lesznek leküldve.

### <a name="supported-destinations-and-azure-roles"></a>Támogatott célhelyek és Azure-szerepkörök 

Itt láthatók azok a minimális szerepkörök, amelyeknek az identitásnak hozzá kell férnie egy végponthoz a célhely típusától függően. A magasabb szintű engedélyekkel rendelkező szerepkörök (például az adattulajdonosi szerepkörök) is működni fognak.

| Cél | Azure-szerepkör |
| --- | --- |
| Azure Event Hubs | Azure Event Hubs adatfeladó |
| Azure Service Bus | Adatfeladó Azure Service Bus |
| Azure Storage-tároló | Storage-blobadatok közreműködője |

További információ a végpontokról, az útvonalakról és az Azure digitális Ikrekben az útválasztáshoz támogatott célhelyekről: [*fogalmak: Event Routes*](concepts-route-events.md).

### <a name="assign-the-role"></a>A szerepkör kiosztása

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

A `--scopes` paramétert hozzáadhatja a `az dt create` parancshoz, hogy az identitást egy adott szerepkörrel rendelkező egy vagy több hatókörhöz rendelje. Ezt a példány első létrehozásakor, vagy később, a már létező példányok nevének átadásával lehet használni.

Íme egy példa, amely egy rendszerfelügyelt identitású példányt hoz létre, és hozzárendeli az identitást egy Event hub nevű egyéni szerepkörhöz `MyCustomRole` .

```azurecli-interactive
az dt create -n {instance_name} -g {resource_group} --assign-identity --scopes "/subscriptions/<subscription ID>/resourceGroups/<resource_group>/providers/Microsoft.EventHub/namespaces/<Event_Hubs_namespace>/eventhubs/<event_hub_name>" --role MyCustomRole
```

A paranccsal kapcsolatos további példákért tekintse meg az az [ **DT Create** Reference dokumentációt](/cli/azure/ext/azure-iot/dt#ext_azure_iot_az_dt_create).

Azt is megteheti, hogy szerepkörök létrehozására és kezelésére az az [**role hozzárendelési**](/cli/azure/role/assignment) parancssori csoportot is használja. Ez olyan további forgatókönyvek támogatásához használható, amelyekben nem kívánja csoportosítani a szerepkör-hozzárendelést a Create paranccsal.

## <a name="create-an-endpoint-with-identity-based-authentication"></a>Végpont létrehozása identitás-alapú hitelesítéssel

Miután beállította a rendszer által felügyelt identitást az Azure Digital Twins-példányhoz, és hozzárendeli a megfelelő szerepkör (eke) t, létrehozhat olyan Azure digitális Twins- [végpontokat](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins) , amelyek képesek az identitás hitelesítésre való használatára. Ez a beállítás csak az Event hub és a Service Bus típusú végpontok esetében érhető el (Event Grid esetén nem támogatott).

>[!NOTE]
> Az identitás-alapú hitelesítésre való váltáshoz olyan végpontot nem lehet szerkeszteni, amely a kulcs alapú identitással már létrejött. A végpont első létrehozásakor ki kell választania a hitelesítési típust.

Ezt úgy teheti meg, hogy hozzáad egy `--auth-type` paramétert a `az dt endpoint create` végpont létrehozásához használt parancshoz. (A paranccsal kapcsolatos további információkért tekintse meg a [dokumentációt](/cli/azure/ext/azure-iot/dt/endpoint/create) , illetve az [Azure Digital Twins-végpont beállításával kapcsolatos általános útmutatást](how-to-manage-routes-apis-cli.md#create-the-endpoint)).

Ha identitás-alapú hitelesítést használó végpontot szeretne létrehozni, adja meg a `IdentityBased` hitelesítési típust a  `--auth-type` paraméterrel. Az alábbi példa egy Event Hubs végpontra mutat.

```azurecli-interactive
az dt endpoint create eventhub --endpoint-name {endpoint_name} --eventhub-resource-group {eventhub_resource_group} --eventhub-namespace {eventhub_namespace} --eventhub {eventhub_name} --auth-type IdentityBased -n {instance_name}
```

## <a name="considerations-for-disabling-system-managed-identities"></a>A rendszer által felügyelt identitások letiltásával kapcsolatos megfontolások

Mivel az identitást az azt használó végpontok külön kezelik, fontos figyelembe venni, hogy az identitás vagy szerepköre változásai milyen hatással lehetnek az Azure Digital Twins-példányban található végpontokra. Ha az identitás le van tiltva, vagy egy végponthoz szükséges szerepkört távolít el belőle, a végpont elérhetetlenné válhat, és a rendszer megszakítja az események folyamatát.

A már letiltott felügyelt identitással beállított végpontok használatának folytatásához törölnie kell a végpontot, és [újra létre kell hoznia](how-to-manage-routes-apis-cli.md#create-an-endpoint-for-azure-digital-twins) egy másik hitelesítési típussal. Akár egy órát is igénybe vehet, hogy az események a változás után folytassanak kézbesítést a végpontnak.

## <a name="next-steps"></a>Következő lépések

További információ a felügyelt identitásokról az Azure AD-ben: 
* [*Azure-erőforrások felügyelt identitásai*](../active-directory/managed-identities-azure-resources/overview.md)