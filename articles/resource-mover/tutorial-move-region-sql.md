---
title: Azure SQL-erőforrások áthelyezése régiók között az Azure erőforrás-mozgatóval
description: Ismerje meg, hogyan helyezhetők át Azure SQL-erőforrások egy másik régióba az Azure-erőforrás-mozgató használatával
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 4678a6128be13ac61dc4ac67bbd1a17e99c6d24d
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820229"
---
# <a name="tutorial-move-azure-sql-database-resources-to-another-region"></a>Oktatóanyag: Azure SQL Database-erőforrások áthelyezése egy másik régióba

Ebből az oktatóanyagból megtudhatja, hogyan helyezhet át Azure SQL-adatbázisokat és rugalmas készleteket egy másik Azure-régióba az Azure-beli [erőforrás-mozgató](overview.md)használatával.

> [!NOTE]
> Az Azure-beli erőforrás-mozgató szolgáltatás jelenleg előzetes verzióban érhető el.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az előfeltételek és a követelmények ellenőrzése.
> * Válassza ki az áthelyezni kívánt erőforrásokat.
> * Erőforrás-függőségek feloldása.
> * Készítse elő és helyezze át a SQL Server a célként megadott régióba.
> * Adatbázisok és rugalmas készletek előkészítése és áthelyezése.
> * Döntse el, hogy el kívánja-e dobni vagy véglegesíteni az áthelyezést. 
> * Az áthelyezést követően szükség esetén eltávolíthatja az erőforrásokat a forrás régióban. 

> [!NOTE]
> Az oktatóanyagok a forgatókönyvek kipróbálásának leggyorsabb elérési útját mutatják be, és az alapértelmezett beállításokat használják. 

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/). Ezután jelentkezzen be a [Azure Portalba](https://portal.azure.com).

## <a name="prerequisites"></a>Előfeltételek

-  Győződjön meg arról, hogy *tulajdonosi* hozzáféréssel rendelkezik az áthelyezni kívánt erőforrásokat tartalmazó előfizetéshez.
    - Amikor először ad hozzá egy erőforrást egy adott forráshoz és célhoz egy Azure-előfizetésben, az erőforrás-mozgató létrehoz egy [rendszerhez rendelt felügyelt identitást](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (korábbi nevén felügyelt szolgáltatás azonosítása (MSI)), amelyet az előfizetés megbízhatónak tekint.
    - Az identitás létrehozásához, valamint a szükséges szerepkör (közreműködő vagy felhasználói hozzáférés rendszergazdája a forrás-előfizetésben) való hozzárendeléséhez az erőforrások hozzáadásához használt fióknak *tulajdonosi* engedélyekkel kell rendelkeznie az előfizetésben. [További](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) információ az Azure-szerepkörökről.
- Az előfizetéshez elegendő kvóta szükséges ahhoz, hogy létrehozza a célhelyen áthelyezett erőforrásokat. Ha nem rendelkezik kvótával, [kérjen további korlátozásokat](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Ellenőrizze, hogy a cél régióhoz milyen díjszabás és milyen díjak vannak áthelyezve. A [díjszabási számológép](https://azure.microsoft.com/pricing/calculator/) használatával segítséget nyújthat.
    

## <a name="check-sql-requirements"></a>SQL-követelmények keresése

1. [Győződjön](support-matrix-move-region-sql.md) meg arról, hogy mely adatbázis/rugalmas készlet funkciók támogatottak a másik régióba való áthelyezéshez.
2. A célként megadott régióban hozzon létre egy célkiszolgáló minden forráskiszolgálón. [További információk](../azure-sql/database/active-geo-replication-security-configure.md#how-to-configure-logins-and-users).
4. Ha az adatbázisok transzparens adattitkosítással (TDE) vannak titkosítva, és a Azure Key Vault saját titkosítási kulcsát használja, [Ismerje meg, hogyan](../key-vault/general/move-region.md) helyezheti át a kulcstartókat egy másik régióba.
5. Ha az SQL-adatszinkronizálás engedélyezve van, a rendszer a tagok adatbázisainak áthelyezését is támogatja. Az áthelyezést követően be kell állítania az SQL-adatszinkronizálást az új céladatbázis-adatbázisba.
6. Az áthelyezés előtt távolítsa el a speciális adatbiztonsági beállításokat. Az áthelyezést követően [konfigurálja a beállításokat](../azure-sql/database/azure-defender-for-sql.md) a célként megadott régió SQL Server szintjén.
7. Ha a naplózás engedélyezve van, a házirendek az áthelyezés után az alapértelmezett értékre állnak vissza. Az áthelyezést követően [állítsa be újra a naplózást](../azure-sql/database/auditing-overview.md) .
7. A forrásadatbázis biztonsági mentési adatmegőrzési szabályzatait a rendszer átadja a céladatbázis számára. [További](../azure-sql/database/long-term-backup-retention-configure.md) információ a beállítások áthelyezés utáni módosításáról.
8. Az áthelyezés előtt távolítsa el a kiszolgálói szintű tűzfalszabályok szabályait. Az adatbázis-szintű tűzfalszabályok a forrás-kiszolgálóról a célkiszolgálóra másolódnak át az áthelyezés során. Az áthelyezést követően [állítsa be a tűzfalszabályok](../azure-sql/database/firewall-create-server-level-portal-quickstart.md) SQL Server a célként megadott régióban.
9. Az áthelyezés előtt távolítsa el az automatikus finomhangolási beállításokat. Az automatikus [hangolást](../azure-sql/database/automatic-tuning-enable.md) az áthelyezés után újra be kell állítania.
10. Az áthelyezés előtt távolítsa el az adatbázis riasztási beállításait. [Alaphelyzetbe állítás](../azure-sql/database/alerts-insights-configure-portal.md) után.
    
## <a name="select-resources"></a>Erőforrások kiválasztása

Válassza ki az áthelyezni kívánt erőforrásokat.

- Bármelyik támogatott erőforrástípust kiválaszthatja a kiválasztott forrásoldali régióban található bármelyik erőforráscsoporthoz.
- Az erőforrásokat egy olyan célcsoportba helyezi át, amely ugyanabban az előfizetésben található, mint a forrásoldali régió. Ha módosítani szeretné az előfizetést, ezt az erőforrások áthelyezése után teheti meg.

1. A Azure Portal keresse meg az *erőforrás-mozgató* kifejezést. Ezután a **szolgáltatások** területen válassza az **Azure-erőforrás mozgató** lehetőséget.

     ![Az erőforrás-mozgató keresési eredményei a Azure Portal](./media/tutorial-move-region-sql/search.png)

2. Az **Áttekintés** területen kattintson az első **lépések** elemre.

    ![Gomb egy másik régióba való áthelyezéshez szükséges erőforrások hozzáadásához](./media/tutorial-move-region-sql/get-started.png)

3. Az **erőforrások áthelyezése**  >  **forrás + cél** területen válassza ki a forrás-előfizetést és a régiót.
4. A **cél** mezőben válassza ki azt a régiót, ahová át szeretné helyezni az erőforrásokat. Ezután kattintson a **Tovább** gombra.

    ![A forrás és a cél régió kiválasztására szolgáló lap](./media/tutorial-move-region-sql/source-target.png)

6. Az **áthelyezni kívánt erőforrások** területen kattintson az **erőforrások kiválasztása** elemre.
7. Az **erőforrások kiválasztása** területen válassza ki az erőforrásokat. Csak az áthelyezéshez támogatott erőforrásokat adhat hozzá. Ezután kattintson a **kész** gombra.

    ![Az áthelyezendő SQL-erőforrások kiválasztására szolgáló lap](./media/tutorial-move-region-sql/select-resources.png)

8. Az **áthelyezni kívánt erőforrásokhoz** kattintson a **tovább** gombra.

9. A **felülvizsgálat + Hozzáadás** elemnél ellenőrizze a forrás-és a célhely beállításait. Győződjön meg arról, hogy tudomásul veszi, hogy az áthelyezéssel kapcsolatos metaadatok a metaadatok régiójában erre a célra létrehozott erőforráscsoporthoz lesznek tárolva.


    ![Lap a beállítások áttekintéséhez és az áthelyezés folytatásához](./media/tutorial-move-region-sql/review.png)

10. Az erőforrások hozzáadásának megkezdéséhez kattintson a **Folytatás** gombra.
11. A hozzáadási folyamat sikeres befejeződése után kattintson az **erőforrások hozzáadása az áthelyezéshez** az értesítési ikonra.
12. Az értesítésre való kattintás után tekintse át az erőforrásokat az **egyes régiók között** oldalon.


> [!NOTE]
> 
> - A SQL Server jelenleg *manuális hozzárendelés függőben* állapotú.
> - Az egyéb hozzáadott erőforrások *előkészítése függő* állapotban van.
> - Ha el kívánja távolítani egy erőforrást egy áthelyezési gyűjteményből, akkor a művelet metódusa attól függ, hogy hol található az áthelyezési folyamat. [További információk](remove-move-resources.md).

## <a name="resolve-dependencies"></a>Függőségek feloldása


1. Ha az erőforrások között a **problémák** oszlopban a *függőségek ellenőrzése üzenet jelenik* meg az **egyes régiók között**, kattintson a **függőségek ellenőrzése** gombra. Az érvényesítési folyamat megkezdődik.
2. Ha függőségek találhatók, kattintson a **függőségek hozzáadása** lehetőségre.

    ![Függőség hozzáadására szolgáló gomb](./media/tutorial-move-region-sql/add-dependencies.png)
   
3. A **függőségek hozzáadása** területen válassza ki a függő erőforrásokat > **függőségek hozzáadása** elemet. Figyelje az értesítések állapotát.

4. Szükség esetén további függőségeket vehet fel, és újból érvényesítheti a függőségeket. 

5. Az **egyes régiók** oldalon ellenőrizze, hogy az erőforrások mostantól függőben vannak-e *előkészítésre váró* állapotban, problémák nélkül.

    ![A függőben lévő előkészítési állapotú erőforrásokat megjelenítő oldal](./media/tutorial-move-region-sql/prepare-pending.png)



## <a name="move-the-sql-server"></a>A SQL Server áthelyezése

Rendeljen hozzá egy cél SQL Server a célként megadott régióban, és véglegesítse az áthelyezést.

### <a name="assign-a-target-sql-server"></a>Cél SQL Serverának kiosztása

1. Az **egyes régiókban** a SQL Server erőforráshoz a **cél konfigurációja** oszlopban kattintson az **erőforrás nincs hozzárendelve** elemre.
2. Válasszon ki egy meglévő SQL Server erőforrást a célként megadott régióban. 
    
    ![Bejegyzés, amely azt mutatja, SQL Server a végrehajtásra való áthelyezésre beállított állapot](./media/tutorial-move-region-sql/sql-server-commit-move-pending.png) 

    
> [!NOTE]
> A forrás SQL Server állapotának változása *folyamatban* van. 

### <a name="commit-the-sql-server-move"></a>A SQL Server áthelyezésének véglegesíte

1. A **régiók között** válassza ki a SQL Servert, majd kattintson az **Áthelyezés elkövetése** elemre.
2. Az **erőforrások elkövetése** területen kattintson a **véglegesítés** elemre.

    ![Az SQL Server áthelyezéséhez használandó oldal](./media/tutorial-move-region-sql/commit-sql-server.png)

3. Nyomon követheti a folyamat lépéseit az értesítések sávján.

> [!NOTE]
> A véglegesítés után a SQL Server már folyamatban van egy *törlési forrás függőben* állapotban.


## <a name="prepare-resources-to-move"></a>Az áthelyezni kívánt erőforrások előkészítése

Ha a forrás SQL Server áthelyezte, előkészítheti a többi erőforrás áthelyezését.

## <a name="prepare-an-elastic-pool"></a>Rugalmas készlet előkészítése

1. A **régiók között** válassza ki a forrás rugalmas készletet (bemutató test1-elasticpool), majd kattintson az **előkészítés** gombra.

    ![Erőforrások előkészítésére szolgáló gomb](./media/tutorial-move-region-sql/prepare-elastic.png)

2. Az **erőforrások előkészítése** területen kattintson az **előkészítés** gombra.
3. Ha az értesítések azt mutatják, hogy az előkészítési folyamat sikeres volt, kattintson a **frissítés** gombra.

> [!NOTE]
> A rugalmas készlet most már folyamatban van az *Indítás függőben állapotba helyezése* .

## <a name="prepare-a-single-database"></a>Önálló adatbázis előkészítése

1. Az egyes **régiókban** válassza ki az önálló adatbázist (nem rugalmas készletben), majd kattintson az **előkészítés** gombra.

    ![A kiválasztott erőforrások előkészítésének gombja](./media/tutorial-move-region-sql/prepare-db.png)

2. Az **erőforrások előkészítése** területen kattintson az **előkészítés** gombra.
3. Ha az értesítések azt mutatják, hogy az előkészítési folyamat sikeres volt, kattintson a **frissítés** gombra.

> [!NOTE]
> Az adatbázis most már folyamatban van egy *függőben* állapotba állítása, és a megcélzott régióban lett létrehozva.


## <a name="move-the-pool-and-prepare-pool-databases"></a>A készlet áthelyezése és a készlet-adatbázisok előkészítése

Az adatbázisok rugalmas készletben való előkészítéséhez a rugalmas készletnek a *függőben lévő áthelyezési* állapotba kell esnie. Az állapotba való áthelyezéshez indítsa el a készlet áthelyezését.

#### <a name="initiate-move---elastic-pool"></a>Mozgás rugalmas készletének kezdeményezése

1. Az **egyes régiókban** válassza ki a forrás rugalmas készletet (bemutató-test1-elasticpool), majd kattintson az **Áthelyezés kezdeményezése** lehetőségre.
2. Az **erőforrások áthelyezése** területen kattintson az **Áthelyezés kezdeményezése** lehetőségre.

    
    ![A rugalmas készlet áthelyezésének kezdeményezésére szolgáló gomb](./media/tutorial-move-region-sql/initiate-elastic.png)

1. Nyomon követheti a folyamat lépéseit az értesítések sávján.
1. Ha az értesítések azt mutatják, hogy az áthelyezés sikeres volt, kattintson a **frissítés** gombra.

> [!NOTE]
> A rugalmas készlet most már folyamatban van egy véglegesített *áthelyezési* állapot.

#### <a name="prepare-database"></a>Adatbázis előkészítése

1. Az **egyes régiókban** válassza ki az adatbázist (bemutató-teszt2-sqldb az útmutatóban), majd kattintson az **előkészítés** gombra.
2. Az **erőforrások előkészítése** területen kattintson az **előkészítés** gombra.

    ![Az adatbázis rugalmas készletben való előkészítésének gombja](./media/tutorial-move-region-sql/prepare-database-elastic.png) 

Az előkészítés során a céladatbázis a célként megadott régióban jön létre, és elindul az adatreplikálás. Az előkészítést követően az adatbázis *kezdeményezett áthelyezés függőben* állapotba kerül. 

![A kiválasztott adatbázis rugalmas készletbe való előkészítésének gombja](./media/tutorial-move-region-sql/initiate-move-pending.png) 

## <a name="move-databases"></a>Adatbázisok áthelyezése

Az adatbázisok áthelyezésének megkezdése.
1. Az **egyes régiókban** válassza az állapot **elindítása függőben** állapotú erőforrások lehetőséget. Ezután kattintson az **Áthelyezés kezdeményezése** lehetőségre.
2. Az **erőforrások áthelyezése** területen kattintson az **Áthelyezés kezdeményezése** lehetőségre.

    ![Áthelyezést kezdeményező oldal](./media/tutorial-move-region-sql/initiate-move.png)

3. Nyomon követheti a folyamat lépéseit az értesítések sávján.

> [!NOTE]
> Az adatbázisok most már folyamatban van egy véglegesített *áthelyezési* állapot.


## <a name="discard-or-commit"></a>Elveti vagy véglegesíti?

A kezdeti áthelyezés után eldöntheti, hogy véglegesíteni kívánja-e az áthelyezést, vagy elveti azt. 

- **Elvetés**: lehet, hogy el szeretné vetni az áthelyezést, ha teszteli, és nem szeretné ténylegesen áthelyezni a forrás erőforrást. Az áthelyezés elvetése visszaadja az erőforrást a **függőben lévő áthelyezés indításának** állapotára.
- **Véglegesítés**: a véglegesítés befejezi az áthelyezést a célként megadott régióba. A véglegesítést követően a forrás erőforrás a **delete Source függőben** állapotba kerül, és eldöntheti, hogy szeretné-e törölni.


## <a name="discard-the-move"></a>Áthelyezés elvetése 

Az áthelyezés a következőképpen törölhető:

1. Az **egyes régiókban** válassza az állapot- **végrehajtási áthelyezés függőben** lévő erőforrások lehetőséget, majd kattintson az **Áthelyezés elvetése** lehetőségre.
2. Az **Áthelyezés elvetése** területen kattintson az **Elvetés** gombra.
3. Nyomon követheti a folyamat lépéseit az értesítések sávján.


> [!NOTE]
> - Az erőforrások elvetése után a rendszer *kezdeményezi az áthelyezés függőben állapotba helyezését* .
> - Ha csak egy rugalmas készlet van, elveti a folyamatokat, és törli a megcélzott régióban létrehozott rugalmas készletet.
> - Ha van olyan rugalmas készlet, amelyhez társított adatbázisok tartoznak a végrehajtás *függőben állapotba lépéséhez* , a rugalmas készlet nem törölhető.
> - Ha elvet egy SQL-adatbázist, a rendszer nem törli a célként megadott régió erőforrásait. 

Ha az Elvetés után ismét el szeretné indítani az áthelyezést, válassza ki az SQL-adatbázist vagy a rugalmas készletet, majd indítsa el újra az áthelyezést.


## <a name="commit-the-move"></a>Az áthelyezés elkövetése

A mozgó adatbázisok és rugalmas készletek befejezése a következőképpen történik:


1. Győződjön meg arról, hogy a SQL Server a *forrás törlés függőben* állapotú.
2. A véglegesítés előtt frissítse az adatbázis-kapcsolódási karakterláncokat a célként megadott régióba.
3. Az **egyes régiókban** válassza ki az SQL-erőforrásokat, majd kattintson az **Áthelyezés elkövetése** elemre.
4. Az **erőforrások elkövetése** területen kattintson a **véglegesítés** elemre.

    ![Áthelyezés elkövetése](./media/tutorial-move-region-sql/commit-sql-resources.png)

5. Nyomon követheti a végrehajtás előrehaladását az értesítések sávján.


> [!NOTE]
> Az SQL-adatbázisok esetében bizonyos állásidő fordul elő a végrehajtás során.
> A véglegesített adatbázisok és a rugalmas készletek mostantól *függőben lévő törlési* állapotban vannak.
> A véglegesítés után frissítse az adatbázissal kapcsolatos beállításokat, beleértve a tűzfalszabályok, a házirendek és a riasztások beállításait a céladatbázis számára.


## <a name="delete-source-resources-after-commit"></a>Forrás erőforrásainak törlése a véglegesítés után

Az áthelyezést követően lehetőség van a forrás régió erőforrásainak törlésére is. 

> [!NOTE]
> SQL Server kiszolgálókat nem lehet törölni a portálról, és az erőforrás-tulajdonságok lapról kell törölni.

1. A **különböző régiókban** kattintson a törölni kívánt forrás-erőforrás nevére.
2. Válassza a **forrás törlése** lehetőséget.

## <a name="next-steps"></a>További lépések

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Áthelyezte az Azure SQL Database-adatbázisokat egy másik Azure-régióba.
> * Áthelyezte az Azure SQL rugalmas készleteit egy másik régióba.

Most próbálkozzon Azure-beli virtuális gépek másik régióba való áthelyezésével.

> [!div class="nextstepaction"]
> [Azure-beli virtuális gépek áthelyezése](./tutorial-move-region-virtual-machines.md)