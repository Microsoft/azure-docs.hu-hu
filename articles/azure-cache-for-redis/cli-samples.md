---
title: Azure cache kezelése az Azure CLI-vel való Redis
description: 'Azure CLI-minták az Azure cache Redis való kezeléséhez: gyorsítótár létrehozása, gyorsítótár törlése, gyorsítótár részleteinek, állomásnév, portok és kulcsok összekapcsolása, webalkalmazások csatlakoztatása.'
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 04/14/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: 32530982bc2a9d1b5deb31b3bc71460462352258
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92536419"
---
# <a name="manage-azure-cache-for-redis-with-azure-cli"></a>Azure cache kezelése az Azure CLI-vel való Redis

A következő táblázat az Azure CLI használatával létrehozott bash parancsfájlokra mutató hivatkozásokat tartalmaz.

| Gyorsítótár létrehozása | Leírás |
| ------------ | ----------- |
| [Gyorsítótár létrehozása](./scripts/create-cache.md) | Létrehoz egy erőforráscsoportot és egy alapszintű Azure cache-gyorsítótárat a Redis számára. |
| [Prémium szintű gyorsítótár létrehozása fürtözéssel](./scripts/create-premium-cache-cluster.md) | Létrehoz egy erőforráscsoportot és egy prémium szintű gyorsítótárat, amely lehetővé teszi a fürtözést.|
| [Gyorsítótár adatainak beolvasása](./scripts/show-cache.md) | Beolvassa a Redis-példányhoz tartozó Azure cache adatait, beleértve a létesítési állapotot is. |
| [Állomásnév, portok és kulcsok lekérése](./scripts/cache-keys-ports.md) | Lekéri az Redis-példányhoz tartozó Azure-gyorsítótár állomásneve, portjai és kulcsai. |
|**Webalkalmazás és gyorsítótár**| **Leírás**|
| [Webalkalmazás összekötése egy Azure cache-Redis](./../app-service/scripts/cli-connect-to-redis.md) | Létrehoz egy Azure-webalkalmazást és egy Azure-gyorsítótárat a Redis, majd hozzáadja a Redis kapcsolati adatait az alkalmazás beállításaihoz. |
|**Gyorsítótár törlése**| **Leírás** |
| [Gyorsítótár törlése](./scripts/delete-cache.md) | Egy Azure cache törlése a Redis-példányhoz  |

Az Azure CLI-vel kapcsolatos további információkért lásd: [Az Azure CLI telepítése](/cli/azure/install-azure-cli) és az [Azure CLI első lépései](/cli/azure/get-started-with-azure-cli).