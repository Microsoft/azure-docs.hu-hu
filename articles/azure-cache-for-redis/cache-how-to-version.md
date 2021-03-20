---
title: Az Azure cache Redis-verziójának beállítása a Redis (előzetes verzió)
description: Ismerje meg, hogyan konfigurálhatja a Redis verzióját
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: d9f48de7ef5d9525a995af4ebbd12c5f14f40189
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93349137"
---
# <a name="set-redis-version-for-azure-cache-for-redis-preview"></a>Az Azure cache Redis-verziójának beállítása a Redis (előzetes verzió)
Ebből a cikkből megtudhatja, hogyan konfigurálhatja a Redis-verziót a gyorsítótár-példánnyal való használatra. A Redis készült Azure cache a Redis legújabb fő verzióját és legalább egy korábbi verzióját kínálja. Ezeket a verziókat rendszeresen frissíti, ahogy újabb Redis szoftver jelenik meg. A két elérhető verzió közül választhat. Ne feledje, hogy a gyorsítótárat a rendszer automatikusan frissíti a következő verzióra, ha a jelenleg használt verzió már nem támogatott.

## <a name="prerequisites"></a>Előfeltételek
* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)

## <a name="create-a-cache"></a>Gyorsítótár létrehozása
Gyorsítótár létrehozásához kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és válassza az **erőforrás létrehozása** lehetőséget.
  
1. Az **új** lapon válassza az **adatbázisok** lehetőséget, majd válassza az Azure cache lehetőséget a **Redis számára**.

    :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Válassza ki az Azure cache-t a Redis.":::
   
1. Az **alapvető** beállítások lapon adja meg az új gyorsítótár beállításait.
   
    | Beállítás      | Ajánlott érték  | Leírás |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Előfizetés** | Válassza ki előfizetését. | Az előfizetés, amely alatt létre kell hoznia ezt az új Azure cache-t a Redis-példányhoz. | 
    | **Erőforráscsoport** | Válasszon ki egy erőforráscsoportot, vagy válassza az **új létrehozása** elemet, és adjon meg egy új erőforráscsoport-nevet. | Azon erőforráscsoport neve, amelyben létre szeretné hozni a gyorsítótárat és az egyéb erőforrásokat. Az összes alkalmazás-erőforrás egy erőforráscsoporthoz való elhelyezésével könnyedén kezelheti és törölheti azokat. | 
    | **DNS-név** | Adjon meg egy globálisan egyedi nevet. | A gyorsítótár nevének 1 és 63 karakter közötti sztringnek kell lennie, amely csak számokat, betűket vagy kötőjeleket tartalmaz. A névnek számmal vagy betűvel kell kezdődnie és végződnie, és nem tartalmazhat egymást követő kötőjeleket. A gyorsítótár-példány *állomásneve* a *\<DNS name> . Redis.cache.Windows.net* lesz. | 
    | **Hely** | Válasszon ki egy helyet. | Válasszon ki egy [régiót](https://azure.microsoft.com/regions/) a többi olyan szolgáltatás közelében, amely a gyorsítótárat fogja használni. |
    | **Gyorsítótár típusa** | Válassza ki a [gyorsítótári szintet és a méretet](https://azure.microsoft.com/pricing/details/cache/). |  A tarifacsomag határozza meg a gyorsítótár méretét, teljesítményét és elérhető funkcióit. További információ: [Azure cache for Redis – áttekintés](cache-overview.md). |
   
1. A **speciális** lapon válassza ki a használni kívánt Redis-verziót.
   
    :::image type="content" source="media/cache-how-to-version/select-redis-version.png" alt-text="Redis verziója.":::

1. Kattintson a **Létrehozás** lehetőségre. 
   
    Eltarthat egy ideig a gyorsítótár létrehozásához. Nyomon követheti a folyamat előrehaladását az Azure cache Redis **– Áttekintés** oldalon. Ha az **állapot** **futásra** mutat, a gyorsítótár készen áll a használatra.

    > [!NOTE]
    > Ekkor a Redis verziója nem módosítható a gyorsítótár létrehozása után.
    >

## <a name="next-steps"></a>Következő lépések
További információ az Azure cache Redis szolgáltatásairól.

> [!div class="nextstepaction"]
> [Azure cache a Redis prémium szintű szolgáltatási szintjeihez](cache-overview.md#service-tiers)
