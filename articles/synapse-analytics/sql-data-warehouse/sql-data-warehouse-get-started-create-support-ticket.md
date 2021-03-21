---
title: A kérelmek kvótájának növelése és támogatás kérése
description: Támogatási kérelem létrehozása az Azure szinapszis Analytics Azure Portaljában. A kérelmek kvótájának növelése vagy a probléma megoldására vonatkozó támogatás beszerzése.
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/10/2020
author: gaursa
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: b5b7563b49c6f0eb155223026797f8306e642f5a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104585655"
---
# <a name="request-quota-increases-and-get-support-for-azure-synapse-analytics"></a>A kérelmek kvótájának növekedése és az Azure szinapszis Analytics támogatásának beszerzése

Ez a cikk azt ismerteti, hogyan lehet támogatási jegyet beküldeni az Azure szinapszis Analytics Azure Portal. Ez a folyamat lehetővé teszi a kvóta növelését vagy technikai támogatási kérés beküldését a mérnöki támogatási csapat számára.

## <a name="create-a-support-ticket"></a>Támogatási jegy létrehozása

Az alábbi lépéseket követve hozzon létre egy új támogatási kérést az Azure szinapszis Analytics Azure Portalból.

1. A [Azure Portal](https://portal.azure.com) menüben válassza a **Súgó + támogatás** lehetőséget.

   ![A Súgó + támogatás hivatkozása](./media/sql-data-warehouse-get-started-create-support-ticket/help-plus-support.png)


1. A **Súgó és támogatás** területen válassza az **új támogatási kérelem** lehetőséget.

    ![Új támogatási kérelem létrehozása](./media/sql-data-warehouse-get-started-create-support-ticket/new-support-request.png)

1. Tekintse át az [Azure-támogatási csomagot](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).

   * A **számlázással, kvótával és az előfizetés kezelésével** kapcsolatos támogatás minden támogatási szinten elérhető.
   * A **hibajavítási** támogatás a [fejlesztői](https://azure.microsoft.com/support/plans/developer/), a standard, a [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/)vagy a [Premier](https://azure.microsoft.com/support/plans/premier/) [szintű](https://azure.microsoft.com/support/plans/standard/)támogatáson keresztül érhető el. A javítás/csere típusú problémákkal az Azure használata során fellépő hibák esetén lehet a támogatáshoz fordulni, ha a hibát nagy valószínűséggel a Microsoft terméke okozta.
   * A **fejlesztői mentorálás** és a **tanácsadási szolgáltatások** a [közvetlen professzionális](https://azure.microsoft.com/support/plans/prodirect/) támogatás és a [Premier](https://azure.microsoft.com/support/plans/premier/) szintű támogatási szinteken érhetők el.

   Premier szintű támogatási csomaggal a [Microsoft Premier online portálon](https://premier.microsoft.com/)is jelentheti az Azure szinapszis Analytics szolgáltatással kapcsolatos problémákat. Tekintse meg az [Azure-támogatási terveket](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) a különböző támogatási csomagokról, beleértve a hatókört, a válaszidőt, a díjszabást stb.  Az Azure-támogatással kapcsolatos gyakori kérdésekért tekintse meg az [Azure-támogatással](https://azure.microsoft.com/support/faq/)kapcsolatos gyakori kérdéseket.

1. A **probléma típusa** mezőben válassza ki a megfelelő típusú problémát. A hibajavítási problémák esetén válassza a **technikai** lehetőséget. A kvóta-növelési kérelmek esetében válassza a **szolgáltatás-és előfizetési korlátok (kvóták)** lehetőséget.

   ![Probléma típusának kiválasztása](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-issue-type.png)  

   > [!NOTE]
   > Ez a cikk a kvóta növelésére vonatkozó főként. A probléma megoldásához szükséges támogatási kérelmek esetében azonban a **technikai** lehetőséget is kiválaszthatja. Ha a **technikai** lehetőséget választja, a rendszer megkéri, hogy adjon meg egy összefoglalást, majd azonosítsa a probléma típusát a **probléma típusának** kiválasztása lehetőség kiválasztásával. Előfordulhat, hogy a probléma megoldásához megoldásokat talál. Ha a bemutatott megoldások nem oldják meg a problémát, válassza a **Next (tovább** ) gombot, és fejezze be az űrlapot a támogatási jegy elküldéséhez.

1. A kvóta-növelési kérelmek esetében válassza az **Azure szinapszis Analytics** lehetőséget a **kvóta típusához**. Ezután válassza a **Tovább: megoldások >>** elemet.

   ![Adja meg a kvóta típusát](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-type.png)

1. További információk megadásához a **részletek** ablakban válassza a **részletek megadása** lehetőséget.

   ![A "részletek megadása" hivatkozás](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>Kvóta-kérelmek típusai

Az **ENTER Details (részletek megadása** ) elemre kattintva megjelenik a **kvóta részletei** ablak, amely lehetővé teszi további információk hozzáadását. A következő szakaszok ismertetik az Azure szinapszis Analytics szolgáltatáshoz elérhető különböző kvóták kéréseit.

### <a name="synapse-sql-pool-data-warehouse-units-dwus-per-server"></a>Szinapszis SQL Pool adattárház-egységek (DWU-EK) kiszolgálónkénti bontásban

A következő lépésekkel növelheti a DWU egy kiszolgálón.

1. Válassza ki a **SZINAPSZIS SQL-készlet DWU** .

1. Válassza ki azt az **erőforrást** , amelynek a kvótáját a legördülő lista használatával szeretné alkalmazni.

1. Adja meg az új kvótát a **kérési kvóta** szakaszban.

1. Válassza a **Mentés és folytatás** lehetőséget.

   ![DWU-kvóta részletei](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)


### <a name="servers-per-subscription"></a>Kiszolgálók/előfizetés

Ha a kiszolgálók számának növelését szeretné igénybe venni, a következő lépéseket kell elvégeznie:

1. Válassza ki az **előfizetéshez tartozó SQL-kiszolgálókat** a kvóta típusaként.

1. A **hely** listában válassza ki a használni kívánt Azure-régiót. Az egyes régiókban a kvóta/előfizetés.

1. A **kérelem kvóta** mezőben adja meg az adott régióban található kiszolgálók maximális számának kérelmét.

   ![Kiszolgálók kvótájának részletei](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)



1. Válassza a **Mentés és folytatás** lehetőséget.

Egyes ajánlati típusok nem érhetők el minden régióban. A következő hibaüzenet jelenhet meg:

![Régió-hozzáférési hiba](./media/sql-data-warehouse-get-started-create-support-ticket/region-access-error.png)

### <a name="enable-subscription-access-to-a-region"></a>Előfizetés-hozzáférés engedélyezése egy régióhoz

Ha engedélyezni szeretné a régió hozzáférését egy előfizetéshez, a következő lépéseket kell elvégeznie:  

1. Válassza ki a **SZINAPSZIS SQL-készlet (adatraktár) régió hozzáférési** kvótájának típusát.

1. Válassza ki a régiót a legördülő listából egy **hely** kiválasztásával.

1. Adja meg a DWU teljesítményre vonatkozó követelményét a **DWU szükséges** szakaszban.

1. Adja meg az **üzleti követelmények leírását**. 

1. Válassza a **Mentés és folytatás** lehetőséget.

![Régió elérése](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-region.png)


### <a name="for-other-quota-requests"></a>Egyéb kvóta-kérelmek esetén

A kvóta típusa legördülő **menüben válassza ki** az egyéb kvóták kérése lehetőséget a következőhöz:

![Egyéb kvóta részletei](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details.png)

## <a name="submit-your-request"></a>A kérés elküldése

Az utolsó lépés a SQL Database támogatási kérelem hátralévő részleteinek kitöltése. Ezután válassza a **Tovább: Áttekintés +>>létrehozása** elemet.

![A létrehozás részleteinek áttekintése](./media/sql-data-warehouse-get-started-create-support-ticket/review-create-details.png)

A kérelem részleteinek áttekintése után válassza a **Létrehozás** lehetőséget a kérelem elküldéséhez.

![Jegy létrehozása](./media/sql-data-warehouse-get-started-create-support-ticket/create-ticket.png)

## <a name="monitor-a-support-ticket"></a>Támogatási jegy nyomon követése

A támogatási kérelem elküldését követően az Azure-támogatási csapat kapcsolatba lép Önnel. A kérelem állapotának és részleteinek megtekintéséhez válassza az **összes támogatási kérelem** lehetőséget az irányítópulton.

![Állapot ellenőrzése](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Egyéb erőforrások

Az Azure szinapszis Analytics-Közösséggel is csatlakozhat [stack overflow](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse) vagy a [Microsoft Q&az Azure szinapszis Analytics kérdéseit tartalmazó oldalon](/answers/topics/azure-synapse-analytics.html).