---
title: Példa Azure CLI-parancsfájlra – batch-feladatok futtatása
description: Ez a szkript létrehoz egy Batch-feladatot, amelyhez tevékenységeket ad hozzá. Emellett bemutatja a feladat és a hozzá tartozó tevékenységek monitorozásának menetét.
ms.topic: sample
ms.date: 12/12/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: b67925f48a9d2dbe0b4559d46d783b500e7a0773
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93100915"
---
# <a name="cli-example-run-a-job-and-tasks-with-azure-batch"></a>Parancssori felületi példa: Feladatok és tevékenységek futtatása az Azure Batch segítségével

Ez a szkript létrehoz egy Batch-feladatot, amelyhez tevékenységeket ad hozzá. Emellett bemutatja a feladat és a hozzá tartozó tevékenységek monitorozásának menetét. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Az oktatóanyaghoz az Azure CLI 2.0.20 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van. 

## <a name="example-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/run-job/run-job.sh "Run Job")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő paranccsal távolítható el az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Létrehoz egy Batch-fiókot. |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Hitelesíti a megadott Batch-fiókot további parancssori felületi interakcióhoz.  |
| [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create) | Létrehozza számítási csomópontok egy készletét.  |
| [az batch job create](/cli/azure/batch/job#az-batch-job-create) | Létrehoz egy Batch-feladatot.  |
| [az batch task create](/cli/azure/batch/task#az-batch-task-create) | Hozzáad egy tevékenységet a megadott Batch-feladathoz.  |
| [az batch job set](/cli/azure/batch/job#az-batch-job-set) | Frissíti egy Batch-feladat tulajdonságait.  |
| [az batch job show](/cli/azure/batch/job#az-batch-job-show) | Lekérdezi a megadott Batch-feladat részleteit.  |
| [az batch task show](/cli/azure/batch/task#az-batch-task-show) | Lekérdezi a megadott Batch-feladat egy tevékenységének részleteit.  |
| [az group delete](/cli/azure/group#az-group-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).
