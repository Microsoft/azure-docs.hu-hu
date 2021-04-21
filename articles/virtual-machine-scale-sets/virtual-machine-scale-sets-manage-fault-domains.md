---
title: Tartalék tartományok kezelése azure-beli virtuálisgép-méretezési készletekben
description: Megtudhatja, hogyan választhatja ki a megfelelő számú FD-t a virtuálisgép-méretezési készlet létrehozása során.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 12/18/2018
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 10d45662f84a354ee4b261c2e7255a57aa81ad0f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774484"
---
# <a name="choosing-the-right-number-of-fault-domains-for-virtual-machine-scale-set"></a>A tartalék tartományok számának megválasztása a virtuálisgép-méretezési csoportnak megfelelően
A virtuálisgép-méretezési csoportokat a rendszer alapértelmezés szerint öt tartalék tartománysal hozta létre a zónákkal nem nem rendelkezik Azure-régiókban. A virtuálisgép-méretezési csoport zónaális üzembe helyezését támogató régiók esetében, ha ez a beállítás be van jelölve, a tartalék tartományok alapértelmezett értéke 1 minden zónában. Az FD=1 ebben az esetben azt jelenti, hogy a méretezési csoporthoz tartozó virtuálisgép-példányok az ajánlott munkamennyiség alapján számos állvány között lesznek elterülve.

A méretezési készlet tartalék tartományának számát a tartalék tartományok számának és a tartalék tartományok számának Managed Disks is érdemes lehet. Ez az igazítás segíthet megakadályozni a kvórum elvesztését, ha egy teljes Managed Disks meghibásodik a tartalék tartomány. Az FD-szám beállítható az egyes régiókban Managed Disks tartalék tartományok számának kisebbre vagy egyenlőre. Ebben a [dokumentumban](../virtual-machines/availability.md) további információt olvashat a tartalék tartományok Managed Disks régiónkénti számról.

## <a name="rest-api"></a>REST API
A tulajdonságot beállíthatja 1, 2 vagy 3 (ha nincs megadva, az alapértelmezett `properties.platformFaultDomainCount` érték 3). A dokumentációt itt REST API [meg.](/rest/api/compute/virtualmachinescalesets/createorupdate)

## <a name="azure-cli"></a>Azure CLI
A paraméter beállítható 1, 2 vagy 3 (ha nincs megadva, az alapértelmezett `--platform-fault-domain-count` érték 3). Az Azure CLI dokumentációját itt [találhatja.](/cli/azure/vmss#az_vmss_create)

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

## <a name="next-steps"></a>Következő lépések
- További információ az [Azure-környezetek rendelkezésre állási és](../virtual-machines/availability.md) redundancia-funkcióiról.
