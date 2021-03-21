---
title: Kvóta növelésének kérése
description: Ez az oldal azt ismerteti, hogyan lehet támogatási kérést létrehozni a Azure SQL Database és az Azure SQL felügyelt példányaira vonatkozó kvóták növelésére.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: how-to
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 06/04/2020
ms.openlocfilehash: 27719663acfbdbcd7293defc4b746153359adb61
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98251852"
---
# <a name="request-quota-increases-for-azure-sql-database-and-sql-managed-instance"></a>A kérelmek kvótájának növekedése Azure SQL Database és az SQL felügyelt példányainál
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Ez a cikk a Azure SQL Database és az Azure SQL felügyelt példányaira vonatkozó kvóta-növelés igénylését ismerteti. Azt is ismerteti, hogyan engedélyezhető az előfizetés-hozzáférés egy adott régióhoz, és hogyan lehet az adott hardvert egy adott régióban engedélyezni.

## <a name="create-a-new-support-request"></a><a id="newquota"></a> Új támogatási kérelem létrehozása

A következő lépésekkel hozzon létre egy új támogatási kérést a SQL Database Azure Portal.

1. A [Azure Portal](https://portal.azure.com) menüben válassza a **Súgó + támogatás** lehetőséget.

   ![A Súgó + támogatás hivatkozása](./media/quota-increase-request/help-plus-support.png)

1. A **Súgó és támogatás** területen válassza az **új támogatási kérelem** lehetőséget.

    ![Új támogatási kérelem létrehozása](./media/quota-increase-request/new-support-request.png)

1. A **probléma típusa** beállításnál válassza a **szolgáltatás-és előfizetési korlátok (kvóták)** lehetőséget.

   ![Probléma típusának kiválasztása](./media/quota-increase-request/select-quota-issue-type.png)

1. Az **előfizetés** mezőben válassza ki azt az előfizetést, amelynek a kvótáját emelni szeretné.

   ![Válassza ki az előfizetést egy nagyobb kvótához](./media/quota-increase-request/select-subscription-support-request.png)

1. A **kvóta típusa** beállításnál válasszon a következő típusú kvóták közül:

   - **SQL Database** az önálló adatbázisok és a rugalmas készlet kvótái számára.
   - Felügyelt példányok **SQL Database felügyelt példánya** .

   Ezután válassza a **Tovább: megoldások >>** elemet.

   ![Adja meg a kvóta típusát](./media/quota-increase-request/select-quota-type.png)

1. További információk megadásához a **részletek** ablakban válassza a **részletek megadása** lehetőséget.

   ![Részletek hivatkozásának megadása](./media/quota-increase-request/provide-details-link.png)

A **részletek megadása** elemre kattintva megjelenik a **kvóta részletei** ablak, amely lehetővé teszi további információk hozzáadását. A következő szakaszok a **felügyelt példányok** **SQL Database** és SQL Database különböző lehetőségeit ismertetik.

## <a name="sql-database-quota-types"></a><a id="sqldbquota"></a> SQL Database kvóta típusai

A következő szakaszok ismertetik a kvóta-növelési lehetőségeket a **SQL Database** kvóták típusaihoz:

- Adatbázis-tranzakciós egységek (DTU) kiszolgálónként
- Kiszolgálók/előfizetés
- Régió-hozzáférés előfizetésekhez vagy adott hardverekhez

### <a name="database-transaction-units-dtus-per-server"></a>Adatbázis-tranzakciós egységek (DTU) kiszolgálónként

A következő lépésekkel növelheti a DTU egy kiszolgálón.

1. Válassza ki az **adatbázis-tranzakciós egységeket (DTU) a kiszolgálói** kvóta típusa mezőben.

1. Az **erőforrás** listában válassza ki a célként használni kívánt erőforrást.

1. Az **új kvóta** mezőben adja meg a kért új DTU-korlátot.

   ![DTU-kvóta részletei](./media/quota-increase-request/quota-details-dtus.png)

További információ: az [önálló adatbázisok erőforrás-korlátai a DTU beszerzési modell](resource-limits-dtu-single-databases.md) és az [erőforrások korlátai a rugalmas készletekhez a DTU beszerzési modell használatával](resource-limits-dtu-elastic-pools.md).

### <a name="servers-per-subscription"></a>Kiszolgálók/előfizetés

A következő lépések végrehajtásával növelheti a kiszolgálók számát az előfizetésben.

1. Válassza ki a **kiszolgálók/előfizetés** típusú kvótát.

1. A **hely** listában válassza ki a használni kívánt Azure-régiót. Az egyes régiókban a kvóta/előfizetés.

1. Az **új kvóta** mezőben adja meg az adott régióban található kiszolgálók maximális számára vonatkozó kérelmét.

   ![Kiszolgálók kvótájának részletei](./media/quota-increase-request/quota-details-servers.png)

További információ: [SQL Database erőforrás-korlátozások és erőforrás-szabályozás](resource-limits-logical-server.md).

### <a name="enable-subscription-access-to-a-region"></a><a id="region"></a> Előfizetés-hozzáférés engedélyezése egy régióhoz

Egyes ajánlati típusok nem érhetők el minden régióban. Előfordulhat, hogy a következő hibaüzenet jelenik meg:

`Your subscription does not have access to create a server in the selected region. For the latest information about region availability for your subscription, go to aka.ms/sqlcapacity. Please try another region or create a support ticket to request access.`

Ha az előfizetéshez egy adott régióban hozzáférésre van szüksége, válassza a **régió-hozzáférés** lehetőséget. A kérelemben adja meg az ajánlat és az SKU adatait, amelyeket engedélyezni szeretne a régió számára. Az ajánlat és az SKU beállításainak megismeréséhez tekintse meg a [Azure SQL Database díjszabását](https://azure.microsoft.com/pricing/details/sql-database/single/).

1. Válassza ki a **régió hozzáférési** kvótájának típusát.

1. A **válasszon helyet** listából válassza ki a használni kívánt Azure-régiót. Az egyes régiókban a kvóta/előfizetés.

1. Adja meg a **vásárlási modellt**, és a **várt felhasználás** részleteit.

   ![Kérelem régióhoz való hozzáférése](./media/quota-increase-request/quota-request.png)

### <a name="request-enabling-specific-hardware-in-a-region"></a>Adott hardver adott régióban való engedélyezésére vonatkozó kérelem

Ha a használni kívánt [hardver-létrehozás](service-tiers-vcore.md#hardware-generations) nem érhető el a régióban (lásd a [hardver rendelkezésre állását](service-tiers-vcore.md#hardware-availability)), akkor a következő lépésekkel kérheti le.

1. Válassza ki az **egyéb kvóta-kérelem** típusú kvótát.

1. A **Leírás** mezőben adja meg a kérelmét, beleértve a hardver generációjának nevét, valamint annak a régiónak a nevét, amelyhez szüksége van.

   ![Hardver kérése egy új régióban](./media/quota-increase-request/hardware-in-new-region.png)

## <a name="submit-your-request"></a>A kérés elküldése

Az utolsó lépés az SQL Database-kvótakéréssel kapcsolatos további adatok kitöltése. Ezután válassza a **Tovább: Felülvizsgálat + létrehozás>>** lehetőséget, és a kérés részleteinek áttekintése után kattintson a **Létrehozás** elemre a kérés létrehozásához.

## <a name="next-steps"></a>További lépések

A kérés elküldését követően megtörténik annak felülvizsgálata. Az űrlapon megadott információk alapján elküldjük Önnek a választ.

Az egyéb Azure-korlátokkal kapcsolatos további információkért lásd az [Azure-előfizetések és-szolgáltatások korlátozásait, kvótáit és korlátozásait](../../azure-resource-manager/management/azure-subscription-service-limits.md)ismertető témakört.
