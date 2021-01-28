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
ms.openlocfilehash: 4d2f339f5a1339b5f249172170fed54c91f1dc24
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98927195"
---
# <a name="quickstart-create-a-public-ip-address-using-azure-cli"></a>Rövid útmutató: nyilvános IP-cím létrehozása az Azure CLI használatával

Ez a cikk bemutatja, hogyan hozható létre nyilvános IP-cím erőforrás az Azure CLI használatával. További információ arról, hogy mely erőforrásokhoz lehet társítva, az alapszintű és a standard SKU és az egyéb kapcsolódó információk között a [nyilvános IP-címek](./public-ip-addresses.md)című részben talál.  Ebben a példában csak IPv4-címekre fogunk összpontosítani; További információ az IPv6-címekről: [IPv6 for Azure VNet](./ipv6-overview.md).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.28 verziójára vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot az [az Group Create](/cli/azure/group#az-group-create) **myResourceGroup** nevű **eastus2** helyen.

```azurecli-interactive
  az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-public-ip"></a>Nyilvános IP-cím létrehozása

---
# <a name="standard-sku---using-zones"></a>[**Szabványos SKU – zónák használatával**](#tab/option-create-public-ip-standard-zones)

>[!NOTE]
>A következő parancs az API 2020-08-01-es vagy újabb verziójával működik.  Az aktuálisan használt API-verzióval kapcsolatos további információkért tekintse meg az [erőforrás-szolgáltatók és típusok](../azure-resource-manager/management/resource-providers-and-types.md)című témakört.

Az [az Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) paranccsal hozzon létre egy standard zóna – REDUNDÁNS nyilvános IP-címet a **myStandardZRPublicIP** nevű **myResourceGroup**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardZRPublicIP \
    --sku Standard
    --zone 1,2,3
```
> [!IMPORTANT]
> Az 2020-08-01-nál régebbi API-verziók esetében a fenti parancs futtatásához a zóna paraméter megadása nélkül hozzon létre egy zóna-redundáns IP-címet. 
>

Ahhoz, hogy a **myResourceGroup** nevű **myStandardZonalPublicIP** -ben hozzon létre egy 2. zóna szabványos, a (z) rendszerbeli, a következő parancsot:

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myStandardZonalPublicIP \
    --sku Standard \
    --zone 2
```

Vegye figyelembe, hogy a zónák fenti beállításai csak a [Availability Zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)-vel rendelkező régiókban érvényesek.

# <a name="standard-sku---no-zones"></a>[**Szabványos SKU – nincsenek zónák**](#tab/option-create-public-ip-standard)

>[!NOTE]
>A következő parancs az API 2020-08-01-es vagy újabb verziójával működik.  Az aktuálisan használt API-verzióval kapcsolatos további információkért tekintse meg az [erőforrás-szolgáltatók és típusok](../azure-resource-manager/management/resource-providers-and-types.md)című témakört.

Az [az Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) paranccsal hozzon létre egy szabványos nyilvános IP-címet, amely egy **myStandardPublicIP** nevű, nem zónákból álló erőforrás a **myResourceGroup**-ben.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardPublicIP \
    --sku Standard
```
Ez a kijelölés minden régióban érvényes, és a szabványos nyilvános IP-címek alapértelmezett kiválasztása [Availability Zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)nélküli régiókban.

# <a name="basic-sku"></a>[**Alapszintű termékváltozat**](#tab/option-create-public-ip-basic)

Az [az Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) paranccsal hozzon létre egy **myBasicPublicIP** nevű alapszintű statikus nyilvános IP-címet a **myResourceGroup**-ben.  Az alapszintű nyilvános IP-címek nem rendelkeznek a rendelkezésre állási zónák fogalmával.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myBasicPublicIP \
    --sku Basic \
    --allocation-method Static
```
Ha az IP-cím időbeli változása elfogadható, a **dinamikus** IP-hozzárendelés kiválasztható úgy, hogy a kiosztási metódust a "dinamikus" értékre módosítja.

---

## <a name="additional-information"></a>További információ 

A fent felsorolt változókról további információt a [nyilvános IP-címek kezelése](./virtual-network-public-ip-address.md#create-a-public-ip-address)című témakörben talál.

## <a name="next-steps"></a>Következő lépések
- Rendeljen [hozzá egy nyilvános IP-címet egy virtuális géphez](./associate-public-ip-address-vm.md#azure-portal).
- További információ az Azure [-beli nyilvános IP-címekről](./public-ip-addresses.md#public-ip-addresses) .
- További információ az összes [nyilvános IP-cím beállításról](virtual-network-public-ip-address.md#create-a-public-ip-address).
