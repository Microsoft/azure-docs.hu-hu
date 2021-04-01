---
title: Események közzététele esemény-tartományokkal Azure Event Grid
description: Bemutatja, hogyan kezelheti a témakörök nagy csoportjait a Azure Event Gridban, és hogyan teheti közzé az eseményeket az esemény-tartományok használatával.
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: e6861e89def10eec391bf302b1ddc726b38bb98c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97109895"
---
# <a name="manage-topics-and-publish-events-using-event-domains"></a>Témakörök kezelése és események közzététele az Event Domain használatával

Ez a cikk a következőket mutatja be:

* Event Grid tartomány létrehozása
* Előfizetés az Event Grid-témakörökre
* Kulcsok listázása
* Események közzététele tartományba

Az események tartományával kapcsolatos további tudnivalókért lásd: [az események tartományának megismerése Event Grid témakörök kezeléséhez](event-domains.md).

## <a name="create-an-event-domain"></a>Esemény tartományának létrehozása

A nagyméretű témakörök kezeléséhez hozzon létre egy Event tartományt.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

```azurecli-interactive
az eventgrid domain create \
  -g <my-resource-group> \
  --name <my-domain-name> \
  -l <location>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)
```azurepowershell-interactive
New-AzEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain-name> `
  -Location <location>
```
---

A sikeres létrehozás a következő értékeket adja vissza:

```json
{
  "endpoint": "https://<my-domain-name>.westus2-1.eventgrid.azure.net/api/events",
  "id": "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>",
  "inputSchema": "EventGridSchema",
  "inputSchemaMapping": null,
  "location": "westus2",
  "name": "<my-domain-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "<my-resource-group>",
  "tags": null,
  "type": "Microsoft.EventGrid/domains"
}
```

Vegye figyelembe, `endpoint` hogy a és a szükséges a `id` tartomány kezeléséhez és az események közzétételéhez.

## <a name="manage-access-to-topics"></a>Témakörökhöz való hozzáférés kezelése

A témakörökhöz való hozzáférés kezelése [szerepkör-hozzárendelés](../role-based-access-control/role-assignments-cli.md)használatával történik. A szerepkör-hozzárendelés az Azure szerepköralapú hozzáférés-vezérlés használatával korlátozza az Azure-erőforrásokon végrehajtott műveleteket egy bizonyos hatókörön belüli jogosult felhasználók számára.

A Event Grid két beépített szerepkörrel rendelkezik, amelyek segítségével adott felhasználókhoz rendelhet hozzá különböző témaköröket a tartományon belül. Ezek a szerepkörök `EventGrid EventSubscription Contributor (Preview)` , amelyek lehetővé teszik az előfizetések létrehozását és törlését, és `EventGrid EventSubscription Reader (Preview)` amelyek csak az esemény-előfizetések listázását teszik lehetővé.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)
Az alábbi Azure CLI-parancs `alice@contoso.com` csak a következő témakörben korlátozza az esemény-előfizetések létrehozását és törlését `demotopic1` :

```azurecli-interactive
az role assignment create \
  --assignee alice@contoso.com \
  --role "EventGrid EventSubscription Contributor (Preview)" \
  --scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)
Az alábbi PowerShell-parancs korlátozza az `alice@contoso.com` esemény-előfizetések létrehozását és törlését csak a következő témakörben `demotopic1` :

```azurepowershell-interactive
New-AzRoleAssignment `
  -SignInName alice@contoso.com `
  -RoleDefinitionName "EventGrid EventSubscription Contributor (Preview)" `
  -Scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```
---

A Event Grid műveletekhez való hozzáférés kezelésével kapcsolatos további információkért lásd: [Event Grid biztonság és hitelesítés](./security-authentication.md).

## <a name="create-topics-and-subscriptions"></a>Témakörök és előfizetések létrehozása

A Event Grid szolgáltatás automatikusan hozza létre és kezeli a megfelelő témakört egy tartományban a tartományhoz tartozó esemény-előfizetés létrehozási hívása alapján. Nincs külön lépés egy témakör létrehozásához egy tartományban. Hasonlóképpen, amikor egy témakör utolsó esemény-előfizetését törlik, a témakör is törlődik.

Egy tartományban lévő témakörre való feliratkozás megegyeznek a többi Azure-erőforrásra való feliratkozással. A forrás erőforrás-AZONOSÍTÓnál adja meg a tartomány korábbi létrehozásakor visszaadott esemény-tartományi azonosítót. Az előfizetni kívánt témakör megadásához a `/topics/<my-topic>` forrás erőforrás-azonosító végéhez adja hozzá a következőt:. Ha olyan tartományi hatókörbeli esemény-előfizetést szeretne létrehozni, amely a tartományban lévő összes eseményt fogadja, a témakörök meghatározása nélkül adhatja meg az esemény tartomány-AZONOSÍTÓját.

Az előző szakaszban megadott hozzáférési jogosultsággal rendelkező felhasználó általában létrehozza az előfizetést. A cikk egyszerűsítése érdekében hozza létre az előfizetést. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

```azurecli-interactive
az eventgrid event-subscription create \
  --name <event-subscription> \
  --source-resource-id "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" \
  --endpoint https://contoso.azurewebsites.net/api/updates
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurepowershell-interactive
New-AzEventGridSubscription `
  -ResourceId "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" `
  -EventSubscriptionName <event-subscription> `
  -Endpoint https://contoso.azurewebsites.net/api/updates
```

---

Ha tesztelési végpontra van szüksége az események előfizetéséhez, bármikor üzembe helyezhet egy [előre elkészített webalkalmazást](https://github.com/Azure-Samples/azure-event-grid-viewer) , amely megjeleníti a bejövő eseményeket. Az eseményeket elküldheti a teszt webhelyére a következő címen: `https://<your-site-name>.azurewebsites.net/api/updates` .

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"  alt="Button to Deploy to Aquent." /></a>

A témakörhöz beállított engedélyek Azure Active Directory tárolódnak, és explicit módon törölni kell őket. Egy esemény-előfizetés törlése nem vonja vissza a felhasználók hozzáférését az esemény-előfizetések létrehozásához, ha egy témakörben írási hozzáféréssel rendelkeznek.


## <a name="publish-events-to-an-event-grid-domain"></a>Események közzététele egy Event Grid tartományban

Az események tartományba való közzététele ugyanaz, mint a [Közzététel egy egyéni témakörben](./post-to-custom-topic.md). Az egyéni témakörre való közzététel helyett azonban az összes eseményt közzé kell tenni a tartományi végponton. A JSON-események adatkészletében meg kell adnia azt a témakört, amelyre az eseményeket el szeretné jutni. A következő események a témakört eredményezik, ha az `"id": "1111"` `demotopic1` eseményt a `"id": "2222"` témakörbe szeretné elküldeni `demotopic2` :

```json
[{
  "topic": "demotopic1",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "demotopic2",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)
A tartomány végpontjának Azure CLI-vel való beszerzéséhez használja a következőt

```azurecli-interactive
az eventgrid domain show \
  -g <my-resource-group> \
  -n <my-domain>
```

Egy tartomány kulcsainak beszerzéséhez használja a következőt:

```azurecli-interactive
az eventgrid domain key list \
  -g <my-resource-group> \
  -n <my-domain>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)
A tartomány végpontjának PowerShell-lel való beszerzéséhez használja a következőt

```azurepowershell-interactive
Get-AzEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```

Egy tartomány kulcsainak beszerzéséhez használja a következőt:

```azurepowershell-interactive
Get-AzEventGridDomainKey `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```
---

Ezt követően pedig kedvenc metódusával teheti közzé az eseményeket a Event Grid tartományában.

## <a name="search-lists-of-topics-or-subscriptions"></a>Témakörök vagy előfizetések keresési listája

Nagy számú témakör vagy előfizetés kereséséhez és kezeléséhez Event Grid API-jai támogatják a listázást és a tördelést.

### <a name="using-cli"></a>A parancssori felület használata
A következő parancs például felsorolja az összes olyan témakört, amely tartalmazza a nevét `mytopic` . 

```azurecli-interactive
az eventgrid topic list --odata-query "contains(name, 'mytopic')"
```

További információ erről a parancsról: [`az eventgrid topic list`](/cli/azure/eventgrid/topic?#az_eventgrid_topic_list) . 


## <a name="next-steps"></a>Következő lépések

* További információ az Event-tartományok magas szintű fogalmakról és azok hasznos okairól: az esemény- [tartományok fogalmi áttekintése](event-domains.md).
