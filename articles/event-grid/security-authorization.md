---
title: Biztonság és hitelesítés Azure Event Grid
description: Az Azure Event Gridet és a vele kapcsolatos fogalmakat ismerteti.
ms.topic: conceptual
ms.date: 02/12/2021
ms.openlocfilehash: e9bcf00e832e4deaaf9c5f81ba5af51609a1c412
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104601040"
---
# <a name="authorizing-access-to-event-grid-resources"></a>Event Grid erőforrásokhoz való hozzáférés engedélyezése
Azure Event Grid lehetővé teszi a különböző felhasználók számára megadott hozzáférési szint szabályozását különböző **felügyeleti műveletek** , például esemény-előfizetések listázása, új adatbázisok létrehozása és kulcsok létrehozása céljából. Event Grid Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) használ.

> [!NOTE]
> A EventGrid nem támogatja az Azure RBAC számára az események közzétételét Event Grid témakörökben vagy tartományokban. Használjon közös hozzáférésű aláírás (SAS) kulcsot vagy jogkivonatot az eseményeket közzétevő ügyfelek hitelesítéséhez. További információ: [közzétételi ügyfelek hitelesítése](security-authenticate-publishing-clients.md). 

## <a name="operation-types"></a>Működési típusok
A Azure Event Grid által támogatott műveletek listáját az alábbi Azure CLI-paranccsal futtathatja: 

```azurecli-interactive
az provider operation show --namespace Microsoft.EventGrid
```

A következő műveletek egy potenciálisan titkos információt adnak vissza, amely kiszűri a normál olvasási műveletekből. Javasoljuk, hogy korlátozza a hozzáférést ezekhez a műveletekhez. 

* Microsoft. EventGrid/eventSubscriptions/getFullUrl/művelet
* Microsoft. EventGrid/témakörök/Listkeys műveletének beolvasása/művelet
* Microsoft. EventGrid/témakörök/regenerateKey/művelet


## <a name="built-in-roles"></a>Beépített szerepkörök
Event Grid a következő három beépített szerepkört biztosítja. 

A Event Grid előfizetés-olvasó és Event Grid előfizetés közreműködői szerepkörei az esemény-előfizetések kezeléséhez szükségesek. Fontosak az esemény- [tartományok](event-domains.md) megvalósításakor, mert a felhasználóknak az adott esemény tartományában lévő témakörökre való előfizetéshez szükséges engedélyeket adják meg. Ezek a szerepkörök az esemény-előfizetésekre összpontosítanak, és nem biztosítanak hozzáférést olyan műveletekhez, mint például a témakörök létrehozása.

A Event Grid közreműködő szerepkör lehetővé teszi Event Grid-erőforrások létrehozását és kezelését. 


| Szerepkör | Leírás |
| ---- | ----------- | 
| [Event Grid előfizetés-olvasó](../role-based-access-control/built-in-roles.md#eventgrid-eventsubscription-reader) | Lehetővé teszi Event Grid esemény-előfizetési műveletek kezelését. |
| [Event Grid előfizetés közreműködői](../role-based-access-control/built-in-roles.md#eventgrid-eventsubscription-contributor) | Lehetővé teszi Event Grid esemény-előfizetések olvasását. |
| [Event Grid közreműködő](../role-based-access-control/built-in-roles.md#eventgrid-contributor) | Event Grid erőforrások létrehozását és kezelését teszi lehetővé. |


> [!NOTE]
> Válassza az első oszlopban található hivatkozások elemet egy olyan cikkhez, amely további részleteket tartalmaz a szerepkörről. A felhasználók vagy csoportok RBAC-szerepkörökhöz való hozzárendelésével kapcsolatos utasításokért tekintse meg [ezt a cikket](../role-based-access-control/quickstart-assign-role-user-portal.md).


## <a name="custom-roles"></a>Egyéni szerepkörök

Ha a beépített szerepköröktől eltérő engedélyeket kell megadnia, létrehozhat egyéni szerepköröket.

A következő példa olyan Event Grid szerepkör-definíciókat mutat be, amelyek lehetővé teszik a felhasználók számára a különböző műveletek elvégzését. Ezek az egyéni szerepkörök különböznek a beépített szerepköröktől, mert szélesebb körű hozzáférést biztosítanak, mint a csak az esemény-előfizetések.

**EventGridReadOnlyRole.jsbekapcsolva**: csak olvasási műveletek engedélyezése.

```json
{
  "Name": "Event grid read only role",
  "Id": "7C0B6B59-A278-4B62-BA19-411B70753856",
  "IsCustom": true,
  "Description": "Event grid read only role",
  "Actions": [
    "Microsoft.EventGrid/*/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription Id>"
  ]
}
```

**EventGridNoDeleteListKeysRole.jsbekapcsolva: a** korlátozott post műveletek engedélyezése, de a törlési műveletek letiltása.

```json
{
  "Name": "Event grid No Delete Listkeys role",
  "Id": "B9170838-5F9D-4103-A1DE-60496F7C9174",
  "IsCustom": true,
  "Description": "Event grid No Delete Listkeys role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action"
  ],
  "NotActions": [
    "Microsoft.EventGrid/*/delete"
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

**EventGridContributorRole.json**: engedélyezi az összes Event Grid-műveletet.

```json
{
  "Name": "Event grid contributor role",
  "Id": "4BA6FB33-2955-491B-A74F-53C9126C9514",
  "IsCustom": true,
  "Description": "Event grid contributor role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/*/delete",
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

Egyéni szerepköröket a [PowerShell](../role-based-access-control/custom-roles-powershell.md), az [Azure CLI](../role-based-access-control/custom-roles-cli.md)és a [Rest](../role-based-access-control/custom-roles-rest.md)használatával hozhat létre.



### <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban

A Event Grid szolgáltatás által lemezre írt összes eseményt vagy adatfájlt egy Microsoft által felügyelt kulcs titkosítja, amely biztosítja, hogy az inaktív állapotban legyen titkosítva. Emellett az események vagy az adatmegőrzési időszak maximális időtartama 24 óra az [Event Grid újrapróbálkozási házirend](delivery-and-retry.md)betartásával. A Event Grid 24 óra elteltével automatikusan törli az összes eseményt vagy az adatmennyiséget, vagy az esemény élettartama, attól függően, hogy melyik a kisebb.

## <a name="permissions-for-event-subscriptions"></a>Esemény-előfizetések engedélyei
Ha olyan eseménykezelőt használ, amely nem webhook (például egy Event hub vagy várólista-tároló), írási hozzáféréssel kell rendelkeznie az adott erőforráshoz. Ez az engedély-ellenőrzési funkció megakadályozza, hogy jogosulatlan felhasználó küldjön eseményeket az erőforrásba.

Az eseményforrás erőforrásának a **Microsoft. EventGrid/EventSubscriptions/Write** engedéllyel kell rendelkeznie. Erre az engedélyre azért van szükség, mert az erőforrás hatókörében új előfizetést ír. A szükséges erőforrás különbözik attól függően, hogy egy rendszertémakörre vagy egy egyéni témakörre való feliratkozásra van-e szükség. Ebben a szakaszban mindkét típust ismertetjük.

### <a name="system-topics-azure-service-publishers"></a>Rendszertémakörök (Azure-szolgáltatás közzétevői)
A rendszertémakörök esetében, ha Ön nem a forrás erőforrás tulajdonosa vagy közreműködője, engedélyre van szüksége ahhoz, hogy új esemény-előfizetést írjon az eseményt közzétevő erőforrás hatókörén. Az erőforrás formátuma: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Ha például egy **myacct** nevű Storage-fiókra szeretne előfizetni egy eseményre, szüksége lesz a Microsoft. EventGrid/EventSubscriptions/Write engedélyre a következőn: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Egyéni témakörök
Egyéni témakörök esetén engedély szükséges ahhoz, hogy új esemény-előfizetést írjon az Event Grid-témakör hatókörébe. Az erőforrás formátuma: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Ha például egy **mytopic** nevű egyéni témakörre szeretne előfizetni, szüksége lesz a Microsoft. EventGrid/EventSubscriptions/Write engedélyre a következőn: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`



## <a name="next-steps"></a>Következő lépések

* A Event Grid bevezetését lásd: [About Event Grid](overview.md)
