---
title: Azure cache törlése a Redis – Azure CLI
description: Ez az Azure CLI-mintakód azt mutatja be, hogyan törölhet egy Azure cache-t a Redis-példányhoz az az Redis delete paranccsal.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9ce6e20e5e4866bc4daa3f331fa2a8612ac6c260
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96184161"
---
# <a name="delete-an-azure-cache-for-redis"></a>Azure cache törlése a Redis-hez

Ebből a forgatókönyvből megtudhatja, hogyan törölhet egy Azure-gyorsítótárat a Redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli[main](../../../cli_scripts/redis-cache/delete-cache/delete-cache.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja a Redis-példány Azure-gyorsítótárának törléséhez. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az Redis delete](/cli/azure/redis) | Törölje az Azure cache-t a Redis-példányhoz. |


## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További Azure cache a Redis CLI-szkriptekhez az [Azure cache Redis dokumentációjában](../cli-samples.md)található.