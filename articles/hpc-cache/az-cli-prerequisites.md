---
title: Az Azure CLI előfeltételei a Azure HPC Cache
description: Beállítási lépések, mielőtt az Azure CLI használatával létrehozhat vagy módosíthat egy Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 0b8e1158bc60c4cceea508db988000fe952a90a4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864288"
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
* [Az Azure CLI hpc-cache dokumentációja](/cli/azure/hpc-cache)
