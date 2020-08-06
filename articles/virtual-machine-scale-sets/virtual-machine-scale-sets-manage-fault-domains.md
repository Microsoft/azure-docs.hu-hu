---
title: Tartalék tartományok kezelése az Azure-beli virtuálisgép-méretezési csoportokban
description: Megtudhatja, hogyan választhatja ki a megfelelő számú tartalék a virtuálisgép-méretezési csoport létrehozásakor.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 12/18/2018
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 32efde2cb2497fb8aab415d09a1063ff07c6b0f0
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87837193"
---
# <a name="choosing-the-right-number-of-fault-domains-for-virtual-machine-scale-set"></a>A tartalék tartományok számának megválasztása a virtuálisgép-méretezési csoportnak megfelelően
A virtuálisgép-méretezési csoportok alapértelmezés szerint öt tartalék tartománnyal jönnek létre a zónák nélküli Azure-régiókban. A virtuálisgép-méretezési csoportok zónákra épülő telepítését támogató régiók esetén a tartalék tartomány alapértelmezett értéke 1 az egyes zónák esetében. Ebben az esetben az FD = 1 érték azt jelenti, hogy a méretezési csoportba tartozó virtuálisgép-példányok számos rackben lesznek elosztva a legjobb erőfeszítés alapján.

Azt is megteheti, hogy összehangolja a méretezési csoport tartalék tartományának számát Managed Disks tartalék tartományok számával. Ez az igazítás segíthet megakadályozni a kvórum elvesztését, ha egy teljes Managed Disks tartalék tartomány leáll. Az FD-szám beállítható úgy, hogy az egyes régiókban elérhető Managed Disks tartalék tartományok száma kisebb vagy egyenlő legyen. Tekintse át ezt a [dokumentumot](../virtual-machines/windows/manage-availability.md) , és ismerkedjen meg az Managed Disks a tartalék tartományok régiónként való számával.

## <a name="rest-api"></a>REST API
A tulajdonságot `properties.platformFaultDomainCount` 1, 2 vagy 3 értékre állíthatja (ha nincs megadva az alapértelmezett 3). Tekintse [meg REST API](/rest/api/compute/virtualmachinescalesets/createorupdate)dokumentációját.

## <a name="azure-cli"></a>Azure CLI
A paramétert beállíthatja `--platform-fault-domain-count` 1, 2 vagy 3 (alapértelmezés szerint 3, ha nincs megadva). Tekintse meg az Azure CLI dokumentációját [itt](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create).

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --platform-fault-domain-count 3\
  --generate-ssh-keys
```

A méretezési csoport erőforrásainak és virtuális gépeinek létrehozása és konfigurálása néhány percet vesz igénybe.

## <a name="next-steps"></a>További lépések
- További információ az Azure-környezetek [rendelkezésre állásával és redundanciával kapcsolatos funkcióival](../virtual-machines/availability.md) kapcsolatban.