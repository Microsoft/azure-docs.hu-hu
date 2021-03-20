---
title: Virtual Machine Scale Sets kezelése az Azure CLI-vel
description: Általános Azure CLI-parancsok Virtual Machine Scale Sets kezelésére, például egy példány indítására és leállítására, illetve a méretezési csoport kapacitásának módosítására.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 05/29/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: d954f7cdda4cae65f822489828226e0364d0fc29
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91570532"
---
# <a name="manage-a-virtual-machine-scale-set-with-the-azure-cli"></a>Virtuálisgép-méretezési csoport kezelése az Azure CLI-vel
A virtuálisgép-méretezési csoport életciklusa során egy vagy több felügyeleti feladat futtatására lehet szükség. Emellett előfordulhat, hogy különféle szkripteket is érdemes létrehozni az életciklus-feladatok automatizálására. Ez a cikk a leggyakoribb Azure CLI-parancsokat ismerteti, amelyek lehetővé teszik ezen feladatok elvégzését.

A felügyeleti feladatok elvégzéséhez szüksége lesz a legújabb Azure CLI-re. További információ: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Ha létre kell hoznia egy virtuálisgép-méretezési készletet, [létrehozhat egy méretezési készletet az Azure CLI](quick-create-cli.md)használatával.


## <a name="view-information-about-a-scale-set"></a>Méretezési csoportra vonatkozó információk megtekintése
A méretezési csoportokra vonatkozó általános információk megtekintéséhez használja az [az vmss show](/cli/azure/vmss)lehetőséget. A következő példa a *myResourceGroup* erőforráscsoport *myScaleSet* nevű méretezési csoportjának adatait olvassa be. Adja meg a saját nevét a következőképpen:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
```


## <a name="view-vms-in-a-scale-set"></a>Virtuális gépek megtekintése egy méretezési csoportban
A méretezési csoportokban lévő virtuálisgép-példányok listájának megtekintéséhez használja az [az vmss List-instances](/cli/azure/vmss)lehetőséget. Az alábbi példa felsorolja a *myResourceGroup* erőforráscsoport *myScaleSet* nevű méretezési csoportjának összes virtuálisgép-példányát. Adja meg a saját értékeit a következő nevekhez:

```azurecli
az vmss list-instances \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --output table
```

Egy adott virtuálisgép-példányra vonatkozó további információk megtekintéséhez adja hozzá a `--instance-id` paramétert az [az vmss Get-instance-View](/cli/azure/vmss) elemhez, és adjon meg egy megtekinteni kívánt példányt. Az alábbi példa a *myScaleSet* és a *myResourceGroup* erőforráscsoport nevű méretezési csoportban található virtuálisgép *-példányok* adatait tekinti meg. Adja meg a saját nevét a következőképpen:

```azurecli
az vmss get-instance-view \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --instance-id 0
```

Egy API-hívásban részletes *instanceView* információkat is megtudhat az összes példányról, ami segíthet elkerülni a nagyméretű TELEPÍTÉSEk API-szabályozását. Adja meg a, a és a saját értékeit `--resource-group` `--subscription` `--name` .

```azurecli
az vmss list-instances \
    --expand instanceView \
    --select instanceView \
    --resource-group <resourceGroupName> \
    --subscription <subID> \
    --name <vmssName>
```

```rest
GET "https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSSName>/virtualMachines?api-version=2019-03-01&%24expand=instanceView"
```

## <a name="list-connection-information-for-vms"></a>Virtuális gépekhez tartozó kapcsolatok adatainak listázása
Ha egy méretezési csoportba tartozó virtuális gépekhez szeretne csatlakozni, SSH vagy RDP protokollt kell rendelnie egy hozzárendelt nyilvános IP-címhez és portszámhoz. Alapértelmezés szerint a hálózati címfordítási (NAT) szabályok hozzáadódnak az Azure Load Balancerhez, amely minden virtuális géphez továbbítja a távoli kapcsolatok forgalmát. A méretezési csoportokban lévő virtuálisgép-példányokhoz való csatlakozáshoz használandó címek és portok listázásához használja az [az vmss List-instance-kapcsolat-info](/cli/azure/vmss)parancsot. Az alábbi példa felsorolja a virtuálisgép-példányok a *myScaleSet* és a *myResourceGroup* erőforráscsoport nevű méretezési csoportban található virtuálisgép-példányokhoz való csatlakoztatási adatait. Adja meg a saját értékeit a következő nevekhez:

```azurecli
az vmss list-instance-connection-info \
    --resource-group myResourceGroup \
    --name myScaleSet
```


## <a name="change-the-capacity-of-a-scale-set"></a>Méretezési csoport kapacitásának módosítása
Az előző parancsok a méretezési csoportra és a virtuálisgép-példányokra vonatkozó információkat mutatták. A méretezési csoport példányainak számának növeléséhez vagy csökkentéséhez módosíthatja a kapacitást. A méretezési csoport létrehozza vagy eltávolítja a szükséges virtuális gépek számát, majd konfigurálja a virtuális gépeket az alkalmazás forgalmának fogadására.

A méretezési csoportban jelenleg futó példányok számának megtekintéséhez használja az [az vmss show](/cli/azure/vmss) parancsot, és állítsa be az *sku.capacity* lekérdezést:

```azurecli
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

Ezt követően az [az vmss scale](/cli/azure/vmss) parancs használatával manuálisan növelheti vagy csökkentheti a méretezési csoportban futó virtuális gépek számát. A következő példa a méretezési csoportba tartozó virtuális gépek számát állítja be *5* értékre:

```azurecli
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 5
```

A méretezési csoport kapacitásának frissítése néhány percet vesz igénybe. Ha csökkenti a méretezési csoport kapacitását, a rendszer először eltávolítja a legmagasabb példány-azonosítóval rendelkező virtuális gépeket.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Virtuális gépek leállítása és elindítása egy méretezési csoportban
A méretezési csoportokban lévő egy vagy több virtuális gép leállításához használja [az az vmss stop](/cli/azure/vmss#az-vmss-stop)lehetőséget. Az `--instance-ids` paraméter segítségével megadhat egy vagy több leállítandó virtuális gépet. Ha nem ad meg példányazonosítót, a méretezési csoportban lévő összes virtuális gép le lesz állítva. Több virtuális gép leállításához az egyes példányok AZONOSÍTÓit szóközzel kell elválasztani.

A következő példa leállítja a *0* . példányt a *myScaleSet* nevű méretezési csoportba és a *myResourceGroup* -erőforráscsoporthoz. Adja meg a saját értékeit az alábbiak szerint:

```azurecli
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```

A leállított virtuális gépek továbbra is kiosztásra kerülnek, és továbbra is számításba vehetők. Ha ehelyett azt szeretné, hogy a virtuális gépek felszabadítva legyenek, és csak a tárolási díjakat terhelik, használja [az az vmss felszabadítása](/cli/azure/vmss)parancsot. Több virtuális gép felszabadításához szóközzel válassza el az egyes példány-AZONOSÍTÓkat. A következő példa leállítja és felszabadítja a *0* . példányt a *MyScaleSet* és a *myResourceGroup* erőforráscsoport nevű méretezési csoportban. Adja meg a saját értékeit az alábbiak szerint:

```azurecli
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


### <a name="start-vms-in-a-scale-set"></a>Virtuális gépek indítása méretezési csoportokban
Egy vagy több virtuális gép egy méretezési csoportba való elindításához használja [az az vmss Start](/cli/azure/vmss)parancsot. Az `--instance-ids` paraméter segítségével megadhat egy vagy több indítandó virtuális gépet. Ha nem ad meg példányazonosítót, a méretezési csoportban lévő összes virtuális gép el lesz indítva. Több virtuális gép indításához külön szóközzel válassza el az egyes példány-AZONOSÍTÓkat.

A következő példa elindítja a *0* . példányt a *myScaleSet* nevű méretezési csoportba és a *myResourceGroup* -erőforráscsoporthoz. Adja meg a saját értékeit az alábbiak szerint:

```azurecli
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="restart-vms-in-a-scale-set"></a>Virtuális gépek újraindítása egy méretezési csoportban
Egy vagy több virtuális gép újraindításához a méretezési csoportokban használja [az az vmss restart](/cli/azure/vmss). Az `--instance-ids` paraméter segítségével megadhat egy vagy több újraindítandó virtuális gépet. Ha nem ad meg példányazonosítót, a méretezési csoportban lévő összes virtuális gép újra lesz indítva. Több virtuális gép újraindításához az egyes példányok AZONOSÍTÓit szóközzel kell elválasztani.

A következő példa újraindítja a *0* . példányt a *MyScaleSet* és a *myResourceGroup* erőforráscsoport nevű méretezési csoportban. Adja meg a saját értékeit az alábbiak szerint:

```azurecli
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="remove-vms-from-a-scale-set"></a>Virtuális gépek eltávolítása egy méretezési csoportból
Egy vagy több virtuális gép egy méretezési csoportból való eltávolításához használja [az az vmss delete-instances](/cli/azure/vmss)parancsot. A `--instance-ids` paraméter segítségével megadhat egy vagy több eltávolítandó virtuális gépet. Ha a példány AZONOSÍTÓját adja meg, a rendszer a méretezési csoport összes virtuális gépe törlődik. Több virtuális gép eltávolításához az egyes példányok AZONOSÍTÓit szóközzel kell elválasztani.

A következő példa eltávolítja a *0* . példányt a *myScaleSet* nevű méretezési csoportba és a *myResourceGroup* -erőforráscsoporthoz. Adja meg a saját értékeit az alábbiak szerint:

```azurecli
az vmss delete-instances --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="next-steps"></a>Következő lépések
A méretezési csoportokkal kapcsolatos egyéb gyakori feladatok közé tartozik az [alkalmazások központi telepítése](virtual-machine-scale-sets-deploy-app.md)és a virtuálisgép- [példányok frissítése](virtual-machine-scale-sets-upgrade-scale-set.md). Az Azure CLI-vel is [konfigurálhatja az automatikus skálázási szabályokat](virtual-machine-scale-sets-autoscale-overview.md).
