---
title: Zóna-redundancia engedélyezése az Azure cache számára a Redis (előzetes verzió)
description: Megtudhatja, hogyan állíthatja be a Redis-példányok prémium és nagyvállalati szintű Azure cache-hez készült zóna-redundanciát
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 0623f47528d0530838f62c28cf5546e1e66c187b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102508265"
---
# <a name="enable-zone-redundancy-for-azure-cache-for-redis-preview"></a>Zóna-redundancia engedélyezése az Azure cache számára a Redis (előzetes verzió)
Ebből a cikkből megtudhatja, hogyan konfigurálhat egy zóna – redundáns Azure cache-példányt a Azure Portal használatával.

Az Azure cache a standard, a prémium szintű és a nagyvállalati szintű Redis beépített redundanciát biztosít a gyorsítótárak két dedikált virtuális gépen (VM) való üzemeltetésével. Annak ellenére, hogy ezek a virtuális gépek külön Azure-beli [meghibásodási és frissítési tartományokban](../virtual-machines/availability.md) találhatók, és magas rendelkezésre állású, az adatközponti szintű hibákra hajlamosak. Az Azure cache for Redis szintén támogatja a zóna-redundanciát a prémium és a nagyvállalati szinteken. Egy zóna – redundáns gyorsítótár fut a több [rendelkezésre állási zónán](../availability-zones/az-overview.md)keresztül elosztott virtuális gépeken. Nagyobb rugalmasságot és rendelkezésre állást biztosít.

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
    | **Gyorsítótár típusa** | Válasszon [prémium vagy nagyvállalati szintű](https://azure.microsoft.com/pricing/details/cache/) gyorsítótárat. |  A tarifacsomag határozza meg a gyorsítótár méretét, teljesítményét és elérhető funkcióit. További információ: [Azure cache for Redis – áttekintés](cache-overview.md). |
   
1. A **speciális** lapon, a prémium szintű gyorsítótár esetében válassza a **replika száma** lehetőséget.
   
    :::image type="content" source="media/cache-how-to-multi-replicas/create-multi-replicas.png" alt-text="Replikák száma":::

1. Válassza ki a **rendelkezésre állási zónákat**. 
   
    :::image type="content" source="media/cache-how-to-zone-redundancy/create-zones.png" alt-text="Rendelkezésre állási zónák":::

1. Az alapértelmezett beállításokban hagyja meg az egyéb beállításokat. 

    > [!NOTE]
    > A zóna-redundancia-támogatás jelenleg csak a nem fürtözött és nem földrajzilag replikált gyorsítótárak esetében működik. Emellett nem támogatja a magánhálózati, a skálázási, az adatmegőrzési vagy az importálási és exportálási hivatkozást.
    >

1. Kattintson a **Létrehozás** lehetőségre. 
   
    Eltarthat egy ideig a gyorsítótár létrehozásához. Nyomon követheti a folyamat előrehaladását az Azure cache Redis **– Áttekintés** oldalon. Ha az **állapot** **futásra** mutat, a gyorsítótár készen áll a használatra.
   
    > [!NOTE]
    > A rendelkezésre állási zónák a gyorsítótár létrehozása után nem módosíthatók.
    >

## <a name="next-steps"></a>Következő lépések
További információ az Azure cache Redis szolgáltatásairól.

> [!div class="nextstepaction"]
> [Azure cache a Redis prémium szintű szolgáltatási szintjeihez](cache-overview.md#service-tiers)