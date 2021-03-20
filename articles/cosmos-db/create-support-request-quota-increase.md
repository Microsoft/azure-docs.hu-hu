---
title: Kvóta növelésének kérése Azure Cosmos DB erőforrásokhoz
description: Megtudhatja, hogyan igényelhet kvóta-növekedést Azure Cosmos DB erőforrásokhoz. Azt is megtudhatja, hogyan engedélyezheti az előfizetéseket egy adott régió eléréséhez.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: e7ec71220b75647e789508c760e50957b3b497fa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93090035"
---
# <a name="how-to-request-quota-increase-for-azure-cosmos-db-resources"></a>Kvóta növelésének kérése Azure Cosmos DB erőforrásokhoz
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

A Azure Cosmos DB erőforrásai rendelkeznek [alapértelmezett kvótákkal/korlátokkal](concepts-limits.md). Előfordulhat azonban, hogy a munkaterhelésnek több kvótára van szüksége, mint az alapértelmezett érték. Ilyen esetben fel kell vennie a Azure Cosmos DB csapatot, hogy kvótát növeljen. Ez a cikk azt ismerteti, hogyan kérhető le kvóta a Azure Cosmos DB erőforrásokra vonatkozóan. Azt is megtudhatja, hogyan engedélyezheti az előfizetéseket egy adott régió eléréséhez.

## <a name="create-a-new-support-request"></a>Új támogatási kérelem létrehozása

A kvóta növeléséhez létre kell hoznia egy új támogatási kérést a munkaterhelés részleteivel. A Azure Cosmos DB csapat ezután kiértékeli a kérést, és jóváhagyja. A következő lépésekkel hozzon létre egy új támogatási kérelmet a Azure Portal:

1. Jelentkezzen be az Azure Portalra.

1. A bal oldali menüben válassza a **Súgó + támogatás** lehetőséget, majd kattintson az **új támogatási kérelem** elemre.

1. Az **alapvető beállítások** lapon adja meg a következő adatokat:

   * A **Probléma típusa** esetében válassza a **Szolgáltatási és előfizetési korlátok (kvóták)** értéket
   * Az **előfizetés** mezőben válassza ki azt az előfizetést, amelynek meg szeretné emelni a kvótáját.
   * A **kvóta típusa** beállításnál válassza a **Cosmos db**

   :::image type="content" source="./media/create-support-request-quota-increase/create-quota-increase-request.png" alt-text="Új Cosmos DB támogatási kérelem létrehozása a kvóta növeléséhez":::

1. A **részletek** lapon adja meg a kvóta-kérelemnek megfelelő adatokat. Az ezen a lapon megjelenő információkat a probléma további felmérésére, valamint a probléma elhárításához segítséget nyújtó mérnök segítségére fogjuk használni.

1. Adja meg a következő adatokat az űrlapon:

   * **Leírás**: adja meg a kérés rövid leírását, például a munkaterhelést, hogy az alapértelmezett értékek miért nem elégségesek. Annak az erőforrásnak a típusa alapján, amelynek a kvótáját emelni szeretné, kötelező megadni a **Leírás** mezőben a következő részleteket:

     **Régiók kérelmek** Ha a kérés megfelel a régiónak az engedélyezési listához való hozzáadásának, ügyeljen arra, hogy a következő értékeket adja meg:

        * Régió neve
        * Előfizetés azonosítója

     **Átviteli sebességre vonatkozó kérelmek** Ha a kérelem megfelel az átviteli sebességre vonatkozó kvóta növelésének, ügyeljen arra, hogy a következő értékeket adja meg:

        * Adatbázis neve
        * Előfizetés azonosítója
        * Új átviteli sebesség korlátja

     **Adatbázis-fiókra vonatkozó korlátozási kérelmek** Ha a kérelem megfelel az előfizetéshez tartozó adatbázis-fiókok számának növelésére vonatkozó kvóta növelésének, ügyeljen arra, hogy a következő értékeket adja meg:

       * Előfizetés azonosítója
       * Új adatbázis-fiók korlátja

   * **Fájlfeltöltés: töltse** fel a támogatási kérelemhez kapcsolódó diagnosztikai fájlokat vagy más fájlokat. Ha többet szeretne megtudni a fájlfeltöltés-útmutatóról, tekintse meg az [Azure-támogatási]( ../azure-portal/supportability/how-to-manage-azure-support-request.md#upload-files) cikket.

   * **Súlyosság**: válassza ki az elérhető súlyossági szintek egyikét az üzleti hatás alapján.

   * **Előnyben részesített kapcsolattartási módszer**: választhat, hogy **e-mailben** vagy **telefonon** keresztül is felveszi-e a kapcsolatot.

1. Adja meg a hátralévő részleteket, például a rendelkezésre állást, a támogatási nyelvet, a kapcsolattartási adatokat, az e-maileket és a telefonszámot az űrlapon.

1. Válassza a **Next (tovább): felülvizsgálat + létrehozás** elemet. Érvényesítse a megadott információkat, és válassza a **Létrehozás** lehetőséget egy támogatási kérelem létrehozásához.

A Azure Cosmos DB támogatási csapat 24 órán belül kiértékeli a kérést, és vissza fog térni Önnek.

## <a name="next-steps"></a>Következő lépések

* Tekintse meg az [alapértelmezett szolgáltatási kvótákat Azure Cosmos db](concepts-limits.md)
