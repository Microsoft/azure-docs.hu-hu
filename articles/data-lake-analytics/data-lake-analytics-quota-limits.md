---
title: Kvóták és korlátok módosítása az Azure Data Lake Analyticsben
description: Megtudhatja, hogyan állíthatja be és növelheti a kvótákat és a korlátokat Azure Data Lake Analytics (ADLA) fiókokban.
services: data-lake-analytics
ms.service: data-lake-analytics
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.topic: how-to
ms.date: 03/15/2018
ms.openlocfilehash: 0025e35f516543c8fe703daa647ca29ed3fb87e6
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87127587"
---
# <a name="adjust-quotas-and-limits-in-azure-data-lake-analytics"></a>Kvóták és korlátok módosítása az Azure Data Lake Analyticsben

Megtudhatja, hogyan módosíthatja és növelheti a kvótákat és a korlátokat Azure Data Lake Analytics (ADLA) fiókokban. A határértékek ismerete segít megérteni a U-SQL-feladatok viselkedését. Az összes kvóta-korlátozás puha, így növelheti a maximális korlátot, ha kapcsolatba lép az Azure támogatási szolgálatával.

## <a name="azure-subscriptions-limits"></a>Azure-előfizetések korlátai

**ADLA-fiókok maximális száma előfizetésben régiónként:** 5

Ha hatodik ADLA-fiókot próbál létrehozni, a következő hibaüzenet jelenik meg: "elérte az engedélyezett Data Lake Analytics fiókok maximális számát (5) a régióban az előfizetés neve alatt".

Ha ezt a korlátot szeretné kipróbálni, a következő lehetőségek közül választhat:
* Ha megfelelő, válasszon egy másik régiót
* vegye fel a kapcsolatot az Azure támogatási szolgálatával, és [Nyisson meg egy támogatási jegyet](#increase-maximum-quota-limits) , amely a kvóta növelését kéri.

## <a name="default-adla-account-limits"></a>Alapértelmezett ADLA-fiók korlátai

**Elemzési egységek maximális száma (AUs):** 250, alapértelmezett 32

A fiókban egyidejűleg futtatható au-t maximális száma. Ha az összes feladatban az összes feladat futása meghaladja ezt a korlátot, az újabb feladatok automatikusan várólistára kerülnek. Például:

* Ha egy második feladatot csak egy 32 AUs-vel rendelkező feladatokkal futtat, a rendszer a feladatok várólistáján várakozik, amíg az első művelet be nem fejeződik.
* Ha már négy feladatot futtat, és mindegyik 8 au-t használ, akkor ha olyan ötödik feladatot küld, amelynek 8 au-t kell megvárnia a feladat-várólistán, amíg nem érhető el 8 au.

    ![Azure Data Lake Analytics korlátok és kvóta lap](./media/data-lake-analytics-quota-limits/adjust-quota-limits.png)

**Az elemzési egységek maximális száma (AUs):** 250, alapértelmezett 32

Ez az a maximális szám, amelyet minden egyes feladathoz hozzá lehet rendelni a fiókjában. Az ennél nagyobb korláttal rendelkező feladatokat a rendszer elutasítja, kivéve, ha a küldőt egy számítási szabályzat (feladat-beküldési korlát) befolyásolja, amely több AUs-t ad hozzá a feladathoz. Ennek az értéknek a felső határa a fiókhoz tartozó AU-korlát.

**Az egyidejű U-SQL-feladatok maximális száma felhasználónként:** 20

A fiókban egyszerre futtatható feladatok maximális száma. Ezen érték felett az újabb feladatok automatikusan várólistára kerülnek.

## <a name="adjust-adla-account-limits"></a>ADLA-fiók korlátainak módosítása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válasszon egy meglévő ADLA-fiókot.
3. Kattintson a **Tulajdonságok** elemre.
4. Az igényeinek megfelelően módosítsa a **maximális**au-t, a **futó feladatok maximális számát**és a **feladat beküldési korlátait** .

## <a name="increase-maximum-quota-limits"></a>Maximális kvóták korlátozása

Az Azure-korlátokkal kapcsolatos további információkért tekintse meg az [Azure-szolgáltatásokra vonatkozó korlátozások dokumentációját](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-analytics-limits).

1. Nyisson meg egy támogatási kérést Azure Portalban.

    ![Azure Data Lake Analytics portál lapja](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Azure Data Lake Analytics portál lapja](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. Válassza ki a probléma típusa **kvótát**.
3. Válassza ki az **előfizetését** (Győződjön meg róla, hogy nem "próbaverzió" előfizetés).
4. Válassza ki a kvóta típusát **Data Lake Analytics**.

    ![Azure Data Lake Analytics portál lapja](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. A probléma lapon adja meg a kért növelési korlátot, amely **részletesen** ismerteti, hogy miért van szüksége erre a további kapacitásra.

    ![Azure Data Lake Analytics portál lapja](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. Ellenőrizze a kapcsolattartási adatokat, és hozza létre a támogatási kérést.

A Microsoft áttekinti a kérését, és a lehető leghamarabb megkísérli az üzleti igények kielégítését.

## <a name="next-steps"></a>További lépések

* [A Microsoft Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [Az Azure Data Lake Analytics kezelése az Azure PowerShell-lel](data-lake-analytics-manage-use-powershell.md)
* [Az Azure Data Lake Analytics-feladatok figyelése és hibaelhárítása az Azure Portal használatával](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
