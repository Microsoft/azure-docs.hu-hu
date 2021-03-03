---
title: Kvóták és korlátok módosítása az Azure Data Lake Analyticsben
description: Megtudhatja, hogyan állíthatja be és növelheti a kvótákat és a korlátokat Azure Data Lake Analytics (ADLA) fiókokban.
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 03/15/2018
ms.openlocfilehash: 416b77fd86316acb8098531257e12b591a0ff052
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101710175"
---
# <a name="adjust-quotas-and-limits-in-azure-data-lake-analytics"></a>Kvóták és korlátok módosítása az Azure Data Lake Analyticsben

Megtudhatja, hogyan módosíthatja és növelheti a kvótákat és a korlátokat Azure Data Lake Analytics (ADLA) fiókokban. A határértékek ismerete segít megérteni a U-SQL-feladatok viselkedését. Az összes kvóta-korlátozás puha, így növelheti a maximális korlátot, ha kapcsolatba lép az Azure támogatási szolgálatával.

## <a name="azure-subscriptions-limits"></a>Azure-előfizetések korlátai

**ADLA-fiókok maximális száma előfizetésben régiónként:**  5

Ha hatodik ADLA-fiókot próbál létrehozni, a következő hibaüzenet jelenik meg: "elérte az engedélyezett Data Lake Analytics fiókok maximális számát (5) a régióban az előfizetés neve alatt".

Ha ezt a korlátot szeretné kipróbálni, a következő lehetőségek közül választhat:

- Ha megfelelő, válasszon egy másik régiót
- vegye fel a kapcsolatot az Azure támogatási szolgálatával, és [Nyisson meg egy támogatási jegyet](#increase-maximum-quota-limits) , amely a kvóta növelését kéri.

## <a name="default-adla-account-limits"></a>Alapértelmezett ADLA-fiók korlátai

**Elemzési egységek maximális száma (AUs):** 250, alapértelmezett 32

A fiókban egyidejűleg futtatható au-t maximális száma. Ha az összes feladatban az összes feladat futása meghaladja ezt a korlátot, az újabb feladatok automatikusan várólistára kerülnek. Például:

- Ha egy második feladatot csak egy 32 AUs-vel rendelkező feladatokkal futtat, a rendszer a feladatok várólistáján várakozik, amíg az első művelet be nem fejeződik.
- Ha már négy feladatot futtat, és mindegyik 8 au-t használ, akkor ha olyan ötödik feladatot küld, amelynek 8 au-t kell megvárnia a feladat-várólistán, amíg nem érhető el 8 au.

    ![Azure Data Lake Analytics korlátok és kvóta lap](./media/data-lake-analytics-quota-limits/adjust-quota-limits.png)

**Az elemzési egységek maximális száma (AUs):** 250, alapértelmezett 32

Ez az a maximális szám, amelyet minden egyes feladathoz hozzá lehet rendelni a fiókjában. Az ennél nagyobb korláttal rendelkező feladatokat a rendszer elutasítja, kivéve, ha a küldőt egy számítási szabályzat (feladat-beküldési korlát) befolyásolja, amely több AUs-t ad hozzá a feladathoz. Ennek az értéknek a felső határa a fiókhoz tartozó AU-korlát.

**Az egyidejű U-SQL-feladatok maximális száma felhasználónként:** 20

A fiókban egyszerre futtatható feladatok maximális száma. Ezen érték felett az újabb feladatok automatikusan várólistára kerülnek.

## <a name="adjust-adla-account-limits"></a>ADLA-fiók korlátainak módosítása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válasszon egy meglévő ADLA-fiókot.
3. Kattintson a **Tulajdonságok** elemre.
4. Az igényeinek megfelelően módosítsa a **maximális** au-t, a **futó feladatok maximális számát** és a **feladat beküldési korlátait** .

## <a name="increase-maximum-quota-limits"></a>Maximális kvóták korlátozása

Az Azure-korlátokkal kapcsolatos további információkért tekintse meg az [Azure-szolgáltatásokra vonatkozó korlátozások dokumentációját](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-analytics-limits).

1. Nyisson meg egy támogatási kérést Azure Portalban.

   ![Azure Data Lake Analytics portál – Súgó és támogatás](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

   ![Azure Data Lake Analytics portál új támogatási kérelem](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)

2. Válassza ki a probléma típusa **kvótát**.

3. Válassza ki az **előfizetését** (Győződjön meg róla, hogy nem "próbaverzió" előfizetés).

4. Válassza ki a kvóta típusát **Data Lake Analytics**.

   ![Azure Data Lake Analytics támogatási kérelem kvótájának típusa](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. A probléma lapon adja meg a kért növelési korlátot, amely **részletesen** ismerteti, hogy miért van szüksége erre a további kapacitásra.

   ![Azure Data Lake Analytics támogatási kérelem részletei](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. Ellenőrizze a kapcsolattartási adatokat, és hozza létre a támogatási kérést.

A Microsoft áttekinti a kérését, és a lehető leghamarabb megkísérli az üzleti igények kielégítését.

## <a name="next-steps"></a>Következő lépések

- [A Microsoft Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
- [Az Azure Data Lake Analytics kezelése az Azure PowerShell-lel](data-lake-analytics-manage-use-powershell.md)
- [Az Azure Data Lake Analytics-feladatok figyelése és hibaelhárítása az Azure Portal használatával](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
