---
title: Nyilvános IP-címek frissítése
titleSuffix: Azure Virtual Network
description: Frissítse a nyilvános IP-címeket Alapszintűről Standardra.
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
ms.openlocfilehash: 74df338fd888bd7f654ddfc2fc5f9dddf10e84ab
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598414"
---
# <a name="upgrade-public-ip-addresses"></a>Nyilvános IP-címek frissítése

Az Azure-beli nyilvános IP-címek alapszintű vagy standard termékváltozattal hozhatók létre, amely meghatározza azok funkcióinak aspektusait (beleértve a lefoglalási módszert, a funkciók támogatását és a hozzájuk társítható erőforrásokat). 

Ez a cikk a következő forgatókönyveket vizsgálja felül:
* Alapszintű termékváltozat nyilvános IP-címének frissítése standard termékváltozatú nyilvános IP-címre (a PowerShell vagy a parancssori felület használatával)
* Klasszikus Azure-beli virtuális Fenntartott IP áttelepítése Azure Resource Manager alapszintű termékváltozatú nyilvános IP-címre

## <a name="upgrade-public-ip-address-from-basic-to-standard-sku"></a>Nyilvános IP-cím frissítése alapszintűről Standard termékváltozatra

A nyilvános IP-címek frissítésének érdekében nem lehet erőforráshoz társítva (a nyilvános IP-címek társításának társításával kapcsolatos további információkért tekintse meg ezt az oldalt). [](./virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address)

>[!IMPORTANT]
>Az Alapszintűről Standard termékváltozatra frissített nyilvános IP-k továbbra sem garantálnak [garantált rendelkezésre állási zónákat.](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)  Ügyeljen rá, hogy ezt ne feledje, amikor kiválasztja, mely erőforrásokkal társítja az IP-címet.

---
# <a name="basic-to-standard---powershell"></a>[**Alapszintűről Standardra – PowerShell**](#tab/option-upgrade-powershell)

Az alábbi példa feltételezi egy alapszintű termékváltozat nyilvános IP-címének létrehozását, az ezen a lapon megadott példán keresztül, a [](./create-public-ip-powershell.md?tabs=option-create-public-ip-basic) **myResourceGroup** **csoportban található myBasicPublicIP** alapszintű nyilvános IP-címmel.

Az IP-cím frissítésének érdekében egyszerűen hajtsa végre az alábbi parancsokat a PowerShell használatával.  Vegye figyelembe, hogy ha az IP-cím már statikusan ki van osztva, akkor ez a szakasz kihagyható.

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

# <a name="basic-to-standard---cli"></a>[**Alapszintűről Standardra – CLI**](#tab/option-upgrade-cli)

Az alábbi példa feltételezi egy alapszintű termékváltozat nyilvános IP-címének létrehozását, az ezen a lapon megadott példán keresztül, a [](./create-public-ip-cli.md?tabs=option-create-public-ip-basic) **myResourceGroup** **csoportban található myBasicPublicIP** alapszintű nyilvános IP-címmel.

Az IP-cím frissítésének érdekében egyszerűen hajtsa végre az alábbi parancsokat az Azure CLI használatával.  Vegye figyelembe, hogy ha az IP-cím már statikusan ki van osztva, akkor ez a szakasz kihagyható.

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

## <a name="upgrade-migrate-a-classic-reserved-ip-to-a-static-public-ip"></a>Klasszikus ip-cím frissítése Fenntartott IP nyilvános IP-címre

A szolgáltatás új képességeinek Azure Resource Manager meglévő nyilvános statikus IP-címeket – a fenntartott IP-címeket – a klasszikus modellből a modern ip-Azure Resource Manager át.  A migrált nyilvános IP-cím alapszintű termékváltozat-típus lesz.


---

# <a name="reserved-to-basic---powershell"></a>[**Alapszintű használatra fenntartva – PowerShell**](#tab/option-migrate-powershell)

Az alábbi példa feltételezi, hogy egy **myReservedIP** klasszikus Azure-Fenntartott IP a **myResourceGroup erőforráscsoportban.** A migrálás egy másik előfeltétele annak biztosítása, Azure Resource Manager előfizetés regisztrálva legyen a migrálásra. Erről részletesen a jelen oldal 3. és 4. [lépése ad részletesen.](../virtual-machines/migration-classic-resource-manager-ps.md)

Az alkalmazás áttelepítése Fenntartott IP az alábbi parancsokat a PowerShell használatával.  Vegye figyelembe, hogy ha az IP-cím nincs szolgáltatáshoz társítva (az alábbiakban található egy **myService** nevű szolgáltatás), akkor ez a lépés kihagyható.

```azurepowershell-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$serviceName = 'myService'
Remove-AzureReservedIPAssociation -ReservedIPName $name -ServiceName $service

$validate = Move-AzureReservedIP -ReservedIPName $name -Validate
$validate.ValidationMessages
```
Az előző parancs megjeleníti a migrálást blokkoló figyelmeztetéseket és hibákat. Ha az érvényesítés sikeres, a következő lépésekkel készítheti elő és véglegesheti a migrálást:
```azurepowershell-interactive
Move-AzureReservedIP -ReservedIPName $name -Prepare
Move-AzureReservedIP -ReservedIPName $name -Commit
```
A rendszer létrehoz egy új erőforráscsoportot a Azure Resource Manager Fenntartott IP migrált erőforráscsoport nevével (a fenti példában ez a **myReservedIP-Migrated** erőforráscsoport).

# <a name="reserved-to-basic---cli"></a>[**Alapszintű használatra fenntartva – CLI**](#tab/option-migrate-cli)

Az alábbi példa feltételezi, hogy egy **myReservedIP** klasszikus Azure-Fenntartott IP a **myResourceGroup erőforráscsoportban.** A migrálás egy másik előfeltétele annak biztosítása, Azure Resource Manager előfizetés regisztrálva legyen a migrálásra. Ezt részletesen a jelen oldal 3. és 4. [lépése ismerteti.](../virtual-machines/migration-classic-resource-manager-cli.md)

Az alkalmazás áttelepítése Fenntartott IP az alábbi parancsokat az Azure CLI használatával.  Vegye figyelembe, hogy ha az IP-cím nincs szolgáltatáshoz társítva (az alábbiakban egy **myService** nevű szolgáltatás és **egy myDeployment** üzemelő példány található), akkor ezt a lépést kihagyhatja.

```azurecli-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$service = 'myService'
$deployment = 'myDeployment'
azure network reserved-ip disassociate $name $service $deployment

azure network reserved-ip validate-migration $name
```
Az előző parancs megjeleníti a migrálást blokkoló figyelmeztetéseket és hibákat. Ha az érvényesítés sikeres, a következő lépésekkel készítheti elő és véglegesheti a migrálást:
```azurecli-interactive
azure network reserved-ip prepare-migration $name
azure network reserved-ip commit-migration $name
```
A rendszer létrehoz egy új erőforráscsoportot a Azure Resource Manager Fenntartott IP migrált erőforráscsoport nevével (a fenti példában ez a **myReservedIP-Migrated** erőforráscsoport).

---

## <a name="limitations"></a>Korlátozások

* Alapszintű nyilvános IP-cím frissítése esetén az nem társítható Azure-erőforrással.  Tekintse át [ezt az oldalt](./virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address) a nyilvános IP-k társításának társításának további információiért.  Hasonlóképpen, a felhőszolgáltatások Fenntartott IP nem társítható egy felhőszolgáltatással sem.  Tekintse át [ezt az oldalt](./remove-public-ip-address-vm.md) a fenntartott IP-k társításának társításának további információiért.  
* Az Alapszintűről Standard termékváltozatra frissített nyilvános IP-k továbbra sem garantálnak [garantált rendelkezésre állási zónákat.](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)  Ügyeljen rá, hogy ezt ne feledje, amikor kiválasztja, mely erőforrásokkal társítja az IP-címet.
* Standardról Alapszintűre nem lehet visszalépést.

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure-beli](./public-ip-addresses.md#public-ip-addresses) nyilvános IP-címekről, beleértve a termékváltozat-típusok közötti különbséget, valamint a nyilvános [IP-címbeállításokat.](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Ismerje meg, hogyan [frissítheti az Azure nyilvános load Balancereket alapszintűről standardra.](../load-balancer/upgrade-basic-standard.md)
- A [klasszikus Azure-beli fenntartott IP-k és](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) a klasszikus erőforrások [migrálásának](../virtual-machines/migration-classic-resource-manager-overview.md)Azure Resource Manager.
