---
title: Megadott nyilvános IP-címmel rendelkező készlet létrehozása
description: Megtudhatja, hogyan hozhat létre saját nyilvános IP-címeket használó batch-készletet.
ms.topic: how-to
ms.date: 10/08/2020
ms.openlocfilehash: e822311718847e173763847d503335f71457308b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91849328"
---
# <a name="create-an-azure-batch-pool-with-specified-public-ip-addresses"></a>Megadott nyilvános IP-címmel rendelkező Azure Batch-készlet létrehozása

Azure Batch készlet létrehozásakor [kiépítheti a készletet](batch-virtual-network.md) egy Ön által megadott Azure-beli virtuális hálózat (VNet) alhálózatában. A Batch-készletben lévő virtuális gépek a Batch által létrehozott nyilvános IP-címeken keresztül érhetők el. Ezek a nyilvános IP-címek megváltoztathatják a készlet élettartamát, ami azt jelenti, hogy a hálózati beállítások elavultak lesznek, ha az IP-címek nem frissülnek.

Létrehozhat egy listát a készletben lévő virtuális gépekhez használható statikus nyilvános IP-címekről. Ez lehetővé teszi a nyilvános IP-címek listájának szabályozását, és biztosítja, hogy a rendszer váratlanul ne változzon meg. Ez különösen akkor lehet hasznos, ha olyan külső szolgáltatással dolgozik, mint például egy adatbázis, amely korlátozza bizonyos IP-címek elérését.

A készletek nyilvános IP-címek nélküli létrehozásával kapcsolatos információkért olvassa el a [Azure batch-készlet létrehozása nyilvános IP-címek nélkül](./batch-pool-no-public-ip-address.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

- **Hitelesítés**. Nyilvános IP-cím használatához a Batch-ügyfél API-nak [Azure Active Directory (ad) hitelesítést](batch-aad-auth.md)kell használnia.

- **Egy Azure-VNet**. Egy olyan Azure-előfizetésből származó [virtuális hálózatot](batch-virtual-network.md) kell használnia, amelyben létrehozza a készletet és az IP-címeit. Csak Azure Resource Manager-alapú virtuális hálózatok lehet használni. Ügyeljen arra, hogy a VNet megfeleljen az összes [általános követelménynek](batch-virtual-network.md#vnet-requirements).

- **Legalább egy nyilvános Azure IP-cím**. Egy vagy több nyilvános IP-cím létrehozásához használhatja a [Azure Portal](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address), az [Azure Command-Line felületét (CLI)](/cli/azure/network/public-ip#az-network-public-ip-create)vagy [Azure PowerShell](/powershell/module/az.network/new-azpublicipaddress). Ügyeljen arra, hogy kövesse az alább felsorolt követelményeket.

> [!NOTE]
> A Batch automatikusan lefoglalja a nyilvános IP-címeket tartalmazó erőforráscsoport további hálózati erőforrásait. Minden 100 dedikált csomópont esetében a Batch általában egy hálózati biztonsági csoportot (NSG) és egy terheléselosztó-t foglal le. Ezeket az erőforrásokat az előfizetés erőforrás-kvótái korlátozzák. Nagyobb készletek használata esetén előfordulhat, hogy egy vagy több ilyen erőforrás esetében [kvótát](batch-quota-limit.md#increase-a-quota) kell megadnia.

## <a name="public-ip-address-requirements"></a>Nyilvános IP-címekre vonatkozó követelmények

A nyilvános IP-címek létrehozásakor vegye figyelembe a következő követelményeket:

- A nyilvános IP-címnek ugyanabban az előfizetésben és régióban kell lennie, mint a készlet létrehozásához használt batch-fióknak.
- Az **IP-cím hozzárendelését** **statikusra** kell beállítani.
- Az **SKU** -t a **standard** értékre kell beállítani.
- Meg kell adni egy DNS-nevet.
- A nyilvános IP-címeket csak a virtuális gép konfigurációs készletei használhatják. Más erőforrásoknak nem kell használniuk ezeket az IP-címeket, vagy a készlet lefoglalási hibákhoz vezethet.
- Nincsenek biztonsági szabályzatok vagy erőforrás-zárolások, amelyek korlátozzák a felhasználó hozzáférését a nyilvános IP-címhez.
- A készlethez megadott nyilvános IP-címek számának elég nagynak kell lennie ahhoz, hogy megfeleljen a készlethez célként megnevezett virtuális gépek számának. Ennek legalább a készlet **targetDedicatedNodes**   és **targetLowPriorityNodes** tulajdonságainak összegének kell lennie   . Ha nincs elegendő IP-cím, a készlet részlegesen lefoglalja a számítási csomópontokat, és átméretezési hiba lép fel. A Batch jelenleg minden 100 virtuális géphez egy nyilvános IP-címet használ.
- Mindig rendelkezik a nyilvános IP-címek további pufferével. Javasoljuk, hogy adjon hozzá legalább egy további nyilvános IP-címet, vagy a készlethez hozzáadott összes nyilvános IP-cím körülbelül 10%-át, attól függően, hogy melyik a nagyobb. Ez a további puffer segítséget nyújt a Batch számára a belső optimalizálással a skálázás során, valamint lehetővé teszi a gyorsabb skálázást a nem sikeres vertikális felskálázás vagy leskálázás után.
- A készlet létrehozása után nem lehet hozzáadni vagy módosítani a készlet által használt nyilvános IP-címek listáját. Ha módosítania kell a listát, törölnie kell a készletet, majd újra létre kell hoznia.

## <a name="create-a-batch-pool-with-public-ip-addresses"></a>Nyilvános IP-címmel rendelkező batch-készlet létrehozása

Az alábbi példa bemutatja, hogyan használható a [Azure batch Service REST API](/rest/api/batchservice/pool/add) egy nyilvános IP-címeket használó készlet létrehozásához.

### <a name="batch-service-rest-api"></a>A Batch szolgáltatás REST API-ja

REST API URI

```http
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

Kérelem törzse

```json
"pool": {
      "id": "pool2",
      "vmSize": "standard_a1",
      "virtualMachineConfiguration": {
        "imageReference": {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04.0-LTS"
        },
        "nodeAgentSKUId": "batch.node.ubuntu 16.04"
      },
"networkConfiguration": {
          "subnetId": "/subscriptions/<subId>/resourceGroups/<rgId>/providers/Microsoft.Network/virtualNetworks/<vNetId>/subnets/<subnetId>",
          "publicIPAddressConfiguration": {
            "provision": "usermanaged",
            "ipAddressIds": [
              "/subscriptions/<subId>/resourceGroups/<rgId>/providers/Microsoft.Network/publicIPAddresses/<publicIpId>"
          ]
        },

       "resizeTimeout":"PT15M",
      "targetDedicatedNodes":5,
      "targetLowPriorityNodes":0,
      "taskSlotsPerNode":3,
      "taskSchedulingPolicy": {
        "nodeFillType":"spread"
      },
      "enableAutoScale":false,
      "enableInterNodeCommunication":true,
      "metadata": [ {
        "name":"myproperty",
        "value":"myvalue"
      } ]
    }
```

## <a name="next-steps"></a>Következő lépések

- Ismerje meg a [Batch szolgáltatás munkafolyamatát és az elsődleges erőforrásokat](batch-service-workflow-features.md) , például a készleteket, a csomópontokat, a feladatokat és a feladatokat.
- Ismerje meg [, hogyan hozhat létre készletet egy Azure-beli virtuális hálózat alhálózatában](batch-virtual-network.md).
- Tudnivalók a [Azure batch-készletek nyilvános IP-címek nélküli létrehozásáról](./batch-pool-no-public-ip-address.md).

