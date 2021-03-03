---
title: Nyilvános IP-Azure PowerShell létrehozása
description: Megtudhatja, hogyan hozhat létre nyilvános IP-címet a Azure PowerShell használatával
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
ms.openlocfilehash: f61d45b6c46830064c3b58608e2eca0787d559c2
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101675112"
---
# <a name="quickstart-create-a-public-ip-address-using-azure-powershell"></a>Rövid útmutató: nyilvános IP-cím létrehozása Azure PowerShell használatával

Ez a cikk bemutatja, hogyan hozhat létre egy nyilvános IP-cím-erőforrást a Azure PowerShell használatával. További információ arról, hogy mely erőforrásokhoz lehet társítva, az alapszintű és a standard SKU és az egyéb kapcsolódó információk között a [nyilvános IP-címek](./public-ip-addresses.md)című részben talál.  Ebben a példában csak IPv4-címekre fogunk összpontosítani; További információ az IPv6-címekről: [IPv6 for Azure VNet](./ipv6-overview.md).

## <a name="prerequisites"></a>Előfeltételek

- Helyileg telepített Azure PowerShell vagy Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, ehhez a cikkhez az Azure PowerShell-modul 5.4.1-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor azt is futtatnia kell, `Connect-AzAccount` hogy létrehozza az Azure-hoz való kapcsolódást.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) nevű **myResourceGroup** a **eastus2** helyen.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'

New-AzResourceGroup -Name $rg -Location $loc
```
## <a name="create-public-ip"></a>Nyilvános IP-cím létrehozása

---
# <a name="standard-sku---using-zones"></a>[**Szabványos SKU – zónák használatával**](#tab/option-create-public-ip-standard-zones)

>[!NOTE]
>A következő parancs az az. Network modul 4.5.0 vagy újabb verziójára működik.  A jelenleg használt PowerShell-modulokkal kapcsolatos további információkért tekintse meg a [PowerShellGet dokumentációját](/powershell/module/powershellget/?view=powershell-7.1).

A [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) használatával hozzon létre egy **myStandardZRPublicIP** nevű standard zóna-redundáns nyilvános IP-címet a **myResourceGroup**-ben.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardZRPublicIP'
$sku = 'Standard'
$alloc = 'Static'
$zone = 1,2,3

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku -zone $zone
```
> [!IMPORTANT]
> Az az. Network moduloknál régebbi 4.5.0 esetében futtassa a fenti parancsot a zóna paraméter megadása nélkül, és hozzon létre egy zóna-redundáns IP-címet. 
>

Ahhoz, hogy a **myResourceGroup** nevű **myStandardZonalPublicIP** -ben hozzon létre egy 2. zóna szabványos, a (z) rendszerbeli, a következő parancsot:

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardZonalPublicIP'
$sku = 'Standard'
$alloc = 'Static'
$zone = 2

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku -zone $zone
```

Vegye figyelembe, hogy a zónák fenti beállításai csak a [Availability Zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)-vel rendelkező régiókban érvényesek.

# <a name="standard-sku---no-zones"></a>[**Szabványos SKU – nincsenek zónák**](#tab/option-create-public-ip-standard)

>[!NOTE]
>A következő parancs az az. Network modul 4.5.0 vagy újabb verziójára működik.  A jelenleg használt PowerShell-modulokkal kapcsolatos további információkért tekintse meg a [PowerShellGet dokumentációját](/powershell/module/powershellget/?view=powershell-7.1).

A [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) használatával hozzon létre egy szabványos nyilvános IP-címet egy **myStandardPublicIP** nevű, a **myResourceGroup**-ben található nem zónákból álló erőforrásként.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardPublicIP'
$sku = 'Standard'
$alloc = 'Static'

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku
```

Ez a kijelölés minden régióban érvényes, és a szabványos nyilvános IP-címek alapértelmezett kiválasztása [Availability Zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)nélküli régiókban.

# <a name="basic-sku"></a>[**Alapszintű termékváltozat**](#tab/option-create-public-ip-basic)

A [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) használatával hozzon létre egy **myBasicPublicIP** nevű alapszintű statikus nyilvános IP-címet a **myResourceGroup**-ben.  Az alapszintű nyilvános IP-címek nem rendelkeznek a rendelkezésre állási zónák fogalmával.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myBasicPublicIP'
$sku = 'Basic'
$alloc = 'Static'

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku
```
Ha az IP-cím időbeli változása elfogadható, a **dinamikus** IP-hozzárendelés kiválasztható úgy, hogy a AllocationMethod a "dinamikus" értékre módosítja.

---

## <a name="additional-information"></a>További információ 

A fent felsorolt változókról további információt a [nyilvános IP-címek kezelése](./virtual-network-public-ip-address.md#create-a-public-ip-address)című témakörben talál.

## <a name="next-steps"></a>Következő lépések
- [Nyilvános IP-cím hozzárendelése virtuális géphez](./associate-public-ip-address-vm.md#azure-portal)
- További információ az Azure [-beli nyilvános IP-címekről](./public-ip-addresses.md#public-ip-addresses) .
- További információ az összes [nyilvános IP-cím beállításról](virtual-network-public-ip-address.md#create-a-public-ip-address).