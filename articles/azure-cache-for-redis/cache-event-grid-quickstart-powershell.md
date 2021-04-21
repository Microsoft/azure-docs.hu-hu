---
title: 'Rövid útmutató: Események Azure Cache for Redis webes végpontra a PowerShell használatával'
description: A Azure Event Grid feliratkozhat a Azure Cache for Redis eseményekre, elküldhet eseményeket egy webhooknak, és kezelni tudja az eseményeket egy webalkalmazásban.
ms.date: 1/5/2021
author: curib
ms.author: cauribeg
ms.topic: quickstart
ms.service: cache
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fa1ae9a7a2200944bd5da0211be88ba4955e3d03
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833885"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-powershell"></a>Rövid útmutató: Események Azure Cache for Redis webes végpontra a PowerShell használatával

Az Azure Event Grid egy felhőalapú eseménykezelési szolgáltatás. Ebben a rövid útmutatóban a Azure PowerShell feliratkozik a Azure Cache for Redis eseményekre, elindít egy eseményt, és megtekinti az eredményeket. 

Általában olyan végpontoknak szoktunk eseményeket küldeni, amelyek eseményadatokat dolgoznak fel és műveleteket hajtanak végre. A rövid útmutató egyszerűsítése érdekében azonban eseményeket fog küldeni egy webalkalmazásnak, amely összegyűjti és megjeleníti az üzeneteket. Az ebben a rövid útmutatóban leírt lépések befejezésekor látni fogja, hogy az eseményadatok el vannak küldve a webalkalmazásnak.

## <a name="setup"></a>Beállítás

Ehhez a rövid útmutatóhoz az alkalmazás legújabb verzióját kell Azure PowerShell. Ha telepíteni vagy frissítenie kell, lásd: [Install and configure Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-előfizetésbe az paranccsal, és kövesse a képernyőn megjelenő `Connect-AzAccount` utasításokat a hitelesítéshez.

```powershell
Connect-AzAccount
```

Ez a példa **a westus2 függvényt használja,** és egy változóban tárolja a kijelölést a teljes használathoz.

```powershell
$location = "westus2"
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Event Grid témakörök különálló Azure-erőforrásként vannak üzembe építve, és egy Azure-erőforráscsoportban kell őket kiépítenünk. Az erőforráscsoport olyan logikai gyűjtemény, amelyben a rendszer üzembe helyezheti és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup paranccsal.](/powershell/module/az.resources/new-azresourcegroup)

A következő példában létrehozunk egy **gridResourceGroup** nevű erőforráscsoportot a **westus2** helyen.  

```powershell
$resourceGroup = "gridResourceGroup"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-an-azure-cache-for-redis-instance"></a>Új Azure Cache for Redis létrehozása 

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
A gyorsítótárpéldányok PowerShellben való létrehozásával kapcsolatos további információkért tekintse meg a következő [Azure PowerShell:](/powershell/module/az.rediscache/new-azrediscache). 

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

:::image type="content" source="media/cache-event-grid-portal/blank-event-grid-viewer.png" alt-text="Üres Event Grid Megtekintő webhely.":::

## <a name="subscribe-to-your-azure-cache-for-redis-event"></a>Feliratkozás a Azure Cache for Redis eseményre

Ebben a lépésben feliratkozik egy témakörre, amelyből Event Grid mely eseményeket szeretné nyomon követni. Az alábbi példa feliratkozik a létrehozott gyorsítótár-példányra, és az eseményértesítés végpontjaként átadja a webalkalmazásból származó URL-címet. A webalkalmazás végpontjának az `/api/updates/` utótagot kell tartalmaznia.

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

## <a name="trigger-an-event-from-azure-cache-for-redis"></a>Esemény aktiválása a Azure Cache for Redis

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
A PowerShell-importálással kapcsolatos további információkért tekintse meg a Azure PowerShell [referenciáját.](/powershell/module/az.rediscache/import-azrediscache) 

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
Ha azt tervezi, hogy továbbra is ezt a Azure Cache for Redis és esemény-előfizetéssel dolgozik, akkor ne takarítsa meg az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, a következő paranccsal törölheti az ebben a rövid útmutatóban létrehozott erőforrásokat.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Következő lépések

Most, hogy már tudja, hogyan hozhat létre témaköröket és esemény-előfizetéseket, többet is megtudhat a Azure Cache for Redis eseményekről, és Event Grid segíthetnek a következőben:

- [Reagálás a Azure Cache for Redis eseményekre](cache-event-grid.md)
- [Bevezetés az Event Grid használatába](../event-grid/overview.md)
