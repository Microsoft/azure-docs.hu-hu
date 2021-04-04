---
title: VNET-átjáró és-kapcsolatok – Azure REST API
titleSuffix: Azure Network Watcher
description: Ez az oldal azt ismerteti, hogyan lehet elhárítani Virtual Network átjárók és az Azure Network Watcher kapcsolatainak használatát a REST használatával
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 72f279d90d071b0e5fe7f552a05474bc3b37a82e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98011033"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher"></a>Az átjáró és a kapcsolatok Virtual Network az Azure Network Watcher használatával – problémamegoldás

> [!div class="op_single_selector"]
> - [Portál](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [Azure CLI](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

A Network Watcher számos képességgel rendelkezik, amelyek az Azure hálózati erőforrásainak megismeréséhez kapcsolódnak. Ezen képességek egyike az erőforrás-hibaelhárítás. Az erőforrás-hibaelhárítás a portál, a PowerShell, a CLI vagy a REST API használatával hívható meg. A híváskor Network Watcher megvizsgálja egy Virtual Network átjáró vagy egy kapcsolat állapotát, és visszaadja az eredményeit.

Ez a cikk végigvezeti az erőforrás-hibaelhárításhoz jelenleg elérhető különböző felügyeleti feladatokon.

- [**Virtual Network átjáró hibáinak megoldása**](#troubleshoot-a-virtual-network-gateway)
- [**Kapcsolatok hibáinak megoldása**](#troubleshoot-connections)

## <a name="before-you-begin"></a>Előkészületek

A ARMclient a REST API a PowerShell használatával történő meghívására szolgál. A ARMClient a chocolatey címen található a [ARMClient-on](https://chocolatey.org/packages/ARMClient)

Ez a forgatókönyv feltételezi, hogy már követte a [Network Watcher létrehozása](network-watcher-create.md) című témakör lépéseit Network Watcher létrehozásához.

A támogatott átjáró-típusok listáját a [támogatott átjárók típusainál](network-watcher-troubleshoot-overview.md#supported-gateway-types)találhatja meg.

## <a name="overview"></a>Áttekintés

Network Watcher hibaelhárítási funkció lehetővé teszi a Virtual Network átjárókkal és kapcsolatokkal kapcsolatos hibák elhárítását. Ha az erőforrás-hibaelhárításra vonatkozó kérelmet küld, a rendszer lekérdezi és ellenőrzi a naplókat. Az ellenőrzés befejezésekor a rendszer az eredményeket adja vissza. Az API-kérelmek hibakeresése hosszú ideig futó kérelmeket eredményez, ami több percet is igénybe vehet. A naplókat tároló fiók tárolja.

## <a name="log-in-with-armclient"></a>Bejelentkezés a ARMClient

```powershell
armclient login
```

## <a name="troubleshoot-a-virtual-network-gateway"></a>Virtual Network átjáró hibáinak megoldása


### <a name="post-the-troubleshoot-request"></a>A hibakeresési kérelem közzététele

A következő példa egy Virtual Network átjáró állapotát kérdezi le.

```powershell

$subscriptionId = "00000000-0000-0000-0000-000000000000"
$resourceGroupName = "ContosoRG"
$NWresourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$vnetGatewayName = "ContosoVNETGateway"
$storageAccountName = "contososa"
$containerName = "gwlogs"
$requestBody = @"
{
'TargetResourceId': '/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/virtualNetworkGateways/${vnetGatewayName}',
'Properties': {
'StorageId': '/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Storage/storageAccounts/${storageAccountName}',
'StoragePath': 'https://${storageAccountName}.blob.core.windows.net/${containerName}'
}
}
"@


armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/troubleshoot?api-version=2016-03-30" $requestBody -verbose
```

Mivel ez a művelet hosszú ideig fut, a művelethez tartozó URI-t és az eredmény URI-JÁT a válasz fejléce adja vissza, ahogy az a következő válaszban látható:

**Fontos értékek**

* **Azure-AsyncOperation** – ez a tulajdonság tartalmazza az aszinkron hibakeresési művelet lekérdezéséhez szükséges URI-t.
* **Hely** – ez a tulajdonság tartalmazza azt az URI-t, amelyben az eredmények a művelet befejezésekor teljesülnek.

```
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 10
x-ms-request-id: 8a1167b7-6768-4ac1-85dc-703c9c9b9247
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Strict-Transport-Security: max-age=31536000; includeSubDomains
Cache-Control: no-cache
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Server: Microsoft-HTTPAPI/2.0; Microsoft-HTTPAPI/2.0
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 4364d88a-bd08-422c-a716-dbb0cdc99f7b
x-ms-routing-request-id: NORTHCENTRALUS:20170112T183202Z:4364d88a-bd08-422c-a716-dbb0cdc99f7b
Date: Thu, 12 Jan 2017 18:32:01 GMT

null
```

### <a name="query-the-async-operation-for-completion"></a>Aszinkron művelet lekérdezése befejezéshez

Az operatív URI használatával kérdezheti le a művelet előrehaladását az alábbi példában látható módon:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

Amíg a művelet folyamatban van, a válasz a következő példában látható módon **Inelőrehaladást** mutat:

```json
{
  "status": "InProgress"
}
```

Ha a művelet befejeződött, az állapot **sikeresre** változik.

```json
{
  "status": "Succeeded"
}
```

### <a name="retrieve-the-results"></a>Az eredmény lekérése

Ha a visszaadott állapot **sikeres** volt, hívja a Get metódust a operationresult tevékenységen URI-n az eredmények lekéréséhez.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

A következő válaszok példákat mutatnak az átjáró hibaelhárítási eredményeinek lekérdezése során visszaadott jellemző csökkentett teljesítményű válaszra. Tekintse meg [az eredmények megismerését](#understanding-the-results) ismertető témakört, amelyből megtudhatja, hogy a válasz milyen tulajdonságokat jelent.

```json
{
  "startTime": "2017-01-12T10:31:41.562646-08:00",
  "endTime": "2017-01-12T18:31:48.677Z",
  "code": "Degraded",
  "results": [
    {
      "id": "PlatformInActive",
      "summary": "We are sorry, your VPN gateway is in standby mode",
      "detail": "During this time the gateway will not initiate or accept VPN connections with on premises VPN devices or other Azure VPN Gateways. This is a transient state while the Azure platform is being updated.",
      "recommendedActions": [
        {
          "actionText": "If the condition persists, please try resetting your Azure VPN gateway",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting the VPN Gateway"
        },
        {
          "actionText": "If your VPN gateway isn't up and running by the expected resolution time, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    },
    {
      "id": "NoFault",
      "summary": "This VPN gateway is running normally",
      "detail": "There aren't any known Azure platform problems affecting this VPN Connection",
      "recommendedActions": [
        {
          "actionText": "If you are still experience problems with the VPN gateway, please try resetting the VPN gateway.",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting VPN gateway"
        },
        {
          "actionText": "If you are experiencing problems you believe are caused by Azure, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    }
  ]
}
```


## <a name="troubleshoot-connections"></a>Kapcsolatok hibáinak megoldása

A következő példa a kapcsolatok állapotát kérdezi le.

```powershell

$subscriptionId = "00000000-0000-0000-0000-000000000000"
$resourceGroupName = "ContosoRG"
$NWresourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$connectionName = "VNET2toVNET1Connection"
$storageAccountName = "contososa"
$containerName = "gwlogs"
$requestBody = @{
"TargetResourceId": "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/connections/${connectionName}",
"Properties": {
"StorageId": "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Storage/storageAccounts/${storageAccountName}",
"StoragePath": "https://${storageAccountName}.blob.core.windows.net/${containerName}"
}

}
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/troubleshoot?api-version=2016-03-30 $requestBody"
```

> [!NOTE]
> A hibakeresési műveletet nem lehet párhuzamosan futtatni a kapcsolaton és a hozzá tartozó átjárón. A műveletnek a korábbi erőforráson való futtatása előtt kell végrehajtania.

Mivel ez egy hosszú ideig futó tranzakció, a válasz fejlécében a művelet lekérdezéséhez és az eredmény URI azonosítójának URI-ja a következő válaszban látható módon lesz visszaadva:

**Fontos értékek**

* **Azure-AsyncOperation** – ez a tulajdonság tartalmazza az aszinkron hibakeresési művelet lekérdezéséhez szükséges URI-t.
* **Hely** – ez a tulajdonság tartalmazza azt az URI-t, amelyben az eredmények a művelet befejezésekor teljesülnek.

```
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 10
x-ms-request-id: 8a1167b7-6768-4ac1-85dc-703c9c9b9247
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Strict-Transport-Security: max-age=31536000; includeSubDomains
Cache-Control: no-cache
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Server: Microsoft-HTTPAPI/2.0; Microsoft-HTTPAPI/2.0
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 4364d88a-bd08-422c-a716-dbb0cdc99f7b
x-ms-routing-request-id: NORTHCENTRALUS:20170112T183202Z:4364d88a-bd08-422c-a716-dbb0cdc99f7b
Date: Thu, 12 Jan 2017 18:32:01 GMT

null
```

### <a name="query-the-async-operation-for-completion"></a>Aszinkron művelet lekérdezése befejezéshez

Az operatív URI használatával kérdezheti le a művelet előrehaladását az alábbi példában látható módon:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

Amíg a művelet folyamatban van, a válasz a következő példában látható módon **Inelőrehaladást** mutat:

```json
{
  "status": "InProgress"
}
```

Ha a művelet befejeződött, az állapot **sikeresre** változik.

```json
{
  "status": "Succeeded"
}
```

A következő válaszok példát mutatnak a kapcsolatok hibaelhárítási eredményeinek lekérdezése során kapott tipikus válaszra.

### <a name="retrieve-the-results"></a>Az eredmény lekérése

Ha a visszaadott állapot **sikeres** volt, hívja a Get metódust a operationresult tevékenységen URI-n az eredmények lekéréséhez.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

A következő válaszok példát mutatnak a kapcsolatok hibaelhárítási eredményeinek lekérdezése során kapott tipikus válaszra.

```json
{
  "startTime": "2017-01-12T14:09:19.1215346-08:00",
  "endTime": "2017-01-12T22:09:23.747Z",
  "code": "UnHealthy",
  "results": [
    {
      "id": "PlatformInActive",
      "summary": "We are sorry, your VPN gateway is in standby mode",
      "detail": "During this time the gateway will not initiate or accept VPN connections with on premises VPN devices or other Azure VPN Gateways. This 
is a transient state while the Azure platform is being updated.",
      "recommendedActions": [
        {
          "actionText": "If the condition persists, please try resetting your Azure VPN gateway",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting the VPN gateway"
        },
        {
          "actionText": "If your VPN Connection isn't up and running by the expected resolution time, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    },
    {
      "id": "NoFault",
      "summary": "This VPN Connection is running normally",
      "detail": "There aren't any known Azure platform problems affecting this VPN Connection",
      "recommendedActions": [
        {
          "actionText": "If you are still experience problems with the VPN gateway, please try resetting the VPN gateway.",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting VPN gateway"
        },
        {
          "actionText": "If you are experiencing problems you believe are caused by Azure, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    }
  ]
}
```

## <a name="understanding-the-results"></a>Az eredmények megismerése

A művelet szövege általános útmutatást nyújt a probléma megoldásához. Ha a probléma megoldására is sor kerül, további útmutatást nyújt a hivatkozáshoz. Abban az esetben, ha nincs további útmutatás, a válasz egy támogatási eset megnyitására szolgáló URL-címet biztosít.  A válasz tulajdonságaival és a benne foglalt információkkal kapcsolatos további információkért tekintse meg [Network Watcher a hibakeresés áttekintése című](network-watcher-troubleshoot-overview.md) témakört.

A fájlok Azure Storage-fiókokból való letöltésével kapcsolatos utasításokért tekintse meg az [Azure Blob Storage használatának első lépései a .NET használatával](../storage/blobs/storage-quickstart-blobs-dotnet.md)című témakört. Egy másik eszköz is használható Storage Explorer. Storage Explorer további információ a következő hivatkozáson található: [Storage Explorer](https://storageexplorer.com/)

## <a name="next-steps"></a>Következő lépések

Ha a beállítások megváltoztak a VPN-kapcsolat leállításakor, tekintse meg a [hálózati biztonsági csoportok kezelése](../virtual-network/manage-network-security-group.md) a hálózati biztonsági csoport és az esetlegesen érintett biztonsági szabályok nyomon követéséhez című témakört.