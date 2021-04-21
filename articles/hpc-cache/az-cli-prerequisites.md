---
title: Az Azure CLI előfeltételei a Azure HPC Cache
description: Beállítási lépések, mielőtt az Azure CLI használatával létrehozhat vagy módosíthat egy Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 30621eceefd69cd3e08de137bb34f1079a17a406
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780478"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>Azure CLI beállítása az Azure HPC Cache-hez

Kövesse az alábbi lépéseket a környezet előkészítéséhez, mielőtt az Azure CLI használatával létrehoz vagy kezel egy Azure HPC Cache.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Azure HPC Cache Azure CLI 2.7-es vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="set-default-resource-group-optional"></a>Alapértelmezett erőforráscsoport beállítása (nem kötelező)

A legtöbb hpc-cache parancshoz át kell adni a gyorsítótár erőforráscsoportját. Az alapértelmezett erőforráscsoportot az az configure használatával [állíthatja be.](/cli/azure/reference-index#az_configure)

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-bővítmény telepítése és a bejelentkezés után az Azure CLI használatával létrehozhat és kezelhet Azure HPC Cache rendszereket.

* [Hozzon létre egy Azure HPC Cache](hpc-cache-create.md)
* [Az Azure CLI hpc-cache dokumentációja](/cli/azure/ext/hpc-cache/hpc-cache)
