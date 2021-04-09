---
title: Nyilvános IP-címek frissítése
titleSuffix: Azure Virtual Network
description: A nyilvános IP-címek frissítése az alapszintről a standard szintre.
services: virtual-network
documentationcenter: na
author: blehr
editor: ''
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/08/2020
ms.author: blehr
ms.custom: references_regions
ms.openlocfilehash: 33c767d847d9e70e95b3ee1648be7852aa5cec98
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100522886"
---
# <a name="upgrade-public-ip-addresses"></a>Nyilvános IP-címek frissítése

Az Azure-beli nyilvános IP-címek SKU--vagy alapszintű vagy standard szintűek, amelyek a funkciójuk szempontjait határozzák meg (beleértve a kiosztási módszert, a szolgáltatások támogatását és a hozzájuk társított erőforrásokat is). 

Ebben a cikkben a következő forgatókönyveket tekintjük át:
* Alapszintű SKU nyilvános IP-címének frissítése standard SKU nyilvános IP-re (a PowerShell vagy a parancssori felület használatával)
* Klasszikus Azure-Fenntartott IP migrálása Azure Resource Manager alapszintű SKU nyilvános IP-címére

## <a name="upgrade-public-ip-address-from-basic-to-standard-sku"></a>Nyilvános IP-cím frissítése alapszintről standard SKU-ra

Nyilvános IP-cím frissítéséhez nem köthető erőforráshoz ( [ebben az oldalon](./virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address) további információt talál a nyilvános IP-címek társításának megszüntetéséről).

>[!IMPORTANT]
>Az alapszintről standard SKU-ra frissített nyilvános IP-címek továbbra is nem rendelkeznek garantált [rendelkezésre állási zónákkal](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).  Gondoskodjon róla, hogy ez ne legyen figyelembe véve, amikor kiválasztja, hogy mely erőforrások legyenek társítva az IP-címhez.

---
# <a name="basic-to-standard---powershell"></a>[**Alapszintű, standard – PowerShell**](#tab/option-upgrade-powershell)

Az alábbi példa egy alapszintű SKU nyilvános IP-cím előző létrehozását feltételezi, amely az [ezen a lapon](./create-public-ip-powershell.md?tabs=option-create-public-ip-basic) megadott példát használja a **myResourceGroup** alapszintű nyilvános IP- **myBasicPublicIP** .

Az IP-cím frissítéséhez egyszerűen hajtsa végre az alábbi parancsokat a PowerShell használatával.  Vegye figyelembe, hogy az IP-cím már statikusan le van foglalva, ezért a szakasz kihagyható.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$name = 'myBasicPublicIP'
$newsku = 'Standard'
$pubIP = Get-AzPublicIpAddress -name $name -ResourceGroupName $rg

## This section is only needed if the Basic IP is not already set to Static ##
$pubIP.PublicIpAllocationMethod = 'Static'
Set-AzPublicIpAddress -PublicIpAddress $pubIP

## This section is for conversion to Standard ##
$pubIP.Sku.Name = $newsku
Set-AzPublicIpAddress -PublicIpAddress $pubIP
```

# <a name="basic-to-standard---cli"></a>[**Alapszintű – standard – parancssori felület**](#tab/option-upgrade-cli)

Az alábbi példa egy alapszintű SKU nyilvános IP-cím előző létrehozását feltételezi, amely az [ezen a lapon](./create-public-ip-cli.md?tabs=option-create-public-ip-basic) megadott példát használja a **myResourceGroup** alapszintű nyilvános IP- **myBasicPublicIP** .

Az IP-cím frissítéséhez egyszerűen hajtsa végre az alábbi parancsokat az Azure CLI használatával.  Vegye figyelembe, hogy az IP-cím már statikusan le van foglalva, ezért a szakasz kihagyható.

```azurecli-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$name = 'myBasicPublicIP'

## This section is only needed if the Basic IP is not already set to Static ##
az network public-ip update \
--resource-group $rg \
--name $name \
--allocation-method Static 

## This section is for conversion to Standard ##
az network public-ip update \
--resource-group $rg \
--name $name \
--sku Standard
```
---

## <a name="upgrade-migrate-a-classic-reserved-ip-to-a-static-public-ip"></a>Klasszikus Fenntartott IP frissítése statikus nyilvános IP-címekre

A Azure Resource Manager új képességeinek kihasználásához áttelepítheti a meglévő nyilvános statikus IP-címet – a fenntartott IP-címeket – a klasszikus modellből a modern Azure Resource Manager modellbe.  Az áttelepített nyilvános IP-cím egy alapszintű SKU-típus lesz.


---

# <a name="reserved-to-basic---powershell"></a>[**Alapszintű – PowerShell számára fenntartva**](#tab/option-migrate-powershell)

Az alábbi példa egy klasszikus Azure Fenntartott IP **myReservedIP** korábbi létrehozását feltételezi a **myResourceGroup**-ben. A Migrálás egy másik előfeltétele annak biztosítása, hogy a Azure Resource Manager előfizetés regisztrálva legyen az áttelepítéshez. Ezt az [oldal](../virtual-machines/migration-classic-resource-manager-ps.md)3. és 4. lépésében részletesen tárgyaljuk.

A Fenntartott IP migrálása érdekében hajtsa végre az alábbi parancsokat a PowerShell használatával.  Vegye figyelembe, hogy ha az IP-cím nincs társítva egyetlen szolgáltatáshoz sem (az alatta van egy **myService** nevű szolgáltatás), akkor a lépés kihagyható.

```azurepowershell-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$serviceName = 'myService'
Remove-AzureReservedIPAssociation -ReservedIPName $name -ServiceName $service

$validate = Move-AzureReservedIP -ReservedIPName $name -Validate
$validate.ValidationMessages
```
Az előző parancs megjeleníti az áttelepítést tiltó figyelmeztetéseket és hibákat. Ha az érvényesítés sikeres, a következő lépések végrehajtásával előkészítheti és véglegesítheti az áttelepítést:
```azurepowershell-interactive
Move-AzureReservedIP -ReservedIPName $name -Prepare
Move-AzureReservedIP -ReservedIPName $name -Commit
```
A rendszer létrehoz egy új erőforráscsoportot a Azure Resource Managerben az áttelepített Fenntartott IP nevével (a fenti példában az erőforráscsoport **myReservedIP-migrálása** lenne).

# <a name="reserved-to-basic---cli"></a>[**Alapszintű – parancssori felület**](#tab/option-migrate-cli)

Az alábbi példa egy klasszikus Azure Fenntartott IP **myReservedIP** korábbi létrehozását feltételezi a **myResourceGroup**-ben. A Migrálás egy másik előfeltétele annak biztosítása, hogy a Azure Resource Manager előfizetés regisztrálva legyen az áttelepítéshez. Ezt az [oldal](../virtual-machines/migration-classic-resource-manager-cli.md)3. és 4. lépésében részletesen tárgyaljuk.

A Fenntartott IP migrálása érdekében hajtsa végre az alábbi parancsokat az Azure CLI használatával.  Vegye figyelembe, hogy az IP-cím nincs társítva egyetlen szolgáltatáshoz sem (a **myService** és az üzembe helyezési **myDeployment** nevű szolgáltatás), ezt a lépést kihagyhatja.

```azurecli-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$service = 'myService'
$deployment = 'myDeployment'
azure network reserved-ip disassociate $name $service $deployment

azure network reserved-ip validate-migration $name
```
Az előző parancs megjeleníti az áttelepítést tiltó figyelmeztetéseket és hibákat. Ha az érvényesítés sikeres, a következő lépések végrehajtásával előkészítheti és véglegesítheti az áttelepítést:
```azurecli-interactive
azure network reserved-ip prepare-migration $name
azure network reserved-ip commit-migration $name
```
A rendszer létrehoz egy új erőforráscsoportot a Azure Resource Managerben az áttelepített Fenntartott IP nevével (a fenti példában az erőforráscsoport **myReservedIP-migrálása** lenne).

---

## <a name="limitations"></a>Korlátozások

* Egy alapszintű nyilvános IP-cím frissítéséhez nem társítható egyetlen Azure-erőforráshoz sem.  A nyilvános IP-címek hozzárendelésével kapcsolatos további információkért tekintse át [ezt a lapot](./virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address) .  Hasonlóképpen, a Fenntartott IP átmigrálása nem rendelhető hozzá semmilyen felhőalapú szolgáltatáshoz.  A fenntartott IP-címek hozzárendelésével kapcsolatos további információkért tekintse át [ezt a lapot](./remove-public-ip-address-vm.md) .  
* Az alapszintről standard SKU-ra frissített nyilvános IP-címek továbbra is nem rendelkeznek [rendelkezésre állási zónákkal](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) , ezért nem társíthatók olyan Azure-erőforrásokkal, amelyek akár zónában redundáns, akár nem.  Vegye figyelembe, hogy ez csak a rendelkezésre állási zónákat tartalmazó régiókra vonatkozik.
* A standard és az alapszintű érték között nem lehetséges a visszalépés.

## <a name="next-steps"></a>Következő lépések

- További információ az Azure-beli [nyilvános IP-címekről](./public-ip-addresses.md#public-ip-addresses) , beleértve az SKU-típusok közötti különbséget, valamint a [nyilvános IP-cím beállításait](virtual-network-public-ip-address.md#create-a-public-ip-address).
- Ismerje meg, hogyan [frissítheti az Azure nyilvános Load balancert alapszintről standard szintre](../load-balancer/upgrade-basic-standard.md).
- Ismerje meg a [klasszikus Azure-beli fenntartott IP-címeket](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) és [a klasszikus erőforrások áttelepítését Azure Resource Managerra](../virtual-machines/migration-classic-resource-manager-overview.md).
