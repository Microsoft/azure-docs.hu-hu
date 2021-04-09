---
title: Kapcsolatok hibáinak megoldása – Azure CLI
titleSuffix: Azure Network Watcher
description: Ismerje meg, hogyan használhatja az Azure Network Watcher kapcsolódási hibáit az Azure CLI használatával.
services: network-watcher
documentationcenter: na
author: damendo
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: e93a9b7ba2c7f801761d33ccacfa3ecdb3c48503
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019788"
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-the-azure-cli"></a>Az Azure Network Watcher kapcsolatának hibáinak megoldása az Azure CLI használatával

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [Azure CLI](network-watcher-connectivity-cli.md)
> - [Azure REST API](network-watcher-connectivity-rest.md)

Megtudhatja, hogyan hozhatja meg a kapcsolódási hibák megoldását annak ellenőrzéséhez, hogy egy virtuális gépről egy adott végpontra irányuló közvetlen TCP-kapcsolódás létesíthető-e.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik a következő erőforrásokkal:

* Network Watcher egy példánya abban a régióban, ahol a kapcsolódást szeretné elhárítani.
* Virtuális gépek a szolgáltatással való kapcsolódási hibák megoldásához.

> [!IMPORTANT]
> A kapcsolati hibákhoz az szükséges, hogy a rendszer a virtuálisgép- `AzureNetworkWatcherExtension` bővítményt telepítse. A bővítmény Windows rendszerű virtuális gépen való telepítéséhez látogasson el az [azure Network Watcher Agent virtuálisgép-bővítmény a Windows](../virtual-machines/extensions/network-watcher-windows.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) rendszerhez és a Linux rendszerű virtuális gépekhez látogasson el az [Azure Network Watcher Agent virtuálisgép-bővítménye Linuxra](../virtual-machines/extensions/network-watcher-linux.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). A cél végponton nem szükséges a kiterjesztés.

## <a name="check-connectivity-to-a-virtual-machine"></a>Virtuális géphez való csatlakozás ellenőrzése

Ez a példa az 80-es porton keresztül ellenőrzi a cél virtuális géphez való kapcsolódást.

### <a name="example"></a>Példa

```azurecli
az network watcher test-connectivity --resource-group ContosoRG --source-resource MultiTierApp0 --dest-resource Database0 --dest-port 80
```

### <a name="response"></a>Reagálás

A következő válasz az előző példából származik.  Ebben a válaszban az `ConnectionStatus` nem **érhető el**. Láthatja, hogy az összes eljuttatott mintavétel sikertelen volt. A kapcsolat nem sikerült a virtuális berendezésen, mert egy UserRule_Port80 nevű felhasználó konfigurálta, amely a `NetworkSecurityRule` 80-es porton való bejövő forgalom blokkolására van konfigurálva.  Ezek az adatok a kapcsolódási problémák kutatására használhatók.

```json
{
  "avgLatencyInMs": null,
  "connectionStatus": "Unreachable",
  "hops": [
    {
      "address": "10.1.1.4",
      "id": "bb01d336-d881-4808-9fbc-72f091974d68",
      "issues": [],
      "nextHopIds": [
        "f8b074e9-9980-496b-a35e-619f9bcbf648"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/ap
pNic0/ipConfigurations/ipconfig1",
      "type": "Source"
    },
    {
      "address": "10.1.2.4",
      "id": "f8b074e9-9980-496b-a35e-619f9bcbf648",
      "issues": [],
      "nextHopIds": [
        "8a5857f3-6ab8-4b11-b9bf-a046d66b8696"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/fw
Nic/ipConfigurations/ipconfig1",
      "type": "VirtualAppliance"
    },
    {
      "address": "10.1.3.4",
      "id": "8a5857f3-6ab8-4b11-b9bf-a046d66b8696",
      "issues": [
        {
          "context": [
            {
              "key": "RuleName",
              "value": "UserRule_Port80"
            }
          ],
          "origin": "Outbound",
          "severity": "Error",
          "type": "NetworkSecurityRule"
        }
      ],
      "nextHopIds": [
        "6ce2f7a2-ceb4-4145-80e8-5d9f661655d6"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/au
Nic/ipConfigurations/ipconfig1",
      "type": "VirtualAppliance"
    },
    {
      "address": "10.1.4.4",
      "id": "6ce2f7a2-ceb4-4145-80e8-5d9f661655d6",
      "issues": [],
      "nextHopIds": [],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/db
Nic0/ipConfigurations/ipconfig1",
      "type": "VnetLocal"
    }
  ],
  "maxLatencyInMs": null,
  "minLatencyInMs": null,
  "probesFailed": 100,
  "probesSent": 100
}
```

## <a name="validate-routing-issues"></a>Útválasztási problémák ellenőrzése

Ez a példa egy virtuális gép és egy távoli végpont közötti kapcsolatot ellenőrzi.

### <a name="example"></a>Példa

```azurecli
az network watcher test-connectivity --resource-group ContosoRG --source-resource MultiTierApp0 --dest-address 13.107.21.200 --dest-port 80
```

### <a name="response"></a>Reagálás

A következő példában a látható, hogy `connectionStatus` nem **érhető el**. A `hops` részletek között látható, `issues` hogy a forgalom le lett tiltva, mert egy `UserDefinedRoute` .

```json
{
  "avgLatencyInMs": null,
  "connectionStatus": "Unreachable",
  "hops": [
    {
      "address": "10.1.1.4",
      "id": "f2cb1868-2049-4839-b8ed-57a480d06f95",
      "issues": [
        {
          "context": [
            {
              "key": "RouteType",
              "value": "User"
            }
          ],
          "origin": "Outbound",
          "severity": "Error",
          "type": "UserDefinedRoute"
        }
      ],
      "nextHopIds": [
        "da4022db-0ab0-48c4-a507-dd4c03561ca5"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/ap
pNic0/ipConfigurations/ipconfig1",
      "type": "Source"
    },
    {
      "address": "13.107.21.200",
      "id": "da4022db-0ab0-48c4-a507-dd4c03561ca5",
      "issues": [],
      "nextHopIds": [],
      "resourceId": "Unknown",
      "type": "Destination"
    }
  ],
  "maxLatencyInMs": null,
  "minLatencyInMs": null,
  "probesFailed": 100,
  "probesSent": 100
}
```

## <a name="check-website-latency"></a>Webhely késésének keresése

A következő példa egy webhelyhez való kapcsolódást ellenőrzi.

### <a name="example"></a>Példa

```azurecli
az network watcher test-connectivity --resource-group ContosoRG --source-resource MultiTierApp0 --dest-address https://bing.com --dest-port 80
```

### <a name="response"></a>Reagálás

A következő válaszban láthatja, hogy a `connectionStatus` láthatók **elérhetők**. Ha a csatlakozás sikeres, a késési értékek megadására kerül sor.

```json
{
  "avgLatencyInMs": 2,
  "connectionStatus": "Reachable",
  "hops": [
    {
      "address": "10.1.1.4",
      "id": "639c2d19-e163-4dfd-8737-5018dd1168ae",
      "issues": [],
      "nextHopIds": [
        "fd43a6e7-c758-4f48-90aa-8db99105a4a3"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/ap
pNic0/ipConfigurations/ipconfig1",
      "type": "Source"
    },
    {
      "address": "204.79.197.200",
      "id": "fd43a6e7-c758-4f48-90aa-8db99105a4a3",
      "issues": [],
      "nextHopIds": [],
      "resourceId": "Internet",
      "type": "Internet"
    }
  ],
  "maxLatencyInMs": 7,
  "minLatencyInMs": 0,
  "probesFailed": 0,
  "probesSent": 100
}
```

## <a name="check-connectivity-to-a-storage-endpoint"></a>A tárolási végponttal létesített kapcsolat ellenőrzése

Az alábbi példa egy virtuális gép és egy blog Storage-fiók közötti kapcsolatot ellenőrzi.

### <a name="example"></a>Példa

```azurecli
az network watcher test-connectivity --resource-group ContosoRG --source-resource MultiTierApp0 --dest-address https://contosoexamplesa.blob.core.windows.net/
```

### <a name="response"></a>Reagálás

A következő JSON az előző parancsmag futtatásának példája. Az ellenőrzések sikeressége esetén a `connectionStatus` tulajdonság **elérhetőként** jelenik meg.  A tárolási blob és a késés eléréséhez szükséges ugrások számával kapcsolatos részletek.

```json
{
  "avgLatencyInMs": 1,
  "connectionStatus": "Reachable",
  "hops": [
    {
      "address": "10.1.1.4",
      "id": "5136acff-bf26-4c93-9966-4edb7dd40353",
      "issues": [],
      "nextHopIds": [
        "f8d958b7-3636-4d63-9441-602c1eb2fd56"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/appNic0/ipConfigurations/ipconfig1",
      "type": "Source"
    },
    {
      "address": "1.2.3.4",
      "id": "f8d958b7-3636-4d63-9441-602c1eb2fd56",
      "issues": [],
      "nextHopIds": [],
      "resourceId": "Internet",
      "type": "Internet"
    }
  ],
  "maxLatencyInMs": 7,
  "minLatencyInMs": 0,
  "probesFailed": 0,
  "probesSent": 100
}
```

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan automatizálható a csomagok rögzítése a virtuálisgép-riasztásokkal a [riasztások által aktivált csomagok létrehozása](network-watcher-alert-triggered-packet-capture.md) funkció megtekintésével

Annak megállapítása, hogy az [IP-forgalom ellenőrzésének](diagnose-vm-network-traffic-filtering-problem.md) meglátogatása esetén engedélyezett-e a virtuális gép bejövő vagy kimenő forgalma