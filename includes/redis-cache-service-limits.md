---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 1ab6243be39bf30bc060ed5745fbf600924743a9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "71839225"
---
| Erőforrás | Korlát |
| --- | --- |
| Gyorsítótár mérete |1,2 TB |
| Adatbázisok |64 |
| Csatlakoztatott ügyfelek maximális száma |40,000 |
| Azure cache a Redis-replikák számára a magas rendelkezésre állás érdekében |1 |
| Szegmensek egy prémium szintű gyorsítótárban fürtözéssel |10 |

Az Redis-korlátok és-méretek Azure cache-je különbözik az egyes díjszabási szintektől. A díjszabási szintek és a hozzájuk tartozó méretek megtekintéséhez tekintse meg az [Azure cache Redis díjszabását](https://azure.microsoft.com/pricing/details/cache/).

Az Azure cache Redis-konfigurációs korlátaival kapcsolatos további információkért lásd az [alapértelmezett Redis-kiszolgáló konfigurációját](../articles/azure-cache-for-redis/cache-configure.md#default-redis-server-configuration)ismertető témakört.

Mivel az Azure cache Redis-példányokhoz való konfigurálását és kezelését a Microsoft végzi, nem minden Redis-parancs támogatott az Azure cache-ben a Redis-hez. További információ: [Redis-parancsok nem támogatottak az Azure cache-ben a Redis](../articles/azure-cache-for-redis/cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis).

