---
title: Kvótaemelés kérése és támogatás kérése
description: Támogatási kérés létrehozása a Azure Portal Azure Synapse Analytics. Kvótaemelés kérése vagy problémamegoldási támogatás kérése.
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/10/2020
author: julieMSFT
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 5256c6d25a9c7acfc45cdffee05c95fb3407c24a
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568318"
---
# <a name="request-quota-increases-and-get-support-for-azure-synapse-analytics"></a>Kvótaemelés kérése és támogatás kérése Azure Synapse Analytics

Ez a cikk azt ismerteti, hogyan küldhet támogatási jegyet a Azure Portal támogatási Azure Synapse Analytics. Ezzel a folyamattal kérheti a kvóta emelését, vagy technikai támogatási kérést küldhet a mérnöki támogatási csapatnak.

## <a name="create-a-support-ticket"></a>Támogatási jegy létrehozása

A következő lépésekkel hozhat létre új támogatási kérést a Azure Portal a Azure Synapse Analytics.

1. A [Azure Portal](https://portal.azure.com) menüben válassza a **Súgó és támogatás lehetőséget.**

   ![A Súgó és támogatás hivatkozás](./media/sql-data-warehouse-get-started-create-support-ticket/help-plus-support.png)


1. A **Súgó és támogatás mezőben** válassza az Új támogatási kérelem **lehetőséget.**

    ![Új támogatási kérelem létrehozása](./media/sql-data-warehouse-get-started-create-support-ticket/new-support-request.png)

1. Tekintse át a [Azure-támogatás tervét.](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)

   * A **számlázással, kvótával és az előfizetés kezelésével** kapcsolatos támogatás minden támogatási szinten elérhető.
   * **A javítás/javítás** [fejlesztői,](https://azure.microsoft.com/support/plans/developer/) [standard,](https://azure.microsoft.com/support/plans/standard/) [Közvetlen professzionális támogatás,](https://azure.microsoft.com/support/plans/prodirect/)vagy [Premier támogatással támogatott.](https://azure.microsoft.com/support/plans/premier/) A javítás/csere típusú problémákkal az Azure használata során fellépő hibák esetén lehet a támogatáshoz fordulni, ha a hibát nagy valószínűséggel a Microsoft terméke okozta.
   * **A fejlesztői mentorálás** és **a** tanácsadási szolgáltatások a Közvetlen professzionális támogatás [premier](https://azure.microsoft.com/support/plans/prodirect/) szintű [támogatási](https://azure.microsoft.com/support/plans/premier/) szinteken érhetők el.

   Premier szintű támogatási csomag esetén a Microsoft Premier online portálon is Azure Synapse Analytics a [problémákat.](https://premier.microsoft.com/) A [Azure-támogatás támogatási](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) csomagokkal kapcsolatos további információkért lásd: hatókör, válaszidő, díjszabás stb.  A gyakori kérdésekért lásd a Azure-támogatás gyakori [Azure-támogatás.](https://azure.microsoft.com/support/faq/)

1. A **Probléma típusa mezőben** válassza ki a megfelelő problématípust. A javítással kapcsolatos problémák esetén válassza a **Technikai lehetőséget.** A kvóta növelésével kapcsolatos kérések esetén válassza a **Szolgáltatási és előfizetési korlátok (kvóták) lehetőséget.**

   ![Problématípus kiválasztása](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-issue-type.png)  

   > [!NOTE]
   > A cikk további részében a kvóta növelésére vonatkozó kérelmekre összpontosítunk. A problémamegoldási támogatási kérelmekhez azonban a **Technikai** lehetőséget is választhatja. Ha a **Technikai** lehetőséget választja, a felhasználónak meg kell adnia egy összegzést, majd azonosítania kell a probléma típusát a Válassza ki a **probléma típusát lehetőséget választva.** Előfordulhat, hogy megoldásokat talál a probléma megoldásához. Ha a bemutatott megoldások nem oldják meg a problémát, kattintson a **Tovább:Részletek** gombra, és töltse ki az űrlapot a támogatási jegy küld küldelmének elküldjön.

1. A kvótaemelési kérések esetében **válassza** a Azure Synapse Analytics **kvótatípust.** Ezután válassza **a Tovább: Megoldások >>** lehetőséget.

   ![Kvótatípus kiválasztása](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-type.png)

1. A Részletek **ablakban** válassza a **Részletek beíratás lehetőséget** a további információk beíratáshoz.

   ![A "Részleteket" hivatkozás](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>Kvótakérés-típusok

A **Részletek beíratás** lehetőség kiválasztásával megjelenik a **Kvóta** részletei ablak, amely további információk hozzáadását teszi lehetővé. A következő szakaszok a különböző kvótakéréseket ismertetik, amelyek a Azure Synapse Analytics.

### <a name="synapse-sql-pool-data-warehouse-units-dwus-per-server"></a>Synapse SQL készlet Data Warehouse (DWUs) kiszolgálónként

A következő lépésekkel kérheti a DWUs kiszolgálónkénti növelésének kérését.

1. Válassza ki **a Synapse SQL DWUs kiszolgálónkénti kvótatípusonkénti DWUs-t.**

1. A legördülő **listában** válassza ki azt az erőforrást, amelyre alkalmazni szeretné a kvótaemelést.

1. Adja meg az új kvótát a **Kérelemkvóta szakaszban.**

1. Válassza a **Mentés és folytatás** lehetőséget.

   ![DWU-kvóta részletei](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)


### <a name="servers-per-subscription"></a>Kiszolgálók előfizetésenként

Az előfizetésenkénti kiszolgálók számának növeléséhez a következő lépéseket kell végrehajtania:

1. **Kvótatípusként válassza ki az sql servers per subscription (SQL-kiszolgálók** előfizetésenként) lehetőséget.

1. A Hely **listában** válassza ki a használni kívánt Azure-régiót. A kvóta előfizetésenként van megszava minden régióban.

1. A Kvóta **kérése** mezőben adja meg az adott régióban található kiszolgálók maximális számára vonatkozó kérést.

   ![Kiszolgálókra vonatkozó kvóta részletei](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)



1. Válassza a **Mentés és folytatás** lehetőséget.

Egyes ajánlattípusok nem érhetők el minden régióban. A következő hibaüzenet jelenhet meg:

![Régió-hozzáférési hiba](./media/sql-data-warehouse-get-started-create-support-ticket/region-access-error.png)

### <a name="enable-subscription-access-to-a-region"></a>Előfizetés-hozzáférés engedélyezése egy régióhoz

Ha engedélyezni szeretné a régióhoz való hozzáférést egy előfizetéshez, a következő lépéseket kell végrehajtania:  

1. Válassza ki **a Synapse SQL (adattárház) régió hozzáférési kvótáját.**

1. Válassza ki a régiót úgy, hogy kiválaszt egy **Helyet** a legördülő listából.

1. Adja meg a DWU teljesítménykövetelményét a **szükséges DWU-szakaszban.**

1. Adja meg **az üzleti követelmények leírását.** 

1. Válassza a **Mentés és folytatás** lehetőséget.

![Régió elérése](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-region.png)


### <a name="for-other-quota-requests"></a>Egyéb kvótakérések esetén

Az **egyéb kvótakérés-típusokhoz** válassza az Egyéb kvótakérés lehetőséget a kvótatípus legördülő menüből:

![Egyéb kvótaadatok](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details.png)

## <a name="submit-your-request"></a>A kérés elküldése

Az utolsó lépés a támogatási kérelem további részleteinek SQL Database kitöltése. Ezután válassza **a Tovább: Áttekintés + létrehozás>>** lehetőséget.

![A létrehozás részleteinek áttekintése](./media/sql-data-warehouse-get-started-create-support-ticket/review-create-details.png)

A kérelem részleteinek áttekintése után válassza a **Létrehozás lehetőséget** a kérés elküldéshez.

![Jegy létrehozása](./media/sql-data-warehouse-get-started-create-support-ticket/create-ticket.png)

## <a name="monitor-a-support-ticket"></a>Támogatási jegy nyomon követése

A támogatási kérelem beküldését követően a támogatási Azure-támogatás kapcsolatba fog lépni Önnel. A kérelem állapotának és részleteinek ellenőrzéshez válassza a **Minden támogatási kérelem** lehetőséget az irányítópulton.

![Állapot ellenőrzése](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Egyéb erőforrások

A microsoftos ügyfélszolgálati Azure Synapse Analytics [a](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse) Stack Overflow a Microsoft Q [&A](/answers/topics/azure-synapse-analytics.html)kérdések oldalán is Azure Synapse Analytics.