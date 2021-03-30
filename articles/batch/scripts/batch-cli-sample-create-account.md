---
title: Azure CLI parancsfájl – példa – batch-fiók létrehozása – batch szolgáltatás
description: Ezt a szkript létrehoz egy Azure Batch-fiókot Batch szolgáltatás módban, és bemutatja, hogyan lehet lekérdezni vagy frissíteni a fiók különböző tulajdonságait.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2349b6b373f271a5aa0f169e5a9ebc9f58f6f608
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93076810"
---
# <a name="cli-example-create-a-batch-account-in-batch-service-mode"></a>CLI-példa: Batch-fiók létrehozása Batch szolgáltatás módban

Ezt a szkript létrehoz egy Azure Batch-fiókot Batch szolgáltatás módban, és bemutatja, hogyan lehet lekérdezni vagy frissíteni a fiók különböző tulajdonságait. Amikor létrehozunk egy Batch-fiókot az alapértelmezett Batch szolgáltatás módban, a számítási csomópontok hozzárendelését a Batch szolgáltatás belsőleg végzi el. A lefoglalt számítási csomópontokra egy külön vCPU- (mag-) kvóta vonatkozik, a fiók hitelesítéséhez pedig megosztott kulcsos hitelesítő adatok vagy egy Azure Active Directory-token használható.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Az oktatóanyaghoz az Azure CLI 2.0.20 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van. 

## <a name="example-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account.sh "Create Account")]

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
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Létrehoz egy tárfiókot. |
| [az batch account set](/cli/azure/batch/account#az-batch-account-set) | Frissíti a Batch-fiók tulajdonságait.  |
| [az batch account show](/cli/azure/batch/account#az-batch-account-show) | Lekérdezi a megadott Batch-fiók adatait.  |
| [az batch account keys list](/cli/azure/batch/account/keys#az-batch-account-keys-list) | Lekérdezi a megadott Batch-fiók hozzáférési kulcsait.  |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Hitelesíti a megadott Batch-fiókot további parancssori felületi interakcióhoz.  |
| [az group delete](/cli/azure/group#az-group-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).
