---
title: Azure parancssori felület előfeltételei az Azure HPC cache-hez
description: Telepítési lépések, mielőtt az Azure CLI-t Azure HPC-gyorsítótár létrehozására vagy módosítására használhatja
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 13f45c96a830110bd0f4a2d4a2b422921d7a2e31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94654456"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>Azure CLI beállítása az Azure HPC Cache-hez

Az alábbi lépéseket követve előkészítheti a környezetet, mielőtt az Azure CLI-t használja az Azure HPC-gyorsítótár létrehozásához vagy kezeléséhez.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Az Azure HPC cache az Azure CLI 2,7-es vagy újabb verzióját igényli. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="set-default-resource-group-optional"></a>Alapértelmezett erőforráscsoport beállítása (nem kötelező)

A HPC-cache parancsok többsége megköveteli a gyorsítótár erőforráscsoport átadását. Az alapértelmezett erőforráscsoportot az [az configure](/cli/azure/reference-index#az-configure)paranccsal állíthatja be.

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-bővítmény telepítését és a bejelentkezést követően az Azure CLI használatával hozhat létre és kezelhet Azure HPC gyorsítótár-rendszereket.

* [Azure HPC-gyorsítótár létrehozása](hpc-cache-create.md)
* [Azure CLI HPC – gyorsítótár dokumentációja](/cli/azure/ext/hpc-cache/hpc-cache)
