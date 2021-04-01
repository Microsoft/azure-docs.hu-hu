---
title: Azure CLI-példaszkript – Alkalmazás hozzáadása a Batch szolgáltatásban
description: Ez a minta azt mutatja be, hogyan adhat hozzá egy alkalmazást Azure Batch készlettel vagy feladattal való használatra.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: 414a401168b1b378ed33f4904607243de0267d22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94565806"
---
# <a name="cli-example-add-an-application-to-an-azure-batch-account"></a>Parancssori felületi példa: Alkalmazás hozzáadása egy Azure Batch-fiókhoz

Ez a szkript bemutatja, hogyan lehet hozzáadni egy alkalmazást az Azure Batch-készlettel vagy feladattal való használatra. Amikor előkészít egy alkalmazást a Batch-fiókjához való hozzáadáshoz, csomagolja be a végrehajtható fájlt – valamennyi függőségével együtt – egy zip-fájlba. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Az oktatóanyaghoz az Azure CLI 2.0.20 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van. 

## <a name="example-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/add-application/add-application.sh "Add Application")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő paranccsal távolítható el az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja.
A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Létrehoz egy tárfiókot. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Létrehoz egy Batch-fiókot. |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Hitelesíti a megadott Batch-fiókot további parancssori felületi interakcióhoz.  |
| [az batch application create](/cli/azure/batch/application#az-batch-application-create) | Létrehoz egy alkalmazást.  |
| [az batch application package create](/cli/azure/batch/application/package#az-batch-application-package-create) | Hozzáad egy alkalmazáscsomagot a megadott alkalmazáshoz.  |
| [az batch application set](/cli/azure/batch/application#az-batch-application-set) | Frissíti egy alkalmazás tulajdonságait.  |
| [az group delete](/cli/azure/group#az-group-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).
