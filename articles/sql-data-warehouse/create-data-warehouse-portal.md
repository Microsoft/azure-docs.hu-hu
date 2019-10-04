---
title: 'Gyors útmutató: Azure SQL Data Warehouse létrehozása és lekérdezése – Azure Portal | Microsoft Docs'
description: Adattárház létrehozása és lekérdezése Azure SQL Data Warehouseekkel a Azure Portalban.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: development
ms.date: 05/28/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: sqlfreshmay19
ms.openlocfilehash: 83475af3cfdd83e718243d80b84599d53716a5d5
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2019
ms.locfileid: "70375839"
---
# <a name="quickstart-create-and-query-an-azure-sql-data-warehouse-in-the-azure-portal"></a>Gyors útmutató: Azure SQL Data Warehouse létrehozása és lekérdezése a Azure Portal

Azure SQL Data Warehouse gyors létrehozása és lekérdezése a Azure Portal használatával.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

> [!NOTE]
> A SQL Data Warehouse létrehozása egy új számlázható szolgáltatás létrejöttét eredményezheti. További információ: [SQL Data Warehouse díjszabása](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="before-you-begin"></a>Előkészületek

Töltse le és telepítse az [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) legújabb verzióját.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-data-warehouse"></a>Adattárház létrehozása

Egy Azure SQL Data Warehouse [számítási erőforrások](memory-and-concurrency-limits.md)meghatározott készletével jön létre. Az adatbázis egy [Azure-erőforráscsoporton](../azure-resource-manager/resource-group-overview.md) belül egy [Azure SQL logikai kiszolgálón](../sql-database/sql-database-logical-servers.md) jön létre. 

Kövesse az alábbi lépéseket egy olyan SQL Data Warehouse létrehozásához, amely tartalmazza a AdventureWorksDW. 

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.

2. Az **Új** oldalon válassza az **Adatbázisok** elemet, majd az **Új** oldal **Kiemelt** területén válassza az **SQL Data Warehouse** lehetőséget.

    ![üres adattárház létrehozása](media/load-data-from-azure-blob-storage-using-polybase/create-empty-data-warehouse.png)

3. Adja meg az alábbi adatokat a SQL Data Warehouse-űrlapon:

    | Beállítás | Ajánlott érték | Leírás |
    | :------ | :-------------- | :---------- |
    | **Adatbázis neve** | mySampleDataWarehouse | Az érvényes adatbázisnevekkel kapcsolatban lásd az [adatbázis-azonosítókat](/sql/relational-databases/databases/database-identifiers) ismertető cikket. Megjegyzés: Az adattárház az adatbázisok egy típusa.|
    | **Előfizetés** | Az Ön előfizetése | Az előfizetései részleteivel kapcsolatban lásd az [előfizetéseket](https://account.windowsazure.com/Subscriptions) ismertető cikket. |
    | **Erőforráscsoport** | myResourceGroup | Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket. |
    | **Forrás kiválasztása** | Sample | Megköveteli egy mintaadatbázis betöltését. Megjegyzés: Az adattárház az adatbázisok egy típusa. |
    | **Minta kiválasztása** | AdventureWorksDW | Megköveteli az AdventureWorksDW mintaadatbázis betöltését. |
    ||||

    ![adattárház létrehozása](media/create-data-warehouse-portal/select-sample.png)

4. Kattintson a **Kiszolgáló** lehetőségre új kiszolgáló létrehozásához és konfigurálásához az új adatbázis számára. Adja meg az alábbi adatokat az **Új kiszolgálóűrlapon**: 

    | Beállítás | Ajánlott érték | Leírás |
    | :------ | :-------------- | :---------- |
    | **Kiszolgálónév** | Bármely globálisan egyedi név | Az érvényes kiszolgálónevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket. |
    | **Kiszolgálói rendszergazdai bejelentkezés** | Bármely érvényes név | Az érvényes bejelentkezési nevekkel kapcsolatban lásd az [adatbázis-azonosítókat](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) ismertető cikket.|
    | **Jelszó** | Bármely érvényes jelszó | A jelszónak legalább nyolc karakter hosszúságúnak kell lennie, és tartalmaznia kell karaktereket a következő kategóriák közül legalább háromból: nagybetűs karakterek, kisbetűs karakterek, számjegyek és nem alfanumerikus karakterek. |
    | **Location** | Bármely érvényes hely | A régiókkal kapcsolatos információkért lásd [az Azure régióit](https://azure.microsoft.com/regions/) ismertető cikket. |
    ||||

    ![adatbázis-kiszolgáló létrehozása](media/load-data-from-azure-blob-storage-using-polybase/create-database-server.png)

5. Kattintson a **Kiválasztás** gombra.

6. Az adatraktár teljesítmény-konfigurációjának megadásához kattintson a **teljesítmény szint** elemre.

7. Ebben az oktatóanyagban válassza a **Gen2**lehetőséget. Alapértelmezés szerint a csúszka a **DW1000c**értékre van állítva. Csúsztassa fel és le, hogy kipróbálja a működését a gyakorlatban. 

    ![teljesítmény konfigurálása](media/load-data-from-azure-blob-storage-using-polybase/configure-performance.png)

8. Kattintson az **Alkalmaz** gombra.

9. Most, hogy végrehajtotta a SQL Data Warehouse űrlapot, kattintson a **Létrehozás** gombra az adatbázis kiépítéséhez. Az üzembe helyezés eltarthat néhány percig.

    ![kattintson a létrehozás parancsra](media/load-data-from-azure-blob-storage-using-polybase/click-create.png)

10. Az eszköztáron kattintson az **Értesítések** parancsra az üzembe helyezési folyamat megfigyeléséhez.
    
     ![értesítés](media/load-data-from-azure-blob-storage-using-polybase/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály létrehozása

A SQL Data Warehouse szolgáltatás egy tűzfalat hoz létre a kiszolgáló szintjén. Ez a tűzfal megakadályozza, hogy a külső alkalmazások és eszközök csatlakozzanak a kiszolgálóhoz vagy a kiszolgálón lévő adatbázisokhoz. A csatlakozás engedélyezéséhez hozzáadhat tűzfalszabályokat, amelyek adott IP-címekkel engedélyezik a kapcsolódást. A következő lépéseket követve hozzon létre egy [kiszolgálószintű tűzfalszabályt](../sql-database/sql-database-firewall-configure.md) az ügyfél IP-címéhez.

> [!NOTE]
> Az SQL Data Warehouse az 1433-as portot használja a kommunikációhoz. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 1433-as porton keresztül. Ebben az esetben nem tud csatlakozni az Azure SQL-adatbáziskiszolgálóhoz, ha az informatikai részleg nem nyitja meg az 1433-as portot.

1. Az üzembe helyezés befejezése után válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben. Válassza az **adatbázisok**lehetőséget, válassza ki az **SQL-adattárházak** melletti csillagot, és adja hozzá az SQL-adattárházak kedvencekhez szolgáltatását.
1. Válassza az **SQL-adattárházak** lehetőséget a bal oldali menüben, majd kattintson a **mySampleDataWarehouse** elemre az **SQL-adattárházak** lapon. Megnyílik az adatbázis áttekintő lapja, amely megjeleníti a teljes kiszolgálónevet (például **mynewserver-20180430.database.Windows.net**), és további konfigurálási lehetőségeket biztosít.
1. Másolja ezt a teljes kiszolgálónevet a kiszolgálóhoz és az adatbázisaihoz való kapcsolódáshoz, és az egyéb gyors indításokat. A kiszolgáló beállításainak megnyitásához kattintson a kiszolgálónévre.

   ![kiszolgálónév keresése](media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)

1. Kattintson a **Tűzfalbeállítások megjelenítése** elemre.

   ![kiszolgáló beállításai](media/load-data-from-azure-blob-storage-using-polybase/server-settings.png)

1. Megnyílik az SQL-adatbáziskiszolgálóhoz tartozó **Tűzfalbeállítások** oldal.

   ![kiszolgálói tűzfalszabály](media/load-data-from-azure-blob-storage-using-polybase/server-firewall-rule.png)

1. Az aktuális IP-címét az eszköztár **Ügyfél IP-címének hozzáadása** elemére kattintva veheti fel egy új tűzfalszabályba. A tűzfalszabály az 1433-as portot egy egyedi IP-cím vagy egy IP-címtartomány számára nyithatja meg.

1. Kattintson a **Save** (Mentés) gombra. A rendszer létrehoz egy kiszolgálószintű tűzfalszabályt az aktuális IP-címhez, és megnyitja az 1433-as portot a logikai kiszolgálón.

1. Kattintson az **OK** gombra, majd zárja be a **Tűzfalbeállítások** lapot.

Mostantól csatlakozhat az SQL-kiszolgálóhoz és annak adattárházaihoz erről az IP-címről. A csatlakozás az SQL Server Management Studio vagy más, választott eszköz használatával lehetséges. A csatlakozáskor használja a korábban létrehozott ServerAdmin-fiókot.

> [!IMPORTANT]
> Alapértelmezés szerint az összes Azure-szolgáltatás számára engedélyezett a hozzáférés az SQL Database tűzfalán keresztül. A tűzfal az összes Azure-szolgáltatásra vonatkozó letiltásához kattintson ezen az oldalon a **KI** gombra, majd a **Mentés** parancsra.

## <a name="get-the-fully-qualified-server-name"></a>A teljes kiszolgálónév lekérése

Kérje le az SQL-kiszolgáló teljes kiszolgálónevét az Azure Portalon. Később ezt a teljes nevet fogja majd használni a kiszolgálóhoz való kapcsolódás során.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza az **SQL-adattárházak** lehetőséget a bal oldali menüben, majd kattintson az adattárházra az **SQL-adattárházak** lapon.
3. Az Azure Portalon az adatbázishoz tartozó lap **Alapvető erőforrások** ablaktábláján keresse meg, majd másolja ki a **Kiszolgáló nevét**. Ebben a példában a teljes név mynewserver-20180430.database.windows.net.

    ![kapcsolatadatok](media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)

## <a name="connect-to-the-server-as-server-admin"></a>Csatlakozás a kiszolgálóhoz kiszolgáló-rendszergazdaként

Ebben a részben az [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) használatával építjük fel a kapcsolatot az Azure SQL-kiszolgálóval.

1. Nyissa meg az SQL Server Management Studio alkalmazást.

2. A **Connect to Server** (Kapcsolódás a kiszolgálóhoz) párbeszédpanelen adja meg a következő adatokat:

   | Beállítás | Ajánlott érték | Leírás |
   | :------ | :-------------- | :---------- |
   | Kiszolgáló típusa | Adatbázismotor | Kötelezően megadandó érték |
   | Kiszolgálónév | A teljes kiszolgálónév | Íme egy példa: **mynewserver-20180430.database.Windows.net**. |
   | Authentication | SQL Server-hitelesítés | Ebben az oktatóanyagban az SQL-hitelesítésen kívül más hitelesítéstípus nincs konfigurálva. |
   | Bejelentkezés | A kiszolgálói rendszergazdai fiók | A kiszolgáló létrehozásakor megadott fiók. |
   | Windows 10 | A kiszolgálói rendszergazdai fiók jelszava | A kiszolgáló létrehozásakor megadott jelszó. |
   ||||

    ![kapcsolódás a kiszolgálóhoz](media/load-data-from-azure-blob-storage-using-polybase/connect-to-server.png)

3. Kattintson a **Csatlakozás** gombra. Megnyílik az Object Explorer ablak az SSMS-ben. 

4. Az Object Explorerben bontsa ki a **Databases** (Adatbázisok) elemet. Ezután bontsa ki a **mySampleDatabase** csomópontot az új adatbázisban található objektumok megtekintéséhez.

    ![adatbázis-objektumok](media/create-data-warehouse-portal/connected.png) 

## <a name="run-some-queries"></a>Néhány lekérdezés futtatása

Az SQL Data Warehouse a T-SQL nyelvet használja lekérdezési nyelvként. A lekérdezésablakok megnyitásához és a T-SQL-lekérdezések futtatásához végezze el az alábbi lépéseket:

1. Kattintson a jobb gombbal a **mySampleDataWarehouse** elemre, majd válassza az **Új lekérdezés** elemet. Megnyílik egy új lekérdezési ablak.
2. A lekérdezésablakban írja be a következő parancsot az adatbázisok listájának megjelenítéséhez.

    ```sql
    SELECT * FROM sys.databases
    ```

3. Kattintson az **Execute** (Végrehajtás) parancsra. A lekérdezés eredménye két adatbázist mutat: a **master** és a **mySampleDataWarehouse** adatbázist.

    ![Adatbázisok lekérdezése](media/create-data-warehouse-portal/query-databases.png)

4. Hogy lássunk néhány adatot is, a következő parancs használatával tekintsük meg, hány Adams vezetéknevű, három gyermeket nevelő ügyfél létezik. A lekérdezés hat ügyfelet listáz ki. 

    ```sql
    SELECT LastName, FirstName FROM dbo.dimCustomer
    WHERE LastName = 'Adams' AND NumberChildrenAtHome = 3;
    ```

    ![A dbo.dimCustomer lekérdezés](media/create-data-warehouse-portal/query-customer.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az adatraktár-egységek és az adattárházban tárolt adatforgalomért kell fizetnie. Ezek a számítási és tárolási erőforrások elkülönítve lesznek kiszámlázva.

- Ha szeretné az adatokat megtartani a tárolóban, a számítási erőforrásokat szüneteltetheti, amíg nem használja az adattárházat. A számítás felfüggesztésével csak az adattárolás díját számítjuk fel. Bármikor folytathatja a számítást, amikor készen áll az adatokkal való együttműködésre.
- Ha szeretné megelőzni a jövőbeli kiadásokat, az adattárházat törölheti is.

Kövesse az alábbi lépéseket a már nem szükséges erőforrások törléséhez.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és kattintson az adattárházra.

    ![Az erőforrások eltávolítása](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. A számítási erőforrások szüneteltetéshez kattintson a **Szüneteltetés** gombra. Ha az adatraktár szüneteltetve van, a **Folytatás** gomb jelenik meg. A számítás folytatásához kattintson a **Folytatás**gombra.

3. Ha el szeretné távolítani az adattárházat, hogy a számítási és a tárolási erőforrásokért ne kelljen fizetnie, kattintson a **Törlés**gombra.

4. A létrehozott SQL-kiszolgáló eltávolításához kattintson a **mynewserver-20180430.database.Windows.net** elemre az előző képen, majd kattintson a **Törlés**gombra. A törléssel bánjon óvatosan, mivel a kiszolgálóval együtt a hozzá rendelt összes adatbázis is törölve lesz.

5. Az erőforráscsoport törléséhez kattintson a **myResourceGroup** elemre, majd az **Erőforráscsoport törlése** parancsra.

## <a name="next-steps"></a>További lépések

Létrehozott egy adattárházat, létrehozott egy tűzfalszabályot, kapcsolódott az adattárházhoz, és futtat néhány lekérdezést. Ha bővebb információra van szüksége az Azure SQL Data Warehouse-zal kapcsolatban, folytassa az adatok betöltésével foglalkozó oktatóanyaggal.

> [!div class="nextstepaction"]
> [Az adatSQL Data Warehouseba való betöltés](load-data-from-azure-blob-storage-using-polybase.md)
