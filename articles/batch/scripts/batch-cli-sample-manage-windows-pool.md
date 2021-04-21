---
title: Azure CLI-példaszk szkript – Windows-készlet a Batchben
description: A szkript bemutat az Azure CLI elérhető parancsai közül néhányat, amelyekkel létrehozható és kezelhető egy Windows-készlet az Azure Batch szolgáltatásban.
ms.topic: sample
ms.date: 12/12/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 773699dde9342a4b230a08471a289a56fca7e308
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768196"
---
# <a name="cli-example-create-and-manage-a-windows-pool-in-azure-batch"></a>CLI-példa: Windows-készlet létrehozása és kezelése az Azure Batch szolgáltatásban

A szkript bemutat az Azure CLI elérhető parancsai közül néhányat, amelyekkel létrehozható és kezelhető egy Windows-készlet az Azure Batch szolgáltatásban. A Windows-készletek kétféleképp konfigurálhatók: egy Cloud Services-konfigurációval vagy egy virtuális gép konfigurálásával. Ez a példa bemutatja egy Windows-készlet létrehozását a Cloud Services-konfigurációval.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Az oktatóanyaghoz az Azure CLI 2.0.20-as vagy újabb verziójára lesz szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van. 

## <a name="example-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/manage-pool/manage-pool-windows.sh "Manage Windows Cloud Services Pool")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő paranccsal távolítható el az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Létrehoz egy Batch-fiókot. |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Hitelesíti a megadott Batch-fiókot további parancssori felületi interakcióhoz. |
| [az batch pool create](/cli/azure/batch/pool#az_batch_pool_create) | Létrehozza számítási csomópontok egy készletét.  |
| [az batch pool set](/cli/azure/batch/pool#az_batch_pool_set) | Frissíti egy készlet tulajdonságait.  |
| [az batch pool autoscale enable](/cli/azure/batch/pool/autoscale#az_batch_pool_autoscale_enable) | Lehetővé teszi egy készlet automatikus méretezését, és alkalmaz egy képletet.  |
| [az batch pool show](/cli/azure/batch/pool#az_batch_pool_show) | Megjeleníti egy készlet tulajdonságait.  |
| [az batch pool autoscale disable](/cli/azure/batch/pool/autoscale#az_batch_pool_autoscale_disable) | Letiltja egy készlet automatikus méretezését. |
| [az group delete](/cli/azure/group#az_group_delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |


## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).
