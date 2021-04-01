---
title: A szinapszis munkaterület funkcióinak engedélyezése
description: Ez a dokumentum azt ismerteti, hogy egy felhasználó hogyan engedélyezheti a szinapszis munkaterület funkcióit egy meglévő dedikált SQL-készleten (korábban SQL DW).
services: synapse-analytics
author: antvgski
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/25/2020
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 769ca4cf0ecbdba5fd80d977eb69c8a4f58df55f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98071187"
---
# <a name="enabling-synapse-workspace-features-for-a-dedicated-sql-pool-formerly-sql-dw"></a>A szinapszis munkaterület funkcióinak engedélyezése dedikált SQL-készlethez (korábban SQL DW)

Az összes SQL-adattárház-felhasználó mostantól hozzáférhet és használhat egy meglévő dedikált SQL-készletet (korábban SQL DW-példányt) a szinapszis Studióban és munkaterületen. A felhasználók a szinapszis Studio és a munkaterület használatát az automatizálás, a kapcsolatok és az eszközök befolyásolása nélkül használhatják. Ez a cikk azt ismerteti, hogy egy meglévő Azure szinapszis Analytics-felhasználó hogyan engedélyezheti a szinapszis munkaterület szolgáltatásait egy meglévő dedikált SQL-készlet (korábban SQL DW) számára. A felhasználó kiterjesztheti meglévő elemzési megoldását úgy, hogy kihasználja a szinapszis munkaterületen és a studión keresztül elérhető új funkciók széles választékát.   

## <a name="prerequisites"></a>Előfeltételek
A szinapszis munkaterület funkcióinak az adattárházban való engedélyezése előtt meg kell győződnie arról, hogy a következőkkel rendelkezik
- Az SQL logikai kiszolgálón futtatott SQL-erőforrások létrehozásához és kezeléséhez szükséges jogosultságok.
- Az Azure szinapszis-erőforrások létrehozásához szükséges jogosultságok.
- A logikai kiszolgálón azonosított Azure Active Directory-rendszergazda

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="enabling-synapse-workspace-features-for-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>A szinapszis munkaterület funkcióinak engedélyezése meglévő dedikált SQL-készlethez (korábban SQL DW)

A szinapszis munkaterület funkcióit engedélyezheti bármely meglévő dedikált SQL-készleten (korábban SQL DW) egy támogatott régióban. Ez a funkció csak a Azure Portalon keresztül érhető el.

A következő lépésekkel hozhat létre egy szinapszis-munkaterületet a meglévő adattárházhoz.
1. Válasszon ki egy meglévő dedikált SQL-készletet (korábbi nevén SQL DW), és nyissa meg az Áttekintés lapot.
2. Válassza ki az **új szinapszis munkaterületet** a felső menüsorban, vagy közvetlenül az alábbi üzenetben.
3. Az **új Azure szinapszis-munkaterület létrehozása** oldalon az új szinapszis munkaterületen elérhetővé tett adattárházak listájának áttekintése után. Kattintson a **Folytatás** gombra a folytatáshoz.
4. Az alapismeretek lapon a meglévő dedikált SQL-készletet (a **projekt** részletei szakaszt előre ki kell tölteni ugyanazzal az **előfizetéssel** és **erőforráscsoporthoz** , amelyet a logikai kiszolgáló alatt telepítenek). A munkaterület **részletei** területen a **munkaterület** neve előre ki van töltve az SQL logikai kiszolgáló azonos nevével és régiójával, így biztosítva, hogy az új szinapszis munkaterület és a logikai kiszolgáló közötti kapcsolat a létesítési folyamat során hozható létre. A kapcsolat közzétételét meg kell őrizni, hogy a munkaterület és a Studio használatával továbbra is elérhető legyen a dedikált SQL-készlet (korábban SQL DW) példányok.
5. Navigáljon a 2. generációs Data Lake Storage kiválasztásához.
6. Válassza az **új létrehozása** lehetőséget, vagy válasszon ki egy meglévő **Data Lake Storage Gen2** a következő kiválasztása előtt **: hálózatkezelés**.
7. Válassza ki a **kiszolgáló nélküli példányhoz** tartozó **SQL-rendszergazdai jelszót** . A jelszó módosítása nem frissíti vagy nem módosítja a logikai kiszolgáló SQL-hitelesítő adatait. Ha a rendszer által meghatározott jelszót részesíti előnyben, hagyja üresen ezeket a mezőket. Ez a jelszó bármikor módosítható az új munkaterületen belül. A rendszergazdának meg kell egyeznie a SQL Serverban használt névvel.
8. Válassza ki az előnyben részesített **hálózati beállításokat** , és válassza a **felülvizsgálat + létrehozás** lehetőséget a munkaterület-kiépítés indításához.
9. Válassza a **goto erőforrás** lehetőséget az új munkaterület megnyitásához.

    > [!NOTE]
    > A logikai kiszolgálón tárolt összes dedikált SQL-készlet (korábban SQL DW) az új munkaterületen keresztül érhető el.

## <a name="post-provisioning-steps"></a>Kiépítési lépések közzététele
A következő lépések végrehajtásával biztosíthatja, hogy a meglévő dedikált SQL-készlet (korábbi nevén SQL DW) példányok elérhetők legyenek a szinapszis Studio használatával.
1. A szinapszis munkaterület áttekintése lapon válassza a **csatlakoztatott kiszolgáló** elemet. A **csatlakoztatott kiszolgáló** végigvezeti az adattárházat üzemeltető csatlakoztatott SQL logikai kiszolgálónak. Az alapvető menüben válassza a **csatlakoztatott kiszolgáló** elemet.
2. Nyissa meg a **tűzfalakat és a virtuális hálózatokat** , és győződjön meg arról, hogy az ügyfél IP-címe vagy egy előre meghatározott IP-címtartomány hozzáfér a logikai kiszolgálóhoz.
3. Nyissa meg **Active Directory rendszergazdát** , és győződjön meg arról, hogy be van ÁLLÍTVA egy HRE-rendszergazda a logikai kiszolgálón.
4. Válassza ki a logikai kiszolgálón üzemeltetett dedikált SQL-készlet (korábban SQL DW) példányok egyikét. Az Áttekintés lapon válassza a **szinapszis Studio elindítása** vagy a [Bejelentkezés a szinapszis studióba](https://web.azuresynapse.net) lehetőséget, és jelentkezzen be a munkaterületre.

5. Nyissa meg az **adatközpontot** , és bontsa ki a dedikált SQL-készletet az Object Explorerben, és győződjön meg arról, hogy a hozzáférése és a lekérdezése az adattárházat

    > [!NOTE] 
    > A csatlakoztatott munkaterületek bármikor törölhetők. A munkaterület törlése nem törli a csatlakoztatott dedikált SQL-készletet (korábban az SQL DW-t). A munkaterület funkció újból engedélyezhető a dedikált SQL-készletben (korábban SQL DW), ha a törlési művelet befejeződött.

## <a name="next-steps"></a>Következő lépések
Bevezetés a [szinapszis munkaterülettel és a Studióval](../get-started.md).
