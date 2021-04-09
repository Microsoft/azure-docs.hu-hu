---
title: Linuxos virtuális gép konvertálása nem felügyelt lemezekről felügyelt lemezekre
description: Linux rendszerű virtuális gép konvertálása nem felügyelt lemezekről a felügyelt lemezekre az Azure CLI használatával.
author: roygara
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 12/15/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 1f3b62f8c05edffa1b55bf3d8cd24494b1c918bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102558491"
---
# <a name="convert-a-linux-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Linuxos virtuális gép konvertálása nem felügyelt lemezekről felügyelt lemezekre

Ha már létezik olyan linuxos virtuális gép (VM), amely nem felügyelt lemezeket használ, a virtuális gépeket átalakíthatja az [Azure Managed Disks](../managed-disks-overview.md)használatára. Ez a folyamat az operációsrendszer-lemezt és a csatlakoztatott adatlemezeket is átalakítja.

Ez a cikk bemutatja, hogyan alakíthatja át a virtuális gépeket az Azure CLI használatával. Ha telepíteni vagy frissíteni szeretné, tekintse meg az [Azure CLI telepítését](/cli/azure/install-azure-cli)ismertető témakört. 

## <a name="before-you-begin"></a>Előkészületek
* Tekintse át [a Managed Disksre való áttelepítéssel kapcsolatos gyakori kérdéseket](../faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

* A virtuális gép által az átalakítás előtt használt eredeti virtuális merevlemezeket és a tárfiókot nem törli a rendszer. A futtatásuk továbbra is költségekkel jár. Annak érdekében, hogy ezekért az összetevőkért a továbbiakban ne fizessen, törölje az eredeti virtuálismerevlemez-blobokat, miután meggyőződött arról, hogy az átalakítás befejeződött. Ha meg kell találnia ezeket a nem csatolt lemezeket a törléséhez, tekintse meg a nem [csatolt Azure felügyelt és nem felügyelt lemezek keresését és törlését](find-unattached-disks.md)ismertető cikket.

## <a name="convert-single-instance-vms"></a>Egypéldányos virtuális gépek konvertálása
Ez a szakasz bemutatja, hogyan alakíthatja át a nem felügyelt lemezekről származó egypéldányos Azure-beli virtuális gépeket a felügyelt lemezekre. (Ha a virtuális gépek rendelkezésre állási csoportban találhatók, tekintse meg a következő szakaszt.) Ezzel a folyamattal a prémium szintű (SSD) nem felügyelt lemezekről a prémium szintű felügyelt lemezekre, vagy a standard szintű (HDD) nem felügyelt lemezekről szabványos felügyelt lemezekre alakíthatja át a virtuális gépeket.

1. Szabadítsa fel a virtuális gépet az [az VM felszabadítása](/cli/azure/vm)paranccsal. A következő példa felszabadítja a nevű virtuális gépet `myVM` a nevű erőforráscsoporthoz `myResourceGroup` :

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. Alakítsa át a virtuális gépet felügyelt lemezekre az [az VM Convert](/cli/azure/vm)paranccsal. A következő folyamat átalakítja a nevű virtuális gépet `myVM` , beleértve az operációsrendszer-lemezt és az adatlemezeket:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. Indítsa el a virtuális gépet a felügyelt lemezekre történő átalakítás után az [az VM Start](/cli/azure/vm)paranccsal. A következő példa elindítja a nevű virtuális gépet `myVM` a nevű erőforráscsoport alatt `myResourceGroup` .

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>Virtuális gépek konvertálása rendelkezésre állási csoportba

Ha a felügyelt lemezekre átalakítani kívánt virtuális gépek rendelkezésre állási csoportba kerülnek, először át kell alakítania a rendelkezésre állási készletet egy felügyelt rendelkezésre állási csoportba.

Meg kell adni a rendelkezésre állási csoportba tartozó összes virtuális gépet a rendelkezésre állási csoport konvertálása előtt. Tervezze meg az összes virtuális gép felügyelt lemezre konvertálását, miután a rendelkezésre állási csoport egy felügyelt rendelkezésre állási csoportba lett konvertálva. Ezután indítsa el az összes virtuális gépet, és folytassa a szokásos módon való működéssel.

1. Egy rendelkezésre állási csoportba tartozó összes virtuális gép listázása az [az VM rendelkezésre állása-set List](/cli/azure/vm/availability-set)paranccsal. Az alábbi példa felsorolja a rendelkezésre állási csoportban lévő összes virtuális gépet a `myAvailabilitySet` nevű erőforráscsoport `myResourceGroup` :

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. Szabadítson fel minden virtuális [gépet az az VM felszabadítása](/cli/azure/vm)paranccsal. A következő példa felszabadítja a nevű virtuális gépet `myVM` a nevű erőforráscsoporthoz `myResourceGroup` :

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Alakítsa át a rendelkezésre állási készletet az [az VM rendelkezésre állása-set Convert](/cli/azure/vm/availability-set)paranccsal. Az alábbi példa átalakítja a nevű rendelkezésre állási `myAvailabilitySet` csoportot a nevű erőforráscsoport `myResourceGroup` :

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. Alakítsa át az összes virtuális gépet a felügyelt lemezekre az [az VM Convert](/cli/azure/vm)paranccsal. A következő folyamat átalakítja a nevű virtuális gépet `myVM` , beleértve az operációsrendszer-lemezt és az adatlemezeket:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. Indítsa el az összes virtuális gépet a felügyelt lemezekre való átalakítás után az [az VM Start](/cli/azure/vm)paranccsal. A következő példa elindítja a nevű virtuális gépet `myVM` a nevű erőforráscsoporthoz `myResourceGroup` :

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-using-the-azure-portal"></a>Konvertálás a Azure Portal használatával

A nem felügyelt lemezeket a Azure Portal használatával is konvertálhatja a felügyelt lemezekre.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a virtuális gépet a portálon lévő virtuális gépek listájából.
3. A virtuális gép paneljén válassza a menü **lemezek** elemét.
4. A **lemezek** panel tetején válassza az **áttelepítés felügyelt lemezekre** lehetőséget.
5. Ha a virtuális gép rendelkezésre állási csoportba esik, a rendszer figyelmeztetést küld a **felügyelt lemezek** átállítása panelre, hogy először a rendelkezésre állási készletet kell konvertálnia. A figyelmeztetésnek tartalmaznia kell egy hivatkozást, amelyre kattintva átalakíthatja a rendelkezésre állási készletet. Ha a rendelkezésre állási csoport át lett konvertálva, vagy ha a virtuális gép nincs rendelkezésre állási csoportba, kattintson az **áttelepítés** elemre a lemezek felügyelt lemezekre való áttelepítési folyamatának elindításához.

A rendszer a Migrálás befejezése után leállítja és újraindítja a virtuális gépet.

## <a name="next-steps"></a>Következő lépések

További információ a tárolási lehetőségekről: az [Azure Managed Disks áttekintése](../managed-disks-overview.md).
