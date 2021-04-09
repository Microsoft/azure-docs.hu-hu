---
title: Példányok védelme az Azure virtuálisgép-méretezési csoport példányai esetében
description: Ismerje meg, hogyan védhető az Azure virtuálisgép-méretezési csoport példányai a méretezési és a méretezési műveletek során.
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: instance-protection
ms.date: 02/26/2020
ms.reviewer: jushiman
ms.custom: avverma
ms.openlocfilehash: 8b331eaf52a0a97232d481dccfff932221cd5faa
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933457"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances"></a>Példányok védelme az Azure virtuálisgép-méretezési csoport példányai esetében

Az Azure virtuálisgép-méretezési csoportok nagyobb rugalmasságot biztosítanak a számítási feladatokhoz az autoscale segítségével, így beállíthatja, hogy az infrastruktúra milyen mértékben legyen [kibővítve](virtual-machine-scale-sets-autoscale-overview.md), és mikor méretezi a méretezést. A méretezési csoportok lehetővé teszik a nagy számú virtuális gép központi felügyeletét, konfigurálását és frissítését különböző [frissítési házirend](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) -beállításokkal. Konfigurálhat egy frissítést a méretezési csoport modelljében, és az új konfigurációt automatikusan alkalmazza minden méretezési csoport példányra, ha a frissítési szabályzatot automatikusra vagy működésre állította.

Ahogy az alkalmazás dolgozza fel a forgalmat, előfordulhatnak olyan helyzetek, amikor azt szeretné, hogy bizonyos példányok eltérően legyenek kezelve a méretezési csoport többi példányának többi részétől. Előfordulhat például, hogy a méretezési csoport bizonyos példányain hosszan futó műveletek hajthatók végre, és nem szeretné, hogy ezek a példányok skálázásra legyenek, amíg a műveletek be nem fejeződik. Előfordulhat, hogy a méretezési csoport néhány példánya is specializált, hogy a méretezési csoport többi tagjánál további vagy eltérő feladatokat hajtson végre. Ezek a speciális virtuális gépek nem módosíthatók a méretezési csoport többi példányával. A példányok védelme biztosítja a további vezérlőket, amelyek lehetővé teszik ezen és egyéb forgatókönyvek alkalmazását.

Ez a cikk bemutatja, hogyan alkalmazhatja és használhatja a különböző példányok védelmi funkcióit a méretezési csoport példányaival.

## <a name="types-of-instance-protection"></a>A példányok védelmének típusai
A méretezési csoportok két típusú példány-védelmi képességet biztosítanak:

-   **Védelem a méretezésből**
    - Engedélyezve a **protectFromScaleIn** tulajdonságon keresztül a méretezési csoport példányán
    - Védi a példányt az autoscale által kezdeményezett skálázásból
    - A felhasználó által kezdeményezett példányok műveletei (beleértve a példányok törlését) nincsenek **Letiltva**
    - A méretezési csoporton kezdeményezett műveletek (frissítés, rendszerkép, felszabadítás stb.) nincsenek **Letiltva**

-   **Védelem a méretezési csoport műveleteiből**
    - Engedélyezve a **protectFromScaleSetActions** tulajdonságon keresztül a méretezési csoport példányán
    - Védi a példányt az autoscale által kezdeményezett skálázásból
    - Védelmet nyújt a méretezési csoporton kezdeményezett műveletektől (például frissítés, rendszerkép-felszabadítás, felszabadítás stb.).
    - A felhasználó által kezdeményezett példányok műveletei (beleértve a példányok törlését) nincsenek **Letiltva**
    - A teljes méretezési csoport törlése **nincs letiltva**

## <a name="protect-from-scale-in"></a>Védelem a méretezésből
A példányok védelme a példányok létrehozása után is alkalmazható a méretezési csoport példányaira. A védelem csak a [példány modelljére](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) van alkalmazva és módosítva, nem a [méretezési csoport modelljén](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

A méretezési csoport példányain az alábbi példákban ismertetett módon többféleképpen is alkalmazhat méretezési védelmet.

### <a name="azure-portal"></a>Azure Portal

A méretezési csoporton belül a Azure Portalon keresztül is alkalmazhat méretezési védelmet. Egyszerre csak egy példányt lehet módosítani. Ismételje meg a lépéseket minden védelemmel ellátni kívánt példánynál.
 
1. Váltson egy meglévő virtuálisgép-méretezési csoportra.
1. A bal oldali menüben válassza a **példányok** lehetőséget a **Beállítások** területen.
1. Válassza ki a védelemmel ellátni kívánt példány nevét.
1. Válassza a **védelmi szabályzat** fület.
1. A **védelmi szabályzat** panelen válassza a **védelem méretezéssel** lehetőséget.
1. Kattintson a **Mentés** gombra. 

### <a name="rest-api"></a>REST API

Az alábbi példa a méretezési csoport egyik példányára alkalmazza a méretezési védelmet.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true
    }
  }        
}

```

> [!NOTE]
>A példányok védelme csak a 2019-03-01-es és újabb verziójú API-k esetében támogatott

### <a name="azure-powershell"></a>Azure PowerShell

Használja az [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) parancsmagot, hogy a méretezési csoport példányain alkalmazza a skálázási védelmet.

A következő példa a méretezési csoport egy példányára kiterjedő, a 0 AZONOSÍTÓJÚ példányra vonatkozó védelmet alkalmaz.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Az [az vmss Update](/cli/azure/vmss#az-vmss-update) használatával méretezhető védelmet alkalmazhat a méretezési csoport példányára.

A következő példa a méretezési csoport egy példányára kiterjedő, a 0 AZONOSÍTÓJÚ példányra vonatkozó védelmet alkalmaz.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>Védelem a méretezési csoport műveleteiből
A példányok védelme a példányok létrehozása után is alkalmazható a méretezési csoport példányaira. A védelem csak a [példány modelljére](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) van alkalmazva és módosítva, nem a [méretezési csoport modelljén](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

A méretezési csoport műveleteinek egy példányának védelme megvédi a példányt az autoscale által kezdeményezett méretezéstől.

Az alábbi példákban a méretezési csoport műveleteinek védelme több módon is végrehajtható a méretezési csoport példányain.

### <a name="azure-portal"></a>Azure Portal

A méretezési csoport műveleteinek védelmét a Azure Portalon keresztül végezheti el a méretezési csoport egyik példányán. Egyszerre csak egy példányt lehet módosítani. Ismételje meg a lépéseket minden védelemmel ellátni kívánt példánynál.
 
1. Váltson egy meglévő virtuálisgép-méretezési csoportra.
1. A bal oldali menüben válassza a **példányok** lehetőséget a **Beállítások** területen.
1. Válassza ki a védelemmel ellátni kívánt példány nevét.
1. Válassza a **védelmi szabályzat** fület.
1. A **védelmi szabályzat** panelen válassza a védelem a **méretezési csoportból művelet** lehetőséget.
1. Kattintson a **Mentés** gombra. 

### <a name="rest-api"></a>REST API

A következő példa a méretezési csoport műveleteinek védelmét alkalmazza a méretezési csoport egy példányára.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vMScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true,
      "protectFromScaleSetActions": true
    }
  }        
}

```

> [!NOTE]
>A példányok védelme csak a 2019-03-01-es és újabb verziójú API-k esetében támogatott.</br>
A méretezési csoport műveleteinek egy példányának védelme megvédi a példányt az autoscale által kezdeményezett méretezéstől. A "protectFromScaleIn" beállítás nem adható meg: hamis a "protectFromScaleSetActions" beállításakor: true

### <a name="azure-powershell"></a>Azure PowerShell

Használja az [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) parancsmagot a méretezési csoport műveleteinek védelmére a méretezési csoport példányára.

Az alábbi példa a méretezési csoport műveleteinek védelmét alkalmazza a méretezési csoport egy olyan példányára, amelynek azonosítója 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Az [az vmss Update](/cli/azure/vmss#az-vmss-update) paranccsal alkalmazhatja a méretezési csoport műveleteinek védelmét a méretezési csoport példányára.

Az alábbi példa a méretezési csoport műveleteinek védelmét alkalmazza a méretezési csoport egy olyan példányára, amelynek azonosítója 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>Hibaelhárítás
### <a name="no-protectionpolicy-on-scale-set-model"></a>Nincs protectionPolicy a méretezési csoport modelljében
A példányok védelme csak a méretezési csoport példányain alkalmazható, nem a méretezési csoport modelljére.

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>Nincs protectionPolicy a méretezési csoport példányainak modelljében
Alapértelmezés szerint a rendszer nem alkalmazza a védelmi házirendet egy példányra, amikor az létrejött.

A példányok védelmét a példányok létrehozása után is alkalmazhatja a méretezési csoport példányaira.

### <a name="not-able-to-apply-instance-protection"></a>Nem sikerült alkalmazni a példányok védelmét
A példányok védelme csak a 2019-03-01-es és újabb verziójú API-k esetében támogatott. Tekintse meg a használt API-verziót, és szükség szerint frissítse azt. Előfordulhat, hogy a legújabb verzióra is frissítenie kell a PowerShellt vagy a CLI-t.

## <a name="next-steps"></a>Következő lépések
Megtudhatja, hogyan [helyezheti üzembe az alkalmazást](virtual-machine-scale-sets-deploy-app.md) a virtuálisgép-méretezési csoportokban.
