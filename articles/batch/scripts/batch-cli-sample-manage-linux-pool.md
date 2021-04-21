---
title: Azure CLI-példaszkprogram – Linux-készlet a Batch szolgáltatásban
description: Ez a szkript bemutatja az Azure CLI néhány olyan parancsát, amelyek linuxos számítási csomópontok készletének létrehozásához és kezeléséhez érhetők el a Azure Batch.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: b5e1bdccefffa7803fbe744e27c1b36ca719560d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768286"
---
# <a name="cli-example-create-and-manage-a-linux-pool-in-azure-batch"></a>CLI-példa: Linux-készlet létrehozása és kezelése az Azure Batch szolgáltatásban

Ez a szkript bemutatja az Azure CLI néhány olyan parancsát, amelyek linuxos számítási csomópontok készletének létrehozásához és kezeléséhez érhetők el a Azure Batch.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez az oktatóanyaghoz az Azure CLI 2.0.20-as vagy újabb verziójára lesz szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van. 

## <a name="example-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/manage-pool/manage-pool-linux.sh "Manage Linux Virtual Machine Pool")]

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
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Hitelesíti a megadott Batch-fiókot további parancssori felületi interakcióhoz.  |
| [az batch pool node-agent-skus list](../batch-linux-nodes.md#list-of-virtual-machine-images) | Felsorolja a csomóponti ügynök elérhető SKU-inak és rendszerképeinek adatait.  |
| [az batch pool create](/cli/azure/batch/pool#az_batch_pool_create) | Létrehozza számítási csomópontok egy készletét.  |
| [az batch pool resize](/cli/azure/batch/pool#az_batch_pool_resize) | Átméretezi a futó virtuális gépek számát a megadott készletben.  |
| [az batch pool show](/cli/azure/batch/pool#az_batch_pool_show) | Megjeleníti egy készlet tulajdonságait.  |
| [az batch node list](/cli/azure/batch/node#az_batch_node_list) | Felsorolja a megadott készlet összes számítási csomópontját.  |
| [az batch node reboot](/cli/azure/batch/node#az_batch_node_reboot) | Újraindítja a megadott számítási csomópontot.  |
| [az batch node delete](/cli/azure/batch/node#az_batch_node_delete) | Törli a felsorolt csomópontokat a megadott készletből.  |
| [az group delete](/cli/azure/group#az_group_delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).
