---
title: Példányvédelem Azure-beli virtuálisgép-méretezésikészlet-példányokhoz
description: Megtudhatja, hogyan védheti meg az Azure-beli virtuálisgép-méretezésihalmaz-példányokat a horizontális le- és leskálásos műveletektől.
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: instance-protection
ms.date: 02/26/2020
ms.reviewer: jushiman
ms.custom: avverma
ms.openlocfilehash: 292abce3361c000eeeef2c399d5ffa2d2c4852e1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762856"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances"></a>Példányvédelem Azure-beli virtuálisgép-méretezésikészlet-példányokhoz

Az Azure-beli virtuálisgép-méretezési készletek az automatikus skálázás segítségével nagyobb rugalmasságot tudnak a számítási feladatok [számára,](virtual-machine-scale-sets-autoscale-overview.md)így konfigurálhatja, hogy mikor és mikor skálázható fel horizontálisan az infrastruktúra. A méretezési csoportokkal számos virtuális gép központilag kezelhető, konfigurálható és frissíthető a különböző frissítési [házirend-beállításokkal.](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) Konfigurálhat frissítést a méretezésihalmaz-modellhez, és az új konfiguráció automatikusan alkalmazva lesz minden méretezésikészlet-példányra, ha a frissítési szabályzatot Automatikus vagy Működés közbenire állította.

Ahogy az alkalmazás feldolgozza a forgalmat, előfordulhatnak olyan helyzetek, amikor azt szeretné, hogy az adott példányok kezelése eltérjen a méretezésikészlet-példány többi példányának kezeléstől. Előfordulhat például, hogy a méretezési készlet bizonyos példányai hosszan futó műveleteket hajt végre, és nem szeretné, hogy ezeket a példányokat a műveletek befejezéséig horizontálisan leskálálják. Előfordulhat, hogy a méretezési csoportban speciális példányok is vannak, amelyek további vagy eltérő feladatokat végeznek el, mint a méretezési csoport többi tagja. A "speciális" virtuális gépeket nem kell módosítani a méretezési készlet többi példányával. A példányvédelem további vezérlőket biztosít az alkalmazás ezen és egyéb forgatókönyvei engedélyezéséhez.

Ez a cikk bemutatja, hogyan alkalmazhatja és használhatja a különböző példányvédelmi képességeket a méretezésikészlet-példányokkal.

## <a name="types-of-instance-protection"></a>Példányvédelem típusai
A méretezési készletek kétféle példányvédelmi képességet biztosítanak:

-   **Védelem a leskálástól**
    - Engedélyezve a **protectFromScaleIn tulajdonságon** keresztül a méretezési csoport példányán
    - Védelmet nyújt a példány számára az automatikus skálázás által kezdeményezett leskálás ellen
    - A felhasználó által kezdeményezett példányműveleteket (beleértve a példány törlését) **nincs letiltva**
    - A méretezési készleten kezdeményezett műveletek (frissítés, rendszerértesítés, felszabadítás stb.) **nincsenek letiltva**

-   **Védelem a méretezésihalmaz-műveletektől**
    - A **méretezési csoport példányán a protectFromScaleSetActions** tulajdonságon keresztül engedélyezett
    - Védelmet nyújt a példánynak az automatikus skálázás által kezdeményezett horizontális leskálástól
    - Védelmet nyújt a példánynak a méretezésicsoporton kezdeményezett műveletektől (például frissítés, rendszerértesítés, felszabadítás stb.)
    - A felhasználó által kezdeményezett példányműveleteket (beleértve a példány törlését is) **nincs letiltva**
    - A teljes méretezési csoport törlése **nincs blokkolva**

## <a name="protect-from-scale-in"></a>Védelem leskálásos védelemhez
A példányok létrehozása után példányvédelem alkalmazható a méretezésikészlet-példányra. A rendszer csak a [](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) példánymodellre alkalmazza és módosítja a védelmet, a [méretezésikészlet-modellen nem.](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model)

A méretezésikészlet-példányok horizontális leskálás elleni védelmét többféleképpen is lehet alkalmazni az alábbi példákban részletezett módon.

### <a name="azure-portal"></a>Azure Portal

Horizontális leskálás elleni védelmet alkalmazhat a Azure Portal méretezési készletben egy példányra. Egyszerre csak egy példány állítható be. Ismételje meg a lépéseket minden védeni kívánt példányon.
 
1. Ugrás egy meglévő virtuálisgép-méretezési készletre.
1. A **bal oldali** menü Beállítások menüjében válassza a Példányok **lehetőséget.**
1. Válassza ki a védeni kívánt példány nevét.
1. Válassza a **Védelmi szabályzat** lapot.
1. A Védelmi **szabályzat panelen** válassza a **Védelem leskálásból lehetőséget.**
1. Kattintson a **Mentés** gombra. 

### <a name="rest-api"></a>REST API

Az alábbi példa a méretezési készlet egy példányára alkalmazza a leskálás védelmet.

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
>A példányvédelem csak a 2019-03-01-es és újabb API-verziókban támogatott

### <a name="azure-powershell"></a>Azure PowerShell

Az [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) parancsmag használatával skálázható védelmet alkalmazhat a méretezésikészlet-példányra.

Az alábbi példa leskálás védelmet alkalmaz a méretezési készlet 0-s azonosítójú példányán.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Az [az vmss update használatával](/cli/azure/vmss#az_vmss_update) horizontális leskálás védelmet alkalmazhat a méretezésikészlet-példányra.

Az alábbi példa leskálás védelmet alkalmaz a méretezési készlet 0-s azonosítójú példányán.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>Védelem a méretezésikészlet-műveletektől
A példányvédelem a példányok létrehozása után alkalmazható a méretezésikészlet-példányra. A rendszer csak a példánymodellre alkalmazza és módosítja a [védelmet,](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) a [méretezésikészlet-modellre nem.](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model)

A példányok méretezésikészlet-műveletekkel való védelme a példány automatikus skálázás által kezdeményezett leméretezése ellen is védelmet nyújt.

A méretezésikészlet-műveletek védelmét többféleképpen is lehet alkalmazni a méretezésihalmaz-példányon az alábbi példákban részletezett módon.

### <a name="azure-portal"></a>Azure Portal

A méretezésikészlet-műveletektől a méretezési Azure Portal a méretezési készletben egy példányra alkalmazhat védelmet. Egyszerre egynél több példány nem módosítható. Ismételje meg a lépéseket minden védeni kívánt példányon.
 
1. Ugrás egy meglévő virtuálisgép-méretezési készletre.
1. A **bal oldali** menü Beállítások menüjében válassza a **Példányok lehetőséget.**
1. Válassza ki a védeni kívánt példány nevét.
1. Válassza a **Védelmi szabályzat** lapot.
1. A Védelmi **szabályzat panelen** válassza a **Védelem méretezésikészlet-műveletekből lehetőséget.**
1. Kattintson a **Mentés** gombra. 

### <a name="rest-api"></a>REST API

Az alábbi példa védelmet alkalmaz a méretezésikészlet-műveletek ellen a méretezési készlet egy példányára.

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
>A példányvédelem csak a 2019-03-01-es és újabb API-verziókban támogatott.</br>
A példányok méretezésikészlet-műveletekkel való védelme a példány automatikus skálázás által kezdeményezett leméretezése ellen is védelmet nyújt. Nem adhatja meg a "protectFromScaleIn": false értéket a "protectFromScaleSetActions": true beállításakor

### <a name="azure-powershell"></a>Azure PowerShell

Az [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) parancsmag használatával védelmet alkalmazhat a méretezésikészlet-műveletek ellen a méretezésikészlet-példányra.

Az alábbi példa védelmet alkalmaz a méretezésikészlet-műveletek ellen a 0 azonosítójú példányra a méretezési készletben.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Az [az vmss update használatával](/cli/azure/vmss#az_vmss_update) védelmet alkalmazhat a méretezésikészlet-műveletekről a méretezésikészlet-példányra.

Az alábbi példa védelmet alkalmaz a méretezésikészlet-műveletek ellen a 0 azonosítójú példányra a méretezési készletben.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>Hibaelhárítás
### <a name="no-protectionpolicy-on-scale-set-model"></a>Nincs protectionPolicy a méretezésihalmaz-modellen
A példányvédelem csak a méretezésikészlet-példányok esetében alkalmazható, a méretezésikészlet-modellben nem.

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>Nincs protectionPolicy a méretezésikészlet-példánymodellen
Alapértelmezés szerint a rendszer nem alkalmazza a védelmi házirendet a példányra annak létrehozásakor.

A példányok létrehozása után példányvédelmet alkalmazhat a méretezésikészlet-példányokhoz.

### <a name="not-able-to-apply-instance-protection"></a>Nem lehet példányvédelmet alkalmazni
A példányvédelem csak a 2019-03-01-es és újabb API-verziókban támogatott. Ellenőrizze a használt API-verziót, és szükség szerint frissítsen. Előfordulhat, hogy frissítenie kell a PowerShellt vagy a parancssori felületet a legújabb verzióra.

## <a name="next-steps"></a>Következő lépések
Megtudhatja, [hogyan helyezheti üzembe alkalmazását](virtual-machine-scale-sets-deploy-app.md) virtuálisgép-méretezési készleteken.
