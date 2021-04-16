---
title: 'Rövid útmutató: Dedikált SQL-készlet (korábban SQL DW) létrehozása és lekérdezése (Azure Portal)'
description: Dedikált SQL-készlet (korábban SQL DW) létrehozása és lekérdezése a Azure Portal
services: synapse-analytics
author: pimorano
ms.author: pimorano
manager: craigg
ms.reviewer: igorstan
ms.date: 05/28/2019
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- seo-lt-2019
- azure-synapse
- mode-portal
ms.openlocfilehash: 99530b1949f03867a5b755208191341556ba8083
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535831"
---
# <a name="quickstart-create-and-query-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-using-the-azure-portal"></a>Rövid útmutató: Dedikált SQL-készlet (korábbi nevén SQL DW) létrehozása és lekérdezése az Azure Synapse Analyticsben az Azure Portal

Gyorsan létrehozhat és lekérdezhet egy dedikált SQL-készletet (korábban SQL DW)a Azure Synapse Analytics a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

1. Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

   > [!NOTE]
   > Ha dedikált SQL-készletet (korábban SQL DW-t) hoz létre a Azure Synapse új számlázható szolgáltatást eredményezhet. További információkért lásd: [Azure Synapse Analytics díjszabása.](https://azure.microsoft.com/pricing/details/synapse-analytics/)

2. Töltse le és telepítse az [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SSMS) legújabb verzióját. Megjegyzés: Az SSMS csak Windows-alapú platformokon érhető el. Lásd a támogatott platformok [teljes listáját.](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15&preserve-view=true#supported-operating-systems-ssms-185t)

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-sql-pool"></a>SQL-készlet létrehozása

Az adattárház létrehozása dedikált SQL-készlet (korábban SQL DW) használatával történik a Azure Synapse Analytics. A dedikált SQL-készlet (korábbi nevén SQL DW) számítási erőforrások egy meghatározott [készletével együtt jön létre.](memory-concurrency-limits.md) Az adatbázis egy [](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Azure-erőforráscsoportban és egy logikai [SQL-kiszolgálón jön létre.](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

Kövesse az alábbi lépéseket egy dedikált SQL-készlet (korábban SQL DW) létrehozásához, amely az **AdventureWorksDW mintaadatokat** tartalmazza.

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.

   ![erőforrás létrehozása a Azure Portal](./media/create-data-warehouse-portal/create-a-resource.png)

2. A keresősávba írja be a "dedicated SQL pool" (dedikált SQL-készlet) keresősávba a dedikált SQL-készlet (korábban SQL DW) lehetőséget. A **megnyíló** oldalon válassza a Létrehozás lehetőséget.

   ![üres adattárház létrehozása](./media/create-data-warehouse-portal/create-a-data-warehouse.png)

3. Az **Alapvető szolgáltatások alatt** adja meg az előfizetését, az erőforráscsoportot, a dedikált SQL-készlet (korábban SQL DW) nevét és a kiszolgáló nevét:

   | Beállítás | Ajánlott érték | Leírás |
   | :------ | :-------------- | :---------- |
   | **Előfizetés** | Az Ön előfizetése | Az előfizetései részleteivel kapcsolatban lásd az [előfizetéseket](https://account.windowsazure.com/Subscriptions) ismertető cikket. |
   | **Erőforráscsoport** | myResourceGroup | Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) ismertető cikket. |
   | **SQL-készlet neve** | Bármely globálisan egyedi név *(például: mySampleDataWarehouse*) | Az érvényes adatbázisnevekkel kapcsolatban lásd az [adatbázis-azonosítókat](/sql/relational-databases/databases/database-identifiers?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) ismertető cikket.  |
   | **Kiszolgáló** | Bármely globálisan egyedi név | Válasszon ki egy meglévő kiszolgálót, vagy hozzon létre egy új kiszolgálónevet, és válassza az **Új létrehozása lehetőséget.** Az érvényes kiszolgálónevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) ismertető cikket. |

   ![adattárház létrehozása – alapvető részletek](./media/create-data-warehouse-portal/create-sql-pool-basics.png)

4. A **Teljesítményszint alatt** válassza **a Teljesítményszint kiválasztása** lehetőséget, ha egy csúszkával is módosítania kell a konfigurációt.

   ![adattárház teljesítményszintjének módosítása](./media/create-data-warehouse-portal/create-sql-pool-performance-level.png)  

   A teljesítményszintekkel kapcsolatos további információkért lásd: [Számítás kezelése a Azure Synapse Analytics.](sql-data-warehouse-manage-compute-overview.md)

5. Válassza **az Additional Settings (További beállítások)** lehetőséget a Use existing data (Meglévő adatok **használata)** alatt válassza a **Sample** (Minta) lehetőséget, hogy az AdventureWorksDW mintaadatbázisként legyen létrehozva.

    ![válassza a Meglévő adatok használata lehetőséget](./media/create-data-warehouse-portal/create-sql-pool-additional-1.png)

6. Most, hogy befejezte az Alapszintű beállítások lapot a Azure Synapse Analytics válassza a  Felülvizsgálat **+** létrehozás, majd a Létrehozás lehetőséget az SQL-készlet létrehozásához. A kiépítés eltarthat néhány percig.

   ![válassza az Áttekintés + létrehozás lehetőséget](./media/create-data-warehouse-portal/create-sql-pool-review-create.png)

   ![létrehozás kiválasztása](./media/create-data-warehouse-portal/create-sql-pool-create.png)

7. Az eszköztáron válassza az **Értesítések** elemet az üzembehelyezési folyamat monitorozásához.

   ![Képernyőkép az értesítésekről, folyamatban lévő üzembe helyezéssel.](./media/create-data-warehouse-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály létrehozása

A Azure Synapse szolgáltatás létrehoz egy tűzfalat a kiszolgáló szintjén. Ez a tűzfal megakadályozza, hogy külső alkalmazások és eszközök csatlakozzon a kiszolgálóhoz vagy a kiszolgálón található adatbázisokhoz. A csatlakozás engedélyezéséhez hozzáadhat tűzfalszabályokat, amelyek adott IP-címekkel engedélyezik a kapcsolódást. A következő lépéseket követve hozzon létre egy [kiszolgálószintű tűzfalszabályt](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) az ügyfél IP-címéhez.

> [!NOTE]
> Azure Synapse 1433-as porton keresztül kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 1433-as porton keresztül. Ha igen, csak akkor tud csatlakozni a kiszolgálóhoz, ha az it-részleg megnyitja az 1433-as portot.

1. Az üzembe helyezés befejezése  után a bal oldali menüben válassza a Minden szolgáltatás lehetőséget. Válassza **az Adatbázisok** lehetőséget, válassza a csillagot a Azure Synapse Analytics, hogy Azure Synapse Analytics kedvencei közé. 

2. Válassza **Azure Synapse Analytics** a bal oldali menüben, majd válassza ki a **mySampleDataWarehouse** elemet **a Azure Synapse Analytics** oldalon. Megnyílik az adatbázis áttekintő oldala, amely megjeleníti a teljes kiszolgálónevet (például **sqlpoolservername.database.windows.net),** és további konfigurációs lehetőségeket biztosít.

3. Másolja ki ezt a teljes kiszolgálónevet, hogy a kiszolgálóhoz és annak adatbázisához csatlakozzon ebben a rövid útmutatóban és egyéb rövid útmutatóban. A kiszolgáló beállításainak megnyitásához válassza ki a kiszolgáló nevét.

   ![kiszolgálónév keresése](./media/create-data-warehouse-portal/find-server-name.png)

4. Válassza a **Tűzfalbeállítások megjelenítése** lehetőséget.

   ![kiszolgáló beállításai](./media/create-data-warehouse-portal/server-settings.png)

5. Megnyílik **a** kiszolgáló Tűzfalbeállítások lapja.

   ![kiszolgálói tűzfalszabály](./media/create-data-warehouse-portal/server-firewall-rule.png)

6. Az aktuális IP-cím új tűzfalszabályhoz való hozzáadásához válassza az **Ügyfél IP-címének** hozzáadása lehetőséget az eszköztáron. A tűzfalszabály az 1433-as portot egy egyedi IP-cím vagy egy IP-címtartomány számára nyithatja meg.

7. válassza a **Mentés lehetőséget.** Létrejön egy kiszolgálószintű tűzfalszabály az aktuális IP-címhez, amely megnyitja az 1433-as portot a kiszolgálón.

8. kattintson **az OK** gombra, majd zárja be a **Tűzfalbeállítások** lapot.

Most már csatlakozhat a kiszolgálóhoz és annak SQL-készletéhez ezzel az IP-címmel. A csatlakozás az SQL Server Management Studio vagy más, választott eszköz használatával lehetséges. A csatlakozáskor használja a korábban létrehozott ServerAdmin-fiókot.

> [!IMPORTANT]
> Alapértelmezés szerint az összes Azure-szolgáltatás számára engedélyezett a hozzáférés az SQL Database tűzfalán keresztül. Válassza **a KI lehetőséget** ezen az oldalon, majd válassza a Mentés **lehetőséget** a tűzfal letiltásához az összes Azure-szolgáltatáshoz.

## <a name="get-the-fully-qualified-server-name"></a>A teljes kiszolgálónév lekérése

Szerezze be a kiszolgáló teljes kiszolgálónevét a Azure Portal. Később ezt a teljes nevet fogja majd használni a kiszolgálóhoz való kapcsolódás során.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza **Azure Synapse Analytics** a bal oldali menüBen, majd válassza ki a elemet **a Azure Synapse Analytics** oldalon.

3. Az Azure Portalon az adatbázishoz tartozó lap **Alapvető erőforrások** ablaktábláján keresse meg, majd másolja ki a **Kiszolgáló nevét**. Ebben a példában a teljes név sqlpoolservername.database.windows.net.

    ![kapcsolatadatok](./media/create-data-warehouse-portal/find-server-name.png)

## <a name="connect-to-the-server-as-server-admin"></a>Csatlakozás a kiszolgálóhoz kiszolgáló-rendszergazdaként

Ez a szakasz [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SSMS) segítségével hoz létre kapcsolatot a kiszolgálóval.

1. Nyissa meg az SQL Server Management Studiót.

2. A **Connect to Server** (Kapcsolódás a kiszolgálóhoz) párbeszédpanelen adja meg a következő adatokat:

   | Beállítás | Ajánlott érték | Leírás |
   | :------ | :-------------- | :---------- |
   | Server type (Kiszolgáló típusa) | Adatbázismotor | Kötelezően megadandó érték |
   | Kiszolgálónév | A teljes kiszolgálónév | Példa: **sqlpoolservername.database.windows.net.** |
   | Hitelesítés | SQL Server-hitelesítés | Ebben az oktatóanyagban az SQL-hitelesítésen kívül más hitelesítéstípus nincs konfigurálva. |
   | Bejelentkezés | A kiszolgálói rendszergazdafiók | A kiszolgáló létrehozásakor megadott fiók. |
   | Jelszó | A kiszolgálói rendszergazdai fiók jelszava | A kiszolgáló létrehozásakor megadott jelszó. |
   ||||

   ![kapcsolódás a kiszolgálóhoz](./media/create-data-warehouse-portal/connect-to-server-ssms.png)

3. válassza a **Csatlakozás lehetőséget.** Megnyílik az Object Explorer ablak az SSMS-ben.

4. Az Object Explorerben bontsa ki a **Databases** (Adatbázisok) elemet. Ezután bontsa ki a **mySampleDatabase** csomópontot az új adatbázisban található objektumok megtekintéséhez.

   ![adatbázis-objektumok](./media/create-data-warehouse-portal/connected-ssms.png)

## <a name="run-some-queries"></a>Néhány lekérdezés futtatása

A kiszolgálói rendszergazdaként naplózott nagy lekérdezések futtatása nem ajánlott, mert korlátozott [erőforrásosztályt használ.](resource-classes-for-workload-management.md) Ehelyett konfigurálja [a számítási feladatok](./quickstart-configure-workload-isolation-tsql.md) elkülönítését az [oktatóanyagokban látható módon.](./load-data-wideworldimportersdw.md#create-a-user-for-loading-data)

Azure Synapse Analytics T-SQL-t használ lekérdezési nyelvként. A lekérdezésablakok megnyitásához és a T-SQL-lekérdezések futtatásához végezze el az alábbi lépéseket:

1. Válassza a jobb gombot **a mySampleDataWarehouse, majd** az **Új lekérdezés lehetőséget.** Megnyílik egy új lekérdezési ablak.

2. A lekérdezésablakban írja be a következő parancsot az adatbázisok listájának megjelenítéséhez.

    ```sql
    SELECT * FROM sys.databases
    ```

3. válassza a **Végrehajtás lehetőséget.** A lekérdezés eredménye két adatbázist mutat: a **master** és a **mySampleDataWarehouse** adatbázist.

   ![Adatbázisok lekérdezése](./media/create-data-warehouse-portal/query-databases.png)

4. Hogy lássunk néhány adatot is, a következő parancs használatával tekintsük meg, hány Adams vezetéknevű, három gyermeket nevelő ügyfél létezik. A lekérdezés hat ügyfelet listáz ki.

    ```sql
    SELECT LastName, FirstName FROM dbo.dimCustomer
    WHERE LastName = 'Adams' AND NumberChildrenAtHome = 3;
    ```

   ![A dbo.dimCustomer lekérdezés](./media/create-data-warehouse-portal/query-customer.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Díjat számítunk fel az adattárházegységekért és a dedikált SQL-készletben (korábban SQL DW) tárolt adatokért. Ezek a számítási és tárolási erőforrások elkülönítve lesznek kiszámlázva.

- Ha az adatokat a tárolóban szeretné tartani, szüneteltetheti a számítást, ha nem dedikált SQL-készletet (korábban SQL DW-t) használ. A számítási erőforrások szüneteltetésért csak az adattárolásért kell fizetnie. Ha készen áll az adatokkal való munkára, folytathatja a számítást.

- Ha el szeretné távolítani a jövőbeli díjakat, törölheti a dedikált SQL-készletet (korábban SQL DW).

Kövesse az alábbi lépéseket a már nem szükséges erőforrások tisztítására.

1. Jelentkezzen be a [Azure Portal,](https://portal.azure.com)válassza ki a dedikált SQL-készletet (korábban SQL DW).

   ![Az erőforrások eltávolítása](./media/create-data-warehouse-portal/clean-up-resources.png)

2. A számítás szüneteltetéshez válassza a **Szüneteltetés** gombot. Ha a dedikált SQL-készlet (korábbi nevén SQL DW) szüneteltetve van, egy **Folytatás gomb látható.** A számítás folytatásához válassza a **Folytatás lehetőséget.**

3. Ha el szeretné távolítani a dedikált SQL-készletet (korábban SQL DW), hogy ne számítson fel díjat a számítási vagy tárolási erőforrásokért, válassza a **Törlés lehetőséget.**

4. A létrehozott kiszolgáló eltávolításához válassza az előző **sqlpoolservername.database.windows.net,** majd a Törlés **lehetőséget.** A törléssel bánjon óvatosan, mivel a kiszolgálóval együtt a hozzá rendelt összes adatbázis is törölve lesz.

5. Az erőforráscsoport eltávolításához válassza ki a **myResourceGroup erőforráscsoportot,** majd válassza az **Erőforráscsoport törlése lehetőséget.**

Optimalizálni szeretné a felhővel töltött kiadásait, és pénzt takaríthat meg?

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az adatok a dedikált SQL-készletbe (korábban SQL DW) való betöltéséről, folytassa az Adatok betöltése dedikált [SQL-készletbe című cikkel.](load-data-from-azure-blob-storage-using-copy.md)
