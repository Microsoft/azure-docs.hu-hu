---
title: Nyilvános IP-cím létrehozása – Azure CLI
description: Ismerje meg, hogyan hozhat létre nyilvános IP-címet az Azure CLI használatával
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: ff0dbf31f6f428b23e00f9366d55703416847b90
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767690"
---
# <a name="quickstart-create-a-public-ip-address-using-azure-cli"></a>Rövid útmutató: Nyilvános IP-cím létrehozása az Azure CLI használatával

Ez a cikk bemutatja, hogyan hozhat létre nyilvános IP-címerőforrást az Azure CLI használatával. Az ezzel társított erőforrásokról, az alapszintű és a standard termékváltozat közötti különbségről és az egyéb kapcsolódó információkról a Nyilvános [IP-címek](./public-ip-addresses.md)oldalon található további információ.  Ebben a példában csak IPv4-címekre összpontosítunk; További információ az IPv6-címekről: [IPv6 for Azure VNet](./ipv6-overview.md).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.28-as vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot [az az group create myResourceGroup](/cli/azure/group#az_group_create) névvel az **eastus2 helyen.** 

```azurecli-interactive
  az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-public-ip"></a>Nyilvános IP-cím létrehozása

---
# <a name="standard-sku---using-zones"></a>[**Standard termékváltozat – Zónák használata**](#tab/option-create-public-ip-standard-zones)

>[!NOTE]
>A következő parancs a 2020-08-01-es vagy újabb API-verzióval működik.  A jelenleg használt API-verzióval kapcsolatos további információkért tekintse meg az erőforrás-szolgáltatókat és [a típusokat.](../azure-resource-manager/management/resource-providers-and-types.md)

Az [az network public-ip create használatával](/cli/azure/network/public-ip#az_network_public_ip_create) hozzon létre egy standard zónaredundáns nyilvános IP-címet **myStandardZRPublicIP** névvel a **myResourceGroup csoportban.**

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardZRPublicIP \
    --sku Standard
    --zone 1 2 3
```
> [!IMPORTANT]
> A 2020-08-01-esnél régebbi API-verziók esetén futtassa a fenti parancsot zónaparaméter megadása nélkül egy zónaredundáns IP-cím létrehozásához. 
>

Ha a **myResourceGroup** csoportban egy **myStandardZonalPublicIP** nevű standard zónai nyilvános IP-címet 2. zóna, használja a következő parancsot:

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myStandardZonalPublicIP \
    --sku Standard \
    --zone 2
```

Vegye figyelembe, hogy a zónák fenti beállításai csak olyan régiókban használhatók, amelyekben a [Availability Zones.](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)

# <a name="standard-sku---no-zones"></a>[**Standard termékváltozat – Zónák nélkül**](#tab/option-create-public-ip-standard)

>[!NOTE]
>A következő parancs a 2020-08-01-es vagy újabb API-verzióval működik.  A jelenleg használt API-verzióval kapcsolatos további információkért tekintse meg az erőforrás-szolgáltatókat és [a típusokat.](../azure-resource-manager/management/resource-providers-and-types.md)

Az [az network public-ip create használatával](/cli/azure/network/public-ip#az_network_public_ip_create) hozzon létre egy standard nyilvános IP-címet nem zónaális erőforrásként **myStandardPublicIP** névvel a **myResourceGroup csoportban.**

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardPublicIP \
    --sku Standard
```
Ez a választás minden régióban érvényes, és az alapértelmezett beállítás a Standard nyilvános IP-címekhez olyan régiókban, amelyekben nincs [Availability Zones.](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)

# <a name="basic-sku"></a>[**Alapszintű termékváltozat**](#tab/option-create-public-ip-basic)

Az [az network public-ip create használatával](/cli/azure/network/public-ip#az_network_public_ip_create) hozzon létre egy alapszintű statikus nyilvános IP-címet **myBasicPublicIP** névvel a **myResourceGroup csoportban.**  Az alapszintű nyilvános IP-knek nincs közük a rendelkezésre állási zónák fogalmához.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myBasicPublicIP \
    --sku Basic \
    --allocation-method Static
```
Ha elfogadható, hogy az IP-cím  idővel megváltozik, a dinamikus IP-hozzárendelés a lefoglalási módszer "Dinamikus" beállításra való módosításával választható ki.

---

## <a name="additional-information"></a>További információ 

A fent felsorolt egyes változókról a Nyilvános [IP-címek kezelésecímek kezelésével kapcsolatos oldalon talál további információt.](./virtual-network-public-ip-address.md#create-a-public-ip-address)

## <a name="next-steps"></a>Következő lépések
- Nyilvános [IP-cím társítása egy virtuális géphez.](./associate-public-ip-address-vm.md#azure-portal)
- További információ az [Azure-beli nyilvános](./public-ip-addresses.md#public-ip-addresses) IP-címekről.
- További információ az összes [nyilvános IP-címbeállításról.](virtual-network-public-ip-address.md#create-a-public-ip-address)
