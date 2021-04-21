---
title: Az Virtual Machine Scale Sets kezelése az Azure CLI-val
description: Gyakori Azure CLI-parancsok a Virtual Machine Scale Sets, például a példányok indításának és leállításának, vagy a méretezési készlet kapacitásának módosítása.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 05/29/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 9c2b2217fc6b32e5191bb67ffdaa10b796adf84b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762766"
---
# <a name="manage-a-virtual-machine-scale-set-with-the-azure-cli"></a>Virtuálisgép-méretezési készlet kezelése az Azure CLI-val
A virtuálisgép-méretezési csoport életciklusa során egy vagy több felügyeleti feladat futtatására lehet szükség. Emellett előfordulhat, hogy különféle szkripteket is érdemes létrehozni az életciklus-feladatok automatizálására. Ez a cikk néhány gyakori Azure CLI-parancsot részletez, amelyek segítségével végrehajthat ilyen feladatokat.

A felügyeleti feladatok elvégzéséhez a legújabb Azure CLI-re lesz szüksége. További információ: [Az Azure CLI telepítése.](/cli/azure/install-azure-cli) Ha létre kell hoznia egy virtuálisgép-méretezési készletet, az Azure CLI használatával hozhat létre [méretezési csoportokat.](quick-create-cli.md)


## <a name="view-information-about-a-scale-set"></a>Méretezési készlet információinak megtekintése
A méretezési készlet általános információinak megtekintéséhez használja [az az vmss show adatokat.](/cli/azure/vmss) Az alábbi példa a *myResourceGroup erőforráscsoportban* *található myScaleSet* nevű méretezési csoport adatait lekérte. Adja meg a saját nevét az alábbiak szerint:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
```


## <a name="view-vms-in-a-scale-set"></a>Virtuális gépek megtekintése egy méretezési csoportban
A méretezési készletben található virtuálisgép-példányok listájának megtekintéséhez használja [az az vmss list-instances et.](/cli/azure/vmss) Az alábbi példa felsorolja a *myResourceGroup* erőforráscsoportban található *myScaleSet* nevű méretezési csoportban található összes virtuálisgép-példányt. Adja meg a saját értékeit a következő nevekhez:

```azurecli
az vmss list-instances \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --output table
```

Egy adott virtuálisgép-példány további információinak megtekintéséhez adja hozzá a paramétert `--instance-id` [az az vmss get-instance-view](/cli/azure/vmss) paraméterhez, és adjon meg egy megtekinteni kívánt példányt. Az alábbi példa a *myScaleSet* és a *myResourceGroup* erőforráscsoport nevű méretezési csoport *0* virtuálisgép-példányának adataitnézett tartalmazza. Adja meg a saját nevét az alábbiak szerint:

```azurecli
az vmss get-instance-view \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --instance-id 0
```

Egy API-hívás minden példányának részletes *instanceView* információit is le tudja kérni, ami segíthet elkerülni a nagy méretű telepítések API-szabályozását. Adja meg a saját értékeit a `--resource-group` , a és a `--subscription` `--name` számára.

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

## <a name="list-connection-information-for-vms"></a>Virtuális gépek kapcsolati információinak felsorolása
A méretezési csoportban található virtuális gépekhez való csatlakozáshoz SSH-t vagy RDP-t kell hozzárendelni egy hozzárendelt nyilvános IP-címhez és portszámhoz. Alapértelmezés szerint a hálózati címfordítási (NAT-) szabályok hozzá vannak adva az Azure Load Balancerhez, amely továbbítja a távoli kapcsolat forgalmát az egyes virtuális gépeknek. A méretezési készletben található virtuálisgép-példányokhoz való csatlakozás címének és portjainak listához használja [az az vmss list-instance-connection-info parancsokat.](/cli/azure/vmss) Az alábbi példa a *myScaleSet* nevű méretezési csoportban és a *myResourceGroup* erőforráscsoportban található virtuálisgép-példányok kapcsolati adatait sorolja fel. Adja meg a saját értékeit ezekhez a nevekhez:

```azurecli
az vmss list-instance-connection-info \
    --resource-group myResourceGroup \
    --name myScaleSet
```


## <a name="change-the-capacity-of-a-scale-set"></a>Méretezési csoport kapacitásának módosítása
A fenti parancsok a méretezési készletről és a virtuálisgép-példányról mutattak be információkat. A méretezési készletben a példányok számának növeléséhez vagy csökkentéséhez módosíthatja a kapacitást. A méretezési készlet létrehozza vagy eltávolítja a szükséges számú virtuális gépeket, majd konfigurálja a virtuális gépeket az alkalmazás forgalmának fogadására.

A méretezési csoportban jelenleg futó példányok számának megtekintéséhez használja az [az vmss show](/cli/azure/vmss) parancsot, és állítsa be az *sku.capacity* lekérdezést:

```azurecli
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

Ezt követően az [az vmss scale](/cli/azure/vmss) parancs használatával manuálisan növelheti vagy csökkentheti a méretezési csoportban futó virtuális gépek számát. Az alábbi példa a méretezési készletben a virtuális gépek számát *5-re állítja:*

```azurecli
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 5
```

A méretezési csoport kapacitásának frissítése néhány percet vesz igénybe. Ha csökkenti egy méretezési készlet kapacitását, a rendszer először a legmagasabb példány-értékekkel rendelkező virtuális gépeket távolítja el.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Virtuális gépek leállítása és elindítása egy méretezési csoportban
Egy méretezési készlet egy vagy több virtuális gépének leállításhoz használja [az az vmss stop et.](/cli/azure/vmss#az_vmss_stop) Az `--instance-ids` paraméter segítségével megadhat egy vagy több leállítandó virtuális gépet. Ha nem ad meg példányazonosítót, a méretezési csoportban lévő összes virtuális gép le lesz állítva. Több virtuális gép leállításhoz minden példányazonosítót szóközre kell elválasztani.

Az alábbi példa leállítja a *myScaleSet* nevű méretezési csoport *0.* példányát és a *myResourceGroup* erőforráscsoportot. Adja meg a saját értékeit az alábbiak szerint:

```azurecli
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```

A leállított virtuális gépek továbbra is le vannak osztva, és továbbra is számítási díjakat kell fizetni. Ha ehelyett szeretné felszabadítani a virtuális gépeket, és csak tárolási költségeket kell fizetni, használja [az az vmss deallocate et.](/cli/azure/vmss) Több virtuális gép felszabadítása esetén az egyes példányazonosítókat szóközre kell elválasztani. Az alábbi példa leállítja és felszabadítja a *myScaleSet* és a *myResourceGroup* erőforráscsoport nevű méretezési csoport *0.* példányát. Adja meg a saját értékeit az alábbiak szerint:

```azurecli
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


### <a name="start-vms-in-a-scale-set"></a>Virtuális gépek elindítani egy méretezési készletben
Egy vagy több virtuális gép méretezési készletben való indításhoz használja [az az vmss start használhatja a következőt:](/cli/azure/vmss). Az `--instance-ids` paraméter segítségével megadhat egy vagy több indítandó virtuális gépet. Ha nem ad meg példányazonosítót, a méretezési csoportban lévő összes virtuális gép el lesz indítva. Több virtuális gép indításhoz külön kell elválasztani az egyes példányazonosítókat egy szóköztől.

A következő példa elindítja a *myScaleSet* nevű méretezési csoport *0.* példányát és a *myResourceGroup* erőforráscsoportot. Adja meg a saját értékeit az alábbiak szerint:

```azurecli
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="restart-vms-in-a-scale-set"></a>Virtuális gépek újraindítása egy méretezési csoportban
Egy méretezési készlet egy vagy több virtuális gépének újraindításához használja [az az vmss restart parancsokat.](/cli/azure/vmss) Az `--instance-ids` paraméter segítségével megadhat egy vagy több újraindítandó virtuális gépet. Ha nem ad meg példányazonosítót, a méretezési csoportban lévő összes virtuális gép újra lesz indítva. Több virtuális gép újraindításához az egyes példányazonosítókat szóközre kell elválasztani.

Az alábbi példa újraindítja a *myScaleSet* nevű méretezési csoport *0.* példányát és a *myResourceGroup* erőforráscsoportot. Adja meg a saját értékeit az alábbiak szerint:

```azurecli
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="remove-vms-from-a-scale-set"></a>Virtuális gépek eltávolítása méretezési készletből
A méretezési csoportban található egy vagy több virtuális gép eltávolításához használja [az az vmss delete-instances parancsot.](/cli/azure/vmss) A paraméterrel egy vagy több eltávolítható virtuális gép `--instance-ids` adható meg. Ha megadja a * értéket a példányazonosítóhoz, a méretezési készletben minden virtuális gép el lesz távolítva. Több virtuális gép eltávolításához az egyes példányazonosítókat szóközre kell elválasztani.

Az alábbi példa eltávolítja a *0* példányt a *myScaleSet* nevű méretezési csoportból és a *myResourceGroup* erőforráscsoportból. Adja meg a saját értékeit az alábbiak szerint:

```azurecli
az vmss delete-instances --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="next-steps"></a>Következő lépések
A méretezési készletek további gyakori feladatai közé tartozik az alkalmazások üzembe helyezése és [a](virtual-machine-scale-sets-deploy-app.md) [virtuálisgép-példányok frissítése.](virtual-machine-scale-sets-upgrade-scale-set.md) Az automatikus skálázható szabályokat az Azure CLI [használatával is konfigurálhatja.](virtual-machine-scale-sets-autoscale-overview.md)
