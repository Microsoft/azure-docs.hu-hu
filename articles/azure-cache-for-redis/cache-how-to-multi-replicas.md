---
title: Replikák hozzáadása az Azure cache-hez a Redis (előzetes verzió)
description: Ismerje meg, hogyan adhat hozzá több replikát a prémium szintű Azure cache Redis-példányokhoz
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 87b5ec5eb13f2bc53bdf993547ce3da1c74404bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91566788"
---
# <a name="add-replicas-to-azure-cache-for-redis-preview"></a>Replikák hozzáadása az Azure cache-hez a Redis (előzetes verzió)
Ebből a cikkből megtudhatja, hogyan állíthat be egy Azure cache-példányt további replikákkal a Azure Portal használatával.

Az Azure cache a Redis standard és a prémium szintekhez az egyes gyorsítótárak két dedikált virtuális gépen (VM) való üzemeltetésével nyújt redundanciát. Ezek a virtuális gépek elsődlegesként és replikaként vannak konfigurálva. Ha az elsődleges virtuális gép elérhetetlenné válik, a replika észleli, hogy a rendszer automatikusan átveszi az új elsődlegest. Most már legfeljebb hármat is megnövelheti a replikák számát egy prémium szintű gyorsítótárban, így összesen négy virtuális gép található a gyorsítótárban. A több replika nagyobb rugalmasságot eredményez, mint amit egyetlen replika tud biztosítani.

> [!IMPORTANT]
> Ez az előzetes verzió szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 
> 

## <a name="prerequisites"></a>Előfeltételek
* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)

> [!NOTE]
> Ez a funkció jelenleg előzetes verzióban érhető el – [lépjen kapcsolatba velünk](mailto:azurecache@microsoft.com) , ha érdeklik.
>

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
    | **Gyorsítótár típusa** | Válassza ki a [prémium szintű](https://azure.microsoft.com/pricing/details/cache/) gyorsítótárat. |  A tarifacsomag határozza meg a gyorsítótár méretét, teljesítményét és elérhető funkcióit. További információ: [Azure cache for Redis – áttekintés](cache-overview.md). |
   
1. A **speciális** lapon válassza a **replika száma** lehetőséget.
   
    :::image type="content" source="media/cache-how-to-multi-replicas/create-multi-replicas.png" alt-text="Replikák száma.":::

1. Az alapértelmezett beállításokban hagyja meg az egyéb beállításokat. 

    > [!NOTE]
    > A több replika támogatása jelenleg csak a nem fürtözött gyorsítótárak esetében működik.
    >

1. Kattintson a **Létrehozás** lehetőségre.
   
    Eltarthat egy ideig a gyorsítótár létrehozásához. Nyomon követheti a folyamat előrehaladását az Azure cache Redis **– Áttekintés** oldalon. Ha az **állapot** **futásra** mutat, a gyorsítótár készen áll a használatra.

    > [!NOTE]
    > A gyorsítótárban lévő replikák száma a létrehozás után nem módosítható.
    >

## <a name="next-steps"></a>Következő lépések
További információ az Azure cache Redis szolgáltatásairól.

> [!div class="nextstepaction"]
> [Azure cache a Redis prémium szintű szolgáltatási szintjeihez](cache-overview.md#service-tiers)
