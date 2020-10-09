---
title: 'Azure CLI-parancsfájl – példa: virtuális gépek újraindítása'
description: Azure CLI-példaszkript – Virtuális gépek újraindítása címke és azonosító alapján
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2017
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 03a1e44ee3bdaa168fb3eb17078bfecb1f45c443
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87479489"
---
# <a name="restart-vms"></a>Virtuális gépek újraindítása

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Ez a példa néhány módszert mutat be virtuális gépek lekérdezésére és újraindítására.

Az első az erőforráscsoportban lévő összes virtuális gépet újraindítja.

```azurecli
az vm restart --ids $(az vm list --resource-group myResourceGroup --query "[].id" -o tsv)
```

A második az `az resource list` segítségével lekérdezi a címkézett virtuális gépeket, rászűr a virtuálisgép-erőforrásokra, majd újraindítja a virtuális gépeket.

```azurecli
az vm restart --ids $(az resource list --tag "restart-tag" --query "[?type=='Microsoft.Compute/virtualMachines'].id" -o tsv)
```

A példa Bash-felületen működik. Az Azure CLI-szkriptek Windows-ügyfeleken való futtatásának lehetőségeiről az [Azure CLI Windows rendszeren való telepítésével](/cli/azure/install-azure-cli-windows) foglalkozó témakörben talál további információt.


## <a name="sample-script"></a>Példaszkript

A példa három szkriptet tartalmaz.
Az első szkript kiépíti a virtuális gépeket.
A nem várakozó beállítást használja, így a parancs a végrehajtás során nem várja meg az egyes virtuális gépek kiépítését.
A második szkript megvárja, amíg a virtuális gépek kiépítése befejeződik.
A harmadik szkript újraindítja az összes kiépített virtuális gépet, majd csak a címkézetteket.

### <a name="provision-the-vms"></a>A virtuális gépek kiépítése

Ez a parancsfájl egy erőforráscsoportot, majd három újraindítandó virtuális gépet hoz létre.
Ezek közül kettő címkézve van.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/provision.sh "Provision the VMs")]

### <a name="wait"></a>Várakozás

A szkript 20 másodpercenként ellenőrzi a kiépítési állapotot, amíg mindhárom virtuális gép ki nincs építve, vagy valamelyiknek a kiépítése meg nem hiúsul.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/wait.sh "Wait for the VMs to be provisioned")]

### <a name="restart-the-vms"></a>A virtuális gépek újraindítása

Ez a szkript újraindítja az erőforráscsoportban lévő összes virtuális gépet, majd csak a címkével rendelkezőket.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/restart.sh "Restart VMs by tag")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A példaszkript futtatása után a következő paranccsal távolíthatók el az erőforráscsoportok, a virtuális gépek és az összes kapcsolódó erőforrás.

```azurecli-interactive
az group delete -n myResourceGroup --no-wait --yes
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy erőforráscsoport, egy virtuális gép, egy rendelkezésre állási csoport, egy terheléselosztó és minden kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az group create](/cli/azure/group) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az vm create](/cli/azure/vm/availability-set) | Létrehozza a virtuális gépeket.  |
| [az vm list](/cli/azure/vm) | A `--query` elemmel együtt használva az újraindítás előtt megbizonyosodik arról, hogy a virtuális gépek ki vannak építve, majd lekéri a virtuális gépek azonosítóját az újraindításhoz. |
| [az resource list](/cli/azure/vm) | A `--query` elemmel együtt használva lekéri a címkével rendelkező virtuális gépek azonosítóját. |
| [az vm restart](/cli/azure/vm) | Újraindítja a virtuális gépeket. |
| [az group delete](/cli/azure/vm/extension) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

A virtuális gépekhez kapcsolódó további CLI-példaszkripteket az [Azure Linux rendszerű virtuális gépekre vonatkozó dokumentációjában](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) találhat.
