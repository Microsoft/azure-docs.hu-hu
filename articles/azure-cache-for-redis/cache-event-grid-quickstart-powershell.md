---
title: 'Gyors útmutató: az Azure cache átirányítása a Redis-események webes végponthoz a PowerShell használatával'
description: A Azure Event Grid használatával előfizethet az Azure cache-re az Redis-események esetében, elküldheti az eseményeket egy webhookba, és kezelheti az eseményeket egy webalkalmazásban.
ms.date: 1/5/2021
author: curib
ms.author: cauribeg
ms.topic: quickstart
ms.service: cache
ms.openlocfilehash: 6c3b433a8e433f39b723a7155bb6de116857efca
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508163"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-powershell"></a>Gyors útmutató: az Azure cache átirányítása a Redis-események webes végponthoz a PowerShell használatával

Az Azure Event Grid egy felhőalapú eseménykezelési szolgáltatás. Ebben a rövid útmutatóban Azure PowerShell fog előfizetni az Azure cache-re a Redis eseményekhez, aktiválhat egy eseményt, és megtekintheti az eredményeket. 

Általában olyan végpontoknak szoktunk eseményeket küldeni, amelyek eseményadatokat dolgoznak fel és műveleteket hajtanak végre. A rövid útmutató egyszerűsítése érdekében azonban olyan webalkalmazásnak küldheti az eseményeket, amely összegyűjti és megjeleníti az üzeneteket. Az ebben a rövid útmutatóban ismertetett lépések elvégzése után láthatja, hogy a rendszer elküldte az eseményre vonatkozó információt a webalkalmazásnak.

## <a name="setup"></a>Telepítés

Ehhez a rövid útmutatóhoz a Azure PowerShell legújabb verzióját kell futtatnia. Ha telepíteni vagy frissíteni szeretne, olvassa el a [Azure PowerShell telepítése és konfigurálása](/powershell/azure/install-Az-ps)című témakört.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és kövesse a képernyőn megjelenő utasításokat a hitelesítéshez.

```powershell
Connect-AzAccount
```

Ez a példa **westus2** használ, és egy változóban tárolja a kijelölést az egészben való használatra.

```powershell
$location = "westus2"
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Event Grid témaköröket különálló Azure-erőforrásként helyezi üzembe, és egy Azure-erőforráscsoport alatt kell kiépíteni. Az erőforráscsoport olyan logikai gyűjtemény, amelyben az Azure-erőforrások üzembe helyezése és kezelése történik.

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) paranccsal.

A következő példában létrehozunk egy **gridResourceGroup** nevű erőforráscsoportot a **westus2** helyen.  

```powershell
$resourceGroup = "gridResourceGroup"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-an-azure-cache-for-redis-instance"></a>Azure cache létrehozása a Redis-példányhoz 

```powershell
New-AzRedisCache
   -ResourceGroupName <String>
   -Name <String>
   -Location <String>
   [-Size <String>]
   [-Sku <String>]
   [-RedisConfiguration <Hashtable>]
   [-EnableNonSslPort <Boolean>]
   [-TenantSettings <Hashtable>]
   [-ShardCount <Int32>]
   [-MinimumTlsVersion <String>]
   [-SubnetId <String>]
   [-StaticIP <String>]
   [-Tag <Hashtable>]
   [-Zone <String[]>]
   [-DefaultProfile <IAzureContextContainer>]
   [-WhatIf]
   [-Confirm]
   [<CommonParameters>]
```
A gyorsítótár-példány PowerShellben való létrehozásával kapcsolatos további információkért tekintse meg a [Azure PowerShell referenciát](/powershell/module/az.rediscache/new-azrediscache). 

## <a name="create-a-message-endpoint"></a>Üzenetvégpont létrehozása

A témakörre való feliratkozás előtt hozzuk létre az eseményüzenet végpontját. A végpont általában az eseményadatok alapján hajt végre műveleteket. Az útmutató egyszerűsítése érdekében egy olyan [előre létrehozott webalkalmazást](https://github.com/Azure-Samples/azure-event-grid-viewer) helyezünk üzembe, amely megjeleníti az esemény üzeneteit. Az üzembe helyezett megoldás egy App Service-csomagot, egy App Service-webalkalmazást és egy, a GitHubról származó forráskódot tartalmaz.

A `<your-site-name>` elemet a webalkalmazás egyedi nevére cserélje le. A webalkalmazás nevének egyedinek kell lennie, mert a DNS-bejegyzés része.

```powershell
$sitename="<your-site-name>"

New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" `
  -siteName $sitename `
  -hostingPlanName viewerhost
```

Az üzembe helyezés befejezése eltarthat néhány percig. A sikeres üzembe helyezést követően tekintse meg a webalkalmazást, hogy meggyőződjön annak működéséről. Egy webböngészőben navigáljon a következő helyre: `https://<your-site-name>.azurewebsites.net`.

A helynek megjelenített üzenetek nélkül kell megjelennie.

:::image type="content" source="media/cache-event-grid-portal/blank-event-grid-viewer.png" alt-text="Üres Event Grid megjelenítői hely.":::

## <a name="subscribe-to-your-azure-cache-for-redis-event"></a>Előfizetés az Azure cache for Redis eseményre

Ebben a lépésben előfizet egy témakörre, hogy elmondja Event Grid mely eseményeket kívánja nyomon követni. Az alábbi példa előfizet a létrehozott gyorsítótár-példányra, és átadja az URL-címet a webalkalmazásból az eseményekről szóló értesítés végpontjának. A webalkalmazás végpontjának az `/api/updates/` utótagot kell tartalmaznia.

```powershell
$cacheId = (Get-AzRedisCache -ResourceGroupName $resourceGroup -Name $cacheName).Id
$endpoint="https://$sitename.azurewebsites.net/api/updates"

New-AzEventGridSubscription `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint $endpoint `
  -ResourceId $cacheId
```

Tekints meg újra a webalkalmazást, ahol láthatja, hogy az fogadta az előfizetés érvényesítési eseményét. Az eseményadatok kibontásához kattintson a szem ikonra. Az Event Grid elküldi az érvényesítési eseményt, így a végpont megerősítheti, hogy eseményadatokat akar kapni. A webalkalmazás az előfizetés érvényesítéséhez szükséges kódot tartalmaz.

  :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Azure Event Grid Viewer.":::

## <a name="trigger-an-event-from-azure-cache-for-redis"></a>Esemény indítása az Azure cache Redis

Most aktiváljunk egy eseményt, és lássuk, hogyan küldi el az üzenetet az Event Grid a végpontnak.

```powershell
Import-AzRedisCache
      [-ResourceGroupName <String>]
      -Name <String>
      -Files <String[]>
      [-Format <String>]
      [-Force]
      [-PassThru]
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
```
A PowerShell-beli importálással kapcsolatos további információkért tekintse meg a [Azure PowerShell referenciát](/powershell/module/az.rediscache/import-azrediscache). 

Ön kiváltotta az eseményt, az Event Grid pedig elküldte az üzenetet a feliratkozáskor konfigurált végpontnak. Tekintse meg a webalkalmazást az imént elküldött esemény megtekintéséhez.

```json
[{
"id": "e1ceb52d-575c-4ce4-8056-115dec723cff",
  "eventType": "Microsoft.Cache.ImportRDBCompleted",
  "topic": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
  "data": {
    "name": "ImportRDBCompleted",
    "timestamp": "2020-12-10T18:07:54.4937063+00:00",
    "status": "Succeeded"
  },
  "subject": "ImportRDBCompleted",
  "dataversion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-12-10T18:07:54.4937063+00:00"
}]

```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha azt tervezi, hogy folytatja az Azure cache használatát a Redis-példány és az esemény-előfizetés esetében, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a műveletet, a következő parancs használatával törölheti az ebben a rövid útmutatóban létrehozott erőforrásokat.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Következő lépések

Most, hogy már tudja, hogyan hozhat létre témaköröket és esemény-előfizetéseket, többet is megtudhat az Azure cache Redis-eseményekről, illetve arról, hogy milyen Event Grid segíthet:

- [Az Azure cache-re való reagálás Redis eseményekhez](cache-event-grid.md)
- [Bevezetés az Event Grid használatába](../event-grid/overview.md)