---
title: Azure CLI-példaszkript – Blobtároló méretének kiszámítása | Microsoft Docs
description: Egy Azure-blobtároló méretének kiszámítása a tárolóban lévő blobok méretének összeadásával.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: cli
ms.topic: sample
ms.date: 06/28/2017
ms.author: tamram
ms.custom: devx-track-azurecli
ms.openlocfilehash: 45712632ebfb2da4b713038503965ce908c1dfc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87498888"
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Blob Storage-tároló méretének kiszámítása

Ez a szkript kiszámítja egy Azure-blobtároló méretét a tárolóban lévő blobok méretének összeadásával.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Ez a parancssori felületi példaszkript a tároló becsült méretét adja meg, ezért nem használható számlázási számításokhoz.

## <a name="sample-script"></a>Példaszkript

[!code-azurecli[main](../../../cli_scripts/storage/calculate-container-size/calculate-container-size.sh?highlight=2-3 "Calculate container size")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

Az alábbi paranccsal eltávolítható az erőforráscsoport, a tároló és az összes kapcsolódó erőforrás.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja a Blob Storage-tároló méretének kiszámításához. A táblázatban lévő összes elem a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az group create](/cli/azure/group) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az storage blob upload](/cli/azure/storage/account) | Helyi fájlokat tölt fel egy Azure Blob Storage-tárolóba. |
| [az storage blob list](/cli/azure/storage/account/keys) | Felsorolja az Azure Blob Storage-tárolóban található blobokat. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További Storage CLI-példaszkripteket [az Azure Blob Storage Azure CLI-példái](../blobs/storage-samples-blobs-cli.md) között találhat.
