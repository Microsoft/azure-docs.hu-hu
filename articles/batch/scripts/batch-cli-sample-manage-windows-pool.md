---
title: Azure CLI parancsfájl-példa – Windows-készlet a Batch-ben
description: A szkript bemutat az Azure CLI elérhető parancsai közül néhányat, amelyekkel létrehozható és kezelhető egy Windows-készlet az Azure Batch szolgáltatásban.
ms.topic: sample
ms.date: 12/12/2019
ms.openlocfilehash: 869e9990fe2d7d3f909ccf3db2c49ce7c33e579e
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681851"
---
# <a name="cli-example-create-and-manage-a-windows-pool-in-azure-batch"></a>CLI-példa: Windows-készlet létrehozása és kezelése az Azure Batch szolgáltatásban

A szkript bemutat az Azure CLI elérhető parancsai közül néhányat, amelyekkel létrehozható és kezelhető egy Windows-készlet az Azure Batch szolgáltatásban. A Windows-készletek kétféleképp konfigurálhatók: egy Cloud Services-konfigurációval vagy egy virtuális gép konfigurálásával. Ez a példa bemutatja egy Windows-készlet létrehozását a Cloud Services-konfigurációval.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0.20-as vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/manage-pool/manage-pool-windows.sh "Manage Windows Cloud Services Pool")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő paranccsal távolítható el az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Létrehoz egy Batch-fiókot. |
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#az-batch-account-login) | Hitelesíti a megadott Batch-fiókot további parancssori felületi interakcióhoz. |
| [az batch pool create](https://docs.microsoft.com/cli/azure/batch/pool#az-batch-pool-create) | Létrehozza számítási csomópontok egy készletét.  |
| [az batch pool set](https://docs.microsoft.com/cli/azure/batch/pool#az-batch-pool-set) | Frissíti egy készlet tulajdonságait.  |
| [az batch pool autoscale enable](https://docs.microsoft.com/cli/azure/batch/pool/autoscale#az-batch-pool-autoscale-enable) | Lehetővé teszi egy készlet automatikus méretezését, és alkalmaz egy képletet.  |
| [az batch pool show](https://docs.microsoft.com/cli/azure/batch/pool#az-batch-pool-show) | Megjeleníti egy készlet tulajdonságait.  |
| [az batch pool autoscale disable](https://docs.microsoft.com/cli/azure/batch/pool/autoscale#az-batch-pool-autoscale-disable) | Letiltja egy készlet automatikus méretezését. |
| [az group delete](/cli/azure/group#az-group-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |


## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).
