---
title: Készlet létrehozása megadott nyilvános IP-címekkel
description: Megtudhatja, hogyan hozhat létre saját nyilvános IP-címeket használó Batch-készletet.
ms.topic: how-to
ms.date: 10/08/2020
ms.openlocfilehash: 82a37f96a91bdad37c1a7828ef0cf71b3581ca82
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768394"
---
# <a name="create-an-azure-batch-pool-with-specified-public-ip-addresses"></a>Hozzon létre egy Azure Batch készletet a megadott nyilvános IP-címekkel

Amikor létrehoz egy Azure Batch készletet, kiépítheti a készletet egy [Ön](batch-virtual-network.md) által megadott Azure-beli virtuális hálózat (VNet) alhálózatán. A Batch-készletben lévő virtuális gépek a Batch által létrehozott nyilvános IP-címeken keresztül érhetők el. Ezek a nyilvános IP-címek a készlet élettartama során változhatnak, ami azt jelenti, hogy a hálózati beállítások elavulttá válhatnak, ha az IP-címek nem frissülnek.

Létrehozhat egy listát a statikus nyilvános IP-címekről, amelyek a készletben lévő virtuális gépekkel használhatók. Ez lehetővé teszi a nyilvános IP-címek listájának szabályozását, és gondoskodik arról, hogy azok ne változnak váratlanul. Ez különösen hasznos lehet, ha olyan külső szolgáltatásokkal dolgozik, mint például egy adatbázis, amely bizonyos IP-címekre korlátozza a hozzáférést.

További információ a nyilvános IP-cím nélküli készletek létrehozásáról: Create an Azure Batch pool without public IP addresses (Nyilvános [IP-cím nélküli](./batch-pool-no-public-ip-address.md)Azure Batch létrehozása).

## <a name="prerequisites"></a>Előfeltételek

- **Hitelesítés:**. Nyilvános IP-cím használata esetén a Batch ügyfél API-jának Azure Active Directory [(AD) hitelesítést kell használnia.](batch-aad-auth.md)

- **Egy Azure-beli virtuális hálózat.** Ugyanannak [az](batch-virtual-network.md) Azure-előfizetésnek a virtuális hálózatát kell használnia, amelyben a készletet és az IP-címeket létrehozza. Csak Azure Resource Manager virtuális hálózatok használhatók. Győződjön meg arról, hogy a virtuális hálózat megfelel az összes általános [követelménynek.](batch-virtual-network.md#vnet-requirements)

- **Legalább egy nyilvános Azure IP-cím.** Egy vagy több nyilvános IP-cím létrehozásához használhatja a [Azure Portal,](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)az [Azure Command-Line Interface (CLI)](/cli/azure/network/public-ip#az_network_public_ip_create)vagy a [Azure PowerShell.](/powershell/module/az.network/new-azpublicipaddress) Mindenképpen kövesse az alábbi követelményeket.

> [!NOTE]
> A Batch automatikusan lefoglal további hálózati erőforrásokat a nyilvános IP-címeket tartalmazó erőforráscsoportban. A Batch általában minden 100 dedikált csomóponthoz lefoglal egy hálózati biztonsági csoportot (NSG-t) és egy terheléselosztót. Ezeket az erőforrásokat az előfizetés erőforráskvótái korlátozzak. Nagyobb készletek használata esetén előfordulhat, hogy kvótaemelést kell [kérnie](batch-quota-limit.md#increase-a-quota) egy vagy több ilyen erőforráshoz.

## <a name="public-ip-address-requirements"></a>Nyilvános IP-címekre vonatkozó követelmények

A nyilvános IP-címek létrehozásakor tartsa szem előtt a következő követelményeket:

- A nyilvános IP-címeknek ugyanabban az előfizetésben és régióban kell lennie, mint a Készlet létrehozásához használt Batch-fióknak.
- Az **IP-cím-hozzárendelést** Statikusra **kell állítani.**
- **A termékváltozatot** Standard **(Standard) termékváltozatra kell állítani.**
- Meg kell adni egy DNS-nevet.
- A nyilvános IP-címeket csak a virtuálisgép-konfigurációs készletekhez szabad használni. Más erőforrás nem használhatja ezeket az IP-címeket, vagy a készlet foglalási hibákat tapasztalhat.
- Semmilyen biztonsági szabályzat vagy erőforrás-zárolás nem korlátozhatja a felhasználók nyilvános IP-címhez való hozzáférését.
- A készlethez megadott nyilvános IP-címek számának elég nagynak kell lennie ahhoz, hogy a készlethez megcélzott virtuális gépek számát elférje. Ennek legalább a **készlet targetDedicatedNodes** és    **targetLowPriorityNodes**   tulajdonságának összegének kell lennie. Ha nincs elég IP-cím, a készlet részlegesen lefoglalja a számítási csomópontokat, és átméretezési hiba történik. A Batch jelenleg 100 virtuális géphez egy nyilvános IP-címet használ.
- Mindig legyen egy további nyilvános IP-címpuffere. Javasoljuk, hogy adjon hozzá legalább egy további nyilvános IP-címet, vagy a készlethez adott nyilvános IP-címek körülbelül 10%-át( amelyik nagyobb). Ez a további puffer segít a Batch számára a belső optimalizálásban a leskálálás során, valamint gyorsabb felskálást is lehetővé téve a sikertelen fel- vagy leskálálás után.
- A készlet létrehozása után nem használhatja és nem módosíthatja a készlet által használt nyilvános IP-címek listáját. Ha módosítania kell a listát, törölnie kell a készletet, majd újra létre kell hoznia.

## <a name="create-a-batch-pool-with-public-ip-addresses"></a>Batch-készlet létrehozása nyilvános IP-címekkel

Az alábbi példa bemutatja, hogyan használható a [Azure Batch szolgáltatás REST API-ja](/rest/api/batchservice/pool/add) nyilvános IP-címeket használó készlet létrehozására.

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

- Ismerje meg a Batch szolgáltatás munkafolyamatát és [az olyan elsődleges erőforrásokat,](batch-service-workflow-features.md) mint a készletek, csomópontok, feladatok és tevékenységek.
- Ismerje [meg, hogyan lehet készletet létrehozni egy Azure-beli virtuális hálózat alhálózatán.](batch-virtual-network.md)
- Útmutató nyilvános [IP-Azure Batch nélküli készlet létrehozásához.](./batch-pool-no-public-ip-address.md)
