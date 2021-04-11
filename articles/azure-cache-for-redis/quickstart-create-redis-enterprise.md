---
title: 'Rövid útmutató: Redis Enterprise cache létrehozása'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Azure cache-példányt a vállalati szintű Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: mvc
ms.topic: quickstart
ms.date: 02/08/2021
ms.openlocfilehash: 8b450ddff0952777652a957ba2ed554a4bc6497d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104584822"
---
# <a name="quickstart-create-a-redis-enterprise-cache"></a>Rövid útmutató: Redis Enterprise cache létrehozása

Az Azure cache for Redis vállalati szintjei teljes mértékben integrált és felügyelt [Redis Enterprise](https://redislabs.com/redis-enterprise/) -t biztosítanak az Azure-ban. Ezek az új szintek a következők:
* Vállalat, amely felejtő memóriát használ a virtuális gépeken az adattároláshoz
* A nagyvállalati Flash, amely illékony és nem felejtő memóriát (NVMe vagy SSD) használ az adattároláshoz.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, szüksége lesz egy Azure-előfizetésre. Ha még nem rendelkezik ilyennel, hozzon létre egy [fiókot](https://azure.microsoft.com/). További információ: [a vállalati rétegek speciális szempontjai](cache-overview.md#special-considerations-for-enterprise-tiers).

## <a name="create-a-cache"></a>Gyorsítótár létrehozása
1. Gyorsítótár létrehozásához jelentkezzen be a Azure Portalba, és válassza az **erőforrás létrehozása** lehetőséget.

1. Az **új** lapon válassza az **adatbázisok** lehetőséget, majd válassza az Azure cache lehetőséget a **Redis számára**.
   
   :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Az Azure cache kiválasztása a Redis":::
   
1. Az **új Redis cache** lapon adja meg az új gyorsítótár beállításait.
   
   | Beállítás      | Ajánlott érték  | Leírás |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Előfizetés** | Legördülő menüből válassza ki az előfizetését. | Az előfizetés, amely alatt létre kell hoznia ezt az új Azure cache-t a Redis-példányhoz. | 
   | **Erőforráscsoport** | Legördülő listából válassza ki az erőforráscsoportot, vagy válassza az **új létrehozása** elemet, és adjon meg egy új erőforráscsoport-nevet. | Azon erőforráscsoport neve, amelyben létre szeretné hozni a gyorsítótárat és az egyéb erőforrásokat. Az összes alkalmazás-erőforrás egy erőforráscsoporthoz való elhelyezésével könnyedén kezelheti és törölheti azokat. | 
   | **DNS-név** | Adjon meg egy globálisan egyedi nevet. | A gyorsítótár nevének 1 és 63 karakter közötti sztringnek kell lennie, amely csak számokat, betűket vagy kötőjeleket tartalmaz. A névnek számmal vagy betűvel kell kezdődnie és végződnie, és nem tartalmazhat egymást követő kötőjeleket. A gyorsítótár-példány *állomásneve* a lesz *\<DNS name> . <Azure region> redisenterprise.cache.azure.net*. | 
   | **Hely** | Legördülő menüből válassza ki a helyet. | A vállalati rétegek a kiválasztott Azure-régiókban érhetők el. |
   | **Gyorsítótár típusa** | Legördülő menüből válassza ki a *vállalati* vagy *vállalati Flash* -szintet és a méretet. |  A szinten a gyorsítótár számára elérhető méretet, teljesítményt és funkciókat határozza meg. |
   
   :::image type="content" source="media/cache-create/enterprise-tier-basics.png" alt-text="Nagyvállalati szint alapjai lap":::

   > [!NOTE] 
   > A folytatás előtt győződjön meg róla, hogy a "feltételek" alatt bejelöli a mezőt.
   >

1. Válassza a **Tovább: hálózatkezelés** és Kihagyás lehetőséget.

1. Válassza a **Next (tovább): speciális** lehetőséget, és állítsa be a **fürtözési házirendet** a **vállalat** számára. Csak akkor engedélyezze a **nem TLS-hozzáférést** , ha a TLS használata nélkül tervezi az új gyorsítótárhoz való kapcsolódást. Ez azonban nem ajánlott.

   :::image type="content" source="media/cache-create/enterprise-tier-advanced.png" alt-text="Vállalati szintű speciális lap":::

1. Válassza a **Next (tovább): címkék** és Kihagyás lehetőséget.

1. Válassza a **Tovább: Ellenőrzés és létrehozás** lehetőségre.

   :::image type="content" source="media/cache-create/enterprise-tier-summary.png" alt-text="Nagyvállalati szintű felülvizsgálat + Létrehozás lap":::

1. Tekintse át a beállításokat, és kattintson a **Létrehozás** gombra.
   
   Eltarthat egy ideig a gyorsítótár létrehozásához. Nyomon követheti a folyamat előrehaladását az Azure cache Redis **– Áttekintés** oldalon. Ha az **állapot** **futásra** mutat, a gyorsítótár készen áll a használatra.

## <a name="next-steps"></a>Következő lépések

Ebből a rövid útmutatóból megtudhatta, hogyan hozhat létre az Azure cache nagyvállalati szintű példányát a Redis.

> [!div class="nextstepaction"]
> [Hozzon létre egy ASP.NET-webalkalmazást, amely egy Azure cache-t használ a Redis.](./cache-web-app-howto.md)

