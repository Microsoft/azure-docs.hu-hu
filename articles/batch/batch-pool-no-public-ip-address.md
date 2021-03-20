---
title: Azure Batch-készlet létrehozása nyilvános IP-címek nélkül
description: Megtudhatja, hogyan hozhat létre nyilvános IP-címek nélküli készletet
author: pkshultz
ms.topic: how-to
ms.date: 12/9/2020
ms.author: peshultz
ms.custom: references_regions
ms.openlocfilehash: 806e85fca0a509d56e248fc7779fba0f0a59a61d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97007670"
---
# <a name="create-an-azure-batch-pool-without-public-ip-addresses"></a>Azure Batch-készlet létrehozása nyilvános IP-címek nélkül

Azure Batch-készlet létrehozásakor a virtuális gép konfigurációs készletét nyilvános IP-cím nélkül is kiépítheti. Ez a cikk bemutatja, hogyan állíthat be egy batch-készletet nyilvános IP-címek nélkül.

## <a name="why-use-a-pool-without-public-ip-addresses"></a>Miért érdemes nyilvános IP-címek nélküli készletet használni?

Alapértelmezés szerint a Azure Batch virtuálisgép-konfigurációs készlet összes számítási csomópontja egy nyilvános IP-címet kap. Ezt a címeket a Batch szolgáltatás használja a feladatok és a számítási csomópontokkal folytatott kommunikációhoz, beleértve az internet felé irányuló kimenő hozzáférést is.

Ha korlátozni szeretné ezen csomópontok elérését, és csökkenti a csomópontok internetről való felderíthetővé tételét, nyilvános IP-címek nélkül is kiépítheti a készletet.

> [!IMPORTANT]
> A nyilvános IP-címek nélküli készletek támogatása a Azure Batch jelenleg nyilvános előzetes verzióban érhető el a következő régiókban: Közép-Franciaország, Kelet-Ázsia, USA nyugati középső régiója, USA déli középső régiója, USA 2. nyugati régiója, USA nyugati régiója, Észak-Európa, USA 2. keleti régiója, USA középső régiója, Nyugat-Kelet-Ausztrália Európa
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

- **Hitelesítés**. Ha nyilvános IP-címek nélküli készletet szeretne használni egy [virtuális hálózaton](./batch-virtual-network.md)belül, a Batch-ügyfél API-nak Azure Active Directory (ad) hitelesítést kell használnia. Az Azure AD Azure Batch-támogatásának dokumentációjáért lásd a [Batch szolgáltatás Active Directoryval történő hitelesítésével](batch-aad-auth.md) foglalkozó témakört. Ha nem a virtuális hálózaton belül hozza létre a készletet, akkor az Azure AD-hitelesítés vagy a kulcs alapú hitelesítés használható.

- **Egy Azure-VNet**. Ha [virtuális hálózatban](batch-virtual-network.md)hozza létre a készletet, kövesse ezeket a követelményeket és konfigurációkat. Egy vagy több alhálózattal rendelkező VNet előkészítéséhez használhatja a Azure Portal, Azure PowerShell, az Azure Command-Line felületét (CLI) vagy más módszert.
  - A virtuális hálózatnak a Batch-fiókkal megegyező előfizetésben és régióban kell lennie.
  - A készlethez meghatározott alhálózatnak elegendő hozzá nem rendelt IP-címmel kell rendelkeznie ahhoz, hogy helyet tudjon adni a készlethez kijelölt számú virtuális gépnek. Ez a szám a készlet `targetDedicatedNodes` és `targetLowPriorityNodes` tulajdonságának összege. Ha az alhálózaton nincs elegendő hozzá nem rendelt IP-cím, akkor a készlet részlegesen lefoglalja a számítási csomópontokat, és átméretezési hiba következik be.
  - Le kell tiltania a Private link Service és a végpont hálózati házirendjeit. Ezt az Azure CLI használatával végezheti el: ```az network vnet subnet update --vnet-name <vnetname> -n <subnetname> --resouce-group <resourcegroup> --disable-private-endpoint-network-policies --disable-private-link-service-network-policies```

> [!IMPORTANT]
> Minden 100 dedikált vagy alacsony prioritású csomópont esetében a Batch egy privát kapcsolati szolgáltatást és egy Load balancert foglal le. Ezekre az erőforrásokra az előfizetésben meghatározott [erőforráskvóták](../azure-resource-manager/management/azure-subscription-service-limits.md) vonatkoznak. Nagyméretű készletek esetén előfordulhat, hogy egy vagy több ilyen erőforráshoz [kvótát kell emelni](batch-quota-limit.md#increase-a-quota) . Emellett nem kell erőforrás-zárolást alkalmazni a Batch által létrehozott összes erőforrásra, mivel ez megakadályozhatja az erőforrások törlését a felhasználó által kezdeményezett műveletek eredményeképpen, például a készlet törlését vagy a nulla értékre való átméretezést.

## <a name="current-limitations"></a>Aktuális korlátozások

1. A nyilvános IP-címek nélküli készleteknek a virtuális gép konfigurációját kell használniuk, és nem Cloud Services konfigurációt.
1. A Batch számítási csomópontok [Egyéni végpont-konfigurációja](pool-endpoint-configuration.md) nem működik nyilvános IP-címek nélküli készletekkel.
1. Mivel nincsenek nyilvános IP-címek, a [saját megadott nyilvános IP-címei nem használhatók](create-pool-public-ip.md) ilyen típusú készlettel.

## <a name="create-a-pool-without-public-ip-addresses-in-the-azure-portal"></a>Nyilvános IP-címek nélküli készlet létrehozása a Azure Portal

1. Az Azure portálon lépjen Batch-fiókjára.
1. A bal oldali **Beállítások** ablakban válassza a **készletek** elemet.
1. A **készletek** ablakban válassza a **Hozzáadás** lehetőséget.
1. A **készlet hozzáadása** ablakban válassza ki a használni kívánt beállítást a **rendszerkép típusa** legördülő listából.
1. Válassza ki a rendszerképének megfelelő **közzétevőt/ajánlatot/SKU-** t.
1. Megadhatja a fennmaradó szükséges beállításokat, beleértve a **csomópont méretét**, a **célként kijelölt csomópontokat** és az **alacsony prioritású csomópontokat**, valamint a kívánt választható beállításokat.
1. Opcionálisan kiválaszthatja a használni kívánt virtuális hálózatot és alhálózatot. A virtuális hálózatnak ugyanabban az erőforráscsoporthoz kell tartoznia, mint a létrehozandó készletnek.
1. Az **IP-cím kiépítési típusa** területen válassza a **NoPublicIPAddresses** lehetőséget.

![Képernyőfelvétel a készlet hozzáadása képernyőről a kiválasztott NoPublicIPAddresses.](./media/batch-pool-no-public-ip-address/create-pool-without-public-ip-address.png)

## <a name="use-the-batch-rest-api-to-create-a-pool-without-public-ip-addresses"></a>A Batch REST API használatával hozzon létre egy nyilvános IP-címek nélküli készletet

Az alábbi példa azt mutatja be, hogyan használható a [Azure Batch REST API](/rest/api/batchservice/pool/add) egy nyilvános IP-címeket használó készlet létrehozásához.

### <a name="rest-api-uri"></a>REST API URI

```http
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

### <a name="request-body"></a>A kérés törzse

```json
"pool": {
     "id": "pool2",
     "vmSize": "standard_a1",
     "virtualMachineConfiguration": {
          "imageReference": {
               "publisher": "Canonical",
               "offer": "UbuntuServer",
               "sku": "16.040-LTS"
          },
     "nodeAgentSKUId": "batch.node.ubuntu 16.04"
     }
     "networkConfiguration": {
          "subnetId": "/subscriptions/<your_subscription_id>/resourceGroups/<your_resource_group>/providers/Microsoft.Network/virtualNetworks/<your_vnet_name>/subnets/<your_subnet_name>",
          "publicIPAddressConfiguration": {
               "provision": "NoPublicIPAddresses"
          }
     },
     "resizeTimeout": "PT15M",
     "targetDedicatedNodes": 5,
     "targetLowPriorityNodes": 0,
     "taskSlotsPerNode": 3,
     "taskSchedulingPolicy": {
          "nodeFillType": "spread"
     },
     "enableAutoScale": false,
     "enableInterNodeCommunication": true,
     "metadata": [
    {
      "name": "myproperty",
      "value": "myvalue"
    }
       ]
}
```

## <a name="outbound-access-to-the-internet"></a>Kimenő hozzáférés az internethez

Nyilvános IP-címek nélküli készlet esetén a virtuális gépek nem férhetnek hozzá a nyilvános internethez, hacsak nem konfigurálja megfelelően a hálózati beállításokat, például a [virtuális hálózati NAT](../virtual-network/nat-overview.md)használatával. Vegye figyelembe, hogy a NAT csak a virtuális hálózatban lévő virtuális gépekről engedélyezi a kimenő hozzáférést az internethez. A Batch által létrehozott számítási csomópontok nem lesznek nyilvánosan elérhetők, mert nincsenek hozzárendelve nyilvános IP-címek.

A kimenő kapcsolatok egy másik módja a felhasználó által megadott útvonal (UDR) használata. Ez lehetővé teszi, hogy a forgalmat egy olyan proxykiszolgálón keresztül irányítsa, amely nyilvános internet-hozzáféréssel rendelkezik.

## <a name="next-steps"></a>Következő lépések

- További információ a [készletek virtuális hálózatban való létrehozásáról](batch-virtual-network.md).
- Ismerje meg, hogyan [használhatók a privát végpontok a Batch-fiókokkal](private-connectivity.md).
