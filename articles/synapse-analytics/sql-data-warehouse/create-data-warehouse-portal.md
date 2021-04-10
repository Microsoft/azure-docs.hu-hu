---
title: 'Rövid útmutató: dedikált SQL-készlet létrehozása és lekérdezése (korábbi nevén SQL DW) (Azure Portal)'
description: Dedikált SQL-készlet (korábban SQL DW) létrehozása és lekérdezése a Azure Portal használatával
services: synapse-analytics
author: pimorano
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 05/28/2019
ms.author: pimorano
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: dd9d66683d5f2349807915651c50bf9eedd285c1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104602230"
---
# <a name="quickstart-create-and-query-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-using-the-azure-portal"></a>Rövid útmutató: dedikált SQL-készlet (korábban SQL DW) létrehozása és lekérdezése az Azure szinapszis Analyticsben az Azure Portal használatával

A Azure Portal használatával gyorsan létrehozhat és lekérdezheti a dedikált SQL-készletet (korábban SQL DW) az Azure szinapszis Analyticsben.

## <a name="prerequisites"></a>Előfeltételek

1. Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

   > [!NOTE]
   > Egy dedikált SQL-készlet (korábban SQL DW) létrehozása az Azure Szinapszisban egy új számlázható szolgáltatás lehet. További információ: az [Azure szinapszis Analytics díjszabása](https://azure.microsoft.com/pricing/details/synapse-analytics/).

2. Töltse le és telepítse az [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SSMS) legújabb verzióját. Megjegyzés: a SSMS csak Windows-alapú platformokon érhető el, lásd a [támogatott platformok teljes listáját](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15&preserve-view=true#supported-operating-systems-ssms-185t).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-sql-pool"></a>SQL-készlet létrehozása

Az adattárházak a dedikált SQL Pool (korábban SQL DW) használatával jönnek létre az Azure szinapszis Analyticsben. A dedikált SQL-készlet (korábban SQL DW) [számítási erőforrások](memory-concurrency-limits.md)meghatározott készletével jön létre. Az adatbázis egy [Azure-erőforráscsoport](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) és egy [logikai SQL Server-kiszolgáló](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)között jön létre.

Kövesse az alábbi lépéseket egy dedikált SQL-készlet (korábban SQL DW) létrehozásához, amely tartalmazza a **AdventureWorksDW** .

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.

   ![erőforrás létrehozása Azure Portal](./media/create-data-warehouse-portal/create-a-resource.png)

2. A keresősáv "dedikált SQL-készlet" mezőjében válassza a dedikált SQL-készlet (korábban SQL DW) elemet. A megnyíló lapon válassza a **Létrehozás** lehetőséget.

   ![üres adattárház létrehozása](./media/create-data-warehouse-portal/create-a-data-warehouse.png)

3. Az **alapismeretek** területen adja meg az előfizetését, az erőforráscsoportot, a dedikált SQL-készletet (korábbi NEVÉN SQL DW) és a kiszolgáló nevét:

   | Beállítás | Ajánlott érték | Leírás |
   | :------ | :-------------- | :---------- |
   | **Előfizetés** | Az Ön előfizetése | Az előfizetései részleteivel kapcsolatban lásd az [előfizetéseket](https://account.windowsazure.com/Subscriptions) ismertető cikket. |
   | **Erőforráscsoport** | myResourceGroup | Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) ismertető cikket. |
   | **SQL-készlet neve** | Bármely globálisan egyedi név (például *mySampleDataWarehouse*) | Az érvényes adatbázisnevekkel kapcsolatban lásd az [adatbázis-azonosítókat](/sql/relational-databases/databases/database-identifiers?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) ismertető cikket.  |
   | **Kiszolgáló** | Bármely globálisan egyedi név | Válasszon ki egy meglévő kiszolgálót, vagy hozzon létre egy új kiszolgálónevet, és válassza az **új létrehozása** lehetőséget. Az érvényes kiszolgálónevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) ismertető cikket. |

   ![adatraktár alapszintű adatainak létrehozása](./media/create-data-warehouse-portal/create-sql-pool-basics.png)

4. A **teljesítmény szintje** területen válassza a **teljesítmény kiválasztása** lehetőséget, ha szeretné, hogy a konfigurációt egy csúszka használatával módosítsa.

   ![az adatraktár teljesítményi szintjének módosítása](./media/create-data-warehouse-portal/create-sql-pool-performance-level.png)  

   A teljesítményszint részletes ismertetését lásd: [a számítások kezelése az Azure szinapszis Analyticsben](sql-data-warehouse-manage-compute-overview.md).

5. Válassza a **További beállítások** lehetőséget, majd a **meglévő adat használata** területen válassza a **minta** lehetőséget, hogy a rendszer létrehozza a AdventureWorksDW.

    ![meglévő adathasználat kiválasztása](./media/create-data-warehouse-portal/create-sql-pool-additional-1.png)

6. Most, hogy végrehajtotta az Azure szinapszis Analytics űrlapjának alapismeretek lapját, válassza a **felülvizsgálat + létrehozás** , majd a **Létrehozás** lehetőséget az SQL-készlet létrehozásához. A kiépítés eltarthat néhány percig.

   ![Válassza a felülvizsgálat + létrehozás lehetőséget](./media/create-data-warehouse-portal/create-sql-pool-review-create.png)

   ![létrehozás kiválasztása](./media/create-data-warehouse-portal/create-sql-pool-create.png)

7. Az eszköztáron válassza az **Értesítések** elemet az üzembehelyezési folyamat monitorozásához.

   ![A képernyőképen a telepítés folyamatban állapotú értesítések láthatók.](./media/create-data-warehouse-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály létrehozása

Az Azure szinapszis szolgáltatás egy tűzfalat hoz létre a kiszolgáló szintjén. Ez a tűzfal megakadályozza, hogy a külső alkalmazások és eszközök csatlakozzanak a kiszolgálóhoz vagy a kiszolgálón lévő adatbázisokhoz. A csatlakozás engedélyezéséhez hozzáadhat tűzfalszabályokat, amelyek adott IP-címekkel engedélyezik a kapcsolódást. A következő lépéseket követve hozzon létre egy [kiszolgálószintű tűzfalszabályt](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) az ügyfél IP-címéhez.

> [!NOTE]
> Az Azure szinapszis a 1433-as porton keresztül kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 1433-as porton keresztül. Ha igen, nem tud csatlakozni a kiszolgálóhoz, kivéve, ha az informatikai részleg megnyitja a 1433-es portot.

1. Az üzembe helyezés befejezése után válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben. Válassza az **adatbázisok** lehetőséget, válassza ki az **Azure szinapszis Analytics** melletti csillagot az Azure szinapszis Analytics kedvencekhez való hozzáadásához.

2. Válassza ki az **Azure szinapszis Analytics** elemet a bal oldali menüben, majd válassza a **MySampleDataWarehouse** lehetőséget az **Azure szinapszis Analytics** oldalán. Megnyílik az adatbázis áttekintő lapja, amely megjeleníti a teljes kiszolgálónevet (például **sqlpoolservername.database.Windows.net**), és további konfigurálási lehetőségeket biztosít.

3. Másolja ezt a teljes kiszolgálónevet a kiszolgálóhoz és az adatbázisaihoz való kapcsolódáshoz, és az egyéb gyors indításokat. A kiszolgáló beállításainak megnyitásához válassza ki a kiszolgáló nevét.

   ![kiszolgálónév keresése](./media/create-data-warehouse-portal/find-server-name.png)

4. Válassza a **Tűzfalbeállítások megjelenítése** lehetőséget.

   ![kiszolgáló beállításai](./media/create-data-warehouse-portal/server-settings.png)

5. Ekkor megnyílik a kiszolgáló **tűzfalbeállítások** lapja.

   ![kiszolgálói tűzfalszabály](./media/create-data-warehouse-portal/server-firewall-rule.png)

6. Az aktuális IP-cím új tűzfalszabály hozzáadásához válassza az **ügyfél IP-** címének hozzáadása lehetőséget az eszköztáron. A tűzfalszabály az 1433-as portot egy egyedi IP-cím vagy egy IP-címtartomány számára nyithatja meg.

7. Válassza a **Mentés** lehetőséget. A rendszer létrehoz egy kiszolgálói szintű tűzfalszabály-szabályt az aktuális IP-címhez, amely megnyitja az 1433-es portot a kiszolgálón.

8. kattintson **az OK gombra** , majd a **tűzfalbeállítások** oldal bezárásához.

Most már csatlakozhat a kiszolgálóhoz és az SQL-készletekhez ezzel az IP-címmel. A csatlakozás az SQL Server Management Studio vagy más, választott eszköz használatával lehetséges. A csatlakozáskor használja a korábban létrehozott ServerAdmin-fiókot.

> [!IMPORTANT]
> Alapértelmezés szerint az összes Azure-szolgáltatás számára engedélyezett a hozzáférés az SQL Database tűzfalán keresztül. válassza **ki** ezt a lapot, majd a **Mentés** gombra kattintva tiltsa le a tűzfalat az összes Azure-szolgáltatáshoz.

## <a name="get-the-fully-qualified-server-name"></a>A teljes kiszolgálónév lekérése

Szerezze be a kiszolgáló teljes nevét a Azure Portalban. Később ezt a teljes nevet fogja majd használni a kiszolgálóhoz való kapcsolódás során.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza ki az **Azure szinapszis Analytics** elemet a bal oldali menüben, és válassza ki az **Azure szinapszis Analytics** -oldalát.

3. Az Azure Portalon az adatbázishoz tartozó lap **Alapvető erőforrások** ablaktábláján keresse meg, majd másolja ki a **Kiszolgáló nevét**. Ebben a példában a teljes név sqlpoolservername.database.windows.net.

    ![kapcsolatadatok](./media/create-data-warehouse-portal/find-server-name.png)

## <a name="connect-to-the-server-as-server-admin"></a>Csatlakozás a kiszolgálóhoz kiszolgáló-rendszergazdaként

Ez a szakasz [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SSMS) használatával létesít kapcsolatot a kiszolgálóval.

1. Nyissa meg az SQL Server Management Studiót.

2. A **Connect to Server** (Kapcsolódás a kiszolgálóhoz) párbeszédpanelen adja meg a következő adatokat:

   | Beállítás | Ajánlott érték | Leírás |
   | :------ | :-------------- | :---------- |
   | Server type (Kiszolgáló típusa) | Adatbázismotor | Kötelezően megadandó érték |
   | Kiszolgálónév | A teljes kiszolgálónév | Íme egy példa: **sqlpoolservername.database.Windows.net**. |
   | Hitelesítés | SQL Server-hitelesítés | Ebben az oktatóanyagban az SQL-hitelesítésen kívül más hitelesítéstípus nincs konfigurálva. |
   | Bejelentkezés | A kiszolgálói rendszergazdafiók | A kiszolgáló létrehozásakor megadott fiók. |
   | Jelszó | A kiszolgálói rendszergazdai fiók jelszava | A kiszolgáló létrehozásakor megadott jelszó. |
   ||||

   ![kapcsolódás a kiszolgálóhoz](./media/create-data-warehouse-portal/connect-to-server-ssms.png)

3. Válassza a **kapcsolat** lehetőséget. Megnyílik az Object Explorer ablak az SSMS-ben.

4. Az Object Explorerben bontsa ki a **Databases** (Adatbázisok) elemet. Ezután bontsa ki a **mySampleDatabase** csomópontot az új adatbázisban található objektumok megtekintéséhez.

   ![adatbázis-objektumok](./media/create-data-warehouse-portal/connected-ssms.png)

## <a name="run-some-queries"></a>Néhány lekérdezés futtatása

Nem ajánlott nagyméretű lekérdezéseket futtatni a kiszolgálói rendszergazdaként való naplózás során, mivel ez egy [korlátozott erőforrás-osztályt](resource-classes-for-workload-management.md)használ. Ehelyett konfigurálja a [munkaterhelés elkülönítését](./quickstart-configure-workload-isolation-tsql.md) [az oktatóanyagokban bemutatott](./load-data-wideworldimportersdw.md#create-a-user-for-loading-data)módon.

Az Azure szinapszis Analytics a T-SQL-t használja a lekérdezési nyelvként. A lekérdezésablakok megnyitásához és a T-SQL-lekérdezések futtatásához végezze el az alábbi lépéseket:

1. Kattintson a jobb gombbal a **mySampleDataWarehouse** elemre, és válassza az **Új lekérdezés** elemet. Megnyílik egy új lekérdezési ablak.

2. A lekérdezésablakban írja be a következő parancsot az adatbázisok listájának megjelenítéséhez.

    ```sql
    SELECT * FROM sys.databases
    ```

3. Válassza a **végrehajtás** lehetőséget. A lekérdezés eredménye két adatbázist mutat: a **master** és a **mySampleDataWarehouse** adatbázist.

   ![Adatbázisok lekérdezése](./media/create-data-warehouse-portal/query-databases.png)

4. Hogy lássunk néhány adatot is, a következő parancs használatával tekintsük meg, hány Adams vezetéknevű, három gyermeket nevelő ügyfél létezik. A lekérdezés hat ügyfelet listáz ki.

    ```sql
    SELECT LastName, FirstName FROM dbo.dimCustomer
    WHERE LastName = 'Adams' AND NumberChildrenAtHome = 3;
    ```

   ![A dbo.dimCustomer lekérdezés](./media/create-data-warehouse-portal/query-customer.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az adatraktár-egységek és a dedikált SQL-készlet (korábbi nevén SQL DW) tárolt adataiért kell fizetnie. Ezek a számítási és tárolási erőforrások elkülönítve lesznek kiszámlázva.

- Ha meg szeretné őrizni az adatok tárolását, szüneteltetheti a számítást, ha nem használja a dedikált SQL-készletet (korábbi nevén SQL DW). A számítás felfüggesztésével csak az adattárolás díját számítjuk fel. Bármikor folytathatja a számítást, amikor készen áll az adatokkal való együttműködésre.

- Ha el szeretné távolítani a jövőbeli díjakat, törölheti a dedikált SQL-készletet (korábbi nevén SQL DW).

Kövesse az alábbi lépéseket a már nem szükséges erőforrások törléséhez.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), válassza ki a dedikált SQL-készletét (korábban SQL DW).

   ![Az erőforrások eltávolítása](./media/create-data-warehouse-portal/clean-up-resources.png)

2. A számítás szüneteltetéséhez kattintson a **szüneteltetés** gombra. Ha a dedikált SQL-készlet (korábbi nevén SQL DW) szüneteltetve van, a **Folytatás** gomb jelenik meg. A számítás folytatásához válassza a **Folytatás** lehetőséget.

3. Ha el szeretné távolítani a dedikált SQL-készletet (korábban az SQL DW-t), ezért nem számít a számítás vagy a tárterület esetében, válassza a **Törlés** lehetőséget.

4. A létrehozott kiszolgáló eltávolításához válassza a **sqlpoolservername.database.Windows.net** lehetőséget az előző képen, majd válassza a **Törlés** lehetőséget. A törléssel bánjon óvatosan, mivel a kiszolgálóval együtt a hozzá rendelt összes adatbázis is törölve lesz.

5. Az erőforráscsoport eltávolításához válassza a **myResourceGroup** lehetőséget, majd válassza az **erőforráscsoport törlése** lehetőséget.

Szeretné optimalizálni és menteni a Felhőbeli kiadásait?

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni arról, hogyan tölthetők be az adatai a dedikált SQL-készletbe (korábban SQL DW), folytassa a [betöltést egy DEDIKÁLT SQL-készletbe](load-data-from-azure-blob-storage-using-copy.md) .