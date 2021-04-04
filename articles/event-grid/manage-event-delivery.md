---
title: Kézbesítetlen levelek és újrapróbálkozási házirendek – Azure Event Grid
description: Leírja, hogyan lehet testre szabni a Event Grid esemény-kézbesítési beállításait. Állítsa be a kézbesítetlen levél célját, és adja meg, hogy mennyi ideig próbálkozzon a kézbesítéssel.
ms.topic: conceptual
ms.date: 07/20/2020
ms.openlocfilehash: 7d8cd74ccfb77bcec45d06071a4f46fb2a640cf8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92460937"
---
# <a name="set-dead-letter-location-and-retry-policy"></a>A kézbesíthetetlen levelek helyének és az újrapróbálkozási szabályzat beállítása

Esemény-előfizetés létrehozásakor testreszabhatja az események kézbesítésének beállításait. Ebből a cikkből megtudhatja, hogyan állíthatja be a kézbesítetlen levelek helyét, és hogyan szabhatja testre az újrapróbálkozási beállításokat. További információ ezekről a funkciókról: [Event Grid üzenet kézbesítése és újrapróbálkozás](delivery-and-retry.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!NOTE]
> Az üzenetek kézbesítésével, az újrapróbálkozásokkal és a kézbesítetlen levelekkel kapcsolatos információkért tekintse meg a fogalmi cikket: [Event Grid üzenet kézbesítését, és próbálkozzon újra](delivery-and-retry.md).

## <a name="set-dead-letter-location"></a>Kézbesítetlen levelek helyének beállítása

A kézbesítetlen levelek helyének beállításához olyan tárolási fiókra van szükség, amely nem kézbesíthető végpontnak. A példák egy meglévő Storage-fiók erőforrás-AZONOSÍTÓját kapják meg. Létrehoznak egy olyan esemény-előfizetést, amely egy tárolót használ az adott Storage-fiókban a kézbesítetlen levelek végpontja számára.

> [!NOTE]
> - A cikkben szereplő parancsok futtatása előtt hozzon létre egy Storage-fiókot és egy BLOB-tárolót a tárolóban.
> - A Event Grid szolgáltatás blobokat hoz létre ebben a tárolóban. A Blobok nevei a Event Grid-előfizetés nevét fogják tartalmazni a nagybetűvel rendelkező összes betűvel. Ha például az előfizetés neve a My-blob-előfizetés, a kézbesítetlen levelek blobjának neve a saját BLOB-előfizetés (myblobcontainer/MY-BLOB-előfizetés/2019/8/8/5/111111111-1111-1111-1111-111111111111.json) lesz. Ez a viselkedés a különböző Azure-szolgáltatások közötti adatfeldolgozások elleni védelem.


### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
containername=testcontainer

topicid=$(az eventgrid topic show --name demoTopic -g gridResourceGroup --query id --output tsv)
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

A kézbesítetlen levelek kikapcsolásához futtassa újra a parancsot az esemény-előfizetés létrehozásához, de ne adjon meg értéket a következőhöz: `deadletter-endpoint` . Nem kell törölnie az esemény-előfizetést.

> [!NOTE]
> Ha az Azure CLI-t használja a helyi gépen, használja az Azure CLI 2.0.56 vagy újabb verzióját. Az Azure CLI legújabb verziójának telepítésével kapcsolatos utasításokért lásd: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$containername = "testcontainer"

$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id
$storageid = (Get-AzStorageAccount -ResourceGroupName gridResourceGroup -Name demostorage).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeadLetterEndpoint "$storageid/blobServices/default/containers/$containername"
```

A kézbesítetlen levelek kikapcsolásához futtassa újra a parancsot az esemény-előfizetés létrehozásához, de ne adjon meg értéket a következőhöz: `DeadLetterEndpoint` . Nem kell törölnie az esemény-előfizetést.

> [!NOTE]
> Ha az Azure bővítményén-t használja a helyi gépen, használja a Azure PowerShell 1.1.0-es vagy újabb verzióját. Töltse le és telepítse a legújabb Azure PowerShell az [Azure downloads](https://azure.microsoft.com/downloads/)szolgáltatásból.

## <a name="set-retry-policy"></a>Újrapróbálkozási szabályzat beállítása

Event Grid-előfizetés létrehozásakor megadhatja, hogy mennyi ideig Event Grid próbálkozzon az esemény kézbesítésével. Alapértelmezés szerint a Event Grid 24 órát (1440 percet) vagy 30 alkalommal próbál meg. Ezen értékek bármelyikét megadhatja az Event Grid-előfizetéshez. Az esemény élettartama értékének 1 és 1440 közötti egész számnak kell lennie. A maximális újrapróbálkozások értékének 1 és 30 közötti egész számnak kell lennie.

Nem lehet konfigurálni az [újrapróbálkozási ütemtervet](delivery-and-retry.md#retry-schedule-and-duration).

### <a name="azure-cli"></a>Azure CLI

Ha az esemény élettartamát 1440 perctől eltérő értékre szeretné beállítani, használja a következőt:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --event-ttl 720
```

Ha a maximális újrapróbálkozást a 30-tól eltérő értékre szeretné beállítani, használja a következőt:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

> [!NOTE]
> Ha a és a lehetőséget is beállítja `event-ttl` `max-deliver-attempts` , Event Grid az elsővel jár le, hogy meghatározza, mikor kell leállítani az események kézbesítését. Ha például 30 percet állít be az élettartam (TTL) és 10 maximális kézbesítési kísérlet esetén. Ha a rendszer 30 perc (vagy) elteltével nem küldi el az eseményt 10 próbálkozás után, attól függően, hogy az esemény megtörténik-e, a rendszer kézbesíti az eseményt.  

### <a name="powershell"></a>PowerShell

Ha az esemény élettartamát 1440 perctől eltérő értékre szeretné beállítani, használja a következőt:

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -EventTtl 720
```

Ha a maximális újrapróbálkozást a 30-tól eltérő értékre szeretné beállítani, használja a következőt:

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -MaxDeliveryAttempt 18
```

> [!NOTE]
> Ha a és a lehetőséget is beállítja `event-ttl` `max-deliver-attempts` , Event Grid az elsővel jár le, hogy meghatározza, mikor kell leállítani az események kézbesítését. Ha például 30 percet állít be az élettartam (TTL) és 10 maximális kézbesítési kísérlet esetén. Ha a rendszer 30 perc (vagy) elteltével nem küldi el az eseményt 10 próbálkozás után, attól függően, hogy az esemény megtörténik-e, a rendszer kézbesíti az eseményt.  

## <a name="next-steps"></a>Következő lépések

* A kézbesítetlen levelek eseményeinek feldolgozására szolgáló Azure Function alkalmazást használó minta alkalmazáshoz lásd: [Azure Event Grid a .net-hez készült kézbesítetlen levelek mintáit](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/).
* További információ az események kézbesítéséről és újrapróbálkozásáról, [Event Grid az üzenetek kézbesítéséről, és próbálkozzon újra](delivery-and-retry.md).
* Az Event Grid ismertetése: [Az Event Grid bemutatása](overview.md).
* Az Event Grid használatának gyors megkezdéséhez tekintse meg [az egyéni események létrehozása és irányítása Azure Event Grid](custom-event-quickstart.md)használatával című témakört.
